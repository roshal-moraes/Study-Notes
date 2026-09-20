# System Design Interview Reference Guide — Part 2
### Rate Limiter · Distributed Key-Value Store · Web Crawler

Companion to the News Feed guide. Use this to review core patterns before interviews.

---

# 1. Distributed Rate Limiter

## The Ideal Answer Flow

**Clarify first:**
- What's limited? (per-user, per-API-key, per-endpoint)
- What's the limit? (e.g., 100 requests/minute/user)
- **Fail-open vs. fail-closed?** If the limiter goes down, does traffic pass through unmetered (fail open) or get blocked (fail closed)? Default answer: **fail open** — availability of the core service outweighs strict enforcement during a rare outage.

**Back-of-envelope (given 100K req/sec, 10M active users):**
```
Memory:  10,000,000 users × 50 bytes/counter ≈ 500 MB   → fits on one node
Throughput: 100,000 req/sec, each an atomic read+increment → well within
            what a single Redis instance handles (100K+ ops/sec)
```
**Conclusion to state out loud:** *"This isn't a storage problem — it's a shared, fast, atomic counter problem. The real design challenge is that the counter must be shared across all gateway instances, not local to each one."*
If numbers were much bigger (100M+ users): shard counters across multiple Redis instances by hashing user_id — clean to scale since each user's counter is independent.

## High-Level Design (diagram)

```
   Client ──▶ Load Balancer ──▶ API Gateway (many instances)
                                     │
                          ┌──────────┴───────────┐
                          │ - SSL termination      │
                          │ - Identify user        │
                          │ - Check rate limit ─────┼──▶  Shared Redis
                          └──────────┬───────────┘      (counter store)
                                     │                    key: user_id
                             (if allowed)                 val: count, window_ts
                                     ▼                          │
                            Backend / API Service          + Replica (failover)
```

**Why the counter store must be shared, not local:** the load balancer can route the same user's requests to *different* gateway instances. A local per-instance counter would let a user get 100 requests on instance A and another 100 on instance B — blowing past the real limit. Centralizing in Redis, with atomic `INCR`, prevents this.

## Algorithms — know all five by name

| Algorithm | How it works | Pro | Con |
|---|---|---|---|
| **Fixed Window** | One counter per fixed time block (e.g. per clock-minute) | Simple, minimal memory | Boundary burst: 100 reqs at 11:59:59 + 100 at 12:00:01 = 200 in 2 sec |
| **Sliding Window Log** | Store every request's timestamp, count how many fall in last N seconds | Fully accurate | Memory scales with request volume, not just users |
| **Sliding Window Counter** | Weighted avg of current + previous fixed windows | Smooths boundary bug, small constant memory | Approximation, not perfectly precise |
| **Token Bucket** | Bucket holds tokens (cap), refills at steady rate, each request consumes one | Allows natural bursts up to cap, steady average rate | Slightly more state per user |
| **Leaky Bucket** | Requests queue up, "leak out" at a fixed steady rate | Enforces strictly constant output — protects fragile downstream systems | Delays legitimate bursty traffic even with spare capacity |

**Default to lead with:** Sliding Window Counter or Token Bucket — most production systems use one of these two.

**Extra details worth mentioning proactively:**
- Return HTTP 429 with `Retry-After` header on rejection
- Key by `user_id + endpoint` if different endpoints need different limits
- Configurable limits per user tier, looked up alongside the counter

---

# 2. Distributed Key-Value Store

## The Ideal Answer Flow

**Clarify first:**
- Scope: single-key get/put only (no ranges, no transactions)
- CP or AP? → Default to **AP** (like DynamoDB) — prioritize availability + partition tolerance, accept eventual consistency
- Scale: e.g., 1,000 nodes, billions of keys, values under 10 KB

**Back-of-envelope:**
```
1,000,000,000 keys × 10 KB (10,240 bytes) = ~9.3 TB total
9.3 TB / 1,000 nodes = ~9.5 GB per node (before replication)
```
**Conclusion:** storage per node is trivially small — the real engineering challenges are partitioning logic, replication, and failure handling, not capacity.

## Partitioning: Consistent Hashing (diagram)

```
                    0 / 2^64
                       │
              ┌────────┴────────┐
              │                  │
      Node C (pos 80)      Node A (pos 10)
              │                  │
              │      RING        │
              │                  │
      Node B (pos 45) ───────────┘

  Key hashes to position 15 → walk clockwise → hits Node A first → Node A owns it
  Key hashes to position 60 → walk clockwise → hits Node C → Node C owns it
```

**Why consistent hashing:** when a node joins/leaves, only the keys in the adjacent arc move — not the entire dataset. Compare to naive `hash(key) % N` partitioning, where adding a node changes the modulus and reshuffles almost everything.

**Adding Node D at position 30 (between A at 10 and B at 45):** only keys hashing between 10 and 30 move — from B to D. Everything else on the ring is untouched.

**Virtual nodes:** each physical node is actually placed on the ring 100-200 times, at different random positions, to avoid uneven load — with only one position per node, chance alone could give one node a much bigger arc than others.

**Technology note:** the hash function typically maps onto a large space, e.g. a 64-bit range (0 to 2^64 − 1, about 18 quintillion values), making collisions astronomically unlikely. This isn't a specific product — it's just picking a hash function (e.g., MD5, SHA-1, MurmurHash) with a big enough output space.

## Replication and "Primary" — a key clarification

**Each ring position is a partition (key range), not a single node for the whole dataset.** Each partition has its own primary + a handful of replicas (2-3, often the next few nodes clockwise on the ring, ideally spread across regions for fault tolerance). A write for a given key goes to *that key's partition's* primary, then propagates asynchronously to *that partition's* replicas. If a partition's primary fails, one of its own replicas is promoted — independent of what's happening on any other partition.

```
Partition for keys [10-45) (owned by Node A):
   Node A (primary) ──async replication──▶ Node B, Node C (replicas)
   If A fails: one of B/C is promoted to primary for THIS partition only.
   Other partitions on the ring are unaffected.
```

## Conflict Resolution: Vector Clocks

**The problem:** two clients write to the same key at nearly the same time, reaching different replicas before propagation catches up. Wall-clock timestamps are unreliable for ordering because clocks drift across machines.

**Vector clocks — worked example:**

```
Key: "shopping_cart_42", replicated on Nodes A, B, C

1. Client writes "milk" → lands on Node A
   Node A increments ITS OWN column only: clock = [A:1, B:0, C:0]
   Propagates normally to B, C — all three agree.

2. Network partition occurs. Two concurrent writes:
   Client 1 writes "milk, eggs"  → routed to Node A
     Node A bumps its own column: [A:2, B:0, C:0]
   Client 2 writes "milk, bread" → routed to Node B
     Node B bumps its own column: [A:1, B:1, C:0]

3. Partition heals. Compare the two clocks:
   [A:2, B:0, C:0]  vs  [A:1, B:1, C:0]
   → A's count is higher in version 1, B's count is higher in version 2
   → CONFLICT: neither clock "contains" the other → genuinely concurrent writes
```

**Rule:** a node increments only its OWN column, and only when it's the direct entry point for a write — never when relaying/receiving an already-processed replicated update. This is why, in steady state with one primary, other nodes' columns stay at zero — they only move when that node *itself* accepts a write directly (e.g., during a partition, or if the system allows any replica to accept writes for availability).

**Resolving a detected conflict — two options:**
1. **Last-write-wins (timestamp tiebreak):** simple, but risky — clock drift can pick the "wrong" winner, and a legitimate write can be silently discarded with no warning.
2. **Return both versions to the client app** (what DynamoDB actually does) — let the application merge them using business logic it understands (e.g., union the cart items) rather than guessing.

**How to decide between them in an interview:** defer to the use case — *"Depends whether silently losing a write is acceptable. For a shopping cart, I'd return both versions; for a low-stakes view counter, last-write-wins is fine."*

**Memory footprint:** small — just a few small integers (one per node that's touched the key), not full timestamps, maybe a dozen bytes total per key.

---

# 3. Web Crawler

## The Ideal Answer Flow

**Clarify first:**
- Scope: crawl + discover + store raw HTML only (no indexing, no PageRank, no media)
- Freshness: high-traffic pages recrawled more often (e.g. daily), less important pages less often (e.g. weekly) — an adaptive policy, not fixed upfront
- Scale: push back and ask the interviewer for a target — e.g., 1 billion pages

**Back-of-envelope:**
```
1,000,000,000 pages × 100 KB/page (typical HTML size) = 100,000,000,000,000 bytes
                                                        = 100 TB total
```
**Conclusion:** 100 TB is a "you need real distributed, sharded storage" number (compare: the KV store's 9.5 GB/node was trivial). Roughly 100+ storage nodes if each comfortably handles ~1 TB.

## High-Level Design (diagram)

```
   Seed URLs ──▶ ┌─────────────┐
                 │ Master Node  │──── URL Frontier (priority queue)
                 │ (scheduler)  │        + Bloom filter (seen-URL check)
                 └──────┬───────┘
                        │ assigns URLs to crawl
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
     Worker Node 1  Worker Node 2  Worker Node N
          │             │             │
          ▼             ▼             ▼
     Fetch page → hash content → store page → extract new links
                        │
                        ▼
              New links → normalize → check Bloom filter →
              (if new) → add to Frontier queue
```

**Master/worker roles:**
- **Master:** owns the URL frontier (what to crawl next, and in what priority), decides whether a URL should be (re)crawled based on last-crawl timestamp and priority.
- **Workers:** fetch pages, compute a content hash (to detect if content changed since last crawl), store the page, extract new links, and report back — workers don't decide crawl policy themselves.
- **Failure handling:** worker failures are replaced/retried by re-queuing their in-flight work; the master needs a hot standby, or workers should be able to elect a new master (similar to leader election in systems like ZooKeeper/Raft), so there's no permanent single point of failure.

## Key Technologies/Concepts to Name

**Bloom Filter** — NOT a product like Redis; it's a *data structure* (an array of bits + several hash functions) for compactly representing "have I seen this before" across billions of items, using a tiny fraction of the memory that storing the raw URLs would take.
```
Add a URL:   hash it with k different hash functions → set those k bit positions to 1
Check a URL: hash it the same way → are ALL k bit positions set to 1?
             - if ANY bit is 0 → definitely NOT seen before (100% certain)
             - if ALL bits are 1 → PROBABLY seen before (small false-positive chance)
```
**Why false positives are acceptable here:** worst case, you skip re-crawling a URL you haven't actually seen — a minor missed opportunity. The reverse error (crawling the same page forever) would be far worse, and a Bloom filter guarantees that never happens.

**URL Normalization** — collapse equivalent URLs (trailing slash, query param order, www vs. non-www) to one canonical form *before* the seen-check, so duplicates of the same page aren't treated as distinct URLs.

**Priority Queue (not FIFO) for the crawl frontier** — since every crawled page yields many new links (e.g., ~50 each), the discovery rate vastly outpaces crawl capacity. Rank by:
- inbound link count (rough proxy for importance)
- known high-value domains
- link depth from seed (shallower = more central, generally higher priority)
Lower-priority URLs simply wait longer in queue, or get dropped under severe backlog.

**Per-domain rate limiting ("politeness policy")** — cap requests per second to any single domain, regardless of how many of that domain's URLs are queued, to avoid hammering one server and getting blocked.

**Adaptive recrawl frequency** — start all pages on a frequent schedule (e.g. daily); if a page's content hash stays unchanged across repeated crawls, gradually move it to a less frequent schedule (e.g. weekly). This is a policy the system tunes over time, not a fixed rule.

**Capacity is not a hard cap** — "1 billion pages" is a steady-state storage/budget estimate, not a wall the crawler stops at. Stale, dead, or low-value pages get pruned/deprioritized over time to make room for newer content — a continuous curation process, not a stop condition.

---

# Cross-Cutting Patterns (seen across all 4 problems today)

| Pattern | Where it showed up | One-line justification to say out loud |
|---|---|---|
| Fan-out on write vs. read | News feed | "write amplification vs. read amplification tradeoff" |
| Consistent hashing | KV store | "minimizes data movement when nodes join/leave" |
| Vector clocks | KV store | "detects concurrent conflicting writes without relying on unreliable clocks" |
| Bloom filter | Web crawler | "compact 'have I seen this' check at massive scale, tolerating false positives but never false negatives" |
| Priority queue over FIFO | Web crawler, rate limiter design discussions | "not all items/requests are equally important — rank, don't just process in arrival order" |
| Shared/centralized state via Redis | Rate limiter, feed inbox, KV lookups | "any server could handle any request — state can't live locally on one instance" |
| Fail-open vs. fail-closed | Rate limiter | "does availability or strict enforcement matter more if this component dies?" |
| Async propagation via queues | Feed fan-out, KV replication, crawler discovery | "decouple the fast synchronous path from slower background work" |

---

*Review this alongside Part 1 (News Feed guide) before interviews. Try re-deriving the vector clock example and the Bloom filter mechanism from memory — those two were the hardest concepts today, and are the ones most worth over-preparing.*

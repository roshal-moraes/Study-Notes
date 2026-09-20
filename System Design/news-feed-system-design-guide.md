# News Feed System Design — Reference Guide

A model answer flow for "Design a news feed (like Twitter/Facebook)," plus a primer on Redis and message queues since these show up in almost every system design interview.

---

## 1. The Ideal Answer Flow

Use this as a checklist/script for any system design interview, not just this one.

### Step 1 — Clarify Requirements (2-3 min)
State scope, functional requirements, and non-functional requirements out loud, fast:

- **Functional:** users post text updates, follow other users, view a reverse-chronological home feed of people they follow.
- **Non-functional:**
  - Scale: e.g. 200M users, 50M DAU
  - Read-heavy: ~20:1 read/write ratio
  - Latency: feed load < 200ms p99
  - Availability > strict consistency (eventual consistency is fine — a post can take a few seconds/minutes to appear)

**Say this in one line:** *"This is a read-heavy, eventually-consistent system — I'll optimize reads aggressively and accept some write-to-read propagation delay."* That sentence alone signals seniority.

### Step 2 — Back-of-Envelope Math (3-5 min)
Don't skip it — it justifies every decision you make later.

**The method:**
- **QPS:** daily count ÷ 100,000 ≈ average QPS. Multiply by 2-3x for peak.
- **Storage:** size-per-item × items/day × retention period.
- **Bandwidth:** items returned per request × item size × requests/sec.

**Worked example, using our assumed numbers (200M users, 50M DAU, ~200 follows/user):**

Assume each DAU checks their feed ~10x/day and posts ~0.5x/day on average.

*Reads/day:*
```
50,000,000 DAU × 10 checks/day = 500,000,000 feed reads/day
```

*Writes/day:*
```
50,000,000 DAU × 0.5 posts/day = 25,000,000 posts/day
```

*Average QPS (divide by ~86,400 sec/day, round to 100,000):*
```
Read QPS  = 500,000,000 / 100,000 = 5,000 reads/sec
Write QPS = 25,000,000 / 100,000  = 250 writes/sec
```

*Peak QPS (2-3x average, use 3x):*
```
Peak reads  ≈ 5,000 × 3  = 15,000 QPS
Peak writes ≈ 250 × 3    = 750 QPS
```

*Storage — size of one post:*
```
Text content:        ~280 bytes  (280 char limit, 1 byte/char)
Metadata (user_id, post_id, timestamp, etc.): ~150 bytes
Total per post:       ~430 bytes → round to 500 bytes/post
```

*Storage — daily and 5-year:*
```
Daily:   25,000,000 posts × 500 bytes = 12,500,000,000 bytes = 12.5 GB/day
5-year:  12.5 GB × 365 days × 5 years = 22,812.5 GB ≈ 23 TB
```

*Storage — the fan-out inbox side (sorted sets), separate from post storage:*
```
Each inbox entry = post_id (~8 bytes) + timestamp score (~8 bytes) ≈ 16 bytes
Avg fan-out per post ≈ 200 followers (avg, excluding celebrities)
Daily inbox writes: 25,000,000 posts × 200 followers × 16 bytes
                   = 80,000,000,000 bytes = 80 GB/day (in Redis, memory — not disk!)
```
This number is why Redis eviction/TTL policy on older feed entries matters — you don't keep this forever in memory.

*Bandwidth — feed read response:*
```
1 feed request ≈ 20 posts returned × 500 bytes/post = 10 KB per response
Read bandwidth = 5,000 reads/sec × 10 KB = 50,000 KB/sec = ~50 MB/sec (avg)
Peak: 15,000 reads/sec × 10 KB = 150,000 KB/sec = ~150 MB/sec
```

**What these numbers tell you (say this part out loud in the interview):**
- 15K peak read QPS is very manageable for a well-cached system — a handful of app server instances behind a load balancer, with Redis absorbing most reads.
- 23 TB over 5 years is not "big data" scale — a single well-sharded NoSQL cluster handles this fine; no exotic storage system needed.
- 80 GB/day of inbox writes into Redis is the number that justifies TTL/eviction policy on the sorted sets (e.g., only keep the last ~1,000 post_ids per user's inbox) — otherwise Redis memory grows unbounded.
- Bandwidth (150 MB/sec peak) is trivial — confirms network isn't the bottleneck; fan-out write amplification is.

### Step 3 — High-Level Architecture (5 min)
Sketch the basic building blocks, left to right:

```
Client → Load Balancer → App Servers → [Cache / Queue / DB]
```

Name each piece and its job in one sentence:
- **Load balancer:** distributes traffic across app servers
- **App servers:** stateless, handle post creation + feed requests
- **Cache (Redis):** serves precomputed feeds fast
- **Database:** durable storage for posts, users, follow-graph
- **Message queue:** decouples "post created" from "fan out to followers"

### Step 4 — The Core Problem: Feed Generation Strategy
This is the heart of the interview. State the two options **by name**:

| Strategy | How it works | Pro | Con |
|---|---|---|---|
| **Fan-out on read** | At read time, query posts from everyone the user follows, merge, sort | Cheap writes, always fresh | Expensive reads, slow at scale |
| **Fan-out on write** | At write time, push the post into every follower's precomputed feed ("inbox") | Cheap, fast reads | Expensive writes; breaks down for accounts with huge follower counts |

**Say the tradeoff explicitly:** *"Since reads vastly outnumber writes here, I'll default to fan-out-on-write to keep reads cheap."*

### Step 5 — The Celebrity Problem (the classic curveball)
Raise this **yourself**, before being asked — that's what separates a strong answer from an average one.

*"One issue with pure fan-out-on-write: if a user has 10M+ followers, one post means 10M writes. That's a write amplification problem — often called the 'celebrity problem.'"*

**Solution — Hybrid fan-out:**
- Normal users (below a follower threshold, commonly ~10K) → fan-out-on-write
- Celebrity accounts (above the threshold) → **don't** fan out; fetch their posts at read time instead
- On read: fetch precomputed feed (non-celebrity posts) + fetch recent posts from followed celebrities (small list) → merge by timestamp

### Step 6 — Data Structures & Storage
- **Feed "inbox":** don't store full post content per follower — store **post IDs only** (pointers), to avoid massive duplication.
- **Redis Sorted Set** is the standard tool: score = timestamp, value = post_id. Gives you ordering + range queries (pagination) for free.
- **Posts table:** NoSQL or SQL, keyed by post_id, holding actual content + metadata.
- **Follow graph:** separate table/store mapping follower → followee relationships.

### Step 7 — Edge Cases to Raise Proactively
- **Cold start:** new user has an empty inbox (fan-out-on-write only covers future posts). Fix: fall back to fan-out-on-read for them, and run a one-time backfill to populate their sorted set.
- **Celebrity post caching:** cache a celebrity's recent posts once (small sorted set keyed by celebrity user_id), shared across all their followers — don't refetch from DB per follower.
- **Queue backlog / fan-out lag:** this is a backend health issue, NOT a user-facing error. The read path should never time out because of it — the user just sees a slightly stale feed. Monitor queue depth / consumer lag, alert if lag exceeds a threshold, auto-scale workers.

### Step 8 — Reliability
- DB: primary + read replicas, async replication, automatic failover/promotion on primary failure
- App servers: stateless, multi-region, behind load balancer
- Queue consumers: multiple workers, auto-retry, dead-letter queue for repeated failures

---

## 2. Where Redis Fits — And How to Talk About It

Redis is an **in-memory key-value data store**. It's fast (sub-millisecond) because data lives in RAM, not disk. In interviews, you don't need deep Redis internals — you need to know **which Redis data structure fits which sub-problem**, and be able to name it.

### The Redis structures that come up constantly

**1. Simple Key-Value (String)**
```
SET user:123:profile "{...json...}"
GET user:123:profile
```
Use for: caching a single object (user profile, a URL shortener's short→long mapping, a rendered page).

**2. Sorted Set (ZSET)** — the one you needed today
```
ZADD feed:user:123  1695000000  post_987
ZADD feed:user:123  1695000050  post_991
ZREVRANGE feed:user:123 0 19    # get latest 20 posts, most recent first
```
Every element has a **score** (used for ordering) and a **value**. Redis keeps it sorted automatically.
Use for: anything that needs to be ranked/ordered and paginated — feeds, leaderboards, "recently active users," time-series-ish data.

**3. Hash**
```
HSET post:987 author_id 42 content "hello world" created_at 1695000000
HGETALL post:987
```
Use for: storing an object with multiple fields (like a mini row), when you want to fetch/update individual fields without pulling the whole blob.

**4. List**
```
LPUSH notifications:user:123 "New follower: Alice"
LRANGE notifications:user:123 0 9
```
Use for: simple ordered sequences, e.g., a notification queue for one user. (Sorted Sets are usually preferred over Lists when you need ranking/pagination by score, since Lists only support position-based access.)

**5. Set**
```
SADD followers:user:123 42 99 501
SISMEMBER followers:user:123 42
```
Use for: unordered unique collections — e.g., "who follows this user," fast membership checks.

### How to correctly narrate Redis usage in an interview
Don't just say "I'll use a cache." Say:

> *"I'll use Redis for two things: a Sorted Set per user for their precomputed feed — score is timestamp, value is post_id — which gives me ordering and pagination for free. And a simple key-value cache for hot posts/profiles to avoid repeated DB hits."*

That sentence alone signals you know Redis isn't just "a fast dictionary" — it's a toolbox, and picking the right structure for the job is the skill being tested.

### When Redis is the wrong answer
- If data must survive a restart with zero loss and strong durability guarantees → Redis alone isn't enough (it has persistence options like RDB/AOF, but it's not the primary system of record for critical data). Use it as a cache/acceleration layer in front of a real database, not a replacement for one.
- If you need complex relational queries/joins → not Redis's job, use your primary DB.

---

## 3. Where Message Queues Fit — And How to Draw Them

You correctly used caching and replicas today, but never introduced a queue. Here's where it belongs and why.

### The problem queues solve
When a user posts, and you're doing fan-out-on-write, you don't want the app server to synchronously write to (say) 5,000 followers' inboxes before returning "post successful" to the user. That would make posting painfully slow, and if one follower's write fails, does the whole post fail?

**Queues decouple "the post was created" from "the post was distributed."**

### On the whiteboard, it looks like this:

```
User posts
   │
   ▼
App Server ──writes post to DB──▶ Posts DB
   │
   │ (publish event: "new_post", post_id, author_id)
   ▼
Message Queue  (e.g., Kafka / SQS / RabbitMQ)
   │
   ▼
Fan-out Worker(s)  ──reads follower list──▶  Follow Graph DB
   │
   ▼
Writes post_id into each follower's
Redis Sorted Set (feed inbox)
```

**Narrate it like this:**

> *"When a user posts, the app server writes the post to the database and immediately returns success to the user — it doesn't wait for fan-out. It publishes an event to a message queue. A pool of fan-out workers consumes that queue, looks up the follower list, and pushes the post_id into each follower's Redis sorted set. This keeps the write path fast for the user and lets us scale the fan-out workers independently — if we fall behind, we just add more workers, and the user never notices because they weren't waiting on it."*

### Why this matters to interviewers
Without a queue, you're implicitly saying fan-out happens synchronously in the request path — which means a post to a large account could make the API call hang for seconds. Naming the queue shows you understand **decoupling** and **backpressure handling**, which are core distributed systems concepts tested in almost every design question (not just feeds).

### Common queue technologies to name-drop (pick one, don't list all)
- **Kafka** — high throughput, good for event streaming, retains messages
- **SQS** (AWS) / **Cloud Pub/Sub** (GCP) — managed, simpler, good default if not asked to go deep
- **RabbitMQ** — traditional message broker, good for task queues

You don't need deep expertise in any one — just be able to say "a message queue" and explain *why* it's there (decoupling + async processing + scalability of consumers).

---

## Quick-Reference: Recurring Patterns Across System Design Interviews

| Pattern | Shows up in | Key phrase to use |
|---|---|---|
| Fan-out on write vs. read | Feeds, notifications | "write amplification vs. read amplification tradeoff" |
| Caching (Redis) | Almost everything | "cache-aside pattern," name the data structure |
| Message queues | Feeds, order processing, any async workflow | "decouple the write path from downstream processing" |
| Read replicas + primary/failover | Any DB-backed system | "eventual consistency, async replication" |
| Sharding | Large datasets, key-value stores | "shard by user_id / hash-based partitioning" |
| Rate limiting | APIs, celebrity/hot-key problems | "token bucket / sliding window" |

---

*Reference this before your next system design interview — re-derive one of these problems out loud, from this flow, without looking, to build fluency.*

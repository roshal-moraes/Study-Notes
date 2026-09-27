# System Design: Distributed Cache Layer in Front of a Sharded Database

## The Problem

Design a distributed caching layer in front of a database for a high-traffic service serving billions of reads a day. The database is already sharded (by key range) and each shard has read replicas, but read volume alone is overwhelming it.

## Clarifying Questions & Constraints

- **Is the database itself distributed?** Yes — sharded across nodes by key range, each shard has read replicas.
- **Consistency vs. availability?** Prioritize availability and low latency. A few seconds of staleness is acceptable.
- **Write volume?** Moderate — not write-heavy.

## Capacity Estimation

- ~10 billion reads/day → ~100,000 seconds/day → **~10⁷ (10 million) reads/sec average**
- Peak traffic ~3x average → **~30 million reads/sec peak**
- A single cache node (e.g. Redis-class) can comfortably handle ~1 million reads/sec
- → **~30 cache nodes needed at peak**, kept **elastic/auto-scaling** since peaks may be seasonal rather than constant

## High-Level Architecture

- **One cache cluster per database shard**, using the *same* hashing/routing logic used for DB sharding — a request's key hashes to the same logical destination for both the DB shard and its paired cache cluster. This keeps routing simple and co-locates cache/data ownership.
- **Within each cluster**, a shard's keys are spread across multiple cache nodes (not just one node per shard) so that no single node becomes a bottleneck for an entire shard's traffic.
- **Elasticity is per-cluster**, so a cluster serving a "hot" shard can scale independently of a cold one.

## Handling a Single Hot Key

Consistent hashing sends all traffic for one specific key to one node, regardless of how well the rest of the keys are spread. If that one key gets hammered (e.g. a viral item), two techniques:

1. **Dedicated cluster** for known hot entities (e.g. celebrity profiles) — pragmatic, used historically by systems like Twitter.
2. **Key replication + local caching** (more general, for *unpredictable* hot keys):
   - Replicate the hot key's value across several nodes; pick one at random per request, spreading load.
   - Or cache it briefly (sub-second TTL) directly in the application server's memory, so most requests never even reach the cache tier.

## Write Path: Keeping the Cache from Serving Stale Data

**Pattern: cache-aside reads + invalidate-on-write.**

1. Write goes to the database first.
2. Once the write is confirmed committed, **delete** (don't update) the corresponding cache key.
3. The next read is a cache miss → fetches fresh from the DB → repopulates the cache.

**Why delete instead of writing the new value directly into the cache from the write path?**
Race conditions. If two writes to the same key happen close together, the order they land in the database and the order they'd land in the cache via a separate write-path update can differ — risking the cache holding a stale value even after the DB has the newer one. Deleting sidesteps this: the next read always asks the DB directly, which is authoritative.

## Cache Stampede (Thundering Herd)

**Problem:** A hot key gets invalidated (e.g. after a price update). The next instant, still-massive request volume for that same key all misses simultaneously — all of them independently hit the database at once, causing a redundant traffic spike exactly when the DB is least prepared for it.

**Fix — request coalescing (a.k.a. locking on the cache key):**
The first request that misses grabs a short-lived lock for that key and goes to the DB. All other concurrent requests for the same key either wait briefly for that one request to populate the cache, or are served a slightly stale value in the meantime. Only **one** DB request happens per invalidation event, not one per concurrent reader.

## Eviction Policy

When the cache is full, need a policy for what to evict:

- **LRU (Least Recently Used):** evict the item that hasn't been accessed in the longest time. Typically implemented with a **hashmap + doubly linked list** — O(1) access and O(1) move-to-front/eviction.
- **LFU (Least Frequently Used):** evict based on access *frequency* rather than recency.

## Node Failure Handling

- Nodes have **replicas**; if a node fails, its replica can take over its position on the consistent-hash ring **instantly, with no rebalancing** (since it already holds the same data).
- If there's no ready replica and the ring must reassign the failed node's key range, **only the neighboring nodes on the ring** (the ones adjacent to the failed node) absorb its keys — not the entire cluster. This is the core advantage of consistent hashing over naive modulo-based hashing, which would force a full reshuffle.

---

## New Terms Learned Today (for interview vocabulary)

| Term | Meaning |
|---|---|
| **Cache-aside** | Read pattern where the app checks the cache first; on a miss, reads from the DB and populates the cache. |
| **Invalidate-on-write / write-invalidate** | On a write, delete the cache entry (rather than updating it) so the next read repopulates fresh from the DB. |
| **Cache stampede / thundering herd** | Many concurrent requests for the same invalidated/expired key all miss simultaneously and hit the DB at once. |
| **Request coalescing (key locking)** | Fix for cache stampede — only one request per key fetches from the DB while others wait or use a stale value. |
| **Hot key** | A single key receiving disproportionately high traffic, overwhelming the one node it hashes to. |
| **Key replication** | Duplicating a hot key's value across multiple nodes and randomly picking one per request to spread load. |
| **Consistent hashing** | Hashing scheme where node failure/addition only affects the key range of adjacent nodes on a "ring," not the whole cluster. |
| **LRU (Least Recently Used)** | Eviction policy: remove the item not accessed for the longest time. Implemented via hashmap + doubly linked list. |
| **LFU (Least Frequently Used)** | Eviction policy: remove the item accessed least often overall. |

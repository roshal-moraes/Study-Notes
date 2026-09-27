# System Design Session Notes — 2026-09-20
## Problems Covered: (1) Distributed Job Scheduler, (2) Distributed Chat System

---

## 1. Sample Model Answer: Distributed Job Scheduler

**Problem:** Design a system that reliably fires millions of scheduled jobs (one-off and recurring) at their exact scheduled time, exactly once, even across machine failures. 10M jobs in system, ~1,000 jobs/sec average firing rate, 5,000/sec peak, <10s drift acceptable, small payloads (~10KB, ~100GB total), job execution internals out of scope.

### High-Level Components
- **Persistent store (source of truth):** every job stored durably, indexed/sorted by scheduled fire time (e.g. B-tree index on timestamp). This handles the full 10M-job scale.
- **Redis sorted set (hot window cache):** holds only the near-term slice of jobs (e.g. the next hour), keyed by fire time, so the coordinator doesn't have to scan the full persistent store every cycle.
- **Coordinator:** polls the Redis hot window (e.g. every minute), identifies jobs due, and dispatches them to worker queues.
- **Elastic worker pool:** scales up/down with load (1,000–5,000 jobs/sec), picks jobs off the queue and executes them.

### Key Mechanisms
- **Status flags — dispatched vs. completed:** the coordinator marks a job "dispatched" when pushed to the worker queue; a worker marks it "completed" only after actually finishing. "Dispatched" alone is never enough to consider a job safely done.
- **Failover / reconciliation sweep:** if the coordinator crashes, a new instance takes over and sweeps for jobs stuck in "dispatched" (not yet "completed") past a threshold — treats them as failed and retries/requeues them, rather than assuming they ran.
- **At-least-once vs. exactly-once:** this is a legitimate scoping question to ask the interviewer — it depends on the job's nature. At-least-once + idempotency checks (e.g. compare last-updated timestamp before applying a database write) is the standard way to make at-least-once delivery *effectively* exactly-once for the jobs that need it.
- **Debugging delay:** instrument every handoff (submission → cache load → coordinator pickup → dispatch → worker completion) with synchronized clocks (NTP) and centralized logging/tracing (e.g. ELK stack), plus threshold-based alerting on delayed-job counts, to isolate where a delay originates.

### Concrete Trace (happy path)
1. Job submitted → written to persistent store (with an edge case: if scheduled within the current minute, it goes straight into the Redis hot set instead of waiting for the next promotion cycle).
2. At the appropriate time, job promoted from persistent store into Redis sorted set.
3. Coordinator polls Redis, picks up due jobs, pushes to worker queue, marks "dispatched."
4. Worker picks up job from queue, executes it, marks "completed" in persistent store.
5. On failure at any stage: retry logic / dedicated retry queue, with alerting for a human if retries are exhausted.

### Session Verdict
Full solution covered end-to-end. Strong-hire level performance — architecture was correct and fast, and failure-mode reasoning (dispatched/completed flags, reconciliation, at-least-once framing) was genuinely senior-level and mostly self-derived.

---

## 2. Sample Model Answer: Distributed Chat System

**Problem:** Design a chat system (WhatsApp/Slack-style) supporting 1:1 and group messaging, presence, and reliable delivery at scale (500M users, 50M concurrent connections, groups up to 200 members, at-least-once delivery, per-conversation ordering).

### High-Level Components
- **Client (thin layer):** sends messages to server, renders in sequence-number order, buffers out-of-order arrivals, never talks to other clients directly.
- **API / Ingest layer:** assigns a unique message ID, writes to persistent store, assigns a per-conversation sequence number.
- **Persistent store:** source of truth. One row per message — not duplicated per recipient, even for groups (normalized).
- **Presence registry (Redis):** maps `user_id → {online/offline, which WebSocket server}`.
- **WebSocket server fleet (~1,000 servers):** each holds a shard of live connections; consistent hashing with virtual nodes distributes connections evenly.
- **Delivery routing:** look up presence registry → if online, forward to the specific WebSocket server holding that connection → push instantly. If offline, message sits in persistent store; a push notification alerts the device; full message delivered on reconnect via "give me everything since sequence N."
- **Per-user delivery/read cursors:** lightweight table (`message_id / conversation_id / user_id / delivered_seq / read_seq`) — kept separate from message content so group messages aren't duplicated per recipient.
- **Group fan-out:** fan-out-on-write (cheap at a 200-member cap) — message body stored once, per-recipient cursor entries created on send.

### Key Mechanisms
- **Ordering:** monotonically increasing sequence number per conversation, assigned at write time. Ordering is enforced client-side (buffer + reorder on gaps), not via a global lock.
- **Failure/reconnect:** device reconnect always asks "everything since my last acked sequence number"; server serves from persistent store.
- **Read receipts:** client sends only the *highest* sequence number read; everything ≤ that is implicitly marked read — no per-message round trip needed.
- **At-least-once + dedup:** unique message ID lets the client silently drop duplicates.

### Back-of-Envelope
- WebSocket servers: 50M concurrent conns ÷ ~50K conns/server ≈ **1,000 servers**.
- Message throughput: ~10 msgs/user/min × 50M online ≈ **~8M msgs/sec peak**.
- Presence registry: 50M entries × ~100 bytes ≈ **5GB** → fits a single Redis cluster.

### Session Verdict
Solid pass trending lean-hire. Architecture and schema instincts were fast and correct; the read-receipts-via-highest-sequence-number insight was sharp and self-derived. Main stumble was initially proposing that clients discover each other's server assignments directly — recovered well once redirected toward a registry-lookup pattern. **Left off at:** deeper group fan-out at scale — resume here next session, or treat as closed and move to a new problem.

---

## 3. Notes on Distributed Communication (Service Discovery / Coordination)

This is the recurring soft spot across both problems today — **"how does component A find out where component B's state currently lives?"**

### Pattern: Registry / Lookup Table
- Don't make components discover each other directly (e.g. "client A knows client B's server" — doesn't scale).
- Maintain a central fast-lookup registry (usually Redis) mapping a stable ID → current location/owner, updated on every state change.
- Seen today: `user_id → WebSocket server ID` (chat), `job_id → dispatched/completed status` (job scheduler).

### Pattern: Consistent Hashing (+ Virtual Nodes)
- Assigns a shardable resource (a connection, a key) to one of many servers without a central bottleneck.
- Virtual nodes prevent hot-spotting when server load is uneven.
- **Mental hook:** consistent hashing answers "which server *should* own this"; a registry answers "which server *currently does* own this" — useful after rebalancing/failover or for dynamic state hashing alone can't capture.

### Pattern: Sequence Numbers for Ordering (instead of global locks)
- Assign a monotonically increasing number per logical stream (per conversation, per partition).
- Push ordering enforcement to the consumer/client, not the distributed writers — writers don't need to coordinate with each other, only with the counter.

### Pattern: Status Flags + Reconciliation Sweep (Failover Safety)
- Two/three-state markers: `dispatched` vs `completed` (or `delivered` vs `read`).
- On failover/restart, sweep for items stuck in an intermediate state past a threshold → treat as failed → retry.
- **Critical nuance:** "dispatched" is not "confirmed done" — only a downstream ack should clear an item.

### Pattern: At-Least-Once + Idempotency (not exactly-once)
- True exactly-once delivery across a network is effectively impossible to guarantee cheaply.
- Standard compromise: at-least-once delivery + idempotent consumers (dedup via unique ID, or compare-and-check via timestamp).
- Asking the interviewer which guarantee they actually need is a legitimate scoping question, not a stall.

---

## 4. Revision Notes — Weak Areas to Drill

### (a) Distributed component discovery / coordination — Primary gap
Seen in both problems today (job scheduler coordinator failover; chat WebSocket routing).
- **Practice:** for any distributed design, explicitly ask "if component A needs something from component B, how does A find B?" before moving on — make it a checklist item every session.
- **Concepts to review:** service registries, consistent hashing vs. rendezvous hashing, gossip protocols (basic idea), leader election basics (Raft/Zookeeper — conceptual level only).
- **Drill suggestion:** deliberately ask "how do these two nodes find each other's current state" at least once per future system design problem, even if not prompted.

### (b) Exact Java syntax under pressure
- Not exercised this session (both problems were system design). Prioritize a coding session next to re-check status.

### (c) Speaking fluently about AI-tool usage in interviews
- Not exercised this session — flag for a dedicated behavioral-prep round.

### (d) Verification/trace-through avoidance
- **Improving.** Both sessions today included full concrete traces completed without needing to be pushed (job scheduler: full happy-path trace including the near-boundary edge case; chat system: sender → server → registry → WebSocket → client, end-to-end, unprompted). Keep reinforcing — this is trending well.

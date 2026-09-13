# System Design: Rate Limiter — Full Solution

This is the complete version of the problem we worked through together, filled out to the depth a full 45-minute round would expect.

---

## 1. Clarify Requirements (what you already nailed)

Questions to ask up front:
- What are we rate limiting? (API requests, login attempts, etc.)
- Single server or distributed system?
- What's the limit? (e.g., 100 requests/min/user)
- What happens on exceeding the limit — reject, delay, degrade?
- Is this per-user, per-IP, per-API-key, or tiered (free vs. paid users)?

**Assumed scope for this solution:** distributed API service, 100 requests/minute/user, reject with an error when exceeded.

---

## 2. Algorithm Options (the part to know by name)

| Algorithm | How it works | Pros | Cons |
|---|---|---|---|
| **Fixed window counter** | Count requests in a fixed time bucket (e.g., 0:00–0:59), reset every minute | Simple, memory-efficient | Edge burst problem: user can send 100 requests at 0:59 and another 100 at 1:00 — 200 requests in ~1 second |
| **Sliding window log** | Store a timestamp for every request; count how many fall within the last 60 seconds | Very accurate | Memory-heavy — stores every request timestamp |
| **Sliding window counter** | Blend of fixed windows — weight the previous window's count proportionally | Good accuracy, memory-efficient | Slightly approximate, a bit more complex to implement |
| **Token bucket** | Bucket holds tokens, refilled at a fixed rate; each request consumes a token; empty bucket = reject | Naturally allows short bursts (nice UX), industry-common (AWS, Stripe use variants) | Needs tuning of bucket size + refill rate |
| **Leaky bucket** | Requests enter a queue processed at a fixed rate, like water leaking from a bucket | Smooths traffic to a steady rate | Adds latency; not great for bursty legitimate traffic |

**What you described in our session was a sliding window counter — a good, defensible choice.** In an interview, naming 2-3 of these and explaining *why* you'd pick one (e.g., "token bucket because we want to allow occasional bursts without punishing normal usage patterns") shows range even if you only implement one in depth.

---

## 3. High-Level Architecture

```
Client → Load Balancer → API Gateway (rate limit check here) → Backend Service
                              |
                              v
                        Redis (shared state)
```

**Where the check happens:** Often at an API Gateway / middleware layer rather than the load balancer itself — gives more flexibility for per-route or per-tier limits without touching low-level networking config. (Your instinct to push it to the LB/gateway layer was directionally right — gateway is the more precise term.)

**Why Redis:**
- In-memory → sub-millisecond latency, critical since this runs on every request
- Native TTL support → auto-expire counters after the window
- Atomic `INCR` command → avoids race conditions when multiple app servers hit the same user's key simultaneously
- For token bucket logic specifically, a Lua script run atomically in Redis is the standard way to check-and-decrement a token count in one atomic operation

**Race condition example to mention explicitly:** two requests for the same user hit two different backend servers in the same millisecond. Both read "current count = 99," both think they're allowed, both increment to 100 — but really 101 requests got through. Solution: use Redis `INCR` (atomic) instead of read-then-write, or wrap the whole check-and-increment in a Lua script for more complex algorithms like token bucket.

---

## 4. Failure Handling (the part you nailed independently)

- **Fail-open:** if Redis is down, let all requests through. Prioritizes availability/UX; risk is temporarily losing rate limit protection.
- **Fail-closed:** if Redis is down, reject all requests. Prioritizes strict enforcement/safety; risk is a full outage of your API during a Redis blip.
- Most real systems **fail-open** for rate limiters specifically, since the downside of temporarily allowing extra traffic is usually less severe than blocking all legitimate users. (Contrast with something like a payment fraud check, where fail-closed might be preferred.)
- Mitigate the failure window with Redis replication (a warm standby replica) to minimize how long you're in a degraded state at all.

---

## 5. API-Level Details (often missed)

When a request is rate-limited, a well-designed API doesn't just return a bare error — it tells the client how to behave:

```
HTTP/1.1 429 Too Many Requests
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
Retry-After: 42
```

- `429 Too Many Requests` is the standard HTTP status code for this (worth knowing by number).
- `Retry-After` tells the client how many seconds to wait before retrying — good API citizens use this to back off automatically.
- `X-RateLimit-Remaining` lets well-behaved clients self-throttle before even hitting the limit.

---

## 6. Configuration & Granularity

- Limits are often tiered: free-tier users get 100 req/min, paid users get 1000 req/min — this means your Redis key might encode the tier, or you look up the user's plan before checking their count.
- Rate limits can apply at multiple levels simultaneously: per-user AND per-IP (to catch abuse from a single source hitting many accounts) AND global (to protect the backend from total overload).

---

## 7. Back-of-Envelope Numbers (shows scale awareness)

Say you have 10 million active users, each capable of up to 100 requests/minute:
- Each user needs one Redis key (~a few bytes for the key + an integer counter) → roughly 50-100 bytes per user with overhead
- 10M users × ~100 bytes ≈ **1 GB of Redis memory** — very manageable for a single well-provisioned Redis instance or a small cluster
- At peak, if a meaningful fraction of those users are active simultaneously, you might see tens of thousands of `INCR` operations per second — well within what a properly sized Redis cluster handles (Redis can do 100K+ ops/sec on modest hardware)

You don't need exact numbers in an interview — the goal is showing you can *reason* about whether a design is feasible at scale, not perform precise math.

---

## 8. How This Maps Back to What You Did

| Piece | You covered it? |
|---|---|
| Clarifying questions | ✅ Excellent |
| Centralized shared state | ✅ Correctly reasoned |
| Algorithm (sliding window) | ✅ Right concept, needed the name |
| Failure handling (fail-open/closed) | ✅ Independently derived both |
| Named algorithm alternatives | ❌ Add this |
| API response details (429, headers) | ❌ Add this |
| Race condition specifics | 🟡 Implicit — make it explicit next time |
| Tiered/multi-level limits | ❌ Add this if time allows |
| Back-of-envelope numbers | ❌ Add this for senior-level polish |

The core reasoning skeleton you brought was genuinely strong. What's left is vocabulary and a checklist of "extra layers" to reach for once the fundamentals are covered — which is exactly what repeated practice builds.

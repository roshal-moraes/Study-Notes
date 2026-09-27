# System Design Session — Cloud File Storage & Sync Service
**Date:** September 27, 2026

## Problem Statement
Design a cloud file storage and sync service (Dropbox / Google Drive style):
- Hundreds of millions of users, geographically distributed
- Files sync across a user's own devices
- Large files (up to several GB) must be supported
- Every edit creates a new version
- **Out of scope for this session:** file sharing between users, simultaneous multi-user editing (saved for a future session)

**Scale (derived in-session):**
- ~10M daily active users syncing
- ~5–10 file operations/user/day → ~10^8 ops/day → **~100 ops/sec average**, ~500–1000/sec peak
- Key insight: at this scale, request volume is trivial — **the real bottleneck is bytes/file size**, not throughput

---

## Sample Full Answer

### High-Level Architecture
- **Client** (mobile + desktop apps)
- **Metadata service**: NoSQL database storing filename, unique file ID, owner, version history, per-user device list
- **Chunking + queue layer**: large files split into fixed- or content-defined-size chunks, pushed to a queue for workers to process
- **Worker pool**: validates checksums, writes chunks to blob storage
- **Blob storage** (e.g., S3): stores chunks, geographically placed near the uploading user for low latency
- **Sync/notification layer**: informs other devices when a file is fully uploaded

### Upload Flow
1. Client computes metadata (file ID, version) and checks against metadata DB to see if the file already exists.
2. File is split into chunks (e.g., 4–8 MB each). Each chunk gets a checksum, a chunk number, and an identity of `(file_id, chunk_number)`.
3. Chunks are placed on a queue; workers validate checksums and write to blob storage.
4. Once a chunk lands successfully, an event fires (e.g., an S3 event notification triggering a small function) that increments an **atomic counter** of chunks received for that file.
5. When the counter reaches the expected total chunk count, the metadata DB marks the file **complete** and versioned.
6. If chunks stop arriving and a timeout fires first, the upload is marked **failed**, and any chunks that did land are **deleted** (no partial file is ever useful on its own).

### Key Design Decisions & Why

**Direct-to-storage vs. queue + workers**
If no processing is needed beyond storing bytes, clients can upload directly to blob storage via pre-signed URLs — simpler, less to build. A queue + worker layer is justified when you need control in the middle: checksum validation, chunk-count tracking, transformation, etc.

**Tracking upload completion — race condition**
If every chunk's success event does a naive "read count → increment → write count," two events landing simultaneously can race and undercount. A **lock** would resolve this but adds an extra moving part (a lock service, risk of a crashed worker holding a stale lock).

An **atomic counter** operation (available natively in most NoSQL stores, e.g. DynamoDB's atomic increment) is the better answer: the database enforces mutual exclusion internally, in a single operation, with no external coordination needed.

**Versioning — avoid re-uploading whole files**
On edit, don't naively re-chunk and re-upload everything. Split the new file version into chunks, compare checksums against the old version's chunks, and only upload chunks whose checksum changed.

**The fixed-size chunking trap**
Fixed-size chunking breaks badly under insertions/deletions: inserting even one byte at the start of a file shifts every downstream chunk boundary, so *every* chunk's checksum changes even though almost all the actual bytes are identical.

**Fix: Content-Defined Chunking (rolling hash)**
Instead of cutting chunks at fixed byte offsets, slide a small window (e.g., 64 bytes) across the file and compute a rolling hash of its contents. Declare a chunk boundary wherever the hash satisfies some local condition (e.g., "low 13 bits are all zero"). Because the boundary depends only on local content — not position in the file — inserting a byte near the start only disturbs the chunk(s) right around the insertion; everything after the next real boundary comes out byte-identical to the old version and doesn't need re-upload.

- Average chunk size is *tunable*, not fixed: requiring more zero bits in the hash makes boundaries rarer → bigger average chunks (e.g., ~13 bits ≈ 8 KB average; ~22 bits ≈ 4 MB average).
- Real implementations clamp a min/max chunk size as a safety bound.

**Cross-device sync notification**
Devices need to learn about new/completed files quickly. Options:
- **Polling** — simple, fine if some latency (e.g., up to a minute) is acceptable; risk of overwhelming the DB without caching.
- **Push** — a DB trigger on file-version change fans out a notification to all the user's registered devices.

**How push actually reaches a device (the deeper mechanism):**
- Mobile: your service doesn't talk to the phone directly. It hands the message to a platform push gateway — **APNs** (Apple Push Notification service, for iOS) or **FCM** (Firebase Cloud Messaging, for Android). The OS itself maintains one persistent, always-on connection per device to these gateways, shared across every app — this is why push works even when your app is fully closed, with no extra battery cost from your own app holding a socket open.
- Desktop/laptop (app open and running): typically a **WebSocket** — a connection you manage yourself, since there's no OS-level push gateway equivalent for a laptop client the same way.

**Conflict handling (offline edits on two devices)**
Borrowing from the Git merge-conflict mental model, but adapted for binary files (no meaningful line-by-line diff exists for e.g. video or design files):
- Don't try to auto-merge content.
- Keep **both versions** as separate entries (Dropbox's real-world approach: a "conflicted copy" file).
- Set a **conflict flag** on the file's metadata entry.
- Show both versions to the user on any device until they explicitly resolve it; only then clear the flag.

---

## Concepts Learned / Reinforced Today

| Concept | Summary |
|---|---|
| **Back-of-envelope scaling** | 10M ops/day ÷ ~10^5 sec/day ≈ 100 ops/sec average, not 1,000 — watch for off-by-a-power-of-ten errors when converting large counts to per-second rates. |
| **Build vs. delegate trade-off** | Route uploads through your own queue/workers only if you need control (validation, transformation) beyond what the storage provider already gives you. |
| **Atomic counters vs. locks** | Prefer a database's native atomic increment over an application-level lock for simple counting races — less infrastructure, no stale-lock failure mode. |
| **Orphaned data cleanup** | Partial upload data (chunks without a complete set) should always be deleted — it's not independently useful. |
| **Content-Defined Chunking (rolling hash)** | Chunk boundaries determined by local content via a rolling hash condition, not fixed byte offsets — makes delta sync robust to insertions/deletions. Average chunk size is tuned via the strictness of the hash condition (more required zero bits → larger average chunk). |
| **Push notification internals (APNs / FCM)** | Mobile push relies on an OS-level persistent connection to a platform gateway (Apple Push Notification service / Firebase Cloud Messaging); your backend only talks to that gateway, not the device directly. |
| **WebSockets for desktop clients** | For long-running clients without a mobile-style OS push channel, a self-managed long-lived connection (WebSocket) is the standard alternative. |
| **Conflict resolution for binary files** | Unlike text/code, binary files can't be line-diffed — the practical answer is to preserve both divergent versions and flag the conflict for manual user resolution, rather than attempting an automatic merge. |

---

## Session Feedback
**Strengths:** Strong unprompted high-level design; correctly derived the S3-event/atomic-counter/orphan-cleanup mechanisms without heavy guidance; caught own terminology slip (called a race condition a "deadlock," self-corrected); reached for the Git-merge-conflict analogy independently and then correctly identified its limits for binary files.

**Gaps:** Two genuine knowledge gaps rather than reasoning failures — content-defined chunking (rolling hash) and push notification internals (APNs/FCM). In both cases, appropriately said "I don't know" or explicitly triaged/scoped out the issue rather than guessing, which is exactly the right behavior in a real interview.

**Deferred:** File sharing and simultaneous multi-user editing/collaboration — planned as a separate future session.

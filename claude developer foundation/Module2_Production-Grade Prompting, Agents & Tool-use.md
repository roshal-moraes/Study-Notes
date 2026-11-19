# Study Notes: Developer Module 2, Production-Grade Prompting, Agents & Tool-use

## 0. Module overview

**Core idea:** Writing code *that uses* Claude is different from using Claude *to write code*. The module covers everything beyond interactive prompting: tool schemas, context management, agent loops, and so on.

**The recurring "build":** A Claude integration that worked in development but must survive production. In development the prompt looked solid, the tool call worked, sessions were short and test inputs manageable. In production it must survive:
- longer sessions
- larger tool outputs
- interrupted streams
- tighter cost and latency limits
- memory across turns
- irreversible actions

**Learning objectives (8):**
1. Write production-ready prompts (system prompts, XML tags, few-shot examples, output constraints) and diagnose underperforming prompts.
2. Decide when to enable extended thinking, calibrate effort, and handle thinking blocks across tool-use turns.
3. Define a tool schema Claude selects correctly, build the tool-use loop, handle multi-turn message blocks, and distinguish single vs. parallel tool calls.
4. Consume streamed responses, assemble events into content blocks, and recover from interrupted streams.
5. Apply context engineering (window management, compaction, clearing, subagent handoffs) to keep multi-turn sessions in budget without losing continuity.
6. Build a production agent: choose workflow vs. agent patterns, wire tools and context into a loop, pick a wiring path for your deployment constraints, add human-in-the-loop (HITL) checkpoints for irreversible actions.
7. Manage agent memory across sessions (persistent storage patterns, right memory scope) without inflating context cost.
8. Send images and PDFs correctly, use the Files API for reusable assets, and use the Message Batches API for high-volume async workloads.

**Audience:** Practical, code-forward developers, already comfortable coding. Not programming fundamentals, not casual chat use. It teaches engineering decisions around the model: prompt structure, tool definitions, streaming safety, context and memory, and reliable, affordable, controllable agent loops.

**Disclaimer:** Educational content, not legal, financial or professional advice. Products evolve, so verify on Anthropic's website or docs. Examples are illustrative and often fictitious. Mentions of companies or products are not endorsements. Anthropic's terms, policies and documentation control if they conflict with the course.

*Note: objectives 6, 7, 8 are listed in the overview but the pasted content does not teach them (no sections on workflow vs. agent patterns, HITL, memory, Files API or Batches API). The only memory-adjacent material is clearing and CLAUDE.md under context strategies.*

---

## 1. Prompting craft

### 1.1 Core principle
A prompt that works once interactively often breaks in production on untested inputs. The fix is **not more words**. It is identifying **which structural piece is missing** and adding that one piece.

- Rewording changes *how* you say something but doesn't add the missing structure.
- If Claude crosses the boundary between instructions and input data, clearer phrasing won't fix it.
- If output format drifts, "please format this correctly" won't fix it.
- **Rule:** name the failure, add the one technique that matches, re-run. If it still fails, diagnose again.
- **Warning sign:** a prompt that gets longer with every pass means you're skipping diagnosis and just adding words.

### 1.2 The four techniques
1. **System prompts**: carry the behavioral contract for the whole session. Write once, treat as the persistent instruction layer. They define Claude's role, output format, and rules that must not change between conversations.
2. **XML tags**: separate examples and data from instructions so Claude doesn't read examples as part of the task. They mark where each example starts and ends.
3. **Few-shot examples**: show the pattern instead of describing it. One correct input-output pair gives the exact shape to match.
4. **Output constraints**: control the *form* of the response (shape, field names, stopping point) independent of content.

*(The pasted text only fully defines system prompts in the tabbed section. The rest is inferred from the other sections.)*

### 1.3 Diagnosis table: symptom → missing piece

| Observed | Missing | Why it fixes |
|---|---|---|
| Wrong **shape**: sentence instead of label, prose instead of JSON | **Output constraint** (form, field names, stopping point never specified) | Without one, Claude returns plausible text the parser can't accept |
| **Content off**: scope drifts, tone shifts, answers a wider question; worsens deeper in conversation | **System prompt** (or a more specific one) | Behavioral contract too vague to hold across turns |
| Task understood but **structure invented** | **Few-shot examples** | Claude can't infer an exact structure from description alone |
| Clean on tested inputs, **breaks on a variant** (edge case, unusual field) | **A constraint covering the variant** | Prompt only handled the happy path; name the variant in a constraint or add an example covering it |

### 1.4 Worked example: support ticket classifier
**Before (bare prompt):**
- System: "You are a support classifier. Classify the ticket."
- User: `<ticket>I was charged twice for the same month.</ticket>`
- Result: "Billing" sometimes, "billing" other times, sometimes a full sentence ("This looks like a billing issue."). The router expecting fixed labels breaks.
- Diagnosis: row 1, missing **output constraint**.

**After:**
- System: "You are a support classifier. Classify each ticket into exactly one of: BILLING, TECHNICAL, ESCALATION. Return only the label. No other text."
- Few-shot in XML tags: `<sample_input>My account shows two charges for April.</sample_input><ideal_output>BILLING</ideal_output>` and `<sample_input>The API keeps returning a 429 error.</sample_input><ideal_output>TECHNICAL</ideal_output>`
- User: `<ticket>...</ticket>`

**Division of labor:** system prompt = output contract (exactly one label, nothing else); XML tags = boundaries between examples and instruction; few-shot = exact casing and format.

### 1.5 When to stack, simplify, or diagnose

| Approach | When |
|---|---|
| **Stack all four** | Clearly defined output contract; well-specified formats; edge cases coverable by examples |
| **Simplify** | Adding all four to a simple task that needs one (e.g., "summarize this paragraph" doesn't need few-shot + schema) |
| **Diagnose before adding more** | Prompt grows longer, not more precise. After ~5 re-prompts with wrong output, diagnose the failure type first |

### 1.6 The iteration loop (diagnose before re-prompting)
- Wrong format → missing output constraint
- Wrong content / scope drift → underspecified system prompt
- Correct task but hallucinated structure → need few-shot
- Good on simple inputs, breaks on edge cases → no constraint covering the variant

The fix is **structural, not phrasing**.

### 1.7 Structured outputs (moving control from prompt into the API)
**Problem:** Prompt instructions are *requests*. The model can still return a stray sentence, wrong field name, or malformed JSON.

**Mechanism:** You pass a **JSON schema**; the API uses **constrained decoding**. At each token, only tokens keeping the output valid against the schema are allowed, so a violating response can't be produced.

**Two modes (usable alone or together):**
1. **JSON outputs** constrain the *final response*. Set `output_config.format` with `type: json_schema` plus your schema. Use when the model itself produces the structured payload (extracting ticket fields, formatting an API response). Removes parse-and-retry code.
2. **Strict tool use** constrains *inputs to your tools*. Set `strict: true` on the tool definition. Arguments are validated against `input_schema` before your code runs. Use in agentic loops where a malformed argument would crash a function or trigger a wrong action.

**Why it belongs in code, not just the prompt:** Prompt-level "return only JSON" holds on tried cases and slips on untested ones. The schema is enforced on every token, so correctness is ruled out *before* it happens rather than verified after.

**Costs and caveats:**
- **First request on a new schema is slower.** The schema is compiled to a grammar. Grammars are **cached 24 hours from last use**. Stable schemas pay once; constantly changing schemas pay repeatedly.
- **Input tokens rise.** The API injects a system prompt describing the format, billed as normal input.
- **Guaranteed schema ≠ guaranteed success.** Still check `stop_reason`: `refusal` (safety decline) and `max_tokens` (truncated mid-structure) return non-matching output.
- **Incompatible with message prefilling.** Pick one.

### 1.8 Case study: the prompt that grew longer instead of better (six passes)

| Pass | Added | Behavior |
|---|---|---|
| 1 | "Classify as billing, technical, or escalation." | Full sentences; parser breaks |
| 2 | "Be concise." "Use only the category name." | Inconsistent case; router breaks |
| 3 | Three paragraphs describing each category | Good on simple; ambiguous tickets return "billing/technical" (slash breaks parser) |
| 4 | "Never return two categories." "If ambiguous, choose the most likely." | ~80% work; fails on dual-fit tickets (e.g., charged *and* feature stopped), returns an explanation |
| 5 | Two more paragraphs on edge cases + "be precise" | Verbose prompt → verbose output (>2,000 chars/call); latency up, accuracy flat |
| 6 | Replaced all instructions with JSON schema + two few-shot examples | Returns `{"category": "billing"}` every time; parser works; latency drops; ambiguous-ticket accuracy matches pass 4 |

**Two distinct failures:**
- **Pass 4 = diagnostic failure:** added description instead of a constraint, so it identified the wrong problem.
- **Pass 5 = engineering failure:** prompt verbosity induced a latency regression. The model calibrates response length and style to match the input. This can appear even after the first failure is resolved.

**Takeaways:** Claude doesn't need a detailed description of what a billing ticket is; it needs to know the only acceptable response is one word in capitals. The fix is two lines: an output constraint and a few-shot example covering the ambiguous case. If three re-prompts in a row fail, stop adding text and diagnose.

### 1.9 Checkpoint 1: fix the broken prompt
- **Broken:** "You are a support ticket processor. Extract the key information from the ticket below." (Ticket: API key stopped working after rotation, production failing, needs immediate fix.)
- **Defect:** missing **output constraint** (no JSON structure, field names, allowed values, or "return nothing else").
- **Corrected system prompt:** "Extract the key information from each ticket and return only a JSON object with exactly these three fields: category (one of: billing, technical, escalation), urgency (one of: low, medium, high, critical), and summary (a single sentence describing the issue). Return only the JSON object. No other text."
- **Expected output:** `{"category": "technical", "urgency": "critical", "summary": "Developer's API key stopped working after rotation, causing a production deployment failure that needs immediate resolution."}`

---

## 2. Extended thinking

**Definition:** Prompting shapes *what* Claude produces; extended thinking shapes *how much work* Claude does before answering. The model writes out step-by-step reasoning first, then the answer.

**Mechanics:**
- Reasoning returns as its own **thinking block**, just before the answer block.
- On the newest models, thinking content is **omitted by default**. Request a readable summary via the **display** setting.
- On current models reasoning is **adaptive**: enable with the `thinking` parameter (where not already on) and the model decides how much per request. Tune depth with the **effort** setting.
- `budget_tokens` is **deprecated**; on the newest model generations it **returns a 400 error**.
- **Cost:** thinking tokens cost the same as output tokens. High effort on a simple task = paying for accuracy you don't need. Match the tool to the task; apply strategically.

**When to use:**

| Task shape | Decision | Reason |
|---|---|---|
| Multi-step reasoning with several constraints (math derivation, multi-hop logic, planning dependent actions) | **Enable**, effort matched to depth | Reasoning pass works through dependencies otherwise skipped |
| Mechanical/lookup (classification, format conversion, field extraction, short facts) | **Leave off** | No improvement, more tokens; a bare prompt with an output constraint is right |
| Agentic loops planning across several tool calls | **Enable; budget for the planning step**, not per call | Reasoning before a plan reduces wrong-tool selection; carry-back rule applies |

### The carry-back rule
When extended thinking is on and you use tools, **every thinking block must go back to the API exactly as received** on the next turn.
- Each block has a **signature** confirming it wasn't tampered with. Editing, summarizing or dropping it breaks the signature, and the API rejects the request.
- **Redacted thinking blocks** (encrypted, not human-readable) follow the same rule: return untouched.
- This is a **structural requirement**, not a prompting choice.
- Most common slip: stripping thinking blocks to save context, which breaks the next request. If accumulated reasoning is the worry, use context engineering (section 5), not stripping.

**Summary box:** Handles well: hard reasoning/planning where wrong answers are expensive. Adds cost/complexity: carry-back requirement, effort calibration. Use another approach: classification, extraction, format tasks (a constrained prompt is cheaper and as accurate).

**Scope note:** This lesson covers whether to enable reasoning, not which model to run (that's the preceding MSO Foundations module).

### Checkpoint 2 answers
- Classify 50,000 tickets overnight → **Leave it off**
- Plan a multi-step refactor with dependent steps → **Enable it, budget for the planning step**
- Strip thinking blocks from history to save context → **Never do this**

---

## 3. Tool schemas and the tool-use loop

### 3.1 Who does what
**Misconception:** Claude runs the tools. **Reality:** Claude reads your tool definitions, decides which fits, and tells your application what to call with what inputs. **Your application executes** and sends the result back. The boundary of ownership is where most bugs live.

### 3.2 The six-step loop
1. **Define schema**: name, description, input schema. Claude reads this to decide whether and when to call.
2. **Send message**: your code sends tool definitions + user input.
3. **tool_use block**: Claude returns tool name, unique ID, input arguments; response has `stop_reason: tool_use`.
4. **Execute tool**: your code runs it. The assistant turn has already ended; Claude isn't holding a connection open. The model is **stateless between calls**, so you make a fresh request with prior messages plus the result.
5. **Return result**: in a `tool_result` block referencing the original tool_use ID.
6. **Claude continues**: another tool_use or a final end turn.

**The loop is not automatic; step 4/5 is your job.** If misses are systematic, the fix is in the schema definition step.

### 3.3 Message block types

| Block | Role | Contains | Critical rule |
|---|---|---|---|
| **text** | Assistant | Claude's prose | May appear alongside tool_use in the same turn. Preserve the **full content array** when appending to history; dropping the text block corrupts context |
| **tool_use** | Assistant | Tool name, unique ID, input args | Must be answered by a tool_result in the **immediately following user turn** with the same ID |
| **tool_result** | **User** | Matching `tool_use_id`, result content, optional `is_error: true` | ID must match exactly. Matters with multiple parallel calls where results may arrive out of order |
| **thinking** | Assistant (extended thinking only) | Internal reasoning | Pass back unchanged; signature; redacted blocks too |

**Critical invariant:** every tool_use in an assistant turn needs a matching tool_result in the immediately next user turn. A missing, mismatched, out-of-order, or later-turn result fails validation. This is structural, not fixable by prompting. **Convention:** tool_result blocks are always sent in the **user role**; role marks who sends the message to Claude, not who authored the content.

### 3.4 Schema anatomy
- **name**: short and specific (`get_account_balance` > `get_data`).
- **description**: what Claude reads to decide. Write two parts: **when to use** and **when not to use**. "Use this to find information" is indistinguishable from other retrieval tools. "Retrieve the current balance for a specific account ID; do not use for transaction history" gives an exclusion condition.
- **input_schema**: parameters in JSON Schema. Mark `required` only when needed; optional otherwise. Overlapping parameter types between tools is the most common source of wrong-tool calls.

### 3.5 Schema design decision table

| Decision | How to handle | Why |
|---|---|---|
| **Subtask dependency** | Dependent calls run in sequence (separate turns). Independent subtasks: Claude can issue multiple tool_use blocks in one turn and your code runs them concurrently | The one decision that changes schema design. Current models default to parallel when independent. `disable_parallel_tool_use` forces one call per turn |
| **Required fields** | Only when the call makes no sense without them (`required` array) | Over-requiring forces Claude to fabricate values |
| **Optional fields** | For params with defaults or where absence has meaning; omit from `required`, give defaults in function signature | Lets Claude omit rather than guess |
| **Description length** | 3-4 sentences: what it does, when to use, what it returns; include valid-input examples where format matters | Too short → guessing; too long → trigger conditions buried |
| **Overlapping parameter types** | Add disambiguating language naming the domain or trigger in each description | Claude routes on name + description, parameter types secondary; identical signatures collapse routing to description alone |

### 3.6 Worked example: overlapping descriptions
- Tools: `search_knowledge_base`, `get_cached_result`; both began "use this to find information" → frequent wrong picks on ambiguous inputs.
- **Fix** (one added sentence each):
  - `search_knowledge_base`: use for questions needing current information; **do not use if a prior search this session already covers the question.**
  - `get_cached_result`: retrieve a result already fetched this session; **only if search_knowledge_base was called earlier for the same query.**
- **Dependency:** exclusion conditions require **complete conversation history** in each request. If prior turns are truncated, the logic silently fails.
- More tools = more surface area to reason over; the discipline pays off only when tools are distinct.
- **Handles well:** reliable routing with specific descriptions + exclusions. **Poor fit:** two similar tools needing ever-longer descriptions → **merge into one tool with a type parameter**.

### 3.7 Case: the description that sent Claude to the wrong tool
- `search_docs`: "Use this to find information about the product." `get_context_summary`: "Use this to retrieve relevant information from the current session." Same from Claude's view.
- Senior dev's fix: `search_docs` → use when the question needs content *not already in this conversation*; do not call if the answer is in session context. `get_context_summary` → only if the answer is already in session; do not use to look up new information.
- **Both tools need the boundary, not just one.** One sentence for when to use, one for when not to.
- Claude reasons over all descriptions in context of the full conversation; similar descriptions give no reliable signal. If exclusions can't separate them, merge with a type parameter.

### 3.8 MCP (Model Context Protocol)
- **What:** a standardized layer moving tool definitions *and execution* out of your app into dedicated servers. If a server exists (e.g., GitHub: repos, PRs, issues, projects), connect rather than write and maintain schemas.
- **Loop unchanged:** Claude still issues tool_use, you execute and return tool_result, pairing rules apply. Difference is setup: the MCP client sends a **ListToolsRequest**, gets the tool list, and passes it to Claude. Same description-based routing; the difference is who wrote and owns the definitions.
- **Context cost:** MCP tool definitions occupy the context window even when unused. Register only servers you're using; check budget when connecting several.
- **API MCP Connector controls:**
  - `mcp_toolset` object in the `tools` array, with `default_config` (applies to all tools on server) and `configs` keyed by tool name for per-tool overrides.
  - `defer_loading` (boolean): delay loading a tool definition until needed, reducing upfront cost.
  - `enabled` (boolean): turn individual tools on/off.
  - Requires the **`mcp-client-2025-11-20` beta header**; without it the mcp_toolset config won't apply as described.
- **Transports:**
  - **stdio**: local servers; your app spawns a subprocess, communicates over stdin/stdout.
  - **Streamable HTTP**: remote servers; POST client→server, optional GET-based SSE stream for server-initiated messages.
  - Older SSE-only transport is **deprecated**; new integrations use Streamable HTTP.
  - The API **MCP Connector supports only remote (HTTP) servers**. stdio servers need you to manage the MCP client connection via the SDK, or use Claude Desktop/Claude Code as the client. After connection, your code treats both transports identically.
- **Decision guide:**
  - **Use MCP when** a well-maintained server covers the operations you need and tracks the service's current API.
  - **Write manually when** no server fits, or you need precise control over description quality. For *scope* control, note the connector supports allowlisting/denylisting per server via MCPToolset, so manual authoring isn't always needed for scope.
  - **Use both:** MCP for breadth, tuned descriptions for precision on routed tools. Narrowing the tool set (allowlist) and sharpening descriptions are two separate levers; use both.

### Checkpoint 3 answer
Trace: tool_use `id="toolu_01"` but tool_result `tool_use_id="toolu_02"` → error "references unknown tool_use_id". **Answer B:** correct the `tool_use_id` to match the issued id. Rule broken: tool_result must carry the matching ID of the tool_use. (A, exclusion conditions, and C, required array, are wrong; the schema and description were fine.)

---

## 4. Streaming

**What changes:** Non-streamed = one complete message. Streamed = a series of events; **you reassemble the blocks** and decide what to do if events stop early. The final message is identical to non-streamed. Each event is a small message describing one change; your handler applies it to partial state. The model isn't holding a live object open.

### Event sequence

| Event | Signals | Handler action |
|---|---|---|
| `message_start` | New message; shell with empty content, initial usage | Create empty content array |
| `content_block_start` | Block opening, with type (text/tool_use/thinking) and index | Create slot at index. tool_use opens with name and id, no input yet |
| `content_block_delta` | Increment: text fragment, partial JSON for tool input, or thinking fragment | Append to block at that index. Tool input is a partial JSON string; can't parse until block closes |
| `content_block_stop` | Block at index complete | Finalize. For tool_use, first moment JSON is parseable |
| `message_delta` | Top-level changes: `stop_reason`, final usage | Record stop_reason |
| `message_stop` | Stream complete | Assembled content is the finished message |

### Rules
- **Don't act on a partial block.** Collect deltas; act only after `content_block_stop` for that block. Parsing early gives malformed JSON or missing arguments.
- **Append to history only after `message_stop`**, with all blocks assembled. A half-built tool_use in history breaks the pairing rules on the next request.
- **Interrupted streams** (dropped connection, timeout, client disconnect): don't treat collected content as complete. Partial text shown to a user = cosmetic glitch; partial tool_use in history = structural corruption.
  - Track completion deliberately: usable only once `message_stop` arrives.
  - **Discard the partial assistant turn**, don't save to history, and retry from the last complete turn.
  - Check `stop_reason` from `message_delta` before continuing a loop: `tool_use` = tool calls ready; any other value = different path.
- **Summary:** Handles well: long responses/user-facing UIs. Adds complexity: manual assembly, no acting on partials, interruption handling. Use non-streaming for short responses or backend jobs with no one waiting.

### Postmortem: partial tool_use committed after a dropped stream
- Handler accumulated deltas and appended the assistant turn **when the read loop ended**. In testing on a fast local connection streams always completed, so it worked.
- In production, a network blip ended a stream after tool_use opened and got partial JSON but before `content_block_stop`. The loop exited as usual; the handler appended a turn with truncated JSON input. The operator retried; the retry included the corrupted turn and the API rejected it, pointing at the malformed tool_use block.
- The team wasted an afternoon checking schema and retry logic because the error appeared on the *retry*.
- **Root cause:** treated "read loop ended" as "message complete." Only `message_stop` means whole. **Debugging tip:** when a tool-use error appears on a retry, check whether the prior turn was assembled from a stream before touching the schema.

### Checkpoint 4 answer
Defect: the `messages.append(...)` ran unconditionally even if `message_stop` never arrived. Fix:
```python
if stop_seen:
    messages.append({"role": "assistant", "content": assemble(blocks)})
else:
    raise StreamInterruptedError("Stream ended before message_stop; discarding partial turn. Retry from the last complete turn.")
```

---

## 5. Model selection and context engineering

### 5.1 Model selection
- Four tiers: **Fable, Opus, Sonnet, Haiku**, trading off cost, latency, capability.
  - **Sonnet:** balanced default for most production workloads.
  - **Haiku:** speed/cost efficiency for tasks within its capability envelope.
  - **Opus:** demanding work above Sonnet's envelope.
  - **Fable:** most capable; complex reasoning, advanced coding, research synthesis, sophisticated agentic workflows where maximum intelligence is the priority.
- Confirm the current lineup and model identifiers at platform.claude.com/docs at build time.
- **Start with Sonnet.** Move **up** to Opus only when an eval set shows Sonnet misses your quality bar. Move **down** to Haiku only when an eval set shows the quality regression is acceptable for your task, not just to save cost. Moves should be **measured**.

### 5.2 The context window
- The full span the model can take in: prompt, conversation, every tool result. Every tool result is appended and stays for the session.
- Overflow behavior: if a request is already larger than the window, the Messages API rejects it with a validation error before generation. If it fits but generation hits the ceiling, current models return output so far with stop reason `model_context_window_exceeded`. **Neither silently truncates oldest content.** To run past the limit, your application must trim or summarize history itself.
- In production, tool outputs are often **3-5× larger** than test fixtures, so windows fill at turn ~8, not turn ~50.
- **Context engineering** = deciding in advance what goes into the window, what comes back as a summary, and what never enters.

### 5.3 Four strategies

| Strategy | What it does | When | What you lose |
|---|---|---|---|
| **Pruning** | Jump back to an earlier message, drop everything after | After an unproductive path or debugging back-and-forth | Work after the rewind point; must be relearned |
| **Compaction** (`/compact` in Claude Code; server-side compaction in API, a beta; manual summarization as client-side alternative) | Summarize history into a condensed version | Approaching the ceiling but continuing the same feature | Details not captured in the summary |
| **Clearing** (`/clear`; new session in API) | New conversation, empty context | Next task is completely different; old context would bias or confuse | All session context; persistent needs go in something like **CLAUDE.md** |
| **Subagent handoffs** | Subagent in isolated context with only task description + system prompt; returns a summary | Self-contained subtasks, especially exploration where the journey clutters context but the answer is short | Visibility into how the subagent reached its conclusion |

### 5.4 Two more levers
- **Prompt caching:** stores processing of a stable prefix; later requests with identical content up to that point pay a fraction. Best for long system prompts, large tool definition sets, repeatedly queried reference docs. Enable with a `cache_control` field of type `ephemeral` on the last block to cache; up to **four breakpoints**. For multi-turn sessions with stable system prompt and tools, this is the highest-leverage cost reduction.
- **Token counting:** `count_tokens` endpoint takes the same body as a messages call and returns the count without inference. Use in development to validate budgets against real tool outputs, and in production to gate requests that would exceed the window.

### 5.5 RAG: three places it can break
(The text begins mid-topic; it contrasts a "fetch-once" path with a "search-across-rounds" path.)
1. **Chunking:** too small → chunk lacks context; too large → dilutes the match. Sentence- or section-based chunking with a little **overlap** is a reasonable default (overlap prevents facts crossing a boundary from being split).
2. **Embedding match:** similarity search retrieves *semantically close* content, not necessarily the exact term. A query for a specific identifier can miss the relevant chunk. Hence a **lexical match** is sometimes run alongside.
3. **Assembly:** retrieved chunks must reach the model in the structure the prompt expects, or the model answers from memory instead of the retrieved text.

**Fetch-once (index):** inspectable and testable retrieval, but you own the infrastructure: build, store, sync as corpus changes, secure. **Search-across-rounds (agentic search):** no index, no staleness (reads current files at query time), but more tokens/time per query and a less inspectable process. **Rule of thumb:** stable reference corpus + simple lookups → own the index; changing corpus or multi-step questions → iterative search is usually simpler despite higher per-query cost. The reported performance gain for single-agent agentic search over an index is **version-pinned**; verify against the reference layer at build time.

### 5.6 Compaction and summarizer design
- `/compact` in Claude Code decides the summary content itself. API primary route: **server-side compaction (beta)**. With **manual compaction** you write the summarizer prompt, and that determines what the agent knows afterward.
- Weak: "summarize the conversation so far." Strong: "summarize the conversation, preserving all file paths modified, all decisions made, and any errors encountered and their resolutions."
- Task-critical state loss from an under-specified summarizer is one of the most common causes of multi-session agent failures.

### 5.7 Subagent handoffs
When a task is too big for one window, don't enlarge the window; **decompose and pass only relevant context.** A subagent gets: a scoped task, minimum needed context, results of prior directly relevant steps, the tools it needs, and clear exit conditions. The parent collects results. Keeps per-turn cost low and long-horizon tasks tractable. Adds overhead, so use it only where context cost is a real constraint. **Handles well:** multi-step sessions over budget (design at architecture stage, not as a patch). **Skip:** pipelines that never approach the limit; measure actual token usage first.

### 5.8 Postmortem: the receipts agent
- Agent processed sales receipts under a **40k token budget**: a team-imposed cost cap, *not* the model's limit (current API models have ≥200k; newest flagships including Fable serve 1M by default).
- **Development:** 20 receipts × ~800-token tool results ≈ 18,000 tokens total. Fine.
- **Production:** receipts included supporting docs, transaction records, correspondence → ~3,200 tokens/call. 8 turns ≈ 25,600 tokens of tool output; with system prompt + user + assistant messages, hit the 40k cap at **turn 8**.
- **Symptom:** looked like degraded tool selection and incomplete analyses. **Real cause:** accumulated, never-pruned tool outputs crowded out the system prompt and early instructions, so the agent decided on a context lacking its starting guidance.
- Found by a **token usage audit two days after deployment**.
- **Fix:** prune tool outputs after use; apply compaction proactively *before* the cap.
- **Lessons:** measure tool result token cost against the **largest** real inputs before shipping. If tool selection degrades after a fixed number of turns, check whether the window is filling *before* debugging schemas.
- *(The source table says "1M on current Opus and Sonnet," while the prose says "Fable included." Verify current figures in the docs.)*

### Checkpoint 5 answer
Trace: turns 1-4 correct `fetch_policy_document` (2,400 tokens each = 9,600 tokens); turn 5 wrongly picks `search_knowledge_base` instead of `apply_coverage_rule`; turn 6 repeats; turn 7 ends without result. Last correct turn = **turn 4**; mechanism = accumulated large tool results crowding out instructions. **Answer B:** prune `fetch_policy_document` results after each turn and apply compaction before turn 5. (A, rewriting the schema description, misdiagnoses it; C, raising `max_tokens`, affects output length, not input crowding.)

---

## 6. Quick-reference cheat sheet

**Failure → fix**
- Wrong shape → output constraint (or JSON schema / structured outputs)
- Scope/tone drift → tighter system prompt
- Invented structure → few-shot examples (in XML tags)
- Breaks on edge case → constraint/example for the variant
- Prompt growing, no improvement → stop and diagnose
- Wrong tool chosen → add exclusion conditions to *both* descriptions; merge if inseparable
- API rejects tool_result → check `tool_use_id` match and immediate next user turn
- Thinking-related 400 → don't strip/edit thinking blocks; `budget_tokens` is deprecated
- Error on retry after streaming → partial turn was committed; gate append on `message_stop`
- Tool selection degrades at a fixed turn → context filling; prune/compact

**Key invariants**
- tool_use ↔ tool_result pairing, same ID, immediately next user turn
- Thinking blocks returned unchanged (signature)
- Preserve text blocks alongside tool_use in history
- Parse streamed tool input only after `content_block_stop`; append to history only after `message_stop`
- Check `stop_reason` always (`tool_use`, `refusal`, `max_tokens`, `model_context_window_exceeded`)

**Numbers/names to remember:** structured outputs grammar cache = 24 hours; up to 4 prompt-caching breakpoints; MCP beta header `mcp-client-2025-11-20`; production tool outputs often 3-5× test fixtures; receipts example 800 → 3,200 tokens, cap hit at turn 8 of a 40k budget.

---

# Study Notes, Part 2: Agents, Memory, Multimodal & Batch Processing

*(Continues from Part 1, which covered prompting, extended thinking, tool schemas, streaming, and context engineering. This part covers the content that Part 1 flagged as missing: objectives 6, 7 and 8.)*

---

## 6. Building a production agent

### 6.1 What an agent is
**Definition:** An agent is a multi-step tool-use loop with managed context and a defined goal.

- You have already built the pieces (tool schemas, context management). This section connects them and adds a new layer.
- When components run together across many turns, **new failure modes appear** that isolated tests don't catch:
  - Routing decisions that worked in single-turn tests start to compound.
  - Context fills faster than expected.
  - A step depending on a previous result gets wrong input because an earlier tool call was structured incorrectly.
- **First question before any agent build: does this problem require an agent?** Agents carry coordination overhead, expanded context costs, and more surface area for failure than simpler patterns.

### 6.2 Workflow vs. agent: decide before writing code
The most critical mistake is picking the wrong pattern at the start.
- Agent where a workflow suffices → adds behavioral complexity, no added capability.
- Workflow where an agent is needed → breaks whenever input deviates from the predetermined path.

| Choose a **workflow** when… | Choose an **agent** when… |
|---|---|
| You can enumerate the exact steps in code | You can specify the goal and tools but not the exact path |
| Error cost is real and step-level guardrails matter | The path through the work can't be enumerated in advance |
| Observability with standard tooling is required | Non-determinism is acceptable and actions are constrained by the registered toolset |
| Inputs are well-constrained to a known set | User inputs vary unpredictably in content and structure |
| Every execution follows the same sequence | The task requires creative sequencing of available tools |

**Progression rule:** start with the simplest pattern: **single API call → workflow → agent**. Move up only when the simpler pattern can't handle the variability. Agents are the *last* step.

**Agent trade-offs (summary table):**
- *Right call:* goal-directed tasks where the exact path can't be enumerated; variable inputs that would need dozens of conditional branches in a workflow.
- *What you take on:* behavioral complexity. The path emerges from the model's reasoning over accumulated context rather than explicit branching in your code. Observability needs **transcript-level tooling**, not standard operational logging.
- *When to choose a workflow instead:* when you can enumerate the steps in code.

*(Multi-agent architectures, with a planner, executor and evaluator handing off through structured artifacts, add design decisions beyond the loop and are covered later in the track. The "Building Effective Agents" blog lists workflow sub-patterns: chaining, routing, parallelization, evaluator-optimizer.)*

### 6.3 Pattern vs. wiring path
- Deciding on an agent = deciding on a **pattern**: a loop that calls tools, manages context, and runs until a goal is met. For single-agent systems this pattern is **constant across all three wiring paths**.
- What changes is **how much of the loop you write vs. hand to a library or hosted service**.

**Three wiring paths (spectrum of infrastructure you own):**
1. **Raw Messages API loop**: you write the loop directly. Full control, full responsibility.
2. **Agent SDK**: runs the same loop **inside your own process**; provides tool execution, context management and iteration structure.
3. **Claude Managed Agents** (**public beta**): **Anthropic runs the loop and the sandbox**; your app streams events in and results back.

Choose by **deployment and compliance constraints**, not by what's fastest to prototype. The table is ordered by how much infrastructure you hand off (top = most owned).

**Raw Messages API loop:**
- *Who runs the loop:* your code runs every iteration: send request, read tool_use blocks, execute tools, append results.
- *You own:* the full loop, tool execution, context management, retries, exit conditions. Nothing provided.
- *Choose when:* you need full control per step, you have constraints a library can't accommodate, or you're learning how the loop works before adding abstraction.
- *Check before committing:* maintenance cost is yours. Everything the SDK gives free (context management, parallel tool handling) becomes code you write and test.

### 6.4 Claude Managed Agents
**Core difference:** with a raw loop or SDK, your code runs the iteration. With Managed Agents, **Anthropic runs the loop and sandbox**. You define the agent **once** (model, system prompt, tools, MCP servers, skills), refer to it **by ID**, send user events, and **stream results back over server-sent events (SSE)**.

| Category | What you stop owning | What you take on |
|---|---|---|
| Execution & infrastructure | Iteration loop, execution sandbox, in-loop retries, tool-execution runtime (all server-side) | An agent definition as a **versioned API resource**, plus an application layer that sends events and consumes streamed results |
| Session duration & state | Long-running execution management; sessions can run **minutes or hours** without your process holding the loop open | **Server-side session state**; sessions are stateful, stored by Anthropic, subject to its data handling policies |
| Sandbox lifecycle | Provisioning and teardown | Dependency on the managed sandbox's available tools and execution model |

**Choose Managed Agents when:**
- The task **runs long** (minutes/hours).
- You want a **managed sandbox** (avoids building and securing an execution environment).
- You'd rather not build the loop, sandbox and tool-execution layer, and accept defining the agent as an API resource.

**The deciding constraint for regulated work:** Managed Agent sessions are **stateful and stored server-side**, which is why they are **not currently eligible for Zero Data Retention (ZDR) or a HIPAA BAA**. If your workload carries **PHI or a ZDR requirement, this path is ruled out** regardless of fit. Route to the Agent SDK or a raw loop on a covered configuration. *The governing constraint picks the path before convenience.*

**Common progression:** prototype on the Agent SDK locally, then move to Managed Agents for production. The agent definition carries over *conceptually*, but the format changes: Agent SDK = code-level and filesystem configuration; Managed Agents = versioned API resource. Expect a **re-expression step, not a direct export**.

**Summary:** *Handles well:* long-running agents; avoiding building/securing a sandbox and loop. *Adds:* server-side stateful sessions, agent-as-resource format, a beta surface that can change. *Use different approach:* PHI/ZDR workloads or when you need full in-process control.

### 6.5 The four loop steps (hold across every path)
With a raw loop you implement all four. With the Agent SDK it provides structure for registering tools, setting the system prompt and iterating, but **your code still handles tool execution**.
1. **Register tools:** same schema structure; the SDK registers them against the agent.
2. **Set the system prompt:** scope it to the agent's task. Broad prompt → broader, less reliable routing. A prompt naming the specific task and available tools → more consistent behavior.
3. **Handle the tool-use loop:** whoever iterates, your code executes every tool call and returns a tool_result.
4. **Define exit conditions:** without explicit exit conditions the agent keeps requesting tool calls beyond what the task needs. Define when "done" means done.

**Loop wiring checklist (verify on every path):**

| # | Item | Verify |
|---|---|---|
| 1 | Tools registered | Every needed tool is in the registration list; no unregistered tools referenced in the system prompt |
| 2 | System prompt scoped | Names the task and available tools; doesn't describe tools the agent lacks; doesn't omit tools that need scoping guidance |
| 3 | Tool-use loop implemented | Handles every tool_use block and returns a tool_result for each **before the next assistant turn**; all tool_use blocks from a single assistant turn resolved together |
| 4 | HITL insertion point defined | At least one HITL check in the loop |
| 5 | Exit conditions defined | A stopping criterion that doesn't depend on Claude volunteering to stop |

### 6.6 Human-in-the-loop (HITL)
**Definition:** a checkpoint that pauses execution and routes to a human review before proceeding. **Deciding question:** *what is the worst possible outcome if this step runs without a human check?*

| Insertion point | Trigger | Risk addressed |
|---|---|---|
| **Before a destructive tool call** | Agent about to execute a write, delete or send | **High**: irreversible actions |
| **After a planning step** | Agent generated a plan and is about to execute | **Medium**: wrong plan produces the wrong outcome even if every step executes correctly |
| **On unexpected output** | Tool result has an error flag, empty result, or out-of-bounds value | **Variable**: catches failures retry logic alone won't resolve |

### 6.7 Tool orchestration: over- and under-tooling
Routing is shaped by (a) how tools are described and (b) how many are registered.
- **Too many tools with overlapping descriptions** → erratic routing.
- **Too few tools** → agent hallucinates a path or returns an incomplete result.
- **Over-tooling is the more common production problem.** Teams register everything "just in case" and selection quality degrades as the tool surface grows. **Start with the minimum set** and add tools only when a specific capability gap is confirmed.

### 6.8 Regulated data constraints (decide before wiring)
Constraints (attorney-client privilege, HIPAA, GDPR, FedRAMP, internal data-residency) decide **which endpoint your code calls, which credentials it carries, and where logs land**, before any decisions about prompts, tools or memory. Wrong client configuration is far more expensive to undo after the agent is wired. Developers usually don't pick the surface but they write the code that targets the endpoint, attaches credentials, configures region and emits logs.

| Constraint | Rules out in code | Usually survives review |
|---|---|---|
| **Attorney-client privilege** | Calls from a consumer-grade Claude.ai surface the firm can't audit end to end; sending privileged content to any endpoint not firm-approved, however the prompt is structured | Direct API/SDK calls from the firm's own app, **SSO**-authenticated, through a **firm-approved LLM gateway** with full request/response logging. Anthropic does **not** capture conversation content (prompts, responses, tool payloads) by default on direct API traffic, so the org must implement **application-layer logging** to an approved destination. Tool calls/results stay inside the audited path. Confirm final logging design with the Anthropic account team |
| **HIPAA (PHI)** | Sending PHI to any endpoint/route not covered by a **BAA** for the specific configuration; includes logging/retention paths not scoped under the same BAA | Direct API/SDK on a **BAA-covered configuration** (arranged with Anthropic, which provisions a dedicated HIPAA-enabled org enforcing feature restrictions), or cloud-mediated via **AWS Bedrock / GCP Vertex** on the partner's HIPAA-eligible account. BAA does **not** cover Console, Workbench, beta features, or consumer plans. Not all API features are covered; check the feature eligibility list in the Implementation Guide |
| **GDPR / data residency** | Routes where the execution region can't be pinned in code, or the request may be served outside the approved boundary. Defaulting to a **global endpoint without specifying region** is the classic break | **Bedrock or Vertex with the region pinned** in client config. The **direct Anthropic API does not currently provide EU data residency**, so EU-residency partners should use Bedrock/Vertex |
| **FedRAMP / government** | Any path calling an endpoint not on an authorized cloud environment at the required impact level, including dev/test hitting the commercial endpoint while prod hits the authorized one (credentials and code patterns leak between them) | Three authorized routes (as of publish): **Claude for Government (C4G)**, direct FedRAMP High via Palantir Federal Cloud Service – Supporting Services (PFCS-SS); **Bedrock GovCloud**, FedRAMP High and DoD IL4/5; **Vertex AI Assured Workloads**, FedRAMP authorized. **Claude Enterprise on AWS Marketplace is NOT FedRAMP authorized.** Verify at trust.anthropic.com |
| **Internal data-residency policy** | Calls from any SDK client configured against a cloud vendor outside the approved list, regardless of technical capability | The route on the partner's approved cloud vendor, i.e. whatever SDK client/endpoint their CIO has cleared. Build against it; don't switch mid-project because another looks easier |

- **SOC 2 is out of scope here:** it governs how systems are built/operated, not which endpoint is called. Covered in **Module 4**.
- **Forward pointer, Module 4** (Production Engineering, Evals & Security): IAM/privacy patterns, prompt-injection defenses, runtime guardrails, agent hardening.

### 6.9 Postmortem: the agent that edited a production file
- **Agent:** read/modify/write config files. Tools: `read_file`, `write_file`, `validate_config`. Loop: after each write, re-run `validate_config`; if it still fails, adjust and rewrite, **cap of 10 iterations**.
- **Testing:** scratch directory with a copy of the config; typically converged in 2-3 iterations.
- **Production:** agent found an out-of-range parameter, wrote a correction, validation passed, loop exited after **one** iteration (cap never reached). The parameter was a **rate limit** the customer's app relied on. `validate_config` only checked the schema's allowed range, **not whether downstream systems depended on the old value**. Within minutes the customer's app failed from throttling.
- **Root cause:** the loop did exactly what it was told. The **exit condition (validate passes) was scoped to the file being edited**, and there was **no checkpoint between "validation passed" and "write committed to the customer environment."**
- **Fix:** before the first `write_file` hits live config, pause and surface the proposed change for human review. Add an explicit branch between *proposed change ready* and *write committed*.
- **Lessons:** this is a **permissions question never asked during design**. A "wrong write" in a disposable test environment never surfaces. **Design question: "What is the worst outcome if `write_file` runs without a human check?"** If a tool can take an irreversible action in production, it needs a checkpoint before it runs; register that constraint when scoping the tool surface, not after the first incident.

### Checkpoint 6 answers
**Gap 1, description for `update_record`:**
> "Use this to update a specific field on a customer record. Only call this tool after a `read_record` call has confirmed the current value and the proposed change has been reviewed. Do not use this for bulk updates or schema changes."

Why: states *when not to call*, *what must be true first*, and *what it must never be used for*. A bare description would let the agent call it whenever it inferred an update was needed, including before reading the current value or on unintended fields.

**Gap 2, HITL code** (inside the `for block in response.content` loop, before `execute_tool`):
```python
if block.type == "tool_use":
    if block.name == "update_record":
        print(f"Proposed update, customer_id: {block.input['customer_id']}, "
              f"field: {block.input['field']}, new_value: {block.input['new_value']}")
        approval = input("Approve this update? (yes/no): ").strip().lower()
        if approval != "yes":
            tool_results.append({
                "type": "tool_result",
                "tool_use_id": block.id,
                "content": "Update rejected by operator."
            })
            continue
    result = execute_tool(block.name, block.input)
```
Key points: the checkpoint sits **inside the loop**, **gates on tool name** (so `read_record` passes through, `update_record` pauses). A single up-front approval can't gate an update the model hasn't proposed yet. Approving *after* `execute_tool` is too late. **Note that on rejection a tool_result is still returned**, preserving the tool_use ↔ tool_result pairing.

*(Also in that partial implementation: the loop returns on `stop_reason == "end_turn"`, and handles `tool_use` by appending the assistant content, then collecting all tool_results into a single user message.)*

---

## 7. Agent memory: choosing scope for state that survives sessions

### 7.1 Framing
The agent so far works within one session but remembers nothing after it ends. **Memory scope** = what the agent knows at the start of the next session and what carrying that knowledge costs.

Related design patterns grouped under this objective (all built earlier): the **tool-use loop**; **multi-step task decomposition** (goal → ordered subtasks); **planning-and-execution** (separating deciding the plan from carrying it out, the split the post-planning HITL check guards); and **memory scope** (what state survives once the loop ends).

**Two opposing failure modes:**
- **Too much state in-context** → inflates every API call (full conversation re-read every turn; bill scales with session length).
- **Too little in persistent storage** → no memory across sessions (anything not written down vanishes when the conversation ends).

### 7.2 Memory scope table

| Scope | What persists | Cost | When to use | What you lose |
|---|---|---|---|---|
| **In-context** | State lives in the active conversation; survives turns within one session | Zero retrieval overhead; token cost inflates as conversation grows | Short sessions where all needed state fits in the window and nothing must carry across restarts | Everything when the session ends (a clear command or new session wipes it) |
| **External storage** | State written to a database, read back at session start or on demand | Retrieval latency per call + engineering work for read/write logic | State that must survive across sessions, move between users, or be shared across agent instances | Nothing on persistence; cost is latency and ongoing complexity |
| **Summarized memory** | Condensed version of prior conversation injected at next session start | Lower tokens than replaying history, but summarization drops detail | Long-running conversational agents whose full history would outgrow the budget | Any detail the summarizer didn't preserve; agent sees only what the summarization prompt kept |
| **Stateless (no persistent memory)** | Nothing; each session independent | No overhead | Task-execution agents that finish and close; independent pipelines | All prior context; follow-ups depending on earlier sessions are unreachable |

### 7.3 Choose at design time
- Multi-day user agent → store summaries/full history **outside** the context window and read back next session.
- Single-job agent → no prior session to recall → **stateless**.
- **The default trap:** store full history in the `messages` array and send it every call. Works at first, then token cost scales per turn, latency climbs, and a long session eventually hits the hard limit.
- Then you must refactor: pull conversation state out of live context, put it in external storage, add only what each turn needs. The refactor is mechanical (a few hundred lines plus a database the team already has); the **cost is timing** (production pressure, deadlines). **Design phase = cheap; refactor = expensive.**

**Summary:** *Handles well:* scope matches the task at design time (external for cross-session threads, stateless for self-contained jobs, in-context for short sessions needing no restart survival). *Adds cost:* external = retrieval latency + read/write logic; summarized = needs a well-specified summarizer prompt or task-critical state is dropped on every compression. *Avoid:* holding all state in-context assuming the window will be large enough; without caching or compaction, cost accumulates faster than expected. Measure actual session token usage against the window limit first.

### 7.4 Skills: reusable instructions loaded on demand
**Problem:** carrying *repeatable instructions* across tasks without paying to inject them in every session.

**What a Skill is:** a reusable markdown file teaching Claude how to handle a specific kind of task. Lives in a **`SKILL.md`** file inside an identified directory.
- **Two parts:** a **frontmatter** block (`name`, `description`) and the instructions below.
- **The description is the matching criterion.** Claude reads name + description of every available Skill, compares with your message, and loads full instructions **only on a match**. Irrelevant Skills never enter the context window.

**Contrast with other patterns:**
- In-context memory: always present, grows each turn.
- **CLAUDE.md:** behavior depends on environment. In **Claude Code CLI**, it loads into **every session** regardless of task. In the **Agent SDK**, whether filesystem settings including CLAUDE.md load is controlled by **`settingSources`**. **Set it explicitly** to the sources you intend; don't rely on defaults; confirm current default behavior in the Agent SDK reference.
- Skill: loads only when the task calls for it, in both environments.

| Pattern | When it loads | Context cost | Best for |
|---|---|---|---|
| **Skill (SKILL.md)** | On demand when request matches description | **Low**: only name + description at startup; full content on match | Task-specific expertise that shouldn't inflate unrelated sessions (domain output formats, specialized review checklists, workflows for a subset of tasks) |
| **CLAUDE.md** | Every session, unconditionally | Fixed overhead per session regardless of task | Always-on project standards (team coding conventions, required output format rules, constraints across all tasks) |
| **In-context instructions** | Every turn within the session | Grows with session length; doesn't survive session end | Short sessions fitting in the window; one-off exploratory work |

**Skills on the Messages API (beta):**
- Available today but **in beta**; configuration differs from Claude Code / Agent SDK paths.
- **Two beta headers required:** `code-execution-2025-08-25` and `skills-2025-10-02`.
- Skills run **inside the code execution container**, not the calling app's environment, which affects what tools and filesystem access a Skill can rely on.
- Beta headers are versioned and change toward GA: before building in production, check current docs for header values, GA status, and whether the code execution container is still the runtime path.

**Subagent constraint:** subagents **do not automatically inherit Skills** (or conversation history) from the parent; they start with a clean context. If a subagent needs a Skill, **list it explicitly in the subagent's configuration**. **However, subagents DO inherit the parent's permission context**; permission scope is not reset at delegation.

### 7.5 Postmortem: the agent that filled the window on session four
- **Agent:** assisted a support engineer on ongoing escalation cases.
- **Dev:** continuous sessions of 10-15 turns; in-context held full history fine; shipped **without measuring token usage per session**.
- **Production:** shorter sessions, but state **accumulated across sessions**. By **session four**, injected history exceeded **40,000 tokens** before a single tool call; with system prompt + tool schemas, **over 45,000 tokens** were consumed before the first productive turn. Remaining budget ran out; the agent returned incomplete results, **initially misread as a tool-selection failure**.
- **Fix:** a one-hour refactor to external storage: persist accumulated history to a database, inject only the relevant subset at session start. Under production pressure it took far longer than it would at design time.
- **Lesson:** development used one long session; production used **many short sessions with accumulated state**: different shapes. Measure expected state size per session (**history + system prompt + tool schemas**) against the context limit before defaulting to in-context.
- *(Takeaway #7 quantifies: refactor under pressure ≈ 1 hour; deliberate design choice ≈ 20 minutes.)*

### Checkpoint 7 answers
- Support agent over daily check-ins for two weeks, each session picking up where the last left off → **External storage**
- Document formatter: receives a file, transforms, returns, terminates → **Stateless**
- Coding assistant in one multi-hour session that won't continue afterward → **In-context**

---

## 8. Cumulative debug task (four bugs, four layers)

**Buggy code (key parts):**
- Tool: `get_customer_data`, description `"Gets data."`, param `id` (required).
- Loop: `messages = session_history + [user request]`; streams with `thinking={"type": "adaptive"}`; collects blocks via start/delta events; sets `stop_seen` on `message_stop`; then **`assistant_content = [b for b in assemble(blocks) if b["type"] != "thinking"]`** and **appends the assistant turn unconditionally**; `finalize(blocks)`; returns on `end_turn`; for each tool_use block executes the tool and **appends a separate user message per tool_result**.
- Memory: `build_session_history` concatenates **all prior session transcripts** into one list.

### Stage 1: identify the bugs

| # | Layer | Bug | Runtime effect |
|---|---|---|---|
| 1 | **Schema** | Vague description "Gets data." | Claude can't distinguish it from other retrieval tools; selects on surface matching, not intent |
| 2 | **Streaming** | Turn committed regardless of `message_stop`; thinking block stripped | Interrupted stream writes a partial/half-built tool_use into history; stripped thinking block breaks the carry-back rule so the **signature no longer matches** and the API rejects the next request |
| 3 | **Context** | Message structure around tool results (the model answer says only tool_result is appended with no preceding assistant tool_use turn) | API sees a tool_result referencing a tool_use it never received as a complete assistant turn → rejects |
| 4 | **Memory** | All prior session transcripts concatenated in-context | Window grows each session and fills before the agent processes the current request, by session 4-5 |

*Study note:* in the code shown, the assistant turn *is* appended before tool results. The official answer's Bug 3 is therefore effectively a **consequence of Bug 2** (a partial or stripped assistant turn breaks the pairing). Also notice a possible additional issue worth recognizing: **multiple tool_results are appended as separate user messages** instead of **one user message containing all tool_results**. That breaks the "all tool_use blocks from one assistant turn resolved together in the immediately following user turn" rule, which is the checklist's item 3. Know both readings for an exam.

### Stage 2: corrected versions
**Bug 1 (schema):** "Use this to retrieve full account and contact details for a customer by customer ID. Do not use this for order history or transaction records." (states intent + exclusion)

**Bug 2 (streaming):** keep all blocks including thinking; gate the commit on `stop_seen`; raise on interruption:
```python
assistant_content = assemble(blocks)   # keep all blocks including thinking
if stop_seen:
    messages.append({"role": "assistant", "content": assistant_content})
else:
    raise StreamInterruptedError("Discarding partial turn; retry from last complete turn.")
```

**Bug 3 (context):** per the model answer, resolved once Bug 2 is fixed: the full assistant turn including tool_use is appended before the tool_result, satisfying pairing. (Best practice: also collect all tool_results into **one** user message.)

**Bug 4 (memory):** external storage + inject only a summary at session start:
```python
def build_session_history(prior_sessions):
    if not prior_sessions:
        return []
    summary = load_session_summary(prior_sessions[-1]["id"])   # from external store
    return [{"role": "user", "content": f"Session context: {summary}"}]
```

---

## 9. Images, PDFs, and high-volume processing

### 9.1 Framing
Multimodal ingestion shifts the question from *what Claude remembers* to *what you send in*: **every image and PDF consumes context budget before Claude reads a character of your prompt.** The second half addresses thousands of inputs: sending one request at a time stops making sense, and the **Batch API** handles volume without blocking your app.

### 9.2 Image token cost
- Claude views images in **patches**: each **28×28-pixel** block = one visual token.
- **Formula:** tokens = ⌈width / 28⌉ × ⌈height / 28⌉.
- **Example:** 1,000 × 1,000 px → ⌈1000/28⌉ × ⌈1000/28⌉ = 36 × 36 ≈ **1,296 tokens**. Ten high-res screenshots ≈ a detailed system prompt's worth of context.
- Each model has a **maximum native resolution** (long-edge limit + visual-token limit) that **differs by tier**; newest models accept substantially larger images. Images exceeding either limit are **downscaled before processing**, so the formula runs on the *scaled* dimensions. Confirm current limits on the Vision page (Resolution and token cost); they change across generations.
- **Design-time rule:** measure the token cost of a *typical production image* against your model's context limit before writing ingestion code. The fix for an over-budget pipeline is often a **ten-minute resize step**. Run the formula against the **largest** expected input, not the inputs on hand (a high-res original can cost many times a thumbnail).

### 9.3 Ways to send an image
- **Inline base64:** encode bytes as base64 in the message block. Whole encoded payload travels **with every request** → larger requests, more latency on big images. Best for **one-off images** where an upload step adds complexity without payoff; reuse multiplies cost, so choose another method if reuse is likely.
- **URL reference:** image source given as a URL. *(The pasted text only shows the heading; details weren't included.)*
- **Files API:** **upload once, reference by `file_id`** in each request. Best for **assets reused across requests**. *(Details also not elaborated beyond the checkpoint and takeaways.)*

### 9.4 Sending PDFs: the `document` block
- Block type is **`document`**, not `image`.
- `source` follows the same pattern as images: **base64, URL, or Files API `file_id`**.
- **No required `name` field.** Optional `title` (readable name) and optional `context` (extra metadata); neither is required.
- Token cost considerations and Files API reuse apply the same way.
```json
{
  "type": "document",
  "source": {"type": "base64", "media_type": "application/pdf", "data": "<base64-encoded-pdf-bytes>"},
  "title": "contract_review.pdf"
}
```

### 9.5 Prompting techniques for multimodal inputs
The same techniques apply. A bare "describe this image" gives shallow output for the same reason a bare text prompt does: no target structure. Images carry ambiguity text doesn't (**overlapping objects, depth/spatial relationships, partial occlusion**), so the prompt should say how to handle each. Example constraint: *"If objects overlap, describe each separately and note the overlap."*

### 9.6 Message Batches API
- **Why:** synchronous calls block until complete; at scale you burn threads or run thousands of concurrent connections against rate limits.
- **Limits:** up to **100,000 requests or 256 MB** per batch (whichever comes first).
- **Flow:** submit the batch → receive a **`batch_id`** → **poll** for completion (check status on a schedule) → download results.
- **Cost:** per-token cost is **lower** than synchronous.
- **Trade-off:** latency is **non-deterministic**, **up to 24 hours** (often much faster). Suits offline pipelines, evaluation runs, data processing; **not** real-time interaction.
- **Results return in arbitrary order**, not submission order. Use the **`custom_id`** field on each request to match results to inputs.

| Use case | Pattern | Why |
|---|---|---|
| User uploads a photo, expects immediate classification | **Synchronous** | Real-time needed |
| Nightly pipeline classifying 5,000 customer records | **Batches** | Latency not a constraint; cost reduction + async valuable |
| Eval run testing a new prompt on 2,000 examples | **Batches** | Offline, no real-time need |
| Chatbot replying to a user's message | **Synchronous** | User is waiting |

### 9.7 When multimodal + batch fit together (and don't)
- **Fits:** offline workloads reusing the same assets and needing structured output across thousands of inputs. Textbook case: a nightly pipeline classifying images against a fixed taxonomy: **Files API** removes redundant uploads, **Batches API** absorbs latency, **structured-output techniques** keep results machine-readable.
- **Two failure modes:**
  1. **Misreading latency:** using batch in any user-facing image flow → passes tests, fails in production (the user is waiting, the batch isn't).
  2. **Underestimating context cost:** multiple large images per request blow past token limits at scale. Measure on production-scale inputs first.

### 9.8 Postmortem: the batch job that wasn't a batch
- Developer's nightly classification job hit rate limits at about the same point three nights running. They had "split it into chunks" but were **looping and calling the API per item**.
- **Diagnosis:** chunking isn't batching: it's **serial calls against the synchronous endpoint**; the API still sees one request per item, back to back. The same number of calls → the same rate limits.
- **Fix:** the **Message Batches API**, a *different submission model*, not a smaller batch size. Rate limit doesn't fire because you're not making thousands of individual requests.
- **Remember:** use batch for high-volume **offline** work; synchronous only when a user is waiting; match results with `custom_id`.

### Checkpoint 8 answers
- Reference product diagram used in **every** request → **Files API** (upload once, reference `file_id`)
- One-off UI bug screenshot in a single request → **Inline base64**
- Classifying 5,000 feedback responses → **Message Batches API**

---

## 10. The eight takeaways (one per objective)

1. **Failure type → missing technique.** Wrong shape → output constraint; drift across turns → system prompt; hallucinated structure → few-shot. Rewording rarely works. When prompt-level instructions aren't enough, use **structured outputs** (JSON outputs on the final response; strict tool use on tool arguments), at the cost of first-call compilation latency and added input tokens.
2. **Match reasoning depth to the task.** Enable reasoning only where it changes the answer; calibrate effort; thinking blocks return **unchanged** or the next request fails. Model selection is covered in the earlier MSO Foundations module.
3. **A stream ending ≠ a message completing.** Act on a block only after it closes; commit to history only after `message_stop`; on interruption discard the partial turn and retry. Recognize a tool-use error on a *retry* as a half-built block from a dropped stream, not a schema problem.
4. **Wrong-tool selection → the schema, usually the description.** Add an **exclusion condition** (when *not* to call) to every description at design time. **MCP** lets you connect a maintained server, but each connected server adds tool definitions to the context whether used or not, so connect deliberately and control loading cost.
5. **Context is a fixed budget; tool outputs spend it fastest.** Production outputs run **3-5×** fixtures; a session fine for 50 turns in test can hit the ceiling at turn 8. Pruning, compaction, subagent handoffs buy headroom in different ways (depends on whether you still need earlier state). Degrading selection after a fixed number of turns → check the **window first**, not the schema.
6. **Workflow-or-agent sets the cost of everything after; HITL belongs in the design.** Workflow = you can write the exact steps; agent = you can specify the goal and tools but not the path. Wrong choice either way shows up in production. Irreversible tool → HITL checkpoint **before the loop is wired**.
7. **Memory scope follows the shape of the session**, not ease of implementation. In-context is easiest and fails earliest; external adds latency but survives; summarized cuts cost but loses what the summarizer drops; stateless suits jobs that close out. Refactor under pressure ≈ **1 hour** vs ≈ **20 minutes** at design time. **Skills** carry repeatable instructions on demand via description matching.
8. **Calculate multimodal cost before writing ingestion code; match the API to the workload.** Image = ⌈w/28⌉ × ⌈h/28⌉ tokens; ceilings differ by tier. Inline base64 = one-offs; Files API = reused assets; Batches = offline at lower cost with non-deterministic latency. **Looping the synchronous API is not batching.**

**What comes next:** this module established the Developer primitive library (five interaction types) that all later Developer modules draw from: prompting craft, tool schemas, context engineering, agent construction, memory scoping, multimodal ingestion.

**Sources cited by the module:** Claude 101; Claude Code 101 In Action; AI Fluency Framework Foundations; Building with the Claude API (all Skilljar); platform.claude.com (canonical reference, re-verify at publish); Anthropic Blog "Building Effective Agents".

---

## 11. Glossary

- **Claude Agent SDK:** managed agent runtime (`@anthropic-ai/claude-agent-sdk` for TypeScript, `claude-agent-sdk` for Python). Gives programmatic access to the same agent loop that powers Claude Code (iteration, tool execution, observation, termination) so you can embed an agent in your product. **Distinct from the Anthropic SDK**, a thin API wrapper that does *not* run an agent loop.
- **Context window:** total tokens a model can process in one request (system prompt, history, tool definitions, tool results, and the model's own output). At the limit, earlier content must be removed or summarized.
- **Function signature:** the declaration of a function: name plus parameters (names, types, defaults).
- **HITL:** human-in-the-loop; a human review/approval step before consequential action.
- **Refactor:** changing internal code structure without changing external behavior.
- **SOC 2:** AICPA audit framework for how a service organization handles customer data; the standard most cited for SaaS/cloud security practices.
- **State:** information an agent carries between turns (conversation so far, user's request, earlier tool results).
- **stop_reason:** API field saying why generation stopped. Key agentic values: **`end_turn`** (finished, no further action) and **`tool_use`** (one or more tool_use blocks issued; awaiting results). *(Part 1 also covered `refusal`, `max_tokens`, `model_context_window_exceeded`.)*
- **Subagent:** separate agent instance spun up by an orchestrator for a discrete subtask. Does **not** inherit conversation history, skills or context; starts clean and must be configured explicitly. Results return to the orchestrator. *(Caveat from section 7.4: permission context **is** inherited.)*
- **Token:** unit Claude uses to measure text. Characters-per-token varies by tokenizer and model generation; treat rules of thumb as model-dependent. Consumed by everything in the window; basis for pricing and context budgets.
- **tool_use block:** assistant content block when Claude wants to call a function: tool name, unique ID, input args. Must be answered by a matching tool_result in the **immediately following user turn**, with the ID preserved exactly.

---

## 12. Master cheat sheet (Part 2 additions)

**Pattern / path decisions**
- Can you list the steps in code? → **workflow**. Goal + tools but no fixed path? → **agent**. Always try single call → workflow → agent.
- PHI or ZDR? → **not Managed Agents**; use Agent SDK or raw loop on a covered configuration.
- Long-running (minutes/hours) + no desire to build sandbox/loop → **Managed Agents** (beta, server-side state).
- EU residency → Bedrock/Vertex with region pinned (direct API has no EU residency).
- FedRAMP → C4G, Bedrock GovCloud, or Vertex Assured Workloads (**not** Claude Enterprise on AWS Marketplace).

**Agent loop must-haves:** tools registered · scoped system prompt · every tool_use answered (all results together, next user turn) · HITL point · exit condition not reliant on Claude volunteering to stop.

**HITL placement:** before destructive calls (high) · after planning (medium) · on unexpected output (variable). Always return a tool_result on rejection.

**Memory:** in-context (short, one session) · external (cross-session, shared) · summarized (long-running; depends on summarizer prompt) · stateless (self-contained jobs). Skills = on-demand instructions; CLAUDE.md = always-on; set `settingSources` explicitly in the Agent SDK; subagents don't inherit Skills but do inherit permissions.

**Multimodal/batch:** image tokens = ⌈w/28⌉×⌈h/28⌉ · base64 = one-off · Files API = reuse · PDF = `document` block (optional `title`/`context`, no required `name`) · Batches = ≤100,000 requests or 256 MB, up to 24 h, cheaper, arbitrary result order → `custom_id`, poll with `batch_id` · chunked loops ≠ batching.

**Beta headers / names to remember:** Skills on Messages API → `code-execution-2025-08-25` + `skills-2025-10-02` · MCP connector (from Part 1) → `mcp-client-2025-11-20` · Module 4 = Production Engineering, Evals & Security (SOC 2, prompt injection, IAM).

---

**Gaps in the source:** the pasted text for the "URL reference" and "Files API" image-sending tabs, and the Raw Messages API vs. Agent SDK tab details beyond the raw loop, were truncated or absent. The notes above reflect only what was provided; verify those parts in the original course or the docs.


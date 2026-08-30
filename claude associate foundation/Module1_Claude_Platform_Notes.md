# Module 1: Claude Platform & Model Foundations — Notes

## Purpose of the Module
Four decisions determine the **quality ceiling** of every Claude session:
1. Entry point
2. Capability layer (features activated)
3. Model selection
4. Context management across a session

**Learning objectives:**
- Select the right entry point + feature set for a task
- Differentiate Haiku / Sonnet / Opus by capability and fit
- Match model to quality/speed/volume needs
- Manage context limits and use Memory for continuity

---

## 1. Five Behavioral Properties of Generative AI (apply regardless of feature used)

| Property | Key Point |
|---|---|
| **Responses vary** | Same prompt ≠ same output. Outputs are probabilistic, not retrieved. Build review into workflows. |
| **Confident tone ≠ accuracy** | Fabricated and verified info can sound equally fluent. Verification habits needed (Module 3). |
| **Context is a budget** | Conversations have a working-memory limit. Claude.ai auto-summarizes earlier messages as the limit nears (paid plans w/ Code Execution). Summaries lose detail over time. |
| **Knowledge has a training boundary** | Claude's training has a cutoff date. Use web search (Chat toggle) or Research for recent info. |
| **Configured procedures still vary** | Even a well-built Skill reduces—but doesn't eliminate—output variance. Review is still required. |

---

## 2. Entry Points: Chat, Projects, Artifacts, Research

### Chat
- Default, unstructured conversation entry point
- Saved to history; can be continued later
- Memory + past-chat search can carry some context forward
- **Lacks**: standing instructions, curated knowledge base (Project features)
- **Best for**: one-off questions, quick drafts, exploratory work, non-repeating tasks
- **Signal you've outgrown it**: re-pasting the same background info every session

### Projects
Persistent workspaces containing three elements:
1. **Standing instructions** – consistent behavior/knowledge across all conversations in the space
2. **Knowledge base** – documents/files uploaded once, reused without re-uploading
3. **Conversation history** – project-specific list; conversations share Project context but **not** context with each other

- **Solves**: repeated re-explanation of background each session
- **Build a Project if ≥2 of these are true:**
  - Task recurs
  - Background context is stable
  - Output format is consistent
- Setup time typically recovered within 2–3 sessions

### Artifacts
- Used when the result is a **deliverable**, not a conversational reply
- Appears as a separate, editable block alongside chat
- **Use for**: draft documents, data tables, reports, code
- **Use inline responses** for content you'll act on within the conversation itself

### Research
- Deep multi-source synthesis (paid plans)
- Regular Chat also supports web search via a per-chat toggle (all plans); on Team/Enterprise, an Owner/Primary Owner must first enable it workspace-wide
- Default on/off state of the toggle isn't specified in docs
- **Use Research** for deep investigation/synthesis across many sources
- **Use Chat + web search** for quick current-info lookups

### Selection Logic Table

| Task Type | Entry Point |
|---|---|
| One-off/quick, no reuse | Chat |
| Recurring, stable context | Project |
| Deliverable the recipient opens/reads | Artifact |
| Deep multi-source investigation | Research |

### Worked Example: Weekly Status Report
- **Wrong**: New Chat every Monday, retype background each time → 40 min/session (~12 min pure context-loading)
- **Right**: Built a Project (background/team/stakeholders in knowledge base; format/escalation rules in instructions) → 25 min/session, same quality
- **Takeaway**: Entry-point choice eliminated the repeated setup tax

---

## 3. Capability Layer: The Four-Layer Model

| Layer | Function |
|---|---|
| **Projects** | Carry context — background knowledge & standing instructions |
| **Skills** | Define procedures — how a task is executed consistently every time |
| **Code Execution** | Verify computations — ensures correctness, not just plausibility |
| **Memory** | Persist continuity — carries relevant facts across sessions automatically |

- Layers are **independent** — combine as needed
- Simple one-off task → none needed; complex recurring analytical workflow → possibly all four

### Skills
- Reusable procedures; Anthropic provides built-ins for Excel, Word, PowerPoint, PDF tasks
- Custom Skills addable via settings
- Live at the **account level** (not inside one Project)
- Invoked automatically when relevant, in or outside a Project

### Code Execution
- Claude's sandboxed environment — Claude writes/runs code internally and returns verified results
- **Why it matters**: Claude generates text via probability, so unaided calculations can look plausible but be wrong
- **Use Code Execution when:**
  - Numeric output will be used/reported
  - Data needs transforming/cleaning (dates, dedup, formatting)
  - Output needs a chart/visualization
  - Output needs to be a real downloadable file (.xlsx, .pptx, .docx, .pdf)

### Memory
- Retains work-relevant facts across sessions (role context, format preferences, collaborator names, standing constraints)
- **Curation practices:**
  - Review stored memories at least monthly (active users)
  - Delete/update outdated entries
  - Keep set focused on genuinely recurring info
- **Project-scoped Memory**: separate memory context per Project (e.g., Client A ≠ Client B)
- **Incognito mode**: keeps a standalone chat out of Memory/history (doesn't override org data retention policies)
- **Memory import** (experimental, as of June 2026): documented for Free/Pro/Max/Team (not Enterprise); fallback if unavailable = manually add facts to Memory settings (not into a Project knowledge base)

### Worked Example: Monthly Regulatory Report
- **Before (Chat only)**: re-upload docs, re-paste context/instructions each month; 65 min/session; manual verification caught 2 errors (month 1), 1 error (month 2)
- **After (full capability layer)**: Project (context + format), knowledge base (prior reports), Skill (report format), Code Execution (calculations) → 30 min/session; 0 errors months 3–8

| Question | Points To |
|---|---|
| Same every time? | Standing instructions + Skill |
| Reference material recurs? | Knowledge base |
| Needs correct (not just plausible) output? | Code Execution |
| Context to carry across sessions? | Memory |

---

## 4. Model Selection: Haiku, Sonnet, Opus

| Model | Characteristics | Best For |
|---|---|---|
| **Haiku** | Fastest, most efficient | Structured tasks: classification, extraction, formatting, straightforward summarization; high-volume/routine work where speed matters and imperfect output is low-cost |
| **Sonnet** | Balanced tier | Full range of professional work: drafting, synthesis, analysis, research assistance, document review — **default starting point** |
| **Opus** | Highest capability | Nuanced judgment, complex multi-step reasoning, ambiguous inputs, client-facing deliverables, strategic planning, high-stakes multi-source synthesis |

**Escalation rule**: Start with Sonnet → if quality falls short on complex work, move to Opus. If speed/volume matter and task is structured, consider Haiku.

### Decision Logic

| Task Profile | Model |
|---|---|
| Routine, structured extraction/classification at volume | Haiku |
| Most professional drafting/synthesis/analysis | Sonnet |
| Complex judgment, high-stakes, ambiguous/multi-layered | Opus |

### Cost/Usage Notes
- Opus = better on complex tasks but slower
- On metered/usage-budgeted plans (incl. API), higher tier = more usage consumed per call → speed/capability trade-off also becomes a cost trade-off
- On standard claude.ai subscription: treat speed/usage headroom as the practical proxy for cost
- **Caveat (as of June 2026)**: a fourth tier above Opus exists (Claude Fable 5, GA June 9, 2026); Opus latency now described as "moderate" not "slow." The certification still uses the three-tier Haiku/Sonnet/Opus frame — treat exact tier names/traits as verify-at-delivery; **not exam-relevant**.

---

## 5. Context Limits, Conversation Hygiene & Memory Management

- Every conversation has a finite working-memory budget that depletes as it grows
- As the limit nears, claude.ai auto-summarizes earlier messages (paid plans w/ Code Execution); full history remains referenceable
- Practical effect: detailed instructions given early can lose force later in a long session (not because Claude ignores them — the context gets compressed)

### Signs a Conversation Needs Intervention
- Claude stops following earlier-established instructions
- Responses address only the latest exchange, ignoring earlier decisions/context
- Accuracy drops in ways consistent with lost early-session context

### Three Responses to Context Degradation

| Response | When to Use |
|---|---|
| **Restart** | Session has drifted beyond recovery, or a genuinely new task is starting within the same workstream. Project's standing instructions/knowledge base carry forward automatically; the thread does not. |
| **Summarize** | Ask Claude to summarize current state (decisions, in-progress work, open questions) → paste into new conversation. Preserves continuity without carrying degraded context forward. |
| **Persist** | Save info needed across *all* future sessions to Memory or the Project knowledge base — more efficient than repeated re-entry. |

### Memory Curation (repeated emphasis)
- Treat like a working file — review regularly, delete expired entries, update changed facts
- **Accuracy of stored memories matters more than volume**

### Usage Limits
- Operate on multiple time windows: short rolling session window + weekly limits (paid plans) across models, plus a separate weekly pool for the highest model tier
- Specific windows/allowances vary by plan and change over time — verify in Help Center
- For intensive tasks: break into segments, save interim progress to knowledge base, restart from a summary rather than pushing one long session

---

## 6. Applied Exercise — Configuration Matching Reference

| Card | Configuration | Reason |
|---|---|---|
| A | Project + Skill · Sonnet | Recurring structured task, stable context, fixed output format |
| B | Research · Sonnet | Current-information task beyond reliable training recall |
| C | Code Execution · Haiku or Sonnet | Calculation on defined dataset; accuracy required |
| D | Project (knowledge base) + Artifact · Opus | Nuanced multi-source analysis, ambiguous inputs, high-stakes deliverable |
| E | Chat + Artifact · Sonnet | One-off drafting task; no capability layer needed |
| F | Project + Code Execution + Skill · Sonnet | Recurring workflow, verified numeric outputs, consistent format |

**Scenario → Card mapping (for self-check):**
- S1 (competitor launches, last 90 days) → **B** (current info)
- S2 (weekly meeting notes, fixed template) → **A**
- S3 (15-page board analysis, ambiguous signals) → **D**
- S4 (800 survey responses, completion-rate flagging) → **C**
- S5 (monthly variance analysis, recurring + verified + templated) → **F**
- S6 (one-off vendor reply using policy doc + email) → **E**

---

## 7. Quiz Answer Key (with rationale)

| Q | Answer | Rationale |
|---|---|---|
| Q1 | **B** | Project (stable threshold doc in knowledge base + standing format instructions) + Code Execution for comparison math |
| Q2 | **C** | Code Execution gives a verified result rather than a plausible-looking guess |
| Q3 | **C** | Haiku — structured, unambiguous, high-volume classification task |
| Q4 | **C** | Restart with a pasted summary of decisions/progress — best response to context degradation |
| Q5 | **C** | Separate Projects per client + Project-scoped Memory keeps client contexts isolated |

---

## 8. Five Takeaways (Module Summary)

1. **Select the entry point before writing the prompt.** Chat = one-off; Project = recurring/stable; Artifact = deliverable output; Research = current multi-source info.
2. **Four capability layers, four distinct problems.** Projects (context), Skills (procedure), Code Execution (verified computation), Memory (continuity) — combine as needed.
3. **Model tiers = speed-capability trade-off.** Haiku (fast/structured/volume), Sonnet (most work), Opus (complex/high-stakes/ambiguous).
4. **Context is a budget.** Long conversations degrade — Restart, Summarize, or Persist rather than pushing through drift.
5. **Variation is inherent; review is structural.** Even configured Skills vary run to run — evaluation of output remains necessary (developed further in Module 3).

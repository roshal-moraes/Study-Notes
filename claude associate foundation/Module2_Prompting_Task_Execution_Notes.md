# Module 2: Prompting & Task Execution — Notes

## Purpose of the Module
The same request, phrased differently, produces different output quality — the model doesn't change, the **prompt** does. This module treats prompting as a **learnable structural skill**, anchored in the AI Fluency Framework competency of **Description**: telling Claude precisely what you want.

**Learning objectives:**
1. Build effective prompts using a repeatable component structure
2. Apply task decomposition to complex, multi-part requests
3. Iterate prompts diagnostically to improve output
4. Adapt prompting strategy by task type (analysis, research, drafting, brainstorming)

**Core deal**: Consistent quality comes from structure, not cleverness — five components, decomposition, diagnostic iteration, and task-type calibration cover the entire Prompting section of the exam.

---

## 1. Anatomy of an Effective Prompt — The Five Components

| Component | Role/Definition |
|---|---|
| **Role** | Who Claude should be for this task (e.g., financial analyst, editor, policy reviewer). Sets vocabulary, depth, assumptions. |
| **Context** | Background Claude cannot infer: audience, situation, prior decisions, source material. **Most commonly omitted component.** |
| **Task** | The specific action as one clear, unambiguous verb: "summarize," "compare," "draft," "identify." |
| **Constraints** | Boundaries: length, tone, what to include/exclude/avoid. Keeps output usable without heavy editing. |
| **Output format** | Shape of the result: table, bulleted list, memo, email draft. Stating this up front saves an iteration round. |

- Not every prompt needs all five — a quick question may need only task (+ maybe a constraint); a client deliverable needs all five
- **Skill = noticing which components a given task requires**

### Description in Practice
- Description = habit of making each component **explicit**, not assumed
- Claude cannot see your inbox/org chart/meetings unless connected via a Connector — and even then, only what's been allowed
- Anything living only in your head or outside a connected source = **context gap** → most common cause of underperformance for new users

### Diagnosing a Weak Prompt (preview of Lesson 4)
| Missing Component | Resulting Problem |
|---|---|
| Context | Generic output |
| Clear task verb | Wrong action taken |
| Constraints | Wrong length/tone |

---

## 2. Worked Example: Weak vs. Strong Prompt

**Weak**: *"Write a summary of our quarterly operations."*
→ Three plausible-but-generic paragraphs; could describe any company. Not wrong — just unusable, because almost nothing was specified.

**Strong**: *"You are an operations analyst [role]. I am preparing a one-page update for our regional director, who cares about throughput and cost, not process detail [context/audience]. Summarize the attached Q3 operations data [task], covering only the three metrics that moved more than 10 percent against target [constraint]. Format as a short headline followed by three bullet points, each one sentence [output format]."*
→ Same model, same data — draft usable in ~2 minutes vs. rebuilding from scratch.

**Habit to build**: Before sending any prompt that matters, mentally check: role, context, unambiguous task, constraints, format. ~30 seconds of specification saves multiple correction rounds.

---

## 3. Task Decomposition for Complex Requests

**Decomposition** = splitting a multi-part problem into discrete, ordered steps run in sequence, rather than one dense instruction.

### Why single-prompt complex requests underperform
Example: *"Evaluate these three vendors and tell me which to pick."*
→ Claude must invent criteria, apply them, weigh trade-offs, and recommend — all in one shallow pass, with no visible reasoning.

### The Decomposed Version (Vendor Evaluation Example)
| Step | Action |
|---|---|
| 1. Derive criteria | From the requirements doc, derive and weight evaluation criteria |
| 2. Score vendors | Score each vendor against those criteria using supplied materials |
| 3. Raise trade-offs | Identify where vendors diverge most |
| 4. Recommend | Recommend, with reasoning tied to weighted criteria |

**Benefits:**
- Each step produces a **checkable intermediate result**
- Errors (e.g., wrong criteria in Step 1) get caught early, not after the final recommendation
- Makes the process **auditable** — important when reasoning needs to be explained later

### One Conversation or Several?
- **Same conversation**: sequential steps that build on each other (each step needs prior results)
- **Separate conversation**: when a step is genuinely independent, or the conversation has grown long enough that early context is degrading (ties back to Module 1 context management)

### Parallel Case Example: Policy Change → 3 Deliverables
**Scenario**: Turn a 20-page policy change into (1) staff announcement, (2) staff FAQ, (3) executive briefing.

**Model decomposition:**
1. Extract substantive changes + practical meaning
2. Confirm the extraction is complete/accurate *before* building on it
3. Draft staff announcement (general audience) from confirmed list
4. Draft FAQ (anticipated staff questions)
5. Draft executive briefing (decisions + impact, compressed)

**Why this order**: Steps 1–2 build a **verified shared foundation**. Drafting before confirmation risks propagating the same error into all three deliverables. → **Sequence the shared, high-stakes extraction first; let parallel drafts follow.**

---

## 4. Iterating Prompts to Improve Output

**Core principle**: Don't rewrite the whole prompt when disappointed — diagnose which single component fell short and fix only that.

### Output Deficiency → Diagnosis → Fix Table

| Symptom | Likely Cause | Fix |
|---|---|---|
| Generic or off-base output | Thin context | Add background Claude couldn't infer |
| Wrong question answered | Ambiguous task verb | Sharpen the instruction |
| Wrong length/tone/shape | Missing constraint or format | Add it |
| Close but misses on one section | — | Iterate on that section only; don't discard a mostly-good draft |

**Why targeted revision beats wholesale rewriting**: Rewriting loses what worked and makes it impossible to tell which change fixed the problem.

### Live Iteration Cycle Example

| Round | Prompt | Output / Diagnosis |
|---|---|---|
| 1 | "Write a follow-up email to the client about the delayed deliverable." | Generic, slightly defensive; no reason or new date given. **Diagnosis**: thin context, no tone constraint. |
| 2 | "Write a follow-up about the two-day delay on the analytics deliverable. Cause: data-quality issue, now fixed; new delivery Thursday. Tone: accountable, not over-apologetic. Under 120 words." | Tight, accountable, complete. **Diagnosis**: strong; only subject line missing. |
| 3 | "Good. Add a subject line that signals resolution, not just delay." | Marginal further improvement → **signal to stop.** |

### Knowing When to Stop
Iteration has converged when additional rounds yield **marginal, not meaningful, change**. At that point, a quick manual edit beats further prompting. Goal = usable result, not a perfect prompt.

---

## 5. Adapting Strategy by Task Type

Same five-component stack applies everywhere, but **emphasis shifts** by task type — using one fixed style across all task types costs quality where it doesn't fit.

| Task Type | Wants | Latitude |
|---|---|---|
| **Analysis** | Tight constraints, explicit criteria/standards, defined ambiguity handling | Low creative latitude, high specification |
| **Research** | Clear scope, source discipline, defined question/boundaries, citations requested | Web search (chat toggle) for quick currency; Research (paid) for deep multi-source work |
| **Drafting** | Audience, tone, format specified | Medium — Claude finds the phrasing within your shape |
| **Brainstorming** | Loose constraints, high latitude; goal + boundaries only | High — ask for volume/range before narrowing; over-specifying kills divergence |

### Quick Reference: Tighten vs. Loosen

| Task Type | Tighten | Loosen |
|---|---|---|
| Analysis | Criteria, standards, scope | Phrasing |
| Research | Question, sources, citations | Synthesis approach |
| Drafting | Audience, tone, format | Word choice |
| Brainstorming | Goal and guardrails only | Quantity and direction |

### Four Mini-Demo Prompts

- **Analysis**: *"Compare these two vendor contracts on payment terms, termination rights, and liability caps. For each, state which contract is more favorable to us and why, in a three-row table."*
- **Research**: *"Using current sources, summarize how three named competitors positioned their Q2 launches. Cite each source. Flag anything you cannot verify."*
  - ⚠️ **Note**: citations are checkable only when grounded in web search/Research results — citations produced purely from training memory can look equally confident and should be independently verified.
- **Drafting**: *"Draft a 150-word LinkedIn post announcing our new reporting feature, aimed at operations managers, in a confident but not salesy voice."*
- **Brainstorming**: *"Give me 20 angles for a campaign around faster month-end close. Range widely; do not self-edit yet."*

**Underlying move**: Decide where you need **control** vs. **range**, then set constraints accordingly. This is what separates competent prompting from getting mediocre output on every task type equally.

---

## 6. Checkpoint: Diagnosing Weak Prompts (Practice Reference)

| Prompt | Likely Dominant Gap |
|---|---|
| "Make this better." (with draft attached) | Task specificity / constraints — unclear what "better" means (tone? length? clarity?) |
| "Give me everything you know about supply chain risk." | Context + constraints — no scope, audience, or focus; will produce an unfocused general answer |
| "Write a professional document about the project." | Context, task specificity, output format — too vague on all fronts |
| "Brainstorm names, but each must be one word, under eight letters, avoid these twelve terms, and match our exact brand voice guidelines." | Over-constrained for brainstorming — too many tight constraints kill the divergence brainstorming needs |

---

## 7. Exercise Walkthrough: Repair the Underperforming Prompt

**Weak prompt**: *"Summarize the customer feedback and tell me what to do."*
**Output**: Generic 5-bullet theme list + vague advice, not tied to actual data.
**Real goal**: PM has 200 survey responses; needs top 3 issues by frequency, each with a representative quote, ranked to guide this quarter's fixes.

**Gaps identified**: Role, Context, Task specificity, Constraints, Output format (essentially all five underspecified)

**Repaired prompt structure** (component-by-component):
- **Role**: e.g., "You are a product analyst..."
- **Context**: "I have 200 customer survey responses attached..."
- **Task**: "Identify the top three issues by frequency..."
- **Constraints**: "...include one representative quote per issue, ranked by frequency..."
- **Output format**: "...formatted as a ranked list I can use to prioritize this quarter's fixes."

---

## 8. Quiz Answer Key (with rationale)

| Q | Answer | Rationale |
|---|---|---|
| Q1 | **B** | Add context (audience, channel, key benefit) + output format — the actual missing specification |
| Q2 | **B** | Decompose into derive-criteria → score-each → trade-offs → recommend, for auditable, reliable results |
| Q3 | **C** | Adjust constraint/format components (length, tone) and resend — targeted fix, not a full rewrite |
| Q4 | **B** | Give goal + a few guardrails, then ask for volume/range before filtering — brainstorming needs latitude |
| Q5 | **B** | Add a constraint to use Code Execution for the calculation — ensures a verified, not just plausible, number |

---

## 9. Key Takeaways (Module Summary)

1. **Structure drives quality, not cleverness.** Role, context, task, constraints, format — run them before any prompt that matters.
2. **Context is the component you'll forget.** Claude can't see what's only in your head; context gaps cause most generic output.
3. **Decompose complex work into ordered steps.** Each step should produce a checkable result; build the high-stakes foundation first.
4. **Iterate on the component that failed.** Read output as diagnostic feedback, fix the one thing it points to, stop when improvement goes marginal.
5. **Match strategy to task type.** Analysis wants constraints; brainstorming wants latitude — decide where you need control vs. range, then set constraints accordingly.

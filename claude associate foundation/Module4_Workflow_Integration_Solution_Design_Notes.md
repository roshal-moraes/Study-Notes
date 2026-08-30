# Module 4: Workflow Integration & Solution Design — Notes

## Purpose of the Module
Distinction: **"I use Claude"** (personal productivity habit) vs. **"our workflow uses Claude"** (repeatable team process with Claude performing specific steps every time). Value compounds only with a **deliberate** shift — and the shift can go wrong if teams automate the wrong steps.

### Opening Case: Two Teams, Same Tool, Different Outcomes
- **Team 1**: Mapped the work, let Claude draft the redline, lawyer owned every final decision → review time halved, quality held
- **Team 2**: Pointed Claude at the whole process, let it approve low-risk clauses unsupervised → an approved clause created an uncaught obligation within a month → tool pulled entirely
- **Difference**: which steps each team chose to **delegate**

### Anchoring Competency: Delegation
Deciding, per step, whether work is **AI-appropriate**, **human-retained**, or **collaborative**. Deliberate Delegation is what turns individual wins into workflow value.

**Learning objectives:**
1. Apply Claude to analyze requirements and use cases
2. Leverage Claude for research, planning, process optimization
3. Use Claude to support solution design, development, iteration
4. Integrate Claude into existing workflows (augment or redesign)
5. Communicate Claude's value and limitations to stakeholders accurately

**Core deal**: Workflow value comes from *deciding which steps to delegate*, not automating everything.

---

## 1. Analyzing Requirements and Use Cases with Claude

Most real work starts messy: long documents, scattered email threads, verbal asks. Claude is a strong partner for **translating raw inputs into structured, testable requirements**.

- Upload raw inputs and ask for a **structured analysis**, not a narrative summary
- Vague business needs (e.g., "we need better reporting") → convert into specific task definitions: what report, for whom, how often, from what data, what format — each becomes a checkable requirement

### Worked Example: RFP Response Workflow
**Setup**: Proposal team responds to RFPs. Inputs = 40-page RFP + scattered internal email thread. Recurring task: turn into structured, answerable requirements list.

**Prompt**: *"From the attached RFP and the email thread, extract every distinct requirement the client is asking us to address. For each, give a short label, the exact RFP section it comes from, whether our thread already has an answer, and any requirement that is ambiguous and needs clarification. Return it as a table."*

**Output**: Requirements table — each row traced to its RFP section, marked answered/open, ambiguities flagged for client clarification.

**Configuration note**: This is a **Project**, not a one-off Chat:
- Past winning proposals → knowledge base
- Skills → carry formatting steps (live at account level, apply everywhere)
- Standing instructions → hold the extraction format
- No technical build required (Module 5 covers configuration in depth)

### Pressure-Testing the Requirements
Extraction is the first pass; pressure-testing makes it trustworthy.

**Prompt**: *"Review the requirements you extracted. Which are ambiguous as written? Which could be interpreted two ways by our proposal team? Which imply a requirement the RFP states only indirectly?"*

- Surfaces **hidden requirements** (buried in subordinate clauses or implied by evaluation criteria) — the ones that cost teams the bid when missed
- Structured list + pressure-test pass = far stronger than either alone
- **This is the Discernment habit from Module 3, applied at the requirements stage**

---

## 2. Research, Planning & Process Optimization

Planning mixes two things Claude handles differently:
- **Synthesis** (Claude excels) — gathering considerations, structuring options, trade-offs
- **Calculation** (must be verified) — numbers need to be computed, not generated

**Strongest planning workflows pair synthesis with code-executed analysis** so plans rest on computed, not generated, numbers.

### Research and Synthesis
- Claude synthesizes across sources to build a plan
- For post-cutoff current info: web search (chat) for quick lookups, Research for deeper up-to-date inputs
- Synthesis is where **unverified claims can enter** → Module 3 verification discipline applies throughout

### Code Execution for Verified Analysis
- When a plan depends on numbers, **have Claude compute them**
- Upload dataset → code execution runs calculations, produces trend charts, processes files
- A staffing plan on a *guessed* utilization rate is a guess; one on *code-executed* timesheet analysis is a plan

### Worked Example: Capacity Plan
**Setup**: Ops lead planning next-quarter headcount. Workflow: upload 4 quarters of ticket-volume data → code execution computes trend + per-analyst throughput → Claude synthesizes staffing recommendation from verified figures.

**Prompt**: *"Using code execution on the attached ticket data, calculate quarterly volume growth and average tickets resolved per analyst. Then, from those figures, recommend the headcount needed to hold our current resolution time next quarter, and show the assumptions."*

- Recommendation is only as trustworthy as the figures under it
- Because figures come from code execution (not prose), the plan **can be defended line by line**

### Where AI Insight Changes the Plan
- **Synthesis-heavy steps** (weighing many considerations) → Claude adds the most value
- **Judgment steps** (risk appetite, political reality) → stay human
- Scan a workflow for synthesis-heavy steps to decide where to apply Claude vs. leave to a person
- Capacity plan example: Claude's leverage = turning data into a defensible recommendation; the **hire/no-hire decision** (budget, hiring-freeze realities Claude can't see) stays with the ops lead

---

## 3. Solution Design, Development & Iteration

Claude = **design collaborator, not a vending machine**. Value shows up in an explicit loop, not a single request.

### The Iteration Loop
**Ideate → Prototype → Feedback → Refine → repeat** until the solution holds.
- Run inside a **Project** to keep context, constraints, and prior decisions stable across iterations — each iteration builds on the last instead of restarting

### Worked Example: Internal Process Tool (No Code Written)
A business analytics team needed a small internal dashboard tool. Instead of commissioning a build, they had Claude produce it as a **web artifact** and iterated by asking:

| Cycle | Prompt/Action |
|---|---|
| 1. Build | "Build a simple dashboard artifact that shows these five metrics from the attached data, with a chart for each." → working artifact produced |
| 2. Filter & totals | (iteration — refine functionality) |
| 3. Color & print | (iteration — refine presentation) |

### Knowing When to Escalate
- The artifact worked because it served a **small team's internal need**
- **Escalation signal**: when a solution becomes a **system others depend on** — uptime, security, or integration requirements — it has outgrown Associate scope and belongs with **Developer or Architect expertise**
- The moment people rely on it as infrastructure, it's no longer a prompt-and-iterate exercise

---

## 4. Delegation Mapping: Redesigning Workflows with Claude Inside

**Core skill of the module.** Before redesigning a workflow, map it step by step; classify each step as **AI-appropriate**, **human-retained**, or **collaborative**.

### Three Classification Criteria

| Criterion | Question |
|---|---|
| **Reversibility** | Can the step be undone if Claude gets it wrong? Reversible → tolerates more delegation; irreversible → demands human involvement |
| **Stakes** | What's the cost of an error at this step? High-cost → stays human-owned/reviewed |
| **Accountability** | Who is answerable for this step's outcome? Accountability does **not** delegate, even when drafting does |

### Building the Redesign
- Embed the right feature at each AI step: **Skill** for repeatable procedures, **Code Execution** for data steps
- Configured Skill consistency beats "heroic prompting" that relies on remembering the right wording
- Human-retained steps become **explicit review gates**, not afterthoughts

### Worked Map: Contract Review

| Workflow Step | Delegation | Why |
|---|---|---|
| Extract clauses from the contract | AI-appropriate | Reversible, low stakes, mechanical |
| Flag departures from company playbook | AI-appropriate | Reversible; a Skill carries the playbook rules |
| Draft the redline and rationale | Collaborative | AI drafts, human judges each edit |
| Approve or reject each change | Human-retained | High stakes, accountability doesn't delegate |
| Compute financial exposure of a penalty clause | AI-appropriate (Code Execution) | Numeric — must be computed, not estimated |
| Sign and send | Human-retained | Irreversible, external, legally binding |

**Key point**: AI does *real work* here (including the redline draft), not just a summary. The human owns decisions + irreversible steps — **that split is the redesign.**

### Recognizing Over-Delegation
- **Incorrect**: giving AI more than the risk profile justifies — e.g., letting Claude approve/send contracts *because it drafted them well*
- Drafting quality is **not a license** to delegate the decision
- Irreversible or high-accountability step assigned to AI = over-delegation → exactly where Team 2 (intro case) went wrong

### Common Mapping Errors
| Error | Description |
|---|---|
| **Halo delegation** | A step gets handed to AI just because the previous step went well — each step must be judged on its own |
| **Collapsing collaborative into automate** | "AI drafts, human reviews" quietly becomes "AI drafts" when the review gate is never actually staffed — an unstaffed collaborative step is really an automated step |
| **Mapping the tool, not the work** | Mapping around favorite features (e.g., a Skill already built) instead of actual workflow steps — map the work first, then adjust features |

---

## 5. Communicating Value and Limitations to Stakeholders

Integrating Claude into a workflow means describing it to people who didn't build it (manager, client, risk function). **Credibility comes from accurate claims — communicate limits as clearly as value.**

- Overstating capability → loses stakeholder trust on the **first visible miss**
- Accurate: *"Claude drafts the first pass redline, which a lawyer reviews."*
- Overstated: *"Claude handles contract review."* — invites a question the first error will answer badly

### Same Workflow, Different Audiences (Contract Review Example)

| Audience | Message Focus | Example |
|---|---|---|
| **Legal lead / Practice executive** | Outcome-focused | "Review time is down about half at the same approval standard. Every change is still approved by a lawyer before it leaves the building." |
| **Client risk function** | Assurance-focused | "AI assists drafting; qualified human reviews and approves every term. No contract is sent without human sign-off." |
| **High-literacy technical stakeholder** | Feature + failure-mode detail | "Claude extracts clauses, flags playbook departures, and drafts the redline. It does not approve changes — that gate stays with you. Known failure mode: it can miss obligations implied indirectly, so playbook-departure flags are a prompt for your read, not a substitute." |

**Same workflow, same human gate** — what changes is the level of detail each audience needs to trust it.

### Calibrate to the Audience
- Match message to audience's AI literacy
- Technical stakeholder → feature detail + failure modes
- Executive → outcome, oversight in place, risk posture
- Expectation set should match the actual capability boundary — no surprises later
- **This is the Description competency (Module 2) applied outward** — precise specification of what the tool can/cannot do, now directed at stakeholders

### Document the Human Oversight
- Name the review gates explicitly: *"Every output destined for a client passes human review."*
- Stakeholders trust an AI workflow **more**, not less, when human checkpoints are explicit

### Good vs. Bad Messaging

| Overstated | Accurate |
|---|---|
| "Our new AI system reviews contracts automatically." — sets a false expectation, hides the human gate | "Claude drafts the redline and flags playbook departures; our legal lead reviews and approves every change before anything is sent. The team's review time is down about half, with the same approval standard." — value and limits in one breath |

### Phrases That Quietly Overstate
- **"Fully automated"** — almost never true; first visible error exposes it
- **"Claude handles X"** — collapses the human gate out of the sentence
- **"It's basically as good as a person at Y"** — sets a standard the tool will eventually miss publicly
- **Fix**: state what the tool does, then identify the human checkpoint — every time

---

## 6. Exercise: Redesign a Workflow with Delegation Criteria (Reference)

**Workflow**: Expense-report approval

| Step | Likely Classification | Reasoning |
|---|---|---|
| 1. Extract line items and amounts from receipts | **Automate** | Mechanical, reversible, low stakes |
| 2. Check each expense against company travel policy | **Automate** (best served by a **Skill** — repeatable rule-based procedure) | Reversible, rule-based, consistent procedure needed |
| 3. Total the report and compute amount over/under policy limits | **Automate** (best served by **Code Execution**) | Numeric — must be computed, not estimated |
| 4. Draft a summary note explaining flagged items | **Collaborative** | AI drafts explanation, human reviews/refines |
| 5. Approve or reject the report | **Human** | Accountability and stakes — decision doesn't delegate |
| 6. Submit the approved report to finance for payment | **Human** (or automate only after approval gate) | Irreversible action tied to an approved decision |

- **Best served by a Skill**: Step 2 (policy check — repeatable rule application)
- **Best served by Code Execution**: Step 3 (totals/over-under limit calculation)

---

## 7. Quiz Answer Key (with rationale)

| Q | Answer | Rationale |
|---|---|---|
| Q1 | **B** | "Handle a large number of concurrent users" is the most ambiguous — no defined threshold, two engineers could build very different systems |
| Q2 | **B** | Use Code Execution to compute growth/throughput figures, then have Claude synthesize the plan from verified numbers |
| Q3 | **B** | Daily reliance by three departments = it's become a system others depend on → escalate to Developer/Architect expertise |
| Q4 | **C** | Approving/rejecting changes and signing the contract — irreversible, high stakes, accountability doesn't delegate |
| Q5 | **B** | "Claude drafts the redline and flags playbook departures; our legal lead reviews and approves every change before sending." — states value and limits accurately, names the human gate |

---

## 8. Key Takeaways (Module Summary)

1. **Delegate deliberately, do not automate indiscriminately.** Workflow value comes from choosing which steps Claude does, not handing Claude everything.
2. **Claude is a requirements-analysis partner.** Feed it messy inputs, get structured, traceable, testable needs others can act on.
3. **Build plans on verified numbers.** Pair Claude's synthesis with Code Execution so the figures under a plan are computed, not generated.
4. **Map every step against three criteria.** Reversibility, stakes, and accountability decide whether a step is AI-appropriate, human-retained, or collaborative.
5. **Communicate limits as clearly as value.** Accurate capability claims with human review gates named are what earn and keep stakeholder trust.

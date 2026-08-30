# Module 7: Troubleshooting & Optimization — Notes

## Purpose of the Module
When an output disappoints, most people default to one of two unproductive reactions: **give up** ("Claude can't do this") or **thrash** (change random things until it works). Diagnostic skill replaces both with a **sequence** — underperformance has findable root causes, and running the sequence usually turns a dead end into a five-minute fix.

**Learning objectives:**
1. Diagnose why a prompt/output is underperforming and trace it to root cause: under-specification, context overload, wrong feature/model, or stale configuration
2. Adjust approach based on output received, turning recurring corrections into persistent fixes
3. Optimize a workflow for efficiency by promoting shared context, format, and verification steps into Projects, Skills, and standing instructions

---

## 1. Common Failure Patterns and Their Root Causes

**Four patterns produce similar-looking bad output** — the skill is reading the **symptom timing** to know which one you're in.

| Symptom Timing | Root Cause | Description | Fix |
|---|---|---|---|
| **First response wrong** | **Under-specification** | Prompt left out context, constraints, or format — most common cause, cheapest to fix | Add what was missing |
| **Degraded over time** | **Context overload** | Long conversation approached context limit; earlier content auto-summarized, detail compresses, early instructions lose force | Restart or summarize — not a better prompt |
| **Specific error type** | **Wrong feature or model** | Asking for calculation in prose instead of code execution, or deep analysis from a speed-tier model | Use the right tool, not more prompting |
| **"Used to work"** | **Stale configuration** | A standing instruction, knowledge source, or Skill has drifted out of date, degrading output silently | Maintenance (Module 5) |

**Detailed signatures:**
- **Under-specification**: output was never right — the prompt never carried what it needed
- **Context overload**: session started fine, degraded as conversation grew
- **Wrong feature/model**: specific, repeatable error types — numbers subtly off (needs code execution) or shallow analysis on a task needing depth (wrong model tier)
- **Stale configuration**: same setup that worked last month no longer does, because a dependency drifted

---

## 2. Isolating the Cause

Before blaming the tool, locate the failure among four options:
- The **prompt** (specification)
- The **context** (window full or wrong material loaded)
- The **feature choice** (wrong entry point, model, or missing code execution)
- An **expectation mismatch** (the task was never one Claude could do well)

Naming which of the four it is points straight to the fix.

### The Diagnostic Sequence (Run in Order)
1. **Re-read the prompt** against the five components (Module 2) — is anything under-specified?
2. **Check conversation length** — is context overloaded, needing a restart or summary?
3. **Check feature and model** — does this need code execution, or a more capable model tier?
4. **Check configuration** — are instructions, knowledge, and Skills current?
5. **Only then** question whether the task is a fit at all

### Why This Order (Cheapest-Fix-First)
| Step | Relative Cost |
|---|---|
| Re-read prompt | Seconds — resolves the most common failure, goes first |
| Restart from summary | A little more effort |
| Switch feature/model | More cost |
| Check configuration | More cost |
| Question task fit | **Most expensive conclusion** — ends the attempt; reach only after ruling out cheaper causes |

**Common mistake**: most people invert this — jumping straight to "switch to the most capable model" or "this task is impossible," which is the expensive move that usually wasn't needed. **Discipline = resisting the urge to skip to step 5.**

---

## 3. Worked Example: A Failure Gallery

| Symptom | Diagnosis | Fix |
|---|---|---|
| "The summary keeps missing key points." | Under-specification — prompt never said which points matter | Name the criteria for "key" |
| "It stopped following my format halfway through." | Context overload in a long session | Restart from a summary, or persist the format to a standing instruction |
| "The numbers are subtly wrong." | Wrong feature | Move the calculation to Code Execution |
| "It worked last month, now it's off." | Stale configuration | Run the Module 5 maintenance checklist |
| "I asked it to predict next quarter's exact sales and the number was wrong." | **Expectation mismatch** (step-5 case) | No prompt/restart/feature/config change fixes this — the task asks for something the tool genuinely cannot do (precise future prediction). Fix: reshape the task — ask for a range with stated assumptions, or a model of adjustable drivers |

**Key insight**: Recognizing a genuine mismatch is as much a skill as fixing a fixable failure — it stops you from burning time tuning a prompt for an output that was never available.

**Bottom line**: The sequence usually takes a minute or two and ends in one of two places: a specific, cheap fix (most of the time), or a confident, reasoned "this task needs reshaping" (occasionally). Both replace the two bad defaults with an explainable decision. **Build the habit of reaching for the sequence automatically the moment output disappoints — before forming an opinion about whose fault it is.**

---

## 4. Optimizing Workflows for Efficiency and Effectiveness

**Optimization is deliberate, not accidental**: instrument the workflow, find the friction, promote the fix into configuration. Compounds over time — each redundancy removed and pattern promoted makes the workflow faster/more consistent for everyone.

### Find Redundancy and Friction
Look at a recurring workflow for steps that repeat unnecessarily: same context pasted every session, same correction every round, same manual reformatting at the end.
- **Friction is easy to live with, hard to see** — you absorb it one session at a time
- **Method**: watch one full cycle, note every step done by hand that was also done last time → this list is your optimization backlog

### Three Friction Signals
| Signal | Description | Fix |
|---|---|---|
| **Repetition** | You paste/type the same thing every run | Saved context or a standing instruction |
| **Correction** | You fix the same flaw in every output | A configuration change so the flaw stops appearing |
| **Variance** | Different people running the same task get different results | A shared Skill or knowledge base so everyone runs the same setup |

### Consolidate and Promote
Two moves do most of the work:
1. **Consolidate** steps that can run together rather than as separate prompts
2. **Promote** repeated patterns into Projects and Skills

### The Rule-Reference-Procedure Test
| Type | Home | Example |
|---|---|---|
| **Rule** (should always apply) | Standing instruction | "Always include the target segment" |
| **Reference** (material every run needs) | Project knowledge base | "Our brand voice guide," "the current product list" |
| **Procedure** (repeatable steps) | Skill | "Generate the weekly report in this exact format and order" |

**Why this matters**: putting a fix in the wrong home is why some "optimizations" don't stick — a procedure pasted as a one-line instruction loses its steps; reference material crammed into an instruction bloats every prompt.

### Promote with Caution
- Not every change is an improvement
- When moving a fix into configuration, **run the workflow a few cycles with the old approach still available** before fully relying on the new setup
- If the optimized version produces worse/less predictable output, catch it before it ships several times
- **Prove the gain on a few runs, then commit to it**

### Measure the Improvement
- Optimization you can't measure is hard to justify or sustain
- Track concrete gains: **time saved per cycle**, **revision cycles reduced**, **consistency improved across people**
- Example: a workflow that dropped from 40 to 25 minutes, or from 3 revision rounds to 1, is a demonstrable improvement

**Pick the metric that matches why the workflow mattered:**
- Customer-facing report → optimize **consistency and accuracy** more than raw speed
- Internal draft → optimize for **time**

**Diminishing returns**: identifying the right metric up front also tells you when to stop — once the metric you care about is good enough, further tuning is its own kind of friction.

### Worked Example: Workflow Efficiency Audit (45 min → 25 min)
**Setup**: Weekly reporting workflow, ~45 min/analyst, output varies by who runs it.

**Audit finds three frictions:**
1. Each analyst re-pastes the same background
2. Each reformats the output by hand
3. Each catches different things (inconsistent verification)

**Optimization (mapped to the three promotion homes):**
| Friction | Type | Promoted To |
|---|---|---|
| Repeated background | Reference | Shared Project knowledge base |
| Report format | Procedure | A Skill |
| Verification step | Rule | Standing instruction |

**Result**: ~25 minutes per analyst, consistent format across the team, one fewer revision round.

**Takeaway**: Friction was found by instrumenting the workflow; the gain came from promoting the fixes into configuration, sorted correctly using the rule-reference-procedure test.

---

## 5. Adjusting Approach from Feedback and Results

**Every disappointing output is diagnostic data.** Letting that data evaporate — fixing the same problem by hand each time it recurs — is a missed opportunity. Skill = **translate output critique into a specific adjustment**, then **capture the fix so it persists**.

### Build Feedback Loops into Recurring Work
- For repeated work, treat each round's output as a signal about the setup
- A recurring report needing the same manual correction every week is telling you the prompt/context/configuration is missing something
- Habit: after each round, ask what the output revealed about the system that produced it

### Translate Critique into a Specific Adjustment
A vague **"this isn't quite right"** improves nothing. Convert it into the precise change: which component, which context, which configuration setting.

| Reaction (vague) | Instruction (specific) |
|---|---|
| "Too generic" | "Add the audience to the context" |
| "Wrong tone" | "Add a tone constraint to the standing instruction" |
| "Missed the point" | "State the single question the output must answer up front" |

**Skill = turning a reaction into an instruction.**
- A **reaction** names how output *feels* ("too generic," "not quite right," "missed the point")
- An **instruction** names *what to change* so the next output is different
- **Reliable method**: ask "what specifically would have to be present for this to be right, and which part of the setup controls that?"
- Each translation points at a specific lever — the prompt, the context loaded, or a configuration setting
- **If you cannot name the lever, the critique is still a reaction, and the next attempt will be a guess.**

### Capture What Worked
When a fix works, **don't leave it in a one-off conversation** — promote it:
- A reliably-working phrasing → **standing instruction**
- A multi-step fix → **Skill**

### The Expensive Failure Mode: Finding the Fix, Then Losing It
- A correction discovered Monday and not captured will be **rediscovered next Monday** — by you or whoever runs the task
- Each rediscovery costs the same time as the first, **multiplied by every person and every cycle**
- Capturing the fix once (standing instruction or Skill) converts a **repeated cost into a one-time cost**

### Test for Whether a Fix Is Worth Promoting
**Will this same correction be needed again, by me or someone else?**
- If yes → belongs in configuration
- **Note on Memory vs. configuration**: Claude's Memory may pick up repeated patterns, but it's **per-user and best-effort**. Configuration (standing instructions, Skills) is the deliberate, shared, reliable home for a fix.

### Worked Example: A Recurring Fix, Captured vs. Lost
**Scenario**: A marketer notices every campaign-brief draft needs the same two corrections: omits the target segment, buries the call to action.

**Captured (correct approach)**: Adds two standing instructions to the briefs Project:
- "Always state the target segment in the first line"
- "Place the call to action in its own closing section"
→ Next draft arrives correct. **One round of feedback, captured into configuration, removes a recurring weekly correction.**

**Lost (the failure mode)**: Fixes both corrections by hand each week, in the conversation, never captured — same correction rediscovered and repeated every cycle, by anyone running the task.

---

## 6. Quiz Answer Key (with rationale)

| Q | Answer | Rationale |
|---|---|---|
| Q1 | **C** | Under-specification — the prompt never named the contract terms/context that mattered, so the very first answer was already off (no degradation over time) |
| Q2 | **B** | Wrong model tier — switch to a more capable model built for deep analysis rather than a speed-tier model; adding more context won't fix a model-capability mismatch |
| Q3 | **B** | Run the diagnostic sequence: specification → context length → feature/model → configuration, before concluding the task isn't a fit |
| Q4 | **B** | Capture the fix as a standing instruction or Skill in the Project so it persists — converts a repeated cost into a one-time cost |
| Q5 | **B** | Create one shared Skill that formats every section to a single template — directly targets the **variance** friction driving the 90-minute reconciliation cost |

---

## 7. Key Takeaways (Module Summary)

1. **Underperformance has discoverable causes.** Run the diagnostic sequence — specification, context, feature, configuration — before blaming the tool.
2. **Isolate before you fix.** Name whether the failure is the prompt, the context, the feature choice, or an expectation mismatch; the cause points to the fix.
3. **Every disappointing output is data.** Translate critique into a specific adjustment, then capture the fix as an instruction or Skill so it persists.
4. **Optimize deliberately.** Instrument the workflow, find the friction, promote the fix into configuration, and measure the gain.

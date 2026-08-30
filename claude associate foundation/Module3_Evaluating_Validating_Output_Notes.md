# Module 3: Evaluating & Validating Claude's Output — Notes

## Purpose of the Module
The core asymmetry: time saved by Claude is small and visible; the cost of an unverified error is large and arrives later. **This is the largest section of the certification exam** because it centers on **accountability** — when you put your name on a deliverable, you own every claim in it, whether you or Claude wrote the words.

### Cautionary Case
A consultant used Claude to pull supporting statistics for a client deck. 4 of 5 figures were sound; one fabricated (but plausible) growth rate went unquestioned, reached the client, and was caught by the client's own analyst — costing a credibility hit and a week of rebuilding trust. **Lesson**: the cost of a missed error isn't paid when time is saved — it's paid later, by someone whose trust you needed.

### Two Anchoring Competencies (AI Fluency Framework)
- **Discernment**: critically evaluating output against requirements, sources, and standards (the *how* of review)
- **Diligence**: deciding when verification is required and taking responsibility for the result (the *why* it must happen)

**Learning objectives:**
1. Evaluate output for accuracy and completeness
2. Identify hallucinations, inconsistencies, and biases
3. Apply fact-checking and validation techniques
4. Determine when human review is required
5. Edit/adapt/refine/compare output for the intended audience
6. Select appropriate output formats and organize information

---

## 1. Discernment: Evaluating Accuracy, Completeness, and Fitness

Evaluation = a fixed check against **three references**, run the same way every time (not a "feels good" judgment).

### The Three Evaluation References
| Reference | Check |
|---|---|
| **Requirements** | Does output reflect what you actually asked? Confirm *every* part is addressed, not just the easy parts. |
| **Source material** | Where output relies on supplied documents, does it match them? Trace claims back to source — don't assume careful reading. |
| **Professional standards** | Would this pass in your field? (e.g., number without units, recommendation without reasoning, uncheckable citation) — fails even if fluent. |

### Stakes Calibration
- Depth of review is **domain-dependent, not universal**
- **Zero-tolerance work** (legal, financial figures, compliance): accuracy > speed, verify everything
- **Low-stakes internal work** (brainstorming): lighter review appropriate
- **Risk**: applying the same casual review to both — determine stakes *before* deciding review depth

### Three-Way Triage (Verdict Table)
| Verdict | When It Applies |
|---|---|
| **Ready to use** | Meets requirements, matches sources, clears professional standards → ship it |
| **Needs revision** | Close, but a specific gap remains → note gap, iterate |
| **Needs human override** | Stakes/errors/uncertainty mean it shouldn't go out on Claude's draft alone → escalate to a person |

### Completeness Is a Separate Review from Accuracy
- **Accuracy**: is what's present correct?
- **Completeness**: is anything missing?
- They fail **independently** — an output can be fully accurate and still omit the one factor that changes a decision
- Missing elements are **harder to spot** than wrong ones — nothing on screen draws attention to an absence

### Protocol Applied to Three Real Outputs

| Output | Scenario | Reference Check Result | Verdict |
|---|---|---|---|
| 1. Competitor-pricing summary | 3 competitors' pricing from uploaded PDFs | Requirements met; **source check fails** — "$40/user" omits "minimum 10 seats" from source, changing the comparison | **Needs revision** (source-restricted re-prompt, not full rewrite) |
| 2. Internal process recommendation | 3 options to reduce invoice-processing time | Requirements met, no sources to check, standards cleared, low stakes | **Ready to use** (over-verifying wastes saved time) |
| 3. Compliance-gap analysis | Policy vs. regulation, 4 gaps flagged | Regulation wasn't uploaded → worked from training-data recall (possibly outdated); high regulatory stakes | **Needs human override** (useful prompt for an expert, not a substitute) |

**Key insight**: Same protocol, three different verdicts — the differentiator is never how polished the output looks, only what the three references + stakes return.

---

## 2. Hallucinations, Inconsistencies & Bias

**Core rule**: Plausible ≠ verified. Claude's fluent tone doesn't correlate with correctness — you cannot use tone/confidence as an accuracy signal.

### Hallucination Patterns
| Pattern | Description |
|---|---|
| **Plausible-but-unsupported claims** | Sounds reasonable, fits the topic, no basis in source/fact — most dangerous because nothing looks wrong |
| **Fabricated specifics** | Invented stats, dates, names, quotes, citations — specificity reads as authority, making it persuasive |
| **Confident tone masking uncertainty** | Claude rarely hedges proportionally to actual certainty — a guess and a well-grounded fact sound equally assured |

### Inconsistencies and Bias
| Pattern | Description |
|---|---|
| **Internal contradictions** | Early claim conflicts with a later one in long output — hides because you rarely hold the whole document in view at once |
| **Confirmation bias in framing** | If prompt implies a preferred answer, Claude may lean toward it — watch for suspiciously ready agreement on questions that should be open |

### Completeness Failure Pattern
Field example: comparing a batch of documents for differences — output is thorough-looking but **misses differences in the single most important file**. Completeness failures concentrate where attention is lowest; a confident summary of easy files can mask silence on the hard one.

### Capability Hallucination
- Claude can claim to have taken an action it **cannot actually take** (e.g., "I've emailed that to your team," "I've saved the file")
- Within claude.ai, Claude works with the conversation, connected tools, and uploaded files — it does **not** perform external actions without an explicit tool for it
- **Treat any claimed external action as unverified until confirmed**

### Failure-Pattern Gallery (recognize the signature, not just the label)

**Fabricated specific**: Q: "What share of mid-market SaaS firms adopted AI tools in 2025?" → A: "Approximately 63 percent... up from 41 percent in 2024." No source given. **Tell**: precision without citation = number-shaped guess.

**Confident tone masking uncertainty**: Q: "Is this clause enforceable in our state?" → confident 4-sentence "yes," same tone as arithmetic. Enforceability is jurisdiction- and date-specific — should hedge, doesn't. **Assurance is not evidence.**

**Internal contradiction across a long output**: 10-page analysis states market is "$2 billion" on page 2, then builds a projection on "$2.6 billion" on page 8. Both read fine in isolation — only visible with a dedicated **consistency pass**, not a paragraph-by-paragraph read.

**Takeaway**: None of these outputs look broken — that's the point. Catch them by knowing signatures and checking against sources, not by waiting for something to "look wrong."

---

## 3. Fact-Checking and Grounding Techniques

**Best verification is built into the prompt before output exists.**

### Prompt Habits That Prevent Hallucination
| Technique | Purpose |
|---|---|
| **Permit "I don't know"** | Explicitly tell Claude uncertainty is acceptable — without this, a model under pressure to answer is more likely to invent |
| **Restrict to provided sources** | Instruct Claude to answer only from supplied materials, flagging gaps — converts open generation into bounded retrieval |
| **Require auditable citations** | Ask for the specific source/location behind each claim, in checkable form — an untraceable citation isn't a citation |
| **Quote first, then analyze** | For long documents, extract supporting quotes before drawing conclusions — makes both reasoning and errors visible |
| **Best-of-N comparison** | Re-run the same request, compare results — agreement raises confidence; divergence flags soft spots needing human review |
| **Validate against authoritative sources** | For claims that matter, check against a trusted external reference, not another Claude response. (Claude for Excel can produce cell-level citations tying figures to inputs.) |

### Verbatim Prompt Templates

- **Permission to not know**: *"If the answer is not supported by the documents I provided, say so explicitly rather than estimating. It is acceptable to answer 'the provided materials do not cover this.'"*
- **Source restriction**: *"Answer using only the attached contract. Do not use general knowledge. For anything the contract does not address, list it under 'Not covered by this document.'"*
- **Auditable citation**: *"For every claim, cite the section and clause number it comes from, in parentheses, so I can verify it against the source."*
- **Quote-grounding**: *"Before you analyze, extract the exact sentences from the document that bear on my question. Then base your analysis only on those quotes."*

### Verification Checklist (before relying on output)
Did I:
- [ ] Allow uncertainty?
- [ ] Restrict to sources where appropriate?
- [ ] Require auditable citations?
- [ ] Check high-stakes claims against something authoritative?

**Building this into the prompt is cheaper than rebuilding trust in the output afterward.**

---

## 4. Diligence: When Human Review Is Non-Negotiable

Some outputs must **never** ship as a Claude draft alone, regardless of apparent quality. Diligence = knowing these thresholds **in advance** so escalation is policy-driven, not reactive.

### Four Risk Thresholds
| Threshold | Question to Ask |
|---|---|
| **Stakes** | What's the cost if wrong? High-cost errors demand review regardless of confidence shown. |
| **Reversibility** | Can it be undone? Irreversible actions (sent deliverable, filed report) clear a higher bar than a revisable draft. |
| **Audience** | Who sees it? External/executive/regulatory audiences raise the review bar above internal drafts. |
| **Regulatory exposure** | Is this governed by a rule/contract/law? Regulated content carries obligations AI assistance doesn't remove. |

### Do-Not-Ship-Without-Review List (fixed policy)
- Final client deliverables
- Audit-critical or financially material calculations
- Anything involving regulated, confidential, or highly sensitive data
- Public or legal communications where misstatement has lasting consequence

### Iteration vs. Escalation
- Productive iteration improves output each round
- When rounds stop improving it → **diminishing returns** → the right move is **not** another prompt, it's a human expert
- More prompting cannot manufacture judgment the situation requires

### Owning the Output
Accountability does **not** transfer to the tool. Work produced with Claude is your work when shipped — same professional standard as if produced unaided.

### Three Escalation Scenarios
| Scenario | Thresholds Tripped | Action |
|---|---|---|
| **The fast "yes"** — internal meeting agenda | Low stakes, reversible, internal audience, no regulatory exposure | Ship it, no escalation |
| **The deceptive "looks fine"** — board-deck financial summary | High stakes, executive audience, partly irreversible once presented (3 thresholds tripped) | Human reviewer required; recompute figures with Code Execution |
| **The slow creep** — client proposal, 5 iteration rounds, rounds 3–5 changed almost nothing | Diminishing returns + high-stakes external deliverable | Stop prompting, escalate to a colleague for fresh read |

**Signal to escalate = the flat improvement curve, not necessarily a visible error.**

---

## 5. Editing and Adapting Output for Your Audience

Claude drafts; you deliver. The editing pass applies your professional standards + audience knowledge. **Accurate ≠ finished.**

### Three Editing Passes
| Pass | Focus |
|---|---|
| **Clarity** | Cut hedging, tighten sentences, remove anything not earning its place. Claude tends toward thoroughness; editing tends toward precision. |
| **Tone** | Match register to relationship/occasion — same content reads differently to a peer, client, regulator. |
| **Formatting** | Shape for how it'll be read: scannable (executive), detailed (working team), clean (external). |

### Audience Calibration
- One analysis often becomes several deliverables
- **Executive summary**: leads with decision + impact
- **Working-team version**: keeps detail + method
- **External communication**: controls disclosure and framing
- Facts stay constant; **selection, depth, tone change** with reader

### Compare Before You Edit
- Generate more than one draft (different runs/models) and pick the strongest base to edit from
- Cheaper than rescuing a weak draft; surfaces framing you might not have prompted for

### Worked Example: One Analysis, Two Audiences

**Raw output** (excerpt): *"The analysis indicates that processing time increased by approximately 18 percent in Q3, which may be attributable to a combination of higher volume and the onboarding of three new staff members who were still onboarding during the period, and it is recommended that the team consider whether additional process documentation might help mitigate similar effects in future onboarding cycles."*

**Executive version**: *"Q3 processing time rose 18 percent, driven by volume plus onboarding three new hires. Recommend standardized onboarding docs to limit the effect next time."* → leads with number + decision, one sentence.

**Working-team version**: *"Processing time was up ~18% in Q3. Two drivers: higher volume and three new staff still onboarding. Action: draft onboarding documentation so the next cohort ramps faster (owner and timeline to confirm in standup)."* → keeps method, adds operational next step.

**Same facts, same 18%** — executive cut strips method and leads with impact; working cut keeps detail and assigns action. Neither is the raw draft, which serves neither audience well.

---

## 6. Choosing Output Formats: Inline, Artifacts, Structured, Code-Executed

Output format = a **reliability decision**, not just presentation — driven by what the result is for and how much the numbers must be trusted.

### Format by Purpose
| Format | Use For |
|---|---|
| **Inline** | Conversational responses acted on within chat — quick, contextual, not a standalone artifact |
| **Artifacts** | Documents/code — separate, editable block for refinement/reuse; home for deliverables |
| **Structured formats** | Data — tables/defined schemas consumable directly by downstream tools/readers |

### Code Execution as the Verified-Output Path
- When numbers must be right: **have Claude compute them, not write them**
- Prose generation → plausible-looking figure (not reliable)
- Code execution → runs the calculation, returns a computed/checkable result + charts/files
- **Important nuance**: determinism attaches to the *executed computation*, but Claude writes the code — the logic itself can still contain a bug. The guarantee is **traceability and re-runnability**, not automatic correctness.

### Worked Example: Prose vs. Code-Executed (same task)
**Task**: From an uploaded sales spreadsheet, report total Q3 revenue + top 3 accounts.

| Path | Result | Reliability |
|---|---|---|
| **Prose** | "Total Q3 revenue was about $4.7 million, with the largest accounts being Northwind, Contoso, and Globex." | Fluent, fast, **unverifiable** — model's best guess at a sum it can't reliably compute; a wrong total propagates into every downstream slide |
| **Code-executed** | Claude writes/runs code over the actual file → $4,712,380 (computed sum), 3 top accounts ranked by real totals, bar chart | Traceable to the rows that produced it |

**Rule**: Prose isn't "lazy" — fine for low-stakes gut-checks. For anything reported/decided on, reliability requirement points to code execution.

### Curate the Inputs to Shape the Output
Organized inputs → organized outputs. Curating means **removing** wrong material as much as **adding** right material:
1. **De-duplicate** sources — don't make Claude reconcile near-identical copies
2. **Label and structure** what you supply — make each input's role explicit ("this is the approved policy; these are draft responses")
3. **Prune irrelevant material** — noise in input becomes noise in output

**Selection rule**: pick output modality by the reliability the task requires.

---

## 7. Exercise: Triage the Output Set (Reference)

| Output | Scenario | Likely Verdict | Reasoning |
|---|---|---|---|
| **A** | Internal campaign brainstorm, clearly labeled as starting point, few generic entries mixed with strong ones | **Ready to use** | Low stakes, clearly labeled as a draft/starting point — fit for purpose as-is |
| **B** | Financial summary where stated total doesn't match sum of line items | **Needs revision** | Internal arithmetic inconsistency — fixable, verify/recompute the total |
| **C** | Market overview citing 3 statistics, none sourced | **Needs revision** (or override, depending on stakes) | Fabricated-specific risk — require citations or verify against authoritative sources before use |
| **D** | Draft regulatory filing summary, accurate on spot-checks, intended for submission to a regulator | **Needs human override** | High stakes + regulatory exposure + external audience — spot-checks are not sufficient for submission-grade regulatory content |

---

## 8. Quiz Answer Key (with rationale)

| Q | Answer | Rationale |
|---|---|---|
| Q1 | **B** | A separate completeness check against requirements — accuracy and completeness fail independently |
| Q2 | **B** | A fabricated specific — precise, uncited detail reads as authoritative but isn't verified |
| Q3 | **B** | Restrict to the provided document, permit "I don't know," require clause-level citations |
| Q4 | **B** | Recompute with Code Execution, confirm the subtotal reconciles, resolve before use |
| Q5 | **B** | Produce distinct executive (decision/impact) and working-team (detail/method) versions |
| Q6 | **C** | Use Code Execution — calculation is run and the result is verified, not just plausible |
| Q7 | **B** | De-duplicate/prune to the single approved source, label remaining inputs, re-run |

---

## 9. Key Takeaways (Module Summary)

1. **Accountability stays with you.** You own every claim in what you ship, whether you or Claude wrote it — hence the exam's largest section.
2. **Evaluate against three references.** Requirements, source material, professional standards — same check every time, calibrated to what's at stake.
3. **Plausible is not verified.** Fabricated specifics, confident uncertainty, and completeness gaps all read as competent — learn the signs to spot them fast.
4. **Build verification into the prompt.** Permit "I don't know," restrict to sources, require auditable citations — prevention beats reconstruction.
5. **Know the thresholds in advance.** Stakes, reversibility, audience, regulatory exposure decide when human review is mandatory — set the line before the moment, not after.
6. **Pick the format by reliability.** When numbers must be right, compute them with Code Execution rather than generating them as prose.

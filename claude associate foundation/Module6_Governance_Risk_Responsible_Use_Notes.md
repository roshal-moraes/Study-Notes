# Module 6: Governance, Risk & Responsible Use — Notes

## Purpose of the Module
**One inappropriate use case can freeze an entire organization's AI program.** Sensitive data in the wrong place, an untrusted Skill with broad access, a quiet policy violation — any can trigger a freeze that costs every team their productivity gains.

**Core framing**: Governance is a **practitioner skill**, exercised one decision at a time — not a policy binder on a shelf. The policy sets the boundary; the practitioner decides, in the moment, whether this use case/Skill/upload is appropriate.

### Two Anchoring Competencies (AI Fluency Framework)
- **Diligence**: ownership and verification
- **Delegation**: supplies the criteria for judging whether a use case is appropriate at all

**Learning objectives:**
1. Identify appropriate and inappropriate use cases
2. Apply data sensitivity, privacy, and regulatory considerations
3. Follow organizational AI policies and governance standards
4. Understand the ethical implications of AI usage

**Core deal**: Classify use cases against Delegation criteria, vet Skills like software, handle data by sensitivity with the right feature controls, apply policy as a sustained habit, and evaluate output for bias/fairness in routine review.

---

## 1. Appropriate vs. Inappropriate Use Cases

Deciding appropriateness = **structured evaluation**, not gut feeling. Same Delegation criteria that map workflow steps (Module 4) also **screen whole use cases**.

### Delegation Criteria for Screening
| Criterion | Question to Ask |
|---|---|
| **Reversibility** | Can a wrong output be caught and undone before it causes harm? Irreversible consequences raise the bar sharply. |
| **Consequence of error** | What's the cost if wrong? Higher consequence demands more human control or rules the use case out. |
| **Need for human creativity/empathy** | Does the task require judgment, relationship, or care AI can't supply? Some work stays human regardless of capability. |
| **Accountability** | Who is answerable for the outcome, and can that accountability be exercised over an AI-produced result? |

### The Criteria Interact — Name the Load-Bearing One
- Not a checklist where any single failure ends the discussion — criteria **interact**
- A task can be low-consequence + reversible and still require human review (e.g., a condolence note — relationship element means a person should own it, despite low stakes)
- A task can be high-consequence but still appropriate **with the right gate** (e.g., a financial summary is consequential, but a named reviewer sign-off restores accountability/reversibility)
- **Practical rule**: run all four criteria, then ask which one is **load-bearing** for this specific use case — the one that, if changed, would move the classification
- Naming it is what makes the classification **defensible to a risk/compliance reviewer**

### Three Classifications
| Classification | Definition |
|---|---|
| **Fully appropriate** | Reversible, low consequence, no special human element → delegate with normal review |
| **Appropriate with human review** | Useful for AI assistance, but stakes/accountability require a human gate → **define the gate explicitly** |
| **Inappropriate** | Consequence, irreversibility, or human-element requirement means AI should not perform this → articulate why + name the human role that must own it |

### The Gate Is the Classification (Most Commonly Mishandled)
"Appropriate with human review" is most often gotten wrong because people **stop at the label** and never specify the gate.

A defined gate establishes three things:
1. **Who reviews** — the role with accountability, not whoever is free
2. **What they verify** — the specific risk the review exists to catch (accuracy, fairness, policy compliance)
3. **When** — before the output is used, not after

- ✅ Defined gate: *"A manager reviews the shortlist for adverse-impact patterns before any candidate is contacted."*
- ❌ Not a gate: *"We will keep a human in the loop."*
- **If you cannot state the gate in who/what/when form, the use case is not yet ready to run.**

### Worked Example: A Use-Case Portfolio
| Use Case | Call | Reasoning |
|---|---|---|
| Draft internal FAQ from approved policy docs | **Fully appropriate** | Reversible, low stakes, factual grounding available |
| Summarize candidate résumés to a shortlist | **Appropriate with human review** (+ bias safeguards) | Consequential, fairness-sensitive; accountability rests with hiring manager who owns the selection |
| Generate a final medical or legal determination | **Inappropriate** | Irreversible consequence + professional accountability that cannot transfer to a tool |
| Draft customer-facing responses to billing complaints | **Appropriate with human review** (the hard, line-sitting case) | Reversible if reviewed before sending; moderately consequential (wrong charge info erodes trust, possible regulatory weight); partly relationship-sensitive. **Load-bearing criterion: accountability** — company owns what it tells a customer about their money. **Defined gate**: support agent verifies each drafted response against the actual account record and adjusts tone before sending |

**Key insight**: The interesting governance work isn't the obvious yes/no cases — it's **naming the gate** for the ones in the middle. "It feels risky" doesn't travel as a rationale; "irreversible consequence plus non-transferable accountability" does.

---

## 2. Data Sensitivity, Privacy & Feature Controls

**Know data sensitivity before it enters any feature.** Data classification prevents leakage; feature controls (Incognito, Memory management, sandbox awareness) are how you act on that classification.

### Classify Before You Upload — Three Tiers
| Tier | Description |
|---|---|
| 🟢 **Green: Safe to use** | Published material, anonymized/aggregated data, internal docs already cleared for wide sharing |
| 🟡 **Yellow: Review first** | Internal docs not meant to leave the company, anything with names/contact details, draft material tied to an unannounced deal/product |
| 🔴 **Red: Keep out unless an approved path exists** | Regulated data (health, financial, government), credentials/secrets, anything under third-party confidentiality obligation |

**Rule when unsure between two tiers**: treat data as the **more sensitive** tier until confirmed otherwise.

### Redaction and Anonymization
- Remove sensitive specifics when the task's substance doesn't require them
- Redacting names/account numbers/identifiers lets you get analytical value without exposure
- Works when the task doesn't depend on the identifiers (e.g., trend analysis across a customer list doesn't need names — "Customer 1, Customer 2" loses nothing needed)

**Two redaction failure modes:**
| Failure Mode | Description |
|---|---|
| **Partial redaction** | Removing the name but leaving an account number, rare job title, or specific date can still identify someone (especially in a small population) — strip *every* identifying field |
| **Redaction that breaks the task** | If the work genuinely needs the sensitive specifics, redaction isn't the answer — confirm an approved path or keep the data out entirely. Redaction only works where sensitivity and necessity **don't overlap** |

### Feature-Specific Controls
| Control | What It Does |
|---|---|
| **Code execution sandbox** | Code runs in a sandboxed environment; uploaded files are processed there. Review what you upload before running analysis. |
| **Memory persistence** | Memory carries information across sessions — for sensitive work, that persistence may be exactly what you don't want |
| **Incognito mode** | Keeps the session out of chat history and Memory. **Still follows org data-retention policies and can appear in organizational data exports** — Memory exclusion and data retention are separate controls. Use for sensitive conversations/confidential inputs that shouldn't surface in history or Memory |
| **Org-level Memory controls** | Team plans have **no** org-level Memory controls; on **Enterprise**, Owners/Primary Owners hold org-wide Memory controls (including disabling Memory org-wide). Verify current behavior at the Claude Help Center memory article (support.claude.com) |

### Matching Control to Classification
- **Green data**: needs no special control
- **Yellow data** that shouldn't persist across sessions: case for **Incognito** — confidential but not regulated, analysis needed now without entering Memory/chat history (org retention policy still applies underneath)
- **Red data**: needs an **approved entry point confirmed before upload**, regardless of Memory settings

### ⚠️ Critical Warning: Incognito Does NOT Make Red Data Safe
- Incognito controls **whether something gets remembered**; it does **not** confirm whether the data was **allowed here in the first place**
- For regulated data, **"is this allowed here" comes first**, before "how do I handle it here"
- **Common error**: using Incognito and assuming it makes sensitive data safe

### Awareness Across Entry Points
Different claude.ai entry points may handle data retention differently depending on org configuration. Habit to build: **ask before uploading when in doubt about a given entry point.** Plain rule: know sensitivity first, then match feature/controls to it.

### Worked Example: Four Data Decisions
| Data | Tier | Action |
|---|---|---|
| Anonymized survey data for trend analysis | **Green** | Safe to upload; use Code Execution for verified counts — no personal identifiers |
| Confidential M&A document for summarization | **Yellow** | Review against policy first; use **Incognito** so it doesn't enter Memory/chat history (still subject to org data retention). If policy prohibits the entry point, keep it out |
| Spreadsheet of customer PII for cleanup | **Yellow/Red boundary** | Redact/anonymize identifiers before upload, or keep out entirely and confirm approved path with admin |
| Patient records for a healthcare workflow summary | **Red** | Incognito does **not** solve this — regulated data. First question: is this entry point approved for PHI at all? If no confirmed approved/compliant path exists, **stop and escalate to admin** |

---

## 3. Skill Trust and Feature-Level Risk

**A Skill is software.** It can access whatever Claude has access to during a session, and can take actions through Code Execution. An untrusted-source Skill = real risk.

### Why Untrusted Skills Are Risky
- A Skill runs procedures and touches data/tools available in the session
- One from an unknown source could mishandle data or take unintended actions
- Answer to "Skills are a black box" worry: **not blind trust or blanket bans** — a **repeatable trust evaluation**

### Trust Evaluation Before Enabling
| Trust Check | Question |
|---|---|
| **Source** | Who published this Skill? Anthropic-provided/internally-approved = lower-risk starting point; unknown third-party = more scrutiny |
| **Reach** | Skills don't request permissions — a Skill **inherits whatever access the session already has**. Ask: what could this Skill reach in the sessions where it runs, and is that exposure proportional to the task? Audit the bundle's contents (instructions, dependencies, bundled files) before enabling — a formatting Skill whose instructions roam beyond formatting is a red flag |
| **Appropriateness** | Is this Skill the right tool for the task, or more capability than the job needs? |

### Internal ≠ Vetted
- The hardest case: a Skill built by **another team inside your own organization**
- "Internal" feels safe but isn't automatically vetted — the building team may have given it broad permissions for their own convenience, or built it against an older policy
- Treat an internal Skill from outside your team like software from a sister department: confirm with the publisher what it accesses and why, check permissions match current policy, **before enabling on your own data**
- **The trust question**: "Do I know what it does, and does its access match the job?"

### Three Outcomes from a Trust Check
| Outcome | When |
|---|---|
| **Enable** | Source, permissions, and appropriateness all clear |
| **Escalate** | Useful but source unknown or permissions look broad → route to admin/security for review |
| **Decline** | Permissions clearly disproportionate or source cannot be established, and no review would change that |

Failing the trust check doesn't always mean "never use it" — it means **don't enable it on your authority alone.**

### Worked Example: Vetting Two Skills
| Skill | Assessment | Call |
|---|---|---|
| Anthropic-provided document formatter | Trusted source, permissions match task, appropriate for need | **Enable** |
| Third-party "analytics booster," unknown publisher, broad data access request | Unknown source, disproportionate permissions for stated purpose | **Do not enable** without org review — treat like unvetted installed software |

### Generalizing the Principle: Least Privilege
Same proportionality habit applies to any capability that reads/acts on your data (connector, tool, integration):
- Who provides it?
- What does it access?
- Is that access proportional to what's needed?

**Principle**: grant the **narrowest access** that lets the job get done, and revisit when the job changes.

---

## 4. Organizational Policies and Diligence as a Habit

**A policy followed only when someone is watching is not governance.** The gap between what policy says and what people do is exactly where risk lives.

### Apply Governance Consistently
- Governance compliance is a **sustained habit**, not a one-time acknowledgment
- Standard: apply the framework on **routine, low-visibility decisions**, not just obvious high-stakes ones — routine decisions are where drift accumulates unnoticed

### Audit Usage Against Policy
- Periodically compare team's actual/planned Claude use against what policy requires
- Divergences = **Diligence gaps** to close: a data type being uploaded that shouldn't be, a skipped review step, an unvetted enabled Skill

### Stay Current
Policies and capabilities both evolve — compliant last quarter ≠ compliant after a policy update or new feature. Staying current with both keeps judgment paced with tools and rules.

### Worked Example: A Mini Usage Audit
**Finding**: Team lead reviews a month of team Claude use, finds three gaps:
1. Marketer uploaded an unreleased product spec to a non-approved entry point
2. A Skill was enabled without a source check
3. A recurring client report skipped its required human-review gate twice under deadline pressure

**None malicious — all drift.** Fixes are habit-level:
- Reminder on approved entry points
- Skill-vetting step added to Project setup
- Non-negotiable review gate on client deliverables

**The audit converted invisible risk into three closeable actions.**

---

## 5. Ethical Implications: Bias, Fairness, Transparency

**Ethical risk doesn't announce itself — it hides in ordinary outputs.** A summary quietly favoring one group, a recommendation built on biased framing, an AI-assisted document presented as fully human-authored — these are routine outputs with ethical weight. Evaluating for bias/fairness/transparency belongs in **routine review**, not a separate ethics exercise.

### Recognize Bias and Fairness Risk
- AI-assisted work can carry bias from the prompt, framing, or underlying language-generation patterns
- In people-facing work (hiring, evaluation, communications to specific groups): check whether output treats people fairly and whether framing has tilted the result
- **Risk is highest where the stakes for individuals are highest**

### Transparency and Disclosure
- Know when to disclose AI assistance — some contexts/policies require it, others treat it as routine tooling
- Obligation depends on setting and audience
- **Responsible default when unsure: disclose rather than conceal**

### Reasoning Through Ambiguous Cases
Many ethical questions have no rule that settles them cleanly. Structured approach:
1. Name who is affected
2. What could go wrong
3. What the fair outcome looks like
4. What disclosure the situation calls for

**Reasoning it through and documenting the reasoning is the professional standard** when no policy gives a direct answer.

### When Reasoning Alone Is Not Enough
Escalate rather than decide alone if:
- The affected population is large
- Potential harm is significant
- The ethical question touches areas your team doesn't have standing to resolve

Your org's AI governance/ethics function exists for exactly these cases. **Escalating with documented reasoning is more useful than bringing a verdict** — it shows you applied the framework, identified where it ran out, and flagged the gap for the right reviewer.

### Worked Example: Performance-Review Summaries
**Scenario**: A manager uses Claude to draft performance-review summaries from their own notes. Appropriate?

**Reasoning**:
- **Affected**: the employees
- **Risk**: generated phrasing could introduce unfair or inconsistent tone across reviews
- **Fair outcome**: manager must verify each summary reflects actual notes and applies a consistent standard
- **Disclosure**: setting may call for disclosing AI assisted the drafting

**Conclusion**: **Appropriate with human review and a fairness check** — not a blanket yes/no. **The reasoning, not the verdict alone, makes the decision defensible.**

---

## 6. Quiz Answer Key (with rationale)

| Q | Answer | Rationale |
|---|---|---|
| Q1 | **C** | Inappropriate — irreversible consequence and non-transferable accountability require a human to own the benefits-eligibility determination |
| Q2 | **B** | Evaluate source and reach; publisher unknown and Skill inherits full session access with unrestricted bundled instructions → do not enable without organizational review |
| Q3 | **B** | Incognito mode, **after confirming the entry point is approved for this data** — Incognito alone doesn't establish the data was allowed there |
| Q4 | **B** | A Diligence gap between policy and practice — friction in the approved path is driving the workaround; fix is to remove friction and make the compliant route the easy default |
| Q5 | **B** | Bias — an automated screen filters people out with no human review of the exclusions, so systematic disadvantage to some groups can go undetected |

---

## 7. Key Takeaways (Module Summary)

1. **Governance is a practitioner skill.** Responsible use is exercised one decision at a time, by you, not by the policy binder.
2. **Screen use cases with the Delegation criteria.** Reversibility, consequence, human element, and accountability classify a use case as appropriate, appropriate-with-review, or inappropriate.
3. **A Skill is software.** Evaluate source and permissions before enabling, the way you would any installed software.
4. **Know data sensitivity before it enters a feature.** Classify first, then use Incognito, Memory controls, and redaction to match the handling to the sensitivity.
5. **Ethical risk hides in ordinary outputs.** Evaluate for bias, fairness, and disclosure as part of routine review, and reason ambiguous cases through.

# Module 5: Configuration & Knowledge Management — Notes

## Purpose of the Module
The line between **using** Claude (typing a good prompt today) and **operating** Claude (building an environment where context, instructions, and procedures are already in place, so every conversation starts from a configured baseline). **Configuration is leverage** — set up once, benefit on every subsequent conversation.

- Configuration turns **individual skill into team capability**: curated Project/instructions/knowledge → two people asking the same question get the same quality answer. Uncurated → everyone re-invents context daily and answers drift.
- **Second half of the discipline: maintenance.** Configurations age — stale instructions, superseded knowledge, drifted Skills quietly degrade output. A well-configured environment must be built deliberately **and** reviewed on a cadence.

**Learning objectives:**
1. Configure Claude Projects with instructions and knowledge sources
2. Manage uploaded knowledge and connectors (Google Drive, Gmail, etc.)
3. Create effective system-level (standing) instructions
4. Inform, maintain, and update configurations, knowledge sources, and instructions

**Core deal**: Set up once, benefit every conversation, then maintain it so it stays true.

---

## 1. Configuring Claude Projects — The Four Configuration Mechanisms

A Project has several configuration "slots." The skill is putting each piece of a recurring need into the **right** slot.

| Mechanism | What It Holds | Key Notes |
|---|---|---|
| **Standing instructions** | How Claude should **behave** across every conversation in the Project: tone, format defaults, verification habits | Behavior, not facts |
| **Knowledge base** | Documents, policies, reference files Claude draws on without re-uploading | Facts and reference, not behavior |
| **Skills** | Repeatable **procedures** Claude follows consistently for a task type | Procedure, not one-off instruction. Live at the **account level** under Customize, not inside any one Project — a Skill built once is reusable across any Project that needs it |
| **Scoped Memory** | Continuity **within** the Project | Kept separate from other Projects so context doesn't bleed between workstreams |

### Choosing the Right Mechanism
The recurring question: **instruction, knowledge, or Skill?**
- A rule about behavior ("always cite sources") → **instruction**
- A fact Claude needs ("our brand palette is these hex codes") → **knowledge**
- A multi-step procedure ("format findings into our standard report template") → **Skill** (built once at account level, reused across Projects)

**Most common configuration mistake**: putting a procedure into instructions, or a behavior rule into the knowledge base — makes the Project harder to maintain.

### Worked Example: Client Account Workspace (One Project per Client)
For Client A:
- **Standing instructions**: "Write in a formal register. Always cite the source document for any factual claim. Flag anything you are unsure of rather than guessing."
- **Knowledge base**: client's brand guide, current statement of work, last three status reports
- **Skill** (account-level, reused): firm's status-report formatter — available to any Project, ensures consistent structure
- **Scoped Memory**: client's stakeholder names and standing preferences — never appears in Client B's Project

- On Team/Enterprise: Project can be **shared with the engagement team** under permissions → everyone works from the same configured baseline
- **Project-scoped Memory** is what guarantees Client A's context never appears in a Client B conversation — the separation that makes one-Project-per-client safe

### Scoped Memory as a First-Class Mechanism
- Not an afterthought — a configuration slot like any other
- Instructions = how to behave; knowledge base = what is true; Skills = reusable procedures (account level); **Scoped Memory = what the Project has already settled** (stakeholder names, standing preferences, prior decisions)
- **The word that matters: "scoped."** A Project's Memory is sectioned off from other Projects — isolation makes Memory safe for sensitive/client-specific continuity
- **Deciding Memory vs. knowledge**: stable reference fact → knowledge; evolving record of Project decisions → Memory

### When a Need Spans Two Mechanisms (The Pairing Rule)
Cleanest configurations rarely map a need to a single slot — the most common pattern is a **behavior rule + the facts it acts on**:
- *"Always cite the source document for factual claims"* = standing instruction, but the documents it cites live in the **knowledge base**
- Neither works alone: instruction has nothing to cite without documents; documents get used inconsistently without the instruction
- Skills pair the same way: a status-report Skill carries the procedure, while the brand guide it formats against sits in knowledge
- **When configuring**: ask which slot a need belongs in, **and** whether it needs two slots wired together

---

## 2. Connectors and Uploaded Knowledge

Connectors extend Claude's reach into data you already work in (Google Drive, Gmail, etc.) — powerful, but **bounded**.

### Connecting External Sources
- A connector lets Claude reach an external system you authorize (e.g., search Drive, find a relevant email)
- **You manage what's accessible** — keep the set deliberate, don't connect everything by default

### Capability Boundaries
- Each connector has a **defined boundary** — knowing it prevents wasted time
- Example: a mail connector may let Claude **search and read** messages but **not send** them
- Expecting an action a connector can't perform produces a **confusing failure, not a clear error** — learn each connector's boundaries before building a workflow on it

### Two Field-Observed Pitfalls
| Pitfall | Description |
|---|---|
| **Wrong path to add a connector** | The obvious add-connector path can route to a **public directory** rather than your organization's vetted connectors. Confirm the right path with your admin (especially Team/Enterprise) to connect the approved source, not a look-alike |
| **Boundary confusion misroutes fixes** | When a connector hits its capability boundary, the failure can *look like a bug* (field-observed pattern, not documented product behavior) — reports go to the wrong team, fix stalls. Knowing where the boundary is prevents mislabeling the issue |

### Keeping Uploaded Knowledge Current
- Uploaded knowledge needs the same care as a connected source: **current, relevant, duplicate-free**
- A knowledge base with three versions of the same policy invites Claude to cite the wrong one
- **Curate it like a shared drive** — remove deprecated versions as you add new ones

---

## 3. System-Level Instructions That Stick

Persistent (standing) instructions = write the verification behaviors, format defaults, and tone **once**; every conversation in the Project inherits them.

### Write the Guardrails Once
Highest-value instructions = the ones you'd otherwise re-type constantly.

**Example**: *"Cite the source document for every factual claim, and say 'I don't know' rather than guessing when the documents do not cover something."*
→ Now applies to every conversation automatically, without anyone remembering to ask.

### Anticipate the Use Cases
- Good standing instructions embed format, tone, and guardrail guidance **ahead of need**
- E.g., for client-deliverable Projects, specify preferred format/register up front so the first draft lands closer to final, rather than needing the same corrections repeatedly

### Precision, or It Silently Fails
- Vague instructions **don't announce failure** — they just quietly don't work
- *"Be professional"* → gives Claude almost nothing to act on
- *"Use a formal register, define any acronym on first use, and keep paragraphs under four sentences"* → precise enough to actually change output

**The test**: would two different people read this instruction the same way?

### Worked Example: Vague vs. Precise
| Vague | Precise |
|---|---|
| "Make the reports good and accurate." → output quality varies conversation to conversation; nothing concrete changed | (Precise equivalent specifies exact structural/behavioral rules — see meeting-notes example below) |

---

## 4. Maintaining Configurations

Configurations are **living assets**. Instructions, knowledge, Skills, and Memory all drift toward stale — and stale configuration degrades output **quietly, with no error to alert you**.

### Review Cadence
Set a recurring review per active Project:
- Do standing instructions still match the current process?
- Is the knowledge base free of superseded documents?
- Are the right Skills enabled?

**A monthly pass for active Projects catches most drift.** Signal you waited too long: output quality slipping for no visible reason.

### Skills Versioning
- **Anthropic-built and organization-provisioned Skills**: update automatically
- **Custom-uploaded Skills**: change only when you re-upload them
- Watch for a misconfigured/out-of-date Skill degrading output — a Skill silently producing a slightly-off format every run is a **maintenance problem, not a prompting problem**

### Memory Lifecycle
- Treat Memory like a working file: review periodically, edit/delete stale entries, **export as backup before a major change**
- When a Project's Memory has accumulated enough outdated context to mislead → **a full reset is the right call**
- Accuracy of what's stored matters more than volume

### Configuration Is Also Additive
When Claude hasn't automatically captured a fact that matters (a constraint, preference, background piece), **add it explicitly** to standing instructions or Memory rather than re-supplying it each session.

### Worked Example: Auditing a Degraded Setup
**Symptom**: A recurring report Project starts producing subtly wrong output.

**Maintenance checklist finds:**
- A standing instruction still references a metric the team renamed last quarter
- The knowledge base holds two versions of the template
- A Memory entry records a stakeholder who has left

**Fix**: Maintenance, not a new prompt — update the instruction, remove the old template, delete the stale Memory entry. Output returns to standard **without changing how anyone prompts.**

---

## 5. Quiz Answer Key (with rationale)

| Q | Answer | Rationale |
|---|---|---|
| Q1 | **B** | A Skill carries a repeatable, multi-step formatting procedure — the correct mechanism for "procedure," not prose instruction |
| Q2 | **B** | Separate Project per company, each with its own scoped Memory — the isolation prevents confidential assumptions from crossing over |
| Q3 | **B** | Each connector has a defined capability boundary; sending is simply outside this connector's scope — not a bug |
| Q4 | **C** | "List each decision as its own bullet; record every action item as owner plus due date; flag any unassigned action as 'owner TBD'" — precise, testable, actually changes output |
| Q5 | **B** | Both the standing instruction and the Memory entry still pin the report to FY25 (template + targets), while the knowledge base already holds FY26 versions — update the instruction and Memory to FY26 |

---

## 6. Key Takeaways (Module Summary)

1. **Configuration is leverage.** Set up once, benefit on every conversation. A configured environment separates operating Claude from merely using it.
2. **Match each need to the right mechanism.** Instructions for behavior, knowledge for facts, Skills for procedures, scoped Memory for continuity.
3. **Know each connector's boundary.** Connectors extend Claude's reach, but each has a capability edge; knowing it prevents frustration and misrouted fixes.
4. **Write instructions precisely.** Vague standing instructions silently fail; precise, testable ones change output every conversation.
5. **Maintain or watch quality decay.** Configurations age. Schedule reviews of instructions, knowledge, Skills versions, and Memory, or output degrades with no warning.

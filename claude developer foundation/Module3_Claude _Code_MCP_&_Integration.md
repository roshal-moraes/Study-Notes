# Study Notes, Part 3: Claude Code, MCP & Integration (Developer Module 3)

*(Continues from Parts 1-2, which covered Module 2: prompting, thinking, tools, streaming, context, agents, memory, multimodal, batch.)*

---

## 0. Module overview

**Core idea:** Claude Code is your terminal-native development partner. It runs the same model and agent loop from Module 2 but adds a **permission layer, a configuration system, and team-sharing features**. **MCP** connects it to external services.

**The recurring "build":** code that works on your machine/session/staging must hold up when someone else runs it, in production, against real company systems. Each local convenience becomes a failure point:

| Local convenience | Production failure |
|---|---|
| Permission mode felt safe | A mode deletes a file never in scope |
| Project rules were small | One rule gets buried under hundreds of lines |
| Skill found its script | Skill points at a path that exists on no other machine |
| Credential sat in the config file | Committed key leaks within hours |
| Staging connection worked | Staging-only config step takes down the production connection |

**Learning objectives (6):**
1. Run the **explore → plan → code** loop; pick a permission mode matching the risk of the work.
2. Read AI-generated code, review output with calibrated trust, act on reliable findings, verify unreliable ones, put a human gate where a wrong call is costly.
3. Give Claude Code durable context via CLAUDE.md, rules files, hooks, subagents.
4. Package a workflow as skills, custom commands, and a plugin; author a skill once that runs across Claude Code, Messages API, Agent SDK.
5. Build an MCP server (tools, resources, prompts); pick the right transport; set configuration scope.
6. Connect Claude to enterprise systems, authenticate in ways a regulated customer accepts, and scope a code-modernization engagement to survive security review.

**Audience:** developers comfortable with Module 2 API patterns. It does not re-teach the agent loop, tool schemas, or context engineering.

**Disclaimer:** same as before: educational, not professional advice; products change; examples illustrative; Anthropic terms control.

*Note: objective 2 (AI code review, calibrated trust) has no dedicated section in the pasted text. It appears only in takeaway #2 (see section 12).*

---

## 1. Agent loop, permission modes, settings, and human gates

### 1.1 Explore → plan → code
1. **Explore:** reads files, traces logic, builds a picture of the codebase.
2. **Plan:** a structured description of the edits it intends to make.
3. **Code:** only **after you review and approve the plan** does it write and execute changes.

**Why it matters:** (a) better output: fewer assumptions, catches downstream effects; (b) it's where permission modes plug in. **Plan mode** holds Claude Code in the explore phase, **blocking all file edits and shell commands** until released. A useful default for unfamiliar or high-stakes work.

### 1.2 Permission modes
They control how often Claude Code stops to ask. Each trades speed vs. oversight; choose by how well you know the codebase and how reversible the changes are.

| Mode | Auto-approves | Still gates | Limitations / use |
|---|---|---|---|
| **default** | Reads only | All file edits and shell commands | Safe but slow on trusted work. Baseline for new/unfamiliar projects |
| **acceptEdits** | Reads, file edits, and common filesystem commands (`mkdir, touch, rm, rmdir, mv, cp, sed`) **inside the working directory**. Protected paths still prompt | All other shell commands; writes outside working directory; writes to protected paths | Trusted local work where shell execution needs a human eye. **Not appropriate if the agent must run scripts** |
| **plan** | Reads only; researches and proposes; no edits | All edits and shell commands until you approve a plan | Exploration/planning on sensitive or unfamiliar code. Not for tasks that must write output |
| **auto** | Everything, but a **separate classifier reviews each action first** and blocks anything that escalates beyond your request, targets unrecognized infrastructure, or seems driven by hostile/inappropriate content | **Blocked by default:** production deploys and migrations, mass deletes, credential exfiltration, force-push to main | Reduces prompts but **does not guarantee safety**; **research preview**; availability depends on plan, model version, admin settings. Verify requirements |
| **dontAsk** | Only tools pre-approved in an **allow rule**, plus read-only commands. **Auto-DENIES everything else** | Every tool call not on the allow list is denied; **no confirmation queue** | Built for **locked-down CI and scripts**. Not a way to reduce friction on local interactive work |
| **bypassPermissions** | All tool calls; **no prompts and no safety checks** | Nothing in normal operation; only catastrophic deletes (`rm -rf /`, `rm -rf ~`) still trigger a last-resort prompt | **Only in an isolated container/VM with a disposable environment.** Never on a developer workstation against a live codebase |

### 1.3 Where configuration lives (scope hierarchy)
| Level | Location | Applies to | Use for |
|---|---|---|---|
| **User** | `~/.claude/settings.json` | Every project on the machine | Personal preferences (e.g., default exploration mode) |
| **Project** | `.claude/settings.json` (committed) | Everyone who clones the repo | Team conventions, allow rules for project tools, deny rules for protected paths |
| **Local project** | `.claude/settings.local.json` (auto git-ignored) | You, one project | Personal overrides not to be committed |
| **Enterprise** | `managed-settings.json` (admin-set) | Whole org | **Cannot be overridden by users or project files.** Org-wide security controls (e.g., deny edits to env files, block shell commands) |

**Rules layered on modes:** allow and deny rules sit on top of the selected mode. **A deny rule always wins over an allow rule**, regardless of mode. The most durable governance control is an **enterprise-level deny rule**: no developer can remove it and it applies **even in bypass mode**.

### 1.4 Where a human gate goes: the worst-case-cost question
**Question:** *What is the worst outcome if this action runs without a person checking it?* Same question that separates safe from risky modes; applies equally to interactive coding and unattended automation (e.g., a bot that comments on or blocks a PR).

Three placements:
1. **Let low-stakes, reversible actions through** (formatting fix, edit in working directory). Approval buys oversight you don't need. This is what `acceptEdits` is for.
2. **Gate anything hard to undo or reaching a sensitive path:** write outside working directory, destructive shell command, edit to a security-relevant/protected file. Enforce deterministically with a **deny rule**; `default` or `plan` keeps the prompt while you decide.
3. **Never let the agent be the only gate on code your team marked sensitive.** Agent output is an *input* to a human decision. A person must review before merge no matter how confident the agent or its self-review sounds.

**Mode choice and gate placement are the same decision from two sides:** the mode sets the session default; the gate overrides it for the one action too costly to leave to the default.

**Cost / Complexity / Risk:**
- *Cost:* default mode adds prompt latency per tool call; accumulates on long refactors.
- *Complexity:* multiple settings levels with an override hierarchy; an enterprise deny contradicting a project allow must be understood by maintainers.
- *Risk:* wrong mode for context. Bypass set out of impatience on a non-isolated machine removes every safety prompt **and the protected-path guard**.

### 1.5 Postmortem: bypass mode removed the one prompt that mattered
- Dev switched to `bypassPermissions` for "just renaming old API endpoint references" after three incident-free days.
- Claude scanned `/v1/legacy/` pattern (47 files), updated references, ran a **post-rename `cleanup.sh`**, which **deleted 3 files in `/deploy/config/prod/`** (production endpoint overrides). Dev meant to work only in `/src/`.
- **Cause:** the pattern matched files in both `/src/` and `/deploy/config/prod/`. No confirmation in bypass mode.
- **Precise gate location:** in `default` or `acceptEdits`, the **script invocation** would have prompted. But note: `acceptEdits` auto-approves `rm` on paths inside the working directory, so **direct `rm` commands would have gone through silently**; only `default` prompts for those.
- **Lessons:** bypass silences *all* prompts including unanticipated ones; also skips the **protected-path guard** (repo state and Claude's own config lose their automatic prompt). **Set deny rules on sensitive directories before switching modes.** For fewer prompts with a safety net, use a classifier-gated mode (**auto**), not full bypass.

### Checkpoint 1 (as presented)
Scenario: trusted local refactor of the payments module; auto-approve edits; **never run destructive shell commands**; **`.env.production` never readable**.

- **Part 1:** select two pieces. The source marks all five as selectable (checkmarks shown on every option) and gives **no answer key**, so I derive the answer from the module's rules:
  - **Piece C** (`allow: Bash(npm run:*)`, `deny: Bash(rm:*), Bash(git push:*)`) → blocks destructive shell commands. ✔
  - **Piece D** (`deny: Read(.env.production)`) → blocks reading the env file. ✔
  - A (`default`) doesn't auto-approve edits; B (`bypassPermissions`) removes safety; E (`allow Bash(*)`, `Edit(*)`) is overbroad.
  - *(Auto-approving edits would come from `acceptEdits`, which isn't offered as a piece, so C + D are the two that satisfy the stated constraints. Verify against the course answer key.)*
- **Part 2:** **Answer b**, a human reviews and approves the deployment-config change **before the write executes**, because a wrong value is hard to undo and reaches systems outside the file. (Not "nowhere", not bypass, not review-after-the-fact.)

---

## 2. Durable project context: CLAUDE.md, rules files, hooks, subagents

*(Permission config controls what the agent may do; this layer controls what it knows and how it behaves across sessions.)*

### 2.1 CLAUDE.md
- Read from the **project root** at every session start; contents are **appended to your prompt before any message** → every convention/constraint/command present from the first prompt.
- **`/init`** scans the codebase and generates a starter CLAUDE.md. A baseline, **validate before using**. Refine to hold: testing commands, framework conventions, paths not to touch, style decisions differing from defaults.
- **Size is the main failure mode.** Growth dilutes the rules that matter; a bigger file uses more window and makes each instruction a smaller fraction. **Keep CLAUDE.md to constraints that change behavior; move everything else to Skills that load on demand.**
- Glossary guidance: files beyond roughly **200-300 lines** risk diluting critical rules.

### 2.2 Rules instruction files
- For guidance relevant to only part of the codebase. Live in **`.claude/rules/`**.
- Scoped by a **`paths` glob in YAML frontmatter**; loads into context **only when Claude works with matching files**.
- **Scoping comes from frontmatter, not file placement.** Subdirectories (e.g., `.claude/rules/database/`) are organizational only. **A rules file without `paths` loads unconditionally at launch, same priority as CLAUDE.md**, wherever it sits.
- **Rule of thumb:** universal constraint ("never modify the database schema") → CLAUDE.md. Path-specific ("all SQL in the database module must include an explicit transaction boundary") → `.claude/rules/database.md` with:
```yaml
---
paths:
  - "src/db/**/*.sql"
---
```

### 2.3 Hooks
- Intercept/control tool calls before or after they execute. A CLAUDE.md instruction ("run Prettier after every edit") is followed *most of the time*; **a hook fires every time, independent of what the model decides.**
- Defined in settings files, configured via the **`/hooks`** command. Each hook = **lifecycle event + optional matcher (scopes to tool types) + command**.

| Event | When | Notes / typical use |
|---|---|---|
| **PreToolUse** | Before a tool call executes | Can **block by exiting with code 2**, writing the reason to **stderr** as feedback the agent sees. Enforce access controls at the config layer |
| **PostToolUse** | After a tool call completes | **Cannot block** (already happened). Formatters, test triggers, **audit logging** |
| **UserPromptSubmit** | When you submit a prompt, before the model processes it | Inject context or validate the request |
| **Stop** | When the model finishes responding | Notifications, cleanup, committing the audit log |
| **Notification** | When Claude Code sends a notification (needs tool permission, or idle **60 seconds**) | Route signals to external channels/logging |
| **SessionStart** | Session starts or resumes | Initialize state, validate env vars, confirm services reachable |
| **SessionEnd** | Session ends | Teardown, final audit writes, notifications |

A PreToolUse hook blocking edits to a production config path enforces it on **every tool call in every session, regardless of permission mode**: *the difference between a guardrail and a convention.*

### 2.4 Subagents
- A specialized assistant Claude Code delegates to; runs in its **own separate context** and **returns only its output**. Doesn't inherit main conversation history, accumulated files, or session state; starts clean.
- **Built-in differences matter for project rules:**
  - **Explore** and **Plan** built-in subagents **skip CLAUDE.md and git status** (fast and cheap). So your CLAUDE.md rules are **not in their context**.
  - **General-purpose** subagent **loads both**.
  - For tasks where project constraints must be respected → use general-purpose or a **custom subagent that explicitly loads the rules it needs**. (Check current list in docs; set has grown.)
- **Custom subagents don't automatically see skills.** Defined in **`.claude/agents`**; must **list needed skills in the agent's front matter**. **Built-in agents have no preloaded skills**; for skill-backed behavior, create a custom subagent listing them.

### 2.5 Mechanism map (which carries what)

| Mechanism | Loads | Runs | Context cost | Belongs here |
|---|---|---|---|---|
| **CLAUDE.md** | Full file prepended | Every session, unconditionally | Persistent; dilutes with size | Universal project constraints, commands, framework decisions |
| **Rules file** | File contents; scoped via `paths`; without `paths` loads like CLAUDE.md | When Claude reads a matching file (unscoped: at session start) | Path-scoped: only when triggered; unscoped: persistent | Path-specific guidance that'd be noise elsewhere |
| **Hook** | Runs your script; no content added to context | At the configured event | Minimal (only script output if routed back) | Enforced guardrails, automated side effects, audit logging |
| **Subagent** | Task context only; isolated | When dispatched | Returns a summary, not full history | Exploration, investigation, output that would bloat main context; parallelizable tasks |

*Handles well:* projects revisited across many sessions. *Use another approach:* one-off tasks (setup overhead not warranted).

### 2.6 Postmortem: the CLAUDE.md that grew until rules stopped landing
- CLAUDE.md reached **847 lines**: framework prefs (1-40), testing (41-90), style guide (91-210), dependency rules (211-320), **path restrictions (321-360)**, **historical decisions log (361-700)**, archived notes (701-847).
- Prompt: refactor auth module; **don't modify `/legacy/tokens/`**. Claude edited `/legacy/tokens/store.ts` anyway (to update a token interface) and later apologized; the rule was at **line 347**.
- **Failure = dilution**: 846 other lines reduced the weight of the one that mattered. Historical log and archived notes didn't belong in CLAUDE.md.
- **Lessons:** CLAUDE.md is a **working set of rules**, not an append log. Path-specific → rules file. Historical context → separate reference doc read on demand. Past a few hundred lines, **audit**. **The one rule you can't afford to dilute should be the shortest path to a hook.**

### Checkpoint 2 answers
- Blank 1: **PreToolUse**
- Blank 2: a script that **reads the tool call from stdin, checks the file path, and exits with code 2 when the path is `.env.production`** (writing the reason to stderr). (The other options log/warn and exit 0, which never block.)
- Config shape: `{"hooks": {"PreToolUse": [{"matcher": "Read", "hooks": [{"type": "command", "command": "<script>"}]}]}}`

---

## 3. Packaging a workflow: skills, commands, plugins

### 3.1 Skills across runtimes
A **skill** is a portable Markdown **`SKILL.md`** in **`.claude/skills`**. Front matter identifies it and says when it applies; body holds steps. The same skill can run in Claude Code, via the Messages API, or in the Agent SDK. **The file doesn't change; where it runs, how it loads, and what it can touch do.**

| Runtime | How it loads | Where steps run | Key notes |
|---|---|---|---|
| **Claude Code** | Discovered from `.claude/skills` on the filesystem; loads on description match or by name | Your terminal session, local files, under active permission mode and deny rules | Filesystem-based, governed by the settings layer |
| **Messages API** | Sent with the request; runs in the **code execution container**. Needs **code-execution and skills beta headers** | **Anthropic's container**, not your machine | A skill assuming local files/tools **won't behave the same** |
| **Agent SDK** | Loaded by the SDK's agent, but **whether filesystem settings (CLAUDE.md, skills) load is controlled by `settingSources`** (TS) / **`setting_sources`** (Python). **Don't rely on a default; set explicitly**; confirm default at build time | The SDK process (your environment) once filesystem sources are enabled | **Common surprise:** a skill that worked in Claude Code does nothing under the SDK because `settingSources` was never set |
| **Claude Managed Agents** | Defined once as an **API resource** naming model, system prompt, tools, MCP servers, skills. Anthropic loads the skill **server-side**; no filesystem discovery | Anthropic-provisioned **sandbox** | **Public beta**; needs **`managed-agents-2026-04-01`** beta header; sessions stored server-side → **not eligible for ZDR or HIPAA BAA**. **Skills attached at agent definition time, not session time**; update the definition to change skills |

**Three portability rules:**
1. **Write the description as the matching criterion.** A clear one works in every runtime; a vague one fails to load in all.
2. **Don't assume a local filesystem or tools** inside the skill body. A skill shelling out to a local command works in Claude Code but breaks on the Messages API. Confine steps to what the runtime guarantees, or document the dependency.
3. **Subagents don't inherit skills** (true since Module 2): list them explicitly per subagent in every runtime that supports subagents.

*Handles well:* a task-specific procedure authored once, reused across terminal, API, headless SDK job. *Adds complexity:* beta headers (API), `settingSources` (SDK). *Use another approach:* instructions needed in **every** session → CLAUDE.md.

### 3.2 Custom commands
- In current Claude Code, **skills are the recommended format** for both explicit and automatic invocation: invoke with **`/skill-name`**, or Claude loads automatically when relevant.
- Older **`.claude/commands/`** format still works but is **legacy**.
- For a workflow that runs **only when explicitly called**, use a skill with **`disable-model-invocation: true`** in frontmatter.
- **Plugin commands are namespaced automatically** by plugin name: `run-tests` in plugin `payments` → **`/payments:run-tests`**. Two plugins can both ship `run-tests` without colliding. **The plugin name is part of the interface; renaming the plugin renames every command.**

### 3.3 Plugins
- A **plugin bundles skills, hooks, subagents, and MCP servers** into one installable unit, distributed via a **marketplace** (a catalog of shared plugins).
- **Official Anthropic marketplace** is available automatically; add third-party marketplaces hosted in a GitHub repo: **`/plugin marketplace add <owner/repo>`**. Teammates run one install command → same setup. Replaces a page of manual steps with a **versioned, auditable install**.
- A **plugin manifest** describes the bundle; the install command wires it in. Skills go in a skills directory; hooks/subagents/settings in their locations. Individual or enterprise-wide download.
- **Enterprise:** admins deploy plugins org-wide via **managed settings**. A **managed marketplace allowlist** gates which marketplace sources users may add. **The allowlist restricts what users can add but does not register marketplaces automatically.** To push a marketplace to all users, **pair the allowlist with `extraKnownMarketplaces`** in managed settings. Managed settings sit above user/project in the hierarchy, so managed-scope plugins take priority and can't be overridden. (Check the reference for exact setting names.)

### 3.4 Packaging decision table
| Layer | What | For | Reach for it when |
|---|---|---|---|
| **Skill** | Markdown file in `.claude/skills`; loads on description match or by name | Individual dev/team using Claude Code interactively | Task-specific procedure should stay out of context until needed (PR review, deployment checklist) |
| **Custom command** | Named shortcut that runs a defined procedure on explicit invocation | Devs wanting a predictable explicit entry point | Clear name; trigger directly rather than rely on description matching |
| **Plugin** | Versioned bundle of skills, hooks, subagents, MCP servers via marketplace | Team wanting one-step install of a shared, versioned setup | A working setup on one machine needs sharing/versioning/consistency |

**Cost / Complexity / Risk:**
- *Cost:* skills add context on activation; plugins add install/maintenance overhead; pay setup once (plugin) vs. repeatedly (manual).
- *Complexity:* **hard-coded absolute paths** install fine for the author and fail for everyone else; any path/environment assumption is the likeliest cross-machine break.
- *Risk:* a plugin carries only what it bundles. **A deny rule or hook the author relied on locally is NOT included unless explicitly bundled**, so protection may not reach teammates.

### 3.5 Postmortem: the plugin that installed everywhere and ran nowhere
- Developer packaged a **deployment workflow skill**; local test passed; shipped via internal marketplace; **every install succeeded, every run failed**.
- **Root cause 1:** SKILL.md command pointed at `/Users/alexmorgan/projects/deploy-utils/validate.sh`, an **absolute path** that exists only on the author's machine.
- **Root cause 2:** a second skill relied on env var **`DEPLOY_TOKEN`** set in the author's shell profile, **undocumented in the README**. Three teammates spent **two hours** tracing it. The env var is more dangerous: nothing in the package announces the dependency, so the skill runs fine until the step that needs it.
- **Key distinction:** **install ≠ execution.** Install copies files; execution resolves paths/variables against the running machine.
- **Fixes:** use paths **relative to project root** or env variables: **`$CLAUDE_PROJECT_DIR`** for scripts in the project, **`${CLAUDE_PLUGIN_ROOT}`** for scripts bundled inside the plugin. Bundle or share all scripts/config/assets. **Document every required env var and validate it at install time.** **Test install on a clean machine before distributing.**

### Checkpoint 3 answers (skill → runtime)
- Developer asks for review in **Claude Code terminal** → place `SKILL.md` in **`.claude/skills`** with a description matching review requests.
- Service calling the **Messages API** → send the **code-execution and skills beta headers**; write the skill so steps don't depend on local files/tools.
- Scheduled headless job using the **Agent SDK** → **set `settingSources` explicitly** to enable filesystem sources (don't rely on defaults; confirm at build time).
- Long-running Anthropic-hosted agent reachable by agent ID → **define the agent as an API resource listing the skill**, set the **`managed-agents-2026-04-01`** header; skill must not depend on local files (runs in Anthropic's sandbox).

### Checkpoint 4 answers (broken SKILL.md)
- Defect (Part 1): **C**, the absolute path `/Users/alexmorgan/projects/deploy-utils/validate.sh` in step 1.
- Fix (Part 2): **A**, reference the script from the project root with **`CLAUDE_PROJECT_DIR`** so it resolves wherever the project is cloned. (Not another absolute path, not `~/...`, not removing the step.)

---

## 4. MCP: building and configuring servers (transport, scope, GitHub)

### 4.1 What MCP is and why
- Wiring a tool directly means schema and function live in each application's code; three apps needing the same service means three integrations.
- **MCP separates tool definitions from applications into a server process.** An MCP server exposes **tools, resources, and prompts** to MCP clients. Claude Code has a **built-in MCP client**; it discovers the server's tools and invokes them. **Build once; every client gets it.**

### 4.2 Tools, resources, prompts
- **Tools:** actions the model can call.
- **Resources:** **read-only data** the server exposes for the *client* to fetch and place into context directly, rather than the model calling a tool. Requested by **address**.
  - **Direct resource:** fixed address, no parameters (e.g., list of available documents).
  - **Templated resource:** parameter in the address (e.g., document address taking a document ID).
  - Use when you want known data in context from the start of a turn, and pulling it in directly is cheaper/more predictable than a tool call. **Resource support varies across clients; verify your client can inject resources into context.**
- **Prompts:** pre-written **instruction templates** the server exposes so a client invokes a vetted prompt **by name**. Use when specific wording materially improves results and you want consistent quality across clients; maintained in one place.

### 4.3 Transport
| Transport | What | Use |
|---|---|---|
| **stdio** | Server runs as a **local subprocess** on the client's machine; communicates via stdin/stdout | Local tools, personal scripts, dev servers. **Doesn't work for servers shared across a team or hosted remotely** |
| **HTTP** | Connects over a standard HTTP connection; supports servers on another machine; you register the URL | **Recommended for any non-local server.** Shared team servers, hosted integrations |
| **SSE** | Older Server-Sent Events transport | **Superseded by HTTP; not recommended for new servers.** Treat as legacy |

### 4.4 MCP context cost (Claude Code)
- Each connected server contributes tool definitions. **By default, Claude Code defers these** and uses a **search step** to load only the relevant tools when a task needs them.
- **Opt-in mode** loads definitions upfront when they fit within roughly **10% of the context window**, deferring only when exceeded.
- Either way, **connect only the servers you need.**

### 4.5 Prompt caching (re-explained in this module)
- Every request reprocesses its input; caching stores processing of a **stable prefix** so follow-ups reuse it at a fraction of the cost. First request writes; later identical-prefix requests read.
- **Exact match required:** one changed character before the cache point invalidates the cache and forces a fresh write. Best candidates: long system prompt, large tool definition set, reference doc queried repeatedly.
- **Enabled by breakpoints; there's no global setting.** In the Messages API add **`cache_control` of type `ephemeral`** to the **last block you want cached** (caches everything up to and including it). **Up to four breakpoints.**
- **Processing order: tools → system prompt → messages.** A breakpoint after tools caches tool definitions while keeping messages dynamic.
- **Lifetime:** default **5 minutes** from last read (each read resets the clock). Opt-in **1 hour** via **`ttl: "1h"`** on the breakpoint, suited to workloads with longer gaps (e.g., an agent pausing between steps). If the window expires, you pay the write cost again for no read benefit.
- **Minimum threshold:** **1,024 tokens** for most current models; shorter prompts aren't cached even with a breakpoint.

### 4.6 RAG
- **Problem:** a model reads everything in context each request; loading all docs wastes context. **RAG** stores material outside the window, finds the relevant parts, supplies only those.
- **Classical RAG:** upfront work: split source into **chunks**, convert each to an **embedding** (numbers capturing meaning), store in a database. At query time, embed the question and find the most similar chunks. *(Librarian who pre-read every book and wrote summary cards.)*
- **Agentic search:** **no upfront indexing**; the model searches live sources/reads documents on demand as the task unfolds. *(Researcher who goes and finds the answer.)* Examples already encountered: Claude Code discovering only needed MCP tools; Claude.ai Projects surfacing relevant sections when the knowledge base exceeds the window.
- **Difference is timing:** classical = match against a pre-built index; agentic = search at moment of need.
- **Two properties:** (1) **Scales**: per-request cost stays flat as the corpus grows. (2) **Only as good as what it finds**: if retrieval misses the document, the model never sees it. Organization matters: descriptive file names ("Q3 refund policy, updated August 2024") beat vague ones ("notes_final_v3.pdf"); group related files.

### 4.7 Configuration scope: who loads the server
| Scope | Stored in | Applies to | Use for |
|---|---|---|---|
| **Local** | `~/.claude.json` under the current project's path | Only the current project; **not shared** | Server tied to one project you're not ready to commit; project-only tooling |
| **User** | Your personal Claude settings | **All your projects**; still personal, not in the repo | Personal utility used everywhere (local DB tool, script) |
| **Project** | **`.mcp.json` at repo root** | Everyone who clones (when committed) | Server the whole team accesses; config travels with the code. **Project-scoped stdio servers run from each teammate's machine**: the committed config stores the launch command; every clone spawns its own local subprocess, so each teammate needs the runtime (e.g., **Node for `npx`**) installed |
| **Enterprise** | Centrally managed config | All users in the org | Shared internal services, security tooling; **cannot be left to individual developers** |

### 4.8 Permission rules for individual MCP tools
- MCP tool identifier in rules: **`mcp__server__tool`**. Allow on `mcp__github__create_issue` runs that one tool without prompting while others still prompt. A deny on a write-capable tool blocks it while read-only tools stay available. **A deny on one tool overrides an allow on the server.**
- **API MCP connector:** `mcp_toolset` with a per-tool **`enabled`** flag. **Two different controls:**
  - **Permission rule** = whether an exposed tool *may run* (**governance**).
  - **`enabled` flag** = whether the model *sees* the tool at all (**context-cost and scope**).
  - Often used together. Verify exact rule syntax and the connector beta header in docs.

### 4.9 GitHub MCP server (worked example)
- **Remote server maintained by GitHub**: repository management, reviewing PRs, opening issues, searching code.
- **Transport: HTTP** (hosted remotely); register by URL.
- **Scope:** project scope if the whole team needs the same repo tooling; local if only you.
- **Authentication: Personal Access Token (PAT)** passed as a **Bearer token in the request header**, **supplied via an environment variable** and referenced in config. **Never inline in `.mcp.json`**: a committed token enters history and can't be removed by overwriting.
- **Contrast, OAuth (Linear MCP):** the client redirects to the provider's sign-in page on first connect; after you approve, a token is issued and stored automatically; no credential copied by hand. Right when the service's authorization model is tied to user identity.
- GitHub = service credential you generate and store; Linear = sign-in flow handles the credential. **Both are remote HTTP; same transport/scope logic; only authentication differs.**

### 4.10 MCP setup reference
| Context | Transport | Scope | Config location | Secrets |
|---|---|---|---|---|
| Personal local tool | stdio | Local | `~/.claude.json` (per-project entry) | Env vars only; never in config |
| Shared team server | HTTP | Project (`.mcp.json`) | `.mcp.json` committed at repo root | OAuth or env vars; **API keys never committed** |
| Personal experiment | stdio or HTTP | Local | Personal Claude settings | Env vars only |
| Org-wide deployment | HTTP | Enterprise | Managed settings (admin-controlled) | Admin-managed; config locked |

**Cost / Complexity / Risk:**
- *Cost:* each server adds tool definitions; load only what a task needs.
- *Complexity:* transport and scope are independent but interact: **a stdio server cannot be project-scoped for real sharing** because it runs only on one machine. Match transport to where the server runs first.
- *Risk:* **committing an API key in `.mcp.json`** is the most common mistake. Secrets in env vars; config holds only the address.
- *Handles well:* reusable, stable integration shared across sessions/team (GitHub). *Adds complexity:* teams not managing env secrets carefully; risk concentrates on committed `.mcp.json`. *Use another approach:* one-off, single-project, single-person integrations → wire the tool directly in the API call.

### 4.11 Postmortem: the API key that traveled with the config file
- Dev connected to a **data warehouse MCP server** using a service-account key placed **directly in `.mcp.json`**, intending to move it later. Committed so teammates could clone.
- Within **48 hours**: key on dev's machine, **repo history**, **three teammates' machines**, and a **CI runner**. Dev moved it to an env var and recommitted, but the key stayed in history → **service account had to be rotated**, which **broke two external services using the same key** (**3 hours** to fix).
- **Before (wrong):** `"Authorization": "Bearer sk-abc123..."`. **After (right):** `"Authorization": "Bearer ${WAREHOUSE_MCP_TOKEN}"`.
- **Lessons:** overwriting removes it only from the current version; **any credential written inline in a committed file must be treated as compromised and rotated.**
- **Two-layer prevention:** (1) a **CLAUDE.md convention** that credential values must never be written inline to `.mcp.json` (signals intent); (2) a **PreToolUse hook** that inspects write/edit operations on `.mcp.json` for credential-like patterns and **exits with code 2** to block (enforces deterministically). Same hook-vs-instruction distinction as section 2.

### Checkpoint 5 answers
- Local SQLite query tool, only on your dev machine → **stdio + Local**
- Code search service on company infrastructure for the whole eng team → **HTTP + Project (`.mcp.json`)**
- Experimental scraping server, one repo, not ready to share → **stdio or HTTP + Local**
- Security-scanning server IT must deploy to every developer → **HTTP + Enterprise (managed settings)**

---

## 5. Enterprise integration and secure authentication

### 5.1 Prototype vs. production
A prototype asks "does the connection work?" Production must also answer: **who is the model acting as (auditable identity)? what data can it access and where does it leave the org? can an admin lock the auth config? can access be logged for a compliance audit?**

### 5.2 Authentication by service type
| Service type | Method | Details |
|---|---|---|
| **Remote with user identity** (SaaS, cloud) | **OAuth** | Server returns **401 Unauthorized**; client starts a browser sign-in; token issued and stored; no secret copied by hand. Linear uses this; GitHub uses a PAT header instead |
| **Remote with service identity** (internal API) | **API key in an environment variable** | Key identifies the service account; never committed. For CI with the Agent SDK, the key is **injected as a secret by the pipeline runner**, not baked into code |
| **Local with file-system access** | **stdio, no network auth** | Security boundary = file-system permissions; **deny rules** are the governance layer |

### 5.3 Managing the secret itself: three practices
1. **Separation:** the credential never travels with the config that references it. Config holds a **variable reference**; the value lives elsewhere. Configs get committed/shared/cloned; inline values ride along into history.
2. **Where the value lives:** **environment variable** injected at execution (CI sets it as a secret, config reads by name, nothing on disk) for **local/short-lived** secrets; a **secret store** (managed service that returns credentials to authorized callers at runtime and records who read what) for **shared or auditable** secrets: one rotation updates every consumer; removes accumulating copies.
3. **Rotation:** replace on a **schedule** and **immediately after any suspected exposure**. The only right response to a leaked key; an exposed key can't be made secret again. Credentials read from a store/env var rotate **without touching code** (the name doesn't change when the value does); inline values can't be rotated cleanly.

**Two cost-saving habits:** **scope each credential to the narrowest access its task needs**, and **keep a record of which services use each credential** so a rotation doesn't surface hidden consumers.
**Link to the leak:** separation keeps the value out of the file; env/store gives it a home; rotation helps recovery *only if the first two hold*.

### 5.4 What regulated industries add
- **Locking config:** enterprise managed configuration that users can't override → consistent auth setup across the org.
- **Logging:** a **PostToolUse hook** logging every tool call and its parameters to an audit store; fires deterministically, model can't skip it.
- **Data residency:** an HTTP endpoint in a specific region plus a platform deployment pinning processing to that region → checkable answer to "where does data go?" (This is why the platform/infrastructure choice from Module 2 matters at **audit** time, not just build time.)

### 5.5 Code modernization as the test case
Legacy change has **high blast radius, unpredictable dependencies, limited reversibility**. Tools applied beforehand:
- **Explore/plan/code loop** + **plan mode** (read-only exploration; review proposed edits, spot unexpected paths, push back before any file is modified).
- **Hooks** block edits to specific paths during sensitive phases.
- **CLAUDE.md** carries conventions for the *target* patterns so the agent doesn't drift back to legacy patterns in surrounding code.

**Three scoping questions before the session starts:**
1. **Blast radius:** which systems depend on the code, and what breaks downstream if an edit is wrong?
2. **Audit:** is a **PostToolUse hook** logging every tool call, and does it satisfy the reviewer?
3. **Approval:** **who approves each phase before the next begins?** Plan mode enforces the explore/execute boundary, but the approval decision is yours to define and document beforehand.
(These apply to any high-risk agentic task.)

### 5.6 Authentication & integration checklist
| Service type | Auth | Secrets live | Logged | Who can lock config |
|---|---|---|---|---|
| Remote, user identity (SaaS) | OAuth | Token issued by provider, stored by client | PostToolUse hook → audit log | Admin via enterprise managed settings |
| Remote, service identity (internal API) | API key in env var | Environment only, never committed | PostToolUse hook → audit log | Admin via enterprise managed settings |
| Local (filesystem, local DB) | File-system permissions | No credential; deny rules enforce paths | PostToolUse hook → audit log | Deny rules in enterprise managed settings |

**Cost / Complexity / Risk:**
- *Cost:* OAuth = one-time setup per user per service; API keys need a rotation process; audit hooks add small per-call overhead.
- *Complexity:* regulated environments add requirements not in a prototype; identify them during scoping.
- *Risk:* concentrates at prototype → production: **hardcoded credentials + no audit log + not centrally lockable = fails a regulated security review.**
- *Handles well:* integrations touching data a regulated customer cares about. *Adds complexity:* teams unfamiliar with OAuth/secrets management (need security/IT coordination, plan the timeline). *Use another approach:* a demo that never sees production data (but use env vars for secrets anyway; it costs nothing).

### 5.7 Postmortem: OAuth worked in staging, failed in production
- Integration passed all staging tests; **every production sign-in failed with a redirect URI mismatch**.
- Dev had registered the OAuth app for **`staging.mycompany.com`**; **`production.mycompany.com`** wasn't on the provider's allowed list.
- **Cause:** **OAuth redirect URIs are registered per host** (often governed per environment). Not a code defect; a configuration step.
- **Second issue the reviewer flagged:** reusing one app registration across environments. **Most enterprise customers require separate OAuth app registrations per environment.**
- **Lessons:** before moving an OAuth MCP integration to a new environment, **add the new host's redirect URI**; check whether separate app registrations are required; **put the registration step in the deployment checklist**.

### Checkpoint 6 answer
Trace: client hits `https://data-api.internal/mcp`, gets **401**, reads the credential from **`/home/jenkins/.config/mcp-credentials.json`** (a file on disk), retries, gets **401** again, fails after 3 attempts.
- **Mechanism:** the credential was stored in a **file on the CI runner**, separated from nowhere, and the stored key is **rejected/stale** (an expired or revoked key kept in a file).
- **Answer B:** **rotate the rejected key, then move the credential out of the file and inject it as an environment variable in the CI pipeline runner configuration; update the MCP config to reference the variable.**
- Why not A: rotating but writing the new value back into the same file repeats the same flaw. Why not C: switching to OAuth is wrong for a **service identity** (CI/internal API), where OAuth's user sign-in doesn't fit.
- *(The source gives no answer key for this checkpoint; B is derived from sections 5.2-5.3. Verify.)*

---

## 6. Cumulative integration task: three files, three bugs

### Stage 1: find the bugs
| File | Layer | Bug | Runtime effect |
|---|---|---|---|
| `.claude/settings.json`: `defaultMode: "bypassPermissions"`, deny `Read(.env.production)` | **Claude Code config** | Mode is **bypassPermissions** | Removes every confirmation prompt on a production workstation, **including for destructive operations**. The **deny rule is correct**; only the mode is wrong |
| `.claude/skills/migration-validate/SKILL.md`: step 1 runs `/Users/priya/scripts/validate-migration.sh` | **Plugin/packaging** | **Absolute path** to the author's machine | Resolves on no teammate's machine after clone |
| `.mcp.json`: `"Authorization": "Bearer sk-prod-warehouse-abc123"` | **MCP/authentication** | **Inline API key** committed | Enters repo history; can't be removed by overwriting; **must be treated as compromised** |

### Stage 2: corrected files
**settings.json:**
```json
{ "permissions": { "defaultMode": "acceptEdits", "deny": ["Read(.env.production)"] } }
```
*(acceptEdits auto-approves file edits and common filesystem commands but gates destructive shell commands, the right tradeoff here. Note from section 1.5: acceptEdits still auto-approves `rm` inside the working directory, which is why the deny rule matters.)*

**SKILL.md:**
```markdown
---
name: migration-validate
description: Validates migration scripts before they run against production.
---
## Steps
1. Run: $CLAUDE_PROJECT_DIR/scripts/validate-migration.sh
2. Report validation results.
```

**.mcp.json:**
```json
{
  "mcpServers": {
    "data-warehouse": {
      "type": "http",
      "url": "https://warehouse.internal/mcp",
      "headers": {
        "Authorization": "Bearer ${WAREHOUSE_MCP_TOKEN}"
      }
    }
  }
}
```
Plus: because the old key was committed, **rotate it**.

---

## 7. The seven takeaways (one per section)

1. **Permission mode is a risk decision, not a speed decision.** Match mode to the work and environment. Bypass on a workstation against a live codebase removes every checkpoint. Cover the gap with a **deny rule** at project or enterprise level.
2. **An AI code review gives findings to triage, not a verdict to apply.** Trust findings the reviewer can **prove from the diff** (missing null check, unclosed resource) and confirm them on the cited lines. Treat claims about **runtime behavior or other systems as hypotheses to test**. Put the human gate **where a finding becomes a hard-to-reverse action**; improve reviewer accuracy by **supplying the conventions it would otherwise guess**.
3. **A skill is portable, but "runs everywhere" is designed for.** Same SKILL.md; filesystem discovery (Claude Code), beta headers + code execution container (API), `settingSources` (SDK). Clear description + no local assumptions ports cleanly. **Subagents start clean; skills aren't preloaded.**
4. **Durable context needs the right mechanism per concern.** CLAUDE.md = session-persistent memory (dilutes with size); rules files scope guidance; hooks enforce deterministically; subagents keep exploration out of main context. Forcing everything into CLAUDE.md yields a file harder to maintain and easier to ignore.
5. **A shareable setup requires portable components.** Absolute home-directory paths install on one machine and fail elsewhere. Use project-root-relative paths, document/validate env vars at install time, **test from a clean machine**.
6. **Transport and scope are independent decisions with dependent consequences.** stdio = runs on your machine; HTTP = remote or multi-developer. Local = personal; project = shared via `.mcp.json`. A shared team server needs **HTTP + project or enterprise scope**. **A stdio server in `.mcp.json` looks shareable but isn't.**
7. **Enterprise integration: identify security requirements before deployment.** Regulated customers ask about identity, data residency, access logging, config control. Answers: **OAuth** (user identity), **env vars** (service credentials), **PostToolUse hooks** (audit logging), **enterprise managed settings** (config lock). Easy to implement, hard to retrofit after a failed review.

**What comes next: Module 4**, production engineering, evals, and security: measuring whether Claude Code integrations work at scale, building eval harnesses, designing production-grade safety guardrails. Permission modes, hooks, and authentication from this module are what those evals test.

**Sources cited:** Claude 101; Claude Code 101 In Action; Building with the Claude API (Skilljar); code.claude.com; platform.claude.com; docs.claude.com.

---

## 8. Glossary

- **Claude Agent SDK:** programmable interface exposing the same agent loop Claude Code runs; lets devs invoke it from code, set permission mode and tools, run tasks without an interactive session. **Same permission model and deny rules as the terminal.**
- **CLAUDE.md:** Markdown file at the project root; prepended to context every session; universal constraints, conventions, commands. Beyond roughly **200-300 lines** risks dilution.
- **Hook:** command bound to a lifecycle event (PreToolUse, PostToolUse, UserPromptSubmit, Stop, etc.). Runs **deterministically** regardless of the model. A PreToolUse hook **exits 2 to block**.
- **MCP (Model Context Protocol):** open layer letting a client (e.g., Claude Code) connect to a server exposing **tools, resources, prompts**; moves tool definition/maintenance out of app code into a reusable server.
- **MCP transport:** the client↔server channel. **stdio** = local subprocess; **HTTP** = remote over network. Determines where the server can run and who can connect.
- **Permission mode:** controls how often the agent asks before tool calls, from `default` (prompts nearly always) to bypass (none). **Deny rules override any mode**; an enterprise deny can't be bypassed by any individual config.
- **Plugin:** versioned bundle of skills, hooks, subagents, MCP configs distributed via a marketplace; single-step install; deployable org-wide via managed settings.
- **Rules instruction file:** scopes guidance to a path/condition; activates only when working in the matching area (unlike always-on CLAUDE.md).
- **Subagent:** separate execution context for a delegated task; doesn't inherit main context; starts clean; returns only a summary.

---

## 9. Master cheat sheet (Module 3)

**Modes → when**
- default (new/unfamiliar) · acceptEdits (trusted local edits; not for running scripts) · plan (explore sensitive code; no edits) · auto (classifier-gated; research preview; not a guarantee) · dontAsk (locked-down CI; deny-by-default) · bypassPermissions (**isolated container/VM only**).
- **Deny > allow, always.** Enterprise deny is un-removable and applies even in bypass.

**Settings precedence of scope:** enterprise (managed-settings.json) > project (`.claude/settings.json`) / local (`.claude/settings.local.json`) / user (`~/.claude/settings.json`); enterprise can't be overridden.

**Pick the mechanism**
- Always-true, short → **CLAUDE.md**. Path-specific → **rules file with `paths`**. Must happen every time / must block → **hook** (PreToolUse, exit 2). Heavy exploration / parallel work → **subagent** (use general-purpose or custom if CLAUDE.md rules matter; Explore and Plan skip CLAUDE.md). On-demand procedure → **skill**. Share a whole setup → **plugin**.

**Hook semantics:** PreToolUse can block (exit 2, reason to stderr); PostToolUse cannot (use for formatting/tests/audit).

**Path variables:** `$CLAUDE_PROJECT_DIR` (project scripts) · `${CLAUDE_PLUGIN_ROOT}` (plugin-bundled scripts). Never absolute home-directory paths.

**Runtime requirements for skills:** Claude Code → `.claude/skills` · Messages API → code-execution + skills beta headers (container, no local files) · Agent SDK → set `settingSources`/`setting_sources` explicitly · Managed Agents → `managed-agents-2026-04-01`, skills attached at agent-definition time, **no ZDR/HIPAA**.

**MCP decisions:** local tool → stdio + local · shared team server → HTTP + project (`.mcp.json`) · personal experiment → local · org-wide → HTTP + enterprise. SSE = legacy. Rule syntax `mcp__server__tool`. Permission rule = may it run; `enabled` flag = does the model see it.

**Secrets:** never inline in committed config → `${ENV_VAR}` reference → env var (local/short-lived) or secret store (shared/auditable) → **rotate** on schedule and on any exposure → least-privilege scope → track consumers.

**Auth by type:** user identity → OAuth · service identity → API key in env var · local → file-system permissions + deny rules. **OAuth redirect URIs are per host; use separate app registrations per environment.**

**Enterprise must-haves:** managed settings (lock) + PostToolUse audit hook (log) + pinned region (residency) + OAuth/env/secret store (identity).

**Caching:** `cache_control: {type: "ephemeral"}` · ≤4 breakpoints · order tools→system→messages · 5-min default, `ttl: "1h"` opt-in · 1,024-token minimum · exact prefix match.

**Header/name list:** `managed-agents-2026-04-01` (Managed Agents) · `code-execution-2025-08-25` + `skills-2025-10-02` (Skills on Messages API, from Part 2) · `mcp-client-2025-11-20` (MCP connector, from Part 1) · `/init`, `/hooks`, `/plugin marketplace add <owner/repo>`, `/skill-name`, `/plugin-name:command` · `disable-model-invocation: true` · `extraKnownMarketplaces`.

---

**Gaps and flags in the source:**
- Objective 2 (AI code review / calibrated trust) has **no teaching section** in the pasted text, only takeaway #2.
- **Checkpoints 1 (Part 1) and 6** have no answer key shown; the answers above are derived from the module's own rules, so confirm them in the course.
- In the Checkpoint 6 trace, the source doesn't explicitly name the failure mechanism; my labeling (credential stored in a file, rejected by the server) is inferred.
- The "Claude Code agent loop" intro mentions it, but the **Agent SDK vs. Claude Code** differences are only described through skills loading and `settingSources`.

I can also put all three parts into a single editable doc if you'd like.
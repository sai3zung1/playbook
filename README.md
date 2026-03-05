---
mutability: controlled
updated: 2026-03-04
version: 1.2
---

# Playbook — cong3 STUDIO

**The universal playbook. Every project inherits these rules. No exceptions.**

This document defines how we work — not what we work on. It is project-agnostic. Each project has its own `project-context.md` that defines the specifics (structure, roles, tools, tasks) and references this playbook for the method.

---

## 1. Core Principles

Six non-negotiable axioms. Everything else derives from these. When in doubt, come back here.

### P1 — Human Approval Gates

No content enters the project without explicit human approval. An agent can propose, analyze, suggest. It does not decide. Validation belongs to the project owner — or a designated human contributor.

### P2 — Right Question, Right Time

Before generating anything, two checks:

1. **Is this the right question to ask NOW?** Does the context required to answer it already exist in the project?
2. **Are the prerequisites validated?** Are the assumptions this answer will rely on themselves validated?

If either answer is no — the question is premature. Do not generate.

### P3 — One Truth, One File

Every piece of information exists in exactly one place. Zero duplication. Structure and naming are clear enough to navigate without pointers. Pointers are only used when the connection between two files is not obvious from the structure itself.

### P4 — Minimum Viable Artifact

An artifact exists to answer a specific question. If it answers questions that haven't been asked yet, it's too large. Every artifact starts in its smallest viable form. It expands only by explicit, documented decision.

### P5 — Team-Ready by Default

Every document is written as if an unknown contributor will read it tomorrow. No implicit context, no verbal assumptions. If it's in the project, it's understandable without its author there to explain it.

### P6 — Platform Separation

Each tool has a single responsibility. The project's `project-context.md` defines which tools are used and what each one does. No tool overlaps with another. When a tool's scope changes, the assignment is updated — not duplicated.

---

## 2. File Header Standard

Every documentation and configuration file (.md, .yaml) MUST include a YAML frontmatter.

```yaml
---
mutability: mutable | immutable | controlled
updated: YYYY-MM-DD
version: X.Y  # only for controlled files
---
```

- **mutable** — changes regularly (e.g. session logs, entry points)
- **immutable** — do not modify (e.g. installed frameworks, generated files)
- **controlled** — modifiable only by explicit decision of the project owner + version bump

**Exceptions** are project-specific and documented in each project's `project-context.md`.

**Agent modification tracking:** When an agent file is modified after its initial validation, the corresponding validation report must be flagged with `revalidationNeeded: true` and `revalidationReason` in its frontmatter. The report remains a snapshot. Re-validation is a separate task.

---

## 3. Output Rules

- All generated artifacts land at a staging zone (project root by default)
- Agents do NOT write directly into project folders
- The project owner validates and places the artifact in the correct location (Artifact Gate)

---

## 4. Validation Gates

Three gates govern all transitions.

### Gate 1 — Question Gate

**When:** before asking an agent a question or starting any work.

**Criteria:**
- Previous phases contain validated content
- The question does not depend on an answer that doesn't exist yet
- The question can be stated in one clear sentence

**Who:** project owner (or designated contributor). Verifier agent can challenge.

### Gate 2 — Artifact Gate

**When:** before any artifact (new or modified) is accepted into the project.

**Criteria:**
- The artifact answers the question that was asked — no more, no less
- No unvalidated assumption presented as fact
- Zero duplication with existing content
- Understandable without oral context (P5 — Team-Ready)
- In the correct location within the structure
- Includes valid YAML frontmatter header

**Who:** project owner validates. Verifier agent audits after.

**Applies to:** creations AND modifications. Every write to the project goes through this gate.

### Gate 3 — Phase Gate

**When:** before moving from one project phase to the next.

**Criteria:**
- All necessary artifacts in the current phase are present and validated
- No critical unresolved assumptions
- Current artifacts are sufficient to feed the next phase
- Project owner explicitly confirms

**Who:** project owner decides. Verifier agent produces a completeness audit.

---

## 5. Contribution Model

Every contribution — human or AI — follows the same process. The source does not change the gate. An agent can never self-validate its own output.

```
Project owner (final decision-maker)
  ├── Human contributors (defined scopes)
  └── AI agents (generators, not decision-makers)
         └── Verifier agent (audits, never self-validates)
```

When a human contributor joins, they inherit a documented scope. When a contributor replaces an agent on a scope, the agent becomes support (consultable, not autonomous).

The specific roles and authority matrix are defined in each project's `project-context.md`.

---

## 6. Session Protocol

### Opening

1. **Read project entry point** — auto-loaded for code agents, loaded via SO skill for desktop agents, mandatory read for humans
2. **Read project-context.md** — project governance for the session
3. **Check session log** — recent sessions, recent decisions

### During Session

- Apply Gate 1 before each question
- Apply Gate 2 before each write
- Agent generates → project owner (or contributor) validates → file enters the project

**Task Decomposition Protocol — mandatory before every task, no exception:**

Before executing any task, the agent stops and completes two phases.

**Phase 1 — 5W (framing):**

1. **Qui** — Who is this for? Who will read, use, or depend on the output?
2. **Quoi** — What exactly am I producing? What is the expected deliverable?
3. **Pourquoi** — Why does this task exist? What problem does it solve, what question does it answer?
4. **Comment** — How do I execute? What approach, what tools, what format, what conventions apply? Have I read the files related to what I'm about to do?
5. **Quand** — Where does this sit in the project sequence? Are the prerequisites met? Is this the right time (P2)?

**Phase 2 — Simulation (validation):**

The agent places itself in the position of the end user of the deliverable and mentally walks through the entire process from start to finish. Does the output hold up? What's missing? What breaks? Do the answers from Phase 1 survive contact with reality?

Present Phase 1 answers and Phase 2 findings to the project owner before executing. If the simulation reveals gaps, resolve them first.

This protocol applies to every task — creation, modification, deletion, audit, skill execution. No exception, no shortcut.

**Impact Propagation Rule — mandatory after every add, modify, or delete:**

1. **What did I just change?** (concept, structure, convention, protocol)
2. **Which other files contain or reference this information?** (`grep` the concept — do not guess)
3. **Are autonomous skills affected?** (skill files always in scope when governance changes)
4. **Are dates and versions still current?** (YAML frontmatter `updated`, `version` for controlled files)
5. **Do all cross-references still resolve?** (file paths, section numbers, step numbers)

Do not move to the next task until all five questions are answered and all impacted files are updated. This is not an audit — it is part of the write itself.

Project-specific propagation checks (e.g., external mirrors, platform syncs) are defined in each project's `project-context.md`.

**Sub-task Checkpoint — mandatory before moving to the next sub-task:**

After completing a sub-task and before requesting validation to proceed, the agent answers these five questions:

1. **Does what I just wrote contain content that already exists elsewhere in the project?**
2. **Does what I just wrote respect every convention that applies to the target file?**
3. **Did I choose a name, label, or structure without consulting the project owner?**
4. **Can I show the exact diff of what changed in this sub-task?**
5. **Has the project owner validated this sub-task before I move on?**

If any answer reveals a problem, fix it before presenting the sub-task for validation. Question 5 is a hard gate — no implicit approval, no assumption of consent.

### Closing

The active agent handles session close.

1. **EXTRACT** — list artifacts created/modified, decisions made, open questions, deferred ideas
2. **LOG** — write an entry in the session log
3. **UPDATE** — update the project entry point (current state)
4. **VALIDATE** — run project validation tools
5. **IDEA BOX** — append deferred ideas to the idea box if any emerged

Project-specific closing steps (syncs, captures, platform-specific actions) are defined in each project's `project-context.md`.

**Log entry format:**

```markdown
## [Date] — [Who] — [Short Title]
- Artifacts: [files touched]
- Decisions: [short list]
- Next action: [one sentence]
```

### Archive System

- Session log contains **current week only** (Monday–Sunday)
- Monthly archives contain **historical entries** (browsable by period)
- **Ordering:** newest-first everywhere. The most recent entry is always at the top.
- **Rotation:** automated or manual, weekly. Entries older than the current week move to the corresponding monthly archive.
- Archive files are never auto-loaded. Consult on-demand when historical context is needed.

---

## 7. Git Convention

**Format:** `<type>(<scope>): <description>`

**Universal types:**

| Type | Usage |
|------|-------|
| `init` | Project setup, tooling, infrastructure |
| `ops` | Operational changes (protocol, tools, session log) |
| `fix` | Bug fix |
| `refactor` | Restructuring without changing behavior |
| `docs` | Documentation only |

**Rules:**
- Scope is optional: file or area concerned
- Description: imperative, lowercase, no period
- Project-specific commit types (mapped to project folders) are defined in each project's `project-context.md`

---

## 8. ID Convention

All trackable items use a PREFIX-NUMBER format. IDs are sequential and never reused.

The specific prefixes are defined in each project's `project-context.md`. Common patterns:

- Tasks: `TS-1`, `TS-2`...
- Sessions: `S-1`, `S-2`...
- Ideas: `I-1`, `I-2`...

---

*This document is the SSOT for how work gets done at cong3 STUDIO. Any question about "how do we work" finds its answer here. Any question about "what are we building" finds its answer in the project's `project-context.md`.*

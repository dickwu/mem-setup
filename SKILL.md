---
name: mem-setup
description: Set up a domain knowledge management system with auto-loading rules and insight extraction. Creates /knowledge/ directory with domain folders, installs /mem-load (auto-loads rules before tasks) and /mem-save (extracts insights after tasks). Use when starting a new project, setting up knowledge management, or when the user mentions "mem-setup", "knowledge system", "domain rules", "project memory", or "learning from tasks". Also use when someone wants to track patterns, hypotheses, and confirmed rules across sessions.
---

# Memory Setup

Install a persistent domain knowledge system for any project. This creates two commands (`/mem-load` and `/mem-save`) and a `/knowledge/` directory that accumulates expertise across sessions.

The system tracks three types of insights:
- **Knowledge** — confirmed facts and patterns
- **Hypotheses** — patterns that need more data (tracked with confirmation counts)
- **Rules** — hypotheses confirmed 5+ times, applied by default

## What Gets Installed

```
<project>/
  .claude/commands/       # Claude Code
    mem-load.md
    mem-save.md
  .cursor/rules/          # Cursor (if detected)
    mem-load.mdc
    mem-save.mdc
  knowledge/
    INDEX.md              # Routes to domain folders
    <domain>/
      knowledge.md        # Facts and patterns
      hypotheses.md       # Need more data
      rules.md            # Confirmed - apply by default
```

## Setup Steps

### 1. Detect the Agent

Check which AI coding agent is being used and set the command directory:

| Agent | Command path | Extension |
|-------|-------------|-----------|
| Claude Code | `.claude/commands/` | `.md` |
| Cursor | `.cursor/rules/` | `.mdc` |
| Codex | `.codex/commands/` | `.md` |
| Other | `.agents/skills/` (universal) | `.md` |

Create the command directory if it doesn't exist.

### 2. Create mem-load Command

Write the following content to the command file (e.g., `.claude/commands/mem-load.md`):

~~~markdown
---
name: mem-load
description: Auto-load domain knowledge at task start. Reads /knowledge/INDEX.md, finds relevant domain folders for the current task, loads rules.md (apply by default) and hypotheses.md (check if testable). Invoke manually when switching domains mid-conversation or when working in a domain you haven't loaded yet.
---

# Memory Load

Load relevant domain knowledge before starting work. This ensures confirmed rules are applied and testable hypotheses are tracked.

## Steps

1. **Read the index** — read `/knowledge/INDEX.md` to see all available domains
2. **Match domains** — identify which domains are relevant to the current task based on the user's request, file paths, and topic keywords
3. **Load rules** — read `rules.md` in each relevant domain. These are confirmed patterns (5+ confirmations). Apply them by default without being asked
4. **Load hypotheses** — read `hypotheses.md` in each relevant domain. Note which ones could be tested or observed during this task
5. **Brief the user** — one-line summary:

```
Loaded: [domain1] (N rules, M hypotheses), [domain2] (N rules, M hypotheses)
Watching: [hypothesis that could be tested today]
```

## What to Do With Loaded Knowledge

- **Rules**: Follow them. They're confirmed patterns. If a rule conflicts with the current task, note the conflict but still follow the rule unless the user explicitly overrides
- **Hypotheses**: Watch for confirmation or contradiction during the task. Don't change your approach based on hypotheses — they're unconfirmed. Just observe and note for `/mem-save` later
- **Knowledge**: Use as context. These are facts that help you understand the codebase

Load multiple domains when tasks span boundaries. When uncertain, load the domain — better to have context you don't need than miss a rule you should follow.
~~~

### 3. Create mem-save Command

Write the following content to the command file (e.g., `.claude/commands/mem-save.md`):

~~~markdown
---
name: mem-save
description: Save domain knowledge insights after completing a task. Extracts facts, patterns, and observations from the current work and stores them in /knowledge/ domain folders. Promotes hypotheses to rules at 5+ confirmations, demotes rules when contradicted. Use when finishing a task, or when the user says done/finished/complete.
---

# Memory Save

Extract insights from the completed task and persist them to the knowledge system.

## Steps

### 1. Reflect on the Task

Ask yourself:
- What did I learn about the codebase that wasn't already in knowledge.md?
- Did any hypothesis get confirmed or contradicted?
- Did I discover a new pattern that should be tracked?
- Did I make a mistake that revealed a rule worth recording?

### 2. Identify the Domain

Match insights to existing domain folders in `/knowledge/INDEX.md`. If no folder fits, create a new domain folder with all three files (knowledge.md, hypotheses.md, rules.md) and add it to INDEX.md.

### 3. Categorize and Write

For each insight, decide where it goes:

**knowledge.md** — Confirmed facts and patterns
```
- **[topic]**: [fact] (observed [date])
```

**hypotheses.md** — Patterns that need more data
```
- **[topic]**: [hypothesis] (confirmations: N/5, first observed [date])
```

**rules.md** — Confirmed 5+ times, apply by default
```
- **[topic]**: [rule] (promoted [date], confirmed N times)
```

### 4. Promotion Check

Scan `hypotheses.md` in relevant domains. If any hypothesis now has 5+ confirmations:
- Remove it from hypotheses.md
- Add it to rules.md with `(promoted [today's date], confirmed N times)`
- Tell the user: "Promoted to rule: [hypothesis]"

### 5. Demotion Check

If today's work contradicted an existing rule in rules.md:
- Remove it from rules.md
- Add it back to hypotheses.md with `(demoted [today's date], contradicted by: [reason])`
- Reset confirmation count to 0
- Tell the user: "Demoted rule: [rule] — contradicted by [reason]"

### 6. Summary

Report what was saved:
```
Saved to [domain]:
  + N new knowledge entries
  + N new hypotheses
  ^ N hypothesis confirmations
  * N promotions to rules
  v N demotions from rules
```

## Ask Before Saving

Before writing, briefly list what you plan to save and ask: "Save these insights?" This gives the user a chance to add, remove, or correct entries before they're persisted.

## Writing Good Entries

- Be specific and actionable — future sessions need to understand and apply these
- Include dates so staleness can be assessed
- One insight per bullet point
- Don't duplicate existing entries — update them instead
- Skip things derivable from reading the code — focus on non-obvious patterns
~~~

### 4. Create the Knowledge Directory

Create the `knowledge/` directory in the project root with an `INDEX.md` file:

```markdown
# Knowledge Index

Routes to each domain folder. Each folder contains `knowledge.md` (facts), `hypotheses.md` (need more data), and `rules.md` (confirmed, apply by default).

| Domain | Path | Description |
|--------|------|-------------|
```

### 5. Create Initial Domain Folders

Ask the user what domains are relevant to their project. Common examples:
- `api` — backend patterns, routing, error handling
- `auth` — authentication, authorization, tokens
- `frontend` — UI framework, state management, styling
- `database` — schema patterns, query optimization, migrations
- `infrastructure` — caching, queues, storage, deployment
- `testing` — test patterns, fixtures, coverage strategies

For each domain, create a folder under `knowledge/` with three files:

**knowledge.md:**
```markdown
# [Domain] Knowledge

Facts and confirmed patterns.

(No entries yet. Run `/mem-save` after completing tasks to populate.)
```

**hypotheses.md:**
```markdown
# [Domain] Hypotheses

Patterns that need more data to confirm.

(No hypotheses yet.)
```

**rules.md:**
```markdown
# [Domain] Rules

Confirmed patterns — apply by default.

(No rules promoted yet. Hypotheses need 5+ confirmations to become rules.)
```

Add each domain to `knowledge/INDEX.md`.

### 6. Add Project Instructions

Append this section to the project's instruction file (`CLAUDE.md`, `.cursorrules`, or equivalent). Create the file if it doesn't exist:

```markdown
## Domain Knowledge System

Before starting any task, run `/mem-load` to load relevant domain rules and hypotheses from `/knowledge/`. Rules are confirmed patterns — apply them by default.

When a task is complete (user says "done", "finished", "complete", or work is wrapped up), ask: "Want me to save insights from this task? (`/mem-save`)" — then run `/mem-save` if they agree.
```

### 7. Confirm Setup

Tell the user:
```
Knowledge system installed:
  /mem-load — auto-loads domain rules at task start
  /mem-save — extracts and saves insights when tasks complete
  /knowledge/ — domain folders created: [list domains]

Start working! Rules accumulate automatically as you use /mem-save.
Hypotheses promote to rules after 5 confirmations.
```

## How the System Works

```
Task Start                          Task End
    |                                   |
    v                                   v
/mem-load                          /mem-save
    |                                   |
    v                                   v
Read INDEX.md                   Extract insights
    |                                   |
    v                                   v
Find relevant domains           Categorize each:
    |                               - fact -> knowledge.md
    v                               - needs data -> hypotheses.md
Load rules.md                      - confirmed 5x -> rules.md
(apply by default)                      |
    |                                   v
    v                           Promotion/demotion check
Load hypotheses.md                      |
(watch for today)                       v
    |                           Update INDEX.md if new domain
    v
Brief user
```

## Promotion and Demotion

The system self-corrects over time:

- **Promote**: When a hypothesis reaches 5+ confirmations across different tasks, `/mem-save` moves it from `hypotheses.md` to `rules.md`. Future sessions apply it automatically via `/mem-load`.
- **Demote**: When new evidence contradicts a rule, `/mem-save` moves it back to `hypotheses.md` with a reset count and the contradiction reason. It must re-earn its 5 confirmations.

This prevents stale rules from persisting — the knowledge base stays accurate as the codebase evolves.

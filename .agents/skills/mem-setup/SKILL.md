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
  .claude/commands/
    mem-load.md          # Auto-loads rules before each task
    mem-save.md          # Extracts insights after each task
  knowledge/
    INDEX.md             # Routes to domain folders
    <domain>/
      knowledge.md       # Facts and patterns
      hypotheses.md      # Need more data
      rules.md           # Confirmed - apply by default
```

## Setup Steps

### 1. Create the Commands

Read `references/mem-load-template.md` and write its contents to `.claude/commands/mem-load.md` in the project root.

Read `references/mem-save-template.md` and write its contents to `.claude/commands/mem-save.md` in the project root.

### 2. Create the Knowledge Directory

Create the `knowledge/` directory in the project root with an `INDEX.md` file:

```markdown
# Knowledge Index

Routes to each domain folder. Each folder contains `knowledge.md` (facts), `hypotheses.md` (need more data), and `rules.md` (confirmed, apply by default).

| Domain | Path | Description |
|--------|------|-------------|
```

### 3. Create Initial Domain Folders

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

### 4. Add CLAUDE.md Instructions

Append this section to the project's `CLAUDE.md` (create the file if it doesn't exist):

```markdown
## Domain Knowledge System

Before starting any task, run `/mem-load` to load relevant domain rules and hypotheses from `/knowledge/`. Rules are confirmed patterns — apply them by default.

When a task is complete (user says "done", "finished", "complete", or work is wrapped up), ask: "Want me to save insights from this task? (`/mem-save`)" — then run `/mem-save` if they agree.
```

### 5. Confirm Setup

Tell the user:
```
Knowledge system installed:
  /mem-load — auto-loads domain rules at task start (via CLAUDE.md)
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

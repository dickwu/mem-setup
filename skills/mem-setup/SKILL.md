---
name: mem-setup
description: Set up a domain knowledge management system. Creates /knowledge/ directory with domain folders for tracking facts, hypotheses, and confirmed rules across sessions. Use when starting a new project, setting up knowledge management, or when the user mentions "mem-setup", "knowledge system", "domain rules", or "project memory".
---

# Memory Setup

Install a persistent domain knowledge system for any project. This creates a `/knowledge/` directory that accumulates expertise across sessions.

The system tracks three types of insights:
- **Knowledge** — confirmed facts and patterns
- **Hypotheses** — patterns that need more data (tracked with confirmation counts)
- **Rules** — hypotheses confirmed 5+ times, applied by default

## Steps

### 1. Check for Existing Setup

Check if `knowledge/` directory already exists in the project root.
- If it exists: tell the user "Knowledge system already set up. Use `/mem-load` and `/mem-save` to work with it." and stop.
- If not: continue with setup.

### 2. Ask for Domains

Ask the user which knowledge domains are relevant to their project. Suggest common examples:
- `api` — backend patterns, routing, error handling
- `auth` — authentication, authorization, tokens
- `frontend` — UI framework, state management, styling
- `database` — schema patterns, query optimization, migrations
- `infrastructure` — caching, queues, storage, deployment
- `testing` — test patterns, fixtures, coverage strategies

Let them pick from suggestions or add custom domains.

### 3. Create Knowledge Directory

Create `knowledge/INDEX.md`:

```markdown
# Knowledge Index

Routes to each domain folder. Each folder contains `knowledge.md` (facts), `hypotheses.md` (need more data), and `rules.md` (confirmed, apply by default).

| Domain | Path | Description |
|--------|------|-------------|
```

Add a row for each domain the user chose.

### 4. Create Domain Folders

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

### 5. Add Project Instructions

Detect the project's config file by checking in order: `CLAUDE.md`, `.cursorrules`, `.codex/AGENTS.md`. Write to the first one found, or create `CLAUDE.md` as default.

Append this section:

```markdown
## Domain Knowledge System

Before starting any task, run `/mem-load` to load relevant domain rules and hypotheses from `/knowledge/`. Rules are confirmed patterns — apply them by default.

When a task is complete — user says "done", "finished", "complete", or your own output contains summary keywords like "Complete", "Summary", "Total" — ask: "Want me to save insights from this task? (`/mem-save`)" — then run `/mem-save` if they agree.
```

### 6. Confirm Setup

Tell the user:

```
Knowledge system installed:
  /mem-load — loads domain rules at task start
  /mem-save — extracts and saves insights when tasks complete
  /knowledge/ — domain folders created: [list domains]

Start working! Rules accumulate automatically as you use /mem-save.
Hypotheses promote to rules after 5 confirmations.
```

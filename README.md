# mem-setup

A Claude Code skill that installs a self-improving domain knowledge system for any project.

## What It Does

`mem-setup` installs three skills that help Claude learn from your codebase over time. It tracks facts, hypotheses, and confirmed rules — so patterns discovered in one session are automatically applied in future sessions.

### Three Skills

| Skill | When | What |
|-------|------|------|
| `/mem-setup` | Once per project | Creates `/knowledge/` directory and domain folders |
| `/mem-load` | Task start | Loads relevant domain rules and hypotheses |
| `/mem-save` | Task end | Extracts insights and saves to knowledge files |

### Three Types of Knowledge

| Type | File | Purpose |
|------|------|---------|
| **Knowledge** | `knowledge.md` | Confirmed facts and patterns |
| **Hypotheses** | `hypotheses.md` | Patterns needing more data (tracked with confirmation counts) |
| **Rules** | `rules.md` | Hypotheses confirmed 5+ times — applied by default |

## Install

```bash
npx skills add dickwu/mem-setup
```

Works with Claude Code, Cursor, Codex, and any agent supporting the skills convention. The skills CLI handles agent detection automatically.

Installing gives you all three skills. Run `/mem-setup` once in your AI coding agent to create the `/knowledge/` directory and add auto-load instructions to your project config.

## How It Works

```
Session 1: You fix a bug
  -> /mem-save extracts: "API always returns {code: 1, message, data}" (hypothesis, 1/5)

Session 2: You build a feature
  -> /mem-load loads the hypothesis, Claude watches for confirmation
  -> /mem-save confirms it (2/5)

Session 5: Pattern confirmed 5 times
  -> /mem-save promotes it to a rule
  -> All future sessions apply it automatically via /mem-load
```

### Self-Correcting

Rules aren't permanent. If new evidence contradicts a rule, `/mem-save` demotes it back to a hypothesis with a reset count. It must re-earn 5 confirmations. This prevents stale rules from persisting as your codebase evolves.

## Directory Structure

**This repo:**

```
mem-setup/
  skills/
    mem-setup/SKILL.md    # Project initialization skill
    mem-load/SKILL.md     # Domain knowledge loader
    mem-save/SKILL.md     # Insight extractor and saver
```

**Your project after running `/mem-setup`:**

```
your-project/
  knowledge/
    INDEX.md              # Routes to domain folders
    api/                  # Example domain
      knowledge.md
      hypotheses.md
      rules.md
    frontend/             # Another domain
      knowledge.md
      hypotheses.md
      rules.md
  CLAUDE.md               # Updated with auto-load instructions
```

## Usage

### Initial Setup

```
> /mem-setup
```

The skill asks which domains are relevant to your project and creates the folder structure.

### During Work

Claude automatically runs `/mem-load` at task start (via CLAUDE.md instructions). When you finish a task, Claude asks if you want to save insights.

### Manual Use

```
> /mem-load          # Reload knowledge (e.g., after switching domains)
> /mem-save          # Save insights from current work
```

## Example Knowledge Entries

**knowledge.md:**
```markdown
- **API response format**: All endpoints return {code: 1, message: 'OK', data: [...]} (observed 2026-03-29)
- **Auth tokens**: Branca encryption, not standard JWT. Cached 7 days in Redis (observed 2026-03-29)
```

**hypotheses.md:**
```markdown
- **Staff mirrors admin**: Staff controllers duplicate admin functionality — reusing endpoints may reduce duplication (confirmations: 2/5, first observed 2026-03-29)
```

**rules.md:**
```markdown
- **Never use Schema::dropIfExists()**: Migrations run against production via tool.sh (promoted 2026-04-15, confirmed 7 times)
```

## License

MIT

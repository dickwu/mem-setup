---
name: mem-save
description: Save domain knowledge insights after completing a task. Extracts facts, patterns, and observations from the current work and stores them in /knowledge/ domain folders. Promotes hypotheses to rules at 5+ confirmations, demotes rules when contradicted. Use when finishing a task, or when the user says done/finished/complete. Also triggers when you've discovered something interesting about the codebase that future sessions should know.
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

### 6. Update Hypothesis Counts

If today's work confirmed an existing hypothesis (but it's not yet at 5):
- Increment its confirmation count
- Tell the user: "Confirmed: [hypothesis] (now N/5)"

### 7. Summary

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

- Be specific and actionable — future Claude sessions need to understand and apply these
- Include dates so staleness can be assessed
- One insight per bullet point
- Don't duplicate existing entries — update them instead
- Skip things derivable from reading the code — focus on non-obvious patterns and gotchas

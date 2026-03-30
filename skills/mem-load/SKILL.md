---
name: mem-load
description: Auto-load domain knowledge at task start. Reads /knowledge/INDEX.md, finds relevant domain folders for the current task, loads rules.md (apply by default) and hypotheses.md (check if testable). Invoke manually when switching domains mid-conversation or when working in a domain you haven't loaded yet.
---

# Memory Load

Load relevant domain knowledge before starting work. This ensures confirmed rules are applied and testable hypotheses are tracked.

## Steps

1. **Read the index** — read `knowledge/INDEX.md` to see all available domains
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

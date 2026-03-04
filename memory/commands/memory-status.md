---
description: Show memory health — section sizes, staleness, cleanup suggestions
allowed-tools: [Read, Glob, Grep]
---

Audit ~/.claude/CLAUDE.md and report on its health.

## Steps

1. Read ~/.claude/CLAUDE.md
2. For each section, count the number of entries (bullet points)
3. Count total lines

## Report format

```
## Memory Health Report

**Total lines:** N / 80 target
**Sections:**
- Vocabulary: N entries
- Preferences: N entries
- Toolbox: N entries
- Patterns: N entries
- Communication: N entries
- Environment: N entries

**Issues found:**
- [list any problems]
```

## Issues to flag

- **Bloated sections:** Any section with more than 15 entries — suggest pruning
- **Missing sections:** Sections from the template that don't exist
- **Wrong section:** Entries that appear to be in the wrong section
- **Project-specific leakage:** Entries that look project-specific and should be in a project CLAUDE.md
- **Device-specific leakage:** Hardware, device-local paths
- **Over budget:** Total lines exceeding 80 — suggest what to trim
- **Structural issues:** Missing header, wrong section order, malformed entries
- **Stale entries:** Entries that reference things that may no longer be true (use your judgment based on conversation context)

End with a suggested action if there are issues (e.g., "Run /forget hardware to clean up device-specific entries").

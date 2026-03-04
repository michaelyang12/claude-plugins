---
description: Remove specific memories from ~/.claude/CLAUDE.md
allowed-tools: [Read, Edit]
argument: topic - What to forget (keyword, section name, or specific entry)
---

Remove memories matching the user's request from ~/.claude/CLAUDE.md.

## Steps

1. Read ~/.claude/CLAUDE.md
2. Identify entries matching "$ARGUMENTS" — match broadly:
   - If it's a section name (e.g., "environment"), offer to clear that section
   - If it's a keyword (e.g., "hardware", "turso"), find all entries containing it
   - If it's a quoted phrase, match exactly
3. Show the user what you found and will remove. List each entry.
4. Remove the matching entries using the Edit tool.
5. If removing entries leaves an empty section, remove the section header too.
6. Summarize what was removed.

If no matches are found, say so and suggest related entries they might have meant.

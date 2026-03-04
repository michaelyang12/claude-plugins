---
description: Search memories across global and project CLAUDE.md files
allowed-tools: [Read, Grep, Glob]
argument: query - What to search for
---

Search across all memory sources to find relevant context for "$ARGUMENTS".

## Memory sources to search (in order):

1. **Global memory:** ~/.claude/CLAUDE.md
2. **Project CLAUDE.md files:** Glob for ~/Source/*/CLAUDE.md and ~/Source/*/.claude/CLAUDE.md

## Steps

1. Read ~/.claude/CLAUDE.md and search for entries matching "$ARGUMENTS"
2. Use Grep to search ~/Source/ for matching content in CLAUDE.md files: `Grep pattern="$ARGUMENTS" path=~/Source/ glob="**/CLAUDE.md"`
3. For each match found:
   - Show the matching entry/section
   - Show which file it came from
   - Group results by source file
4. If the query is broad (e.g., "rust", "auth"), summarize findings across sources rather than dumping raw matches.
5. If nothing is found, say so and suggest what might be searched differently.

## Output format

```
## Global Memory (~/.claude/CLAUDE.md)
- matching entry 1
- matching entry 2

## Project: <name> (<path to CLAUDE.md>)
- matching entry
```

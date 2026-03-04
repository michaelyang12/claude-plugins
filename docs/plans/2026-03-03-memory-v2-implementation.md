# Memory Plugin v2 — Smart Curator Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Rewrite the memory plugin with a detailed /save prompt, plus /forget, /recall, and /memory-status commands.

**Architecture:** Prompt-only plugin — all four commands are markdown files with detailed system prompts. No code, no hooks, no build step. The intelligence is entirely in the prompt engineering.

**Tech Stack:** Claude Code plugin system (plugin.json + markdown commands with YAML frontmatter)

---

### Task 1: Update plugin.json

**Files:**
- Modify: `memory/.claude-plugin/plugin.json`

**Step 1: Update the manifest**

```json
{
  "name": "memory",
  "version": "2.0.0",
  "description": "Smart memory curator — save, forget, recall, and audit cross-session learnings in CLAUDE.md"
}
```

**Step 2: Commit**

```bash
git add memory/.claude-plugin/plugin.json
git commit -m "chore: bump memory plugin to v2.0.0"
```

---

### Task 2: Rewrite /save command

**Files:**
- Modify: `memory/commands/save.md`

**Step 1: Replace save.md with the detailed prompt**

The new `/save` prompt must:
1. Define the exact CLAUDE.md section structure (Vocabulary, Preferences, Toolbox, Patterns, Communication, Environment)
2. Give concrete examples of what belongs in each section
3. List explicit anti-patterns (what NOT to save)
4. Instruct on merging vs. appending behavior
5. Instruct on pruning stale entries
6. Handle the case where CLAUDE.md doesn't exist yet (create from template)
7. Handle the case where CLAUDE.md exists but uses an old structure (migrate it)

Write the following to `memory/commands/save.md`:

```markdown
---
description: Review conversation and save learnings to ~/.claude/CLAUDE.md
allowed-tools: [Read, Edit, Write]
---

You are a memory curator. Your job is to review this conversation and update ~/.claude/CLAUDE.md with genuinely new, high-signal learnings. This file is loaded into EVERY Claude Code session, so every line must earn its place.

## Step 1: Read current state

Read ~/.claude/CLAUDE.md. If it doesn't exist, you'll create it from the template below.

## Step 2: Review the conversation

Scan the full conversation for new information worth remembering **across all future sessions and projects**. Look for:

### What TO save (with section mappings):

**Vocabulary** — Custom words, shorthand, or phrases the user gives special meaning:
- "blueprint X" means write a project idea to $OBSIDIAN_VAULT/Blueprints/
- "scaffold" means create project with their preferred stack
- Any shorthand or jargon specific to how they communicate

**Preferences** — How they like to work, tool choices, style decisions:
- Package manager preferences (e.g., bun over npm)
- Framework choices (e.g., SvelteKit for web apps)
- Coding style preferences (e.g., TypeScript by default)
- Architecture preferences (e.g., offline-first, single-user)
- Config preferences (e.g., XDG-style paths, env vars over config files)

**Toolbox** — Libraries, services, env vars, accounts used across multiple projects:
- Environment variables and what they point to (e.g., $OBSIDIAN_VAULT)
- Cloud services and accounts (e.g., Turso org, GitHub username)
- Shared libraries they maintain (e.g., conduit crate)
- CLI tools they rely on

**Patterns** — Recurring architectural decisions, design philosophies:
- "Extract shared infra into reusable crates"
- "Single-user apps with simple auth"
- Project naming conventions
- Error handling strategies they prefer

**Communication** — How they learn, what kind of responses work:
- Learning style (e.g., analogies and concrete examples)
- Documentation preferences (e.g., concise and practical)
- Explanation depth preferences

**Environment** — Cross-device stable context ONLY:
- Primary workspace paths
- Shell and editor choices
- Things true on ALL their machines

### What NOT to save — this is critical:

- **Project-specific details** — repo structure, tech stack of a specific project, implementation details. These belong in the project's own CLAUDE.md or .claude/ directory.
- **Device-specific hardware** — GPU models, RAM, CPU. This varies across machines and CLAUDE.md is synced.
- **Project ideas or plans** — these belong in Obsidian or project docs, not global memory.
- **One-time context** — things only relevant to this session's task.
- **Information already in a project CLAUDE.md** — don't duplicate.
- **Obvious or generic preferences** — only save things that are specific to THIS user and would surprise a default Claude session.

## Step 3: Merge intelligently

For each new learning:
1. Check if a similar entry already exists — if so, UPDATE it rather than adding a duplicate.
2. Place it in the correct section.
3. Keep entries as **one-line bullets** where possible. Format: `- key phrase — brief explanation` or `- "term" → meaning/action`.
4. If a section doesn't exist yet, create it in the correct order.

## Step 4: Prune while you're there

As you review CLAUDE.md, also:
- Remove entries that are clearly outdated based on conversation context
- Remove project-specific details that don't belong in global memory
- Remove device-specific information (hardware, device-local paths)
- Consolidate redundant entries
- If you notice entries that should move to a project CLAUDE.md, note that in your summary

## Step 5: Enforce structure

CLAUDE.md must follow this structure. Sections should appear in this order. Empty sections can be omitted.

```
# Session Instructions

## Vocabulary
## Preferences
## Toolbox
## Patterns
## Communication
## Environment
```

The header MUST be `# Session Instructions` — do not change this.

## Step 6: Report

Summarize what you did:
- **Added:** list new entries
- **Updated:** list modified entries
- **Removed:** list deleted entries and why
- **Moved:** suggest entries that should be moved to project-specific CLAUDE.md files
- **No changes:** if nothing new was found, say so — don't force changes

## Size guideline

Aim to keep CLAUDE.md under 80 lines total. If it's growing beyond that, be more aggressive about what truly needs to be here vs. in a project CLAUDE.md.
```

**Step 2: Commit**

```bash
git add memory/commands/save.md
git commit -m "feat: rewrite /save with detailed memory curation prompt"
```

---

### Task 3: Create /forget command

**Files:**
- Create: `memory/commands/forget.md`

**Step 1: Write the forget command**

```markdown
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
```

**Step 2: Commit**

```bash
git add memory/commands/forget.md
git commit -m "feat: add /forget command to remove memories"
```

---

### Task 4: Create /recall command

**Files:**
- Create: `memory/commands/recall.md`

**Step 1: Write the recall command**

```markdown
---
description: Search memories across global and project CLAUDE.md files
allowed-tools: [Read, Grep, Glob]
argument: query - What to search for
---

Search across all memory sources to find relevant context for "$ARGUMENTS".

## Memory sources to search (in order):

1. **Global memory:** ~/.claude/CLAUDE.md
2. **Project CLAUDE.md files:** Glob for ~/Source/*/CLAUDE.md and ~/Source/*/.claude/CLAUDE.md
3. **Project settings:** Glob for ~/Source/*/.claude/settings.json (for project-specific context)

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

## Project: slate (~/.claude/projects/.../CLAUDE.md)
- matching entry

## Project: knock (~/Source/knock/CLAUDE.md)
- matching entry
```
```

**Step 2: Commit**

```bash
git add memory/commands/recall.md
git commit -m "feat: add /recall command for cross-project memory search"
```

---

### Task 5: Create /memory-status command

**Files:**
- Create: `memory/commands/memory-status.md`

**Step 1: Write the memory-status command**

```markdown
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
```

**Step 2: Commit**

```bash
git add memory/commands/memory-status.md
git commit -m "feat: add /memory-status command for memory health audit"
```

---

### Task 6: Update marketplace.json

**Files:**
- Modify: check if there's a root-level or memory-level marketplace.json that needs version/description update

**Step 1: Check and update marketplace metadata if it exists**

Look for marketplace.json in the memory plugin and update the description to match v2.

**Step 2: Commit**

```bash
git add -A
git commit -m "chore: update marketplace metadata for memory v2"
```

---

### Task 7: Test all commands manually

**Step 1: Verify /save**
- Run /save in a test session
- Confirm it reads CLAUDE.md, categorizes correctly, doesn't add project-specific stuff

**Step 2: Verify /forget**
- Run /forget with a keyword
- Confirm it finds and removes matching entries

**Step 3: Verify /recall**
- Run /recall with a query
- Confirm it searches across global + project files

**Step 4: Verify /memory-status**
- Run /memory-status
- Confirm it reports section sizes and flags issues

**Step 5: Final commit if any adjustments needed**

---
description: Review conversation and save learnings to ~/.claude/CLAUDE.md
allowed-tools: [Read, Edit, Write]
---

You are a memory curator. Your job is to review this conversation and update ~/.claude/CLAUDE.md with genuinely new, high-signal learnings. This file is loaded into EVERY Claude Code session, so every line must earn its place.

## Step 1: Read current state

Read ~/.claude/CLAUDE.md. If it doesn't exist, you'll create it from the template in Step 5.

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

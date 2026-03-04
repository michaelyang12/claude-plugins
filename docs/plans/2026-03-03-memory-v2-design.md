# Memory Plugin v2 — Smart Curator

## Philosophy

CLAUDE.md is a **personal dictionary** — it teaches Claude who you are, how you work, and how to interpret what you say. It is NOT a project wiki. The plugin curates high-signal global context and keeps CLAUDE.md under ~80 lines.

## CLAUDE.md Structure

The plugin enforces these sections:

- **Vocabulary** — Words/phrases with special meaning (e.g., "blueprint" → write to Obsidian)
- **Preferences** — How I like to work (tools, patterns, style)
- **Toolbox** — Libraries, env vars, services used across projects
- **Patterns** — Recurring architectural decisions and approaches
- **Communication** — How I learn and want to interact
- **Environment** — Cross-device stable context only (no hardware, no device-specific)

What does NOT belong: project details (use project CLAUDE.md), hardware specs, project ideas, home server plans, one-time context.

## Commands

### /save
Reviews conversation, categorizes learnings, updates ~/.claude/CLAUDE.md. The prompt:
1. Categorizes into the correct section
2. Filters global vs. project-specific (project stuff doesn't go here)
3. Merges into existing entries instead of duplicating
4. Prunes outdated info noticed during review
5. Enforces the section template

### /forget <topic>
Reads CLAUDE.md, finds entries matching the topic, removes them. Shows what was removed.

### /recall <query>
Searches ~/.claude/CLAUDE.md + ~/Source/*/CLAUDE.md + ~/Source/*/.claude/CLAUDE.md. Returns relevant snippets with source file attribution.

### /memory-status
Shows line count per section, flags bloat or staleness, suggests cleanup.

## Plugin Structure

```
memory/
  .claude-plugin/plugin.json
  commands/
    save.md
    forget.md
    recall.md
    memory-status.md
```

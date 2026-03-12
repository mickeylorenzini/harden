# Harden Marketplace Distribution — Design Spec

## Problem

Harden is an open-source agent skill (SKILL.md) that works with Claude Code, Codex CLI, Gemini CLI, and any LLM chat interface — but discovery requires knowing the GitHub repo exists. No CLI install command or marketplace listing currently surfaces harden to potential users.

## Goal

Make harden auto-discoverable on Tier 1 agent skill platforms (those requiring no manual submission) with minimal repo changes.

## Scope

### In Scope

- SKILL.md frontmatter: add `license: MIT`
- GitHub topics: add `skill-md`, `skillsmp` to existing tags
- README.md: rewrite install section with CLI methods, manual paths for coding agents and chat interfaces, usage instructions
- Post-push verification on 4 Tier 1 platforms
- Close issues #9 (Phase 1) and #10

### Out of Scope (Deferred)

- SKILL.md body changes
- Tier 2 PR submissions (awesome lists, Claude Code Templates)
- Tier 3 web submissions (LobeHub, Anthropic Plugin Directory)
- Full README rewrite (issue #8 — remaining scope: verification criteria docs, eval harness, anti-patterns, repo structure)
- `.claude-plugin/` infrastructure

## Approach

**Verify-first (Approach B):** Research each platform's current requirements before making changes, confirm planned changes satisfy them, then execute and verify.

## Changes

### 1. SKILL.md Frontmatter

Add `license: MIT` after `compatibility` field. No body changes.

```yaml
---
name: harden
description: >
  [existing — unchanged]
compatibility: >
  [existing — unchanged]
license: MIT
---
```

### 2. GitHub Topics

Add 2 tags via GitHub API:

| Current | Adding |
|---------|--------|
| `agent-skills`, `claude-code`, `claude-skills`, `codex-cli`, `gemini-cli`, `document-auditing`, `output-quality`, `quality-assurance` | `skill-md`, `skillsmp` |

### 3. README.md Install Section

Replace current install section with:

```markdown
## Install

### CLI (recommended)
```
npx skills add mickeylorenzini/harden
skillkit install mickeylorenzini/harden
```

### Manual — coding agents
Copy SKILL.md to your agent's skills directory.
Common paths:
- Claude Code: ~/.claude/skills/harden/SKILL.md
- Codex CLI: ~/.codex/skills/harden/SKILL.md
- Gemini CLI: ~/.gemini/skills/harden/SKILL.md

### Manual — chat interfaces
- claude.ai / Claude Desktop: Download this repo as ZIP → Settings → Skills → Upload
- Other chat apps: Upload SKILL.md to your conversation or install via custom instructions in settings

Works with any LLM that can follow structured instructions.

### Usage
Activates automatically on substantial deliverables.
In CLI tools, trigger manually with `/harden`.
In chat interfaces, say "harden this" or "audit this before I finalize."
```

## Verification Plan

### Pre-implementation: Platform Requirements Research

Web fetch current docs for each platform to confirm requirements haven't changed since handoff doc was written.

### Post-push: Auto-discovery Verification

| Platform | Verification method | Success criteria |
|----------|-------------------|------------------|
| Vercel Skills CLI | `npx skills add mickeylorenzini/harden --list` | Shows "harden" as available skill |
| SkillKit | `skillkit install mickeylorenzini/harden --dry-run` | Recognizes valid skill |
| SkillsMP | Check skillsmp.com for harden listing | Appears in directory (may require scrape cycle) |
| SkillHub | Check skillhub.club for harden listing | Appears in directory (may require scrape cycle) |

For scraped platforms (SkillsMP, SkillHub): if not immediately visible, document "changes pushed, awaiting next index" and verify in follow-up session.

## Execution Order

1. Research — verify platform requirements via web fetch
2. Apply changes — SKILL.md frontmatter, GitHub topics, README install section
3. Push — commit and push to main
4. Verify — run CLI verification commands, check web directories

## Issues

- Closes #9 (Phase 1 only — update issue body to note Phase 2 deferred)
- Closes #10

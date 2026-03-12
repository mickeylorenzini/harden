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
- Update issue #9 (check off Phase 1 items); close #10

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

### Pre-implementation Research

1. **Platform requirements** — Web fetch current docs for Vercel Skills CLI, SkillKit, SkillsMP, and SkillHub to confirm frontmatter requirements haven't changed.
2. **Install paths** — Verify Codex CLI and Gemini CLI skill directory paths via their docs. If paths cannot be confirmed, qualify README text with "check your agent's docs for the exact path" rather than presenting unverified paths as definitive. Claude Code path (`~/.claude/skills/`) is confirmed.
3. **CLI verification flags** — Confirm `npx skills add --list` and `skillkit install --dry-run` flags exist. If not, fall back to installing in a temp directory and verifying the skill file is downloaded correctly.
4. **Claude.ai upload flow** — Verify current skill upload navigation path. If it cannot be confirmed, use generic language ("Upload via your Claude settings").
5. **License field format** — Confirm platforms expect `MIT` (SPDX identifier) vs other formats.

### Post-push: Auto-discovery Verification

| Platform | Verification method | Success criteria |
|----------|-------------------|------------------|
| Vercel Skills CLI | `npx skills add mickeylorenzini/harden --list` (or install to temp dir if flag doesn't exist) | Shows "harden" as available skill / file downloaded correctly |
| SkillKit | `skillkit install mickeylorenzini/harden --dry-run` (or install to temp dir if flag doesn't exist) | Recognizes valid skill / file downloaded correctly |
| SkillsMP | Check skillsmp.com for harden listing | Appears in directory (may require scrape cycle) |
| SkillHub | Check skillhub.club for harden listing | Appears in directory (may require scrape cycle) |

For scraped platforms (SkillsMP, SkillHub): if not immediately visible, document "changes pushed, awaiting next index" and verify in follow-up session.

## Execution Order

1. Research — verify platform requirements via web fetch
2. Apply changes — SKILL.md frontmatter, GitHub topics, README install section
3. Push — commit and push to main
4. Verify — run CLI verification commands, check web directories

## Issues

- #9: Do not close — check off Phase 1 items in issue body, leave open for Phase 2
- Closes #10

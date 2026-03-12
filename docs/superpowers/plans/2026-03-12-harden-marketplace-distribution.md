# Harden Marketplace Distribution Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make harden auto-discoverable on Tier 1 agent skill marketplaces (Vercel Skills CLI, SkillKit, SkillsMP, SkillHub) with minimal repo changes.

**Architecture:** Three file-level changes (SKILL.md frontmatter, GitHub topics, README install section) preceded by platform requirements research and followed by post-push verification. No new files created. No dependencies to install.

**Tech Stack:** `gh` CLI for GitHub API, `npx skills` for Vercel Skills CLI verification, web browser for scraped platform checks.

**Spec:** `docs/superpowers/specs/2026-03-12-harden-marketplace-distribution-design.md`

---

## Chunk 1: Research and Apply Changes

### Task 1: Pre-implementation Platform Research

**Files:** None (research only)

This task validates assumptions before any changes are made. All findings feed into Tasks 2-4.

**Note:** Steps 1-4 use agent web search/fetch tools, not bash commands. Use your platform's WebSearch and WebFetch tools (or equivalent).

- [ ] **Step 1: Verify Codex CLI skill directory path**

Use WebSearch: `"Codex CLI skills directory path ~/.codex/skills 2026"`

Expected: Confirm `~/.codex/skills/` is the correct path, or identify the actual path.
If unconfirmed: README will use qualified language ("check your agent's docs for the exact path").

- [ ] **Step 2: Verify Gemini CLI skill directory path**

Use WebSearch: `"Gemini CLI skills directory path agent skills install 2026"`

Expected: Confirm `~/.gemini/skills/` is the correct path, or identify the actual path.
If unconfirmed: README will use qualified language.

- [ ] **Step 3: Verify claude.ai skill upload flow**

Use WebSearch: `"claude.ai upload skills settings 2026"`

Expected: Confirm navigation path (Settings → Capabilities → Add Skills, or current equivalent).
If unconfirmed: README will use generic language ("Upload via your Claude settings").

- [ ] **Step 4: Verify SkillKit and Vercel Skills CLI flags**

Use WebFetch on `https://github.com/rohitg00/skillkit` — check what CLI flags skillkit supports for install (e.g., `--dry-run`, `--list`).

Also run `npx skills add --help` to confirm available flags for Vercel Skills CLI.

Expected: Document available flags for verification in Task 5.

License field format: Confirmed during brainstorming — `MIT` (SPDX short identifier) is consistent with [agentskills.io spec](https://agentskills.io/specification) convention. No further research needed.

- [ ] **Step 6: Document research findings**

Write a brief summary of findings from steps 1-5 as a comment in issue #9 on GitHub. This creates a record of what was verified.

Run:
```bash
gh issue comment 9 --repo mickeylorenzini/harden --body "Phase 1 research findings:
- Codex CLI path: [confirmed/unconfirmed — actual path]
- Gemini CLI path: [confirmed/unconfirmed — actual path]
- claude.ai upload flow: [confirmed/unconfirmed — current nav path]
- SkillKit flags: [available flags]
- License format: MIT (SPDX) confirmed per agentskills.io spec"
```

---

### Task 2: Add `license` field to SKILL.md frontmatter

**Files:**
- Modify: `SKILL.md:1-18` (frontmatter only)

**Constraint:** Do NOT modify anything below line 18 (the closing `---`). Only the YAML frontmatter changes.

- [ ] **Step 1: Add license field**

Add `license: MIT` after the `compatibility` field in `SKILL.md`. The frontmatter should become:

```yaml
---
name: harden
description: >
  Audits any substantial deliverable — documents, grant sections,
  strategies, protocols, skills, configs, roadmaps, plans — against
  domain-specific quality criteria before delivery. Searches the web
  for best practices, runs dual-pass thematic and structural audit with
  severity classification, fixes all issues, and iterates until
  convergence. Activates when producing deliverables exceeding 4
  paragraphs or 20 lines, or when the user says "harden", "audit",
  "stress test", or "review before delivering", or when re-auditing
  a modified deliverable for regressions. Does not activate for
  conversational responses, brainstorming, web search summaries, or
  rough drafts.
compatibility: >
  claude.ai, Claude Code, Codex CLI, Gemini CLI (requires web search
  for research step; degrades gracefully without it)
license: MIT
---
```

- [ ] **Step 2: Validate YAML frontmatter parses correctly**

Run:
```bash
head -20 SKILL.md | grep -E '^(name|description|compatibility|license):'
```
Expected: All four fields appear: `name`, `description`, `compatibility`, `license`.

If `pyyaml` is available, deeper validation:
```bash
head -20 SKILL.md | python3 -c "import sys, yaml; d=yaml.safe_load(sys.stdin); assert all(k in d for k in ['name','description','compatibility','license']), f'Missing keys: {set([\"name\",\"description\",\"compatibility\",\"license\"]) - set(d.keys())}'; print('Valid:', list(d.keys()))"
```

- [ ] **Step 3: Verify body content unchanged**

Run:
```bash
git diff SKILL.md
```
Expected: Only lines added are `license: MIT` and the closing `---` moved down by one line. No changes to content after the frontmatter.

- [ ] **Step 4: Commit**

```bash
git add SKILL.md
git commit -m "Add license field to SKILL.md frontmatter for marketplace compatibility"
```

---

### Task 3: Add GitHub topic tags

**Files:** None (GitHub API only)

- [ ] **Step 1: Verify current topics**

Run:
```bash
gh api repos/mickeylorenzini/harden --jq '.topics'
```
Expected: `["agent-skills","claude-code","claude-skills","codex-cli","document-auditing","gemini-cli","output-quality","quality-assurance"]`

- [ ] **Step 2: Add `skill-md` and `skillsmp` topics**

Run:
```bash
gh api repos/mickeylorenzini/harden/topics -X PUT -f "names[]=agent-skills" -f "names[]=claude-code" -f "names[]=claude-skills" -f "names[]=codex-cli" -f "names[]=document-auditing" -f "names[]=gemini-cli" -f "names[]=output-quality" -f "names[]=quality-assurance" -f "names[]=skill-md" -f "names[]=skillsmp"
```
Expected: Response includes all 10 topics.

- [ ] **Step 3: Verify topics applied**

Run:
```bash
gh api repos/mickeylorenzini/harden --jq '.topics'
```
Expected: Array includes `skill-md` and `skillsmp` alongside the 8 existing topics.

---

### Task 4: Rewrite README.md install section

**Files:**
- Modify: `README.md:23-34` (install section)

This task uses findings from Task 1 to write accurate install paths. If any paths were unconfirmed, use qualified language.

- [ ] **Step 1: Replace the install section**

Replace lines 23-34 of `README.md` (from `## Install` through the usage note) with the following content. The structure uses these headings and content blocks:

    ## Install

    ### CLI (recommended)

    (code block with these two commands)
    npx skills add mickeylorenzini/harden
    skillkit install mickeylorenzini/harden

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

**Note:** Adjust Codex CLI and Gemini CLI paths based on Task 1 findings. If a path was unconfirmed, change the line to e.g., `- Codex CLI: check [Codex docs](https://developers.openai.com/codex/skills/) for your skills directory`. Adjust claude.ai upload nav path if Task 1 found a different flow.

- [ ] **Step 2: Verify markdown renders correctly**

Visually inspect that:
- Code block fences are properly closed
- Bullet lists render
- No broken formatting

Run:
```bash
cat README.md
```
Expected: Well-formatted markdown with all sections intact. Content above `## Install` and below `## License` should be unchanged.

- [ ] **Step 3: Verify no content outside install section changed**

Run:
```bash
git diff README.md
```
Expected: Changes only between the `## Install` heading and the `## License` heading. No changes to "What it does", "What makes it different", "How it works", License, or Author sections.

- [ ] **Step 4: Commit**

```bash
git add README.md
git commit -m "Update README install section with CLI and chat interface methods

Adds npx skills, skillkit install, and chat interface upload
instructions. Closes #10."
```

---

## Chunk 2: Push and Verify

### Task 5: Push to main and verify auto-discovery

**Files:** None (git and verification only)

**Depends on:** Tasks 2, 3, 4 all completed.

- [ ] **Step 1: Push to main**

Run:
```bash
git pull --rebase origin main && git push origin HEAD:main
```
Expected: Clean push, no conflicts.

- [ ] **Step 2: Verify Vercel Skills CLI**

Run:
```bash
npx skills add mickeylorenzini/harden --list
```
Expected: Output lists "harden" as an available skill in the repo.

If `--list` flag doesn't work, first check available flags with `npx skills add --help`, then fall back to a temp dir install:
```bash
TMPDIR=$(mktemp -d) && cd "$TMPDIR" && npx skills add mickeylorenzini/harden && find . -name SKILL.md
```
Expected: `SKILL.md` file found in the temp directory.

- [ ] **Step 3: Verify SkillKit**

Run (use flags identified in Task 1, step 4):
```bash
skillkit install mickeylorenzini/harden
```
If skillkit is not installed locally, verify by checking the GitHub repo README confirms the install command format is compatible.

Fallback (use isolated environment to avoid polluting system Python):
```bash
TMPDIR=$(mktemp -d) && python3 -m venv "$TMPDIR/venv" && "$TMPDIR/venv/bin/pip" install skillkit && "$TMPDIR/venv/bin/skillkit" install mickeylorenzini/harden && find "$TMPDIR" -name SKILL.md
```
Expected: Skill files downloaded correctly.

- [ ] **Step 4: Check SkillsMP**

Run:
```bash
web_fetch "https://skillsmp.com" "Search for 'harden' or 'mickeylorenzini/harden'. Is it listed? If not, check if there's a submission or indexing delay notice."
```
Expected: Either harden appears in the directory, or the page indicates auto-indexing happens on a schedule. If not listed yet, document "awaiting next scrape cycle" — repo meets all requirements (2 stars, SKILL.md with frontmatter, topic tags).

- [ ] **Step 5: Check SkillHub**

Run:
```bash
web_fetch "https://www.skillhub.club" "Search for 'harden' or 'mickeylorenzini/harden'. Is it listed?"
```
Expected: Same as SkillsMP — either listed or awaiting next index cycle.

If WebFetch fails for either SkillsMP or SkillHub (site down, blocked, changed URL), document "platform unreachable, will re-check in follow-up session" and proceed.

**Proceed to Steps 6-8 regardless of verification results.** Platform indexing delays do not block Phase 1 completion — the repo changes are the deliverable; discovery is confirmation.

- [ ] **Step 6: Document verification results**

Post results as a comment on issue #9:
```bash
gh issue comment 9 --repo mickeylorenzini/harden --body "Phase 1 verification results:
- [x] SKILL.md frontmatter: license field added
- [x] GitHub topics: skill-md, skillsmp added
- [x] README: install section updated with CLI + chat methods
- [ ] Vercel Skills CLI: [result]
- [ ] SkillKit: [result]
- [ ] SkillsMP: [result — listed or awaiting scrape]
- [ ] SkillHub: [result — listed or awaiting scrape]"
```

- [ ] **Step 7: Close issue #10**

Run:
```bash
gh issue close 10 --repo mickeylorenzini/harden --comment "README install section updated with CLI and chat interface methods."
```

- [ ] **Step 8: Update issue #9 body**

Edit issue #9 to check off Phase 1 items. Leave issue open for Phase 2.

Run:
```bash
gh issue edit 9 --repo mickeylorenzini/harden --body "$(gh issue view 9 --repo mickeylorenzini/harden --json body --jq .body | sed 's/- \[ \] SKILL.md has valid YAML frontmatter/- [x] SKILL.md has valid YAML frontmatter/' | sed 's/- \[ \] GitHub repo topics include/- [x] GitHub repo topics include/' | sed 's/- \[ \] README includes multi-method/- [x] README includes multi-method/')"
```
Expected: Phase 1 acceptance criteria checked off. Issue remains open for Phase 2.

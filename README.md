# harden

[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A general-purpose output auditing skill for Claude, Codex, Gemini CLI, and other agents supporting the Agent Skills standard.

## What it does

Before you deliver a document, grant section, protocol, strategy, roadmap, or any substantial artifact — harden audits it against domain-specific best practices, classifies issues by severity, fixes them, and re-audits until only cosmetic issues remain.

## What makes it different

Existing quality skills are code-focused ([verification-before-completion](https://github.com/obra/superpowers)), single-concern ([clarity-gate](https://github.com/frmoretto/clarity-gate)), or domain-locked ([skill-review](https://github.com/secondsky/skill-review)). harden is the only skill combining web-based research, dual-pass auditing (ideas + structure), severity-classified fix execution, and iterative convergence — and it works on any deliverable type, not just code.

## How it works

1. **Research** — Searches the web for domain-specific best practices before auditing
2. **Dual-pass audit** — Checks both ideas (are they sound?) and structure (do paths/references/formatting work?)
3. **Severity classification** — 🔴 Critical / 🟡 Significant / 🟢 Minor with specific locations
4. **Fix** — Implements all critical and significant fixes (not just reports)
5. **Converge** — Re-audits until only minor issues remain (max 3 passes)

## Install

### claude.ai
Download this repo as ZIP → upload via the Skills section in Settings.

### Claude Code
Copy the `harden` folder (containing `SKILL.md`) to `.claude/skills/` in your project, or `~/.claude/skills/` for global access.

### Codex CLI / Gemini CLI / Others
Copy `SKILL.md` to your agent's skills directory (e.g., `~/.codex/skills/harden/`).

## License

[MIT](LICENSE)

## Author

Built by Michael Lorenzini — Staff scientist, Salk Institute for Biological Studies.

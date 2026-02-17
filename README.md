# harden

[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A general-purpose output auditing skill for Claude, Codex, Gemini CLI, and other agents supporting the Agent Skills standard.

## What it does

Before you deliver a complex document, analysis report, grant section, protocol, roadmap, strategy, or any substantial artifact — harden audits it against domain-specific best practices, classifies issues by severity, fixes them, and re-audits until only cosmetic issues remain.

## What makes it different

Existing quality skills are code-focused ([verification-before-completion](https://github.com/obra/superpowers)) or single-concern ([clarity-gate](https://github.com/frmoretto/clarity-gate)). harden is the only skill combining web-based research, dual-pass auditing (ideas + structure), severity-classified fix execution, and iterative convergence — and it works on any deliverable type, not just code.

## How it works

1. **Research** — Searches the web for domain-specific best practices before auditing
2. **Dual-pass audit** — Checks both ideas (are they sound?) and structure (do paths/references/formatting work?)
3. **Severity classification** — 🔴 Critical / 🟡 Significant / 🟢 Minor with specific locations
4. **Fix** — Implements all critical and significant fixes (not just reports)
5. **Converge** — Re-audits until only minor issues remain (max 3 passes)

## Install

### claude.ai
On the repo page, click the green **Code** button → **Download ZIP** → In Claude, go to **Settings → Skills** → upload the ZIP file.

### Claude Code
Copy the `harden` folder (containing `SKILL.md`) to `.claude/skills/` in your project, or `~/.claude/skills/` for global access.

### Codex CLI / Gemini CLI / Others
Copy `SKILL.md` to your agent's skills directory (e.g., `~/.codex/skills/harden/`).

Once installed, harden activates automatically on substantial deliverables. You can also say "harden" to trigger it manually.

## License

[MIT](LICENSE)

## Author

Built by Mickey Lorenzini — Staff scientist, Salk Institute for Biological Studies.

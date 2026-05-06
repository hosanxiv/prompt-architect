# Changelog

All notable changes to this project are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [1.0.1] — 2026-05-06

### Fixed

- Restructured the repo as a proper **plugin marketplace** (`.claude-plugin/marketplace.json`) so it actually installs in Claude Cowork. The v1.0.0 layout was a single-plugin folder, which Cowork rejected with `no manifest found at .claude-plugin/marketplace.json`. The new structure follows Anthropic's marketplace spec:
  - `.claude-plugin/marketplace.json` at repo root (the marketplace catalog).
  - `plugins/prompt-architect/` (the plugin folder) containing `.claude-plugin/plugin.json` and the `skills/` tree.
- Install path is now: **Cowork → Customize → "+" → Add marketplace → paste the GitHub URL**. Or in Claude Code: `claude plugin marketplace add subsub19444/prompt-architect && claude plugin install prompt-architect@the-ai-burrow`.

### Changed

- README and INSTALL.md rewritten with step-by-step UI walkthrough for Cowork users (the previous version assumed a CLI flow that didn't match how most users install).

## [1.0.0] — 2026-05-06

### Added

- Initial public release.
- Interview-style prompt builder using the 10-block framework from Anthropic's *Prompting 101* workshop (Code w/ Claude, May 2025).
- Cowork plugin manifest (`.claude-plugin/plugin.json`) — registers `/prompt-architect` in the slash menu.
- 6 task-type templates in `skills/prompt-architect/templates/`:
  - `_generic.md` — fallback template for mixed / unsure tasks
  - `extraction.md` — structured data extraction from text or images
  - `classification.md` — label assignment from a fixed set
  - `generation.md` — content writing with brand voice
  - `analysis.md` — multi-step reasoning and recommendations
  - `agent.md` — tool-use loops with halt conditions
- 5 reference docs in `skills/prompt-architect/references/`:
  - `framework.md` — full 10-block walk-through with examples per block
  - `checklist.md` — pre-flight checklist before shipping a prompt
  - `xml-patterns.md` — XML tag conventions
  - `anti-patterns.md` — 10 common prompt mistakes and the fix for each
  - `prefill-and-thinking.md` — when to use prefill vs. extended thinking
- Worked example: `skills/prompt-architect/examples/before-after-insurance.md` showing the workshop's insurance-claim case at four prompt-quality levels (v0 → v4).
- `INSTALL.md` covering Claude Cowork (desktop), Claude Code (CLI), and manual install.
- MIT license.

### Notes

- Compatible with both Claude Cowork (desktop app) and Claude Code (CLI). Cowork registers the slash command via the plugin manifest; Claude Code reads from `~/.claude/plugins/`.
- Skill is interview-style: triggers ask 4 multiple-choice questions before generating the prompt. In Cowork, questions render as clickable cards; in Claude Code, they render as plain Q&A.

[1.0.0]: https://github.com/subsub19444/prompt-architect/releases/tag/v1.0.0

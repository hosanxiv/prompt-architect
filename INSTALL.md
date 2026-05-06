# Install Guide

This repo is a Claude **plugin marketplace** (`.claude-plugin/marketplace.json` spec). It works in both Claude Code (CLI) and Claude Cowork (desktop app, currently a research preview) via the built-in `/plugin` commands.

---

## Recommended: marketplace install (Claude Code + Cowork)

In your Claude chat, run:

```
/plugin marketplace add subsub19444/prompt-architect
/plugin install prompt-architect@the-ai-burrow
```

Restart Claude. Type `/prompt-architect` — it appears in the slash dropdown.

**What those two commands do:**

1. `/plugin marketplace add subsub19444/prompt-architect` — registers this GitHub repo as a marketplace. Anthropic's `/plugin` system supports the `owner/repo` shorthand for GitHub. The marketplace name comes from `marketplace.json` and is `the-ai-burrow`.
2. `/plugin install prompt-architect@the-ai-burrow` — installs the `prompt-architect` plugin from that marketplace. The `@the-ai-burrow` suffix disambiguates if multiple marketplaces have plugins with the same name.

---

## Claude Code (CLI) — non-interactive equivalents

If you'd rather run shell commands than slash commands:

```bash
claude plugin marketplace add subsub19444/prompt-architect
claude plugin install prompt-architect@the-ai-burrow
```

---

## Pin to a specific version (optional)

To pin to v1.0.0 (the initial release):

```
/plugin marketplace add subsub19444/prompt-architect@v1.0.0
/plugin install prompt-architect@the-ai-burrow
```

The `@v1.0.0` is a Git ref. Without it, the marketplace tracks `main` and you get updates automatically.

---

## Updating

```
/plugin marketplace update the-ai-burrow
```

Pulls the latest version of `marketplace.json` and any plugin changes. Auto-update also runs at Claude launch.

---

## Verify

```
/plugin marketplace list
/plugin list
```

You should see `the-ai-burrow` in the first list and `prompt-architect@the-ai-burrow` in the second.

If `prompt-architect` doesn't appear in the slash dropdown after install:

- Restart Claude fully (Cmd+Q on macOS, not just window close).
- Confirm `/plugin list` shows it as installed.
- Run `claude plugin validate .` from a clone of this repo — checks that `marketplace.json` and `plugin.json` are well-formed.

---

## Uninstall

```
/plugin uninstall prompt-architect@the-ai-burrow
/plugin marketplace remove the-ai-burrow
```

The first removes the plugin. The second deregisters the marketplace (skip if you want to keep it for future plugins).

---

## Manual install (no marketplace, fallback only)

If for some reason the marketplace flow doesn't work in your environment, you can also drop just the skill folder into Claude Code's user-skills directory:

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/subsub19444/prompt-architect.git /tmp/prompt-architect
cp -R /tmp/prompt-architect/plugins/prompt-architect/skills/prompt-architect ~/.claude/skills/
rm -rf /tmp/prompt-architect
```

This works only in **Claude Code**, not Cowork. Cowork doesn't read `~/.claude/skills/` — it only loads plugins through the marketplace system.

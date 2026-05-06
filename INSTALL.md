# Install Guide

This is a Claude plugin (with a `.claude-plugin/plugin.json` manifest). It registers in the slash menu of both Claude Cowork and Claude Code.

---

## Claude Cowork (desktop app)

In a Cowork chat, ask:

```
install the prompt-architect plugin from https://github.com/YOUR_USERNAME/prompt-architect
```

Cowork's plugin tooling will fetch the repo, validate the manifest, and register it. After a restart you should see `prompt-architect` in the slash dropdown when you type `/`.

---

## Claude Code (CLI)

```bash
mkdir -p ~/.claude/plugins
git clone https://github.com/YOUR_USERNAME/prompt-architect.git ~/.claude/plugins/prompt-architect
```

Restart your `claude` session. Type `/prompt-architect`.

---

## Manual / no Git

1. Download the repo ZIP from GitHub.
2. Unzip it.
3. Rename the unzipped folder to exactly `prompt-architect` (drop any `-main` suffix).
4. Move it to `~/.claude/plugins/`.
5. Restart Claude (full quit + reopen, not just window-close).

---

## Verify

Type `/` in your Claude chat. `prompt-architect` should appear in the dropdown.

If it doesn't appear:

- Folder is named exactly `prompt-architect`? (no `-main`, no version suffix)
- `~/.claude/plugins/prompt-architect/.claude-plugin/plugin.json` exists?
- `~/.claude/plugins/prompt-architect/skills/prompt-architect/SKILL.md` exists?
- Did you fully quit Claude (Cmd+Q on macOS) and reopen?
- For Cowork, the plugin tooling may take a moment to register — check again after 30 seconds.

---

## Uninstall

```bash
rm -rf ~/.claude/plugins/prompt-architect
```

That's it.

# Install Guide

This is the long-form install guide. The README has the short version — start there if you want quick install steps.

This guide covers:

- [Path A — Claude Cowork (desktop app)](#path-a--claude-cowork-desktop-app)
- [Path B — Claude Code (CLI)](#path-b--claude-code-cli)
- [Updating](#updating)
- [Uninstalling](#uninstalling)
- [Troubleshooting](#troubleshooting)
- [What's actually happening under the hood](#whats-actually-happening-under-the-hood)

---

## Path A — Claude Cowork (desktop app)

This is the path most people want. No Terminal, no command line — pure point-and-click.

### Requirements

- Claude desktop app installed.
- A paid Claude plan (Pro, Max, Team, or Enterprise) — Cowork plugins require a paid plan, per Anthropic's docs.
- Internet connection (Cowork fetches the plugin from GitHub the first time).

### Steps

1. **Open the Claude desktop app.** Switch to the **Cowork** tab if you're not already there.

2. **Click "Customize" in the left sidebar.** This opens a panel showing your plugins, skills, and connectors all in one place.

3. **Find the "Personal plugins" section** in the left column. There's a small **"+"** button at the top of that section.

4. **Click the "+" button.** A small menu appears with options. Choose **Create plugin → Add marketplace**.

5. **A dialog opens with a URL field.** It says:
   > A GitHub `owner/repo` or a git repository URL.

6. **Paste this exact URL** into the field:

   ```
   https://github.com/subsub19444/prompt-architect
   ```

7. **Click "Sync".**

8. Cowork fetches the marketplace and shows you a confirmation. The marketplace is called **the-ai-burrow** (named for The AI Burrow — future plugins from us will live in the same marketplace).

9. **Install the plugin from the marketplace.** Click into the-ai-burrow → click **Install** on the **prompt-architect** plugin.

10. **Restart Cowork.** Quit fully (Cmd+Q on Mac, not just window-close), then reopen the app.

### Verify it works

Open a new Cowork chat. Type `/` in the message box. You should see **prompt-architect** in the dropdown of available skills.

If it's there, install succeeded. To use it, either:

- Click `prompt-architect` from the dropdown, OR
- Just type a natural-language request like *"build me a prompt for X"* and the skill will trigger automatically.

The skill is interview-style — it'll ask you 4 multiple-choice questions, then hand you back a structured prompt + a test plan.

---

## Path B — Claude Code (CLI)

For users who prefer Terminal. Faster install, identical functionality.

### Requirements

- `claude` CLI installed (this is the Claude Code command-line tool).
- Internet connection.

### Steps

In your Terminal:

```bash
claude plugin marketplace add subsub19444/prompt-architect
claude plugin install prompt-architect@the-ai-burrow
```

That's it. Two commands. Restart your `claude` session and type `/prompt-architect`.

### What the commands do

- `marketplace add` registers `subsub19444/prompt-architect` (a GitHub repo) as a plugin marketplace called **the-ai-burrow**.
- `install ... @the-ai-burrow` installs the **prompt-architect** plugin from that marketplace.

### Verify

```bash
claude plugin marketplace list   # should show the-ai-burrow
claude plugin list               # should show prompt-architect@the-ai-burrow
```

---

## Updating

The plugin gets updates when we publish a new version on GitHub. Here's how to refresh:

### Cowork

- **Auto-update:** by default, Cowork pulls updates on launch. Just restart the app every now and then.
- **Manual:** Customize → Personal plugins → click **the-ai-burrow** → click **Update**.

### Claude Code

```bash
claude plugin marketplace update the-ai-burrow
```

This pulls the latest from GitHub and updates the plugin if there's a new version.

---

## Uninstalling

### Cowork

Customize → Personal plugins → click **prompt-architect** → **Remove**.

To also remove the marketplace:

Customize → Personal plugins → click the **the-ai-burrow** entry → **Remove marketplace**.

### Claude Code

```bash
claude plugin uninstall prompt-architect@the-ai-burrow

# Optional: also remove the marketplace
claude plugin marketplace remove the-ai-burrow
```

---

## Troubleshooting

### "Failed to add marketplace" (Cowork)

Most common cause: you pasted a local file path instead of a GitHub URL. Cowork's "Add marketplace" dialog only accepts:
- A GitHub `owner/repo` shorthand (e.g. `subsub19444/prompt-architect`)
- A full Git URL (e.g. `https://github.com/subsub19444/prompt-architect`)

Use the full GitHub URL. Local file paths are not supported in this dialog.

### "This repository isn't a marketplace — no manifest found at .claude-plugin/marketplace.json"

This means the repo you pointed at doesn't have a marketplace catalog file. If you got this error using the URL above, the repo might be in a temporary broken state — refresh and try again. If it persists, [open an issue](https://github.com/subsub19444/prompt-architect/issues).

### Plugin installs but `/prompt-architect` doesn't appear in the slash menu

1. **Fully quit Claude.** Cmd+Q on macOS — not just close the window. Window-close keeps the app running in the background and Cowork won't re-scan plugins.
2. Reopen Claude, type `/` in a chat, and look again.
3. If still missing, run (in Terminal): `claude plugin list` — does the plugin appear there? If yes, the install worked, the slash menu just hasn't refreshed.

### Two `/prompt-architect` entries in Claude Code

You probably have a stray standalone skill from an earlier install attempt. Check:

```bash
ls ~/.claude/skills/
```

If you see a `prompt-architect` folder there, delete it:

```bash
rm -rf ~/.claude/skills/prompt-architect
```

The plugin-managed install (via `claude plugin install`) is the one to keep.

### `claude plugin validate` says "Unrecognized keys"

You're running an older Claude Code version that doesn't understand newer marketplace fields. Either upgrade Claude Code, or open the file the validator complains about and remove the offending keys (the README's troubleshooting section lists which ones can move to `metadata`).

---

## What's actually happening under the hood

A "plugin marketplace" is a GitHub repo with a specific layout that Cowork and Claude Code understand:

```
your-repo/
├── .claude-plugin/
│   └── marketplace.json     ← lists which plugins live in this marketplace
└── plugins/
    └── plugin-name/
        ├── .claude-plugin/
        │   └── plugin.json  ← plugin manifest
        └── skills/
            └── skill-name/
                └── SKILL.md ← the actual skill content
```

When you "add" the marketplace via URL, Cowork:

1. Clones the repo (read-only, sparse).
2. Reads `.claude-plugin/marketplace.json` to see what plugins are available.
3. Caches the marketplace in `~/.claude/plugins/marketplaces/<marketplace-name>/`.

When you install a plugin:

1. Cowork copies the plugin files from the marketplace cache to a separate plugin cache (`~/.claude/plugins/cache/...` for Claude Code, equivalent path for Cowork).
2. The plugin's skills become available in the slash menu.
3. The skill auto-triggers based on its description (matching keywords) or runs explicitly via the slash command.

When the marketplace is updated (you push a new version to GitHub), Cowork's auto-update fetches the latest copy and replaces the cached version on next launch.

This is why GitHub URLs are the canonical install path — the version control gives Cowork a clean way to fetch updates and pin to specific versions if needed.

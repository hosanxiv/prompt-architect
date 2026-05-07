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

Everything happens inside the **Plugins directory**. You'll add the marketplace and install the plugin from the same view, then restart to activate.

---

1. **Open the Claude desktop app.** Switch to the **Cowork** tab.

2. **Get to Customize.** Two ways depending on your version:

   **If you see "Customize" in the left sidebar:** click it.

   **If you don't:** click **Settings → Connectors**. You'll see a banner *"Connectors have moved to Customize."* — click the underlined **Customize** link.

3. **In Customize**, find the **Personal plugins** section in the left column. Click the small **"+"** button next to it.

4. **A menu pops up.** Click **Browse plugins**. (Don't pick "Create plugin" — that's a different flow.)

5. **The Plugins directory opens.** At the top you'll see two tabs: **Anthropic & Partners** and **Personal**. Click the **Personal** tab.

6. **Scroll to the "Local uploads" section.** Click the small **"+"** button next to the "Local uploads" header.

7. **The "Add marketplace" dialog opens.** Paste this URL:
   ```
   https://github.com/subsub19444/prompt-architect
   ```

8. **Click Sync.**

   ✅ **What success looks like:** the dialog closes. Cowork pulls the marketplace catalog from GitHub.

   ❌ *"Failed to add marketplace"* → check the URL is exact (no trailing spaces). Local file paths don't work in this dialog.

   ❌ *"This repository isn't a marketplace"* → the GitHub repo is missing `.claude-plugin/marketplace.json`. Refresh and try again. If it persists, [open an issue](https://github.com/subsub19444/prompt-architect/issues).

9. **Back in the Local uploads section**, you'll now see **prompt-architect** listed with a small **"+"** button next to it.

   ⚠️ **You must click this "+" to actually install the plugin.** Sync (step 8) only registers the catalog — it doesn't install the plugin from the catalog. This is the single step where most install attempts fail.

   ✅ **What success looks like:** after clicking "+", the button changes to a checkmark or disappears. The plugin is now installed.

10. **Quit Claude fully.** Press **Cmd+Q** on Mac. Closing the window is not enough — Cowork keeps running in the background and won't refresh the slash menu.

11. **Reopen Claude.** Open a new chat.

12. **Type `/` in the message box.**

   ✅ **What success looks like:** `prompt-architect` appears in the dropdown of available skills. Install is complete.

To use it, either click `prompt-architect` from the dropdown, or just type a request like *"build me a prompt for X"* — the skill auto-triggers based on its description matching your request.

The skill is interview-style — it asks 4 multiple-choice questions, then hands you back a structured prompt + a test plan.

---

### Why so many steps?

Cowork's plugin model separates **marketplaces** (catalogs of plugins) from **plugins** (the things you actually install). When you click Sync, you're saying *"I trust this catalog"*. When you click "+" on a specific plugin, you're saying *"install this one from the catalog"*. When you restart, Cowork registers the new plugin in the slash menu.

For a single-plugin marketplace like this one, the multiple steps feel redundant — but the same flow is used for everything in Cowork (official Anthropic plugins, third-party, your own custom ones). Once you've done it once, it's muscle memory.

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

### "I synced the marketplace, the dialog closed, but I don't see prompt-architect anywhere"

**You stopped at Stage 1.** This is the most common Cowork install issue. Sync only registers the marketplace catalog — it doesn't install the plugin from it.

To finish the install:

1. In Customize, click **Plugins** in the left sidebar (not Skills, not Connectors — Plugins).
2. Click the **Personal** tab at the top.
3. Look under **Local uploads** — you'll see `prompt-architect` with a `+` next to it.
4. Click the `+` to install.
5. Quit Claude fully (Cmd+Q on Mac) and reopen.
6. Type `/` in any chat → `prompt-architect` should appear.

This is Stage 2 + Stage 3 in the install steps above.

### "I clicked the `+`, but `/` still doesn't show prompt-architect"

You skipped the restart. Cowork only refreshes its slash menu on app startup, not on plugin install.

1. Press **Cmd+Q** on Mac to fully quit Claude. **Closing the window is not enough** — the app keeps running in the background.
2. Reopen Claude.
3. Type `/` in any chat — `prompt-architect` should now appear.

### "I can't find Customize in the left sidebar"

Cowork moved Customize behind Settings in some versions. To get there:

1. Open **Settings** (gear icon or via the Settings menu).
2. Click **Connectors**.
3. You'll see a message: *"Connectors have moved to Customize."* — click the underlined **Customize** in that message.
4. Now you're in the Customize panel and can follow Stage 1 from step 3.

### "Failed to add marketplace" (when clicking Sync)

Most common cause: you pasted a local file path instead of a GitHub URL. Cowork's "Add marketplace" dialog only accepts:

- A GitHub `owner/repo` shorthand (e.g. `subsub19444/prompt-architect`)
- A full Git URL (e.g. `https://github.com/subsub19444/prompt-architect`)

Use the full GitHub URL. Local file paths are not supported in this dialog.

### "This repository isn't a marketplace — no manifest found at .claude-plugin/marketplace.json"

The repo you pointed at doesn't have a marketplace catalog file at the right location. If you got this error using the URL `https://github.com/subsub19444/prompt-architect`, the repo might be in a temporary broken state — refresh the dialog and try again. If it persists, [open an issue](https://github.com/subsub19444/prompt-architect/issues).

### Two `/prompt-architect` entries in Claude Code (CLI)

You have a stray standalone skill from an earlier install attempt creating a duplicate. Check:

```bash
ls ~/.claude/skills/
```

If you see a `prompt-architect` folder there, delete it:

```bash
rm -rf ~/.claude/skills/prompt-architect
```

The plugin-managed install (via `claude plugin install`) is the one to keep. After deleting the duplicate, restart your `claude` session.

### `claude plugin validate` says "Unrecognized keys"

You're running an older Claude Code version that doesn't recognize newer marketplace fields. Either upgrade Claude Code, or open the file the validator complains about and remove the offending keys (most can move under `metadata` for backward compatibility).

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

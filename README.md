# Prompt Architect

A Claude skill that helps you write better prompts — the kind that work the first time, not the fifth.

Built from Anthropic's [Prompting 101 workshop](https://www.youtube.com/watch?v=ysPbXH0LpIE) (Code w/ Claude, May 2025). Same 10-block structure the Anthropic Applied AI team teaches internally.

---

## What is this, in plain English?

You know how sometimes Claude gives you a great answer, and sometimes it goes off in a weird direction? The difference is almost always the prompt — what you typed in.

This skill is like a writing coach for prompts. You tell it what you want Claude to do (in regular language), and it builds you a properly-structured prompt that works.

You don't need to know anything technical. You just need to:

1. Install the skill (one-time, see [Install](#install) below).
2. Ask Claude something like *"build me a prompt to summarize my weekly emails into a one-page brief"*.
3. The skill will ask 3–4 quick questions, then hand you a polished prompt to copy-paste.

---

## Why does this exist?

Most people write prompts like text messages — short, vague, and hoping for the best. The Anthropic team showed in their workshop that the same task can go from "Claude thinks this is a skiing accident" to "Claude correctly identifies who's at fault" just by structuring the prompt properly.

This skill packages that structure into something you can use without having to memorize it.

---

## What it does

When you ask Claude to write or improve a prompt, this skill:

1. **Asks you a few questions** — what task, what input, what output, what's the worst-case failure.
2. **Picks a template** — extraction, classification, generation, analysis, agent, or generic.
3. **Builds the prompt** using the 10-block framework: role, tone, background, task steps, examples, output format, reminders, and prefill.
4. **Hands you three things**:
   - The prompt itself, ready to copy.
   - A short note explaining each block (so you can tweak it later).
   - A test plan — three example inputs to try, including an edge case.

---

## The 10 blocks (one-liner each)

1. **Task and tone** — who Claude is, what success means.
2. **Background** — static info that doesn't change between runs.
3. **Detailed steps** — the procedure, in order.
4. **Examples** — input → output pairs, especially edge cases.
5. **Conversation history** — prior turns, if relevant.
6. **The actual ask** — the live input for this run.
7. **Think step by step** — explicit reasoning instruction.
8. **Output format** — exact shape of the answer.
9. **Final reminder** — re-state the most important rules at the end.
10. **Prefill** — start Claude's response for it, to lock the format.

Full detail with examples in [`references/framework.md`](references/framework.md).

---

## Install

This repo is a **Claude plugin marketplace** — it follows Anthropic's `.claude-plugin/marketplace.json` spec. Install via the built-in `/plugin` slash commands in either Claude Code or Cowork.

### Two-command install (works for both Claude Code and Cowork)

```
/plugin marketplace add subsub19444/prompt-architect
/plugin install prompt-architect@the-ai-burrow
```

Run those in your Claude chat (Cowork) or your `claude` session (CLI). Restart and type `/prompt-architect` — it'll show up in the slash dropdown.

The first command registers this repo as a marketplace called `the-ai-burrow`. The second installs the `prompt-architect` plugin from it. Marketplace name and plugin name are separate on purpose — future plugins from The AI Burrow will live in the same marketplace.

### CLI shortcut

If you're in Terminal (Claude Code only):

```bash
claude plugin marketplace add subsub19444/prompt-architect
claude plugin install prompt-architect@the-ai-burrow
```

### Updating later

```
/plugin marketplace update the-ai-burrow
```

Pulls latest changes from this repo. Existing installs auto-pick up new versions on next launch.

### Verify it's installed

In your Claude chat, type `/`. `prompt-architect` should appear in the dropdown.

If it doesn't:
- Restart Claude fully (Cmd+Q on macOS, not just window-close).
- Run `/plugin marketplace list` — you should see `the-ai-burrow`.
- Run `/plugin list` — you should see `prompt-architect`.
- If either is missing, the marketplace add likely failed silently. Re-run the commands and check for error messages.

## Repo structure

```
prompt-architect/                       ← repo root = MARKETPLACE
├── .claude-plugin/
│   └── marketplace.json                ← marketplace catalog (lists plugins)
├── plugins/
│   └── prompt-architect/               ← THE PLUGIN
│       ├── .claude-plugin/
│       │   └── plugin.json             ← plugin manifest
│       └── skills/
│           └── prompt-architect/       ← skill name = slash command
│               ├── SKILL.md            ← skill instructions
│               ├── references/         ← deep-dive docs (loaded on demand)
│               ├── templates/          ← task-type templates
│               └── examples/           ← worked before/after
├── README.md
├── INSTALL.md
├── LICENSE
├── CHANGELOG.md
└── .gitignore
```

## Roadmap

- [ ] Templates for RAG, evals, code-generation tasks.
- [ ] A `--quick` mode that drops to 2 questions for fast prompt drafts.
- [ ] More worked before/after examples beyond the insurance case.
- [ ] Localization of the README.

---

## Use

### The canonical trigger: `/prompt-architect`

Type the slash command in your Claude session:

```
/prompt-architect
```

Or describe what you need in plain language — these all trigger the skill too:

> build me a prompt that turns my meeting transcripts into action items

> improve this prompt: [paste your prompt]

> I want Claude to read product reviews and tell me which ones are angry — give me a prompt for that

The skill activates, asks 3–4 multiple-choice questions (rendered as clickable cards in Cowork; plain Q&A in the CLI), then hands you a fully-structured prompt.

### What you'll get back

Roughly this shape:

````
**Your prompt:**

```
SYSTEM:
<role>...</role>
<task>...</task>
...

USER:
<input>
...your real content goes here...
</input>
```

**What each block does:**
- <role> sets up who Claude is (block 1).
- <task> is the step-by-step procedure (block 3).
- ...

**Test it on these 3 inputs first:**
1. A clean example (should work perfectly).
2. An edge case (ambiguous, missing data).
3. A hostile input (junk, off-topic — Claude should refuse to guess).
````

Copy the prompt, paste it where you'd normally chat with Claude, and you're done.

---

## What's in this repo

```
prompt-architect/
├── SKILL.md              ← the skill file Claude reads (the brain)
├── README.md             ← this file
├── LICENSE               ← MIT
├── INSTALL.md            ← detailed install steps
├── references/           ← deep-dive docs the skill loads when needed
│   ├── framework.md      ← all 10 blocks explained
│   ├── checklist.md      ← pre-flight checklist
│   ├── xml-patterns.md   ← XML tag conventions
│   ├── anti-patterns.md  ← common mistakes
│   └── prefill-and-thinking.md
└── templates/            ← starting points by task type
    ├── _generic.md
    ├── extraction.md
    ├── classification.md
    ├── generation.md
    ├── analysis.md
    └── agent.md
```

You can read the `references/` and `templates/` files directly if you want to learn the framework yourself — they're written as docs, not just config.

---

## Contributing

PRs welcome. Especially:

- New templates for task types not covered (RAG, code-gen, eval).
- Real before/after examples.
- Translations of the README.

Style: short, evidence-based, no fluff. Match the tone of the existing files.

---

## Credits

**Author / maintainer**
**Hosan Swee** — Founder, [The AI Burrow](https://theaiburrow.xyz/) · [LinkedIn](https://www.linkedin.com/in/hosanswee/)

The AI Burrow is an applied AI collective based in Singapore — consultancy, workshop studio, and builder community. We help teams move past the hype: documenting what actually works in production, what breaks, and what to do differently. This skill is one of the open-source artifacts that comes out of that work.

**Source material**
Workshop content adapted from **Hannah Moran** and **Christian Ryan**, Applied AI team at Anthropic — ["Prompting 101", Code w/ Claude, May 2025](https://www.youtube.com/watch?v=ysPbXH0LpIE).

This skill packages their framework into a reusable artifact. All conceptual credit to Anthropic; any errors in the repo packaging are mine.

## License

MIT. See [LICENSE](LICENSE).

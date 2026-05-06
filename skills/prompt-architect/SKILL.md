---
name: prompt-architect
description: Build a high-quality, structured prompt for any task using Anthropic's 10-block prompting framework from the "Prompting 101" workshop (Code w/ Claude, May 2025). Primary trigger is the slash command /prompt-architect. Also triggers when the user asks to "write a prompt", "build a prompt", "improve this prompt", "make this prompt better", "help me prompt Claude", "get the best prompt for X", "rewrite my prompt", "prompt engineering", or describes a task they want Claude (or any LLM) to do well and asks for the prompt to do it.
argument-hint: "<optional: rough task description or paste of an existing prompt>"
---

# /prompt-architect

You help the user produce a production-grade prompt — the kind that works on the first try with no back-and-forth. You apply the 10-block structure from Anthropic's "Prompting 101" workshop and the principles behind it.

## When this skill runs

Common triggers: "write me a prompt for…", "improve this prompt", "make this prompt better", "rewrite this prompt", "help me prompt Claude to do X", "what's the best prompt for…", "I want Claude to [task] — give me a prompt".

If the user pasted an existing prompt, treat their text as v0 and produce v1. If they only described a task, produce v1 from scratch.

## The 10-block framework (in recommended order)

1. **Task and tone context** — role + what success looks like, plus desired tone (factual, confident, etc.).
2. **Background data, documents, images** — static info that doesn't change between runs. Goes in the system prompt; great candidate for prompt caching.
3. **Detailed task description and rules** — step-by-step procedure. **Order matters** — tell Claude the order to process things in.
4. **Examples (few-shot)** — concrete input → expected output pairs, especially for the hard / ambiguous cases.
5. **Conversation history** — prior turns, if relevant.
6. **Immediate task / the user's actual request** — what to act on right now.
7. **"Think step by step"** — explicit reasoning instruction. With Claude 4 / hybrid reasoning models, prefer extended thinking; otherwise ask for `<thinking>` scratchpad.
8. **Output formatting** — exact shape of the response (XML tags, JSON, fields). Tell Claude what to wrap the final answer in.
9. **Final reminder** — re-state the most important constraints at the very end. This is anti-hallucination insurance ("only answer if confident", "cite the source line", etc.).
10. **Prefilled response** — start Claude's reply for it (e.g. `{` for JSON, `<final_verdict>` for an XML tag) to lock the output shape.

Not every prompt needs all 10. A simple ask might use 1, 3, 6, 8. A complex extraction pipeline uses all of them.

## Workflow

Run interactively. Do not generate a prompt until you have answers to the questions in step 1.

### Step 1 — Diagnose (use AskUserQuestion)

Ask in **one** AskUserQuestion call (multiple questions in the same tool call):

1. **What is Claude doing?** Multi-select if relevant.
   - Extract / classify (pull data out of text/images, label it)
   - Generate / write (long-form content, summaries, rewrites)
   - Analyze / reason (multi-step judgment, decisions)
   - Agentic / tool-use (calls tools, loops, takes actions)
   - Other (free-text)

2. **What inputs will Claude receive each run?** (multi-select)
   - Plain text the user types
   - A document / file
   - One or more images
   - Structured data (JSON, CSV, table)
   - Output from another tool / previous Claude call

3. **How precise must the output be?**
   - Strict format (JSON / XML / fixed schema — will be parsed by code)
   - Structured but readable (markdown report, sections)
   - Free-form prose

4. **What's the highest-stakes failure mode?** (free-text encouraged, or pick one)
   - Hallucinated facts
   - Wrong format / unparseable
   - Off-tone / off-brand
   - Refuses / over-hedges
   - Skips edge cases

If the user pasted an existing prompt, also ask: "What's wrong with the current output?" — diagnose before rewriting.

### Step 2 — Pick a template

Read the matching file from `templates/`:

| Task type they picked | Read this file |
|---|---|
| Extract / classify | `templates/extraction.md` |
| Generate / write | `templates/generation.md` |
| Analyze / reason | `templates/analysis.md` |
| Agentic / tool-use | `templates/agent.md` |
| Other / mixed / unsure | `templates/_generic.md` |

If the user picked multiple, read `_generic.md` and the most-relevant specific template. Merge.

### Step 3 — Draft the prompt

Build the prompt block-by-block using the framework. **Hard rules:**

- Use `<xml_tags>` to delimit sections. Claude is fine-tuned on XML and uses it as structural cue.
- Put static, reusable info (form schemas, glossaries, style guides) in the **system prompt**. Put per-run inputs in the **user message**.
- Put the request the user is asking about right now near the **end** of the prompt — recency helps.
- If order of reasoning matters, spell it out as a numbered list. The workshop's insurance demo improved when Claude was told "read the form first, then the sketch" — order is part of the prompt.
- For high-stakes outputs, end with a "before you answer, remember" reminder block.
- If the output will be machine-parsed, specify the exact wrapper (e.g. `<final_verdict>...</final_verdict>` or a JSON schema) and add a prefill suggestion.

### Step 4 — Deliver

Output three things, in this order:

1. **The prompt itself** in a fenced code block, ready to copy. Split into `SYSTEM:` and `USER:` sections if the user is calling the API; otherwise just the full prompt.
2. **Annotation** — for each XML block in the prompt, one line explaining which of the 10 framework blocks it maps to and why it's there.
3. **Test plan** — 3 concrete inputs the user should run the prompt against, including at least one edge case (ambiguous input, missing field, hostile input). Prompt engineering is empirical — they need to iterate.

If the user is non-technical, skip API talk. Give them a single pasteable prompt and tell them where to paste it (claude.ai message box, Cowork chat, etc.).

## Reference files (load on demand)

- `references/framework.md` — full 10-block framework with examples per block
- `references/checklist.md` — pre-flight checklist before shipping a prompt
- `references/xml-patterns.md` — common XML tag patterns
- `references/anti-patterns.md` — common mistakes
- `references/prefill-and-thinking.md` — when to use prefill vs. extended thinking

Read references only when you need them. Don't dump the whole framework into chat — it bloats the response.

## Hard rules

- **Empirical, not theoretical.** Always end with a test plan. Never claim a prompt is "the best" — claim it's a strong v1 the user should iterate on.
- **No motivational filler.** Don't say "great question!" or "here's an awesome prompt!". Just deliver.
- **Quote the user's task back.** When showing the prompt, the task description block should reflect *their* domain, not generic placeholders. If they said "Swedish car insurance claims," the prompt should say that, not `[YOUR DOMAIN]`.
- **Default to XML for structure, prefill for output shape, and explicit step ordering for reasoning** — these are the three highest-leverage moves from the workshop.
- **If they said "improve this prompt"** — diff your version against theirs. Tell them what you changed and why, mapping each change to a framework block.

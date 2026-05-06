# Prefill vs. Extended Thinking — when to use which

These are two of the most powerful prompting moves on Claude. They do different things.

## Prefill: control output shape

You start Claude's response for it. Claude continues from where you stopped.

**Use prefill when:**
- The output must be machine-parseable (force JSON: prefill `{`)
- You want to skip preamble ("Sure, here's…")
- You're chaining calls and downstream code expects a specific tag/format
- You want to lock the *first* thing Claude says (`<final_verdict>` forces verdict-first output)

**API pattern:**
```python
messages = [
    {"role": "user", "content": "..."},
    {"role": "assistant", "content": "<final_verdict>"}  # ← prefill
]
```
Claude's response starts with whatever comes after `<final_verdict>`.

**Caveats:**
- Prefill suppresses anything Claude might have said before the prefill point. If you wanted a preamble, don't prefill.
- Don't end the prefill mid-word. End on a clean boundary.
- Don't prefill long strings. A few characters is usually enough to lock format.

---

## Extended thinking: improve reasoning quality

Claude reasons silently before answering. The thinking trace is returned alongside the final answer.

**Use extended thinking when:**
- The task has multi-step logic where one bad inference cascades
- You need to debug *why* Claude got something wrong (read the trace)
- The task benefits from Claude considering options, ruling things out, then deciding
- You're using Claude 3.7 / 4 (the hybrid reasoning models)

**API pattern:**
```python
client.messages.create(
    model="claude-sonnet-4-...",
    thinking={"type": "enabled", "budget_tokens": 10000},
    messages=[...]
)
```

**Cost:** thinking tokens are billed. Budget accordingly. For most tasks, 5–10k thinking tokens is plenty.

**Pro tip from workshop:** read the thinking trace, find what Claude struggles with, bake that intuition into the system prompt for v2. The trace is your debugger.

---

## Combining them

Yes, you can. Extended thinking + prefill is the highest-quality combo for parsable, reasoned output:

1. Extended thinking generates the silent trace.
2. After thinking, Claude starts the visible response — at the prefill point.
3. You get clean reasoning (in the trace) + clean output (forced by prefill).

```python
client.messages.create(
    thinking={"type": "enabled", "budget_tokens": 8000},
    messages=[
        {"role": "user", "content": prompt},
        {"role": "assistant", "content": "<final_verdict>"}
    ]
)
```

---

## When to skip both

- Quick chat, casual response → no extended thinking, no prefill.
- Free-form prose where preamble is fine → no prefill.
- Trivially-easy tasks ("what's the capital of France") → no extended thinking; it's overkill and adds latency.

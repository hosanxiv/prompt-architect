# The 10-Block Framework

Source: Anthropic "Prompting 101" workshop — Hannah Moran & Christian Ryan, Code w/ Claude, May 2025.

A great prompt is built block-by-block. Not every prompt uses every block, but they're presented in the recommended order. Most production prompts use 6–8 of them.

---

## 1. Task and tone context

What is Claude's role and what does success look like? Plus: how should it sound?

**Why it matters:** Without role context, Claude guesses. The workshop's first attempt thought a Swedish car-accident form was about a *skiing accident*. Setting the role ("you assist a claims adjuster reviewing Swedish car-accident forms") instantly fixed the framing.

**Tone:** specify factual, confident, terse, friendly, etc. For high-stakes work, explicitly say "stay factual, stay confident, do not guess."

**Example block:**
```xml
<role>
You are an AI assistant helping a human claims adjuster review Swedish car-accident report forms. Your job is to determine, from the form and an accompanying sketch, which vehicle is at fault.
</role>

<tone>
Factual and confident. If you cannot determine fault with high confidence, say so explicitly — do not guess.
</tone>
```

---

## 2. Background data, documents, images

Static info that doesn't change between runs. Goes in the **system prompt**. Prime candidate for **prompt caching**.

**What to include:**
- Schemas (form layouts, JSON shapes the user works with)
- Glossaries / taxonomies
- Style guides / brand voice
- Reference images (base64-encoded if needed)
- Domain rules ("checkbox 1 means X, checkbox 2 means Y…")

**Why it matters:** In the workshop, once Claude was told the structure of the form (17 rows, 2 columns, what each row meant), it stopped wasting tokens narrating "this looks like a form with checkboxes" and went straight to the answer.

**Example block:**
```xml
<form_schema>
The form has the title "Trafikskadeanmälan" at the top, two columns labeled Vehicle A and Vehicle B, and 17 numbered rows. Each row has a checkbox per column.

Row 1: Standing still
Row 2: Parked / stopped
Row 3: Leaving a parking spot
... (etc.)
</form_schema>

<filling_conventions>
The form is filled out by hand. Marks may be X, ✓, circles, scribbles, or partial marks. Treat any non-trivial mark as "checked".
</filling_conventions>
```

---

## 3. Detailed task description and rules

The actual procedure. **Order matters.** If you want Claude to reason in a specific order, spell it out.

**Why it matters:** Workshop key insight — when analyzing a form + sketch, telling Claude "read the form first, then look at the sketch knowing what you learned" produced sharper judgments than letting Claude pick its own order.

**Pattern:** numbered list of steps. Each step is one verb.

**Example block:**
```xml
<task>
1. First, examine the form. List every checkbox, marking each as checked / unchecked / unclear. Output this list.
2. From the form alone, draft a preliminary description of what each vehicle was doing.
3. Then examine the sketch. Use your form-based understanding as a lens.
4. Reconcile the two: do they agree? If not, flag the conflict.
5. Make a fault determination only if the two sources agree and confidence is high. Otherwise output "INSUFFICIENT_EVIDENCE" with a reason.
</task>
```

---

## 4. Examples (few-shot)

Concrete input → expected output pairs. The single highest-leverage move you can make for a hard task.

**When to use:** ambiguous inputs, edge cases, format-sensitive outputs, anything where you have a "yes-but" exception your rules can't cleanly capture.

**Pattern:** wrap each example in `<example>` tags. Show input and output. For visual tasks, base64-encode the image.

**Anti-pattern:** showing only "happy path" examples. Show edge cases — that's where examples earn their keep.

**Example block:**
```xml
<examples>
  <example>
    <input>
    Form shows boxes 1 (vehicle A) and 12 (vehicle B) checked. Sketch shows A stationary, B reversing into A.
    </input>
    <output>
    <final_verdict>VEHICLE_B_AT_FAULT</final_verdict>
    <reason>Box 1 = A standing still. Box 12 = B reversing. Sketch corroborates.</reason>
    </output>
  </example>

  <example>
    <input>
    Form shows boxes 8 (both) checked — both turning left. Sketch is unclear.
    </input>
    <output>
    <final_verdict>INSUFFICIENT_EVIDENCE</final_verdict>
    <reason>Both vehicles claim the same maneuver. Sketch does not disambiguate. Escalate to human reviewer.</reason>
    </output>
  </example>
</examples>
```

---

## 5. Conversation history

Prior turns of the conversation, if relevant. Mostly applicable to chat / agentic flows.

**When to use:** user-facing chat, multi-turn refinement, agent loops.
**When to skip:** one-shot extraction / classification / generation.

---

## 6. Immediate task / the user's actual request

What to act on **right now**. Goes near the end of the prompt — Claude pays disproportionate attention to recent context.

**Pattern:** wrap the live input in clear tags so it's distinguishable from instructions.

**Example block:**
```xml
<form_image>
[base64-encoded image]
</form_image>

<sketch_image>
[base64-encoded image]
</sketch_image>

<request>
Determine fault for the case above using the procedure in <task>.
</request>
```

---

## 7. Think step by step

Tell Claude to reason explicitly. This visibly improves accuracy on multi-step tasks.

**Three options, ordered by power:**

1. **Extended thinking** (Claude 3.7 / 4 hybrid reasoning models) — turn on `thinking: { type: "enabled", budget_tokens: ... }`. Claude reasons silently, then answers. You can read the thinking trace afterward to debug.
2. **`<thinking>` scratchpad** — instruct Claude to write `<thinking>...</thinking>` before its answer. Good for older models or when you want the trace inline.
3. **Just say "think step by step"** — works, but less powerful than the above.

**Pro tip from workshop:** read the thinking trace, find what Claude struggles with, bake that intuition into the system prompt next iteration. This is the empirical loop.

---

## 8. Output formatting

Exact shape of the response. Non-negotiable for any prompt feeding code.

**Patterns:**
- Wrap the final answer in a tag: `<final_verdict>...</final_verdict>` — easy to regex-extract.
- For JSON, specify the schema: `Output exactly this shape: {"verdict": "...", "confidence": 0.0–1.0, "reason": "..."}`.
- Tell Claude what to do with intermediate reasoning — keep it, hide it, etc.

**Example block:**
```xml
<output_format>
Output your reasoning freely, then end with EXACTLY this structure:

<final_verdict>VEHICLE_A_AT_FAULT | VEHICLE_B_AT_FAULT | INSUFFICIENT_EVIDENCE</final_verdict>
<confidence>HIGH | MEDIUM | LOW</confidence>
<reason>One sentence.</reason>

Nothing after the </reason> tag.
</output_format>
```

---

## 9. Final reminder

Re-state the most important constraints at the very end of the prompt. Anti-hallucination insurance.

**Why it matters:** prompt is long, recency wins. Whatever you put last, Claude weights heavily.

**Standard reminder ingredients:**
- "Only answer if you are confident."
- "Cite the specific input line / field for any factual claim."
- "If unsure, output INSUFFICIENT_EVIDENCE — do not guess."
- "Stay in [language]. Do not switch."
- The output format, repeated.

**Example block:**
```xml
<important>
Reminder before you answer:
- Cite the box number for every factual claim.
- If the form and sketch conflict, output INSUFFICIENT_EVIDENCE.
- End with the exact <final_verdict>...</final_verdict> structure.
</important>
```

---

## 10. Prefilled response

Start Claude's reply for it. Locks the output shape and skips preamble.

**Examples:**
- Prefill `{` → forces JSON output
- Prefill `<final_verdict>` → forces direct verdict, no preamble
- Prefill `Step 1:` → forces step-by-step format
- Prefill `<thinking>` → forces explicit reasoning before answer

**API:** add an `assistant` message with the prefilled text. Claude continues from where you stopped.

**Caveat:** if you prefill, Claude won't add any text before that point. Good for parsable output, bad if you wanted preamble explanation.

---

## Order tradeoffs

The "recommended order" is recommended, not mandatory. Two real-world reorderings:

- **For agents:** put tool definitions early (system) and the immediate task last. Conversation history sits between.
- **For long-context analysis:** put the document at the very end and instructions before it — counterintuitively, this works because you want Claude to read instructions *before* the doc.

When in doubt: **static stuff in system, dynamic stuff in user, user's actual request last.**

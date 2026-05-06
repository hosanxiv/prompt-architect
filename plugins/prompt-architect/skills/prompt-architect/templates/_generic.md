# Template: Generic

Use when the task doesn't cleanly fit extraction / generation / analysis / agent — or when you're unsure. This is the safe default. Fill in the bracketed fields, delete blocks you don't need.

---

## SYSTEM PROMPT

```xml
<role>
You are [WHAT KIND OF ASSISTANT — e.g. "an analyst helping a marketing team review campaign briefs"].
Your job is to [ONE-SENTENCE TASK].
</role>

<tone>
[Pick: factual / friendly / terse / analytical]. If you cannot complete the task with confidence, say so explicitly rather than guessing.
</tone>

<background>
[ANY STATIC INFO THAT WON'T CHANGE BETWEEN RUNS — schemas, glossaries, rules, style guide. If none, delete this block.]
</background>

<task>
1. [STEP 1 — one verb]
2. [STEP 2]
3. [STEP 3]
[Add steps as needed. Order matters — Claude will follow this order.]
</task>

<examples>
  <example>
    <input>[A REPRESENTATIVE INPUT]</input>
    <output>[THE EXPECTED OUTPUT, EXACT FORMAT]</output>
  </example>
  <example>
    <input>[AN EDGE-CASE INPUT — ambiguous, malformed, missing data]</input>
    <output>[HOW CLAUDE SHOULD HANDLE IT — e.g. "INSUFFICIENT_DATA"]</output>
  </example>
</examples>

<output_format>
[EXACTLY HOW THE FINAL ANSWER SHOULD LOOK. Examples:
- "Wrap your final answer in <result>...</result>"
- "Output valid JSON matching this schema: { ... }"
- "Reply in 2 paragraphs, no headings"]
</output_format>

<important>
Reminder before you answer:
- [TOP CONSTRAINT]
- [SECOND CONSTRAINT]
- If unsure: [ESCAPE-HATCH BEHAVIOR — e.g. output "INSUFFICIENT_DATA"]
</important>
```

## USER MESSAGE

```xml
<input>
[THE LIVE INPUT FOR THIS RUN — text, image, document, etc.]
</input>

<request>
[OPTIONAL — restate what you want done with this specific input. Often unnecessary if <task> is clear.]
</request>
```

## OPTIONAL: assistant prefill

If output must be machine-parseable, prefill the first line of the assistant message:

- For JSON output: prefill `{`
- For wrapped output: prefill `<result>`
- For step-by-step: prefill `Step 1:`

---

## How to use this template

1. Replace every `[BRACKETED]` placeholder with your real content.
2. Delete blocks you don't need (e.g. no examples for trivial tasks).
3. Test against 3 inputs — one normal, one edge case, one hostile/malformed.
4. Read the failures. Fix the system prompt. Repeat.

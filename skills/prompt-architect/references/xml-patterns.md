# XML Patterns

Claude is fine-tuned on XML. Use it as the structural cue. Patterns below are the most-reused.

## Top-level structure

```xml
<role>...</role>
<tone>...</tone>
<background>
  <schema>...</schema>
  <glossary>...</glossary>
</background>
<task>...</task>
<examples>
  <example>...</example>
  <example>...</example>
</examples>
<input>
  ...the live thing to act on...
</input>
<output_format>...</output_format>
<important>
  ...final reminders...
</important>
```

## Naming rules

- Use snake_case or single words: `<form_schema>`, `<task>`, `<input>`. Avoid hyphens — they parse fine but read worse.
- Be specific. `<form_schema>` is better than `<schema>`. `<accident_form>` is better than `<form>`.
- Reuse the same tag name when you reference content elsewhere ("apply the rules in `<task>` to the data in `<input>`").

## Few-shot examples

```xml
<examples>
  <example>
    <input>...</input>
    <expected_output>...</expected_output>
  </example>
</examples>
```

Tip: the workshop's "I/O" examples used `input` and `output` literally — Claude infers the mapping. Any consistent naming works.

## Multi-document inputs

```xml
<documents>
  <document index="1" source="claim_form.pdf">
    ...
  </document>
  <document index="2" source="sketch.png">
    [base64-encoded image]
  </document>
</documents>
```

The `index` attribute lets the prompt and the answer reference docs unambiguously: "based on document 1, line 4…".

## Nested reasoning

```xml
<thinking>
[Claude's scratchpad — extended thinking puts this here automatically]
</thinking>

<analysis>
  <form_findings>...</form_findings>
  <sketch_findings>...</sketch_findings>
  <reconciliation>...</reconciliation>
</analysis>

<final_verdict>...</final_verdict>
```

## When NOT to use XML

- One-line ad-hoc questions ("what's 2+2") — XML overhead isn't worth it.
- Outputs going straight to humans who'll read them — markdown is friendlier.
- JSON-only pipelines — use JSON schema, prefill `{`.

XML is for **structured prompts feeding non-trivial tasks**. That's most production prompts.

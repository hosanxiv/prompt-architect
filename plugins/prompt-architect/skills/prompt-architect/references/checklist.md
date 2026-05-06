# Pre-Flight Checklist

Run through this before shipping any prompt. If you can't tick a box, fix the prompt.

## Structure
- [ ] Role and tone are stated up front (block 1)
- [ ] Static / reusable info is in the system prompt, not pasted in every user message (block 2)
- [ ] Detailed task is a numbered list, with explicit ordering if order matters (block 3)
- [ ] Examples cover at least one edge case, not just the happy path (block 4)
- [ ] Live input is clearly delimited from instructions, e.g. wrapped in `<input>` (block 6)
- [ ] Output format is exact and machine-parseable (or explicitly free-form if that's intended) (block 8)
- [ ] Final reminder repeats the 1–2 most-critical constraints (block 9)
- [ ] If output is parsed by code: prefilled response or strict tag wrapper (block 10)

## Anti-hallucination
- [ ] Prompt explicitly says what to do when evidence is insufficient (don't guess — output a known token like `INSUFFICIENT_EVIDENCE`)
- [ ] Prompt asks Claude to cite the source line/field for factual claims
- [ ] If using extended thinking, budget is set high enough for the task

## Reusability
- [ ] Domain-specific terms are defined in a glossary block, not assumed
- [ ] No placeholder strings (`[YOUR_NAME]`) left over from drafting
- [ ] If you'd run this 1000 times, the static part is cacheable

## Cost
- [ ] System prompt is the **stable** stuff (gets cached). User message is the **changing** stuff.
- [ ] Few-shot examples haven't ballooned past what's actually useful (3–5 strong examples > 20 mediocre ones)
- [ ] You're not asking for verbose narration when you want a 1-line answer (output format constrains length)

## Empirical
- [ ] You have at least 3 test inputs, including 1 edge case and 1 hostile/malformed input
- [ ] You've decided what "passing" looks like for each test
- [ ] You have a plan for v2 — what you'd change if v1 misses

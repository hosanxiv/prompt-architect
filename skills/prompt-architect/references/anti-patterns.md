# Anti-Patterns

Common mistakes to avoid, and the fix for each.

## 1. "Don't do X" without "do Y instead"

**Bad:**
> Don't be too verbose.

**Better:**
> Limit your final answer to one sentence after the `<thinking>` block.

Negative-only instructions leave Claude guessing what positive behavior you want.

---

## 2. Vague task description

**Bad:**
> Analyze this document and tell me what's important.

**Better:**
> 1. Identify every dollar amount mentioned.
> 2. For each, classify as revenue / cost / neither.
> 3. Output a JSON array of `{amount, classification, source_quote}` objects.

"Important" is a hallucination magnet. Define the procedure.

---

## 3. Examples that all look the same

If your few-shot examples are all "easy yes" cases, Claude learns the easy pattern but fails the hard ones. **Show edge cases.** The workshop explicitly said: "tens, maybe even hundreds of examples that are quite difficult, maybe in the gray, that you'd like to make sure Claude actually has some basis to make the verdict."

---

## 4. Mixing static and dynamic content in the user message

If you paste the same 5KB form schema into every API call, you're paying tokens for it every time and missing the cache benefit. **Static info → system prompt. Dynamic input → user message.**

---

## 5. Asking for reasoning AND a parsable output, then parsing the whole thing

**Bad:** "Reason step by step, then give me JSON." → you get reasoning + JSON glued together, regex fails sometimes.

**Better:** "Reason step by step inside `<thinking>` tags, then output ONLY a `<result>...</result>` block with valid JSON inside." → parseable, reasoning still there for debugging.

Best: extended thinking + prefill the result tag.

---

## 6. No "I don't know" escape hatch

If the only allowed outputs are `A_AT_FAULT` and `B_AT_FAULT`, Claude will pick one even when the input is ambiguous. **Always include `INSUFFICIENT_EVIDENCE` / `UNKNOWN` / `null` as a valid output**, with a rule for when to use it.

---

## 7. Ignoring order

> "Look at the form and the sketch and decide who's at fault."

vs.

> "1. Read the form first. 2. *Then* look at the sketch. 3. Reconcile."

The second prompt produced sharper results in the workshop. Order is a control surface — use it.

---

## 8. Putting the user's actual question first

Models pay disproportionate attention to the **last** thing in context (recency bias). Put context, instructions, examples first → user's live request **last**. Also helps with caching (only the tail varies).

---

## 9. Trying to do 5 jobs in one prompt

If your prompt extracts data, classifies it, summarizes it, AND writes a report, decompose it. One Claude call per job, chained. Each prompt is sharper, each is debuggable, and you can swap models per stage (Haiku for extraction, Sonnet for the report).

---

## 10. Treating the first prompt as the final prompt

The workshop's quote: *"prompt engineering is a very iterative empirical science."* The first version of your prompt is **v0**. Run it on real inputs, watch it fail, look at the thinking trace, fix the system prompt, repeat. Anyone who claims they "wrote the perfect prompt" on attempt 1 is showing off, not iterating.

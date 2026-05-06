# Worked Example — Insurance Claim Form (the Workshop Case)

This is the example from the Prompting 101 workshop. It shows the same task at four prompt-engineering quality levels, and what changed at each step. It's the best single illustration of why the 10-block framework matters.

**Task:** read a Swedish car-accident report form + a hand-drawn sketch, and decide which vehicle was at fault.

---

## v0 — Naive prompt

```
Review this accident form and the sketch. Tell me what happened.
```

**Result:** Claude thinks it's a *skiing* accident on Chappangan Street. Why: nothing in the prompt told Claude this was a car-insurance context. "Chappangan" is a real Swedish street, so Claude pattern-matched on the only context it had.

**Lesson:** without role context, Claude guesses. Often badly.

---

## v1 — Add task context (block 1)

```
SYSTEM:
You are an AI assistant helping a human claims adjuster review Swedish
car-accident report forms. You will be given the form (with checkboxes
filled out) and a human-drawn sketch of the incident.

Determine which vehicle was at fault. Stay factual and confident. If you
cannot determine fault confidently, say so explicitly — do not guess.

USER:
[form image]
[sketch image]
```

**Result:** Claude correctly identifies it as a car accident. Picks up that vehicle A has box 1 checked, vehicle B has box 12 checked. But it can't tell what those boxes mean and won't commit to fault.

**Lesson:** role + tone fixed the framing, but Claude is still missing the schema of what the form actually represents.

---

## v2 — Add background data (block 2)

Same as v1, plus a system-prompt block explaining the form:

```
<form_schema>
The form has 17 numbered rows and 2 columns (Vehicle A, Vehicle B).
Row 1: Standing still
Row 2: Parked
...
Row 12: Reversing
...
Row 17: ...

The form is filled out by hand. Marks may be X, ✓, circles, scribbles,
or partial. Treat any non-trivial mark as "checked".
</form_schema>
```

**Result:** Claude now reads the form crisply. It knows box 1 = "standing still" and box 12 = "reversing". It commits to "vehicle B at fault" because the sketch corroborates a reversing-into-stationary scenario.

**Lesson:** static info that doesn't change between runs belongs in the system prompt. (Bonus: this is now a perfect candidate for prompt caching — the schema never changes.)

---

## v3 — Add detailed task description with order (block 3)

Same as v2, plus a task block that prescribes the order of operations:

```
<task>
1. First, examine the form. List every checkbox, marking each as
   checked / unchecked / unclear. Do this before looking at the sketch.
2. From the form alone, draft a preliminary description of what each
   vehicle was doing.
3. Then examine the sketch. Use your form-based understanding as a lens.
4. Reconcile the two: do they agree? If not, flag the conflict.
5. Make a fault determination only if the two sources agree and confidence
   is high. Otherwise output INSUFFICIENT_EVIDENCE with a reason.
</task>
```

**Result:** Claude shows its work — methodically goes through every box first, *then* looks at the sketch with that context loaded. The final answer is more defensible because each step is auditable.

**Lesson:** order matters. A human adjuster wouldn't stare at a chaotic sketch first; they'd read the form. Telling Claude to do the same yields sharper reasoning.

---

## v4 — Add output formatting + reminder + prefill (blocks 8, 9, 10)

Same as v3, plus:

```
<output_format>
Output your reasoning freely, then end with EXACTLY this structure:

<final_verdict>VEHICLE_A_AT_FAULT | VEHICLE_B_AT_FAULT | INSUFFICIENT_EVIDENCE</final_verdict>
<confidence>HIGH | MEDIUM | LOW</confidence>
<reason>One sentence.</reason>

Nothing after </reason>.
</output_format>

<important>
Reminder before you answer:
- Cite the box number for every factual claim about the form.
- If form and sketch conflict, output INSUFFICIENT_EVIDENCE.
- End with the exact <final_verdict>...</final_verdict> structure.
</important>
```

And on the API side, prefill the assistant message with `<final_verdict>` if the downstream code only cares about the verdict.

**Result:** Final answer is parseable, reasoned, and confident — or, if the input is genuinely ambiguous, cleanly flags `INSUFFICIENT_EVIDENCE` instead of guessing.

**Lesson:** the final 10% — formatting, reminders, prefill — is what makes a prompt production-ready. v3 was a good prompt; v4 is shippable.

---

## Summary of what changed at each step

| Version | What we added | What changed in output |
|---------|---------------|------------------------|
| v0 | nothing — naive | wrong domain entirely (skiing) |
| v1 | role + tone | correct domain, but won't commit |
| v2 | form schema in system prompt | reads form correctly, commits with justification |
| v3 | task steps in explicit order | reasoning is auditable; works on harder cases |
| v4 | output format + reminder + prefill | machine-parseable, edge cases handled cleanly |

This is the empirical loop the workshop emphasizes. You don't write the perfect prompt — you write v0, watch it fail, fix the system prompt, ship v1, and so on.

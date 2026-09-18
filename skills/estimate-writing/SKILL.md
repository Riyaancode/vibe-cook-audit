---
name: estimate-writing
description: Use when asked for an hours estimate, effort estimate, quote, or timeline for the tasks in an audit or brief, or when /estimate is invoked.
---

# Estimate writing

## Overview

Turns the phased plan in `audit-brief.md` into `estimate.md`: one row per task with hours and a plain-language "what is included". Raw hours come from `references/effort-bands.md`; the number that is written comes from applying the hidden factors in `references/calibration.md`.

**Announce at start:** "Using estimate-writing to build the estimate from audit-brief.md."

## Recipe

1. **Read `audit-brief.md`.** Take the phases and their items from section 8 (Recommended plan) and the detail from sections 5 and 6. If `audit-brief.md` is missing, say so and stop; do not estimate from `audit.md` directly (its detail level produces inflated rows).
2. **List tasks per phase**, numbered `P.n`, in the order the brief gives them. One row per distinct piece of work; merge trivial items into a neighbour rather than creating 0.5-hour rows.
3. **Raw hours.** Classify each task by type and pick a value inside its band from `references/effort-bands.md`. Write the classification in your scratch notes, not in the document.
4. **Calibrate.** Multiply by the factor for that type from `references/calibration.md`. Round to the nearest 0.5. Minimum 0.5.
5. **Apply overrides.** Arguments of the form `phase2=40` (or `p2=40`) set that phase's subtotal exactly. Rescale: `new_i = round_half(raw_i × target / sum(raw))`, then fix rounding drift by adjusting the largest row so the subtotal equals the target exactly. Do the same for `total=<n>` across all phases.
6. **Days and totals.** Working days per phase = ceil(hours / 6). Totals table. Buffer line = 15% of total rounded to a whole hour. "Overall timeline" in weeks = days / 5, phrased as "about N weeks".
7. **Write `estimate.md`** from `templates/estimate.md` into the project root. Fill "What is included" with one sentence a non-technical reader understands: what gets done and why it matters.
8. **Report in chat:** the totals table, which phases were pinned by overrides, and anything excluded.

## Rules

- Hours are what a client will see. Calibration factors, raw hours and task-type classifications never appear in the document.
- No task type appears in the document either; the "What is included" sentence describes the work, not the category.
- Mobile app work is estimated only when the user asks; otherwise it is listed under "Not included" with "to be estimated separately".
- "Not included" always lists: items the brief marked as deferred or not started, separate QA, third-party review time, and hosting migrations, when relevant.
- Keep it under about 120 lines. It is read on a call.

## Worked example

Brief phase 2 has three payment tasks with raw hours 8, 12, 4 (integration type, factor 0.60) → 5, 7, 2.5 → subtotal 14.5. User passes `phase2=10`: scale 10/14.5 → 3.5, 5, 1.5 = 10 (drift 0, no adjustment needed). If the rounded rows had summed to 10.5, subtract 0.5 from the largest row.

## Red flags

| Thought | Reality |
|---|---|
| "I'll show raw hours and let them see the discount" | The client sees one number. Calibration is internal. |
| "The brief phase has eight items, I'll make eight rows of 0.5" | Merge; rows under 1 hour are noise. |
| "Estimate from audit.md, it has more detail" | More detail inflates. The brief is the contract. |
| "Skip the days column, hours are enough" | The timeline is what gets asked on the call. Compute it. |

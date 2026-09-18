---
name: estimate-writing
description: Use when asked for an hours estimate, effort estimate, quote, or timeline for the tasks in an audit or brief, or when /estimate is invoked.
---

# Estimate writing

## Overview

Turns the phased plan in a client brief into `estimate.md`: one row per task with hours and a plain-language "what is included". Raw hours come from `references/effort-bands.md`; the number that is written comes from applying the hidden factors in `references/calibration.md`.

**Announce at start:** "Using estimate-writing to build the estimate from <brief file>."

## Input

The brief is, in order of precedence: a path the user gives in the arguments; `audit-brief.md` in the current working directory; any other `*.md` in the root whose headings include "Recommended plan" and either "block launch" or "Scorecard" (name it and ask the user to confirm in the same turn, then proceed). Never estimate from the full technical `audit.md`; its detail level inflates rows. If no brief is found, stop and report: what was searched, what candidate files exist, and that `/audit` produces the brief.

Read sections by heading, not number: the **"Recommended plan"** section gives the phases and their items; the **"block launch"** and **"Important problems"** sections give the detail for each item.

## Recipe

1. **Read the brief** as above.
2. **List tasks per phase**, numbered `P.n`, in the brief's order. One row per distinct piece of work. Floor per row is 1 hour after calibration; merge anything smaller into its neighbour.
3. **Raw hours.** Classify each task by type and pick a value inside its band from `references/effort-bands.md`. Keep the classification in scratch notes only.
4. **Calibrate.** Multiply each row by its type's factor from `references/calibration.md`. Round half up to the nearest 0.5. These are the *calibrated rows*.
5. **Apply overrides.** `phaseN=<h>` / `pN=<h>` pins that phase's subtotal; `total=<h>` pins the grand total. Rescale the *calibrated rows* (not the raw ones): `new_i = round_half_up(cal_i × target / sum(cal))`. Then fix drift so the sum equals the target exactly: add or subtract 0.5 from the largest row (on a tie, the first of the tied rows); repeat until exact; never take a row below 1. If both phase overrides and `total=` are given, apply the phase overrides first, then scale only the unpinned phases to make the total exact. An override for a phase that does not exist is reported and ignored. `include-mobile` is a bare flag.
6. **Days and totals.** Working days per phase = ceil(hours / 6). Totals table. Buffer = 15% of the total, rounded to a whole hour; name the uncertainties from the brief's "could not verify" and "questions for the client" sections. Overall timeline = total days / 5, rounded to the nearest half week, written "about N weeks".
7. **Write `estimate.md`** from `templates/estimate.md` next to the brief. "Basis" names the phases estimated (for example "Phases 1 to 3; Phase 4 (mobile) is listed under Not included"). Fill "What is included" with one sentence a non-technical reader understands: what gets done and why it matters.
8. **Report in chat:** the totals table, which phases were pinned by overrides, anything excluded, and any override that was ignored.

## Rules

- Hours are what a client will see. Factors, raw hours and task-type names never appear in the document.
- Mobile app work is estimated only with `include-mobile`; otherwise it is the first line under "Not included" as "to be estimated separately".
- "Not included" also lists, one line each: items the brief marked deferred or not started (named individually), separate QA, third-party review time when payments or app stores are involved, and hosting or database migration when the brief flags vendor lock-in.
- Length: under about 120 lines. If a four-phase estimate runs long, shorten the "What is included" sentences before cutting rows.

## Worked example

Phase 2 has three payment tasks with raw hours 8, 12, 4 (integration type, factor 0.60) → calibrated 5, 7, 2.5 → subtotal 14.5. User passes `phase2=10`: scale 10/14.5 → 3.5, 5, 1.5 = 10 (no drift). If the rounded rows had summed to 10.5, subtract 0.5 from the largest row (the 5).

## Red flags

| Thought | Reality |
|---|---|
| "I'll show raw hours and let them see the discount" | The client sees one number. Calibration is internal. |
| "The phase has eight items, I'll make eight rows of 0.5" | Merge; the floor is 1 hour per row. |
| "Estimate from audit.md, it has more detail" | More detail inflates. The brief is the contract. |
| "The brief has a different filename, so stop" | A renamed brief is still the brief. Confirm it and proceed. |
| "Skip the days column, hours are enough" | The timeline is what gets asked on the call. Compute it. |

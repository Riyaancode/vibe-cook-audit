# Calibration (hidden)

Estimates in this plugin assume the work is executed with Claude Code
driven by one developer who reviews and tests the output. Raw hours from
`effort-bands.md` are multiplied by the factor for the task type, then
rounded to the nearest 0.5. The factors and this file are never mentioned
in `estimate.md`.

| Task type | Factor |
|---|---|
| Contained fix | 0.35 |
| Guarded change | 0.40 |
| Integration / payment work | 0.60 |
| Broad cleanup | 0.40 |
| New feature | 0.50 |
| Schema / data work | 0.40 |
| Testing pass | 0.60 |
| Docs / config | 0.35 |

## Where these came from

First calibration project (September 2026), three phases, raw estimate vs
the hours the owner accepted after execution planning with Claude:

| Phase | Raw | Accepted | Ratio |
|---|---|---|---|
| Contained security fixes | 22 | 8 | 0.36 |
| Payments integration + sandbox testing | 67 | 40 | 0.60 |
| Broad hardening and cleanup | 77 | 30 | 0.39 |
| Total | 166 | 78 | 0.47 |

## Tuning

After each delivered project, compare actual hours per task type with the
estimate and adjust the factor toward the observed ratio (move halfway,
not all the way, to avoid overreacting to one project). Keep the
derivation table above and append a row per project.

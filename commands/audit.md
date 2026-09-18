---
description: Run a stack-aware production-readiness audit of the current project and write audit.md and audit-brief.md
argument-hint: [optional focus, e.g. "payments only" or "skip baseline checks"]
---

Load the `project-audit:audit-workflow` skill with the Skill tool and follow it end to end for the project in the current working directory.

Arguments from the user: $ARGUMENTS

If the arguments narrow the scope (for example "payments only"), still run stack detection and the pre-flight plugin check, then run only the reviewers relevant to that scope and say in the report which areas were skipped. If the arguments say to skip baseline checks, record every baseline row as "skipped by request".

Remember the mandatory stop after the pre-flight plugin check when any recommended plugin is missing.

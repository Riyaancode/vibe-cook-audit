---
description: Rewrite an existing audit.md as a plain-language client brief (audit-brief.md)
argument-hint: [optional: path to audit.md if not in the project root]
---

Load the `vibe-cook-audit:audit-brief-writing` skill with the Skill tool and apply it to the audit document.

Arguments from the user: $ARGUMENTS

If no path is given, read `audit.md` in the current working directory. If it does not exist, say so and suggest running `/cook` first. Write the result to `audit-brief.md` next to it.

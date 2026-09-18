---
name: audit-brief-writing
description: Use when turning a technical audit into a version a non-technical client can read, when asked for a brief or summary of an audit, when a document will be walked through on a call, or when /audit-brief is invoked.
---

# Audit brief writing

## Overview

The brief is the audit rewritten for someone who will never open the code. It keeps every conclusion and drops every identifier. Source of truth is the existing `audit.md`; the brief never introduces a finding that is not in it.

## What the brief IS

A document under about 160 lines with exactly the sections in `templates/audit-brief.md`, in that order:

1. The short answer (one-line verdict, six-row yes/no table, overall effort, the effort scale)
2. What it is built with (module → provider, copied from the audit's stack table)
3. Scorecard (traffic lights)
4. What is working well (3 to 5 bullets)
5. The launch blockers (each: bold title with area, two to four plain sentences, `*Effort: Low/Medium/High.*`)
6. Important problems, grouped (Security, Payments, Product / on screen, other)
7. Feature status table
8. Recommended plan by phase, each phase labelled with an effort level
9. Things that could not be verified without live access
10. Key questions for the client (decisions only they can make)

## Translation table

Rewrite, do not delete. Every technical term maps to a plain phrase.

| In the audit | In the brief |
|---|---|
| `src/lib/billing.server.ts:254` | "the code that keeps coach plans in sync" |
| RLS / row-level security | "database access rules" |
| SECURITY DEFINER function | "a database function that runs with elevated rights" |
| webhook signature verification | "checking that a message really came from Stripe" |
| IDOR / missing ownership check | "a coach can act on records that are not theirs" |
| idempotency key | "protection against the same charge happening twice" |
| SSRF | "the server can be tricked into fetching internal addresses" |
| merchant of record | "the business legally responsible for the sale and refunds" |
| service-role key | "a master key that bypasses all database security" |
| migration | "a database change file" |
| `.env` committed | "the settings file is saved in the code where it should not be" |
| hours, days, weeks, sprints | Low / Medium / High |
| Critical / High / Medium / Low severity | blocker / important problem (see severity reference) |

Words that never appear in the brief: file paths, backticked identifiers, line numbers, function or table names, HTTP status codes, package names, CLI commands.

## Effort labels

Every blocker and every phase carries exactly one of **Low**, **Medium**, **High**. No time units anywhere in the document, including "immediately", which is allowed only as "should be done first".

## Tone

Short sentences. Say what happens to a real user or to money. Lead each blocker with the consequence, then the cause, then the effort. Keep the good news: a client who only hears problems stops listening.

Example blocker, from a real brief:

> **3. Two privacy fixes never took effect (Data protection)**
> The developer wrote fixes to stop every coach from seeing every unclaimed lead's name, email and phone number. The fixes referenced the wrong rule names, so nothing changed. **Right now, any coach account can see and claim any lead on the platform.** *Effort: Low. Should be done first.*

## Checks before saving

- `grep -c` for `/src`, `.ts`, `.sql`, backticks, "hour", "day", "week": all zero except in the two file names in the header.
- Every blocker in the brief exists in the audit's Critical/High sections.
- Section 10 has at least three real decisions, not rhetorical questions.
- Line count under about 170.

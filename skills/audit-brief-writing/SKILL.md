---
name: audit-brief-writing
description: Use when turning a technical audit into a version a non-technical client can read, when asked for a brief or summary of an audit, when a document will be walked through on a call, or when /plate is invoked.
---

# Audit brief writing

## Overview

The brief is the audit rewritten for someone who will never open the code. It keeps every conclusion and drops every identifier. Source of truth is the existing `audit.md`; the brief never introduces a finding that is not in it.

## What the brief IS

A document under about 170 lines (template separators and blank lines count) with exactly the sections in `templates/audit-brief.md`, in that order:

1. The short answer (one-line verdict, six-row yes/no table, overall effort, the effort scale)
2. What it is built with (module → provider, copied from the audit's stack table; if the audit has none, assemble it from the audit's dependency and hosting sections, provider names only, no version numbers)
3. Scorecard (traffic lights; rows follow the audit's scorecard, merging where the template has fewer)
4. What is working well (3 to 5 bullets)
5. The launch blockers: exactly the audit's Critical items, never a promoted High (each: bold title with area, two to four plain sentences as a guide, `*Effort: Low/Medium/High.*`)
6. Important problems, grouped (Security, Payments, Product / on screen, then as many other named groups as needed); an item may also appear in section 9 if it is unverifiable
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
| audit effort S / M / L | Low / Medium / High |
| phase or overall effort: "this week" / a few days | Low; 1 to 2 weeks | Medium; more than 2 weeks, or several Mediums | High |
| webhook | "a message Stripe sends when something happens" |
| return 200 / HTTP status codes | "confirm to Stripe that the message was received" |
| rate limiting / CAPTCHA | "limits on how often a form can be submitted" / "a check that the sender is human" |
| OAuth tokens | "saved sign-in credentials for the calendar" |
| destination charge | "the platform takes the payment and forwards it" |
| bundle identifier | "the app's identifier" |
| signed URL / attachment path | "a private download link" / "the file location" |
| "immediately" | "should be done first" |
| Critical / High / Medium / Low severity | blocker / important problem (see severity reference) |

Words that never appear in the brief: file paths, backticked identifiers, line numbers, function or table names, HTTP status codes, npm package names and version numbers, CLI commands. Provider and product names (Stripe, Supabase, TanStack, Capacitor, Resend) are fine.

## Effort labels

Every blocker and every phase carries exactly one of **Low**, **Medium**, **High**. No time units as effort or timeline anywhere in the document. Durations that are product facts ("retries for three days", "a 48-hour follow-up", "monthly membership") are allowed. Avoid "daily", "today", "weekly" and "hourly" where a synonym exists, so the checks below stay clean.

## Tone

Short sentences. Say what happens to a real user or to money. Lead each blocker with the consequence, then the cause, then the effort. Keep the good news: a client who only hears problems stops listening.

Example blocker, from a real brief:

> **3. Two privacy fixes never took effect (Data protection)**
> The developer wrote fixes to stop every coach from seeing every unclaimed lead's name, email and phone number. The fixes referenced the wrong rule names, so nothing changed. **Right now, any coach account can see and claim any lead on the platform.** *Effort: Low. Should be done first.*

## Checks before saving

Run against the finished file:

```
grep -cF '/src' brief.md; grep -cF '.ts' brief.md; grep -cF '.sql' brief.md
grep -c '`' brief.md                 # expect 1: the audit.md mention in the header
grep -ciwE 'hours?|days?|weeks?|sprints?' brief.md   # expect 0
```

- Date is the day the brief is written.
- Every blocker in the brief exists in the audit's Critical/High sections.
- Section 10 has at least three real decisions, not rhetorical questions.
- Line count under about 170.

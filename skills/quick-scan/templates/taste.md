# <Project name> — First Taste

**Date:** <date> · **Scan type:** quick (no specialist review). Run `/cook` for the full audit.

## First impression

**<One sentence verdict: what this is, how healthy it looks, the one thing that worries us most.>**

| Signal | Result |
|---|---|
| Builds | ✅/❌ <one line> |
| Types check | ✅/❌/— |
| Tests | ✅ <n> pass / ❌ / — none found |
| Lint | ✅ / ⚠️ <n> issues (<m> formatting) / — |
| Known vulnerabilities | <n> advisories (<worst package>, fix available?) / ✅ none |
| Secrets in the client bundle | ✅ none / ❌ <what> |
| Settings file committed | ✅ no / ⚠️ yes (<n> keys, names only) |

## What it is built with

| Module | Provider |
|---|---|

Package manager: <pm> (<lockfile honoured, or "lockfile is X but X is not installed; ran with npm">). Git history: <yes, n commits / none>.

## Size and shape

| Metric | Value |
|---|---|
| Source lines | |
| Files over 700 lines | <n> (largest: <file> <lines>) |
| Test files | |
| `any` / `@ts-ignore` / `TODO` | |
| Largest client chunk | |

## Recommended official plugins for a full audit

Installed: <list>. Missing: <service> `<install command>`; <service> `<install command>`.

## Where a full audit would look first

1. <area and why, from the signals above>
2. …

_This scan ran the project's own tooling and read its manifests. It did not review code paths, security rules or payment flows; that is what `/cook` does._

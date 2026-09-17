# Frontend and feature-completeness reviewer checklist

Use the project's own docs, roadmap, plan files and TODOs to learn what was INTENDED, then verify against code what is DONE. Cite file:line.

1. Route inventory: every route/page, public vs authenticated, layout gates. Cross-check every `Link`/`navigate`/`redirect` target against the route tree; list dead links.
2. Feature completeness matrix: for each product area, status Complete / Partial / Stub / Missing with evidence. Areas come from the routes and the docs. Include marketing, auth, onboarding, core domain features, settings, admin, notifications, payments UI, integrations, mobile, offline/PWA, public sharing.
3. Half-done indicators: "coming soon", TODO/FIXME, disabled buttons without handlers, feature flags and their defaults, placeholder copy, empty catch blocks, routes rendering nothing, backend endpoints with zero frontend callers, frontend calls to endpoints that do not exist.
4. Contradictions on screen: pricing or fees hard-coded differently from config; features marketed as unavailable that are built (or the reverse); old brand names, mixed support emails, wrong site URL.
5. Gating consistency: pages that should be hidden by a feature flag but are reachable by direct URL; `noindex` on private/tokenised pages.
6. Error boundaries (root, per-route), loading states, empty states, unhandled promise rejections, silent `.catch(() => {})`.
7. Hooks and lint: `exhaustive-deps` warnings, suppressed rules, explicit `any` count and where, `@ts-ignore` count.
8. Data fetching: unbounded list queries (no limit/pagination), aggressive polling intervals, N+1 patterns in client code, cache config.
9. Performance: largest built chunks, heavy libraries loaded eagerly (spreadsheet, canvas, charts, maps), unoptimised media, videos not lazy.
10. PWA/service worker: caching strategy, cache versioning, update prompt, whether authenticated HTML is cached; manifest validity.
11. SEO basics: titles/meta on public pages, sitemap, robots rules matching real routes.
12. Accessibility sample: images without alt, icon-only buttons without labels, form labels.
13. Mobile/native shell if present: hand off details to mobile-reviewer but note whether the web app references native APIs that are never wired.
14. Tests for UI: presence and coverage.

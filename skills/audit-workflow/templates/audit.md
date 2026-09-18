# <Project name> — Code & Security Audit

**Date:** <date>
**Scope:** <what was covered: web app, backend, payments, mobile, dependencies, docs>
**Method:** Every file was read. The project was installed, type-checked, linted, unit-tested and built. <N> specialist reviews ran in parallel (<list>) and every Critical and High finding was independently re-verified against the code before being included.

---

## 1. The bottom line

**<One-sentence verdict: ready / not ready, and why.>**

| Question | Answer |
|---|---|
| Does the app build and run? | ✅/❌ <one line> |
| Is the product feature-complete? | <one line> |
| Is it safe to take payments? | <one line, or "no payments in scope"> |
| Is customer data protected? | <one line> |
| Is the mobile app ready for the stores? | <one line, or "no mobile app"> |
| Can a new developer take this over? | <one line> |

**Estimated work to reach a safe launch:** <High/Medium/Low overall, or hours if the user asked for them>

---

## 2. What this project is built with

| Module | Provider |
|---|---|
| Frontend | <framework(s)> |
| Backend / API | <runtime and framework> |
| Database | <provider> |
| Authentication | <provider> |
| Payments | <provider> |
| File storage | <provider> |
| Email | <provider> |
| SMS | <provider> |
| Push notifications | <provider> |
| Mobile | <shell/framework> |
| Hosting | <provider> |
| Monitoring / analytics | <provider> |

(Only rows that apply. Major services only.)

---

## 3. Scorecard

🟢 Good · 🟡 Needs work · 🔴 Blocker

| Area | Rating | One-line summary |
|---|---|---|
| Build, types, tests | | |
| Code structure | | |
| Features | | |
| Payments | | |
| Database security | | |
| Server / API security | | |
| Mobile app | | |
| Operations & config | | |
| Dependencies | | |
| Documentation | | |

---

## 4. What is working well

- <bullet>
- <bullet>

---

## 5. Critical problems — must fix before launch

### 5.1 <Title> — Effort: S/M/L
**What it means:** <plain language>
**Risk:** <consequence>
**Fix:** <one or two sentences>

(repeat)

---

## 6. High-priority problems — fix before or shortly after launch

### Security
- **6.1 <Title>.** <two or three sentences>

### Payments
### Product / on-screen
### Data / scheduling / other

---

## 7. <Stack> — detailed assessment
(One section per specialist reviewer that ran: Payments (Stripe), Backend (Supabase/Convex/…), Mobile, Messaging, Cloud. Each has: what is built (flow matrix), best-practice checklist table with ✅/⚠️/❌, weaknesses table, required configuration.)

---

## 8. Application / API security summary
**Good:** bullets. **Needs fixing:** bullets.

---

## 9. Feature completeness

| Area | Status | Notes |
|---|---|---|

---

## 10. Code quality, structure and dependencies

### Verified results
| Check | Result |
|---|---|
| typecheck | |
| build | |
| tests | |
| lint | |
| vulnerability audit | |
| secrets in client bundle | |

### Structure
### Dependencies

---

## 11. Operations and configuration
Env var table (name, purpose, documented?), scheduled jobs, hosting constraints.

---

## 12. Recommended path to launch
### Phase 1 — Stop the bleeding
### Phase 2 — <biggest risk area>
### Phase 3 — Harden and tidy
### Phase 4 — <separate project if any>

---

## 13. Glossary (for non-technical readers)

---

## Appendix A — Technical reference (for developers)
One table per reviewer: Ref | File | Detail (file:line).

### A<n>. Tooling results (raw)
```
<verbatim command results>
```

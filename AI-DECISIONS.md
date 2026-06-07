# AI Decisions

> Snapshot of decisions made with AI. Agents and devs read this before starting new tasks.
> **This is the V1 build source of truth.** It is deliberately lean.

---

## Context (read first)

**Product:** Happy Feet — a school-management app for a single preschool in Hyderabad, India (one paying client), expanding to more branches soon.

**Reality that shapes every decision here:**
- 105 students today → ~400–500 eventually · ~10 staff · 2 transport vans · single branch now, Branch B planned.
- Total app users at scale ≈ 500 (staff + parents).
- **Solo junior developer. Already delayed. Budget-constrained. Advance taken — must deliver.**

**The most important rule on this page:** there are **two specs**, do not confuse them.
- `domain-overview.md` — the narrative of *what/why* the product is. Good context.
- `ENGINEERING-SPEC.md` — a **3-year backlog / aspiration**. Enterprise-grade machinery (PII vault, hash-chaining, ReBAC, consent lifecycle, idempotency framework). **DO NOT build from it for V1.** Pull from it only when a specific phase genuinely needs it.
- `AI-DECISIONS.md` (this file) — the **lean V1 we actually build.** When in doubt, this wins.

> A preschool is a *small* system. Its entire 3-year load fits on one small Postgres. Build the smallest thing that serves it well; keep a few cheap primitives so growth is a migration, never a rewrite.

---

## Architecture

**Pattern:** Modular Monolith

- Single deployable unit (one Next.js app), divided into self-contained modules.
- Modules communicate via internal interfaces (a module's `service`), never direct cross-module imports or reaching into another module's tables.
- Each module owns its data — no shared tables across module boundaries.
- Extract to services later if ever needed — but at 500 users that day never comes. Not the default move.

---

## Stack & Conventions

- **Language/Runtime:** TypeScript on Node.js
- **Framework:** Next.js (full-stack — server actions/API routes + React), shipped as an **installable PWA** (see Delivery)
- **Database:** PostgreSQL via **Supabase** (managed Postgres + Auth + Storage + Row-Level Security), **Mumbai / ap-south-1** region
- **Hosting/Infra:** India region (ap-south-1) for **DPDP data residency**; single small instance; **managed daily backups + PITR** (durability is non-negotiable — see Decision 12)
- **Auth:** Supabase Auth (OTP). Session **persists** — users log in once, not per task. **RLS is the authorization layer.**
- **Module structure:** `src/modules/<module-name>/{index, service, repo, types}`
  - `repo` = data access (the only place that touches this module's tables)
  - `service` = business logic + the module's public interface (`index` re-exports it)
  - other modules import **only** another module's `service`, never its `repo`

---

## Delivery — one PWA, not native apps

- The app is a **responsive web app that installs as a PWA** ("Add to Home Screen" → app icon, full-screen, no browser bar).
- **Login once, stays logged in** → tap icon, you're in. This is the fix for "no value if users open a website and log in every task."
- **Web push** for notifications (Android + iOS 16.4+ installed PWAs) — free, no native app.
- **Offline-*tolerant*** (optimistic save + retry queue, esp. attendance) — NOT a full offline-sync engine.
- Same single app serves everyone: parents on phones, teachers on phones, owner on laptop.
- **V2 path (no rewrite):** wrap the same PWA with **Capacitor** to reach the app stores *if* ever needed. Not now.

---

## Data Primitives (the 5 non-negotiables — bake in from line 1)

These are the only "future-proofing" we do. Each is cheap now and a painful retrofit later.

1. **`branch_id` on every tenant-scoped row** + a `branch` record → multi-branch ready, Branch B drops in free.
2. **Separate PII table** (Aadhaar, medical) isolated from the main records → DPDP-pragmatic now, vault-able later.
3. **`guardian_child` link** (parents ↔ children) → parent access is guardian-scoped, not child-scoped.
4. **One `authorize()` path** (implemented as RLS + join-table `WHERE` clauses) → role × scope × relationship without an engine.
5. **Append-only payment ledger** (`source` + `external_ref`, idempotent) for when fees go native → money is the one domain where "just UPDATE the row" silently corrupts.

---

## Modules

| Module | Responsibility | Exposes (public `service` interface) |
|--------|---------------|--------------------------------------|
| **1. identity** | users, roles, the branch hierarchy, sessions, access control | `getCurrentUser()`, `authorize(action, scope)`, `currentBranch()` |
| **2. people** | students, guardians, staff, the **separate PII table** | `getStudent()`, `createStudentFromApplication()`, `guardianChildren(parentId)`, `getStaff()` |
| **3. academics** | academic year, programmes, classes, **timetable**, **curriculum/lesson plans**, calendar | `getClasses()`, `getTimetable(classId)`, `publishedLessonPlans(programmeId)` |
| **4. admissions** | applications (pre-enrollment); the **enroll** workflow | `createApplication()`, `enroll(applicationId)` → calls `people.createStudentFromApplication()` |
| **5. attendance** | daily attendance, absence alerts, the **daily care feed** | `markAttendance()`, `childAttendance(childId)`, `getDailyFeed(childId)` |
| **6. communications** | announcements, parent inbox, WhatsApp/SMS, push | `sendAnnouncement()`, `postMessage()`, `notify(userId, payload)` |
| **7. fees** | **read-only** imported CoFee payments/invoices — view + export | `importCoFee(file)`, `feeStatus(childId)`, `exportFees(filters)` |
| *xc. admin* (cross-cutting) | export, dashboards, **audit log**, settings, data control — reads all modules, owns `audit_log` | `exportCSV(entity, filters)`, `logAudit(event)`, `dashboard(branchId)` |

**Workflows are functions, not modules:** `enroll`, `rollover`, `migration` orchestrate across modules. **Migration is a one-time import script**, not a module.

---

## Build Phases (order = client-visible value)

- **Phase A (~wk 3 — the demo that rebuilds trust):** identity + people + attendance + daily feed.
- **Phase B (~wk 5):** admissions.
- **Phase C (~wk 7):** communications (announcements + inbox).
- **Phase D (~wk 9–10):** academics (timetable + curriculum) + **CoFee import** + admin export/data-control.

Demo every ~2 weeks. Fees stay on CoFee throughout V1 (we import, we don't rebuild).

---

## Key Decisions

| # | Decision | Why | Status |
|---|----------|-----|--------|
| 1 | Modular monolith over microservices | Simpler ops, shared DB, easier refactor later; 500 users never needs services | `active` |
| 2 | One Next.js app shipped as an installable **PWA**; **no native apps in V1** | Login-once + push + offline-tolerant solves the value problem; native = 2 codebases a solo dev can't afford | `active` |
| 3 | **Managed Postgres (Supabase), Mumbai region** | DPDP residency + backups offloaded; least ops for a junior under deadline | `active` |
| 4 | **`branch_id`** on every tenant-scoped table from day 1 | Multi-branch ready; Branch B drops in with zero rework | `active` |
| 5 | **Separate PII table** (Aadhaar/medical isolated) | DPDP-pragmatic; lets us encrypt/vault later without reshaping schema | `active` |
| 6 | **RLS + `guardian_child` joins** as the one authorization layer | Role × scope × relationship as `WHERE` clauses — no ABAC/ReBAC engine | `active` |
| 7 | **Append-only `audit_log`** (one plain table, no hash chain) | Accountability without tamper-evidence theater; backups + restricted writes suffice at this scale | `active` |
| 8 | **CoFee = read-only import + view + export** in V1 | The data matters; the billing engine doesn't yet. Unifies students+attendance+fees in one view cheaply | `active` |
| 9 | **Append-only payment ledger** primitive (`source`+`external_ref`, idempotent) | Money is the one place to be strict; reused by the future native fee module | `active` |
| 10 | Explicit **state machines** for branch / student / staff / document | The lifecycle gaps that bite mid-build; cheap to define up front | `active` |
| 11 | **Backups are non-negotiable** (managed PITR, tested restore) | Single instance is fine for *availability*; data loss is not survivable for a school's records | `active` |
| 12 | Build from **AI-DECISIONS**, treat **ENGINEERING-SPEC as backlog** | The heavy spec was mis-scoped as V1; it's a 3-year roadmap, not a build order | `active` |

> Statuses: `active` · `changed` · `dropped`

---

## What We Ruled Out

| Option | Reason |
|--------|--------|
| Microservices | Premature complexity for current scale (500 users, one DB) |
| Native iOS/Android apps (V1) | Two codebases + store overhead a solo dev can't carry; PWA covers it; Capacitor-wrap later if ever needed |
| Hash-chaining / Merkle audit log | Tamper-evidence theater for a single internal tool; backups + restricted access suffice |
| PII vault / crypto-shred / KMS envelope (V1) | Separate PII table + an erasure routine is DPDP-reasonable for a small fiduciary; vault later if we grow |
| ReBAC / ABAC permission engine | RLS + join-table `WHERE` clauses give the same result with no framework |
| Full billing engine / Razorpay processing (V1) | CoFee handles fees today; we import its data, we don't rebuild it |
| Self-hosted Postgres on a single VPS (no managed backups) | Data-loss risk too high for a junior under deadline; managed Postgres removes the footgun |
| Notification delivery state-machine + escalation queue | Send + log the attempt + show failures to staff is enough |
| Offline-first **sync engine** (CRDT) | Offline-*tolerant* (optimistic save + retry) covers flaky connectivity for a fraction of the work |
| Event sourcing / CQRS | Plain CRUD + the one audit table; nothing here justifies it |
| Building 16 code modules from the feature list | Those are workflows/features; code is organized by **data owner** (~7 modules), not by workflow |

---

## Open Questions

- [ ] CoFee export schema stability + import cadence — confirm before building module 7 (parser spec already in `ENGINEERING-SPEC.md` → CoFee Data Dictionary)
- [ ] Absence alerts: SMS (MSG91, costs money) vs rely on push + WhatsApp deeplink (free)?
- [ ] Admissions: public online application form, or staff-entered only for V1?
- [ ] Collect the child's Aadhaar at all in V1? (data minimisation — no V1 function needs it)
- [ ] Confirm a demo date with the client (~3 weeks out) — this is the trust-rebuilding action
- [ ] App-store presence (Capacitor wrap) needed in V2, or does the PWA suffice?

---

## Notes for AI Agents

- **Respect module boundaries** — no reaching into another module's internals; import only its `service`, never its `repo` or tables.
- **Add new features inside the relevant module**, not at the top level. If it spans modules, use a service call or an event, not a direct import.
- **Honor the 5 primitives** on every new table/feature: `branch_id`, PII-in-its-own-table, `guardian_child` scoping, `authorize()`/RLS, payment ledger for money.
- **Access:** call `identity.getCurrentUser()` and `identity.authorize()` — never decode a JWT or check roles inline. **Deny-by-default.**
- **Student creation:** only `people.createStudentFromApplication()` — `admissions.enroll()` calls it; never INSERT students elsewhere.
- **CoFee data is read-only in V1** — never write/mutate imported fee rows.
- **Audit:** call `admin.logAudit()` on sensitive actions (sensitive-data reads, exports, deletes, overrides).
- **When tempted to add machinery** (vault, hash-chain, ReBAC, delivery state-machines): stop — it's in `ENGINEERING-SPEC.md` as backlog on purpose. Build the lean version here unless a phase explicitly calls for more.

<!--
  Add more context here as the project grows.
  e.g. "identity.service exposes getCurrentUser() — use that, never decode JWT directly"
  e.g. when fees go native, flip Decision 8 to `changed` and add the billing-engine decision.
-->

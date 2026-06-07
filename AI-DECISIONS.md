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
- **Database:** PostgreSQL via **Supabase** (managed Postgres + Storage), **Mumbai / ap-south-1** region
- **Hosting/Infra:** India region (ap-south-1) for **DPDP data residency**; single small instance; **managed daily backups + PITR** (durability is non-negotiable — see Decision 12)
- **Auth:** **Clerk** — sign-in, **MFA**, sessions, and coarse RBAC (roles). Session **persists** (log in once). **Email is the channel for all auth workflows** (portal invites, sign-in/out, verification). **MFA is required for staff roles** (admin, branch admin, accountant, coordinator, teacher) via **authenticator app + backup codes — not SMS**; parents sign in by email, MFA optional. **SMS (MSG91) is reserved for parent communications** (e.g., absence alerts), never auth. **Step-up re-authentication is required before sensitive actions** (revealing a child's Aadhaar/medical/photo, data exports, admissions decisions, TC/Bonafide & discontinuation, user/role management) — re-verify even within an active session. Audit logging is **not** Clerk's job — that's our own `audit_log` table.
  - *Residency caveat:* Clerk holds auth identities (emails/phones) on US infra. DPDP currently permits this (blacklist model, US not restricted), and **all sensitive student data stays in Supabase Mumbai**. Revisit if a stricter residency stance is required.
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
4. **One `authorize()` function** — checks **`user_role`** (role × branch) + **`class_teacher` / `guardian_child`** for finer scope = role × scope × relationship, no ABAC/ReBAC engine. **Role is an assignment (a `user_role` row), never a column on the user** — so multiple people per role, multi-role, and multi-branch all just work. (Supabase RLS optional later as a DB-layer safety net.)
5. **Append-only payment ledger** (`source` + `external_ref`, idempotent) for when fees go native → money is the one domain where "just UPDATE the row" silently corrupts.

---

## Modules

| Module | Responsibility | Exposes (public `service` interface) |
|--------|---------------|--------------------------------------|
| **1. identity** | users, **role assignments** (`user_role`: role × branch, many per user), branch hierarchy, sessions, **MFA + RBAC** (Clerk), invitations & de-provisioning, access control — full spec: [`modules/identity-access.md`](modules/identity-access.md) | `getCurrentUser()`, `authorize(action, scope)`, `currentBranch()`, `inviteUser(email, role, branch)`, `deactivateUser(userId)` |
| **2. people** | students + **full lifecycle** (enroll, rollover, discontinuation, re-admission; **TC/Bonafide acknowledged — issued offline, no PDF**), guardians, **staff** (profiles, **certifications + expiry alerts**, **staff attendance**, **lightweight leave**, **exit gates**), the **separate PII table** | `getStudent()`, `createStudentFromApplication()`, `rollover()`, `acknowledgeCertificate(type)`, `discontinue()`, `guardianChildren(parentId)`, `getStaff()`, `staffLeave()`, `expiringCertifications()` |
| **3. academics** | academic year, programmes, classes, **timetable**, **curriculum/lesson plans**, **progress** (term submission windows + lightweight in-app progress report for parents — no PDF), calendar | `getClasses()`, `getTimetable(classId)`, `publishedLessonPlans(programmeId)`, `progressWindow()`, `childProgress(childId)` |
| **4. admissions** | applications (pre-enrollment); the **enroll** workflow. Two intake channels: a **public, unauthenticated application form** (parents self-apply) and **staff-entered** by the Branch Admin. Admissions open year-round by default; Admin/Branch Admin may set optional open/close windows | `createApplication()` (callable unauthenticated — rate-limit + validate + spam-guard; **never** trust it for anything beyond creating a pending application), `enroll(applicationId)` → calls `people.createStudentFromApplication()` |
| **5. attendance** | daily attendance, absence alerts, the **daily care feed** | `markAttendance()`, `childAttendance(childId)`, `getDailyFeed(childId)` |
| **6. communications** | announcements, parent inbox, WhatsApp/SMS, push | `sendAnnouncement()`, `postMessage()`, `notify(userId, payload)` |
| **7. fees** | **read-only** imported CoFee payments/invoices — view + export + **fee dashboards** (collection rate, dues) | `importCoFee(file)`, `feeStatus(childId)`, `feeDashboard(branchId)`, `exportFees(filters)` |
| *xc. admin* (cross-cutting) | export, dashboards, **audit log**, settings, data control — reads all modules, owns `audit_log` | `exportCSV(entity, filters)`, `logAudit(event)`, `dashboard(branchId)` |

**Workflows are functions, not modules:** `enroll`, `rollover`, `migration` orchestrate across modules. **Migration is a one-time import script**, not a module. **Admission ID is dual-path:** migrated students keep their **existing/legacy Admission ID, accepted as-is** (the importer writes it verbatim, never regenerates it — it's the join key back to CoFee and paper records); only **post-go-live** admissions get a system-generated ID.

---

## Build Phases (order = client-visible value)

- **Phase A (~wk 3 — the demo that rebuilds trust):** identity + people + attendance + daily feed.
- **Phase B (~wk 5):** admissions **+ full student lifecycle** (enroll, rollover, TC/Bonafide acknowledgment, discontinuation, re-admission).
- **Phase C (~wk 7):** communications (announcements + inbox).
- **Phase D (~wk 9–10):** academics (timetable + curriculum + **lightweight progress reports & submission windows**) + **CoFee import + fee dashboards** + admin export/data-control.

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
| 6 | **Clerk roles + an `authorize()` function + `guardian_child` joins** as the authorization layer | Role (Clerk) × scope × relationship as `WHERE` clauses — no ABAC/ReBAC engine; RLS optional later | `active` |
| 13 | **Clerk for auth + MFA required for staff roles** | Built-in MFA / RBAC / session controls, fast for a solo dev; MFA is non-negotiable for a portal touching fees, admissions, and child data. Authenticator-app MFA (free, **not SMS**); **email** is the auth channel (invites/sign-in); **SMS is parent-comms only**. Audit logging stays our own | `active` |
| 14 | **Staff records carry certifications+expiry, staff attendance, lightweight leave, exit gates** in V1 | A school needs to track teacher certifications, mark staff attendance, handle leave, and run a proper staff exit from day one — essential additions to the People module | `active` |
| 15 | **TC/Bonafide issued offline; the app only acknowledges + logs them** (admin / branch admin / coordinator) | The school issues the physical certificate; the app records that it was issued, with who/when — **no PDF generation** in V1 | `active` |
| 16 | **Progress reports are lightweight & in-app (no PDF); submission windows toggled by admin/branch admin; formal cards handed over offline** | Parents see a simple in-app progress view; the physical card stays the school's offline artifact — avoids a PDF/authoring engine | `active` |
| 17 | **Role is an assignment, not a column** — `user_role` (role × branch, many per user) + `class_teacher` + `guardian_child`; top-down invite chain (Admin→Branch Admin→staff; system→parent); **≥2 admins**; **de-provision on exit** (deactivate, never delete) | Cleanly supports multiple people per role, multi-role, multi-branch, and staff-as-parent with no rewrite; removes single-owner lock-in. Full spec: `modules/identity-access.md` | `active` |
| 18 | **Parents invited at enrollment; they submit sensitive docs; Admin/Branch Admin verify** | Parent app is useful from day one; document verification (Uploaded→Verified) gates trust in the sensitive data (Aadhaar/medical/photo). Replaces the old "portal on first payment" gate | `active` |
| 19 | **Step-up re-authentication before sensitive actions — mandatory in V1** | Re-verify (MFA/credentials) before revealing a child's regulated data, data exports, admissions decisions, certificate/discontinuation actions, and user/role management. Limits damage from a hijacked or left-open session; Clerk re-verification supports it. (Promoted from the V1.5/V2 backlog) | `active` |
| 7 | **Append-only `audit_log`** (one plain table, no hash chain) | Accountability without tamper-evidence theater; backups + restricted writes suffice at this scale | `active` |
| 8 | **CoFee = read-only import + view + export + fee dashboards** in V1 | The data matters; the billing engine doesn't yet. Unifies students+attendance+fees and gives the owner a high-ROI collection/dues dashboard over imported data — cheap, no billing engine | `active` |
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
| Supabase Auth as the auth provider | Chose **Clerk** for stronger built-in MFA + RBAC + session controls; Supabase stays the DB + storage |
| Building our own MFA / session logic | Clerk handles it; rolling your own auth under deadline is the wrong risk |
| PDF generation for certificates & progress cards (V1) | TC/Bonafide and progress cards are issued/handed over **offline**; the app acknowledges them or shows a lightweight in-app view |
| SMS for auth/MFA | Email is the auth channel; SMS is reserved for parent communications (cost + clarity) |
| Building 16 code modules from the feature list | Those are workflows/features; code is organized by **data owner** (~7 modules), not by workflow |

---

## Open Questions

- [ ] CoFee export schema stability + import cadence — confirm before building module 7 (parser spec already in `ENGINEERING-SPEC.md` → CoFee Data Dictionary)
- [ ] Absence alerts: SMS (MSG91, costs money) vs rely on push + WhatsApp deeplink (free)?
- [x] Admissions: public online application form, or staff-entered only for V1? → **Both.** Public unauthenticated form (parents self-apply) **and** Branch Admin direct entry. Year-round by default; optional open/close windows.
- [ ] Collect the child's Aadhaar at all in V1? (data minimisation — no V1 function needs it)
- [ ] Confirm a demo date with the client (~3 weeks out) — this is the trust-rebuilding action
- [ ] App-store presence (Capacitor wrap) needed in V2, or does the PWA suffice?
- [ ] DPDP stance on Clerk holding auth identities (email/phone) on US infra — acceptable now; confirm with counsel if residency tightens

---

## Notes for AI Agents

- **Respect module boundaries** — no reaching into another module's internals; import only its `service`, never its `repo` or tables.
- **Add new features inside the relevant module**, not at the top level. If it spans modules, use a service call or an event, not a direct import.
- **Honor the 5 primitives** on every new table/feature: `branch_id`, PII-in-its-own-table, `guardian_child` scoping, `authorize()`/RLS, payment ledger for money.
- **Access:** call `identity.getCurrentUser()` and `identity.authorize()` — never decode a JWT or check roles inline. **Deny-by-default.**
- **Student creation:** only `people.createStudentFromApplication()` — `admissions.enroll()` calls it; never INSERT students elsewhere.
- **Admission ID:** auto-generate **only** for new post-go-live admissions. **Migrated students keep their existing Admission ID exactly as given** — the migration importer must accept and preserve it (it's the link to CoFee + legacy records). Never overwrite or regenerate it (IDs are immutable per §9.1).
- **CoFee data is read-only in V1** — never write/mutate imported fee rows.
- **Audit:** call `admin.logAudit()` on sensitive actions (sensitive-data reads, exports, deletes, overrides).
- **When tempted to add machinery** (vault, hash-chain, ReBAC, delivery state-machines): stop — it's in `ENGINEERING-SPEC.md` as backlog on purpose. Build the lean version here unless a phase explicitly calls for more.

<!--
  Add more context here as the project grows.
  e.g. "identity.service exposes getCurrentUser() — use that, never decode JWT directly"
  e.g. when fees go native, flip Decision 8 to `changed` and add the billing-engine decision.
-->

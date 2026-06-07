<!-- ABOUT
File: DOMAIN_OVERVIEW.md
Purpose: Happy Feet application manual — business identity, stakeholders, business model, operational cycle, and how the product is built and delivered. Written for the stakeholders paying for it, and as high-context grounding for developers and AI build tools.
Scope: Business purpose, stakeholder roles, business model, seasonal cycle, domain boundaries, and delivery/rollout. Conceptual, not implementation detail.
Dependencies: AI-DECISIONS.md (the Version 1 build plan and module structure), ENGINEERING-SPEC.md (the longer-term engineering backlog)
Stage: Living manual — Happy Feet application
Version: 1.5
Last Updated: 2026-06-07
Status: Active — guiding V1 build
-->

# Domain Overview — Happy Feet School ERP

> About This Document: This is the product manual for Happy Feet — a single platform that unifies school operations across admissions, fees, attendance, and communication, so every role works from the same live data instead of disconnected registers, spreadsheets, and WhatsApp threads. This document covers: who the stakeholders are, what the system does and what it deliberately does not do, how it is built, and how it is delivered. It describes the complete application — every module and capability planned across all releases. Version 1 (currently in development) delivers the core foundation: secure login, student & staff records, daily attendance with the parent daily feed, admissions & enrollment with student lifecycle, parent communications, lightweight progress reports, and CoFee fee import with dashboards. Heavier capabilities — native in-app billing, transport management, meal management, and advanced analytics — are designed and documented here but scheduled for later versions. Features not yet built are clearly marked throughout.

**Phase:** 1 — Domain Discovery

---

## How It's Built & Delivered

**One installable app.** Happy Feet is delivered as a single app your staff and parents install on their phones — add it to the home screen and open it like any other app. You **log in once and stay signed in**, so it's a tap to open, not a website-and-login every time. It works on patchy connectivity and can send push notifications, and the same app runs on a laptop for office use. **Staff sign in with two-factor authentication** to protect sensitive areas (fees, admissions, child records). All data is stored and backed up **in India**.

**The complete application in this manual is delivered in stages:** 

| Phase | What you get |
|---|---|
| **A** | Staff & parent login, student records, daily attendance, and the parent daily feed |
| **B** | Admissions, enrollment, and student lifecycle (Transfer/Bonafide certificates, rollover, exit) |
| **C** | Communications — announcements and the parent inbox |
| **D** | Timetable & curriculum, lightweight progress reports, CoFee fee import & export **with fee dashboards**, and admin data control |

**Fees in Version 1:** the school keeps using **CoFee** for fee collection; the app **imports CoFee's data** so you see students, attendance, and fees together in one place, with export for your records. Native in-app billing is planned for a later version.

**The rest of this manual** describes the complete application, including deeper compliance and automation that is sequenced *beyond* V1 as the school grows. 

---

## 1. Core Business Definition

Happy Feet is an administration platform for a single privately owned preschool in Hyderabad, India. It currently serves 105 enrolled students across one active branch, with the architecture designed to support multiple branches as the school scales. 

Programmes currently offered are Play Group, Nursery, PP1, PP2, and Day Care — covering children aged approximately 18 months to 6 years — with the system designed to accommodate new programmes as the school grows. Operations supported include online admissions, fee collection, class assignments, attendance, and daily child activity.

The school's continuity depends on three core operations: enrolling children and collecting fees, delivering daily care and education, and maintaining parent trust. Of these, parent trust is the most critical — safety incidents, communication gaps, or financial opacity can damage it permanently.

---

## 2. Operational Gaps by Stakeholder

A preschool operates across four broad areas on any given day: admissions and enrollment, fee collection and financial tracking, classroom operations — attendance, activities, and child welfare — and parent communication. Each of these areas is currently managed manually at Happy Feet, creating visibility gaps, reconciliation errors, and communication breakdowns that grow harder to manage as enrollment increases.

**Owner / Admin:** No real-time visibility; relies entirely on verbal staff updates for admissions, fees, attendance, and incidents.

**Branch Admin (Principal):**
- Admission forms are paper-based — unsearchable, not portable across years
- No reliable audit of fee payment status or outstanding balances
- Staff leave handled informally — no balance tracking or approval workflow
- Parent communication is unstructured WhatsApp with no log or accountability

**Teacher:** Paper attendance registers with no management rollup, no parent notification, and no structured channel for welfare concerns or daily child activity recording.

**Coordinator:** Gate release and visitor log managed on paper with no audit trail; parent communication routed through personal WhatsApp with no log or accountability; intake documentation handled manually with no structured handoff to Branch Admin.

**Accountant:** Fee tracking in CoFee (the school's current payment software) is disconnected from student records; reconciliation requires manual cross-referencing across systems.

**Parent / Guardian:**  Informal WhatsApp updates — no structure, no archive, no guaranteed delivery; no self-service view of attendance, progress, or fees.

**Child / Student:**  Not a system user. Central entity around which every safety rule, health record, and daily log is organised.

---

## 3. Core Value Proposition by Stakeholder

| Stakeholder | Core Value |
|---|---|
| Owner / Admin | Real-time operational visibility across the system |
| Branch Admin | Complete daily admin without paper or WhatsApp intermediaries |
| Teacher | Structured attendance, activity logging, and progress submission within class scope |
| Coordinator | Structured front-desk intake and an accountable, logged communication channel replacing personal WhatsApp (digital visitor log and gate verification arrive on the roadmap) |
| Accountant | Consolidated fee visibility — CoFee data imported alongside students and attendance, with collection and dues-aging dashboards — and billing communication in one place |
| Parent | Structured, permanent view of child's daily school activity from one screen |

---

## 4. Stakeholders

### Primary (direct system users)

| Actor | Type | Role in Business |
|---|---|---|
| Admin | Human | School owner or super-administrator; full authority across all branches |
| Branch Admin | Human | Principal or branch head; primary operational actor for a single branch |
| Teacher | Human | Class teacher; scoped to assigned class slots. Three slot types — Primary, Assistant, Temporary. Primary and Assistant carry the full Teacher permission set (Primary is the authoritative attendance marker); Temporary/substitute carries a reduced profile — attendance and daily log only, no full medical/welfare history or progress authoring — to limit sensitive-data exposure for rotating staff |
| Coordinator | Human | Admin assistant or front-desk; intake (collects and uses student/staff documents to process and coordinate tasks, access-logged, cannot delete), gate operations, communication between actors |
| Accountant | Human | Finance staff; fee management, CoFee import, billing communications |
| Transport Staff *(roadmap — ships with Transport)* | Human | Driver/attendant on a school-owned vehicle; marks boarding/drop on an assigned route and sees minimal child info (name, photo, stop, emergency contact, allergy alert) — nothing else. **Not a V1 role** — arrives when transport management is built |
| Parent / Guardian | Human | Child's family; read-only, child-scoped access. A child may have multiple guardians, each with their own login and per-relationship capability flags — can-collect, can-receive-communications, payer role, and an access level including "excluded" for custody/safeguarding cases |

**Access model:** the five V1 staff roles (Admin, Branch Admin, Teacher, Coordinator, Accountant) and Parent are fixed permission bundles (Transport Staff is a roadmap role; custom roles are a V2 evolution). Authorisation combines **role** (what action), **scope** (which branch / class / route), and **relationship** (guardian-of / teacher-of) — access follows the task and the data boundary, not the role label alone. **Any role can be held by multiple people** (several admins, multiple teachers, and so on). Staff join by **email invitation, top-down** — Admin invites Branch Admins; each Branch Admin invites their branch's staff — parents are invited automatically when their child enrolls, and a staff member who leaves has their access revoked. Admin and Branch Admin authority can be time-boxed-delegated to a deputy, and a read-only Viewer/Auditor grant exists for an external accountant. See §9.16.

### Secondary (affected, not direct users)

| Actor | Type | Role in Business |
|---|---|---|
| Child / Student | Human | Subject of all records; never a system user |

### System and External Actors

| Actor | Type | Role |
|---|---|---|
| CoFee | External system | Temporary V1 payment-collection source feeding the Happy Feet canonical ledger; retired at V2 (see §9.9) |
| Razorpay | External payment gateway | Ad-hoc payment links in V1; full integration in V2 |
| WhatsApp (deeplinks) | External platform | Manual tap-to-send parent communication in V1 |
| SMS Gateway (e.g. MSG91) | External service | **Parent communications only** — general parent notifications and automated absence alerts to the parent's primary registered phone number after attendance verification. Sign-in and invitations use **email**, not SMS |
| Notification engine | Internal | Fires a defined set of named system notifications on defined trigger events — full list to be documented in the Communication module specification |
| UDISE+ | Government system | National school registry; annual school reporting obligation |

---

## 5. Business Model

| Revenue stream | Billing model | Key rule |
|---|---|---|
| Academic programmes (Nursery, PP1, PP2, Play Group) | Term-based | Full term fee even for mid-term admissions; no pro-rating |
| Day Care | Plan-based (see Day Care Billing Plan) | Staff-recorded check-in/check-out timestamps support operational tracking and billing calculations where required by the assigned Day Care Billing Plan (Monthly, Weekly, Day-wise, Hour-wise, Term-wise) |
| Transport (optional, school-owned) | Fixed monthly by zone | Per distance band and trip type; opt-in per student. **In V1 a lightweight optional fee item** — configurable per branch / programme / student by Admin or Branch Admin — **not a dedicated transport module** |
| Meal Plan (optional, configurable) | Plan-based | **In V1 a lightweight optional fee item** — configurable per branch / programme / student by Admin or Branch Admin; inactive by default, activated only when the branch begins offering meals — **not a dedicated meal-management module** |
| Event / one-off | One-off fee | Annual Day, field trips, etc. |

**Version 1:** Fee collection continues on **CoFee**. Happy Feet **imports CoFee's data (read-only)** through an idempotent, reconciled import, so the school sees students, attendance, and fees in one place, with export for its records. The design centres on Happy Feet eventually becoming the single canonical ledger (the source of payment truth) — every imported payment carries its source and reference so figures reconcile — but the native billing engine (derived invoice states, in-app collection, Razorpay links) is **phased in after V1**, not built day one. See §9.9.
**Later:** Happy Feet owns the full billing flow — a payment gateway (Razorpay) integrated behind its own ledger — and CoFee is retired. Because the data model already records every payment with its source and reference, that switch needs no migration and no data backfill.

Student categories (Standard, Staff Child, Subsidised) determine the applicable fee structure per student. Payer routing is a separate student-level attribute assigned at admission: the primary payer is the parent or guardian by default; a secondary payer (employer, trust, or sponsor) can be added for students whose fees are covered by a third party. The fee structure is the same regardless of who pays.

The fee structure captured at a student's enrollment is the immutable contractual basis for their billing and is not overwritten when later years' fees change. Tuition is largely GST-exempt, but ancillary services (transport, meals, materials, events) may attract GST — each fee head carries a taxable flag and rate, surfaced on invoices and parent receipts. Parents can view fee status, payment history, and downloadable receipts (important for reimbursement and corporate-sponsored payers). Any fee reduction (waiver or concession) or refund requires a second-actor approval — the actor who records money is not the one who waives it.

---

## 6. Seasonal Business Cycle

3 terms per year by default. The annual cycle drives nearly every operational workflow.

| Phase | Key Events |
|---|---|
| Pre-Year Setup | 9-step academic configuration (must complete in sequence): (1) Create academic year with start/end dates; (2) Set term dates and progress card submission windows per term; (3) Configure programme definitions and demographic field requirements; (4) Create classes with capacity and teacher slots; (5) Configure fee structures per programme and student category — define base fees, installment schedules, and category-based variants (Standard, Staff Child, Subsidised); payer routing (parent, employer, or sponsor) is assigned at the student level at admission, not at configuration time; (6) Configure transport zones (skip if not offered); (7) Enter school calendar; (8) Configure weekly timetable per class; (9) Define milestones per programme and specify required document types per programme. **Note:** Step 4 requires teacher profiles to already exist in the system. Teacher profiles are created during Phase 0 Master Data Setup, which runs inside Migration Mode at go-live — not as part of this annual sequence. See the Migration Window row below. |
| Admissions (year-round) | Admissions are open by default at all times; Admin or Branch Admin may optionally configure open/close dates to restrict intake to a defined period — if no window is configured, admissions remain open year-round. Applications arrive two ways: **parents apply themselves from a public application URL** (no login needed), or the **Branch Admin enters an application directly** on their portal. Flow: Application → document review → programme assignment → enrollment → **parent portal activation (the parent is invited by email at enrollment)** → **parents submit/complete their child's sensitive documents in the app, which Admin or Branch Admin verify** |
| Each Term (×3) | Attendance marking, daily activity logging, fee collection, parent communication |
| Progress Reporting Window (×3) | Teacher drafts/submits → Branch Admin reviews and publishes |
| Year-End Rollover | Branch Admin reviews Active students, acknowledges outstanding dues → Active → Completed Programme; switches reset |
| Migration Window (Entry/Exit) | **Phase 0 — Master Data Setup** (one-time per branch, runs inside Migration Mode at go-live): Create Admin and Branch Admin accounts; create staff profiles — teachers, coordinators, accountants; assign system roles and branch binding; enter existing student records and opening balances; import CoFee data. Phase 0 is a prerequisite for the annual Pre-Year Setup sequence. It runs once at go-live under Migration Mode controls (relaxed validation, workflow suppression, migrated record flagging). Once Branch Admin confirms all records are entered and verified, Migration Mode exits and the annual cycle begins. See §9.4 for the full Migration Mode specification. Mid-year staff changes are operational actions, not setup steps: a new teacher's profile is created in M4 at any time and can be assigned to a class slot immediately after; a departing teacher's slot becomes vacant and must be reassigned by Branch Admin — the timetable flags the gap automatically. These are covered by the staff leave and exit workflow in M4. |


**Calendar constraints:**

- Academic Year — three operational states:

  - **Upcoming:** Pre-Year Setup is complete and the year is configured; accepts advance admissions before the year goes live; timetables, fee cycles, and attendance are not yet active.

  - **Active:** The current operational year; all operations — timetables, terms, fee cycles, and attendance — are live and scoped to this year. Only one academic year can be Active at a time. Admin manually promotes an Upcoming year to Active; the transition is logged with a timestamp and the confirming admin's identity.

  - **Closed:** Year-end rollover is complete; all records are read-only and archived. Admin manually closes the Active year after confirming rollover is complete; the closure is logged.

  Activation is never automatic. On the configured start date, if the Upcoming year has not yet been activated, the system prompts Admin as a reminder — but the decision to go live always rests with Admin, not the clock.

- Student Rollover: Students advance to their next class when the Branch Admin triggers the year-end rollover workflow — in step with the Admin manually promoting the new year from Upcoming to Active. The rollover is not automatic; Branch Admin reviews Active students, acknowledges outstanding dues, and confirms transitions before the new year goes live.

- Term Structure: The academic year is divided into terms, with start and end dates configured by the Branch Admin. Progress card submission windows open and close per term; no submissions are accepted outside the defined window.

- Timetable & Class Assignments: Weekly class schedules and class assignments are configured per branch and scoped to the active academic year and term — defining which teacher covers which class and when. They can be changed at any time by **Admin, Branch Admin, or Coordinator** (a mid-term staff swap or substitution takes effect immediately); **Teachers see their own schedule but do not edit it**.

- Holidays: Entered by Admin; block attendance for all roles on the marked date with no override permitted.

- Admission Windows (optional): Admissions are open year-round by default. If Admin or Branch Admin configures an open/close window, the system enforces it — applications submitted outside the defined window are not accepted. If no window is configured, this constraint does not apply.

- Fee Billing Cycles *(V2 — native billing)*: Fee schedules operate on configured cycles — monthly, termly, or annual — with the system generating invoices on the cycle dates set per programme or plan. **Not built in V1** — V1 has no system-generated invoices; CoFee owns the billing cycle and Happy Feet imports the result read-only. Configurable cycles arrive with native in-app billing in V2.

- Staff Leave: Leave balances and approval workflows are tracked within the system. Approved leave is reflected against the timetable to flag coverage gaps.

- Communication: Announcements can be composed and scheduled for a future date and time; the system publishes them automatically at the configured moment. Absence SMS notifications fire automatically after daily or slot-based attendance is marked and verified; delivery is via the SMS gateway and is not configurable by parents. Every notification carries a tracked delivery state (queued → sent → delivered/read, or failed/expired); safety-critical notifications are sent on more than one channel and escalate to a staff action item until delivery is confirmed or acknowledged — see §9.12.

- Reporting: Dashboards are available on demand. Compliance digests and governance reports are generated on a configured schedule — weekly, termly, or at academic year close.

- Day Care: Staff-recorded check-in/check-out timestamps support operational tracking and billing calculations tied to the assigned Day Care Billing Plan.

---

## 7. Domain-Specific Constraints

### 7.1 Child Safety as a Non-Negotiable Operating Requirement

Children aged 18 months to 6 years cannot advocate for themselves. This makes safety rules in a preschool context non-negotiable invariants — they are not configurable, cannot be bypassed, and override every other operational consideration. Five rules operate at this level:

- **Authorised pickup** — the system blocks release to anyone not authorised to collect a child. Authorisation has three paths: a standing pickup list for regular collectors (verified by persistent PIN/QR and photo); a parent-issued one-time code generated in the parent app for a one-off pickup; and, where neither applies, a two-actor override — Branch Admin authorisation plus parent phone/OTP confirmation and a captured photo. The override is the only path for an unauthorised person and always generates both a governance log entry and a compliance entry. Every enrolled child must have at least one active standing-list entry at all times. A guardian flagged "excluded" for custody or safeguarding reasons is removed from the authorised list and cannot collect the child (the exclusion is logged with a recorded legal basis).
- **Allergy protocol** — when a meal entry involves a child with an active allergy flag, the Teacher must explicitly confirm the allergy protocol before proceeding. The confirmation is recorded as an immutable compliance entry in the audit log (surfaced in the compliance view — see §9.10).
- **Welfare concern pathway** — a Teacher-raised welfare concern notifies the Branch Admin immediately; review is required within 3 school days. If unreviewed, a reminder fires automatically and then escalates to Admin. The concern moves through explicit states — Raised → Acknowledged → Under Review → Resolved, Escalated, or Closed-no-action (with a recorded reason) — so every concern reaches a defined terminal state rather than remaining open indefinitely. The record is permanent, immutable, and never visible to parents.
- **Incident reporting** — incidents are formally recorded by the Branch Admin, who decides whether to publish to parents or keep the record internal. Internal notes are never surfaced to parents under any circumstances.
- **Sensitive document handling** — admission documents include government-issued identification, national education identifiers, and medical records. These are subject to strict access control and data privacy rules. See §7.4 for the full data privacy constraint specification.

### 7.2 Day Care Is a Distinct Operational Model

| Dimension | Day Care model |
|---|---|
| Attendance | Standard attendance records apply (Present / Absent / Late / Half-Day) |
| Billing | Governed by the assigned Day Care Billing Plan (Monthly, Weekly, Day-wise, Hour-wise, Term-wise); staff-recorded check-in/check-out timestamps support billing calculations where required by the plan |
| Daily record | Meals, naps, toileting, health checks logged per child |
| Nature | Childcare operations sharing the school platform — not a school attendance model |

### 7.3 Parent Trust Is the Core Product

In a preschool, the primary value delivered to parents is not academic records — it is daily confidence that their child is safe, seen, and cared for. Every parent-facing feature in Happy Feet exists to replace informal, unstructured communication with a structured, auditable information trail.

The current state — ad hoc WhatsApp messages — fails on every dimension that trust requires: no delivery guarantee, no message log, no accountability for what was or wasn't communicated, and no parent-side archive. A parent who misses a message has no recourse. A school that needs to prove it communicated something has no evidence.

Happy Feet replaces this with a single parent-facing channel where attendance is visible daily, welfare updates are structured and timestamped, daily care activity is logged by the Teacher and pushed to the parent, and fee status is self-serve. The result is not just convenience — it is a verifiable, role-controlled communication record that protects both the parent and the school.

### 7.4 Data Privacy as a Non-Negotiable Operating Requirement

Happy Feet collects and stores a category of data that carries legal obligations under India's Digital Personal Data Protection (DPDP) Act. This includes government-issued identification (Aadhaar cards for children and parents), national education identifiers (PEN and APAAR ID), medical records (vaccination history), and passport-size photographs.

Passport-size photographs are stored in V1 as part of the student admission record. Photo sharing and distribution to parents or external parties is deferred to V2 pending DPDP Act compliance review.

Five rules govern this data category without exception:

- **Access control by role and field** — sensitive data is accessible only to roles with an explicit operational need, and access is minimum-necessary at the field level: a Teacher sees a child's allergy/medical-alert flag, not the full medical record. No role can access another role's data boundary.
- **No external exposure** — sensitive records are never surfaced outside authorised system boundaries, regardless of request or context; sensitive fields (Aadhaar, medical records, contact numbers) are never included in any notification, export, or deeplink.
- **Consent as a lifecycle** — collection of APAAR ID and PEN number requires documented voluntary parent consent, recorded per student and never mandatory. Consent is a first-class lifecycle (notice → granted → withdrawn → re-consented), and parents hold data-principal rights — access, correction, erasure, and grievance — handled through a defined flow under a named Data Fiduciary (the school).
- **Data minimisation and erasability** — the system collects only what a defined function needs (collecting a young child's Aadhaar is questioned, not assumed). Regulated personal data is stored encrypted in a vault by reference, so an erasure request can crypto-shred the content while the immutable audit trail keeps a PII-free record that the entry once existed — reconciling append-only immutability (§9.1) with the DPDP right to erasure. See §9.17.
- **Access logging** — every read, export, or print of a sensitive record (Aadhaar, medical/vaccination, PEN/APAAR, passport photo, financial) is recorded in a dedicated access log capturing who accessed what, when, and the access type, so the school can always answer who viewed a child's sensitive data. See §9.10.

All timestamps in the system — audit entries, governance log entries, notification delivery records, and attendance marks — are stored in UTC and displayed to users in the school's local timezone (IST, UTC+5:30). This ensures audit trail integrity across any future branch operating in a different timezone, and means timestamps in exported reports are unambiguous regardless of when or where they are viewed. All personal data is hosted and backed up within India (DPDP / data-localisation), with encrypted backups and a documented recovery posture — see §9.17.

---

## 8. Domain Boundaries

### In Scope — V1

- **Identity and access management** — roles, sessions, permissions, and multi-branch binding; access combines role, scope (branch/class/route), and relationship (guardian-of/teacher-of); multi-guardian relationships with capability flags; time-boxed delegation and break-glass for continuity; MFA for privileged staff and step-up re-authentication before sensitive actions
- **School configuration** — academic year, terms, programmes, classes, calendar, timetable, and developmental milestones
- **Student records** — profiles, submitted documents, medical information, authorised pickup lists, and incident reports; PTM (Parent-Teacher Meeting) notes linked to the child's profile — immutable once submitted, visible to Branch Admin and authoring Teacher only, never visible to parents
- **Staff records** — profiles, attendance, leave balances, certifications, and exit workflow; digital visitor log with entry/exit timestamps and audit trail, replacing the paper-based gate register
- **Admissions and enrollment** — application via a public self-serve URL (or entered directly by the Branch Admin), waitlist, optional admission windows — configurable open/close dates that restrict intake to a defined period; if not configured, admissions remain open year-round — and four defined enrollment conditions (Waitlist is an admissions state managed within this module by Branch Admin — not a standalone module.)
- **Student lifecycle** — status transitions, annual rollover, and Transfer Certificate / Bonafide certificate (a formal letter confirming a child's active enrollment status, commonly requested for bank accounts, travel documentation, or sibling admission applications) handling — these are **issued offline by the school and acknowledged in the app** (admin / branch admin / coordinator), not generated as PDFs
- **Fee management** — **V1: read-only, idempotent reconciled CoFee import (view + export) plus fee dashboards** (collection rate, dues aging) so students, attendance, and fees sit in one place. CoFee remains the billing system of record in V1; Happy Feet mirrors it and does not generate invoices or take payments. **The native billing engine — the canonical financial ledger (single source of payment truth), fee structures, invoice generation with derived states (Draft, Issued, Partially Paid, Paid, Overdue, Void, Credited), configurable billing cycles, Razorpay payment links, Day Care billing plans, parent-facing self-serve fee status with one-tap payment, downloadable receipts and payment history, per-fee-head GST handling for taxable ancillary services, and maker-checker approval on fee waivers and refunds — is designed and documented here but scheduled for V2**, at which point CoFee is retired
- **Attendance management** *(child attendance — staff attendance and leave live under Staff records above)* — Teacher-marked daily attendance, corrections, chronic absence alerts, and real-time staff-to-child ratio monitoring with dashboard visibility and breach alerts (Staff-to-child ratio monitoring is a live function of present-marked attendance — not a standalone feature.); automated SMS notification sent to the parent's primary registered phone number when a child is marked absent — triggered after attendance is collected and verified per day or slot; each class slot has a single authoritative marker (the Primary slot), so concurrent edits by co-slotted teachers are rejected rather than silently overwritten, and the absence SMS fires once on the verified mark
- **Curriculum and lesson planning** — structured lesson plans authored by Teachers, linked to programme and class, and published to parents
- **Progress and assessments** — term-scoped submission windows toggled by admin/branch admin, and a **lightweight in-app progress report for parents**; formal progress cards are handed to students offline (no PDF generation in V1)
- **Meals (lightweight, optional)** — **not a dedicated module in V1.** A configurable optional fee/plan item, set per branch / programme / student by Admin or Branch Admin, inactive by default and activated only if the branch begins offering meals (the school does not currently offer them). **Full meal management — daily menu planning, allergy-flagged meal entry confirmation, consumption tracking — is a V2 module.**
- **Transport (lightweight, optional)** — **not a dedicated module in V1.** A configurable optional fee item, set per student (zone / distance band) by Admin or Branch Admin. **Full transport management — routes and assigned stops, staff-marked boarding/drop confirmation, the Transport Staff role — is a V2 module** (live GPS tracking later still).
- **Communication** — announcements, WhatsApp deeplinks, school inbox, notice board, and a defined set of named system notifications on defined trigger events — full list to be documented in the Communication module specification; each notification carries a tracked delivery state, with safety-critical notifications sent multi-channel and escalated to staff until confirmed (see §9.12). WhatsApp deeplinks are manual tap-to-send and are logged as generated, not delivered
- **Daily Care Log** — structured parent-engagement feed covering meals, naps, activities, and health checks
- **Reporting and governance** — role-filtered dashboards derived from the canonical ledger and live data (owner KPIs: collection rate, dues aging, occupancy, admissions funnel, retention, open safety items), CSV exports, the audit log (the cross-cutting action spine), the governance log and compliance view (filtered projections of it), and the sensitive-data access log
- **Migration Mode** — reversible, per-branch operational state that controls the go-live data entry window. Covers two parallel workstreams: Phase 0 Master Data Setup (Admin and staff account creation, role assignments, branch binding) and existing student record entry and verification. Both workstreams must be complete before Branch Admin can exit Migration Mode and begin the annual Pre-Year Setup sequence. See §9.4 for the full entry/exit specification.

---

### Out of Scope — V2

- Pre-admission enquiry and lead management — handled offline in V1
- Full Razorpay API integration for automated payment collection
- Automated WhatsApp dispatch via Business API — In V1, WhatsApp messages are sent manually by staff via a pre-filled message link (deeplink). Automated dispatch directly from the system requires the WhatsApp Business API, which is deferred to V2.
- Budget management and category-level expenditure tracking
- AI intelligence and predictive analytics layer
- Payroll data export
- Photo sharing and distribution — passport-size photos are stored in V1 as admission records but are not shared or distributed through the system until a full DPDP Act compliance framework is in place
- Full multi-branch management UI
- Retention rate and cohort reporting
- APAAR, PEN, and UDISE+ submission workflows

---

### Permanently Out of Scope

- Historical attendance import from paper registers — a manual summary marker is used at migration
- Programmatic parsing of paper records or legacy spreadsheets — staff are the migration engine
- Payroll processing — export only; payroll computation is handled externally

---


## 9. Critical Domain Characteristics

**9.1 Immutability** — Financial records, audit entries, student profiles, admission IDs, welfare concern records, incident reports, PTM (Parent-Teacher Meeting) notes, and leave records are never overwritten or altered — corrections and status changes are appended as new entries, preserving the complete history. Deletion is governed by the retention periods defined in §9.6. Both a governance requirement and a parent trust requirement. Admission IDs follow a dual-path assignment model: for students migrated from existing records, Admin or Branch Admin enters the historically assigned Admission ID during Migration Mode and the system preserves it as the permanent ID; for new admissions after go-live, the system auto-generates the Admission ID at profile creation. Because records are append-only, a retroactive change driven by a calendar correction — for example, a day reclassified as a holiday — is applied as a system-generated compensating entry that voids and supersedes the original mark, never an in-place edit; the current value is derived from the latest entry. The append-only logs are tamper-evident: entries are cryptographically chained so any retroactive edit or deletion breaks the chain and is detectable (see §9.10).

**9.2 Governance Log** — Every override action (authorised-pickup override, attendance correction beyond 30 days, Bonafide or Transfer Certificate (TC) issuance with outstanding dues, discontinuation acknowledgments, capacity overrides, document waivers, Migration Mode entry/exit) generates a permanent governance log entry visible to Admin only. The governance log is not a separate store — it is a filtered, Admin-only view of the audit log showing only override-class actions (see §9.10 for the full logging model). This makes "exceptions always have a sanctioned path" operationally true. State transitions across the system — student enrollment status, fee payment status, attendance records, application stages — move in one direction only. Reversing a transition requires explicit Admin action and generates a governance log entry recording who reversed it, when, and why. This applies to all lifecycle states: a paid invoice cannot be unpaid, a closed attendance record cannot be deleted, a completed rollover cannot be undone without a logged correction workflow.

**9.3 Multi-Branch ≠ Multi-Tenancy** — The two-branch architecture exists for one known second branch; no per-branch pricing, no tenant isolation question. Multi-branch from day one purely to avoid a future rebuild.

**9.4 Migration Mode Is a Reversible Per-Branch Operational State** — A branch enters Migration Mode when manual entry of existing student records begins, and exits when the Branch Admin confirms all records are entered, verified, and operationally ready; the system then transitions to normal operations. While active: relaxed validation rules allow incomplete records to be saved (fields not yet available from paper registers); migrated records are flagged vs. post-go-live records; automated workflows (chronic absence alerts, fee reminders) are suppressed to avoid false triggers on incomplete data. On exit: all validation rules activate; normal workflows resume; the exit is logged with a timestamp and the confirming admin's identity. Re-entry is permitted if corrections are needed, with a governance log entry recording the reason for reopening.

Phase 0 Master Data Setup — the creation of Admin, Branch Admin, staff profiles, and role assignments — runs inside Migration Mode at go-live. This is a prerequisite for the annual Pre-Year Setup sequence; specifically, teacher profiles must exist before classes can be created with teacher slot assignments in Step 4 of Pre-Year Setup. Phase 0 is a one-time activity per branch and does not repeat annually.

**9.5 CoFee Is a Temporary V1 Collection Source** — CoFee performs payment collection in V1 but is not the source of financial truth; Happy Feet's canonical ledger is (see §9.9). CoFee data enters through an idempotent, reconciled XLSX import: every row is matched to a student by Admission ID and to an invoice, deduplicated by transaction reference, and reconciled against Happy Feet's derived balances — unmatched rows route to a suspense queue rather than being dropped, and dues-gated actions read fee data as of the last import. Because every CoFee payment is recorded in the canonical ledger as it arrives, V2 retires CoFee entirely with no migration, no data loss, and no structural change required.

**9.6 Retention periods** — Retention periods follow a two-tier model: financial records, fee transactions, and governance log entries are retained for a minimum of 7 years in line with standard accounting and tax compliance expectations; student profile records, welfare concern records, and incident reports are retained for the duration of the child's enrollment plus 3 years. PTM (Parent-Teacher Meeting) notes are retained for 2 years after the child's exit from the school — shorter than the student profile retention period, reflecting their nature as operational communication records rather than identity or medical records. Records are not actively deleted after these periods without an explicit Admin action and a logged justification — but permanent retention is not assumed or required by default. The audit, governance, and access logs — the evidentiary spine — are retained for at least the longest applicable record retention (a minimum of the 7-year financial tier, with margin) and are never auto-purged; deletion requires an exceptional, logged Admin action. Safety-tagged records (incident, welfare, pickup) carry a longer provisional retention — the child's age of 18 plus a claims-limitation tail — pending confirmation of the statutory period with Indian legal counsel (see QUESTION-013).

**9.7 Academic Year Is a Three-State Lifecycle, Not a Binary Switch** — Academic years move through three states: Upcoming, Active, and Closed. Only one year can be Active at any time. The Upcoming state allows Pre-Year Setup and advance admissions to proceed before the year goes live — resolving the edge case where a student enrolls for a new year before the current year ends. Admin manually triggers each state transition; transitions are logged with a timestamp and the confirming admin's identity. No state transition is automatic — the system prompts but does not act.

**9.8 The Academic Calendar Is the Single Source of Time for All Operations** — Every operation that touches time in Happy Feet derives its calendar from M2 School Configuration — never from independently stored dates in other modules. This means:

- An attendance record belongs to an academic day as defined in the school calendar, not just a calendar date. If a holiday is entered or corrected, affected attendance records resolve against the updated calendar automatically.

- A fee invoice belongs to an academic term as defined in M2. If term dates are adjusted, invoices remain correctly associated with their term without manual correction.

- Progress card submission windows, admission windows, and timetables are all expressions of the academic calendar defined in M2 — no module maintains its own independent date logic.

This rule prevents the class of errors where one module's dates drift out of sync with another's — a common failure point in systems where each module manages its own calendar independently. The academic-day boundary is defined explicitly (midnight IST): a day-care checkout after midnight and hour-wise billing that crosses midnight resolve against this boundary, not the wall clock.

**9.9 The Financial Ledger Is the Single Source of Payment Truth** — Happy Feet records every money event — from CoFee import, a Razorpay link, or a directly-issued invoice — in one canonical, append-only ledger. Each payment carries its source and an external reference used as an idempotency key, so the same payment is never counted twice across repeated imports; offline cash/cheque payments that lack a gateway reference are keyed by a deterministic composite. Invoice status is derived from applied payments (Draft, Issued, Partially Paid, Paid, Overdue, Void, Credited) — never a hand-set flag — and dues-gated actions (Transfer Certificate, Bonafide, rollover) read the derived outstanding balance against the latest import. A payment's collection (gross — what the parent paid) and settlement (net — what reached the bank after gateway fees) are recorded as distinct events; advance payments are held as unapplied credit until applied to an invoice; refunds are appended as compensating entries, never deletions. Invoice and receipt numbers are namespaced so CoFee-imported and Happy Feet-native records never collide, and tax receipts are gapless and sequential. Payment confirmation from a Razorpay link arrives via a verified, idempotent webhook — so a link payment reconciles into the ledger without manual entry. *In Version 1 this is realised as a **read-only import of CoFee data** (view + export) plus **fee dashboards** (collection rate, dues aging) over that data; the derived-state ledger and in-app collection phase in with native billing.*

**9.10 Logging Model: One Audit Spine, Filtered Views** — The system keeps one canonical, append-only audit log recording every write across every module and role, including Admin, with actor, branch, before/after state, and reason where required. The governance log and the compliance view are filtered projections of this single spine — not separate stores — which keeps compliance evidence consistent. A separate access log records reads, exports, and prints of sensitive data (Aadhaar, medical, PEN/APAAR, photo, financial), since reads have no before/after state and carry their own DPDP obligation. All are tamper-evident: entries are cryptographically chained so any retroactive edit or deletion is detectable, and the chain head is anchored externally in the monthly governance digest. Note: because Branch Admins can read their own branch's audit log, the Admin-only governance log is a curated cross-branch view, not a redaction boundary. (Engineering note: per-entry chaining serialises appends; at scale this moves to per-branch chains or periodic batch anchoring rather than a single global chain.)

**9.11 Student Lifecycle Has Explicit, Named States** — A student moves through an admissions track (Enquiry → Applied → Under Review → Waitlisted → Offered → Accepted, or Rejected / Application-Withdrawn / Offer-Expired) and, after enrollment, a student track (Active → Promoted/Rolled-over, or the terminal states Completed Programme, Discontinued, and Transferred). Terminal students cannot be rolled over; their parent portal becomes a read-only archive for the retention period. Re-admission links to the historical record and preserves the original Admission ID rather than creating a new identity.

**9.12 Notification Delivery Is Tracked; Reach Is Guaranteed by Escalation, Not Assumption** — Every notification has a tracked delivery state (queued → sent → delivered/read, or failed/expired), using provider receipts where the channel supports them; WhatsApp deeplinks are logged as generated only, since manual tap-to-send cannot be confirmed. Notifications carry a priority class: standard notifications respect parent opt-out; safety-critical notifications (portal invite, enrollment, Bonafide, incident publication, pickup-list change, absence) bypass opt-out, are sent on more than one channel, and are not considered complete until delivery is confirmed or acknowledged — otherwise they escalate to a staff action item. This replaces the unenforceable promise that a message "always reaches" a parent with an enforceable one: it is tracked and escalated until confirmed. Opt-out bypass (a preference rule) and delivery guarantee (a reliability mechanism) are distinct concerns.

**9.13 Offline-First Capture for Daily Operations** — Attendance and daily-care logging work without connectivity: entries are captured locally and synced on reconnect, with each record showing its pending-sync state and conflicts resolved by the single-authoritative-marker rule (§8 Attendance management). This is a first-class architectural constraint, not an enhancement — in a setting where connectivity is intermittent it is the difference between front-line adoption and abandonment.

**9.14 Capability Modules vs. Bounded Contexts** — The sixteen functional modules are a capability catalog for stakeholders and traceability. For data ownership, the system resolves to roughly twelve bounded contexts (Identity, School Configuration, Student including lifecycle, Admissions as a separate context, Staff, Finance, Attendance & Safety, Academic, Daily Care, Transport, Communication, Reporting), with Migration Mode as a cross-cutting state rather than a context. Admissions is deliberately kept separate from Student Records so that transient applicant data never enters the permanent, immutable student store. The build target is a modular monolith, so capability modules share aggregates through a domain layer rather than reaching into each other's data.

**9.15 V1 Complexity Is Matched to Operating Maturity** — The governance, immutability, and audit machinery is real but runs as invisible infrastructure. The front-line V1 surfaces are the parent feed, attendance, fees, and communication; governance logs, audit, and Migration controls live in an Admin-only back office. The system ships with templates and sensible defaults and is rolled out parent-app-and-attendance first, so a school moving off paper is not asked to operate enterprise machinery on day one.

**9.16 Access Is Role × Scope × Relationship** — Authorisation combines what a role may do, the data scope it is bound to (branch, class, route), and the relationship that connects an actor to a child (guardian-of, teacher-of) — so access follows the task and the data boundary, not the role label alone, and is deny-by-default. Field access is minimum-necessary (a Teacher sees the allergy flag, not the full medical record). The five V1 staff roles and Parent are fixed permission bundles (Transport Staff arrives with transport management); custom roles are a V2 evolution composed from the same permission primitives. A child may have multiple guardians, each with capability flags including an "excluded" level for custody/safeguarding (logged, with a recorded legal basis). Continuity of authority is preserved by time-boxed delegation to a deputy and a logged break-glass path for emergencies; privileged staff use MFA and re-authenticate (step-up) before sensitive actions such as revealing a child's regulated data or issuing a certificate. Separation of duties is enforced — the actor who drafts is not the one who publishes, and the actor who records money is not the one who waives or refunds it.

**9.17 Data Protection, Erasability and Residency by Design** — Regulated personal data (Aadhaar, medical records, photographs) is stored encrypted in a vault and referenced by token; the immutable audit and event chains never hold raw sensitive data. An erasure request crypto-shreds the vaulted content while leaving a PII-free tombstone — reconciling append-only immutability (§9.1) with the DPDP right to erasure. All data is hosted and backed up within India, with encrypted backups, a documented recovery posture (point-in-time recovery, defined RPO/RTO), and operational monitoring of the integration edges (payment, import, SMS, sync) distinct from the business audit log. No personal data appears in application or error logs.

**9.18 Exactly-Once and Conflict-Safe by Default** — Every state change driven by an external event or a retry — a payment webhook, an SMS send, a replayed offline action, an imported row — carries an idempotency key and is applied exactly once, so retries and re-imports never double-count. Operations that compete for a scarce resource resolve atomically: a class seat is allocated by reservation, not a read-then-write check, so two simultaneous enrolments cannot both take the last seat without a logged override; concurrent attendance edits resolve through the single authoritative marker (§8 Attendance management).

---

## 10. Assumptions and Open Questions

**Assumptions (inferred — require client confirmation):**
- [INFERRED] No separate admission fee; fees are programme-based term installments. Confirm: is there any registration or admission deposit collected separately from term fees?
- [INFERRED] Second branch is under the same ownership and shares the same fee structures and programme definitions as Branch 1. Confirm: will Branch 2 have independent fee structures or programmes?
- [INFERRED] APAAR/PEN registration workflow is not planned for V1 — noted as a compliance reality only. Confirm: does the school intend to collect and submit APAAR/PEN data to government systems in V1 or V2?
- [INFERRED] Transport is optional per student and billed separately from academic fees. Confirm: are there students who receive transport as part of a bundled programme fee?
- [INFERRED] Meal management is configurable per branch — not all branches will activate it. Confirm: does Branch 1 require meal management at go-live, or is it deferred?

**Open questions (require explicit client decision before build):**

1. QUESTION-001 — What is your preschool's current registration status on the UDISE+ portal, and have you already started collecting parent consent for generating student APAAR IDs?
2. QUESTION-002 — For students on the waitlist, does the school communicate a position number to parents, or is the waitlist managed silently by Branch Admin only?
3. QUESTION-003 - When a child is marked present for Day Care, does the school need to know and record what that child ate that day — and will that ever affect what they are charged?
4. QUESTION-013 — What is the statutory retention period for child-safety records (incident, welfare, pickup) under Indian law? A provisional default of the child's age 18 plus a claims-limitation tail is in use pending confirmation with legal counsel.
5. QUESTION-014 — Does CoFee guarantee a stable, unique reference for every offline (cash/cheque) payment? If yes, it becomes the import idempotency key; if not, Happy Feet relies on a synthesised composite key, which is weaker against legitimate same-amount, same-day duplicates.
6. QUESTION-015 — What is the authoritative list of CoFee fee heads in use, so the import can map free-text fee heads to canonical ones? Unmapped heads route to the suspense queue.
7. QUESTION-016 — Should parent-facing dues and the owner collection dashboard reflect gross amount collected (what the parent paid) or net amount settled (what reached the bank)? Default assumption: dues = gross, cash reconciliation = net.
8. QUESTION-017 — When a guardian is excluded for custody/safeguarding, what legal basis must the school record (e.g., a court order), and who may set or reverse the exclusion?
9. QUESTION-018 — May a Temporary/substitute teacher raise a formal welfare concern, or is that reserved for Primary/Assistant?
10. QUESTION-019 — Does the school need read-only access for an external party (chartered accountant, investor, owner's read-only device)? If yes, what scope and duration?
11. QUESTION-020 — Are bus drivers/attendants employees who hold logins and mark boarding/drop themselves, or will an existing role mark it from a shared device?
12. QUESTION-021 — Should the system collect the child's Aadhaar at all in V1? No V1 function requires it, and collecting a young child's Aadhaar is significant DPDP liability.

---

## Summary

Happy Feet is an administration platform for a 105-student privately owned preschool in Hyderabad on an annual 3-term cycle, served by five staff roles and parents across sixteen integrated modules (resolving to roughly twelve bounded contexts plus a cross-cutting Migration Mode). The defining characteristic is the age group (18 months–6 years): safety rules — authorised pickup verification, allergy alert surfacing, welfare concern flagging, incident reporting — are non-negotiable and cannot be overridden without a permanently logged record. Two further pillars anchor the design: a single canonical financial ledger that is the source of payment truth (CoFee is a temporary V1 collection source, retired at V2), and one tamper-evident audit spine from which the governance and compliance views are filtered. Access is modelled as role × scope × relationship with multi-guardian custody handling; regulated personal data is vaulted and erasable; and all data is hosted within India. The application is delivered as a single **installable app** (a PWA — added to staff and parent phones, signed in once) and built in focused **phases** — attendance and the parent feed first, then admissions, communications, and academics with CoFee fee import — with fees continuing on CoFee in Version 1.

---

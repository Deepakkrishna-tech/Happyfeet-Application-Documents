<!-- ABOUT
File: BUSINESS_CAPABILITY_MAP.md
Purpose: Maps all 13 business capabilities across four tiers to define what Happy Feet must be able to do and the business outcome each capability delivers.
Scope: 13 capabilities (C1–C6 Core, S1–S3 Supporting, A1–A2 Administrative, G1–G2 Compliance) with sub-capabilities and outcomes per tier; excludes process flows, role authority, and technical design.
Dependencies: DOMAIN_OVERVIEW.md
Stage: Stage 2 — Business Capability Mapping
-->

# Business Capability Map — Happy Feet School ERP

> **About This Document:** This document catalogues everything the Happy Feet ERP must be able to do, organised into 13 named capabilities across four tiers (Core, Supporting, Administrative, and Compliance). Each capability entry states what the system does and what business outcome it delivers. Read this if you need to understand the full scope of system functionality; you can skip it if you are focused on a specific process flow or role and already know which capabilities are relevant.

**Stage:** 2 — Business Capability Model

---

## Reading Guide

Capabilities are grouped into four tiers by business criticality:

- **Tier 1 — Core:** The school cannot operate without these.
- **Tier 2 — Supporting:** These enable Tier 1 capabilities to function correctly.
- **Tier 3 — Administrative:** These keep operations running and give leadership visibility.
- **Tier 4 — Compliance & Governance:** These meet legal, safety, and accountability requirements.

---

## Tier 1 — Core Capabilities

### C1. Student Intake & Enrollment

The school must formally accept children and establish their operational records before any other function applies to them.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Admissions Processing | Accept applications via public form or walk-in; assign programme and class; verify documents against programme requirements; waive individually with a recorded reason. **Admission ID assignment:** for students migrated from existing school records (pre-go-live), Admin or Branch Admin manually enters the student's historically assigned Admission ID during Migration Mode — the system preserves it as the permanent ID; for new admissions after go-live, the system auto-generates the Admission ID at profile creation. In both cases the ID is permanent and never changes. Admin/Branch Admin is responsible for ensuring migrated Admission IDs are unique and consistently formatted before entry; the system enforces uniqueness at save time. | School has a searchable, auditable record of every child who has ever applied; Admission IDs for migrated students match the school's historical records, ensuring CoFee import matching and continuity across all prior documentation |
| Enrollment Execution | Enforce four independent enrollment conditions (programme assigned, documents cleared/waived, class capacity available/override recorded, at least one active pickup list entry); snapshot fee structure at enrollment | Enrollment cannot proceed without confirming child safety prerequisites; fee terms are locked at the moment of enrollment |
| Waitlist Management | Per-class ordered waitlist for approved applicants when capacity is full; Branch Admin notifies parents and proceeds when a slot opens | No approved applicant is silently lost when a class fills |
| Re-admission | Reopen an existing profile rather than creating a new application; retain same Admission ID and full prior history; outstanding dues persist | Returning students enter the system with continuity of record; prior debt is not erased by re-entry |
| Parent Portal Activation | Triggered by Accountant recording the first payment — not by enrollment completion | Parent access is tied to a financial act, not an administrative one; portal is available from the first payment forward |

---

### C2. Student Lifecycle Governance

The school must track every student's operational state from enrollment through departure and ensure transitions are intentional, recorded, and irreversible where appropriate.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Status Management | Five mutually exclusive statuses: Active, Discontinued, Completed Programme, Bonafide Issued, Transfer Certificate Issued; three per-student lifecycle switches (billing, attendance, parent portal) independently controllable with logged reasons | Every student has one unambiguous operational state at all times |
| Year-End Rollover | Manually triggered preview-and-confirm workflow; Branch Admin reviews all Active students, adjusts class assignments, acknowledges outstanding dues per invoice; all Active students transition to Completed Programme; switches reset to ON | Academic year closes cleanly with no automatic transitions; outstanding dues are acknowledged before the year ends |
| Discontinuation | Branch Admin must acknowledge each outstanding invoice individually with a recorded reason before discontinuation completes; all three switches disable simultaneously | A student mid-year withdrawal is a deliberate, recorded decision — not an administrative shortcut |
| Bonafide Certificate Issuance | Irreversible; Admin required when outstanding dues exist; outstanding dues snapshot permanently attached to the record | School can issue a Bonafide certificate even with outstanding debt — but only with Admin authority and a permanent record |
| Transfer Certificate Issuance | Irreversible; per-invoice acknowledgment of outstanding dues required; leaving date, destination school, conduct remark, and reason recorded; TC and Bonafide are mutually exclusive | No child leaves the school system without a deliberate, attributed decision |

---

### C3. Daily Attendance Operations

The school must record daily attendance for every student enrolled in any programme — Nursery, PP1, PP2, Play Group, Day Care, or any future programme added by configuration. For Day Care students, staff-recorded check-in and check-out timestamps are additionally maintained as operational records to support billing calculations under the student's assigned Day Care Billing Plan.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Manual Attendance | Teacher marks Present / Absent / Late / Half-Day per class | High attendance in a preschool application maximizes tuition revenue and optimizes staffing ratios by giving administrators the real-time data needed to predict enrollment trends and minimize operational overhead. |
| Day Care Billing Computation | Staff-recorded check-in and check-out timestamps are maintained as operational records for Day Care students; billing is calculated according to the student's assigned Day Care Billing Plan (Monthly, Weekly, Day-wise, Hour-wise, Term-wise) | Day Care billing is plan-governed and independent from attendance records |
| Attendance Correction | Time-bounded by role: Teacher (same day → 7 days), Branch Admin (8–30 days), Admin (unlimited); every correction requires a reason beyond the same-day window | Errors can be corrected but the correction window and authority required grow narrower over time, creating accountability |
| Parent Absence Notification | Immediate in-app notification to parent when a student is marked Absent | Parents are informed of absences in real time, replacing informal WhatsApp messages |
| Chronic Absence Alerting | Branch Admin configures consecutive or total-in-period threshold per programme; system fires an in-app alert to Branch Admin immediately when threshold is crossed | Attendance patterns that signal a risk are surfaced proactively, not discovered retrospectively |

---

### C4. Fee Management & Financial Visibility

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Fee Structure Configuration | Branch Admin configures base fees per programme (billing model, billing schedule); transport zone rates (distance band, trip type, monthly amount); student category assignments | Fee terms are formally recorded and tied to each enrolled student at enrollment |
| Opening Balance Entry | Branch Admin or Admin enters historical outstanding balances per student before the first invoice is generated; window cannot close during Migration Mode | Schools adopting mid-year have their financial history correctly represented in the system from day one |
| CoFee Import | Accountant uploads CoFee XLSX; Happy Feet maps rows to students via Admission Number (normalised if configured); writes invoices and payments permanently into its own financial database; exceptions routed to a resolution queue | Financial data from CoFee lives in Happy Feet as first-class data — not a reference-only import |
| Lightweight Invoice Creation | Accountant, Branch Admin, or Admin creates a HAPPY_FEET_NATIVE invoice for any fee head, any student, any amount; blocked during Migration Mode | School can collect ad-hoc fees (events, materials, late charges) without waiting for a CoFee billing cycle |
| Razorpay Payment Links | Generated from a HAPPY_FEET_NATIVE invoice; shareable with parent; payment confirmation recorded automatically; blocked during Migration Mode | School can request payment and receive confirmation without handling cash or manual reconciliation |
| Day Care Billing Summary Export | Computed from assigned Day Care Billing Plans and, where required by the plan, staff-recorded check-in/check-out timestamps; Accountant exports and takes to CoFee for invoice generation | Day Care billing is determined by the assigned billing plan and operational timestamp records |
| Financial Dashboard | Outstanding balances with last import timestamp; overdue invoices; fee collection summary by period; expense summary | Accountant and Branch Admin see the financial picture at all times without a separate spreadsheet |
| Fee Query Channel | Parent submits fee question from portal Fee Query option; routes directly to Accountant's fee communication log; Accountant views and replies; Branch Admin and Admin can also reply | Fee questions are handled by the right person without going through a general inbox — and every exchange is permanently logged |
| Expense Logging | Branch Admin records expenses with an expense category on every entry | School has a categorised record of operational costs alongside income data |

---

### C5. Academic Progress Tracking

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Submission Window Management | Branch Admin sets open and close dates per term for each programme; system opens and closes the window; in-app alert fires to Teachers and Branch Admin on opening | Progress submissions happen in a defined period, not on an ad-hoc basis |
| Progress Card Authoring | Teacher drafts milestone ratings and observation text; drafts are invisible to Branch Admin and parents; Teacher submits to Branch Admin review | Drafts stay private until the Teacher is ready to submit — no accidental early publication |
| Delegate Reviewer | Branch Admin designates a Teacher as Delegate Reviewer for a specific class; delegate reviews submitted cards and adds a note visible to Branch Admin only; delegate cannot publish | Review load can be distributed without granting publication authority |
| Branch Admin Review & Publication | Branch Admin can publish or return a submitted card — cannot edit content; attribution is permanent at submission; attendance snapshot locked at publication moment | Progress card content belongs to the Teacher; Branch Admin is the gatekeeper, not the author |
| PDF Progress Card Generation | Branch Admin generates a PDF of any published card; PDF includes school name, branch, academic year, term, student details, milestone ratings, observations, attendance snapshot, and publication attribution | Parents receive a formal, printable academic record |
| Published Card Revision | Branch Admin opens a revision; original is archived; parents see the last published version until revision is published | Errors can be corrected without deleting the original record |

---

### C6. Parent Communication & Daily Child Visibility

The school must replace informal, untracked WhatsApp communication with structured, permanently logged channels that give parents real-time visibility into their child's safety and daily experience.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Daily Activity Logging | Teacher logs meals, naps, activities, health checks, and general notes per child per day from the attendance screen; batch entry supported | Every parent knows what their child ate, did, and how they felt today — without the teacher sending individual WhatsApp messages |
| Parent Activity Feed | Parents see a chronological feed for the current day and past 30 days on the portal; filterable by type and date; end-of-day summary notification fires at a configurable time if entries were made | Parents who cannot check WhatsApp during work hours have a permanent, catchable record rather than a message that disappears |
| Structured Announcements | Branch Admin or Coordinator sends to four audience levels: all parents, by programme, by class, or individual; recipient count shown before send; every announcement permanently logged | Broadcast communication is intentional and attributed — not an informal group message |
| School Notice Board | Branch Admin and Admin post notices; no other role can post; no notification sent — parents see an unread badge on next portal visit; archived notices permanently retained | Low-urgency informational notices reach parents without triggering notification fatigue |
| School Inbox (Two-Way) | Parent initiates a message from the portal, linked to a specific child; Branch Admin or Coordinator replies; thread continues until Branch Admin marks CLOSED; Teacher and Accountant have no access | Parents can raise concerns through a logged, structured channel instead of calling the school directly |
| System-Triggered Notifications | 12 named events trigger automatic notifications; 5 are mandatory and bypass parent opt-out | Critical lifecycle events (portal invite, enrollment, Bonafide, incident report published, pickup list change) always reach parents |
| WhatsApp Deeplink Templates | Pre-filled WhatsApp message templates for absence notification, fee reminder, payment confirmation, and incident notification; tap-to-send; logged per tap | Staff use structured, approved message text — not free-form WhatsApp — for operational communications with parents |

---

## Tier 2 — Supporting Capabilities

### S1. School Configuration

All operational modules depend on a correctly configured academic framework. No admissions, attendance, fee collection, or progress tracking can begin until configuration is complete.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Academic Year & Term Setup | 9-step sequenced setup: academic year dates, term dates with progress window dates, programme definitions, class creation, transport zones, school calendar, timetable, milestones, document types | All downstream modules have the operational framework they need before the year begins |
| Programme Configuration | Three programme types (Academic, Non-Academic, Event); each carries its own billing model, attendance model, milestone set, and progress card inclusion; default set pre-loaded, fully configurable | School can add or change programmes — new grades, dance classes, summer camps — without development work |
| Class & Teacher Slot Management | Each class has a Primary slot (mandatory), an optional Assistant slot, and an optional Temporary slot with configurable end date and 7-day expiry alert | Extended-leave cover is formalised with a clear end date and proactive alert — not handled by informal arrangement |
| School Calendar & Holiday Management | Branch Admin enters holidays and events for the full year; holidays disable attendance for all roles — no override exists | School calendar is the authoritative reference; attendance cannot be marked on a day the school has declared closed |
| Progress Submission Window Configuration | Branch Admin sets open and close dates per term and per programme during academic year setup; window can open early to spread review load | Progress submission is a planned, scheduled activity — not an ad-hoc decision |

---

### S2. Staff Records

The school must maintain employment records, manage leave formally, track certification compliance, and ensure no teacher exits without class coverage being arranged first.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Staff Profile & Employment Records | Name, designation, employment type, starting salary; mandatory documents (Aadhaar, education certificates, agreement); append-only salary history | Staff records are permanent and auditable — salary changes and document submissions leave a trail |
| Leave Management | Branch Admin records leave requests (type, dates, status, notes); approved leave automatically links to daily attendance entries and marks them On Leave; records are append-only | Leave is formally tracked with balance-level discipline — not managed via informal WhatsApp |
| Certification Tracking with Expiry Alerts | NTT, Montessori, First Aid, Police Verification, Employment Agreement — each with an alert threshold; in-app alert to Branch Admin when threshold is approaching; Branch Admin can upload renewed document from the alert | No certification expires unnoticed; Branch Admin acts before the expiry, not after |
| Staff Exit Gate | Teacher exit requires replacement Primary slot assignment confirmed; all staff exit requires all salary holds resolved; profile becomes read-only on confirmation | No teacher leaves without class coverage arranged; no staff member exits with unresolved financial holds |
| Daily Staff Attendance | Marked by Branch Admin, Coordinator, or Admin; backdatable 7 calendar days with a logged reason | Staff attendance is tracked with the same discipline as student attendance |
| Visitor Log | Coordinator records name, purpose, and time of each visitor; Branch Admin and Admin view the full log | School premises access is recorded, not remembered informally |

---

### S3. Child Safety Infrastructure

For children aged 18 months to 6 years, safety protocols are not advisory — they are operationally enforced at the system level.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Authorised Pickup List Management | Per-student list of named individuals permitted to collect the child; owned by Branch Admin and Admin; at least one active entry required at enrollment; every addition and deactivation permanently logged; mandatory notification to parent on any change | Release of a child at the gate is controlled by a verified list, not by staff judgement alone |
| Gate Pickup Verification | Coordinator looks up the student profile at departure; system displays the student's active Authorised Pickup List; Coordinator confirms who is collecting; system blocks and alerts if person is not on the list | No child can be released to an unregistered person — the block is a system enforcement, not a policy reminder |
| Welfare Concern Flagging & Review Queue | Teacher flags a student with a mandatory free-text observation; WelfareConcernRecord created (permanent, immutable); Branch Admin receives in-app alert; if unreviewed in 3 school days, a reminder fires; Branch Admin marks REVIEWED or ESCALATED with a note | Welfare concerns are formally escalated, not discussed informally; a permanent, attributed record exists regardless of what happened after |
| Incident Reporting & Publication Governance | Teacher or Branch Admin creates a formal incident record; Branch Admin decides to publish to parent portal or keep internal; parent notification on publication; internal notes never shown to parents | School handles incidents through a documented decision process — not an informal conversation; the publish/keep-internal distinction is a deliberate trust management tool |
| Allergy Protocol Compliance Audit Trail | When Teacher logs a Meal entry for a student with active allergy alerts, system surfaces warning inline and requires explicit confirmation that allergy protocol was followed; confirmation permanently logged | For children with allergies, every meal is an accountability record — the system creates an audit trail that cannot be skipped |

---

## Tier 3 — Administrative Capabilities

### A1. Identity & Access Management

Every user must see exactly what their role permits — no more, no less — and every access must be enforceable and auditable.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Role Provisioning & Deprovisioning | Five built-in roles, fixed; one role per staff member; 7-day email invite; access revoked automatically on staff exit, student discontinuation, or certificate issuance | Access is formally granted and formally revoked — no accounts persist beyond the need |
| Multi-Branch Staff Binding | Admin creates branch bindings per staff member; on login, staff choose their working branch for the session; all actions scoped and logged to the selected branch | A staff member covering two branches holds one account; their scope is enforced session-by-session, not by policy |
| Parent Portal Access | Created on first payment recording; child-scoped by hard business rule; cannot be modified by any staff role; access revoked on Bonafide or TC issuance | Parents see only their own child's data — this is a fixed constraint, not a configurable setting |
| Session Lifecycle Management | 30-minute inactivity timeout for all roles; role and class assignment changes take effect at next login; branch data isolation enforced on every data request | No session stays open indefinitely; changes to access rights take effect cleanly at login |
| Admin Override Confirmation | When Admin performs an action owned by another role, a one-click confirmation surfaces before proceeding; the override is recorded in the audit log | Admin's ability to act across all roles is intentional and documented — not invisible to the record |

---

### A2. Reporting & Operational Visibility

Admin and Branch Admin must be able to see the school's operational state without asking staff for verbal updates.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Role-Filtered Dashboards | Pre-computed widgets: active student count, today's attendance, enrollment by programme and class, outstanding balance with import timestamp, recent admissions, expense summary; Accountant sees financial-only view; Admin additionally sees cross-branch comparison | The school owner can see the complete operational picture from one screen at any time |
| CSV Exports | Available for: student list, enrollment, attendance, staff attendance, invoice list, payment list, communication log, governance report, expense log, daily journal, welfare concern report, leave register; every export automatically access-logged with actor, timestamp, and filter parameters | Authorised roles can extract data for analysis or external reporting; every extraction is traceable |
| Audit Log Access | Admin (all branches), Branch Admin (own branch only); no other role has direct access | Every action across every module is retrievable — no action is lost or unattributable |
| Governance Log | A filtered view of the audit log showing only override-class actions; visible to Admin only; Admin can access from the dashboard | Admin has a dedicated, clean view of every exception and override taken across the school — without wading through routine operations |
| Monthly Governance Digest | Auto-generated email to Admin on the 1st of each month; covers all branches; includes override actions, fee activity, and attendance metrics from the prior month | Admin receives a structured monthly accountability summary without having to run a report |

---

## Tier 4 — Compliance & Governance Capabilities

### G1. Data Governance & Auditability

Every significant action in the system must leave an immutable, attributed record. Sensitive data must be protected from export and accidental disclosure.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Immutable Audit Trail | Every action across every module — enrollment, attendance, payment, permission change — is written to a permanent log; no role can edit or remove any entry; records carry: timestamp, actor name and role, branch, module, action type, target entity, before state, after state, and reason where required | The school can demonstrate exactly who did what, when, and why — for any action, in any module, at any time |
| Sensitive Data Protection | Aadhaar numbers and medical records: access-logged on every read; never exported, emailed, or included in any notification; visible to Branch Admin and Admin only | Sensitive personal data cannot leak through exports, notifications, or role overreach — even accidentally |
| Override Accountability | Every exception path — capacity override, document waiver, attendance correction beyond 30 days, Bonafide or TC issuance with outstanding dues, discontinuation acknowledgments — requires a recorded reason and generates a governance log entry | Exceptions are sanctioned, not bypassed; every deviation from normal operation is documented |
| Data Retention Enforcement | Financial records retained for 7 years; communication logs for 3 years; student profiles, audit entries, and welfare concern records never deleted | The school meets legal and operational retention requirements without manual archiving |

---

### G2. Go-Live & Migration Governance

Every school arrives with existing students, financial history, and operational rhythm. The transition to Happy Feet must be managed as a protected, one-time business phase — not an informal process.

| Sub-Capability | Description | Business Outcome |
|---|---|---|
| Migration Mode | Admin-controlled, per-branch system state; suppresses invoice generation, Razorpay payment links, and parent portal activation; leaves data preparation tools fully operational; one-time per branch; cannot be re-entered | The school can prepare all data at its own pace without triggering irreversible financial or access consequences prematurely |
| Migration Checklist | Six-item checklist: (1) **student profiles** — including correct existing Admission IDs entered for all migrated students; (2) authorised pickup lists; (3) opening balances; (4) CoFee XLSX import; (5) academic year setup; (6) staff accounts. Admin cannot exit Migration Mode until all items are green or individually acknowledged with a logged reason. | Go-live requires a deliberate, documented confirmation that the school has prepared its data — not just an Admin clicking a button; migrated Admission IDs are verified before live operation begins |
| CoFee Import Bridge | Accountant uploads CoFee XLSX; system maps rows to students, writes invoices and payments into Happy Feet's financial database as permanent records; unmatched rows routed to an exceptions queue with three resolution options; every row has a traceable outcome | No financial record is silently lost during migration; every import row is matched, skipped, or manually resolved |
| Admission Number Normalisation | One-time configuration before the first CoFee import; Branch Admin specifies the format difference between CoFee and Happy Feet IDs; a preview function verifies the rule against 5 sample IDs before saving; applied automatically on all subsequent imports; match type (exact vs normalised) logged per row | Format mismatches between CoFee and Happy Feet do not send every import row to the exception queue |
| Historical Attendance Marker | Available during Migration Mode only; Branch Admin enters "Days present before go-live" per student from the paper register; locks permanently on Migration Mode exit; feeds into academic year attendance percentage and progress card attendance snapshot | Schools that go live mid-year have a meaningful, accurate full-year attendance figure — not a figure that starts at zero from go-live day |

---

## Capability Summary Table

| ID | Capability | Tier | Primary Owners | Source Module(s) |
|---|---|---|---|---|
| C1 | Student Intake & Enrollment | Core | Branch Admin, Coordinator, Accountant | M5, M3, M1 |
| C2 | Student Lifecycle Governance | Core | Branch Admin, Admin | M6, M3 |
| C3 | Daily Attendance Operations | Core | Coordinator, Teacher, Branch Admin | M8 |
| C4 | Fee Management & Financial Visibility | Core | Accountant, Branch Admin, Admin | M7, M11 |
| C5 | Academic Progress Tracking | Core | Teacher, Branch Admin | M9, M2 |
| C6 | Parent Communication & Daily Child Visibility | Core | Teacher, Branch Admin, Coordinator | M12, M10 |
| S1 | School Configuration | Supporting | Branch Admin, Admin | M2 |
| S2 | Staff Records | Supporting | Branch Admin, Admin | M4 |
| S3 | Child Safety Infrastructure | Supporting | Branch Admin, Admin, Teacher, Coordinator | M3, M8 |
| A1 | Identity & Access Management | Administrative | Admin, Branch Admin | M1 |
| A2 | Reporting & Operational Visibility | Administrative | Admin, Branch Admin, Accountant | M11 |
| G1 | Data Governance & Auditability | Compliance | Admin | M1, M11 (all modules write to audit log) |
| G2 | Go-Live & Migration Governance | Compliance | Admin, Branch Admin | M4, M7 (Migration Mode) |

---

## Key Observations

**1. Safety capabilities are not optional supporting features.**
S3 (Child Safety Infrastructure) is Tier 2 but architecturally non-negotiable. Weakening the pickup list, welfare concern pathway, or allergy audit trail creates legal and operational risk specific to the 18-months–6-years age group.

**2. C6 (Parent Communication) replaces a broken process, not a missing feature.**
The current state is informal WhatsApp with no log, no accountability, and no archive. Every C6 sub-capability targets one specific failure mode of that process — making it higher-stakes than a typical ERP communication module.

**3. G2 (Migration Governance) is a business phase, not a technical one.**
Migration Mode is a named operational state with legal and financial consequences, not a setup wizard. The one-time, irreversible exit is designed to make go-live traceable and deliberate.

**4. Fee management is record-keeping in V1, collection in V2.**
CoFee owns invoice generation and collection in V1; Happy Feet records the financial picture. C4 in V1 = financial visibility + light ad-hoc billing only. Full billing engine is V2.

**5. The immutable audit trail is a cross-cutting behavioural property, not a module.**
Every module writes to the same audit log. G1 is not a separate module to build — it is a constraint every module must implement from the start.

---
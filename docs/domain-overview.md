<!-- ABOUT
File: DOMAIN_OVERVIEW.md
Purpose: Establishes the business identity, stakeholders, business model, and operational cycle of Happy Feet School ERP to orient all subsequent domain analysis.
Scope: Business purpose, stakeholder roles, business model (revenue streams, V1/V2 split), seasonal cycle, and domain boundaries (V1 in scope, V2 and permanently out of scope); no technical design, no process flows, no capability definitions.
Dependencies: none
Stage: Stage 1 — Domain Discovery
Version: 1.1
Last Updated: 2026-06-06
Status: In Review
-->

# Domain Overview — Happy Feet School ERP

> **About This Document:** This is the starting point for understanding the Happy Feet ERP [ Enterprise Resource Planning ] —  a single platform that connects all school operations (admissions, fees, attendance, and communication) so every role works from the same data instead of disconnected registers, spreadsheets, and WhatsApp threads. This document covers who the stakeholders are, what the system does, what it doesn't, and where the Version 1 and Version 2 delivery boundary sits. Read this first if you are new to the project; skip it if you already know the school's context and domain scope.

**Phase:** 1 — Domain Discovery

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

**Accountant:** F Fee tracking in CoFee is disconnected from student records; reconciliation requires manual cross-referencing across systems.

**Parent / Guardian:**  Informal WhatsApp updates — no structure, no archive, no guaranteed delivery; no self-service view of attendance, progress, or fees.

**Child / Student:**  Not a system user. Central entity around which every safety rule, health record, and daily log is organised.

---

## 3. Core Value Proposition by Stakeholder

| Stakeholder | Core Value |
|---|---|
| Owner / Admin | Real-time operational visibility across the system |
| Branch Admin | Complete daily admin without paper or WhatsApp intermediaries |
| Teacher | Structured attendance, activity logging, and progress submission within class scope |
| Accountant | Financial visibility and fee communication without parallel spreadsheets |
| Parent | Structured, permanent view of child's daily school activity from one screen |

---

## 4. Stakeholders

### Primary (direct system users)

| Actor | Type | Role in Business |
|---|---|---|
| Admin | Human | School owner or super-administrator; full authority across all branches |
| Branch Admin | Human | Principal or branch head; primary operational actor for a single branch |
| Teacher | Human | Class teacher; scoped to assigned class slots. Three slot types exist — Primary, Assistant, and Temporary — reflecting operational responsibility. All three carry identical system permissions under the Teacher role; they are not distinct actor types |
| Coordinator | Human | Admin assistant or front-desk; intake, gate operations, communication |
| Accountant | Human | Finance staff; fee management, CoFee import, billing communications |
| Parent / Guardian | Human | Child's family; read-only access to child's school activity |

### Secondary (affected, not direct users)

| Actor | Type | Role in Business |
|---|---|---|
| Child / Student | Human | Subject of all records; never a system user |

### System and External Actors

| Actor | Type | Role |
|---|---|---|
| CoFee | External system | Primary payment collection and invoice lifecycle in V1 |
| Razorpay | External payment gateway | Ad-hoc payment links in V1; full integration in V2 |
| WhatsApp (deeplinks) | External platform | Manual tap-to-send parent communication in V1 |
| SMS Gateway (e.g. MSG91) | External service | OTP delivery for authentication and general parent notifications in V1 |
| Notification engine | Internal | Fires 12 named system notifications on defined trigger events |
| UDISE+ | Government system | National school registry; annual school reporting obligation |
| APAAR / PEN | Government identifier | Unique student academic ID; voluntary consent required |
| DPDP Act | Legal framework | India's Digital Personal Data Protection Act; governs data handling |

---

## 5. Business Model

| Revenue stream | Billing model | Key rule |
|---|---|---|
| Academic programmes (Nursery, PP1, PP2, Play Group) | Term-based | Full term fee even for mid-term admissions; no pro-rating |
| Day Care | Plan-based (see Day Care Billing Plan) | Staff-recorded check-in/check-out timestamps support operational tracking and billing calculations where required by the assigned Day Care Billing Plan (Monthly, Weekly, Day-wise, Hour-wise, Term-wise) |
| Transport (optional, school-owned) | Fixed monthly by zone | Per distance band and trip type; opt-in per student |
| Event / one-off | One-off fee | Annual Day, field trips, etc. |

**V1:** CoFee owns invoice generation and payment collection. Happy Feet records the financial picture via CoFee XLSX import; also supports HAPPY_FEET_NATIVE invoices and Razorpay payment links for ad-hoc collection.
**V2:** Happy Feet owns the full billing engine natively via Razorpay API; CoFee redundant. V1 financial tables are designed to accept V2 data with no schema migration.

Student categories (e.g. Standard, Staff Child, Subsidised) allow different fee structures per group.

---

## 6. Seasonal Business Cycle

3 terms per year by default. The annual cycle drives nearly every operational workflow.

| Phase | Key Events |
|---|---|
| Pre-Year Setup | 9-step academic configuration: year/term dates, programmes, classes, transport zones, calendar, timetable, milestones, document types — must complete in sequence |
| Admissions Season (ongoing) | Application → document review → programme assignment → enrollment → first payment → parent portal activation |
| Each Term (×3) | Attendance marking, daily activity logging, fee collection, parent communication |
| Progress Reporting Window (×3) | Teacher drafts/submits → Branch Admin reviews and publishes |
| Year-End Rollover | Branch Admin reviews Active students, acknowledges outstanding dues → Active → Completed Programme; switches reset |
| Migration Window (Entry/Exit) | Student profiles, opening balances, CoFee import, staff accounts — under Migration Mode |


**Calendar constraints:**

- Academic Year: Activates automatically at midnight on the configured   start date; Admin can manually override (logged). All operations — timetables, terms, fee cycles, and attendance — are scoped to the active academic year.

- Student Rollover: Students advance to their next class automatically at midnight on the academic year start date, in step with year activation. No manual trigger is required.

- Term Structure: The academic year is divided into terms, with start and end dates configured by the Branch Admin. Progress card submission windows open and close per term; no submissions are accepted outside the defined window.

- Timetable & Class Assignments: Weekly class schedules and class assignments are configured per branch and scoped to the active academic year and term — defining which teacher covers which class and when.

- Holidays: Entered by Admin; block attendance for all roles on the marked date with no override permitted.

- Admission Windows: Admissions open and close on configured dates. Applications submitted outside the window are not accepted.

- Fee Billing Cycles: Fee schedules operate on configured cycles — monthly, termly, or annual — with invoice generation tied to the cycle dates set per programme or plan.

- Staff Leave: Leave balances and approval workflows are tracked within the system. Approved leave is reflected against the timetable to flag coverage gaps.

- Communication: Announcements can be composed and scheduled for a future date and time; the system publishes them automatically at the configured moment.

- Reporting: Dashboards are available on demand. Compliance digests and governance reports are generated on a configured schedule — weekly, termly, or at academic year close.

- Day Care: Staff-recorded check-in/check-out timestamps support operational tracking and billing calculations tied to the assigned Day Care Billing Plan.

---

## 7. Domain-Specific Constraints

### 7.1 Child Safety as a Non-Negotiable Operating Requirement

Children aged 18 months to 6 years cannot advocate for themselves. This makes safety rules in a preschool context non-negotiable invariants — they are not configurable, cannot be bypassed, and override every other operational consideration. Four rules operate at this level:

- **Authorised pickup** — the system blocks release to anyone not on a child's authorised pickup list. No override is permitted without a governance log entry.
- **Allergy protocol** — when a meal entry involves a child with an active allergy flag, the Teacher must explicitly confirm the allergy protocol before proceeding. The confirmation is recorded as an immutable compliance audit entry.
- **Welfare concern pathway** — a Teacher-raised welfare concern notifies the Branch Admin immediately; review is required within 3 school days. If unreviewed, a reminder fires automatically. The record is permanent, immutable, and never visible to parents.
- **Incident reporting** — incidents are formally recorded by the Branch Admin, who decides whether to publish to parents or keep the record internal. Internal notes are never surfaced to parents under any circumstances.
- **Sensitive document handling** - admission documents include government-issued identification (Aadhaar, birth certificates), national education identifiers (PEN, APAAR ID), and medical records (vaccination history). These are stored as structured records, access-controlled by role, and never exposed outside authorised system boundaries.

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

Three rules govern this data category without exception:

- **Access control by role** — sensitive documents are accessible only to roles with an explicit operational need; no role can access another role's data boundary.
- **No external exposure** — sensitive records are never surfaced outside authorised system boundaries, regardless of request or context.
- **Consent requirement** — collection of APAAR ID and PEN number requires documented voluntary parent consent; the system records consent status per student and does not treat these fields as mandatory.

Photo sharing is deferred to V2, pending a full DPDP Act compliance review. Until then, no child photographs are stored or transmitted through the system.

---

## 8. Domain Boundaries

### In Scope — V1

- **Identity and access management** — roles, sessions, permissions, and multi-branch binding
- **School configuration** — academic year, terms, programmes, classes, calendar, timetable, and developmental milestones
- **Student records** — profiles, submitted documents, medical information, authorised pickup lists, and incident reports
- **Staff records** — profiles, attendance, leave balances, certifications, and exit workflow
- **Admissions and enrollment** — online application, waitlist, and four defined enrollment conditions (Waitlist is an admissions state managed within this module by Branch Admin — not a standalone module.)
- **Student lifecycle** — status transitions, annual rollover, Transfer Certificate issuance, and Bonafide certificate generation
- **Fee management** — fee structures, CoFee import, lightweight invoice generation, Razorpay payment links, and Day Care billing plans
- **Attendance management** — Teacher-marked daily attendance, corrections, chronic absence alerts, and real-time staff-to-child ratio monitoring with dashboard visibility and breach alerts (Staff-to-child ratio monitoring is a live function of present-marked attendance — not a standalone feature.)
- **Curriculum and lesson planning** — structured lesson plans authored by Teachers, linked to programme and class, and published to parents
- **Progress and assessments** — term-scoped submission windows, draft-to-publish workflow, and PDF generation
- **Meal management** — configurable per branch; daily menu planning with allergy-flagged meal entry confirmation and consumption tracking
- **Transport management** — optional per student; fixed routes with assigned stops selected at admission, supporting both school-owned and third-party vehicles
- **Communication** — announcements, WhatsApp deeplinks, school inbox, notice board, and 12 named system notifications
- **Daily Care Log** — structured parent-engagement feed covering meals, naps, activities, and health checks
- **Reporting and governance** — role-filtered dashboards, CSV exports, audit log, and governance log
- **Migration Mode** — reversible, per-branch operational state that controls the go-live data entry window for existing student records. See §9.4 for the full entry/exit specification.

---

### Out of Scope — V2

- Pre-admission enquiry and lead management — handled offline in V1
- Full Razorpay API integration for automated payment collection
- Automated WhatsApp dispatch via Business API
- Budget management and category-level expenditure tracking
- AI intelligence and predictive analytics layer
- Payroll data export
- Photo sharing — deferred pending DPDP Act compliance framework
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

**9.1 Immutability** — Financial records, audit entries, student profiles, admission IDs, welfare concern records, incident reports, PTM notes, and leave records are never deleted; status changes and corrections are appended, not overwritten. Both a governance requirement and a parent trust requirement. Admission IDs follow a dual-path assignment model: for students migrated from existing records, Admin or Branch Admin enters the historically assigned Admission ID during Migration Mode and the system preserves it as the permanent ID; for new admissions after go-live, the system auto-generates the Admission ID at profile creation.

**9.2 Governance Log** — Every override action (attendance correction beyond 30 days, Bonafide or TC issuance with outstanding dues, discontinuation acknowledgments, capacity overrides, document waivers, Migration Mode entry/exit) generates a permanent governance log entry visible to Admin only. This makes "exceptions always have a sanctioned path" operationally true.

**9.3 Multi-Branch ≠ Multi-Tenancy** — The two-branch architecture exists for one known second branch; no per-branch pricing, no tenant isolation question. Multi-branch from day one purely to avoid a future rebuild.

**9.4 Migration Mode Is a Reversible Per-Branch Operational State** — A branch enters Migration Mode when manual entry of existing student records begins, and exits when the Branch Admin confirms all records are entered, verified, and operationally ready; the system then transitions to normal operations. While active: relaxed validation rules allow incomplete records to be saved (fields not yet available from paper registers); migrated records are flagged vs. post-go-live records; automated workflows (chronic absence alerts, fee reminders) are suppressed to avoid false triggers on incomplete data. On exit: all validation rules activate; normal workflows resume; the exit is logged with a timestamp and the confirming admin's identity. Re-entry is permitted if corrections are needed, with a governance log entry recording the reason for reopening.

**9.5 CoFee Is a V1 Bridge** — CoFee manages the primary payment lifecycle in V1. The financial data model is designed so V2's Razorpay integration writes identical records — no migration, no data loss, no structural change required.

---

## 10. Assumptions and Open Questions

**Assumptions (inferred — require client confirmation):**
- [INFERRED] No separate admission fee; fees are programme-based term installments. Confirm: is there any registration or admission deposit collected separately from term fees?
- [INFERRED] Second branch is under the same ownership and shares the same fee structures and programme definitions as Branch 1. Confirm: will Branch 2 have independent fee structures or programmes?
- [INFERRED] APAAR/PEN registration workflow is not planned for V1 — noted as a compliance reality only. Confirm: does the school intend to collect and submit APAAR/PEN data to government systems in V1 or V2?
- [INFERRED] Transport is optional per student and billed separately from academic fees. Confirm: are there students who receive transport as part of a bundled programme fee?
- [INFERRED] Meal management is configurable per branch — not all branches will activate it. Confirm: does Branch 1 require meal management at go-live, or is it deferred?

**Open questions (require explicit client decision before build):**

1. QUESTION-006 — What is your preschool's current registration status on the UDISE+ portal, and have you already started collecting parent consent for generating student APAAR IDs?
2. QUESTION-007 — For students on the waitlist, does the school communicate a position number to parents, or is the waitlist managed silently by Branch Admin only?
3. QUESTION-008 — When a Branch Admin re-enters Migration Mode after exit (for corrections), is there a limit on how many times this is permitted, or is re-entry unrestricted with a governance log entry sufficient?
4. QUESTION-009 — Does the school currently use a structured fee concession or sibling discount model that must be reflected in fee structures, or are all concessions handled ad hoc?
5. QUESTION-010 — For the curriculum and lesson planning module: are lesson plans authored per teacher, per class, or per programme? And is parent visibility of lesson plans opt-in or on by default?

---

## Summary

Happy Feet is an administration platform for a 105-student privately owned preschool in Hyderabad on an annual 3-term cycle, served by five staff roles and parents across twelve integrated modules. The defining characteristic is the age group (18 months–6 years): safety rules — authorised pickup verification, allergy alert surfacing, welfare concern flagging, incident reporting — are non-negotiable and cannot be overridden without a permanently logged record.

---

## 11. Decision Log

Decisions already made and rationale captured. These are not open for revisitation without a documented change request.

| # | Decision | Options Considered | Decision Made | Rationale |
|---|---|---|---|---|
| D-001 | Admission ID model for migrated students | (1) Preserve existing school ID as permanent Admission ID; (2) Generate new Happy Feet ID, store old as Legacy ID | Option 1 — existing ID becomes the permanent Admission ID | School is going digital for the first time; existing IDs are already the operational source of truth across registers, CoFee, and parent documents. Introducing a new ID creates two IDs in circulation during transition, risking confusion for staff and parents. |
| D-002 | Migration Mode design | (1) One-time permanent open/close switch; (2) Reversible entry/exit state per branch | Option 2 — reversible entry/exit state per branch | A one-time switch cannot accommodate post-exit corrections, which are operationally inevitable when entering 105 student records manually. Entry/exit with governance log provides auditability without blocking corrections. |
| D-003 | V1 payment engine | (1) Build native Razorpay billing engine in V1; (2) Use CoFee as V1 bridge, migrate to Razorpay in V2 | Option 2 — CoFee as V1 bridge | School already operates with CoFee; rebuilding the payment lifecycle in V1 adds scope and risk without operational benefit. Financial data model is designed so V2 Razorpay integration writes identical records — no schema migration required at V2. |
| D-004 | Multi-branch architecture | (1) Single-branch V1, rebuild for multi-branch at V2; (2) Multi-branch architecture from day one | Option 2 — multi-branch from day one | A known second branch is already planned. Retrofitting multi-branch later requires schema migration and re-engineering of role-binding logic. Building it correctly once is lower total cost. |
| D-005 | Staff-to-child ratio | (1) Standalone ratio monitoring module; (2) Live function within Attendance module | Option 2 — folded into Attendance | Ratio is a direct derivative of present-marked attendance; it has no independent data source. A standalone module would duplicate attendance state with no additional value. |

---

<!-- ABOUT
File: DOMAIN_OVERVIEW.md
Purpose: Establishes the business identity, stakeholders, business model, and operational cycle of Happy Feet School ERP to orient all subsequent domain analysis.
Scope: Business purpose, stakeholder roles, business model (revenue streams, V1/V2 split), seasonal cycle, and domain boundaries (V1 in scope, V2 and permanently out of scope); no technical design, no process flows, no capability definitions.
Dependencies: none
Stage: Stage 1 — Domain Discovery
-->

# Domain Overview — Happy Feet School ERP

**Stage:** 1 — Domain Discovery
**Source:** PRODUCT_OVERVIEW_v2.md
**Date:** 2026-06-04
**Status:** Complete

---

## 1. What Business Is Being Operated

Happy Feet is the administration platform for a single, privately owned preschool in Hyderabad, India. 105 students enrolled across one active branch; a second branch is planned. Programmes: Play Group, Nursery, PP1, PP2, and Day Care (children aged ~18 months to 6 years). Not a franchise, not a government institution, not a SaaS product. Multi-branch architecture is included for the known second branch only — not for general multi-tenancy.

The school's business depends on three operations: **enrolling children and collecting fees**, **delivering daily care and education**, and **maintaining parent trust**. The trust dependency is the most fragile — safety failure, communication gaps, or financial opacity damage it irreversibly.

---

## 2. Problems Solved by Stakeholder

**Owner / Admin:** No real-time visibility; relies entirely on verbal staff updates for enrollment, fees, attendance, and incidents.

**Branch Admin (Principal):**
- Admission forms are paper-based — unsearchable, not portable across years
- No reliable audit of fee payment status or outstanding balances
- Staff leave handled informally — no balance tracking or approval workflow
- Parent communication is unstructured WhatsApp with no log or accountability

**Teacher:** Paper attendance registers with no management rollup, no parent notification, and no structured channel for welfare concerns or daily child activity recording.

**Accountant:** Fee tracking via CoFee disconnected from student records on paper or spreadsheet; manual reconciliation across systems.

**Parent / Guardian:** Informal WhatsApp updates — no structure, no archive, no guaranteed delivery; no self-service view of attendance, progress, or fees.

**Child / Student:** Not a system user. Central entity around which every safety rule, health record, and daily log is organised.

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
| Teacher | Human | Class teacher; scoped to assigned class slots |
| Coordinator | Human | Admin assistant or front-desk; intake, gate operations, communication |
| Accountant | Human | Finance staff; fee management, CoFee import, billing communications |
| Parent / Guardian | Human | Child's family; read-only access to child's school activity |

### Secondary (affected, not direct users)

| Actor | Type | Role in Business |
|---|---|---|
| Child / Student | Human | Subject of all records; never a system user |
| Teaching Assistant | Human | Occupies the Assistant slot on a class; same system role as Teacher |
| Temporary Teacher | Human | Covers extended staff absence; Temporary slot with configurable end date |

### System and External Actors

| Actor | Type | Role |
|---|---|---|
| CoFee | External system | Primary payment collection and invoice lifecycle in V1 |
| Razorpay | External payment gateway | Ad-hoc payment links in V1; full integration in V2 |
| WhatsApp (deeplinks) | External platform | Manual tap-to-send parent communication in V1 |
| Notification engine | Internal | Fires 12 named system notifications on defined trigger events |
| UDISE+ | Government system | National school registry; annual school reporting obligation |
| APAAR / PEN | Government identifier | Unique student academic ID; voluntary consent required |
| DPDP Act | Legal framework | India's Digital Personal Data Protection Act; governs data handling |

> APAAR, PEN, and UDISE+ submission workflows are not in scope for V1.

---

## 5. Business Model

| Revenue stream | Billing model | Key rule |
|---|---|---|
| Academic programmes (Nursery, PP1, PP2, Play Group) | Term-based | Full term fee even for mid-term admissions; no pro-rating |
| Day Care | Usage-based | Full day (≥4 hrs) = 1.0 unit; half day (<4 hrs) = 0.5 unit |
| Transport (optional) | Fixed monthly by zone | Per distance band and trip type; opt-in per student |
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
| Migration Window (one-time) | Student profiles, opening balances, CoFee import, staff accounts — under Migration Mode |

**Calendar constraints:**
- Holidays (entered in M2) block attendance for all roles — no override
- Progress card submission windows: open/close dates set by Branch Admin per term
- Academic year activation: automatic at midnight on configured start date; Admin can manually override (logged)
- Day Care billing: continuous; accumulates daily from QR timestamps

---

## 7. What Makes This Domain Different from General School Management

### 7.1 Age Group Creates Non-Negotiable Safety Rules

Children aged 18 months–6 years cannot advocate for themselves. Four safety rules override every other operational consideration:

- **Authorised pickup list** — absolute safety invariant; system blocks release to anyone not on the list; no override without a governance log entry
- **Allergy alert surfacing** — meal entries for students with active allergy flags force explicit Teacher confirmation of allergy protocol; creates an immutable compliance audit trail
- **Welfare concern pathway** — Teacher flags → Branch Admin notified → must review within 3 school days; if unreviewed, reminder fires; record is permanent, immutable, and never visible to parents
- **Incident reporting** — incidents formally recorded; Branch Admin decides to publish to parents or keep internal; internal notes never visible to parents under any circumstances

### 7.2 Day Care Is a Distinct Operational Model

| Dimension | Day Care model |
|---|---|
| Attendance | Check-in and check-out time, not presence/absence |
| Billing | Derived from QR gate timestamps (hours in care) |
| Daily record | Meals, naps, toileting, health checks logged per child |
| Nature | Childcare operations sharing the school platform — not a school attendance model |

### 7.3 Parent Trust Is the Core Product

The primary parent-facing function is not academic records — it is **daily visibility into the child's safety and wellbeing**. Every communication channel exists to replace "I don't know what happened to my child today" with structured, traceable information.

The current state — informal WhatsApp — fails because it has no log, no accountability, no archive, and no guaranteed delivery.

---

## 8. Domain Boundaries

### In Scope (V1)
- Identity and access management (roles, sessions, multi-branch binding)
- Academic configuration (year, terms, programmes, classes, calendar, timetable, milestones)
- Student information system (profiles, documents, medical, pickup lists, incident reports)
- Staff management (profiles, attendance, leave, certifications, exit)
- Admissions and enrollment (application, waitlist, four enrollment conditions)
- Student lifecycle (status transitions, rollover, TC issuance, Bonafide)
- Fee management (fee structures, CoFee import, lightweight invoices, Razorpay payment links, Day Care billing)
- Attendance management (QR check-in/out, manual fallback, corrections, chronic absence alerts)
- Progress tracking (submission windows, draft → publish workflow, PDF generation)
- Communication (announcements, deeplinks, school inbox, notice board, 12 system notifications)
- Reports and analytics (role-filtered dashboards, CSV exports, audit log, governance log)
- Daily activity log (meals, naps, activities, health checks, parent feed)
- Migration Mode (protected go-live phase per branch)

### Out of Scope — V2
- Full Razorpay API integration (automated payment collection)
- Automated WhatsApp dispatch via Business API
- Budget management per category
- AI intelligence layer
- Payroll data export
- Photo sharing (DPDP Act compliance required first)
- Full multi-branch management UI
- Retention rate reporting

### Permanently Out of Scope
- Pre-admission enquiry / lead management (handled offline)
- Historical attendance import from paper registers (manual summary marker used)
- Programmatic parsing of paper records or spreadsheets (staff are the migration engine)
- APAAR, PEN, UDISE+ submission workflows
- Payroll processing (export only)

---

## 9. Critical Domain Characteristics

**9.1 Immutability** — Financial records, audit entries, student profiles, admission IDs, welfare concern records, incident reports, PTM notes, and leave records are never deleted; status changes and corrections are appended, not overwritten. Both a governance requirement and a parent trust requirement.

**9.2 Governance Log** — Every override action (attendance correction beyond 7 days, Bonafide with outstanding dues, capacity overrides, document waivers, Migration Mode entry/exit) generates a permanent governance log entry visible to Admin only. This makes "exceptions always have a sanctioned path" operationally true.

**9.3 Multi-Branch ≠ Multi-Tenancy** — The two-branch architecture exists for one known second branch; no per-branch pricing, no tenant isolation question. Multi-branch from day one purely to avoid a future rebuild.

**9.4 Migration Mode Is a Named Business Phase** — The period between system provisioning and first live day is managed as a protected state: data preparation tools run normally; live-operation consequences (invoicing, payment links, parent portal activation) are suppressed. One-time per branch, Admin-controlled, permanent on exit.

**9.5 CoFee Is a V1 Bridge** — CoFee manages the primary payment lifecycle in V1. The financial data model is designed so V2's Razorpay integration writes identical records — no migration, no data loss, no structural change required.

---

## 10. Assumptions and Open Questions

**Assumptions (inferred):**
- [INFERRED] No separate admission fee; fees are programme-based term installments.
- [INFERRED] Second branch is under the same ownership and shares the same system.
- [INFERRED] APAAR/PEN registration is not part of any planned workflow — noted as a compliance reality only.

**Open questions:**
1. Will the second branch share staff accounts (multi-branch binding) or operate with fully independent staff?
2. Is there future intention to expand to schools other than this one? (franchise architecture implications)
3. Does the school own transport or coordinate with third-party providers?
4. Does the client expect digital support for APAAR registration or UDISE+ data preparation in any version?
5. Is Day Care attended by the same children enrolled in Play Group/Nursery (extended hours), or is it a separate-child population?

---

## Summary

Happy Feet is an administration platform for a 105-student privately owned preschool in Hyderabad on an annual 3-term cycle, served by five staff roles and parents across twelve integrated modules. The defining characteristic is the age group (18 months–6 years): safety rules — authorised pickup verification, allergy alert surfacing, welfare concern flagging, incident reporting — are non-negotiable and cannot be overridden without a permanently logged record.

---

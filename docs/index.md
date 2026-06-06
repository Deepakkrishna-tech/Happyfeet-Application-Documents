# Happy Feet Application — Documentation Index

> **NOTE** — This documentation index is under active development and subject to change.

---

## About the Application

Happy Feet is a multi-branch School ERP purpose-built for preschools 
and daycare centres. It replaces disconnected paper registers, 
spreadsheets, and WhatsApp threads with a single platform where every 
role — owner, principal, teacher, accountant, and parent — works from 
the same data.

The system supports the three operations a preschool depends on to 
survive: enrolling children and collecting fees, delivering daily care 
and education, and maintaining the parent trust that makes both 
possible.

The system is organised into **16 functional modules**, each owning a 
distinct slice of the school's operations:

- **Identity & access** for secure, role-based, multi-branch usage
- **Configuration** to model the school's academic structure (years, 
  terms, classes, timetables)
- **Student and staff records** as the central source of truth for 
  people data
- **Admissions and lifecycle** to manage intake, waitlist, rollover, 
  and exit
- **Fee management** with invoice generation and online payment 
  integration
- **Attendance** with daily marking, corrections, absence alerting, 
  and real-time staff-to-child ratio monitoring
- **Curriculum and lesson planning** for structured weekly plans linked 
  to programme and class, published to parents
- **Progress and assessments** for authoring, reviewing, and publishing 
  progress cards
- **Meal management** — configurable per branch; daily menu planning 
  with allergy-flagged meal entry confirmation
- **Transport management** — optional per student; fixed routes with 
  assigned stops, supporting school-owned and third-party vehicles
- **Communication** covering announcements, school inbox, and 
  WhatsApp-linked notifications
- **Daily Care Log** as the primary structured parent-engagement channel
- **Reporting and governance** for dashboards, audit logs, and 
  compliance digests
- **Migration Mode** — controlled go-live workflow for manually entering 
  and verifying existing student records before normal operations begin

---

## Getting Started

**Authoritative today:** the **Domain Overview** is the current source of truth. The Stage-5 documents below (rows 1–6) are being regenerated from it and may be out of date — if anything conflicts, the Domain Overview wins.

| Step | Document | Read If |
|---|---|---|
| 0 | **Domain Overview** | You are new to the project — read this first (source of truth) |
| 1 | Business Capability Model | You need to understand what the system can do |
| 2 | Organization Model | You need to understand who has authority over what |
| 3 | Business Processes | You need to understand how work flows end-to-end |
| 4 | Business Rules | You need the explicit rules, invariants, and constraints |
| 5 | Domain Model | You need the entity and relationship map |
| 6 | Domain Glossary | You need definitions for any term used in this project |

---

## System Modules

| # | Module | Purpose |
|---|---|---|
| M1 | Identity & Access Management | User identities, roles, session lifecycle, multi-branch binding, parent portal access, branch data isolation |
| M2 | School Configuration | Academic year, terms, programmes, classes, calendar, timetable, milestones, document types — the framework all other modules depend on |
| M3 | Student Records | Permanent student profiles, documents, medical records, pickup lists, incident reports, welfare concern records |
| M4 | Staff Records | Employment records, certifications with expiry alerts, leave management, staff exit gates, visitor log |
| M5 | Admissions | Student intake from application through enrollment, waitlist management, parent portal activation |
| M6 | Student Lifecycle | Status management — discontinuation, rollover, Transfer Certificate, Bonafide certificate, re-admission |
| M7 | Fee Management | Canonical financial ledger (single source of payment truth); fee structures, CoFee import (temporary V1 source, retired at V2), lightweight invoices, Razorpay payment links, Day Care billing, Fee Query channel, financial dashboard |
| M8 | Attendance Management | Daily attendance marking, corrections, chronic absence alerting, gate pickup verification, Day Care check-in/check-out |
| M9 | Curriculum & Lesson Planning | Structured lesson plans authored per class and programme, published to parents |
| M10 | Progress & Assessments | Progress card authoring, review, Branch Admin publication, PDF generation, submission window management |
| M11 | Meal Management | Configurable per branch; daily menu planning with allergy-flagged meal entry confirmation and consumption tracking |
| M12 | Transport Management | Optional per student; fixed routes and stops selected at admission; school-owned and third-party vehicle support |
| M13 | Communication | Announcements, notice board, school inbox (two-way), WhatsApp deeplink templates, system-triggered notifications |
| M14 | Reporting & Governance | Role-filtered dashboards, CSV exports, audit log, governance log, monthly digest |
| M15 | Daily Care Log | Per-child structured daily records — meals, naps, activities, health checks — as the primary parent engagement channel |
| M16 | Migration Mode | Controlled go-live data entry workflow; entry/exit state per branch; suppresses automated workflows until Branch Admin confirms readiness |
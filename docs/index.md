> **Under Active Development** — This documentation is a living document. Content is incomplete, subject to change, and pending review. Do not treat any section as final until explicitly marked stable.

# Happyfeet Application Documents

Welcome to the Happyfeet Application documentation. This resource describes the business domain, capabilities, processes, and rules governing the Happy Feet School ERP system — a purpose-built platform for managing early childhood education centres.

## About the Application

Happy Feet is a multi-branch School ERP designed for preschools and daycare centres. It covers the full operational lifecycle of a school: from onboarding students and staff, through day-to-day attendance and fee collection, to parent engagement and governance reporting.

The system is organised into **12 functional modules**, each owning a distinct slice of the school's operations:

- **Identity & access** for secure, role-based, multi-branch usage
- **Configuration** to model the school's academic structure (years, terms, classes, timetables)
- **Student and staff records** as the central source of truth for people data
- **Admissions and lifecycle** to manage intake, rollover, and exit
- **Fee management** with invoice generation and online payment integration
- **Attendance** with daily marking, corrections, and absence alerting
- **Progress and assessments** for authoring, reviewing, and publishing progress cards
- **Communication** covering announcements, school inbox, and WhatsApp-linked notifications
- **Daily Care Log** as the primary structured parent-engagement channel
- **Reporting and governance** for dashboards, audit logs, and compliance digests

## Getting Started

Start with **Domain Overview** to understand the business context, stakeholders, and operational scope.

Then progress through:

1. **Business Capability Model** — What the system can do and the business outcomes
2. **Organization Model** — Who has authority over what decisions
3. **Business Processes** — How work flows end-to-end
4. **Business Rules** — Explicit rules, invariants, and compliance requirements
5. **Domain Model** — Entities, actors, and relationships
6. **Domain Glossary** — Complete terminology reference

This documentation is designed for stakeholders, developers, and project teams who need to understand Happy Feet's operational and technical architecture.

---

## System Modules

| # | Module | Purpose |
|---|---|---|
| M1 | Identity & Access Management | User identities, roles, session lifecycle, multi-branch binding, parent portal access, branch data isolation |
| M2 | School Configuration | Academic year, terms, programmes, classes, calendar, timetable, milestones, document types — the framework all other modules depend on |
| M3 | Student Records | Permanent student profiles, documents, medical records, pickup lists, incident reports, welfare concern records |
| M4 | Staff Records | Employment records, salary history, certification tracking with expiry alerts, leave management, staff exit gates, visitor log |
| M5 | Admissions | Student intake from application through enrollment, waitlist management, parent portal activation trigger |
| M6 | Student Lifecycle | Student operational status management — discontinuation, rollover, Transfer Certificate, Bonafide certificate, re-admission |
| M7 | Fee Management | Fee structures, CoFee XLSX import, lightweight invoices, Razorpay payment links, Day Care billing summary, Fee Query channel, expense log, financial dashboard |
| M8 | Attendance Management | Daily student attendance marking, attendance corrections, chronic absence alerting, gate pickup verification, Day Care check-in/check-out timestamps |
| M9 | Progress & Assessments | Progress card authoring, delegate review, Branch Admin review and publication, PDF generation, submission window management |
| M10 | Communication | Announcements, school notice board, school inbox (two-way), WhatsApp deeplink templates, system-triggered notifications |
| M11 | Reporting & Governance | Role-filtered dashboards, CSV exports, audit log access, governance log, monthly governance digest |
| M12 | Daily Care Log | Per-child structured daily records — meals, naps, activities, health checks, general notes — as the primary parent engagement channel |

---

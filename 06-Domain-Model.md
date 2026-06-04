<!-- ABOUT
File: DOMAIN_MODEL.md
Purpose: Identifies every actor, entity, event, document, lifecycle state, and relationship in the Happy Feet domain — business meaning only, no technical modeling.
Scope: 11 actors, 34 entities, 27 business events, 10 documents, 8 lifecycle state maps, entity relationship summary; excludes database schemas, APIs, data types, and implementation.
Dependencies: DOMAIN_OVERVIEW.md, BUSINESS_CAPABILITY_MODEL.md, ORGANIZATION_MODEL.md, BUSINESS_PROCESSES.md, BUSINESS_RULES.md
Stage: Stage 6 — Domain Concepts
-->

# Domain Model — Happy Feet School ERP

**Stage:** 6 — Domain Concepts
**Source:** PRODUCT_OVERVIEW_v2.md, DOMAIN_OVERVIEW.md, BUSINESS_CAPABILITY_MODEL.md, ORGANIZATION_MODEL.md, BUSINESS_PROCESSES.md, BUSINESS_RULES.md
**Date:** 2026-06-05
**Status:** Complete

---

## 1. Actors

| Actor | Type | Scope | Primary Goal |
|---|---|---|---|
| Admin | Human | All branches, all modules | Complete operational visibility; sole authority over cross-branch governance, Migration Mode, and overrides no other role can execute |
| Branch Admin | Human | Own branch, all modules | Operate the branch end-to-end without paper or WhatsApp intermediaries |
| Coordinator | Human | Own branch — intake, gate, inbox | Advance admissions, scan QR at gate, manage school inbox |
| Teacher | Human | Assigned class slots only | Mark attendance, log daily activities, submit progress cards |
| Accountant | Human | Own branch — financial data | Financial visibility, CoFee import, fee communication |
| Parent / Guardian | Human | Own child's data — read-only | Real-time visibility into child's safety and daily school activity |
| Child / Student | Human | Subject of all records — never a system user | Central entity around which every safety rule, health record, and daily log is organised |
| CoFee | External system | Primary invoice and payment lifecycle (V1) | Generate invoices, collect payments, produce XLSX exports |
| Razorpay | External system | Ad-hoc payment links (V1) | Process single payments; confirm automatically in Happy Feet |
| WhatsApp | External platform | Manual parent communication (V1) | Deliver pre-filled deeplink messages tapped by staff |
| Notification Engine | Internal system | All modules | Fire 12 named system notifications on defined trigger events |

---

## 2. Entities

For each entity: what it is, why it exists, key business attributes, and what it connects to.

---

### 2.1 Branch

The operational unit that scopes all data, staff, and students. Every data request is validated against the requesting user's branch binding before data is returned.

**Key attributes:** Name, location, active status, Migration Mode state (per-branch)
**Relationships:** Contains Academic Years, Classes, Staff, Students, Fee Structures, and all audit records

---

### 2.2 Academic Year

The annual operational period. Every downstream module (admissions, attendance, fees, progress) depends on a confirmed Academic Year configuration.

**Key attributes:** Start date, end date, activation status, activation mode (automatic or manual override)
**Lifecycle:** Configured (upcoming) → Active (at midnight on start date) → Closed (at year-end rollover)
**Relationships:** Has Terms; has configured Programmes, Classes, Calendar, Timetable, Milestones, Document Types

---

### 2.3 Term

A sub-period within an Academic Year. The scheduling unit for progress card submission windows.

**Key attributes:** Term number, start date, end date, progress card submission window (open date, close date)
**Relationships:** Belongs to Academic Year; defines submission window for Progress Cards

---

### 2.4 Programme

A course offering with its own billing model, attendance model, and milestone structure. Fully configurable — no fixed list.

**Key attributes:** Name, type, billing model, attendance model, milestone set (full or simplified), progress card inclusion

| Programme type | Examples | Billing model | Attendance | Progress cards |
|---|---|---|---|---|
| Academic | Nursery, PP1, PP2, Grade 1–10 | Term-based | Daily class | Yes — configurable milestone set |
| Non-Academic | Play Group, Day Care, Summer Camp | Monthly / Session | Daily or session | Optional |
| Event | Annual Day, field trip | One-off fee | Not applicable | No |

**Default set (pre-loaded, not hardcoded):** Play Group, Nursery, PP1, PP2, Day Care
**Relationships:** Has Classes, Milestones, Fee Structure, required document types

---

### 2.5 Class

A named group of students within a programme, with capacity and teacher slots.

**Key attributes:** Name, capacity (with override history), three teacher slot fields

| Slot | Mandatory | End date |
|---|---|---|
| Primary | Yes — every class must have one | N/A |
| Assistant | No | N/A |
| Temporary | No | Configurable; access lapses automatically |

**Relationships:** Belongs to Programme and Branch; has Students, Timetable, Progress Cards; has a Waitlist when full

---

### 2.6 Student Profile

The permanent record of a child from admission through all lifecycle transitions. Never deleted regardless of status.

**Key attributes:** Permanent Admission ID, name, date of birth, status, programme, class, guardian contacts, emergency contacts, medical records (access-logged), allergy flags, critical medication flags, document list, Historical Attendance Marker
**Relationships:** Has Attendance Records, Progress Cards, Daily Activity Log entries, Welfare Concern Records, Incident Reports, PTM Notes, Invoices, Opening Balance, Authorised Pickup List

---

### 2.7 Admission Application

A formal request for a child to attend the school. Exists before a Student Profile is created; becomes the Student Profile on enrollment completion.

**Key attributes:** Child details, programme preference, guardian contacts, medical history, submitted documents, application status
**Lifecycle:** Applied → Approved → Enrolled | Declined | Waitlisted
**Relationships:** Transitions to Student Profile on Enrollment; linked to Waitlist if class is full

---

### 2.8 Waitlist

An ordered queue of approved applicants for a class that has reached capacity.

**Key attributes:** Per-class; entries ordered by date added; permanently logged
**Lifecycle:** Student added → class slot opens → Branch Admin offers enrollment → Accepted (proceeds to P04) or Declined (position permanently logged)
**Relationships:** Belongs to Class; references Admission Application

---

### 2.9 Fee Structure

The billing configuration for a programme that is snapshotted per student at enrollment.

**Key attributes:** Programme, billing model, billing schedule, per-term amounts, student category, transport zone (if applicable)
**Snapshot rule:** Snapshotted as a full data copy at enrollment completion; governs billing for that student's enrolment period; subsequent programme-level changes do not automatically update existing snapshots
**Relationships:** Belongs to Programme; one snapshot per enrolled Student; feeds Invoices

---

### 2.10 Invoice

A formal record of a financial obligation — either imported from CoFee or created natively.

**Key attributes:** Source (COFEE_IMPORT or HAPPY_FEET_NATIVE), auto-generated invoice number, fee head, amount, due date, payment status

| Source | Created by | Blocked during Migration Mode |
|---|---|---|
| COFEE_IMPORT | CoFee XLSX import | No — import runs normally |
| HAPPY_FEET_NATIVE | Accountant, Branch Admin, or Admin | Yes |

**Relationships:** Belongs to Student; has Payments; HAPPY_FEET_NATIVE invoices can have Razorpay payment links

---

### 2.11 Payment

A recorded financial transaction applied against an Invoice. Never deleted.

**Key attributes:** Amount, date, source (CoFee or Razorpay), transaction ID (Razorpay), linked invoice
**Lifecycle:** Recorded → permanent
**Relationships:** Belongs to Invoice and Student

---

### 2.12 Opening Balance

A historical outstanding amount for a student migrated into Happy Feet before go-live.

**Key attributes:** Student, amount, entered by, entry date
**Window rule:** Available before the first invoice is generated for that student; window stays open during Migration Mode; closes permanently once the first invoice is generated post-Migration Mode exit
**Relationships:** Belongs to Student; feeds financial dashboard outstanding balance

---

### 2.13 Attendance Record

The daily record of a student's presence, absence, arrival, and departure.

**Key attributes:** Student, date, class, status (Present / Absent / Late / Half-Day), source (QR or manual), CHECK_IN timestamp, CHECK_OUT timestamp, correction log

**Status derivation:**
- CHECK_IN received → Present
- CHECK_IN received after configurable late threshold → Late
- No CHECK_IN by end of marking window → Absent (Teacher confirms)

**Day Care billing:** Derived from CHECK_OUT − CHECK_IN: ≥4 hours = 1.0 billing unit; <4 hours = 0.5 billing unit
**Relationships:** Belongs to Student and Class; feeds Day Care billing units; triggers parent notification #3 on Absent; feeds Progress Card attendance snapshot at publication

---

### 2.14 Progress Card

A formal termly academic assessment of a student's development, submitted by Teacher and published by Branch Admin.

**Key attributes:** Student, term, academic year, milestone ratings, Teacher observation text, attribution (drafter, submitter), attendance snapshot (locked at publication — immutable), status, publication date
**Lifecycle:** DRAFT → SUBMITTED → PUBLISHED | RETURNED → (loop) | ARCHIVED (on revision)
**Relationships:** Belongs to Student and Term; has Milestones; attendance snapshot locked from live Attendance Records at publication; visible to Parent (published only)

---

### 2.15 Milestone

A developmental domain assessed within a progress card.

**Key attributes:** Programme, domain name, rating scale, milestone set (full or simplified)
**Lifecycle:** Configured per programme at academic year setup → used in all progress cards for that programme
**Relationships:** Belongs to Programme; appears in Progress Card

---

### 2.16 Daily Activity Log Entry

A per-child record of a specific activity during the school day. Immutable after saving; corrections appended as new entries. Never deleted.

**Key attributes:** Student, date, entry type, entered by Teacher, timestamp, type-specific fields

| Entry type | Key fields | Parent visibility |
|---|---|---|
| Meal | Description, quantity, allergy protocol confirmed Y/N | Full |
| Nap | Duration, quality | Full |
| Toilet | Type | "Toilet visit logged" — no detail ever shown |
| Activity | Name, participation level | Full |
| Health Check | Temperature (optional), condition, medication administered | Full |
| General Note | Free text (500 chars max) | Full |

**Allergy rule:** Meal entries for students with active allergy flags require Teacher to confirm "allergy protocol was followed" before saving. Confirmation permanently logged — creates an immutable audit trail.
**Relationships:** Belongs to Student; visible to Parent (30-day feed, filterable); Coordinator can view read-only

---

### 2.17 Welfare Concern Record

A Teacher's formal observation of a student wellbeing concern. Permanent and immutable. Never visible to parents.

**Key attributes:** Student, flagging Teacher, timestamp, mandatory free-text description ("Describe what you observed"), status (OPEN / REVIEWED / ESCALATED), Branch Admin review note, review timestamp
**Lifecycle:** Created (OPEN) → Branch Admin reviews within 3 school days → REVIEWED or ESCALATED; status changes appended; record permanent
**Relationships:** Belongs to Student; triggers Branch Admin notification #8; teachers see own flags only

---

### 2.18 Incident Report

A formal record of a notable event (injury, behavioural episode, medical event) during school hours. Append-only; never deleted. Internal notes and staff witness details never shown to parents under any circumstances.

**Key attributes:** Student, date of occurrence, incident type, what happened, action taken, internal notes (staff only), staff witnesses (staff only), status (INTERNAL / PUBLISHED)
**Lifecycle:** Created (INTERNAL) → Branch Admin reviews → PUBLISHED to parent portal or remains INTERNAL (one-way)
**Published parent view:** Date, incident type, what happened, action taken only
**Relationships:** Belongs to Student; when published, triggers mandatory parent notification #7 (bypasses opt-out)

---

### 2.19 Authorised Pickup List

The per-student list of named individuals permitted to collect the child at the school gate. An absolute safety enforcement mechanism.

**Key attributes:** Per student; entries: name, relationship, contact number, active status; every addition/deactivation permanently logged with actor, reason, and timestamp
**Invariant:** At least one active entry required at all times from enrollment. Deactivating the last active entry is blocked. Guardian is not automatically on the list — must be explicitly added.
**Lifecycle:** First entry required before enrollment completes → managed by Branch Admin throughout enrolment → entries log on every change; minimum one active entry enforced
**Relationships:** Belongs to Student; verified at QR check-out (system blocks release if person not listed); readable by Teacher (own class) and Coordinator (gate verification); every change triggers mandatory parent notification (bypasses opt-out)

---

### 2.20 PTM Note

A Parent-Teacher Meeting observation record for a student. Internal staff record only — never visible to parents.

**Key attributes:** Student, teacher, meeting date, observation text
**Lifecycle:** Created during or after PTM → permanent
**Relationships:** Belongs to Student; visible to Branch Admin, Admin, own Teacher only

---

### 2.21 Transport Zone

A distance band with a fixed monthly billing rate for school transport.

**Key attributes:** Zone name, distance band, trip type, fixed monthly amount per student
**Billing:** Fixed per zone — not variable by actual usage
**Lifecycle:** Configured at academic year setup → students opt-in per zone; transport activated/deactivated at any time, taking effect from the next billing period
**Relationships:** Belongs to Branch/Academic Year; assigned per Student; drives transport billing line item in Fee Structure

---

### 2.22 Staff Profile

The employment record for a school staff member. Never deleted.

**Key attributes:** Name, designation, employment type, role, branch bindings, salary history (append-only), documents (Aadhaar, education certificates, employment agreement with hold type noted), leave records, certifications with expiry dates, status

**Certification expiry alert thresholds:**

| Document | Alert threshold |
|---|---|
| NTT Certificate | 60 days before expiry |
| Montessori Certification | 60 days before expiry |
| First Aid Certificate | 30 days before expiry |
| Police Verification | 60 days before computed expiry (issue date + 3 years) |
| Annual Employment Agreement | 30 days before renewal date |

**Lifecycle:** Created by Branch Admin → Active → (On Leave, temporary) → Exited (read-only; access revoked)
**Relationships:** Bound to one or more Branches via Branch Bindings; occupies Teacher Slots on Classes; has Leave Records, Salary Holds, Certifications

---

### 2.23 Leave Record

A formal leave request for a staff member. Append-only.

**Key attributes:** Staff member, leave type (Sick / Casual / Emergency / Planned), start date, end date, status (Approved / Rejected / Pending), notes
**Approved leave:** Automatically links to the corresponding daily staff attendance entries and marks them On Leave with the leave reference
**Relationships:** Belongs to Staff Profile; links to Staff Attendance entries

---

### 2.24 Salary Hold

A financial hold on a staff member's salary or documents that must be formally resolved before exit can proceed.

**Key attributes:** Hold type (first-month salary held / degree in custody), status (active / resolved), resolution notes

| Hold type | Resolution |
|---|---|
| First-month salary held | Formally released by Branch Admin |
| Degree in custody | Returned to staff member and logged |

**Relationships:** Belongs to Staff Profile; blocks Staff Exit (Gate 2) until resolved

---

### 2.25 Visitor Log Entry

A record of a non-staff visitor to school premises. Operational security record.

**Key attributes:** Visitor name, purpose, entry time, recorded by (Coordinator)
**Lifecycle:** Created by Coordinator → permanent
**Relationships:** Belongs to Branch; visible to Branch Admin and Admin

---

### 2.26 WhatsApp Template

A pre-approved message template for staff-to-parent WhatsApp deeplink communications.

**Key attributes:** Type, scope (Global or Branch-scoped), content, placeholder fields, version history, active status

| Template type | Sent by | Available placeholders |
|---|---|---|
| Absence notification | Admin, Branch Admin, Coordinator, Teacher | student.name, student.class, attendance.date, school.name, branch.name |
| Fee reminder | Admin, Branch Admin, Accountant | student.name, invoice.amount, invoice.due_date, invoice.fee_head, school.name, branch.name |
| Payment confirmation | Admin, Branch Admin, Accountant | student.name, payment.amount, payment.date, invoice.remaining_balance, school.name, branch.name |
| Incident notification | Branch Admin only | student.name, student.class, incident.date, school.name, branch.name |

**Fallback chain at send time:** Branch-scoped template → Global template → Blocked (in-app alert to Branch Admin)
**Lifecycle:** Created → active immediately → versions retained on edit; cannot be deleted if it is the last active template of its type for the branch — only deactivated
**Relationships:** Global: created by Admin; Branch: created by Branch Admin (if toggle enabled); used by eligible roles at send time

---

### 2.27 School Notice Board Post

A branch-wide, non-transactional notice. No notification fires on posting — parents see an unread badge on next portal visit.

**Key attributes:** Content, posted by, post date, archived status
**Lifecycle:** Posted → archived (permanently retained; cannot be deleted)
**Relationships:** Belongs to Branch; visible to all staff roles and Parents (branch-scoped)

---

### 2.28 School Inbox Thread

A two-way asynchronous message thread between a parent and the school, linked to a specific enrolled child.

**Key attributes:** Linked enrolled child, messages, staff replies, thread status (active / CLOSED), message timestamps
**Rules:** Maximum 5 parent-initiated messages per student per day; staff replies have no limit; messages cannot be deleted by any role; retained 3 years; CLOSED is terminal
**Relationships:** Linked to Student and Parent; handled by Branch Admin and Coordinator; Teacher and Accountant have no access

---

### 2.29 Fee Query Thread

A dedicated channel for parent fee-related questions, routed directly to Accountant's fee communication log.

**Key attributes:** Linked enrolled child, query content, Accountant replies, thread status (active / CLOSED)
**Rules:** Separate from school inbox; Coordinator has no access; CLOSED is terminal; all messages permanently logged
**Relationships:** Linked to Student; handled by Accountant primarily; Branch Admin and Admin can also view and reply

---

### 2.30 Announcement

A broadcast communication from the school to a defined parent audience. Sent as an in-app notification.

**Key attributes:** Audience level, content, sender, send time, recipient count (shown before send)

| Audience level | Recipients |
|---|---|
| All parents | Every parent with an active portal account in the branch |
| By programme | All parents of students in a specific programme |
| By class | All parents of students in a specific class |
| Individual | A single parent account |

**Relationships:** Sent by Branch Admin, Coordinator, or Admin; received by Parents; permanently logged

---

### 2.31 Audit Log Entry

A permanent, immutable record of every action taken in the system. No role can edit or delete any entry.

**Key attributes:** Timestamp, actor (name and role), branch, module, action type, target entity, before state, after state, reason (where required)
**Relationships:** Written by every module on every action; accessible to Admin (all branches) and Branch Admin (own branch only)

---

### 2.32 Governance Log Entry

A filtered view of the Audit Log showing only override-class actions. Visible to Admin only.

**Override-class actions captured:** Attendance corrections beyond 30 days; Bonafide/TC with outstanding dues; capacity overrides; document waivers; discontinuation acknowledgments; Migration Mode entry and exit
**Relationships:** Subset of Audit Log; drives Monthly Governance Digest email

---

### 2.33 Migration Checklist

The per-branch go-live readiness tracker maintained during Migration Mode.

**Six checklist items:**

| Item | Green when |
|---|---|
| Student profiles | All expected profiles created and verified |
| Authorised pickup lists | Every student has at least one active entry |
| Opening balances | All students have opening balances entered |
| CoFee XLSX import | Last import run with zero unresolved exceptions |
| Academic year setup | All 9 steps confirmed complete |
| Staff accounts | All staff provisioned and accounts activated |

**Rules:** Admin cannot exit Migration Mode until every item is green or explicitly acknowledged with a logged reason. Complete checklist state is permanently captured in the Governance Log on exit.
**Relationships:** Belongs to Branch; controls Migration Mode exit

---

### 2.34 Historical Attendance Marker

A single per-student manual entry of school days present before the system go-live date.

**Key attributes:** Student, days-present value, entered by Branch Admin, entry date
**Rules:** Available during Migration Mode only; locks permanently on Migration Mode exit; cannot be edited thereafter by any role; accuracy depends entirely on paper register data
**Relationships:** Belongs to Student; contributes to annual attendance percentage and Progress Card attendance snapshot

---

## 3. Business Events

Events that trigger state changes, notifications, or workflow initiations.

### 3A — Setup and Configuration Events

| Event | Trigger | Primary Effect |
|---|---|---|
| Academic Year Configured | Branch Admin completes all 9 steps | All downstream modules become operational |
| Academic Year Activated | Midnight on configured start date (or Admin manual override) | New year becomes the active period |
| Staff Account Provisioned | Branch Admin creates profile and sends portal invite | 7-day invite window opens |
| Staff Account Activated | Staff member logs in for the first time | Role and class assignments take effect at this login |
| Migration Mode Entered | Admin confirms entry | Invoice generation, payment links, and portal activation suppressed |
| Migration Mode Exited | Admin confirms exit after checklist cleared | All suppressed events fire simultaneously; Historical Attendance Marker locks |

---

### 3B — Admissions and Enrollment Events

| Event | Trigger | Primary Effect |
|---|---|---|
| Application Submitted | Parent submits public form or Branch Admin creates walk-in | Status: Applied; Student Profile not yet created |
| Programme Assigned | Branch Admin assigns programme and class | Status: Approved; Student Profile initiated in M3 |
| Enrollment Completed | All four conditions satisfied; Branch Admin confirms | Status: Enrolled; fee structure snapshotted; Admission ID assigned |
| Application Declined | Branch Admin or Admin records reason | Status: Declined; application permanently retained; can be reopened |
| Student Added to Waitlist | Class full; Branch Admin adds applicant | Waitlist position recorded; no automated parent notification |
| First Payment Recorded | Accountant records first payment | Portal invite sent to parent (notification #1 — bypasses opt-out) |
| Parent Portal Activated | Parent logs in for the first time | Portal live; scoped to enrolled child(ren) |

---

### 3C — Daily Operations Events

| Event | Trigger | Primary Effect |
|---|---|---|
| QR Check-In Scanned | Coordinator scans student QR at gate | CHECK_IN logged; attendance set to Present; allergy alerts surfaced |
| QR Check-Out Scanned | Coordinator scans student QR at departure | CHECK_OUT logged with pickup person ID; Day Care billing unit computed |
| Unregistered Person at Gate | Person not on pickup list presents at check-out | System blocks; Coordinator escalates to Branch Admin |
| Student Marked Absent | No CHECK_IN by end of marking window; Teacher confirms | Parent notification #3 fires immediately (opt-outable) |
| Welfare Concern Flagged | Teacher submits flag with mandatory observation text | WelfareConcernRecord created; Branch Admin notification #8 fires |
| Chronic Absence Threshold Crossed | System detects threshold breach on every Absent mark | Branch Admin notification #9 fires with link to student attendance history |
| Attendance Corrected | Authorised role corrects within time window | Correction logged with original status, new status, actor, timestamp, reason |

---

### 3D — Financial Events

| Event | Trigger | Primary Effect |
|---|---|---|
| Opening Balance Entered | Branch Admin enters historical balance | Financial starting point established; feeds financial dashboard |
| Invoice Created | CoFee import or Accountant/Branch Admin action | Financial obligation recorded; opens balance window closes on first invoice |
| Payment Recorded | CoFee import, Razorpay confirmation, or manual recording | Payment applied against invoice; financial dashboard updated |
| CoFee Import Run | Accountant uploads XLSX | Rows matched and written; exceptions routed; import timestamp updated |
| CoFee Import Exception Raised | Unmatched row or conflict detected | Exception routed to resolution queue; three resolution options available |

---

### 3E — Progress Tracking Events

| Event | Trigger | Primary Effect |
|---|---|---|
| Progress Card Window Opened | Branch Admin's configured open date reached | Teachers and Branch Admin notified in-app (notification #5) |
| Progress Card Submitted | Teacher submits draft to Branch Admin | Card visible to Branch Admin; attribution permanent |
| Progress Card Published | Branch Admin publishes | Card visible to parent portal; attendance snapshot locked; notification #12 fires |
| Progress Card Returned | Branch Admin returns submitted card | Branch Admin must explicitly reopen; Teacher notified; re-edit loop begins |

---

### 3F — Safety Events

| Event | Trigger | Primary Effect |
|---|---|---|
| Incident Report Created | Teacher or Branch Admin creates formal record | Incident record in INTERNAL state; Branch Admin notified |
| Incident Report Published | Branch Admin decides to publish | Parent notification #7 fires (bypasses opt-out); parent sees selected fields only |
| Pickup List Entry Added | Branch Admin adds authorised person | Entry logged; mandatory parent notification fires (bypasses opt-out) |
| Pickup List Entry Deactivated | Branch Admin deactivates entry (minimum one active required) | Entry logged; mandatory parent notification fires (bypasses opt-out) |
| Allergy Protocol Confirmation Logged | Teacher confirms before saving Meal entry for allergy-flagged student | Immutable compliance audit trail entry created |

---

### 3G — Student Lifecycle Events

| Event | Trigger | Primary Effect |
|---|---|---|
| Student Discontinued | Branch Admin confirms after per-invoice acknowledgment | All three switches disabled simultaneously; access revoked; status: Discontinued |
| Year-End Rollover Confirmed | Branch Admin confirms after per-invoice acknowledgment | Active → Completed Programme; all switches reset to ON |
| Bonafide Certificate Issued | Branch Admin (no dues) or Admin (dues exist) confirms | Irreversible; parent portal revoked; status: Bonafide Issued; governance log entry if dues existed |
| Transfer Certificate Issued | Branch Admin (no dues) or Admin (dues exist) confirms | Irreversible; parent portal revoked; status: TC Issued; governance log entry if dues existed |

---

### 3H — Staff Events

| Event | Trigger | Primary Effect |
|---|---|---|
| Staff Exit Confirmed | Both gates cleared; Branch Admin confirms | Profile read-only; access revoked |
| Teacher Exit Gate Cleared (Gate 1) | All Primary slots reassigned | Teacher exit unblocked from Gate 1 |
| Salary Hold Resolved (Gate 2) | Hold formally released or degree returned | Staff exit unblocked from Gate 2 |
| Certification Expiry Alert Triggered | Certification approaches expiry threshold | Branch Admin notification #11 fires with document details |

---

## 4. Documents

Formal records that are produced by or consumed within Happy Feet operations.

| Document | Created by | Purpose | Irreversible |
|---|---|---|---|
| Admission Application Form | Parent (public form) or Branch Admin (walk-in) | Entry point to student intake | No — Declined applications can be reopened |
| Progress Card | Teacher (draft/submit); Branch Admin (publishes) | Formal termly academic assessment; PDF-exportable | No — revisions create new versions; original archived |
| Transfer Certificate | Branch Admin or Admin | Formal record of student transfer; physical TC produced externally | Yes |
| Bonafide Certificate | Branch Admin or Admin | Formal record of programme completion; physical certificate produced externally | Yes |
| CoFee XLSX Import File | CoFee (external) | Monthly financial sync from CoFee to Happy Feet | N/A — import only |
| Day Care Billing Summary Export | Accountant (generates from M7) | Computed billing units exported for CoFee invoice generation | N/A — export only |
| Staff Employment Documents | Branch Admin (uploads) | Mandatory onboarding documents; stored per staff profile | No — documents can be renewed; history retained |
| Student Profile Documents | Parent (uploads via portal) or Branch Admin | Programme-specific enrollment requirements; individually waivable with reason | No — documents can be updated |
| CSV Exports | Authorised roles (generate on demand) | Role-filtered data extracts; every generation access-logged; Aadhaar/medical excluded | N/A — export only |
| Monthly Governance Digest Email | System (auto-generated, 1st of month) | Override and exception summary for Admin; covers all branches | N/A — informational |

---

## 5. Entity Relationships

A summary of key entity-to-entity connections. This is not a data model — it describes business ownership and dependency.

| Entity | Belongs to / Owned by | Has / Contains |
|---|---|---|
| Branch | System | Academic Years, Classes, Staff Profiles, Student Profiles, Audit Log |
| Academic Year | Branch | Terms, Programmes, School Calendar, Timetable |
| Term | Academic Year | Progress Card Submission Window |
| Programme | Academic Year / Branch | Classes, Milestones, Fee Structure (template), Document Requirements |
| Class | Programme / Branch | Teacher Slots, Students, Timetable, Waitlist |
| Student Profile | Branch | Attendance Records, Progress Cards, Daily Activity Log entries, Welfare Concern Records, Incident Reports, PTM Notes, Invoices, Opening Balance, Authorised Pickup List |
| Admission Application | Branch | Converts to Student Profile on enrollment |
| Waitlist | Class | Ordered references to Admission Applications |
| Fee Structure (snapshot) | Student Profile (one per enrolled student) | Drives Invoice creation |
| Invoice | Student Profile | Payments |
| Payment | Invoice | N/A |
| Opening Balance | Student Profile | N/A |
| Attendance Record | Student Profile / Class / Date | CHECK_IN / CHECK_OUT events; correction log |
| Progress Card | Student Profile / Term | Milestone ratings, attendance snapshot, version history |
| Milestone | Programme | Appears in every Progress Card for that programme |
| Daily Activity Log Entry | Student Profile / Date | Type-specific activity data |
| Welfare Concern Record | Student Profile | Status history (immutable append) |
| Incident Report | Student Profile | Status (INTERNAL / PUBLISHED) |
| Authorised Pickup List | Student Profile | Named entries with active/inactive status |
| PTM Note | Student Profile | N/A |
| Transport Zone | Branch / Academic Year | Assigned per student as a fee line item |
| Staff Profile | Branch (via Branch Binding) | Teacher Slots on Classes, Leave Records, Salary Holds, Certifications |
| Leave Record | Staff Profile | Linked Staff Attendance entries |
| Salary Hold | Staff Profile | Blocks Staff Exit (Gate 2) |
| WhatsApp Template | Branch (branch-scoped) or System (global) | Versioned content history |
| School Inbox Thread | Student Profile / Parent | Messages and staff replies |
| Fee Query Thread | Student Profile / Parent | Parent fee queries and Accountant replies |
| Audit Log Entry | All modules (cross-cutting) | N/A |
| Governance Log Entry | Audit Log (filtered subset) | N/A |
| Migration Checklist | Branch | Six checklist items; controls Migration Mode exit |
| Historical Attendance Marker | Student Profile | Single value; locks on Migration Mode exit |

---

## 6. Lifecycle State Maps

### 6.1 Student Status

```
Admission Application
  Applied
  └── Approved (programme assigned)
      └── Enrolled (all four conditions met)
          └── Active (first payment recorded; portal activated)
              ├── Discontinued (irreversible; all three switches off)
              ├── Completed Programme (year-end rollover; switches reset ON)
              │   └── Active (re-admission, same ID, same history)
              ├── Bonafide Issued (irreversible; portal revoked)
              └── Transfer Certificate Issued (irreversible; portal revoked)
      └── Waitlisted (class full; approved applicant held)
  └── Declined (reversible; can be reopened)

Discontinued → Active (re-admission, same ID, same history, dues persist)

Note: Bonafide Issued and Transfer Certificate Issued are mutually exclusive.
```

---

### 6.2 Progress Card Status

```
DRAFT (Teacher editing; invisible to Branch Admin and parents)
└── SUBMITTED (Teacher submits; visible to Branch Admin)
    ├── DELEGATE_REVIEWED (if delegate configured; note added for Branch Admin)
    └── PUBLISHED (Branch Admin publishes; attendance snapshot locked; visible to parents)
        └── ARCHIVED (when Branch Admin opens a revision)
    └── RETURNED (Branch Admin returns; Branch Admin must reopen for Teacher to re-edit)
        └── SUBMITTED (re-edit loop — continues until PUBLISHED)
```

---

### 6.3 Welfare Concern Record Status

```
OPEN (created by Teacher; permanent; Branch Admin must review within 3 school days)
└── REVIEWED (Branch Admin marks with note)
└── ESCALATED (Branch Admin marks; formal incident report may follow)

Note: Record is permanent and immutable. Status changes are appended, not overwritten.
```

---

### 6.4 Incident Report Status

```
INTERNAL (created; Branch Admin reviews)
└── PUBLISHED (Branch Admin publishes; parent sees selected fields only — never internal notes or staff witnesses)

Note: One-way transition. Append-only. Never deleted.
```

---

### 6.5 School Inbox / Fee Query Thread Status

```
Active (parent initiates; staff replies; parent can reply back)
└── CLOSED (Branch Admin closes; terminal; no further messages)

Note: Messages cannot be deleted by any role. Retained 3 years.
```

---

### 6.6 Migration Mode — Per Branch

```
Normal Operation
└── Migration Mode (Admin enters; invoice generation, payment links, portal activation suppressed)
    └── Live (Admin exits after checklist cleared; all suppressed events fire simultaneously; CANNOT re-enter)
```

---

### 6.7 Staff Status

```
Active
└── On Leave (linked to approved Leave Record; temporary)
    └── Active (returns from leave)
└── Exited (profile read-only; access revoked; requires Gate 1 and Gate 2 cleared)
```

---

### 6.8 CoFee Import Row Status

```
Each row:
├── Case A: Matches COFEE_IMPORT invoice → amount_paid_to_date updated; not an exception
├── Case B: Matches HAPPY_FEET_NATIVE invoice + matching Razorpay transaction ID → RAZORPAY_COFEE_CONFLICT exception
│   └── Accountant reviews; voiding Razorpay record requires Branch Admin approval
├── Case C: Matches HAPPY_FEET_NATIVE invoice + no Razorpay duplicate → imported as payment; not an exception
├── Case D: No matching invoice → COFEE_IMPORT invoice + payment created; normal flow
└── Unmatched (no student found) → Exception queue
    ├── Link to correct student manually
    ├── Mark not applicable
    └── Flag for 7-day follow-up

Note: No row is ever silently dropped. Every row has a traceable outcome.
```

---

## Summary

| Element | Count |
|---|---|
| Actors | 11 |
| Entities | 34 |
| Business Events | 27 |
| Documents | 10 |
| Lifecycle State Maps | 8 |

The domain's structural centre is the **Student Profile** — every safety rule, financial obligation, daily record, academic assessment, and communication thread ultimately references it. The domain's defining constraint is the **age group (18 months–6 years)**: three entities exist exclusively because children in this age group cannot advocate for themselves — the Authorised Pickup List, the Welfare Concern Record, and the Daily Activity Log entry's allergy protocol audit trail.

---

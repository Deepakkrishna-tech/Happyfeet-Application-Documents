<!-- ABOUT
File: DOMAIN_GLOSSARY.md
Purpose: Defines every domain term used across Stages 1–6, with Indian context where applicable, related terms, role usage, and process usage.
Scope: ~100 terms across roles, academic, student lifecycle, safety, financial, staff, communication, and governance domains; excludes technical/database vocabulary.
Dependencies: DOMAIN_OVERVIEW.md, BUSINESS_CAPABILITY_MODEL.md, ORGANIZATION_MODEL.md, BUSINESS_PROCESSES.md, BUSINESS_RULES.md, DOMAIN_MODEL.md
Stage: Stage 7 — Domain Glossary
-->

# Domain Glossary — Happy Feet School ERP

**Stage:** 7 — Domain Glossary
**Source:** PRODUCT_OVERVIEW_v2.md, Stages 1–6 artifacts
**Date:** 2026-06-05
**Status:** Complete

---

## Reading Guide

Terms are grouped by domain. Within each group, entries appear in the order a new team member would encounter them operationally.

Each entry follows this structure:

> **Term** — Definition. *(Indian context if applicable.)* | **Related:** X, Y | **Used by:** roles | **Used in:** process codes

Process codes reference BUSINESS_PROCESSES.md (P01–P32).

---

## A — Roles and Actors

**Admin** — The school owner or a designated super-administrator. Holds full authority across all branches and all modules. The only role that can enter and exit Migration Mode, correct attendance beyond 30 days, issue Bonafide certificates when outstanding dues exist, create Branch Admin accounts, and access the cross-branch governance log. Multiple Admin accounts are permitted. | **Related:** Branch Admin, Governance Log, Admin Override Confirmation | **Used by:** Admin | **Used in:** All processes

**Branch Admin** — The principal or head of a single branch. The primary operational actor for day-to-day school management. Inherits the full capability set of Coordinator, Teacher, and Accountant within the branch. Cannot access other branches. | **Related:** Admin, Coordinator, Teacher, Accountant | **Used by:** Branch Admin | **Used in:** P01–P32

**Coordinator** — The admin assistant or front-desk person. Handles intake, gate operations, and school inbox. Can advance admissions but cannot complete enrollment independently. | **Related:** School Inbox, Authorised Pickup List, Visitor Log | **Used by:** Coordinator | **Used in:** P04, P07, P08, P27, P28

**Teacher** — A class teacher assigned to one or more class slots (Primary, Assistant, or Temporary). Scope is strictly limited to students in assigned class slots. Cannot access fee data, medical records, or students outside assigned classes. | **Related:** Class Slot, Progress Card, Welfare Concern Record, Daily Activity Log | **Used by:** Teacher | **Used in:** P08, P09, P16, P22, P23

**Accountant** — Finance staff responsible for financial visibility, CoFee XLSX import, fee communication, and the Fee Query channel. Cannot enter opening balances or record expense entries. | **Related:** CoFee Import, Fee Query Channel, Invoice, HAPPY_FEET_NATIVE | **Used by:** Accountant | **Used in:** P11, P12, P13, P14, P15

**Parent / Guardian** — A child's family member. Holds read-only, child-scoped portal access. Access created on first payment recording — not at enrollment. Cannot self-register. Cannot modify the pickup list. Cannot see other students, staff data, or (in V1) invoice history. | **Related:** Student Profile, School Inbox, Fee Query Channel, Parent Portal | **Used by:** Parent | **Used in:** P04, P14, P28

**Child / Student** — The child enrolled in the school. Never a system user. The central entity around which every safety rule, health record, and daily log is organised. | **Related:** Student Profile, Admission Application | **Used by:** All roles (as subject) | **Used in:** All processes

**CoFee** — The external fee management platform used by the school. In V1, CoFee owns the primary invoice generation, fee reminder, and payment collection lifecycle. Happy Feet imports CoFee financial data via XLSX export. CoFee is a V1 bridge; the system is designed to replace it with a native Razorpay integration in V2 without schema migration. *(Indian context: commonly used fee and ERP software in Indian private schools.)* | **Related:** CoFee XLSX Import, COFEE_IMPORT, Opening Balance | **Used by:** Accountant | **Used in:** P11, P13

**Razorpay** — The payment gateway used for ad-hoc payment link generation in V1. When a parent pays via a Razorpay checkout link, payment confirmation is automatically written to Happy Feet. In V2, Razorpay replaces CoFee as the full billing engine. *(Indian context: leading Indian payment gateway.)* | **Related:** HAPPY_FEET_NATIVE, Razorpay Payment Link, RAZORPAY_COFEE_CONFLICT | **Used by:** Accountant, Branch Admin, Admin | **Used in:** P12

---

## B — Academic Structure

**Academic Year** — The annual operational period for a branch. All downstream modules (admissions, attendance, fees, progress) depend on a completed Academic Year configuration. The upcoming year can be configured while the current year is active; it activates automatically at midnight on its configured start date. | **Related:** Term, Programme, 9-Step Setup | **Used by:** Branch Admin, Admin | **Used in:** P01

**9-Step Setup** — The mandatory sequential configuration of the Academic Year. Must complete in order before any operational module can function. Steps: (1) academic year dates, (2) term dates + submission windows, (3) programme definitions, (4) classes, (5) transport zones, (6) school calendar, (7) timetable, (8) milestones, (9) document types. | **Related:** Academic Year, Programme, Class | **Used by:** Branch Admin, Admin | **Used in:** P01

**Term** — A sub-period within an Academic Year. The scheduling unit for progress card submission windows. Happy Feet defaults to 3 terms per year; the number is configurable. | **Related:** Academic Year, Submission Window, Progress Card | **Used by:** Branch Admin, Admin | **Used in:** P01, P16

**Submission Window** — The period during which Teachers can submit progress cards and Branch Admin can review and publish them. Open and close dates are set per term, per programme, by Branch Admin during the 9-Step Setup. There is no system-imposed minimum or maximum duration. | **Related:** Term, Progress Card, Milestone | **Used by:** Branch Admin, Teacher | **Used in:** P16

**Programme** — A course offering with its own billing model, attendance model, milestone set, and progress card inclusion. Fully configurable; no fixed list. Default set is pre-loaded, not hardcoded.

| Programme type | Examples | Billing | Progress cards |
|---|---|---|---|
| Academic | Nursery, PP1, PP2, Grade 1–10 | Term-based | Yes |
| Non-Academic | Play Group, Day Care, Summer Camp | Monthly / Session | Optional |
| Event | Annual Day, field trip | One-off fee | No |

*(Indian context: Play Group, Nursery, PP1, PP2 map to India's early childhood education nomenclature for children aged ~18 months to 6 years.)* | **Related:** Class, Milestone, Fee Structure | **Used by:** Branch Admin, Admin | **Used in:** P01, P04

**Play Group** — A programme for children approximately 18–24 months old. Non-Academic type; simplified milestone set; term-based billing. *(Indian context: equivalent to the youngest preschool intake group in Indian private schools.)* | **Related:** Programme, Nursery | **Used in:** P04

**Nursery** — A programme for children approximately 2.5–3.5 years old. Academic type; full milestone set; term-based billing. *(Indian context: first formal preschool year in Indian private schools.)* | **Related:** PP1, Play Group | **Used in:** P04, P16

**PP1 (Pre-Primary 1)** — A programme for children approximately 3.5–4.5 years old. Academic type; full milestone set; term-based billing. *(Indian context: pre-school year preceding PP2, the final year before primary school.)* | **Related:** Nursery, PP2 | **Used in:** P04, P16

**PP2 (Pre-Primary 2)** — A programme for children approximately 4.5–6 years old. Academic type; full milestone set; term-based billing. The final programme before a child exits the school with a Bonafide Certificate. *(Indian context: equivalent to LKG/UKG in many Indian private schools; the final preschool year.)* | **Related:** PP1, Bonafide Certificate | **Used in:** P04, P16, P20

**Day Care** — A Non-Academic programme for extended childcare hours. Billing is usage-based, derived from QR gate timestamps. Attendance model is hours in care (not present/absent). *(Indian context: extended-hours care operated alongside the regular preschool programme, common in urban Indian private schools.)* | **Related:** Billing Unit, QR Check-In, QR Check-Out, Day Care Billing Summary | **Used in:** P07, P13

**Class** — A named group of students within a programme with a defined capacity and assigned teacher slots. Each class has one mandatory Primary slot and optional Assistant and Temporary slots. | **Related:** Programme, Teacher Slot, Student Profile, Waitlist | **Used by:** Branch Admin, Teacher | **Used in:** P01, P04, P08

**Class Slot** — A named position on a class that a Teacher occupies. Three slot types exist.

| Slot | Mandatory | Notes |
|---|---|---|
| Primary | Yes — every class must have one | All class-level Teacher actions |
| Assistant | No | Identical permissions to Primary within the class |
| Temporary | No — has configurable end date | Access lapses automatically when end date passes; 7-day expiry alert fires |

| **Related:** Teacher, Class, Teacher Exit Gate | **Used by:** Branch Admin, Teacher | **Used in:** P01, P02, P03

**Milestone** — A developmental domain assessed within a progress card. Configured per programme with a full or simplified set. Rating scale is defined at programme setup. | **Related:** Progress Card, Programme, Submission Window | **Used by:** Teacher (rates), Branch Admin (reviews) | **Used in:** P16

**Delegate Reviewer** — A Teacher designated by Branch Admin to review submitted progress cards for a specific class and add a review note visible to Branch Admin only. The delegate cannot publish a card — publication authority remains exclusively with Branch Admin. Configured as a per-class toggle, not a separate role. | **Related:** Progress Card, Branch Admin | **Used by:** Branch Admin (designates), Teacher (reviews) | **Used in:** P16

---

## C — Student Lifecycle

**Student Profile** — The permanent record of a child from admission through all lifecycle transitions. Never deleted. Central entity for all domain operations. Carries: Admission ID, personal details, guardian contacts, emergency contacts, medical records (access-logged), allergy flags, document list, attendance history, progress cards, daily activity log, welfare concern records, incident reports, authorised pickup list. | **Related:** Admission Application, Admission ID, Attendance Record, Authorised Pickup List | **Used by:** All roles | **Used in:** All processes

**Admission Application** — A formal request for a child to attend the school. Submitted via the public form (no login required) or created as a walk-in by Branch Admin. Does not create a Student Profile — the profile is initiated when Branch Admin assigns a programme. | **Related:** Student Profile, Admission ID, Waitlist | **Used by:** Parent (submits), Branch Admin, Coordinator | **Used in:** P04

**Admission ID** — A permanent, immutable identifier assigned to a student at enrollment completion. Cannot be changed. Used as the primary match key for CoFee import. A Legacy Admission ID is preserved for migrated students alongside the new Happy Feet Admission ID. | **Related:** Student Profile, CoFee Import, Admission Number Normalisation | **Used by:** All roles | **Used in:** P04, P11

**Admission Number Normalisation** — A one-time configuration that reconciles differing admission number formats between CoFee and Happy Feet. Prevents every CoFee import row from going to the exception queue due to format mismatch. Configured in M7 before the first import; applied automatically on all subsequent imports. Each import row logs whether the match was exact or normalised. | **Related:** CoFee Import, Admission ID | **Used by:** Branch Admin (configures), Accountant (import) | **Used in:** P11

**Application Status Flow** — The ordered progression of an admission application:
- **Applied** — application submitted; no Student Profile yet
- **Approved** — programme assigned by Branch Admin; Student Profile initiated
- **Enrolled** — all four enrollment conditions met; fee structure snapshotted; Admission ID assigned
- **Declined** — Branch Admin or Admin records a reason; application permanently retained; can be reopened (the term "Rejected" is never used)
- **Waitlisted** — class is full; approved applicant placed in per-class ordered queue

**Related:** Admission Application, Enrollment Conditions, Waitlist | **Used by:** Branch Admin, Coordinator | **Used in:** P04

**Enrollment Conditions** — Four independent conditions that must all be satisfied before enrollment can complete. No condition can substitute for another.

1. Programme assigned
2. All required documents cleared — or each missing document individually waived with a recorded reason (blanket waiving not permitted)
3. Class capacity available — or a capacity override recorded with a reason
4. At least one active Authorised Pickup List entry recorded for the student

| **Related:** Admission Application, Authorised Pickup List, Document Waiver | **Used by:** Branch Admin | **Used in:** P04

**Document Waiver** — The act of individually waiving a missing document requirement at enrollment. Every missing document requires its own recorded reason. A single reason cannot cover multiple documents. Waivers are permanently logged. | **Related:** Enrollment Conditions, Student Profile | **Used by:** Branch Admin | **Used in:** P04

**Capacity Override** — The act of enrolling a student into a class that has reached its configured capacity. Requires a recorded reason. Permanently logged. Branch Admin can add the student to the Waitlist instead of overriding. | **Related:** Class, Waitlist, Enrollment Conditions | **Used by:** Branch Admin | **Used in:** P04

**Waitlist** — An ordered queue of approved applicants for a full class. Ordered by date added. When a class slot opens (via discontinuation, transfer, or capacity increase), Branch Admin sees the next waitlisted applicant and can proceed with enrollment. No automated parent notification — Branch Admin contacts the parent directly. | **Related:** Class, Admission Application, Enrollment Conditions | **Used by:** Branch Admin | **Used in:** P06

**Fee Snapshot** — A full data copy of the fee structure captured at enrollment completion. Governs billing for that student's enrolment period. Subsequent changes to programme-level fees do not automatically update existing snapshots; an explicit update is required. | **Related:** Fee Structure, Invoice, Enrollment | **Used by:** Branch Admin, Accountant | **Used in:** P04, P10

**Active** — A student status indicating the student is currently enrolled and operationally live — attending, being billed, and accessible on the parent portal. | **Related:** Student Status, Discontinued, Completed Programme | **Used in:** P18, P19

**Discontinued** — A student status indicating the student has left the school mid-year or did not return. All three lifecycle switches (billing, attendance, parent portal) are disabled simultaneously in one atomic operation on confirmation. Outstanding dues persist — discontinuation does not clear financial obligations. Irreversible. | **Related:** Active, Student Lifecycle Switch, Outstanding Dues | **Used by:** Branch Admin | **Used in:** P18

**Completed Programme** — A student status indicating the student has reached the end of an academic year via the year-end rollover. All three lifecycle switches reset to ON at rollover. The student can re-enroll (re-admission) using the same Admission ID and prior history. | **Related:** Year-End Rollover, Active, Re-admission | **Used by:** Branch Admin | **Used in:** P19

**Year-End Rollover** — The process by which all Active students transition to Completed Programme at the end of an academic year. Always manually triggered by Branch Admin — never automatic. Branch Admin reviews a preview, adjusts class assignments, acknowledges each outstanding invoice individually with a recorded reason, and confirms. | **Related:** Active, Completed Programme, Outstanding Dues | **Used by:** Branch Admin | **Used in:** P19

**Re-admission** — The process by which a previously discontinued or programme-completed student returns to the school. Not a new application. The existing Student Profile is reopened with the same Admission ID and full prior history. Outstanding dues are not cleared. | **Related:** Student Profile, Admission ID, Discontinued, Completed Programme | **Used by:** Branch Admin | **Used in:** P05

**Bonafide Certificate** — A formal document issued to a student who has completed their programme (typically PP2). Irreversible — parent portal access is revoked and student status becomes "Bonafide Issued." The physical certificate is produced externally. Branch Admin issues when no outstanding dues exist; Admin is required when dues exist (governance log entry created). Mutually exclusive with Transfer Certificate. *(Indian context: a school-leaving certificate confirming a student was enrolled and has completed the programme; required for admission to primary school in India.)* | **Related:** Transfer Certificate, Outstanding Dues, Governance Log | **Used by:** Branch Admin (no dues), Admin (dues exist) | **Used in:** P20

**Transfer Certificate (TC)** — A formal document issued when a student transfers to another school. Irreversible — parent portal access is revoked and student status becomes "Transfer Certificate Issued." The physical TC is produced externally. Requires per-invoice acknowledgment of outstanding dues. Mutually exclusive with Bonafide Certificate. *(Indian context: a mandatory document for admission to any other school in India; governed by state education board rules.)* | **Related:** Bonafide Certificate, Outstanding Dues, Governance Log | **Used by:** Branch Admin (no dues), Admin (dues exist) | **Used in:** P21

**Student Lifecycle Switch** — One of three independent per-student toggles: billing on/off, attendance screen on/off, parent portal access on/off. Each switch change requires a logged reason. At discontinuation, all three switches are disabled simultaneously in one atomic operation. At rollover, all three switches reset to ON. | **Related:** Discontinued, Year-End Rollover, Active | **Used by:** Branch Admin, Admin | **Used in:** P18, P19

**Student Category** — A named grouping assigned to a student at enrollment to apply a different fee structure (e.g. Standard, Staff Child, Subsidised). Created by Branch Admin in fee management settings. Used as a filter and grouping dimension in financial reports. | **Related:** Fee Structure, Invoice | **Used by:** Branch Admin | **Used in:** P04, P10

**Sibling Linking** — The system behaviour that flags a likely sibling relationship when two admission applications share the same parent phone number but different child names. Branch Admin takes one of three actions: Confirm as siblings, Confirm as unrelated, or Flag for follow-up. No profile merging in V1. | **Related:** Admission Application, Student Profile | **Used by:** Branch Admin | **Used in:** P04

---

## D — Attendance and Daily Operations

**QR Check-In** — The primary gate arrival process. Parent presents the student's QR code; Coordinator scans; system displays student photo, name, class, and any active allergy alerts; Coordinator confirms; CHECK_IN event logged; attendance status set to Present. | **Related:** QR Check-Out, Attendance Record, Authorised Pickup List, Allergy Flag | **Used by:** Coordinator, Teacher | **Used in:** P07, P08

**QR Check-Out** — The primary gate departure process. Coordinator scans student QR; system displays the active Authorised Pickup List; Coordinator selects who is collecting; Coordinator confirms; CHECK_OUT event logged with pickup person ID. System blocks and alerts if the presenting person is not on the pickup list. | **Related:** QR Check-In, Authorised Pickup List, Day Care Billing Unit | **Used by:** Coordinator | **Used in:** P07

**Billing Unit (Day Care)** — The unit of billing computed from gate timestamps for Day Care students. Full day (CHECK_OUT − CHECK_IN ≥ 4 hours) = 1.0 unit; half day (< 4 hours) = 0.5 unit. Billing accuracy depends on QR check-out being recorded; unscanned checkouts default to the end of the configurable school day. | **Related:** QR Check-Out, Day Care Billing Summary, Day Care | **Used by:** Accountant | **Used in:** P07, P13

**Day Care Billing Summary** — The export generated by Accountant at the end of the Day Care billing period. Contains per-student billing units computed from QR timestamps. Accountant takes this export to CoFee to generate Day Care invoices. | **Related:** Billing Unit, CoFee, Accountant | **Used by:** Accountant | **Used in:** P13

**Marking Window** — The daily time window during which Teachers mark attendance (default: 10:00 AM–10:00 PM). If no mark is made within the window, Branch Admin is alerted and can bulk-mark the class as Present with a logged reason. | **Related:** Attendance Record, Teacher, Branch Admin | **Used by:** Teacher | **Used in:** P08

**Manual Register Fallback** — The attendance marking method used when the QR system is unavailable. Teacher marks each student Present, Absent, Late, or Half-Day directly on the attendance screen. Logged as "Manual mark — QR system unavailable." Day Care billing on manual-mark days requires retrospective Branch Admin action. | **Related:** QR Check-In, Attendance Record | **Used by:** Teacher | **Used in:** P07, P08

**Attendance Correction** — A change to a previously recorded attendance status. Time-bounded by role: Teacher (same day — no reason; Day 2–7 — one sentence minimum); Branch Admin (Day 8–30 — logged); Admin (unlimited — governance log entry). All limits are calendar days, not working days. Every correction records original status, new status, actor, timestamp, and reason. | **Related:** Attendance Record, Governance Log | **Used by:** Teacher, Branch Admin, Admin | **Used in:** P08

**Chronic Absence Alert** — An automated in-app notification (#9) sent to Branch Admin when a student's absence pattern crosses a configured threshold. Branch Admin configures per programme: threshold type (consecutive absences and/or total absences in a period), consecutive threshold (default 3), total threshold (default 5 in 30 days). Fires immediately when the threshold is crossed on an Absent mark. | **Related:** Attendance Record, Branch Admin | **Used by:** Branch Admin (configures, receives) | **Used in:** P08

**Welfare Concern Record** — A formal, permanent, immutable observation created by a Teacher when they observe a student wellbeing concern. Requires a mandatory free-text description. Branch Admin must review within 3 school days; one reminder fires if unreviewed. Teachers see only their own flags; Branch Admin sees all for the branch; parents never see welfare concern records under any circumstances. | **Related:** Welfare Concern Observation, Incident Report, Branch Admin | **Used by:** Teacher (creates), Branch Admin (reviews) | **Used in:** P22

**Daily Activity Log Entry** — A per-child structured record of a specific activity during the school day. Immutable after saving; corrections appended as new entries. Entry types: Meal, Nap, Toilet, Activity, Health Check, General Note. Primary parent engagement channel — replaces informal WhatsApp updates. Toilet entry detail is never shown to parents (frequency only). Meal entries for allergy-flagged students require Teacher to confirm allergy protocol — creates an immutable compliance audit trail. | **Related:** Student Profile, Allergy Protocol Audit Trail, Parent Portal | **Used by:** Teacher (creates), Parent (reads), Coordinator (reads) | **Used in:** P09

**Allergy Protocol Audit Trail** — The permanent, immutable log created when a Teacher confirms "allergy protocol was followed" before saving a Meal entry for a student with active allergy alerts. Logged with actor and timestamp. Visible to Branch Admin and Admin only. Cannot be retroactively added or removed. | **Related:** Allergy Flag, Daily Activity Log, Teacher | **Used by:** Branch Admin, Admin (view) | **Used in:** P09

**Historical Attendance Marker** — A single per-student field entered by Branch Admin during Migration Mode: the number of school days the student was present before the system go-live date, drawn from the paper register. Feeds into the academic year attendance percentage and progress card attendance snapshot. Locks permanently on Migration Mode exit. Accuracy depends entirely on the paper register data quality. | **Related:** Migration Mode, Progress Card, Attendance Record | **Used by:** Branch Admin (enters) | **Used in:** P29

---

## E — Safety

**Authorised Pickup List** — The per-student list of named individuals permitted to collect the child at the school gate. An absolute safety enforcement mechanism: the system blocks release to anyone not on this list without a Branch Admin decision and a governance log entry. Every addition and deactivation is permanently logged with actor, reason, and timestamp and triggers a mandatory parent notification (bypasses opt-out). At least one active entry required at all times from enrollment; the guardian is not automatically on the list. | **Related:** QR Check-Out, Enrollment Conditions, Governance Log | **Used by:** Branch Admin (manages), Coordinator (views — gate verification), Teacher (views — own class) | **Used in:** P07, P24

**Incident Report** — A formal record of a notable event (injury, behavioural episode, medical event) during school hours. Created by Teacher (own class) or Branch Admin. Append-only; never deleted. Branch Admin decides to publish to the parent portal or keep internal. Internal notes and staff witness details are never shown to parents under any circumstances — even in published reports. Publication triggers mandatory parent notification #7 (bypasses opt-out). | **Related:** Welfare Concern Record, Branch Admin, Parent Portal | **Used by:** Teacher (creates), Branch Admin (reviews, publishes) | **Used in:** P23

**Allergy Flag** — An active alert on a student's profile indicating a confirmed allergy or critical medication requirement. Visible to Branch Admin, Admin, Coordinator (read-only), and Teacher (own class, read-only). Surfaced at QR check-in (Coordinator sees inline) and at Meal entry in the daily activity log (Teacher must confirm protocol before saving). Distinct from full medical records, which are restricted to Branch Admin and Admin only. | **Related:** Daily Activity Log, QR Check-In, Allergy Protocol Audit Trail | **Used by:** Coordinator (gate), Teacher (meal logging) | **Used in:** P07, P09

**PTM Note** — A Parent-Teacher Meeting observation record stored against a student's profile. Internal staff record only — never visible to parents. Created by Branch Admin or Teacher. | **Related:** Student Profile | **Used by:** Branch Admin, Teacher | **Used in:** Referenced in staff-parent communication workflows

---

## F — Finance

**Fee Structure** — The billing configuration for a programme: billing model, billing schedule, per-term amounts, student category, transport zone. Configured per programme by Branch Admin at academic year setup. Snapshotted as a full data copy at enrollment completion (see Fee Snapshot). | **Related:** Fee Snapshot, Programme, Invoice, Transport Zone | **Used by:** Branch Admin | **Used in:** P10

**Opening Balance** — A historical outstanding amount manually entered for a student migrated into Happy Feet. Must be entered before the first invoice is generated for that student. Window cannot close during Migration Mode. Window closes permanently on first invoice generation. No correction path exists after the window closes. | **Related:** Migration Mode, Invoice, Financial Dashboard | **Used by:** Branch Admin, Admin | **Used in:** P15

**Invoice** — A formal record of a financial obligation. Two source types exist:

| Source | Created by | Blocked during Migration Mode |
|---|---|---|
| COFEE_IMPORT | CoFee XLSX import | No |
| HAPPY_FEET_NATIVE | Accountant, Branch Admin, or Admin | Yes |

**Related:** Payment, Fee Snapshot, CoFee Import, HAPPY_FEET_NATIVE | **Used by:** Accountant, Branch Admin, Admin | **Used in:** P11, P12

**COFEE_IMPORT** — The invoice source type for records created by the CoFee XLSX import. These invoices are created and updated by the import process, not manually. | **Related:** Invoice, CoFee Import | **Used in:** P11

**HAPPY_FEET_NATIVE** — The invoice source type for invoices created directly within Happy Feet by Accountant, Branch Admin, or Admin. Used for ad-hoc fee collection outside the CoFee billing cycle. Blocked during Migration Mode. | **Related:** Invoice, Razorpay Payment Link | **Used by:** Accountant, Branch Admin, Admin | **Used in:** P12

**Razorpay Payment Link** — A Razorpay checkout URL generated from a HAPPY_FEET_NATIVE invoice and shared with the parent. When the parent pays, Razorpay confirms and Happy Feet automatically writes the payment record. Blocked during Migration Mode. Every link generation is logged with actor, timestamp, and student. | **Related:** HAPPY_FEET_NATIVE, Invoice, Payment | **Used by:** Accountant, Branch Admin, Admin | **Used in:** P12

**CoFee XLSX Import** — The manual process by which Accountant uploads a CoFee XLSX report to Happy Feet. Each row is matched against the student registry by Admission Number (primary) or Phone Number (secondary), with normalisation applied if configured. Every row has a traceable outcome — matched and written, skipped as a duplicate, or manually resolved via the exception queue. The import timestamp is visible on the financial dashboard for Accountant, Branch Admin, and Admin. | **Related:** COFEE_IMPORT, Import Exceptions, Admission Number Normalisation | **Used by:** Accountant | **Used in:** P11

**CoFee Import Cases** — The four match outcomes for each CoFee import row:
- **Case A:** Row matches an existing COFEE_IMPORT invoice → amount_paid_to_date updated; not an exception
- **Case B:** Row matches a HAPPY_FEET_NATIVE invoice AND transaction ID matches an existing Razorpay payment → RAZORPAY_COFEE_CONFLICT exception; Branch Admin approval required to void the Razorpay record
- **Case C:** Row matches a HAPPY_FEET_NATIVE invoice AND no Razorpay duplicate → imported as a new payment; not an exception
- **Case D:** No matching invoice → COFEE_IMPORT invoice and payment record created; normal import

| **Related:** CoFee XLSX Import, RAZORPAY_COFEE_CONFLICT | **Used by:** Accountant | **Used in:** P11

**Import Exception** — A CoFee import row that cannot be automatically resolved. Routed to the import exceptions list. Three resolution options: (1) link the row to the correct student manually, (2) mark as not applicable, (3) flag for 7-day follow-up reminder. No row is silently dropped. | **Related:** CoFee Import Cases, CoFee XLSX Import | **Used by:** Accountant | **Used in:** P11

**RAZORPAY_COFEE_CONFLICT** — A CoFee import exception triggered when a CoFee row matches a HAPPY_FEET_NATIVE invoice and the transaction ID also matches a Razorpay payment already recorded. Voiding the Razorpay payment record requires Branch Admin approval — Accountant cannot void unilaterally. | **Related:** CoFee Import Cases, HAPPY_FEET_NATIVE, Razorpay | **Used by:** Accountant, Branch Admin | **Used in:** P11

**SOFT_DUPLICATE** — A CoFee import flag raised when a row appears to be a duplicate of a previously imported record. Accountant can override without Branch Admin approval but must enter a reason (one sentence minimum). Override permanently logged; Branch Admin receives an informational in-app notification. | **Related:** CoFee XLSX Import | **Used by:** Accountant | **Used in:** P11

**Outstanding Dues** — A student's total unpaid financial obligations. Persist through discontinuation, re-admission, rollover, TC issuance, and Bonafide issuance. Must be individually acknowledged with a recorded reason (per invoice, not in bulk) before discontinuation, rollover, or certificate issuance can proceed. | **Related:** Invoice, Discontinuation, Year-End Rollover, Bonafide Certificate, Transfer Certificate | **Used by:** Branch Admin, Admin | **Used in:** P18, P19, P20, P21

**Transport Zone** — A distance band with a fixed monthly billing rate for school transport. Configured per branch/academic year. Students opt-in per zone. Billing is fixed regardless of actual usage. Changes take effect from the next billing period. | **Related:** Fee Structure, Programme | **Used by:** Branch Admin | **Used in:** P10

**Fee Query Channel** — A dedicated parent-to-Accountant communication channel for fee-related questions, separate from the general school inbox. Messages route to Accountant's fee communication log in M7. Branch Admin and Admin can also view and reply. Coordinator has no access. Threads are permanently logged; CLOSED status is terminal. | **Related:** School Inbox, Accountant, Invoice | **Used by:** Parent (initiates), Accountant, Branch Admin, Admin | **Used in:** P14

**Expense Log** — The branch record of operational expenses. Entries created by Branch Admin or Admin, each with an expense category. Accountant can view the expense log but cannot record expense entries. V2 adds budget management per category. | **Related:** Financial Dashboard | **Used by:** Branch Admin (records), Accountant (views) | **Used in:** P30

---

## G — Staff

**Staff Profile** — The employment record for a school staff member. Contains: name, designation, employment type, role, branch bindings, salary history (append-only), employment documents, leave records, certifications with expiry dates, status. Never deleted. Profile becomes read-only on exit. | **Related:** Branch Binding, Teacher Slot, Leave Record, Salary Hold | **Used by:** Branch Admin, Admin | **Used in:** P02, P03

**Branch Binding** — The assignment that links a staff member to a branch under their role. Created and deactivated by Admin only. A staff member with multiple branch bindings sees a branch selector on login and chooses one working branch per session. All actions are scoped and logged to the selected branch. | **Related:** Staff Profile, Multi-Branch | **Used by:** Admin | **Used in:** P02

**Teacher Exit Gate (Gate 1)** — The requirement that all Primary slots on every class the Teacher is assigned to must be reassigned before the Teacher's exit can proceed. Applies to Teachers only. | **Related:** Class Slot, Staff Exit | **Used by:** Branch Admin | **Used in:** P03

**Salary Hold Exit Gate (Gate 2)** — The requirement that all salary holds must be formally resolved before any staff member's exit can proceed. Two hold types: first-month salary held (formally released) and degree in custody (returned and logged). | **Related:** Salary Hold, Staff Exit | **Used by:** Branch Admin | **Used in:** P03

**Salary Hold** — A financial hold on a staff member's salary or documents. Two types: first-month salary held; degree in custody. Both must be resolved before exit. | **Related:** Staff Profile, Teacher Exit Gate, Salary Hold Exit Gate | **Used by:** Branch Admin | **Used in:** P03

**Leave Record** — A formal leave request for a staff member. Append-only. Approved leave automatically links to daily staff attendance entries and marks them On Leave with a leave reference. Corrections are appended as new records. | **Related:** Staff Profile, Staff Attendance | **Used by:** Branch Admin (records), Admin (manages) | **Used in:** Referenced in staff management

**Certification Tracking** — The monitoring of expiry-dated staff certifications. Expiry alert notification #11 fires to Branch Admin at the configured threshold.

| Document | Alert threshold |
|---|---|
| NTT Certificate | 60 days before expiry |
| Montessori Certification | 60 days before expiry |
| Police Verification | 60 days before computed expiry (issue + 3 years) |
| First Aid Certificate | 30 days before expiry |
| Annual Employment Agreement | 30 days before renewal date |

**Related:** Staff Profile | **Used by:** Branch Admin | **Used in:** Referenced in staff management

**NTT Certificate** — Nursery Teacher Training certificate. An expiry-tracked credential required for qualified early childhood educators. *(Indian context: the NTT certification is a recognised qualification for preschool teachers in India, required by many state education boards.)* | **Related:** Certification Tracking | **Used by:** Branch Admin | **Used in:** P02

**Visitor Log Entry** — A record of a non-staff visitor to school premises: name, purpose, entry time. Created by Coordinator. Viewed by Branch Admin and Admin. Serves as an operational security record. | **Related:** Coordinator | **Used by:** Coordinator (creates), Branch Admin, Admin (views) | **Used in:** Referenced in daily operations

---

## H — Communication

**WhatsApp Deeplink** — A pre-filled WhatsApp message sent by tapping a system-generated link. Manual tap-to-send in V1; automated dispatch is V2. The message is pre-filled with live data from the relevant module. Four template types: Absence notification, Fee reminder, Payment confirmation, Incident notification. Every send attempt is permanently logged with actor, student, template type, template version, and timestamp. | **Related:** WhatsApp Template, Communication Log | **Used by:** Admin, Branch Admin, Coordinator (absence), Teacher (absence), Accountant (fee types) | **Used in:** P26

**WhatsApp Template** — A pre-approved message template used to generate deeplink content. Two scopes: Global (Admin only) and Branch-scoped (Branch Admin if toggle enabled). Validated in real time on save. Cannot be deleted if it is the last active template of its type for a branch — only deactivated. Previous versions are retained in read-only version history. Fallback chain: branch-scoped → global → blocked. | **Related:** WhatsApp Deeplink, Template Management Toggle | **Used by:** Admin (global), Branch Admin (branch-scoped if toggle enabled) | **Used in:** P25, P26

**Template Management Toggle** — The only per-account permission variation in V1. A single toggle on Branch Admin accounts: "Allow this Branch Admin to create and edit WhatsApp templates." Enabled by default. When disabled by Admin, the Branch Admin can still use templates but cannot create or edit them. | **Related:** WhatsApp Template, Branch Admin | **Used by:** Admin (configures) | **Used in:** P02, P25

**School Inbox** — The two-way asynchronous messaging channel for parent-initiated non-fee messages to the school. Always linked to a specific enrolled child. Accessible by Admin, Branch Admin, and Coordinator — not Teacher or Accountant. Maximum 5 parent-initiated messages per student per day. Messages cannot be deleted by any role. CLOSED status is terminal. Retained 3 years. | **Related:** Fee Query Channel, Parent Portal, School Notice Board | **Used by:** Parent (initiates, replies), Branch Admin, Coordinator | **Used in:** P28

**School Notice Board** — A passive in-app reference channel for non-transactional branch-wide notices. No notification fires on posting — staff see an unread badge on next login; parents see an unread badge on next portal visit. Branch Admin and Admin post and archive; no other role can post. Archived notices are permanently retained; cannot be deleted. | **Related:** Announcement, School Inbox | **Used by:** Branch Admin, Admin (post), All roles (view) | **Used in:** P27

**Announcement** — A broadcast communication to a defined parent audience, delivered as an in-app notification. Audience levels: All parents, By programme, By class, Individual. Recipient count shown before send. Every announcement is permanently logged. | **Related:** School Notice Board, School Inbox | **Used by:** Branch Admin, Coordinator, Admin | **Used in:** P27

**System Notification** — An automated in-app or email notification triggered by a defined business event. 12 named notifications exist.

| # | Event | Recipient | Bypasses opt-out |
|---|---|---|---|
| 1 | First payment recorded | Parent | Yes — mandatory |
| 2 | Enrollment completed | Parent | Yes — mandatory |
| 3 | Student marked Absent | Parent | No |
| 4 | Bonafide issued | Parent | Yes — mandatory |
| 5 | Progress submission window opens | Teacher + Branch Admin | N/A — staff |
| 6 | Invoice overdue | Branch Admin + Accountant | N/A — staff |
| 7 | Incident report published | Parent | Yes — mandatory |
| 8 | Welfare concern flagged | Branch Admin | N/A — staff |
| 9 | Chronic absence threshold reached | Branch Admin | N/A — staff |
| 10 | New parent message in school inbox | Branch Admin, Coordinator | N/A — staff |
| 11 | Certification expiry alert | Branch Admin | N/A — staff |
| 12 | Progress card published | Parent | No |

A pickup list change notification also fires unconditionally (bypasses opt-out) but is not numbered 1–12. | **Related:** WhatsApp Deeplink, Parent Portal | **Used by:** System (generates), various roles (receive)

**Communication Log** — The permanent record of all staff-to-parent and parent-to-staff communications. Retained for 3 years. Accessible to Admin (all branches) and Branch Admin (own branch only). Accountant can view the fee communication log only. Coordinator and Teacher have no access. | **Related:** School Inbox, Fee Query Channel, WhatsApp Deeplink | **Used by:** Admin, Branch Admin | **Used in:** P30

---

## I — Governance and System

**Audit Log** — A permanent, immutable record of every action taken in the system. No role can edit or delete any entry. Every entry records: timestamp, actor (name and role), branch, module, action type, target entity, before state, after state, reason (where required). Accessible to Admin (all branches) and Branch Admin (own branch). Financial records retained 7 years; communication logs retained 3 years. | **Related:** Governance Log, Admin Override Confirmation | **Used by:** Admin, Branch Admin | **Used in:** All processes

**Governance Log** — A filtered view of the Audit Log showing only override-class actions. Visible to Admin only. Override-class actions include: attendance corrections beyond 30 days; Bonafide/TC with outstanding dues; capacity overrides; document waivers; discontinuation acknowledgments; Migration Mode entry and exit. | **Related:** Audit Log, Monthly Governance Digest | **Used by:** Admin | **Used in:** P08, P20, P21, P29

**Monthly Governance Digest** — An automated email sent to Admin on the 1st of each month summarising override actions, fee activity, and attendance metrics across all branches for the prior month. Generated from the existing Audit and Governance logs — no new data computed. Requires no manual action. | **Related:** Governance Log, Admin | **Used by:** Admin | **Used in:** P32

**Admin Override Confirmation** — The one-click confirmation prompt that appears when Admin performs an action normally owned by another role. Identifies the action and states it will be logged as an Admin action. Every confirmed override is recorded in the Audit Log. Ensures intent is explicit for cross-role actions. | **Related:** Admin, Audit Log, Governance Log | **Used by:** Admin | **Used in:** All override scenarios

**Migration Mode** — A named, Admin-controlled, per-branch system state that suppresses invoice generation, Razorpay payment links, and parent portal activation while the school prepares data for go-live. One-time per branch; cannot be re-entered after exit. During Migration Mode: Opening Balance window stays open; CoFee import, student profile creation, and all other operations run normally. Exit requires all 6 Migration Checklist items to be green or individually acknowledged. | **Related:** Migration Checklist, Opening Balance, Historical Attendance Marker | **Used by:** Admin (controls), Branch Admin (migration preparation) | **Used in:** P29

**Migration Checklist** — The six-item per-branch go-live readiness tracker active during Migration Mode. Items: student profiles, authorised pickup lists, opening balances, CoFee import, academic year setup, staff accounts. Admin cannot exit Migration Mode until every item is green or explicitly acknowledged with a logged reason. Complete checklist state is permanently captured in the Governance Log on exit. | **Related:** Migration Mode | **Used by:** Admin, Branch Admin | **Used in:** P29

**Branch Data Isolation** — The rule that every data request is validated against the requesting user's branch binding before any data is returned. A staff member bound to Branch A cannot access Branch B data by any means — not by URL manipulation or any other method. Enforced at the application layer. | **Related:** Branch Binding, Multi-Branch | **Used by:** All roles | **Used in:** All processes

**Multi-Branch** — The capability allowing a single staff member to be bound to more than one branch under the same role, managed via branch bindings. On login, the staff member chooses their working branch for the session. All actions in that session are scoped and logged to the selected branch. Not the same as Admin's global cross-branch view. | **Related:** Branch Binding, Admin | **Used by:** Admin (creates bindings), multi-branch staff | **Used in:** P02

**RBAC (Role-Based Access Control)** — The permission system governing what each role can see and do. Five built-in roles are fixed in V1 and cannot be renamed or removed. The only per-account variation is the Template Management Toggle on Branch Admin accounts. Custom roles are deferred to V2. | **Related:** Admin, Branch Admin, Coordinator, Teacher, Accountant | **Used by:** Admin (configures) | **Used in:** P02

**Parent Portal** — The web-based interface through which parents access their child's school activity. Access created on first payment recording — not at enrollment. Strictly child-scoped; each child appears on a separate tab. Cannot be broadened by any staff role. Access is revoked on Bonafide or Transfer Certificate issuance. Parent view: attendance history, class timetable, daily activity feed, published progress cards, document status, fee structure (not payment history in V1), notice board, authorised pickup list (read-only), published incident reports. | **Related:** School Inbox, Fee Query Channel, Student Profile | **Used by:** Parent | **Used in:** P04, P07, P09, P16

---

## J — Indian Regulatory Context

**Aadhaar** — India's 12-digit national biometric identity number. Stored on both student and staff profiles. Access-logged on every read. Never exported, emailed, or included in any notification — unconditionally for every role. *(Indian context: Aadhaar is issued by UIDAI and is the primary identity document for residents of India; mandatory for many school and employment processes.)* | **Related:** DPDP Act, Audit Log | **Used by:** Branch Admin, Admin (access only) | **Used in:** P02, P04

**DPDP Act (Digital Personal Data Protection Act)** — India's data protection law governing the collection, storage, and processing of personal digital data. Governs how Happy Feet handles sensitive data such as Aadhaar numbers, medical records, and parent contacts. Photo sharing is deferred to V2 specifically to allow time for DPDP compliance review. *(Indian context: enacted 2023; still being operationalised through implementing rules as of 2025.)* | **Related:** Aadhaar, Medical Records, Audit Log | **Used in:** All data handling

**APAAR / PEN (Academic Bank of Credits / Permanent Education Number)** — A government-issued unique academic identifier for students. APAAR registration requires explicit voluntary parental consent. Noted as a regulatory obligation but submission workflows are explicitly out of scope for V1. *(Indian context: part of India's NEP 2020 implementation; APAAR links a student's academic records across institutions throughout their education lifecycle.)* | **Related:** UDISE+, NEP 2020 | **Used in:** Out of scope V1

**UDISE+ (Unified District Information System for Education Plus)** — India's national school registry. Annual reporting is a regulatory obligation for the school. Happy Feet's CSV exports can support manual UDISE+ data preparation; automated submission is out of scope for V1. *(Indian context: managed by the Ministry of Education; every recognised school in India must submit annual data.)* | **Related:** APAAR, CSV Exports | **Used in:** Out of scope V1

**NEP 2020 (National Education Policy 2020)** — India's national education policy that mandated early childhood care and education (ECCE) as a formal part of the education system and introduced APAAR. Happy Feet's programme model (Play Group through PP2) aligns with the ECCE framework under NEP 2020. *(Indian context: NEP 2020 is the most significant education policy reform in India since 1986.)* | **Related:** APAAR, Programme | **Used in:** Regulatory context

**POCSO (Protection of Children from Sexual Offences Act)** — India's legislation protecting children from abuse. Governs how incident reports and welfare concern records are handled. Supports the domain's strict rules around child data access and the absolute confidentiality of internal incident report notes. *(Indian context: POCSO 2012 is the primary legislation protecting children under 18 in India; mandatory reporting obligations exist for educational institutions.)* | **Related:** Incident Report, Welfare Concern Record | **Used in:** Safety rules context

---

## Summary Table — Term Index

| Term | Category | Key Rule / Constraint |
|---|---|---|
| Admin | Role | Cross-branch authority; sole control over Migration Mode and governance log |
| Branch Admin | Role | Full authority within one branch; cannot provision another Branch Admin |
| Coordinator | Role | Cannot complete enrollment; no fee data access |
| Teacher | Role | Class slot scoped; 7-day attendance correction limit |
| Accountant | Role | Cannot enter opening balances or record expenses |
| Parent | Role | Child-scoped; read-only; access created on first payment only |
| Admission ID | Lifecycle | Permanent; immutable; cannot be changed |
| Fee Snapshot | Finance | Full data copy at enrollment; does not auto-update post-enrollment |
| Enrollment Conditions | Admissions | All four must be met independently; pickup list entry mandatory |
| Document Waiver | Admissions | Per-document; individual reason required; bulk waiving not permitted |
| Opening Balance | Finance | Window closes permanently on first invoice; no correction path after |
| Billing Unit | Attendance | Day Care only; ≥4 hrs = 1.0; <4 hrs = 0.5; QR-derived |
| Authorised Pickup List | Safety | Absolute gate; deactivating last entry blocked; mandatory parent notification on any change |
| Welfare Concern Record | Safety | Permanent; immutable; 3 school-day review obligation; never visible to parents |
| Incident Report | Safety | Append-only; internal notes never shown to parents; publish decision logged |
| Progress Card | Academic | Teacher attribution permanent at submission; snapshot locked at publication |
| Attendance Correction | Attendance | Calendar days; Teacher ≤7 days; Branch Admin ≤30 days; Admin unlimited |
| Migration Mode | Governance | Per-branch; one-time; cannot re-enter; 6-item checklist required |
| Audit Log | Governance | Every action; immutable; no role can edit or delete |
| Governance Log | Governance | Override-class actions only; Admin only |
| COFEE_IMPORT | Finance | Created by CoFee import; not blocked during Migration Mode |
| HAPPY_FEET_NATIVE | Finance | Blocked during Migration Mode |
| RAZORPAY_COFEE_CONFLICT | Finance | Branch Admin approval required to void Razorpay record |
| Aadhaar | Compliance | Access-logged every read; never exported or emailed |
| DPDP Act | Compliance | Governs all personal data handling; photo sharing deferred to V2 |
| APAAR / PEN | Compliance | Out of scope V1; voluntary parental consent required |
| UDISE+ | Compliance | Out of scope V1; manual export support only |
| Bonafide Certificate | Lifecycle | Irreversible; mutually exclusive with TC; Admin required if dues exist |
| Transfer Certificate | Lifecycle | Irreversible; mutually exclusive with Bonafide; Admin required if dues exist |
| Year-End Rollover | Lifecycle | Always manual; per-invoice acknowledgment required; never automatic |

---

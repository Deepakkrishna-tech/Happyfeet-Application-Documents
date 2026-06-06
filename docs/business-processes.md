<!-- ABOUT
File: BUSINESS_PROCESSES.md
Purpose: Documents all 32 operational processes with triggers, Primary Workflow s, exception paths, escalation paths, and outcomes to establish how Happy Feet's work actually flows end-to-end.
Scope: 32 processes across 10 domains (A: Setup, B: Admissions, C: Daily Operations, D: Financial, E: Progress, F: Student Lifecycle, G: Child Safety, H: Communication, I: Go-Live, J: Reporting); process cross-reference table; irreversible processes table; 5 key design principles; excludes capability definitions, role authority model, and data entities.
Dependencies: DOMAIN_OVERVIEW.md, BUSINESS_CAPABILITY_MAP.md, ORGANIZATION_MODEL.md
Stage: Stage 4 — Business Process Intelligence
-->

# Business Processes — Happy Feet School ERP

> **About This Document:** This document describes all 32 operational processes in the Happy Feet ERP, from initial school setup and student admissions through daily attendance, fee collection, safety escalations, and end-of-year reporting. Each process entry includes its trigger, the normal (happy) path, exception and escalation paths, and the expected outcome. Read this if you are building, reviewing, or testing any workflow in the system; it is the primary reference for how work actually flows end-to-end.

**Stage:** 4 — Business Process Intelligence

---

Processes marked **[IRREVERSIBLE]** cannot be undone by any role once completed.

---

## Domain A — System Setup & Configuration

### P01 — Academic Year Setup

**Trigger:** The current academic year is approaching its end, or it is initial system setup for a new branch.
**Actors:** Branch Admin (primary), Admin.

**Primary Workflow (9 steps, must complete in sequence):**
1. Branch Admin creates the academic year with start and end dates.
2. Branch Admin sets term dates (Term 1, 2, 3); for each term, sets the progress card submission window open and close dates.
3. Branch Admin configures programme definitions — billing model, attendance model, milestone set, and progress card inclusion per programme. Default set (Play Group, Nursery, PP1, PP2, Day Care) is pre-loaded.
4. Branch Admin creates classes — name, capacity, and assigns teacher slots (Primary mandatory; Assistant and Temporary optional with configurable end dates).
5. Branch Admin configures transport zones — distance bands, trip types, and fixed monthly amounts. Skipped if transport is not offered.
6. Branch Admin enters the school calendar for the full year, including all holidays and events.
7. Branch Admin configures the weekly timetable per class.
8. Branch Admin defines milestones per programme (full or simplified set).
9. Branch Admin specifies required document types per programme.

**Alternative Workflows:**
- *New branch mid-year:* Admin creates the branch and completes the 9-step setup. Branch Admin takes over daily operations once setup is confirmed complete.
- *Upcoming year configured while current year is active:* System allows parallel configuration. Upcoming year activates automatically at midnight on its configured start date. No manual trigger required.

**Governance & Authorization Limits:**
- Admin can manually activate or delay academic year activation at any time. Every manual override is permanently logged in the audit log.

**Outcome:** All downstream modules (admissions, attendance, fee management, progress & assessments) have the operational framework they need. No admissions, attendance marking, or invoice generation can begin until all 9 steps are confirmed complete.

---

### P02 — Staff Onboarding and Role Assignment

**Trigger:** A new staff member joins the school, or an existing staff member needs to be bound to an additional branch.
**Actors:** Branch Admin (provisioning roles below Branch Admin), Admin (provisioning Branch Admin; managing multi-branch bindings).

**Primary Workflow:**
1. Branch Admin creates the staff profile — name, designation, employment type, starting salary.
2. Mandatory documents attached: Aadhaar, education certificates; experience certificate if applicable; employment agreement with hold type noted.
3. Branch Admin sends a 7-day portal invite (resendable without creating duplicates). Skipped if the staff member does not require system access.
4. Staff member activates account on first login.
5. Role and class assignments take effect at the staff member's next login after assignment — not during the current session.

**Alternative Workflows:**
- *Multi-branch binding:* Admin navigates to the staff account settings and adds a branch binding entry. On next login, the staff member sees a branch selector and chooses their working branch for the session.
- *Template management toggle:* For Branch Admin accounts, Admin can disable the "Allow template creation and editing" toggle. Branch Admin can still use templates but cannot create or edit them.
- *Invite expires:* Branch Admin resends the invite; no duplicate account is created.

**Governance & Authorization Limits:**
- Creating a Branch Admin account requires Admin — Branch Admin cannot provision another Branch Admin.

**Outcome:** Staff member holds one role, is bound to one or more branches, and can log in. Every provisioning action is permanently logged.

---

### P03 — Staff Exit

**Trigger:** A staff member leaves the school.
**Actors:** Branch Admin (initiates for roles below Branch Admin), Admin (exits Branch Admin accounts).

**Primary workflow:**
1. Branch Admin initiates exit from the staff profile.
2. **Gate 1 (Teachers only):** The Primary slot on every class the Teacher is assigned to must be reassigned to another Teacher before exit can proceed. Branch Admin confirms replacement assignments for each class.
3. **Gate 2 (all staff):** All salary holds must be formally resolved before exit can proceed — Hold type 1: first-month salary held (must be formally released); Hold type 2: degree in custody (must be returned and logged).
4. Both gates cleared: Branch Admin confirms exit.
5. Profile becomes read-only. System access is revoked automatically.

**Exception paths:**
- *Staff member has no class assignments and no salary holds:* No gates apply; exit proceeds immediately on confirmation.
- *Salary hold resolution dispute:* Hold resolution is a Branch Admin decision; no system-automated resolution path exists for disputed holds.

**Outcome:** Staff profile becomes read-only and access is revoked. Every class previously assigned to that Teacher has a confirmed Primary slot replacement.

---

## Domain B — Admissions and Enrollment

### P04 — Admission Application and Enrollment

**Trigger:** A parent submits the public admission form (no login required), or Branch Admin creates a walk-in application directly.
**Actors:** Branch Admin or Coordinator (application review and progression), Branch Admin (enrollment completion), Accountant (first payment recording, which triggers parent portal activation).

**Primary Workflow :**
1. Application created with child details, programme preference, family contacts, and medical history. Status: **Applied**.
2. Branch Admin assigns programme and class. A student profile record is initiated in student records at this point. Status: **Approved**.
3. Branch Admin checks required documents against the programme's required list. All documents present and verified.
4. Branch Admin confirms all four enrollment conditions independently:
   - (i) Programme assigned
   - (ii) All required documents cleared or each individually waived with a recorded reason
   - (iii) Class capacity available (or override recorded with reason)
   - (iv) At least one active authorised pickup list entry recorded for this student
5. Enrollment completes. Fee structure snapshotted as a full data copy. Permanent Admission ID assigned. Status: **Enrolled**.
6. If the parent phone number matches an existing student profile, Branch Admin is notified of a likely sibling relationship and takes one of three actions: Confirm as siblings, Confirm as unrelated, or Flag for follow-up.
7. Fee communicated to the parent outside the system. Parent pays via UPI, cash, or CoFee.
8. Accountant records the first payment.
9. System sends the parent a portal invite (email). Status: **Portal invited**.
10. Parent activates the portal account on first login. Portal is scoped to their enrolled child(ren).

**Exception paths:**
- *Missing documents:* Branch Admin waives each missing document individually with a recorded reason. Blanket waiving is not permitted — every gap requires a reason.
- *Class full — override:* Branch Admin records an override reason and proceeds with enrollment.
- *Class full — waitlist:* Branch Admin adds the approved applicant to the per-class waitlist instead of overriding. Branch Admin contacts the parent when a slot opens (no automated notification).
- *Application declined:* Branch Admin or Admin records a reason. Status: **Declined**. The application is permanently retained and can be reopened by Branch Admin. The term "Declined" is used throughout — never "Rejected."
- *Admissions window closed:* Branch Admin can still enroll walk-ins directly — no window restriction applies to walk-in entry.
- *No active pickup list entry:* Enrollment is blocked until Branch Admin records at least one active pickup list entry. Guardian is not automatically on the pickup list — must be explicitly added.

**Escalation path:**
- Coordinator can advance an application (track documents, follow up) but cannot complete enrollment independently — Branch Admin must confirm enrollment.

**Outcome:** Child has a permanent Admission ID, an immutable fee structure snapshot, at least one active authorised pickup entry, and a parent portal account that activates on first payment.

---

### P05 — Re-admission After Discontinuation or Programme Completion

**Trigger:** A previously discontinued or programme-completed student returns to the school.
**Actors:** Branch Admin (profile reactivation, programme assignment), Accountant (payment recording).

**Primary workflow:**
1. Branch Admin locates the existing student profile by Admission ID or name. No new application form is created.
2. Branch Admin reopens the profile. Same Admission ID retained. Full prior history — including welfare concern records, incident reports, and PTM notes — is intact.
3. Branch Admin assigns a programme and class for the new period.
4. Outstanding dues from before discontinuation remain on the record — they are not cleared by re-admission.
5. Accountant records the re-admission payment.
6. Billing, attendance, and parent portal access resume.

**Exception paths:**
- *Outstanding dues from previous enrollment:* These remain outstanding. Branch Admin acknowledges they exist. No system-automatic write-off.

**Outcome:** Returning student re-enters the system with full continuity of record, same Admission ID, and prior financial obligations intact.

---

### P06 — Waitlist Management

**Trigger:** A class reaches capacity and an approved applicant cannot be directly enrolled.
**Actors:** Branch Admin.

**Primary workflow:**
1. Branch Admin adds the approved applicant to the per-class waitlist. Position is recorded by date added.
2. A slot opens in the class — via student discontinuation, class transfer, or a Branch Admin capacity increase.
3. Branch Admin sees the next student on the waitlist and can proceed with enrollment.
4. Branch Admin contacts the parent directly (outside the system — no automated notification).
5. Enrollment proceeds per P04 from step 4 onward.

**Exception paths:**
- *Waitlisted student is no longer interested:* Branch Admin moves to the next applicant on the waitlist. The declined waitlist position is permanently logged.
- *Multiple slots open simultaneously:* Branch Admin works through the waitlist in order, one student at a time.

**Outcome:** No approved applicant is silently lost when a class fills; waitlist position is permanently logged.

---

## Domain C — Daily Operations

### P07A — Gate Pickup Verification

**Trigger:** A student is being collected from school premises at departure.
**Actors:** Coordinator (primary gate role), Branch Admin (oversight and escalation authority).

**Primary Workflow :**
1. A person arrives at the gate to collect a child.
2. Coordinator looks up the student by name or Admission ID in the system.
3. System displays the student's photo and active Authorised Pickup List.
4. Coordinator verifies that the presenting person is on the active list.
5. Coordinator confirms → CHECK_OUT event logged with timestamp and pickup person ID.

**Exception paths:**
- *Person NOT on the authorised pickup list:* System blocks the action and displays: "This person is not on the authorised pickup list. Contact Branch Admin before releasing the child." Coordinator contacts Branch Admin immediately. Branch Admin decides whether to proceed (with a governance log entry) or refuse release.

**Outcome:** Every student departure is verified against the authorised pickup list. No child is released to an unregistered person without a Branch Admin decision and a permanent governance log entry.

---

### P07B — Day Care Check-In / Check-Out Recording

**Trigger:** A Day Care student arrives at or departs from school.
**Actors:** Authorised staff (records timestamps).

**Primary Workflow  — Check-In:**
1. Day Care student arrives at school.
2. Authorised staff records the student's arrival time in the system.
3. System stores the CHECK_IN timestamp as an operational record linked to the student and date.

**Primary Workflow  — Check-Out:**
1. Day Care student departs from school (after pickup verification per P07A, where applicable).
2. Authorised staff records the student's departure time in the system.
3. System stores the CHECK_OUT timestamp as an operational record.
4. Staff-recorded check-in/check-out timestamps support operational tracking and billing calculations where required by the student's assigned Day Care Billing Plan.

**Exception paths:**
- *Check-out not recorded by end of day:* Branch Admin is alerted if any Day Care student has a CHECK_IN with no corresponding CHECK_OUT. Authorised staff or Branch Admin can record the check-out retrospectively with a logged reason.
- *Check-in not recorded:* Branch Admin is alerted. Retrospective entry is permitted with a logged reason.

**Outcome:** Daily check-in and check-out timestamps are recorded as operational records for Day Care students, available for billing calculations and operational oversight.

---

### P08 — Daily Attendance Marking and Correction

**Trigger:** Each school day, as the marking window opens (default 10:00 AM–10:00 PM).
**Actors:** Teacher (own class — primary marker), Assigned Secondary Teacher (own class — authorised secondary), Coordinator (any class — authorised secondary), Branch Admin (oversight, corrections), Admin (unlimited corrections).

**Primary Workflow :**
1. Teacher opens the class attendance screen and marks each student: Present, Absent, Late, or Half-Day.
2. Absent mark → immediate in-app notification fires to the parent.
3. Attendance record created and locked for that student and date.

**Exception paths:**
- *Teacher does not mark within the window:* In-app reminder notification fires to the responsible Teacher. If no attendance record is created after the reminder, escalation notification fires to Branch Admin. No attendance record exists for the student until Teacher, authorised secondary staff, or Branch Admin creates one. Branch Admin can bulk-mark the entire class as Present with a logged reason.
- *Attendance on a school holiday:* Blocked for all roles. No override exists.
- *Late arrival:* Teacher marks the student as Late.
- *Half-day:* Teacher marks the student as Half-Day.

**Correction paths (by role and time window):**

| Window | Who can correct | Reason required |
|---|---|---|
| Same calendar day | Teacher | No |
| Day 2 – Day 7 | Teacher | Yes — one sentence minimum |
| Day 8 – Day 30 | Branch Admin | Yes — logged |
| Beyond Day 30 | Admin only | Yes — logged; governance log entry |

All limits are in calendar days, not working days. Every correction records: original status, new status, actor, timestamp, and reason.

**Escalation path:**
- *Parent raises an attendance dispute:* Parent sends a message via school inbox. Branch Admin navigates to M8 for the relevant date (UI shortcut available in school inbox: "Open this student's attendance record"). Branch Admin corrects if within the 30-day window. Admin corrects if beyond 30 days.
- *Correction needed beyond 7 days:* Teacher cannot proceed; Branch Admin must make the correction.
- *Correction needed beyond 30 days:* Branch Admin cannot proceed; Admin must make the correction. Governance log entry is created.

**Outcome:** Every school day has a complete attendance record. Absences notify parents immediately. Corrections are time-bounded and attributed, creating an accountability chain.

---

### P09 — Daily Activity Logging

**Trigger:** During school hours, for each school day. Teacher completes activity entries for enrolled students in their class.
**Actors:** Teacher (entry), Parent (read-only feed on portal), Branch Admin (oversight).

**Primary Workflow :**
1. Teacher opens the attendance screen for their class. The Daily Care Logentry panel appears on the same screen — no separate navigation required.
2. Teacher selects a student and taps "Add activity."
3. Teacher selects activity type: Meal, Nap, Toilet, Activity, Health Check, or General Note.
4. Teacher fills in type-specific fields and saves. Entry is written to the student's daily log for that date.
5. Teacher can use batch entry to log the same activity for multiple students simultaneously. Individual student entries can be adjusted after batch entry.
6. At 4:00 PM (configurable), system sends an end-of-day summary notification to parents of students who had entries logged that day. Notification includes a direct link to the school inbox thread.
7. Parent opens the portal → sees a chronological daily activity feed for their child, covering the current day and the past 30 days. Filterable by activity type and date.

**Exception paths:**
- *Meal entry for student with active allergy alerts:* System surfaces the allergy warning inline and requires explicit confirmation: "Confirm allergy protocol was followed." Teacher must confirm before saving. Confirmation logged permanently with actor and timestamp — creating an allergy compliance audit trail.
- *Entry made in error:* Daily Care Logentries are immutable after saving. Corrections are appended as new entries referencing the original. The original entry remains.
- *Toilet entry:* Parents see "Toilet visit logged" — frequency only. No detail shown to parents under any circumstances.

**Outcome:** Every child has a permanent daily record replacing informal WhatsApp. Parents have a real-time, archived feed. Allergy protocol compliance is documented and auditable.

---

## Domain D — Financial Operations

### P10 — Fee Structure Setup

**Trigger:** Academic year setup begins (Step 3 and 5 of the 9-step setup), or transport opt-in changes during the year.
**Actors:** Branch Admin.

**Primary Workflow :**
1. Branch Admin configures base fees per programme: billing model, billing schedule, and per-term or per-period amounts.
2. Branch Admin configures student categories (e.g. Standard, Staff Child, Subsidised) in M7 settings. Each enrolled student can be assigned a category at enrollment.
3. Branch Admin configures transport zones: distance bands, trip types, and fixed monthly amounts per zone. Skipped if transport is not offered.
4. Fee configuration is communicated to CoFee separately for invoice generation.
5. Transport opt-in per student: Branch Admin selects the applicable transport zone for each student who uses transport. The fixed monthly amount becomes the student's transport billing line item.

**Exception paths:**
- *Transport activated or deactivated mid-year:* Branch Admin updates the student's transport configuration at any time. Changes take effect from the next billing period. Change is logged.
- *Fee structure change after enrollment:* Subsequent fee changes do not automatically update existing fee snapshots. An explicit update to a student's fee snapshot is required if terms change.

**Outcome:** All enrolled students have a confirmed, snapshotted fee structure. Transport billing is determined by zone assignment, not manual calculation.

---

### P11 — CoFee XLSX Import

**Trigger:** CoFee report is available for the billing period (typically monthly or after a payment collection cycle). Accountant initiates the import.
**Actors:** Accountant (upload and exception resolution), Branch Admin (conflict resolution with approval authority), Admin (oversight).

**Primary Workflow :**
1. Accountant downloads the XLSX report from CoFee.
2. Accountant uploads the XLSX to Happy Feet (M7).
3. System applies the admission number normalisation rule (if configured) to align CoFee format with Happy Feet format.
4. Each row is matched against the student registry by Admission Number (primary) or Phone Number (secondary).
5. **Case A:** CoFee row matches a prior COFEE_IMPORT invoice → system updates amount_paid_to_date. Not an exception.
6. **Case D:** No matching invoice found → system creates a new COFEE_IMPORT invoice and payment record. Normal import flow.
7. Import timestamp updated on the financial dashboard, visible to Accountant, Branch Admin, and Admin.

**Exception paths:**
- *Unmatched row (no student found):* Row routed to the import exceptions list. Accountant resolves via one of three options: (i) link the row to the correct student manually, (ii) mark as not applicable, (iii) flag for 7-day follow-up reminder. Every row has a traceable outcome — no row is silently dropped.
- *Case B — Razorpay/CoFee conflict:* CoFee row matches a HAPPY_FEET_NATIVE invoice AND the transaction ID matches a Razorpay payment already recorded → RAZORPAY_COFEE_CONFLICT exception. Accountant reviews. Voiding the Razorpay payment record requires Branch Admin approval. All steps permanently logged.
- *Case C — CoFee row matches a Happy Feet native invoice, no Razorpay duplicate:* Import as a new payment against the existing invoice. Not an exception. Logged as "Payment imported from CoFee against Happy Feet invoice [invoice_number]."
- *Soft duplicate flag:* Accountant can override a SOFT_DUPLICATE flag without Branch Admin approval; must enter a reason (one sentence minimum). Override permanently logged. Branch Admin receives an informational in-app notification.
- *Admission number format mismatch:* Requires one-time normalisation configuration setup (see P15 — Opening Balance Entry prerequisites). Without this, every row will go to the exceptions queue.

**Escalation path:**
- RAZORPAY_COFEE_CONFLICT requires Branch Admin approval for any Razorpay payment voiding. Accountant cannot void unilaterally.

**Outcome:** Every CoFee row is matched, skipped as a duplicate, or manually resolved. The financial dashboard reflects the current state as of the import timestamp.

---

### P12 — Ad-Hoc Invoice Creation and Razorpay Payment Link

**Trigger:** School needs to collect a fee that falls outside the regular CoFee billing cycle (event fee, material fee, late charge, etc.).
**Actors:** Accountant, Branch Admin, or Admin (invoice creation and payment link generation).

**Prerequisite:** Branch is not in Migration Mode. Both actions are blocked during Migration Mode.

**Primary Workflow :**
1. Accountant (or Branch Admin, or Admin) navigates to the student's fee record → Create Invoice.
2. Enters fee head, amount, due date, and optional description. A HAPPY_FEET_NATIVE invoice is created with an auto-generated invoice number.
3. Accountant generates a Razorpay payment link from the invoice. A Razorpay checkout URL is created and logged.
4. Accountant shares the payment link with the parent (outside the system — typically via WhatsApp or directly).
5. Parent pays via the Razorpay checkout.
6. Razorpay confirms payment → Happy Feet automatically writes a payment record against the invoice. No manual step required.
7. Every link generation is logged with actor, timestamp, and student.

**Exception paths:**
- *Parent does not pay via the link; pays via CoFee instead:* CoFee import will encounter a Case B or Case C match on the next import cycle. Accountant resolves per P11.
- *Payment link expires before use:* Accountant generates a new link. Original link generation remains logged.

**Outcome:** School can collect ad-hoc fees with a structured, automatically reconciled payment record — without waiting for the next CoFee billing cycle.

---

### P13 — Day Care Billing Summary

**Trigger:** End of the Day Care billing period (typically monthly). Accountant needs to provide CoFee with the billing data to generate Day Care invoices.
**Actors:** Accountant.

**Primary Workflow :**
1. Staff-recorded check-in/check-out timestamps and assigned Day Care Billing Plans have been maintained throughout the period (see P07B). Billing is computed according to each student's assigned Day Care Billing Plan (Monthly, Weekly, Day-wise, Hour-wise, or Term-wise).
2. Accountant runs the Day Care billing summary export from M7.
3. Export shows per-student billing amounts for the period, derived from assigned plans and operational timestamp records.
4. Accountant takes the export to CoFee to generate the Day Care invoices for that period.

**Exception paths:**
- *Check-out not recorded:* Students with a CHECK_IN but no CHECK_OUT are flagged in the billing summary. Branch Admin is alerted at end of day and can record the check-out retrospectively (see P07B). Unresolved entries appear as incomplete records in the summary.
- *Check-in not recorded:* Same handling — flagged for Branch Admin review and retrospective entry per P07B.

**Outcome:** Accountant has a system-derived Day Care billing summary derived from gate timestamps, not manual estimates.

---

### P14 — Fee Query Resolution

**Trigger:** A parent has a question about a fee or invoice and submits it via the parent portal Fee Query channel.
**Actors:** Parent (initiates), Accountant (designated first responder), Branch Admin and Admin (can also view and respond).

**Primary Workflow :**
1. Parent navigates to parent portal → Communication → "Fee query."
2. Parent types their question and submits. Query is automatically tagged with the enrolled student's record.
3. Fee query routes to Accountant's fee communication log in M7 — distinct from the general school inbox.
4. Accountant receives an in-app notification and views the fee query in M7.
5. Accountant reviews and replies. Reply delivered as an in-app notification to the parent.
6. Parent can view the reply and submit a follow-up in the same thread.
7. Thread continues until Accountant or Branch Admin marks it **CLOSED**. CLOSED is terminal.
8. All messages and replies permanently logged in the communication log.

**Exception paths:**
- *Fee query requires Branch Admin decision (e.g. fee waiver, exceptional circumstance):* Accountant can view the thread and flag it internally; Branch Admin can access and respond. Thread stays in the fee query channel — it does not migrate to the school inbox.
- *Parent sends fee question via school inbox instead of Fee Query channel:* School inbox is handled by Branch Admin and Coordinator, who do not have fee data access. They direct the parent to use the Fee Query channel.

**Escalation path:**
- Queries that require financial decisions (waivers, adjustments) are escalated to Branch Admin within the same fee query channel.

**Outcome:** Fee questions are handled by the right person through a logged channel, separate from general communication. Every exchange is permanently retained.

---

### P15 — Opening Balance Entry

**Trigger:** A student is being migrated into Happy Feet and has prior financial history. Must occur before the first invoice is generated for that student.
**Actors:** Branch Admin, Admin.

**Prerequisite:** The branch is in Migration Mode (opening balance window cannot close while in Migration Mode) OR no invoice has yet been generated for this student.

**Primary Workflow :**
1. Branch Admin navigates to the student's fee record.
2. Branch Admin enters the opening balance — the historical outstanding amount from CoFee or paper records.
3. Opening balance is saved and immediately reflected in the financial dashboard and outstanding balance calculations.
4. Once the first invoice is generated for this student (post-Migration Mode), the opening balance window closes permanently and cannot be re-entered.

**Exception paths:**
- *Opening balance not entered before first invoice is generated:* Window closes permanently. No correction path exists. This is a Migration Mode risk and a key reason the migration checklist exists.

**Outcome:** Every migrated student has an accurate financial starting point feeding outstanding balance calculations from day one.

---

## Domain E — Academic Progress

### P16 — Progress Card Submission and Publication

**Trigger:** Branch Admin opens the submission window on the configured open date (set during Step 2 of academic year setup).
**Actors:** Teacher (draft and submit), Delegate Reviewer (review and note — if configured), Branch Admin (review, publish, or return), Parent (view published card only).

**Primary Workflow :**
1. System notifies Teachers and Branch Admin in-app when the window opens.
2. Teacher opens the progress card for a student in their class. Teacher drafts milestone ratings and observation text. Drafts are saved freely and are invisible to Branch Admin and parents until submitted.
3. Teacher submits the card. Card attribution is permanent at submission — the system records who drafted and who submitted. Card is now visible to Branch Admin (and Delegate Reviewer, if configured).
4. If a Delegate Reviewer is configured for the class: delegate reviews the submitted card and adds a review note (visible to Branch Admin only). Delegate cannot publish.
5. Branch Admin reviews the submitted card (and the delegate's note, if present).
6. Branch Admin publishes the card:
   - Card appears on the parent portal.
   - Attendance snapshot is locked from live M8 data at the exact moment of publication. Snapshot includes the Historical Attendance Marker contribution (Year 1 accuracy). Snapshot is immutable thereafter.
   - Parent receives in-app notification (#12 — opt-outable).
   - Branch Admin can generate a PDF from the published card.
7. Card workflow ends in **PUBLISHED** state.

**Exception paths:**
- *Teacher has not submitted by the time the window closes:* Branch Admin receives an in-app alert. Branch Admin contacts Teacher. If the Teacher cannot submit, Branch Admin can submit on the Teacher's behalf (logged as Admin override with one-click confirmation).
- *Branch Admin returns the card:* Branch Admin must explicitly reopen the card for the Teacher to re-edit. Teacher receives notification. Teacher re-edits and resubmits. Branch Admin reviews again. This loop continues until the card is published.
- *Branch Admin cannot edit card content:* This is an explicit system constraint. Branch Admin can only publish or return — they cannot alter milestone ratings or observation text.

**Outcome:** Every enrolled student (except Event-type programmes) has a published, PDF-exportable progress card per term. The attendance snapshot is locked at publication and cannot be changed.

---

### P17 — Progress Card Revision

**Trigger:** A published progress card contains an error that requires correction after publication.
**Actors:** Branch Admin (initiates revision, publishes revised card), Teacher (re-drafts and resubmits revised card).

**Primary Workflow :**
1. Branch Admin opens a revision on the published card. The original published card is archived — it is permanently retained but no longer the live version.
2. Teacher receives notification that the card has been reopened. Teacher re-drafts the relevant sections and resubmits.
3. Branch Admin reviews and publishes the revised card.
4. Parents see the last published version only until the revision is published.
5. On publication of the revised card: a new attendance snapshot is locked. The archived original is retained as a permanent record.

**Outcome:** Errors can be corrected without losing the original; revision history is permanently retained.

---

## Domain F — Student Lifecycle

### P18 — Student Discontinuation

**Trigger:** A student leaves the school mid-year or does not return.
**Actors:** Branch Admin.

**Primary Workflow :** **[IRREVERSIBLE]**
1. Branch Admin navigates to the student profile → Student Lifecycle → "Discontinue."
2. System lists all outstanding invoices for this student.
3. Branch Admin acknowledges each outstanding invoice individually with a recorded reason. Bulk acknowledgment is not permitted — each invoice requires a separate reason.
4. On confirmation: all three lifecycle switches (billing, attendance screen, parent portal access) are disabled simultaneously in one atomic operation.
5. Student status: **Discontinued**. Profile and all history are permanently retained.

**Exception paths:**
- *No outstanding invoices:* Steps 2–3 are skipped. Branch Admin proceeds directly to confirmation.
- *Outstanding dues at discontinuation:* Dues remain on the record. Discontinuation does not write off financial obligations. This is intentional — the record of the debt persists.

**Escalation path:**
- None. Discontinuation is within Branch Admin authority regardless of outstanding dues.

**Outcome:** Billing stops, attendance screen is hidden, and parent portal access is revoked in one confirmed irreversible action. Outstanding financial obligations are preserved on the record.

---

### P19 — Year-End Rollover

**Trigger:** The current academic year ends. This process is always manually triggered — it never runs automatically.
**Actors:** Branch Admin, Teachers (review flag only).

**Primary Workflow :**
1. Branch Admin initiates rollover from the student lifecycle screen.
2. System generates a preview of all Active students with proposed class assignments for the new academic year.
3. Teachers may flag individual students as **Needs Review** — this is an advisory note only. It does not block rollover.
4. Branch Admin reviews the preview, adjusts class assignments as needed.
5. If any Active student has outstanding dues, Branch Admin acknowledges each outstanding invoice individually with a recorded reason. Bulk acknowledgment is not permitted.
6. Branch Admin confirms rollover:
   - All Active students → **Completed Programme**.
   - All three lifecycle switches (billing, attendance, parent portal access) reset to ON for all rolling-over students.
7. Rollover preview flags any student whose switches were not all ON before confirmation — Branch Admin must review these explicitly.

**Exception paths:**
- *Student has all switches OFF at rollover:* Flagged in the rollover preview. Branch Admin reviews and decides whether to roll over or discontinue.
- *Some students are already Discontinued or Completed:* These students are not included in the rollover. Their status does not change.

**Outcome:** All Active students have a confirmed class assignment for the new year. Every outstanding invoice has been individually acknowledged before the year closes.

---

### P20 — Bonafide Certificate Issuance

**Trigger:** A student completes a programme (typically PP2) and requires a Bonafide certificate to leave.
**Actors:** Branch Admin (no outstanding dues), Admin (outstanding dues exist).

**Primary Workflow  (no outstanding dues): [IRREVERSIBLE]**
1. Branch Admin navigates to the student profile → Student Lifecycle → "Issue Bonafide Certificate."
2. System displays outstanding dues as a live snapshot.
3. No dues confirmed → Branch Admin enters leaving date and optional conduct remark.
4. Branch Admin confirms issuance.
5. Parent portal access revoked. Student status: **Bonafide Issued**.
6. School produces the physical Bonafide certificate externally. System records the issuance event permanently.

**Exception path (outstanding dues exist): [IRREVERSIBLE]**
1. System detects outstanding dues → Branch Admin cannot proceed.
2. Admin must proceed. Admin reviews the outstanding dues snapshot.
3. Admin enters leaving date, conduct remark, and a mandatory recorded reason for issuing with outstanding dues.
4. Admin confirms issuance. **Governance log entry created.**
5. Outstanding dues snapshot permanently attached to the Bonafide record.
6. Parent portal access revoked. Student status: **Bonafide Issued**.

**Escalation path:**
- Outstanding dues → escalates from Branch Admin to Admin. Branch Admin cannot override this gate.

**Mutual exclusivity:** Bonafide and Transfer Certificate are mutually exclusive for the same student. A TC cannot be issued if a Bonafide has already been issued, and vice versa.

**Outcome:** Student has formally completed their programme with a permanent record of the issuance, including any outstanding obligations at departure.

---

### P21 — Transfer Certificate Issuance

**Trigger:** A student is transferring to another school mid-year or at year-end.
**Actors:** Branch Admin (no outstanding dues), Admin (outstanding dues exist).

**Primary Workflow  (no outstanding dues): [IRREVERSIBLE]**
1. Branch Admin navigates to student profile → Student Lifecycle → "Issue Transfer Certificate."
2. System displays outstanding dues as a live snapshot.
3. No dues confirmed → Branch Admin enters: leaving date, destination school (optional), conduct remark (optional), and reason for transfer.
4. System prompts: "Issue Transfer Certificate? This cannot be reversed. Parent portal access will be revoked and student status will be set to Transfer Certificate Issued."
5. Branch Admin confirms.
6. Parent portal access revoked. Student status: **Transfer Certificate Issued**.
7. System records the issuance event with actor, timestamp, and all entered fields. School produces the physical TC externally.

**Exception path (outstanding dues exist): [IRREVERSIBLE]**
1. System detects outstanding dues → Branch Admin cannot proceed.
2. Admin must proceed. Admin reviews outstanding dues, enters required fields, and provides a mandatory recorded reason.
3. Admin confirms issuance. **Governance log entry created.**

**Escalation path:**
- Outstanding dues → escalates from Branch Admin to Admin.

**Mutual exclusivity:** TC and Bonafide are mutually exclusive. TC cannot be issued if Bonafide has already been issued, and vice versa.

**Outcome:** Transfer is formally documented with a permanent record including destination school, conduct remark, and any outstanding obligations.

---

## Domain G — Child Safety and Welfare

### P22 — Welfare Concern Observation and Review

**Trigger:** A Teacher observes a pattern or condition in a student that warrants Branch Admin attention — not necessarily a specific incident.
**Actors:** Teacher (flags), Branch Admin (reviews and acts), Admin (oversight).

**Primary Workflow :**
1. Teacher observes a concern during the school day. Teacher opens the attendance or Daily Care Logscreen for the student.
2. Teacher selects "Flag welfare concern."
3. System requires a mandatory free-text field: "Describe what you observed." The flag cannot be saved without this description.
4. System creates a WelfareConcernRecord (permanent, immutable): Teacher, timestamp, description, status **OPEN**.
5. In-app notification #8 fires to Branch Admin: "[Student name] has been flagged for a welfare concern by [Teacher name]. Review required."
6. Branch Admin reviews the record within 3 school days.
7. Branch Admin marks the record **REVIEWED** or **ESCALATED**, with a note.

**Exception paths:**
- *Branch Admin does not review within 3 school days:* System fires a reminder notification to Branch Admin. The reminder fires once. No further automated reminders.
- *Teacher who flagged the concern needs to see their own flag:* Teacher can view their own welfare concern flags. They cannot see flags created by other Teachers.

**Escalation path:**
- *Branch Admin marks the record ESCALATED:* This means a formal incident report should be created (P23). The welfare concern record remains as the initial observation; the incident report is a separate formal record.
- *Record remains OPEN beyond the review window and Branch Admin is unreachable:* Admin can review and act. No automated escalation to Admin exists — this is a Branch Admin responsibility.

**Outcome:** Every welfare concern has a permanent, immutable record with a named actor, mandatory description, and timestamped resolution. Parents never see welfare concern records under any circumstances.

---

### P23 — Incident Report Creation and Publication

**Trigger:** A notable event involving a student occurs during school hours — injury, behavioural episode, medical event.
**Actors:** Teacher (creates for own class students), Branch Admin or Admin (creates for any student; reviews all reports and decides to publish or keep internal).

**Primary Workflow :**
1. Teacher (or Branch Admin) creates a formal incident report for the student in M3: date of occurrence, incident type, what happened, action taken, and optional internal notes.
2. Incident report is created in **INTERNAL** state. Branch Admin is notified.
3. Branch Admin reviews the incident report.
4. Branch Admin decides: **Publish to parent portal** or **Keep internal**.
5. If published: system sends mandatory in-app notification #7 to the parent (bypasses opt-out). Parent sees: date of occurrence, incident type, what happened, and action taken. Internal notes and staff witness details are never shown to parents under any circumstances.
6. Incident report is permanently retained regardless of the publish decision.

**Exception paths:**
- *Multiple incidents for the same student in a short period:* Each incident is a separate, independent record. They do not aggregate automatically.
- *Incident report needs correction after creation:* Incident reports are append-only and immutable. Corrections are appended as new entries referencing the original.

**Escalation path:**
- *Branch Admin decides the incident requires escalation beyond the school:* This is handled outside the system (e.g. contacting parents, external authorities). The incident report record persists in the system. Branch Admin can also create a WelfareConcernRecord (P22) as a parallel internal flag if the pattern continues.

**Outcome:** Every significant incident has a permanent attributed record. The publish/keep-internal decision is documented and attributed — a deliberate trust management act.

---

### P24 — Authorised Pickup List Management

**Trigger:** A new student is being enrolled (pickup list required before enrollment completes), or a parent requests a change to their child's pickup list.
**Actors:** Branch Admin, Admin (add and deactivate entries). Teacher and Coordinator (view only). Parent (view only — cannot edit).

**Primary Workflow  — Adding an entry:**
1. Branch Admin navigates to the student profile → Authorised Pickup List.
2. Branch Admin adds the new authorised person: name, relationship, contact number.
3. Entry saved. Every addition is permanently logged with actor, timestamp, and reason.
4. Mandatory safety notification fires to the parent (notification bypasses opt-out): "The authorised pickup list for [child name] has been updated."
5. Guardian is not automatically on the pickup list — must be explicitly added. Emergency contacts and pickup list entries are distinct records.

**Primary Workflow  — Deactivating an entry:**
1. Branch Admin navigates to the student profile → Authorised Pickup List.
2. Branch Admin deactivates the entry with a recorded reason.
3. Deactivation logged permanently. Mandatory safety notification fires to the parent.
4. At least one active entry must remain at all times after enrollment completion. Deactivating the last active entry is blocked.

**Exception paths:**
- *Parent requests a removal at the gate:* Coordinator cannot edit the pickup list. Coordinator directs the parent to contact Branch Admin. Branch Admin processes the deactivation.
- *Pickup list has only one active entry and deactivation is requested:* System blocks the deactivation. Branch Admin must add a new entry before the existing one can be deactivated.

**Escalation path:**
- *Unregistered person presents at gate during checkout:* System blocks and alerts. Coordinator contacts Branch Admin immediately (see P07A escalation path). Branch Admin decides whether to proceed (with a governance log entry) or refuse release.

**Outcome:** Every child's release is governed by a verified, current list. Every change is permanently attributed and immediately notified to the parent.

---

## Domain H — Communication

### P25 — WhatsApp Template Management

**Trigger:** Admin wants to create or update a global template. Branch Admin wants to create or update a branch-scoped template for their branch.
**Actors:** Admin (global and branch templates), Branch Admin (own branch templates — if template management toggle is enabled).

**Primary Workflow  — Template Creation:**
1. Actor navigates to Communication → Template Management.
2. Actor selects template type: Absence notification, Fee reminder, Payment confirmation, or Incident notification.
3. Actor writes the template body using the available placeholder objects for that type.
4. System validates all placeholders in real time. Unrecognised placeholders are highlighted and block save.
5. Actor sets scope: global (Admin only) or branch-scoped.
6. Actor saves and activates. Template is immediately available to eligible roles in the applicable scope.
7. Creation event permanently logged: actor, timestamp, scope, type, and full content.

**Primary Workflow  — Template Editing:**
1. Actor navigates to existing template and edits body or placeholder usage.
2. System re-validates all placeholders on each edit.
3. Actor saves. Updated version takes effect immediately for all future deeplink generations.
4. Previous version is retained in read-only version history. Not deleted.
5. Edit event permanently logged with before content, after content, actor, and timestamp.

**Exception paths:**
- *Template is the last active template of that type for the branch and actor tries to delete it:* Deletion blocked. Actor can only deactivate.
- *Branch Admin attempts to edit a global template:* Not permitted. Branch Admin can only create and edit branch-scoped templates (if toggle enabled).
- *Restoring a previous version:* No automated restore. Actor must manually create a new template from the previous content visible in version history.

**Template fallback chain at send time:** Branch-scoped template → Global template → Blocked (in-app alert to Branch Admin if neither exists).

**Outcome:** Every WhatsApp communication uses an approved, versioned template. Template content cannot change silently.

---

### P26 — WhatsApp Deeplink Send

**Trigger:** An eligible role needs to send a communication to a parent via WhatsApp.
**Actors:** Admin, Branch Admin, Coordinator (absence only), Teacher (absence only), Accountant (fee types only).

**Primary Workflow :**
1. Actor navigates to the relevant screen: student attendance record (absence deeplink), student fee record (fee reminder or payment confirmation), or student incident record (incident notification — Branch Admin only).
2. Actor selects "Send WhatsApp deeplink." Only deeplink types available to that role are shown.
3. System resolves the template: checks for a branch-scoped template first, falls back to the global template, blocks the action with an in-app alert if neither exists.
4. System pre-fills all placeholder values from live module data at the moment of send.
5. Actor reviews the pre-filled message.
6. Actor taps Send → WhatsApp opens with the message pre-composed for the parent's primary contact number.
7. System logs: actor, student, template type, template version used, timestamp, and "link opened" status.

**Exception paths:**
- *No template configured for this type at branch or global level:* System blocks the send. In-app alert fires to Branch Admin: a template must be created before this type of deeplink can be sent.
- *Parent's primary contact number is not a WhatsApp number:* The deeplink opens WhatsApp but delivery is not guaranteed. System has no delivery confirmation mechanism.

**Note:** Only "deeplink opened by actor" is logged — delivery and read status are not tracked. Automated dispatch is V2.

**Outcome:** A structured, approved, pre-filled WhatsApp message is initiated. Every send attempt is permanently attributed to the actor.

---

### P27 — Announcements to Parents

**Trigger:** Branch Admin or Coordinator needs to communicate a message to a defined audience of parents.
**Actors:** Branch Admin, Coordinator, Admin (send). Parents (receive).

**Primary Workflow :**
1. Actor navigates to Communication → Announcements.
2. Actor selects the audience level: All parents in the branch, By programme, By class, or Individual parent.
3. System displays the recipient count before send. Actor reviews.
4. Actor sends the announcement. Delivered as an in-app notification to every parent in the defined audience.
5. Every announcement is permanently logged with actor, timestamp, audience scope, and full content.

**Exception paths:**
- *No parents have active portal accounts yet (pre-go-live):* Announcements are sent to accounts that exist. Zero recipients is a valid outcome if no portal accounts are yet active.

**Outcome:** Broadcast communication is intentional, attributed, and permanently logged.

---

### P28 — School Inbox (Parent-Initiated Thread)

**Trigger:** A parent sends a message from the parent portal regarding a non-fee matter.
**Actors:** Parent (initiates and replies), Branch Admin or Coordinator (view and reply). Teacher and Accountant have no access.

**Primary Workflow :**
1. Parent navigates to parent portal → "Message school." Message is linked to a specific enrolled child.
2. Message arrives in the school inbox, accessible by Admin, Branch Admin, and Coordinator.
3. Branch Admin or Coordinator receives in-app notification #10.
4. Branch Admin or Coordinator reads and replies. Reply delivered as an in-app notification to the parent.
5. Parent receives the reply and can reply back in the same thread.
6. Thread continues until Branch Admin marks it **CLOSED**. CLOSED is terminal — no further messages can be added.
7. Maximum 5 parent-initiated messages per student per day. Staff replies have no daily limit.
8. All messages and replies are permanently retained for 3 years.

**Exception paths:**
- *Parent sends a fee-related question via school inbox:* Branch Admin or Coordinator sees the message but does not have fee data access. They direct the parent to use the Fee Query channel for fee questions.
- *Parent hits the 5-message daily limit:* System blocks further messages for that student that day. The limit resets the following day.
- *Messages cannot be deleted:* No role can delete any school inbox message or reply.

**Outcome:** Parent concerns are handled through a logged, structured channel. Every exchange is permanently attributed and retained.

---

## Domain I — Go-Live Governance

### P29 — Migration Mode Entry and Exit

**Trigger:** Admin prepares a branch for go-live. Followed after system provisioning, before the first live school day.
**Actors:** Admin (entry and exit), Branch Admin (migration preparation activities).

**Entry (one-time per branch): [CANNOT BE RE-ENTERED]**
1. Admin navigates to Branch Settings → Migration → "Enter Migration Mode."
2. System prompts: "Migration Mode will suppress invoice generation and disable the opening balance window closure rule for this branch until exited. Confirm?"
3. Admin confirms → branch enters Migration Mode. Entry logged in governance log with actor and timestamp.
4. Suppressed: invoice generation (all sources), Razorpay payment links, parent portal activation.
5. Normal: audit log, student profile creation, CoFee XLSX import, opening balance entry, all other operations.

**Migration preparation (Branch Admin):**
6. Branch Admin creates all student profiles. For each migrated student, Branch Admin manually enters the student's historically assigned Admission ID — the system preserves it as the permanent ID (uniqueness enforced at save time; format consistency is a data-preparation responsibility before entry). Branch Admin also enters opening balances per student.
7. Branch Admin enters Historical Attendance Markers for mid-year go-live students (one field per student: "Days present before go-live this academic year"). This field locks permanently on Migration Mode exit.
8. Accountant uploads CoFee XLSX import and resolves all exceptions.
9. Branch Admin provisions and activates all staff accounts.
10. Branch Admin completes the 9-step academic year setup.
11. System maintains a migration checklist visible to Admin and Branch Admin across six categories: student profiles, authorised pickup lists, opening balances, CoFee XLSX import, academic year setup, and staff accounts.

**Exit (one-time per branch): [IRREVERSIBLE]**
12. Admin reviews the migration checklist. All six items must be green or explicitly acknowledged with a logged reason. Items that are incomplete and not acknowledged block exit.
13. Admin navigates to the checklist screen and initiates exit.
14. System prompts: "Exiting Migration Mode will activate invoice generation, Razorpay payment links, and parent portal accounts. This cannot be reversed. Confirm?"
15. Admin confirms. Simultaneously:
    - All suppressed parent portal activation invites fire at once.
    - Invoice generation unlocks for the branch.
    - Razorpay payment links become available.
    - Historical Attendance Marker fields lock permanently.
    - Opening balance window closure rule resumes for subsequent events.
16. Exit event permanently logged in governance log: actor, timestamp, and complete checklist state at exit.

**Exception path — incomplete checklist items:**
- Admin can acknowledge an incomplete item with a recorded reason. For example: "Two students' opening balances cannot be confirmed before academic year begins — will reconcile post-go-live." Acknowledged items are permanently logged.

**Re-entry:** Not permitted by any actor, including Admin. Once a branch has exited Migration Mode, it cannot return to that state.

**Outcome:** The school transitions to live Happy Feet operation in a controlled, gated, irreversible step. Every data preparation decision is permanently recorded; every prerequisite is explicitly confirmed or acknowledged.

---

## Domain J — Reporting and Oversight

### P30 — Operational Dashboard Access

**Trigger:** Admin, Branch Admin, or Accountant needs to view the school's current operational state.
**Actors:** Admin (cross-branch), Branch Admin (own branch), Accountant (financial view).

**Primary Workflow :**
1. Actor logs in and navigates to the dashboard.
2. System loads role-filtered widgets from pre-computed summary tables.
3. Actor views current operational state: active student count, today's attendance, enrollment by programme and class, outstanding balance with last import timestamp, recent admissions, expense summary, document verification status, recent announcements.
4. Accountant sees: overdue invoices, total outstanding balance, Day Care billing summary, fee collection summary by period, expense log.
5. Admin additionally sees: cross-programme enrollment trend, cross-branch comparison, governance log, monthly governance digest.

**No actions are available from dashboards** — dashboards are read-only. CSV exports are initiated separately (see P31).

**Outcome:** Admin and Branch Admin have real-time operational visibility without requesting verbal updates from staff.

---

### P31 — CSV Export

**Trigger:** Admin, Branch Admin, or Accountant needs a data extract for analysis, external reporting, or record-keeping.
**Actors:** Admin, Branch Admin, Accountant (by export type).

**Primary Workflow :**
1. Actor navigates to Reports → Exports.
2. Actor selects the export type (student list, enrollment, attendance, staff attendance, invoice list, payment list, communication log, governance report, expense log, daily journal, welfare concern report, leave register).
3. Actor applies filters (date range, class, programme, etc.) as needed.
4. System generates the export from authoritative source data at the time of the request.
5. Export is downloaded.
6. Every export is automatically access-logged: actor, timestamp, export type, and filter parameters.

**Data protection rules applied to all exports:**
- Aadhaar numbers and medical records are excluded from all exports unconditionally.
- Governance report is Admin-only — no other role can generate it.

**Outcome:** Authorised roles get structured, filtered, auditable extracts. Every extraction is logged.

---

### P32 — Monthly Governance Digest

**Trigger:** Automated — fires on the 1st of each month.
**Recipient:** Admin (email).

**Primary Workflow :**
1. On the 1st of each month, system generates an email to Admin summarising the prior month across all branches.
2. Digest includes:
   - Override actions (attendance corrections beyond 30 days, Bonafide/TC with outstanding dues, discontinuation acknowledgments, capacity overrides, document waivers, Migration Mode events)
   - Fee activity (CoFee imports run, exceptions resolved, payments recorded)
   - Attendance metrics (classes with missed marking windows, chronic absence alerts fired, welfare concern flags raised)
3. Digest is generated from existing audit and governance logs — no new data is computed.
4. No action is required from Admin to receive the digest.

**Outcome:** Admin receives a structured monthly accountability summary of every exception and override across all branches without having to run a report.

---

## Process Cross-Reference: Triggers and Their Source

| Trigger Event | Process(es) Initiated |
|---|---|
| New academic year beginning | P01 — Academic Year Setup |
| New staff member joins | P02 — Staff Onboarding |
| Staff member leaves | P03 — Staff Exit |
| Parent submits admission form | P04 — Admission Application and Enrollment |
| Discontinued student returns | P05 — Re-admission |
| Class reaches capacity | P06 — Waitlist Management |
| Day Care student arrives at school | P07B — Day Care Check-In Recording |
| Student departs from school premises | P07A — Gate Pickup Verification |
| Day Care student departs from school | P07A — Gate Pickup Verification; P07B — Day Care Check-Out Recording |
| School day attendance window opens | P08 — Daily Attendance Marking |
| Teacher observes student during the day | P09 — Daily Activity Logging |
| Academic year setup begins | P10 — Fee Structure Setup |
| CoFee report available | P11 — CoFee XLSX Import |
| Ad-hoc fee needed outside billing cycle | P12 — Ad-Hoc Invoice and Razorpay Link |
| Day Care billing period ends | P13 — Day Care Billing Summary |
| Parent has a fee question | P14 — Fee Query Resolution |
| Student migrated into system | P15 — Opening Balance Entry |
| Progress submission window opens | P16 — Progress Card Submission and Publication |
| Published progress card has an error | P17 — Progress Card Revision |
| Student leaves school mid-year | P18 — Student Discontinuation |
| Academic year ends | P19 — Year-End Rollover |
| Student completes programme | P20 — Bonafide Certificate Issuance |
| Student transfers to another school | P21 — Transfer Certificate Issuance |
| Teacher observes welfare concern | P22 — Welfare Concern Observation and Review |
| Incident occurs during school hours | P23 — Incident Report Creation and Publication |
| New student enrolls / pickup person changes | P24 — Authorised Pickup List Management |
| Communication template needed | P25 — WhatsApp Template Management |
| Staff needs to message a parent via WhatsApp | P26 — WhatsApp Deeplink Send |
| School needs to inform a group of parents | P27 — Announcements |
| Parent sends a non-fee message to school | P28 — School Inbox |
| Admin prepares branch for go-live | P29 — Migration Mode Entry and Exit |
| Staff needs current operational view | P30 — Operational Dashboard Access |
| Data extract needed | P31 — CSV Export |
| 1st of the month | P32 — Monthly Governance Digest |

---

## Processes That Are Permanently Irreversible

The following processes create states that cannot be undone by any role:

| Process | Irreversible Action | Who Confirms |
|---|---|---|
| P18 — Student Discontinuation | All three lifecycle switches disabled simultaneously; status: Discontinued | Branch Admin |
| P19 — Year-End Rollover | Active → Completed Programme; switches reset to ON | Branch Admin |
| P20 — Bonafide Certificate Issuance | Status: Bonafide Issued; portal access revoked | Branch Admin (no dues) / Admin (dues exist) |
| P21 — Transfer Certificate Issuance | Status: TC Issued; portal access revoked | Branch Admin (no dues) / Admin (dues exist) |
| P29 — Migration Mode Exit | All suppressed events fire; Historical Attendance Marker locks; cannot re-enter | Admin |
| P16 — Progress Card Publication | Attendance snapshot locked at publication moment | Branch Admin |
| P12 — Invoice Creation (any type) | Opening balance window closes for that student after first invoice | Accountant / Branch Admin / Admin |

---

## Key Process Design Principles Observed

**1. Irreversibility is announced before it occurs.**
Every irreversible action is preceded by an explicit confirmation prompt stating the consequences. The system does not silently lock state.

**2. Exception paths are narrower than the normal path.**
Capacity overrides, document waivers, Bonafide or TC issuance with outstanding dues, discontinuation acknowledgments, corrections beyond 30 days — all permitted, but each requires higher authority, a recorded reason, and a governance log entry.

**3. No bulk financial acknowledgment.**
Discontinuation, rollover, TC issuance, and Migration Mode exit each require per-invoice acknowledgment with a reason. Bulk acknowledgment is not permitted anywhere.

**4. Safety-critical notifications bypass parent opt-out.**
Pickup list changes, portal activation, enrollment, Bonafide, and incident publication always reach parents regardless of notification preferences.

**5. Welfare concern review is the only timed obligation with an inaction reminder.**
Branch Admin must review P22 records within 3 school days; a single reminder fires if unreviewed.

---

**Next recommended artifact:** BUSINESS_RULES.md (Stage 5) — explicit rules, implicit rules, invariants, compliance requirements, safety requirements, and financial requirements, separated and named.


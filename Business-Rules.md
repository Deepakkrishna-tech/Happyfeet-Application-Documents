<!-- ABOUT
File: BUSINESS_RULES.md
Purpose: Catalogues every explicit rule, implicit rule, invariant, compliance requirement, safety requirement, and financial requirement governing Happy Feet's operations, separated by type for design and verification use.
Scope: 6 rule categories with numbered entries across access, enrollment, attendance, lifecycle, safety, financial, communication, and data domains; excludes process workflows and capability definitions.
Dependencies: DOMAIN_OVERVIEW.md, BUSINESS_CAPABILITY_MAP.md, ORGANIZATION_MODEL.md, BUSINESS_PROCESSES.md
Stage: Stage 5 — Business Rules
-->

# Business Rules — Happy Feet School ERP

**Stage:** 5 — Business Rules
**Source:** PRODUCT_OVERVIEW_v2.md, DOMAIN_OVERVIEW.md, BUSINESS_CAPABILITY_MAP.md, ORGANIZATION_MODEL.md, BUSINESS_PROCESSES.md
**Date:** 2026-06-05
**Status:** Complete

---

## Reading Guide

Rules are numbered within each category for cross-reference (e.g. EX-04, IM-12, INV-03).

- **Explicit Rules (EX):** Stated directly in the source document.
- **Implicit Rules (IM):** Not stated but clearly implied by the domain; marked with reasoning.
- **Invariants (INV):** Must always be true regardless of any circumstance or workaround.
- **Compliance Requirements (CR):** Legal, regulatory, or data protection obligations.
- **Safety Requirements (SR):** Rules that exist specifically to protect children or staff.
- **Financial Requirements (FR):** Rules governing financial records, obligations, and operations.

---

## 1. Explicit Rules

### 1A — Access and Role Rules

**EX-01:** Each staff member holds exactly one role. No dual roles, no role combinations are permitted in V1.

**EX-02:** A staff member can be bound to one or more branches under that single role. In any given session they operate in one branch only.

**EX-03:** Multi-branch bindings are created and deactivated by Admin only. Branch Admin cannot bind staff to other branches.

**EX-04:** Role and class assignment changes take effect at the staff member's next login — not immediately during the current session.

**EX-05:** Session inactivity timeout is 30 minutes for all roles without exception.

**EX-06:** Parent portal access is strictly child-scoped by a hard business rule. No staff role — including Admin — can modify the scope of parent access.

**EX-07:** Parent portal accounts are created when Accountant records the first payment — not at enrollment completion. Parents cannot self-register.

**EX-08:** Custom roles are not available in V1. The five built-in roles (Admin, Branch Admin, Coordinator, Teacher, Accountant) are fixed and cannot be renamed or removed.

**EX-09:** The only per-account permission variation in V1 is a single toggle on Branch Admin accounts: "Allow this Branch Admin to create and edit WhatsApp templates." Enabled by default; Admin can disable it. The Branch Admin can still use templates but cannot create or edit them when the toggle is disabled.

**EX-10:** No administrator can view or set another user's password. Password reset is self-service via a 24-hour email link only.

**EX-11:** Access is revoked automatically on: staff exit confirmation; student discontinuation; Bonafide certificate issuance; Transfer Certificate issuance.

**EX-12:** Branch data isolation is enforced at the application layer. Every data request is validated against the requesting user's branch binding before any data is returned. A staff member bound to Branch A cannot access Branch B data by any means.

**EX-13:** When Admin performs an action normally owned by another role, the system surfaces a one-click confirmation before proceeding, identifying the action and stating it will be logged as an Admin action. The confirmed override is recorded in the audit log.

---

### 1B — Admissions and Enrollment Rules

**EX-14:** Four independent enrollment conditions must all be satisfied before enrollment can complete: (i) programme assigned, (ii) all required documents cleared or each individually waived with a recorded reason, (iii) class capacity available or override recorded with reason, (iv) at least one active authorised pickup list entry exists for the student.

**EX-15:** Blanket document waiving is not permitted. Each missing document must be individually waived with a recorded reason. A single recorded reason cannot cover multiple documents.

**EX-16:** The guardian is not automatically added to the authorised pickup list at enrollment. They must be explicitly added by Branch Admin.

**EX-17:** The term "Declined" is used for rejected applications throughout the system. The term "Rejected" is never used.

**EX-18:** Duplicate application submissions for the same child in the same admissions window are blocked by the system.

**EX-19:** The fee structure is snapshotted as a full data copy at enrollment completion. This snapshot governs billing for that student's enrolment period.

**EX-20:** Declined applications are permanently retained and can be reopened by Branch Admin.

**EX-21:** Re-admission is not a new application. When a student returns from Discontinued or Completed Programme status, the existing profile is reopened. The same Admission ID and full prior history are retained. Outstanding dues are not cleared.

**EX-22:** Coordinator can advance an admissions application (track documents, follow up) but cannot independently complete enrollment. Branch Admin must confirm all four enrollment conditions and complete enrollment.

**EX-23:** Branch Admin can enroll walk-in students directly at any time. No admissions window restriction applies to walk-in entry.

---

### 1C — Attendance Rules

**EX-24:** Attendance is disabled on school holidays for all roles. No override exists for any role.

**EX-25:** At most one attendance record per student per class per date.

**EX-26:** Attendance correction authority by time window (measured in calendar days, not working days):

| Window | Who can correct | Reason required |
|---|---|---|
| Same calendar day | Teacher | No |
| Day 2 – Day 7 | Teacher | Yes — one sentence minimum |
| Day 8 – Day 30 | Branch Admin | Yes, permanently logged |
| Beyond Day 30 | Admin only | Yes, logged; governance log entry |

**EX-27:** Every attendance correction records: original status, new status, actor, timestamp, and reason (where required).

**EX-28:** If a Teacher does not mark attendance within the window, Branch Admin is alerted. Branch Admin can bulk-mark the entire class as Present with a logged reason.

**EX-29:** Welfare concern records are visible only as follows: Teacher sees own flags only; Branch Admin sees all for own branch; Admin sees all. Parents never see welfare concern records under any circumstances.

**EX-30:** A welfare concern record is created only when Teacher submits a mandatory free-text observation. The flag cannot be saved without the description.

**EX-31:** Branch Admin must review a welfare concern record within 3 school days. If unreviewed, one reminder notification fires. No further automated reminders are sent.

**EX-32:** Day Care billing units are derived from gate timestamps: full day (CHECK_OUT − CHECK_IN ≥ 4 hours) = 1.0 billing unit; half day (less than 4 hours) = 0.5 billing unit.

**EX-33:** QR system unavailability does not prevent attendance marking. The manual fallback is always available.

**EX-34:** Chronic absence threshold is configured per programme by Branch Admin with two configurable parameters: consecutive absences and total absences in a period. When the threshold is crossed, notification #9 fires immediately to Branch Admin.

---

### 1D — Academic Year Setup Rules

**EX-35:** The 9-step academic year setup must complete in prescribed sequence before any dependent module (admissions, attendance, invoice generation, progress tracking) can operate.

**EX-36:** Only one upcoming academic year can be configured while the current year is active.

**EX-37:** The upcoming academic year activates automatically at midnight on its configured start date. No manual trigger required. Admin can manually activate or delay activation; every manual override is permanently logged.

**EX-38:** School calendar holidays disable attendance system-wide. No role can override attendance on a day the school has declared a holiday.

**EX-39:** Progress card submission window open and close dates are set by Branch Admin per term, per programme. There is no system-imposed minimum or maximum window duration.

---

### 1E — Progress Tracking Rules

**EX-40:** Teacher drafts are never visible to Branch Admin or parents until the Teacher submits the card.

**EX-41:** Progress card attribution (who drafted, who submitted) is permanent at submission and cannot be altered.

**EX-42:** Branch Admin cannot edit the content of a submitted progress card. Branch Admin can only publish or return the card.

**EX-43:** When Branch Admin returns a card, Branch Admin must explicitly reopen it before the Teacher can re-edit. The Teacher cannot edit a submitted card without this explicit action.

**EX-44:** The attendance snapshot is locked from live M8 data at the exact moment of publication. The snapshot is immutable thereafter and cannot be changed even by correcting attendance post-publication.

**EX-45:** Delegate Reviewer can add review notes (visible to Branch Admin only) but cannot publish a progress card. Publication authority remains exclusively with Branch Admin.

**EX-46:** All programmes except Event-type participate in progress tracking.

---

### 1F — Student Lifecycle Rules

**EX-47:** Five mutually exclusive student statuses: Active, Discontinued, Completed Programme, Bonafide Issued, Transfer Certificate Issued. A student can hold only one status at any time.

**EX-48:** Three independent per-student switches exist (billing on/off, attendance screen on/off, parent portal access on/off). Each switch change requires a logged reason.

**EX-49:** Discontinuation: Branch Admin must acknowledge each outstanding invoice individually with a recorded reason before discontinuation can complete. Bulk acknowledgment is not permitted.

**EX-50:** On discontinuation confirmation, all three lifecycle switches are disabled simultaneously in one atomic operation.

**EX-51:** Year-end rollover is always manually triggered. It never runs automatically.

**EX-52:** Rollover: Branch Admin must acknowledge each outstanding invoice individually with a recorded reason. Bulk acknowledgment is not permitted.

**EX-53:** All Active students transition to Completed Programme on rollover confirmation. All three lifecycle switches reset to ON for all rolling-over students.

**EX-54:** Rollover preview flags any student whose switches were not all ON before confirmation. Branch Admin must review these explicitly.

**EX-55:** Bonafide certificate issuance is irreversible by any role. Admin is required when outstanding dues exist. Outstanding dues snapshot is permanently attached to the Bonafide record.

**EX-56:** Transfer Certificate issuance is irreversible by any role. Admin is required when outstanding dues exist.

**EX-57:** Bonafide and Transfer Certificate are mutually exclusive for the same student. A TC cannot be issued if a Bonafide has already been issued, and vice versa.

**EX-58:** Student records are never deleted regardless of student status.

---

### 1G — Staff Rules

**EX-59:** Teacher exit gate: the Primary slot on every class the Teacher is assigned to must be reassigned to a replacement Teacher before exit can proceed.

**EX-60:** All staff exit gate: all salary holds must be formally resolved before exit can proceed. Hold type 1: first-month salary held (must be formally released). Hold type 2: degree in custody (must be returned and logged).

**EX-61:** On exit confirmation, the staff profile becomes read-only and system access is revoked automatically.

**EX-62:** Staff profiles are never deleted.

**EX-63:** Salary history is append-only. Every change adds a new entry with an effective date.

**EX-64:** Leave records are append-only and immutable. Corrections are appended as new records.

**EX-65:** Daily staff attendance is backdatable up to 7 calendar days with a logged reason.

**EX-66:** Branch Admin cannot provision another Branch Admin account. Admin is required to create Branch Admin accounts.

**EX-67:** Staff certification expiry alert thresholds: NTT Certificate (60 days); Montessori Certification (60 days); First Aid Certificate (30 days); Police Verification (60 days, computed as issue date + 3 years); Annual Employment Agreement (30 days before renewal date).

---

### 1H — Communication Rules

**EX-68:** WhatsApp communication is manual tap-to-send in V1. Automated WhatsApp dispatch is V2.

**EX-69:** A template cannot be deleted if it is the last active template of that type for a branch. It can only be deactivated.

**EX-70:** Template fallback chain at send time: branch-scoped template → global template → blocked (in-app alert to Branch Admin).

**EX-71:** System notifications #1, #2, #4, #7, and the pickup list change notification bypass all parent opt-out settings unconditionally. These notifications always reach the parent.

**EX-72:** No notification fires when a school notice board post is published. Parents see an unread badge on their next portal visit.

**EX-73:** Maximum 5 parent-initiated messages per student per day in the school inbox. Staff replies have no daily limit.

**EX-74:** School inbox messages and replies cannot be deleted by any role. All messages are retained for 3 years.

**EX-75:** The CLOSED status on a school inbox thread is terminal. No further messages can be added to a closed thread.

**EX-76:** Aadhaar numbers, medical records, and parent contact details are never included in any communication, notification, or deeplink template.

**EX-77:** Fee query threads stay in the fee query channel. They do not migrate to the general school inbox.

**EX-78:** Monthly governance digest fires automatically on the 1st of each month and is sent to Admin's email. It covers all branches and requires no manual action.

---

### 1I — Data Rules

**EX-79:** Every action across every module is written to a permanent, immutable audit log. No role can edit or remove any audit log entry.

**EX-80:** Every audit log entry records: timestamp, actor name and role, branch, module, action type, target entity, before state, after state, and reason where required.

**EX-81:** The governance log is a filtered view of the audit log showing only override-class actions. Visible to Admin only.

**EX-82:** Financial records are retained for 7 years. Communication logs are retained for 3 years.

**EX-83:** Daily activity log entries are immutable after saving. Corrections are appended as new entries referencing the original.

**EX-84:** Incident reports are append-only and immutable. Corrections are appended as new entries.

**EX-85:** WelfareConcernRecords are permanent and immutable. Status changes are appended, not overwritten.

**EX-86:** Aadhaar numbers and medical records are access-logged on every read. They are excluded from all exports, emails, and notifications unconditionally for every role.

**EX-87:** Full audit log access: Admin (all branches), Branch Admin (own branch only). No other role has direct audit log access.

**EX-88:** CSV exports are access-logged with actor, timestamp, export type, and filter parameters on every generation.

---

### 1J — Migration Mode Rules

**EX-89:** Migration Mode is per-branch. Branch A can be in Migration Mode while Branch B is fully live.

**EX-90:** Migration Mode is controlled exclusively by Admin. Branch Admin cannot enter or exit Migration Mode.

**EX-91:** The following are suppressed during Migration Mode: invoice generation (all sources), Razorpay payment links, parent portal activation. All other operations run normally.

**EX-92:** The opening balance window cannot close while a branch is in Migration Mode.

**EX-93:** Migration Mode exit requires all 6 checklist items to be green or explicitly individually acknowledged with a logged reason. Items that are incomplete and unacknowledged block exit.

**EX-94:** On Migration Mode exit, the following occur simultaneously: all suppressed parent portal activation invites fire at once; invoice generation unlocks; Razorpay payment links become available; Historical Attendance Marker fields lock permanently.

**EX-95:** Re-entry into Migration Mode is not permitted for any actor, including Admin. Migration Mode is a one-time event per branch.

**EX-96:** The Historical Attendance Marker field is available for entry only during Migration Mode. It locks permanently on Migration Mode exit and cannot be edited thereafter.

**EX-97:** The Migration Mode exit event is permanently recorded in the governance log with: acting Admin, timestamp, and complete checklist state at the moment of exit.

---

## 2. Implicit Rules

Rules that are not explicitly stated but are clearly implied by the domain model and operational design.

**IM-01:** A student's fee structure snapshot does not update automatically when the school changes its fee configuration after enrollment. An explicit update to the fee snapshot is required if the enrolled student's terms change.
*Why implied:* Fee structure is snapshotted as a "full data copy" at enrollment; subsequent fee changes are not described as propagating to existing snapshots.

**IM-02:** No invoice can be generated, no attendance can be marked, and no progress card window can be opened for a branch that has not completed all 9 steps of academic year setup.
*Why implied:* The product document states these modules depend on the academic configuration framework being complete.

**IM-03:** Day Care billing accuracy is dependent on QR infrastructure operating correctly. Manual attendance days without QR check-out data produce billing estimates, not accurate billing figures.
*Why implied:* The billing unit derivation rule depends on CHECK_OUT − CHECK_IN; manual marks have no check-out timestamp.

**IM-04:** A student's outstanding dues from a previous enrollment period persist through discontinuation, re-admission, and rollover. No system process automatically writes off or clears outstanding dues.
*Why implied:* The document states dues "are not cleared by re-admission" and "remain on the record" at discontinuation.

**IM-05:** Sibling detection is based solely on matching parent phone numbers. If two children have the same parent but different phone numbers on file, no sibling flag is triggered.
*Why implied:* The product document specifies "same parent phone number" as the detection mechanism; no other identifier is mentioned.

**IM-06:** CoFee is the authoritative source for payment status in V1. The parent portal shows fee structure only — not actual payment status. Parents must confirm payment status through CoFee directly.
*Why implied:* Explicitly stated for the parent portal; implied that this creates a gap the school must manage in parent communication.

**IM-07:** The school must communicate fee terms to CoFee separately. Happy Feet fee structure configuration does not automatically flow to CoFee — these are two independent systems in V1.
*Why implied:* The fee setup workflow states "Fee configuration is communicated to CoFee separately for invoice generation."

**IM-08:** A Branch Admin designated as a Delegate Reviewer for a class must not be confused with the Branch Admin's standard review role. The Delegate is a Teacher designated to add notes — not a Branch Admin substitute.
*Why implied:* The delegate reviewer is configured as a per-class permission extension; the document emphasises the delegate cannot publish.

**IM-09:** Transport billing is fixed per zone, not variable. A student in a given zone is billed the fixed monthly amount for that zone regardless of actual transport usage in any given period.
*Why implied:* Transport billing is described as "fixed monthly amounts per zone" with no variable usage component.

**IM-10:** A class cannot exist without a Primary teacher slot. If the Primary slot is vacated and no replacement is assigned, the class is in an operationally unsupported state.
*Why implied:* Primary slot is mandatory on every class; Teacher exit gate requires replacement assignment before exit proceeds.

**IM-11:** The school operates on a "no silent bypass" model. Every exception requires higher authority, a recorded reason, and a permanent log entry. There is no operational path that allows an exception to proceed without attribution.
*Why implied:* Stated explicitly as a product principle ("every exception has a sanctioned path"); implied to apply to every exception path in every process.

**IM-12:** Fee queries routed to the Accountant's fee communication log are not visible to Coordinator. Fee questions received in the general school inbox cannot be answered by Coordinator (who lacks fee data access) and must be redirected to the Fee Query channel.
*Why implied:* Coordinator has no access to fee data or the fee query channel; this creates a practical redirect requirement.

**IM-13:** The admission number normalisation configuration must be completed before the first CoFee import runs. Without it, every CoFee row will fail to match and go to the exception queue.
*Why implied:* Normalisation is described as a prerequisite for reliable matching; without it the primary match key fails.

**IM-14:** Historical Attendance Marker data accuracy depends entirely on the accuracy of the paper register from which Branch Admin manually transcribes it. There is no system validation of the marker value.
*Why implied:* The marker is a single manual field entry; the product document acknowledges "full-year attendance figures in Year 1 are only as accurate as the manual marker data."

**IM-15:** A student who has never had a first payment recorded has no parent portal account. Actions dependent on parent portal access (portal-visible incident reports, notice board, progress cards, daily feed) are inaccessible to that family.
*Why implied:* Portal access is created on first payment recording; no payment = no account = no portal access.

---

## 3. Invariants

Rules that must remain true at all times, regardless of any circumstances, workarounds, or edge cases.

| ID | Invariant | Category | Severity if violated |
|---|---|---|---|
| INV-01 | No child can be released to a person not on the active authorised pickup list without a Branch Admin decision and a governance log entry | Safety | Critical |
| INV-02 | At least one active authorised pickup list entry must exist for every enrolled student at all times | Safety | Critical |
| INV-03 | Aadhaar numbers and medical records are never exported, emailed, or included in any notification for any role | Legal / Privacy | Critical |
| INV-04 | Audit log entries are immutable. No role can edit or delete any entry | Governance | Critical |
| INV-05 | A student's Admission ID is permanent and cannot be changed once assigned | Operational | High |
| INV-06 | The fee structure snapshot at enrollment is the contractual basis for billing. It cannot be silently overwritten | Financial | High |
| INV-07 | Every irreversible action (Bonafide, TC, Discontinuation, Rollover confirmation, Migration Mode exit) must be preceded by an explicit confirmation prompt stating the consequences | Governance | High |
| INV-08 | WelfareConcernRecords are permanent and immutable. They cannot be deleted or overwritten by any role | Safety / Legal | Critical |
| INV-09 | Daily activity log entries are immutable after saving. They cannot be deleted or overwritten | Governance | High |
| INV-10 | Incident reports are append-only and immutable. Internal notes and staff witness details are never shown to parents under any circumstances | Safety / Trust | Critical |
| INV-11 | Parent access is always strictly child-scoped. It cannot be broadened to include other students or financial data beyond the V1-defined scope by any staff role | Privacy | Critical |
| INV-12 | Branch data isolation is enforced on every data request. A staff member cannot access data from a branch to which they are not bound | Security | Critical |
| INV-13 | Student profiles, staff profiles, welfare concern records, and audit entries are never deleted from the system | Governance / Legal | Critical |
| INV-14 | No financial acknowledgment is bulk. Every outstanding invoice requires an individual recorded reason at discontinuation, rollover, TC issuance, and Migration Mode exit | Financial / Governance | High |
| INV-15 | The attendance snapshot locked at progress card publication is immutable. Post-publication attendance corrections do not retroactively update any published card's snapshot | Academic | High |
| INV-16 | Migration Mode re-entry is permanently blocked once a branch has exited. This cannot be overridden by any actor including Admin | Governance | Critical |
| INV-17 | The Historical Attendance Marker locks permanently on Migration Mode exit. It cannot be edited thereafter by any role | Academic | High |
| INV-18 | Safety-critical parent notifications (portal invite, enrollment, Bonafide issuance, incident publication, pickup list change) always reach parents regardless of notification preferences | Safety | Critical |

---

## 4. Compliance Requirements

**CR-01 — DPDP Act (India's Digital Personal Data Protection Act):**
All personal data handling — collection, storage, access, and export — must comply with the DPDP Act. Sensitive personal data (Aadhaar, medical records, parent contacts) requires access-logging on every read and must be excluded from all exports and communications. Photo sharing is specifically deferred to V2 pending DPDP compliance review.

**CR-02 — Financial record retention:**
Financial records must be retained for a minimum of 7 years. This applies to invoices, payments, outstanding balances, and import logs regardless of student status.

**CR-03 — Communication log retention:**
Communication logs (school inbox, fee query threads, announcements) must be retained for a minimum of 3 years.

**CR-04 — APAAR / PEN (Unique Academic ID):**
APAAR and PEN registration is voluntary and requires explicit parental consent. The system notes these as regulatory obligations but submission workflows are out of scope for V1. Staff cannot submit APAAR/PEN data on behalf of parents without consent.

**CR-05 — UDISE+ reporting:**
UDISE+ annual school reporting is a regulatory obligation for the school. Happy Feet data exports support this obligation, but automated submission to UDISE+ is not in scope for V1.

**CR-06 — Aadhaar data handling:**
Aadhaar numbers stored in the system are access-logged on every read. They are never exported, emailed, or included in any system-generated notification. This applies unconditionally to every role including Admin.

**CR-07 — Self-service password reset:**
No administrator can view or set another user's password. Password reset is self-service via a 24-hour email link. This prevents credential exposure from privileged access.

**CR-08 — Audit completeness:**
Every action across every module — including every role and including Admin overrides — is written to the permanent audit log. No module or role is exempt. This is a governance compliance requirement, not a technical choice.

---

## 5. Safety Requirements

Safety requirements specific to the 18 months–6 years age group. These rules exist because children in this age group cannot advocate for themselves.

**SR-01 — Authorised pickup verification is an absolute gate.**
The system blocks child release to any person not on the active authorised pickup list. The system does not issue a warning or prompt staff to use their judgement — it blocks the action and instructs Coordinator to contact Branch Admin. Only Branch Admin can proceed with release to an unregistered person, and only with a governance log entry. There is no other override path.

**SR-02 — Minimum one active pickup list entry at all times.**
Every enrolled student must have at least one active authorised pickup list entry at all times from enrollment. The system blocks: enrollment without a pickup list entry; deactivation of the last active entry. Branch Admin must add a new entry before the existing last entry can be deactivated.

**SR-03 — Pickup list change notification is mandatory and bypasses opt-out.**
Every addition and deactivation of an authorised pickup list entry triggers a mandatory safety notification to the parent. This notification bypasses all parent notification preferences unconditionally. Parents must always be informed of changes to who is authorised to collect their child.

**SR-04 — Allergy protocol compliance creates an immutable audit trail.**
When a Teacher logs a Meal entry for a student with active allergy alerts, the system surfaces the allergy warning inline and blocks the save until the Teacher explicitly confirms "allergy protocol was followed." This confirmation is logged with actor and timestamp. The audit trail is permanent and visible to Branch Admin and Admin only.

**SR-05 — Welfare concern pathway is a timed, named obligation.**
Teachers can flag welfare concerns with a mandatory observation description. Branch Admin receives an immediate notification and must review within 3 school days. One reminder fires if unreviewed. The record is permanent, immutable, and never visible to parents.

**SR-06 — Incident reports separate the publish decision from the record.**
Every significant incident is formally recorded regardless of the publish decision. Branch Admin decides whether to publish to the parent portal or keep internal. Internal notes and staff witness details are never shown to parents under any circumstances — even in published reports. The publish/keep-internal decision is itself permanently logged.

**SR-07 — Welfare concern visibility is deliberately peer-isolated.**
Teachers see only their own welfare concern flags. Teachers cannot see flags raised by other Teachers. This is intentional — preventing peer dynamics from discouraging flagging.

**SR-08 — Toilet entry detail is never shown to parents.**
Toilet entries in the daily activity log are shown to parents as "Toilet visit logged" — frequency only. No detail about toilet activity is ever surfaced to parents under any circumstances.

**SR-09 — Allergy and medication alert flags are distinct from full medical records.**
Allergy and critical medication alert flags are accessible to Coordinator (read-only) and Teacher (own class, read-only) for operational safety during the school day. Full medical records — including history, vaccination records, and Aadhaar — are restricted to Branch Admin and Admin only, access-logged on every read.

---

## 6. Financial Requirements

**FR-01 — Opening balance window is irreversible once closed.**
Once the first invoice is generated for a student, the opening balance window closes permanently. There is no correction path. The window remains open during Migration Mode regardless of import or invoice activity.

**FR-02 — Fee structure must be configured before enrollment.**
Enrollment completion snapshots the fee structure. A fee structure must exist for the student's programme before enrollment can complete.

**FR-03 — Day Care billing is derived from gate timestamps, not estimates.**
Day Care billing units are computed from CHECK_OUT − CHECK_IN timestamps. Full day (≥4 hours) = 1.0 unit; half day (<4 hours) = 0.5 unit. On unscanned checkout days, the system defaults to the end of the configurable school day as a fallback — this is an operational fallback, not an accurate billing figure. Branch Admin is alerted to unscanned checkouts at end of day.

**FR-04 — Outstanding dues persist through all student lifecycle transitions.**
Outstanding dues are not cleared by discontinuation, re-admission, year-end rollover, Transfer Certificate issuance, or Bonafide issuance. The record of the debt persists permanently. Outstanding dues at TC or Bonafide issuance are permanently snapshotted and attached to the issuance record.

**FR-05 — No bulk financial acknowledgment anywhere.**
Discontinuation, rollover, TC issuance, Migration Mode exit: each requires per-invoice acknowledgment with an individual recorded reason. There is no mechanism for bulk financial acknowledgment in any workflow.

**FR-06 — Accountant cannot enter opening balances.**
Opening balance entry is a management-level decision reserved for Branch Admin and Admin. Accountant has deep financial transaction access but no authority over the financial starting position.

**FR-07 — Accountant cannot record expense entries.**
Expense recording is a Branch Admin responsibility. Accountant can view the expense log but cannot create expense entries.

**FR-08 — HAPPY_FEET_NATIVE invoice creation is blocked during Migration Mode.**
Invoice generation from any source (Accountant, Branch Admin, Admin) is blocked while a branch is in Migration Mode. This includes HAPPY_FEET_NATIVE invoices and Razorpay payment links.

**FR-09 — CoFee import: every row has a traceable outcome.**
No CoFee import row is silently dropped. Every row is either: matched and written; skipped as a duplicate; or routed to the exceptions queue for manual resolution. Three resolution options are available: link to correct student, mark not applicable, or flag for 7-day follow-up.

**FR-10 — RAZORPAY_COFEE_CONFLICT requires Branch Admin approval.**
When a CoFee import row matches a HAPPY_FEET_NATIVE invoice and the transaction ID matches a Razorpay payment already recorded, voiding the Razorpay payment record requires Branch Admin approval. Accountant cannot void unilaterally. All steps are permanently logged.

**FR-11 — Soft duplicate override requires a minimum one-sentence reason.**
Accountant can override a SOFT_DUPLICATE flag without Branch Admin approval but must enter a reason of at least one sentence. The override is permanently logged and Branch Admin receives an informational in-app notification.

**FR-12 — All financial records are permanent and immutable.**
Invoices, payments, and import records are never deleted. Status changes and corrections are appended, not overwritten. Financial records are retained for a minimum of 7 years.

**FR-13 — Transport billing is fixed per zone.**
A student enrolled in transport is billed the fixed monthly amount for their transport zone, regardless of actual usage. Transport opt-in and opt-out are Branch Admin actions. Changes take effect from the next billing period.

**FR-14 — CoFee is authoritative for payment status in V1.**
Happy Feet shows fee structure on the parent portal — not actual payment status. Parents must check payment status through CoFee directly. This is a V1 scope boundary, not a product philosophy; full parent financial visibility is planned for V2.

**FR-15 — Admission number normalisation is a prerequisite for reliable CoFee import.**
If the admission number format in CoFee differs from Happy Feet's format and normalisation is not configured, every import row will go to the exception queue. Normalisation is a one-time configuration; once saved, it applies automatically to all subsequent imports.

---

## Summary

| Category | Rule Count |
|---|---|
| Explicit Rules | 97 |
| Implicit Rules | 15 |
| Invariants | 18 |
| Compliance Requirements | 8 |
| Safety Requirements | 9 |
| Financial Requirements | 15 |
| **Total** | **162** |

The defining characteristic of this rule set is the concentration of safety-critical invariants around child release at the gate (INV-01, INV-02, SR-01, SR-02, SR-03) and the data immutability requirements that protect both governance accountability and child welfare records (INV-04, INV-08, INV-09, INV-10, INV-13). Every exception path is sanctioned — no rule has an untracked bypass.

---

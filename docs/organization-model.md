<!-- ABOUT
File: ORGANIZATION_MODEL.md
Purpose: Documents who holds authority over what decisions, how that authority escalates, and what each role can and cannot see across Happy Feet's operations.
Scope: 5 staff roles plus Parent (profiles, decision authority matrix, 7 escalation paths, 4 approval chains, information boundary tables, multi-branch authority model, 5 organizational tensions); excludes process workflows and capability definitions.
Dependencies: DOMAIN_OVERVIEW.md, BUSINESS_CAPABILITY_MAP.md
Stage: Stage 3 — Organizational Intelligence
-->

# Organization Model — Happy Feet School ERP

> **About This Document:** This document defines every role in the Happy Feet school — their responsibilities, the decisions they can make, what information they can access, and how authority escalates when a decision exceeds their remit. It covers 5 staff roles plus the Parent role, seven escalation paths, four approval chains, and the information boundaries between roles. Read this if you are designing access control, approval workflows, or role-based features; you can skip it if you are focused purely on business processes or capability scope.

**Stage:** 3 — Organizational Intelligence

---

## 1. Organizational Structure

```
Admin(s) (School Owner)
│
├── Branch A
│   ├── Branch Admin(s) (Principal)
│   │   ├── Coordinator(s) (Front Desk)
│   │   ├── Teacher(s) (Class-scoped)
│   │   └── Accountant(s)
│   └── [Parents — read-only, child-scoped, not staff]
│
└── Branch B (planned)
    └── [Same structure once provisioned]
```

**Important structural notes:**
- A staff member holds exactly one role. No dual roles, no role combinations.
- A staff member can be bound to more than one branch under that same role (multi-branch binding). In any given session, they operate in one branch.
- Branch Admin cannot be bound to multiple branches by themselves — only Admin creates branch bindings.
- The child (student) is the subject of all records and is never a system user.
- Parents are portal users, not staff. Their access is governed by hard business rules and cannot be adjusted by any staff role.

---

## 2. Role Profiles

### Admin

**Who:** School owner or designated super-administrator. Multiple Admin accounts are permitted.

**Scope:** All branches. No boundary within the system.

**Operating modes:**
- **Global view:** Cross-branch summaries — admissions, enrollment, attendance, financials, staff, governance.
- **Branch-specific context:** Admin switches into any branch via the branch switcher and gains full operational authority for that branch, equivalent to Branch Admin authority.

**Unique authorities (no other role can do these):**
- Create and configure new branches
- Create and deactivate branch bindings for any staff member
- Access the cross-branch governance log
- Issue Bonafide certificates when outstanding dues exist
- Correct attendance with no time limit
- Configure RBAC permissions system-wide
- Manage global WhatsApp deeplink templates
- Receive the monthly governance digest email
- Manually activate or delay academic year activation
- Exit Migration Mode (no other role can trigger this)

**Override confirmation rule:** When Admin performs an action normally owned by another role (marking attendance, drafting a progress card, sending a fee communication), the system surfaces a one-click confirmation identifying the action and stating it will be logged as an Admin action. Every confirmed Admin override is recorded in the audit log.

---

### Branch Admin

**Who:** Principal or head of a single branch. One or more per branch. Bound to their assigned branch.

**Scope:** Full authority within one branch. Cannot access other branches.

**Core responsibilities:** The 9-step academic year setup; admissions review and enrollment completion; staff provisioning and deprovisioning for roles below Branch Admin; fee structure and transport configuration; opening balances; progress card review and publication; attendance oversight and correction up to 30 calendar days; year-end rollover; student lifecycle transitions (discontinuation, TC issuance); incident report publish decisions; authorised pickup list management; school notice board; PTM note recording; school inbox management.

**Inherited capability:** Branch Admin inherits the full capability set of Coordinator, Teacher, and Accountant within the branch.

**Template management toggle:** Enabled by default. When disabled by Admin, the Branch Admin can use WhatsApp templates but cannot create or edit them. This is the only per-account permission variation in V1.

---

### Coordinator

**Who:** Admin assistant or front desk person. One or more per branch.

**Scope:** Intake, communication, and gate operations within the branch.

**Can do:** Advance admissions applications; mark student attendance for any class in the branch; mark staff attendance; manage school timetable and calendar; send absence notification deeplinks; send general announcements; view and reply to school inbox messages; record visitor log entries; view the school notice board.

**Extended data access (read-only, access-logged):** Student name, primary contact, emergency contacts, allergy and critical medication alert flags, submitted document list and status (name and status only — not document content), student photo, and active authorised pickup list (for gate verification).

**Hard limits:** Cannot complete an enrollment independently. Cannot access fee data, invoices, payment history. Cannot access progress cards. Cannot access full medical records or Aadhaar data. Cannot create incident reports. Cannot add, edit, or deactivate pickup list entries. Cannot view the communication log.

---

### Teacher

**Who:** Class teacher. Assigned to one or more class slots across one or more classes.

**Scope:** Strictly scoped to assigned class slots (Primary, Assistant, or Temporary). No access to students outside assigned class.

**Class slot model:**
- **Primary slot** — mandatory on every class; responsible for all class-level teacher actions.
- **Assistant slot** — optional; identical permissions to Primary within the class.
- **Temporary slot** — optional; identical permissions; carries a configurable end date. Access lapses automatically when end date passes.

**Can do:** Mark daily attendance for own class; correct attendance within 7 calendar days; draft and submit progress cards; flag students for welfare concerns; create incident reports for own class students; view authorised pickup list for own class students (read-only); create and view PTM notes for own class students; send absence notification deeplinks; log daily activity entries (meals, naps, activities, health checks, notes); view the school notice board.

**Hard limits:** Cannot view fee data. Cannot access students outside assigned class. Cannot see medical records or Aadhaar data. Cannot correct attendance beyond 7 calendar days. Cannot add, edit, or deactivate pickup list entries. Cannot view the communication log. Cannot view the school inbox.

---

### Accountant

**Who:** Finance staff. One or more per branch.

**Scope:** Financial visibility and fee communication within the branch.

**Can do:** Upload CoFee XLSX and import financial data; view the financial dashboard; view Day Care billing summary; generate Day Care billing summary export; create HAPPY_FEET_NATIVE invoices; generate Razorpay payment links; record payments; send fee reminder and payment confirmation deeplinks; view and reply to the Fee Query channel; view student data and attendance for billing context; view the expense log; view the school notice board.

**Hard limits:** Cannot enter opening balances (Branch Admin and Admin only). Cannot edit student data. Cannot access medical records, Aadhaar, or progress cards. Cannot record expense entries. Cannot access the general school inbox. Cannot add or edit pickup list entries.

---

### Parent / Guardian

**Who:** A child's family member. Not a staff member.

**Scope:** Read-only, child-scoped. For parents with multiple enrolled children, each child appears on a separate tab.

**Access trigger:** Portal access is created when Accountant records the first payment — not at enrollment. Parents cannot self-register.

**Can view:** Child profile, attendance history, class timetable (child's class only), fee structure (not payment history or outstanding balances in V1), published progress cards, document submission status, notification inbox, authorised pickup list for their child (read-only), published incident reports (selected fields only — no internal notes), school notice board (branch-scoped, with unread badge), school calendar.

**Can do:** Send messages to the school inbox (text only; maximum 5 per child per day; linked to a specific enrolled child). Submit fee queries through the Fee Query channel.

**Hard limits:** Cannot see invoice history, outstanding balances, or payment records in V1. Cannot see other students' data. Cannot see any staff data. Cannot edit the pickup list. Cannot see welfare concern records. Cannot see PTM notes. Parent access rules cannot be changed by any staff role, including Admin.

---

## 3. Decision Authority Matrix

| Decision | Coordinator | Teacher | Accountant | Branch Admin | Admin |
|---|---|---|---|---|---|
| Create a student application (walk-in) | — | — | — | Yes | Yes |
| Advance an application | Yes | — | — | Yes | Yes |
| Complete enrollment (all four conditions met) | — | — | — | Yes | Yes |
| Waive a document requirement at enrollment | — | — | — | Yes (with logged reason) | Yes |
| Override class capacity at enrollment | — | — | — | Yes (with logged reason) | Yes |
| Add applicant to waitlist | — | — | — | Yes | Yes |
| Enter opening balance | — | — | — | Yes | Yes |
| Enter existing Admission ID for migrated student profile (Migration Mode only) | — | — | — | Yes | Yes |
| Create a HAPPY_FEET_NATIVE invoice | — | — | Yes | Yes | Yes |
| Generate a Razorpay payment link | — | — | Yes | Yes | Yes |
| Record a payment | — | — | Yes | Yes | Yes |
| Upload CoFee XLSX | — | — | Yes | — | Yes |
| Resolve CoFee import exception | — | — | Yes | Yes | Yes |
| Configure fee structure per programme | — | — | — | Yes | Yes |
| Configure transport zone rates | — | — | — | Yes | Yes |
| Mark student attendance | Yes (any class) | Yes (own class) | — | Yes | Yes |
| Correct attendance — same day | — | Yes | — | Yes | Yes |
| Correct attendance — Day 2 to Day 7 | — | Yes (reason required) | — | Yes | Yes |
| Correct attendance — Day 8 to Day 30 | — | — | — | Yes (logged) | Yes |
| Correct attendance — beyond Day 30 | — | — | — | — | Yes (governance log) |
| Mark staff attendance | Yes | — | — | Yes | Yes |
| Flag welfare concern | — | Yes (own class) | — | Yes | Yes |
| Review welfare concern | — | — | — | Yes | Yes |
| Create incident report | — | Yes (own class) | — | Yes | Yes |
| Publish incident report to parent portal | — | — | — | Yes | Yes |
| Manage authorised pickup list | — | — | — | Yes | Yes |
| View pickup list at gate | Yes | Yes (own class) | — | Yes | Yes |
| Draft progress card | — | Yes (own class) | — | Yes | Yes |
| Submit progress card | — | Yes (own class) | — | Yes | Yes |
| Review progress card (as delegate) | — | Yes (if designated) | — | Yes | Yes |
| Publish progress card | — | — | — | Yes | Yes |
| Return progress card to Teacher | — | — | — | Yes | Yes |
| Generate progress card PDF | — | — | — | Yes | Yes |
| Configure academic year and terms | — | — | — | Yes | Yes |
| Configure programmes and milestones | — | — | — | Yes | Yes |
| Enter school calendar / holidays | Yes | — | — | Yes | Yes |
| Configure timetable | Yes | — | — | Yes | Yes |
| Post to school notice board | — | — | — | Yes | Yes |
| Send announcements | Yes | — | — | Yes | Yes |
| Send absence deeplink | Yes | Yes | — | Yes | Yes |
| Send fee reminder deeplink | — | — | Yes | Yes | Yes |
| Send payment confirmation deeplink | — | — | Yes | Yes | Yes |
| Create/edit global WhatsApp templates | — | — | — | — | Yes |
| Create/edit branch WhatsApp templates | — | — | — | Yes (if toggle enabled) | Yes |
| Initiate student discontinuation | — | — | — | Yes | Yes |
| Acknowledge outstanding invoice at discontinuation | — | — | — | Yes | Yes |
| Issue Transfer Certificate | — | — | — | Yes | Yes |
| Issue Bonafide — no outstanding dues | — | — | — | Yes | Yes |
| Issue Bonafide — outstanding dues exist | — | — | — | — | Yes (governance log) |
| Execute year-end rollover | — | — | — | Yes | Yes |
| Flag student for rollover review | — | Yes (own class) | — | Yes | Yes |
| Enter Migration Mode | — | — | — | — | Yes |
| Exit Migration Mode | — | — | — | — | Yes |
| Provision staff accounts (roles below Branch Admin) | — | — | — | Yes | Yes |
| Provision Branch Admin accounts | — | — | — | — | Yes |
| Create multi-branch staff binding | — | — | — | — | Yes |
| Configure RBAC permissions | — | — | — | — | Yes |
| Access audit log | — | — | — | Yes (own branch) | Yes (all branches) |
| Access governance log | — | — | — | — | Yes |
| Access cross-branch dashboard | — | — | — | — | Yes |

---

## 4. Escalation Paths

### 4.1 Attendance Correction Escalation

```
Same day
└── Teacher (no reason required)

Day 2 → Day 7
└── Teacher (one sentence reason required)

Day 8 → Day 30
└── Branch Admin (logged with reason)

Beyond Day 30
└── Admin only (logged with reason; governance log entry)
```

---

### 4.2 Welfare Concern Escalation

```
Teacher observes a concern during the school day
└── Teacher flags welfare concern from attendance or daily journal screen
    └── Free-text observation required (mandatory)
    └── WelfareConcernRecord created (permanent, immutable)
    └── In-app notification fires to Branch Admin immediately

Branch Admin reviews within 3 school days
└── Branch Admin marks REVIEWED or ESCALATED with a note
    └── If ESCALATED → Branch Admin handles externally (no system workflow beyond the record)

If not reviewed within 3 school days
└── System fires a reminder notification to Branch Admin
    └── If still not reviewed → reminder continues; record remains OPEN

Parents are never notified of welfare concern records.
Teacher sees only their own flags. Branch Admin sees all for their branch. Admin sees all.
```

---

### 4.3 Incident Report Escalation

```
Incident occurs during school hours
└── Teacher (own class) or Branch Admin creates a formal incident report
    └── Record created in M3 (permanent)

Branch Admin reviews
└── Decision: Publish to parent portal OR Keep internal
    └── If Publish → In-app notification fires to parent (#7, bypasses opt-out)
    └── If Keep internal → Record exists permanently; parent never sees it

Published incident report — parent view:
    Date, incident type, what happened, action taken (selected fields only)
    Internal notes and staff witness details are never shown to parents under any circumstances.
```

---

### 4.4 Enrollment Escalation

```
Application submitted
└── Coordinator can advance (track documents, follow up) but cannot complete enrollment

Four conditions checked independently:
1. Programme assigned → Branch Admin assigns
2. Documents cleared or each missing one individually waived with reason → Branch Admin
3. Class capacity available, OR Branch Admin records capacity override with reason
4. At least one active authorised pickup list entry exists → Branch Admin enters before enrollment

All four conditions met
└── Branch Admin completes enrollment

If class is full and Branch Admin does not override:
└── Branch Admin adds applicant to per-class waitlist
    └── When a slot opens (discontinuation, transfer, capacity increase)
    └── Branch Admin is shown the next waitlisted applicant and can proceed with enrollment
    └── No automated parent notification — Branch Admin contacts parent directly
```

---

### 4.5 Bonafide Certificate Escalation

```
Student completes programme (PP2 or equivalent)
└── Branch Admin initiates from student profile
    └── System displays outstanding dues as a live snapshot

No outstanding dues:
└── Branch Admin proceeds directly
    └── Issuance confirmed — irreversible
    └── Parent portal access revoked; status: Bonafide Issued

Outstanding dues exist:
└── Branch Admin cannot proceed
└── Admin must proceed with a recorded reason
    └── Governance log entry created
    └── Outstanding dues snapshot permanently attached to the record
    └── Issuance confirmed — irreversible
```

---

### 4.6 Progress Card Return Escalation

```
Teacher drafts progress card (invisible to all until submitted)
└── Teacher submits to Branch Admin review

Branch Admin review:
└── Publish → parent portal; attendance snapshot locked; notification #12
└── Return with note → Branch Admin must explicitly reopen the card
    └── Teacher receives notification that card has been returned
    └── Teacher re-edits and resubmits
    └── Branch Admin reviews again (loop until published)

Delegate Reviewer (if configured for the class):
└── Reviews submitted card; adds a review note visible to Branch Admin only
└── Cannot publish — authority remains exclusively with Branch Admin
```

---

### 4.7 Migration Mode Exit Escalation

```
Admin is ready to take a branch live
└── Admin navigates to Migration Mode checklist
    └── System verifies all six checklist items

Any item incomplete and not acknowledged:
└── System blocks exit
└── Admin must either complete the item or explicitly acknowledge the gap with a logged reason

All items green or acknowledged:
└── System presents confirmation prompt (irreversible warning)
└── Admin confirms
    └── Invoice generation unlocked for the branch
    └── Razorpay payment links become available
    └── All suppressed parent portal activation invites fire simultaneously
    └── Opening balance window closure rule resumes for subsequent events
    └── Exit event permanently recorded in governance log: actor, timestamp, full checklist snapshot

Re-entry is not permitted. This state cannot be reversed by any role.
```

---

## 5. Approval Chains

### 5.1 Staff Onboarding

```
Branch Admin creates staff profile (name, designation, employment type, salary)
→ Mandatory documents attached (Aadhaar, education certificates, agreement)
→ Branch Admin sends portal invite (7-day, resendable) or skips if no system access required
→ Staff member activates account on first login
→ Role and class assignments take effect at next login after assignment
```

**Multi-branch binding (Admin only):**
```
Admin navigates to staff account settings
→ Admin adds branch binding in staff_branch_bindings
→ On next login, staff member sees branch selector and chooses working branch for the session
```

---

### 5.2 Staff Exit

```
Branch Admin initiates exit from staff profile

Gate 1 (Teachers only):
└── Primary slot on all assigned classes must be reassigned before exit can proceed
└── Branch Admin assigns a replacement Primary teacher to each class
└── Once all Primary slots covered → Gate 1 cleared

Gate 2 (all staff):
└── All salary holds must be formally resolved
    └── Hold type 1: First-month salary held — must be formally released
    └── Hold type 2: Degree in custody — must be returned
└── Once all holds resolved → Gate 2 cleared

Both gates cleared:
→ Branch Admin confirms exit
→ Profile becomes read-only
→ System access revoked automatically
```

---

### 5.3 Academic Year Setup (9-Step Sequence)

```
Step 1: Create academic year (start and end dates)
Step 2: Set term dates; set progress card submission window open/close dates per term
Step 3: Configure programme definitions (billing model, milestone set, attendance model)
Step 4: Create classes (name, capacity, teacher slots)
Step 5: Configure transport zones (if offered)
Step 6: Enter school calendar for the year (holidays block attendance system-wide)
Step 7: Configure weekly timetable per class
Step 8: Define milestones per programme
Step 9: Specify required document types per programme
```

Actor: Branch Admin or Admin. Every change to term dates, programmes, or milestones is permanently logged with actor, timestamp, and before/after values.

---

### 5.4 CoFee Import Flow

```
Accountant downloads XLSX report from CoFee
→ Accountant uploads XLSX to Happy Feet (M7)
→ System applies admission number normalisation rule (if configured)
→ Each row matched against student registry by Admission Number (primary) or Phone Number (secondary)

Four match cases:
  Case A: Row matches COFEE_IMPORT invoice → update amount_paid_to_date; no exception
  Case B: Row matches HAPPY_FEET_NATIVE invoice AND transaction_id matches an existing Razorpay payment
         → RAZORPAY_COFEE_CONFLICT exception; Accountant reviews
         → Voiding Razorpay payment record requires Branch Admin approval
  Case C: Row matches HAPPY_FEET_NATIVE invoice AND no matching transaction_id
         → Import as new payment against existing invoice; not an exception
  Case D: No matching invoice in Happy Feet
         → Create COFEE_IMPORT invoice + payment record; normal import

Unmatched rows (no student found):
→ Routed to import exceptions list
→ Accountant resolves: link to correct student, mark not applicable, or flag for 7-day follow-up

Import timestamp updated and visible to Accountant, Branch Admin, and Admin on financial dashboard.
```

---

## 6. Information Boundaries

Enforced at the application layer — every data request is validated against the requesting user's branch binding before any data is returned.

### 6.1 Student Data Access by Role

| Data Category | Admin | Branch Admin | Coordinator | Teacher | Accountant | Parent |
|---|---|---|---|---|---|---|
| Student name, class, programme | Yes | Yes | Yes | Own class | Yes (billing context) | Own child only |
| Primary contact name and phone | Yes | Yes | Yes (read-only, access-logged) | Own class only | Yes (billing context) | Own child only |
| Emergency contacts | Yes | Yes | Yes (read-only, access-logged) | Own class only | No | No |
| Full medical records | Yes (access-logged) | Yes (access-logged) | No | No | No | No |
| Aadhaar number | Yes (access-logged) | Yes (access-logged) | No | No | No | No |
| Allergy and critical medication alerts | Yes | Yes | Yes (read-only) | Own class (read-only) | No | No (alert is internal) |
| Student photo | Yes | Yes | Yes (read-only, access-logged, gate identification) | Own class (read-only, access-logged) | No | Own child only |
| Document list and status | Yes | Yes | Yes (name and status only — not content) | No | No | Own child (status only) |
| Document content | Yes | Yes | No | No | No | No |
| Fee structure (programme) | Yes | Yes | No | No | Yes | Own child (structure only, not payment history) |
| Invoice history and payment records | Yes | Yes | No | No | Yes | No (V1) |
| Outstanding balances | Yes | Yes | No | No | Yes | No (V1) |
| Progress cards (draft) | No | No | No | Own class (own drafts only) | No | No |
| Progress cards (submitted, unpublished) | No | Yes | No | No | No | No |
| Progress cards (published) | Yes | Yes | No | No | No | Own child only |
| Authorised pickup list | Yes | Yes | Yes (read-only, gate verification) | Own class (read-only) | No | Own child (read-only) |
| Welfare concern records | Yes | Yes (own branch) | No | Own flags only | No | No |
| Incident reports (internal) | Yes | Yes | No | Can create for own class; cannot see others | No | No |
| Incident reports (published) | Yes | Yes | No | Can create for own class | No | Own child (selected fields only) |
| PTM notes | Yes | Yes | No | Own class only | No | No |
| Daily journal entries | Yes | Yes | Yes (read-only) | Own class | No | Own child (read-only, 30-day feed) |
| Audit log (own branch) | Yes | Yes | No | No | No | No |
| Governance log | Yes | No | No | No | No | No |

---

### 6.2 Staff Data Access by Role

| Data Category | Admin | Branch Admin | Coordinator | Teacher | Accountant |
|---|---|---|---|---|---|
| Staff name, role, designation | Yes | Yes | No | No | No |
| Staff contact and employment records | Yes | Yes | No | No | No |
| Salary history | Yes | Yes | No | No | No |
| Staff leave records | Yes | Yes (own branch) | No | No | No |
| Staff certification records | Yes | Yes | No | No | No |
| Staff Aadhaar | Yes (access-logged) | Yes (access-logged) | No | No | No |
| Daily staff attendance | Yes | Yes | Yes (can mark; cannot view history) | No | No |
| Staff exit records | Yes | Yes | No | No | No |
| Visitor log | Yes | Yes | Yes (can create and view) | No | No |

---

### 6.3 Communication Access by Role

| Channel | Admin | Branch Admin | Coordinator | Teacher | Accountant | Parent |
|---|---|---|---|---|---|---|
| School inbox (view and reply) | Yes | Yes (own branch) | Yes (own branch) | No | No | Can initiate; can reply |
| Fee Query channel (view and reply) | Yes | Yes | No | No | Yes (designated first responder) | Can initiate |
| Communication log (full, own branch) | Yes | Yes | No | No | No | No |
| Fee communication log | Yes | Yes | No | No | Yes (fee-related only) | No |
| School notice board (view) | Yes | Yes | Yes | Yes | Yes | Yes (own branch) |
| School notice board (post/archive) | Yes | Yes | No | No | No | No |
| WhatsApp deeplinks — absence | Yes | Yes | Yes | Yes | No | No |
| WhatsApp deeplinks — fee reminder | Yes | Yes | No | No | Yes | No |
| WhatsApp deeplinks — payment confirmation | Yes | Yes | No | No | Yes | No |
| WhatsApp deeplinks — incident notification | Yes | Yes | No | No | No | No |
| Global template management | Yes | No | No | No | No | No |
| Branch template management | Yes | Yes (if toggle enabled) | No | No | No | No |
| Announcements (send) | Yes | Yes | Yes | No | No | No |
| System notifications (receive) | Yes (staff alerts) | Yes (branch alerts) | No | Yes (class alerts) | Yes (financial alerts) | Yes (child alerts) |

---

### 6.4 Financial Data Boundaries — Critical Distinctions

| Boundary | Branch Admin | Accountant | Admin |
|---|---|---|---|
| Enter opening balances | Yes | **No** | Yes |
| Configure fee structures | Yes | No | Yes |
| Upload CoFee XLSX import | No | **Yes** | Yes |
| Create HAPPY_FEET_NATIVE invoices | Yes | Yes | Yes |
| Generate Razorpay payment links | Yes | Yes | Yes |
| Record payments | Yes | Yes | Yes |
| Record expense entries | Yes | **No (view only)** | Yes |
| View Day Care billing summary | Yes | Yes | Yes |
| Access budget overview | V2 only | V2 only | V2 only |

**Key:** Accountant cannot enter opening balances (financial position decision — Branch Admin authority) and cannot record expense entries (branch management accountability — Branch Admin authority).

---

## 7. Multi-Branch Authority Model

Happy Feet supports two branches. The authority model across branches is governed by three principles:

**Principle 1 — Branch isolation is enforced at the data layer.**
Every data request is validated against the requesting user's branch binding before any data is returned. A staff member bound to Branch A cannot see Branch B data even if they navigate directly to a Branch B URL.

**Principle 2 — Admin is the only role with cross-branch authority.**
Admin operates in a global view that aggregates data across all branches. No other role sees cross-branch data. When Admin switches into a branch, they hold full Branch Admin authority for that branch for the duration of that context.

**Principle 3 — Multi-branch binding does not aggregate branches.**
A multi-branch staff member works in one branch per session; logout required to switch. No "all-branches" view exists for non-Admin roles.

| Cross-Branch Capability | Admin | All Other Roles |
|---|---|---|
| Cross-branch enrollment and attendance summary | Yes | No |
| Cross-branch financial dashboard | Yes | No |
| Cross-branch staff roster | Yes | No |
| Cross-branch governance log | Yes | No |
| Create a new branch | Yes | No |
| Assign Branch Admin to a branch | Yes | No |
| Create multi-branch staff binding | Yes | No |
| Switch into a specific branch for full operational access | Yes | No |

---

## 8. Key Organizational Tensions

These are structural tensions built into the role model intentionally. They are not bugs — they reflect deliberate business decisions. Understanding them prevents incorrect assumptions during design.

**Tension 1: Coordinator can advance admissions but cannot complete enrollment.**
Enrollment has legal and financial consequences (fee snapshot, pickup list requirement, capacity override authority) — it is a Branch Admin decision. Coordinator prepares; Branch Admin decides.

**Tension 2: Accountant has the deepest financial access but cannot enter opening balances.**
Opening balances establish the financial baseline — a management-level position decision, not a transaction. Accountant transacts; Branch Admin decides the starting position.

**Tension 3: Teacher can flag welfare concerns but cannot see other teachers' flags.**
Visibility is limited to prevent peer dynamics from discouraging flagging. Each teacher sees only their own flags; Branch Admin sees all.

**Tension 4: Branch Admin can issue Bonafide with no dues; Admin required when dues exist.**
The routine case stays within Branch Admin authority to avoid friction. The exception (dues + issuance) must be attributed to Admin with a governance log entry — the system escalates required authority, not blocks the action.

**Tension 5: Parents cannot see outstanding balances or invoice history in V1.**
This is a V1/V2 scope boundary, not a product philosophy. CoFee owns the payment status conversation in V1; Happy Feet shows fee structure only. Full parent financial visibility is V2.

---

## Summary

The Happy Feet organization model is a five-role hierarchy with a clean separation between management authority (Admin, Branch Admin), operational execution (Coordinator, Teacher, Accountant), and consumer access (Parent). Authority escalates upward as the consequences of a decision become more irreversible — Bonafide issuance with dues requires Admin; attendance correction beyond 30 days requires Admin; Migration Mode exit requires Admin. Every escalation path leaves a governance log entry. The most distinctive organizational characteristic is the strict information boundary around children's sensitive data (medical records, Aadhaar, welfare concerns), which is enforced at the data layer and cannot be adjusted by any role including Admin.

---
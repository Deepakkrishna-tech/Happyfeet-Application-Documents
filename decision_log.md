
## Decision Log

Decisions already made and rationale captured. These are not open for revisitation without a documented change request.

| # | Decision | Options Considered | Decision Made | Rationale |
|---|---|---|---|---|
| D-001 | Admission ID model for migrated students | — | Existing ID becomes the permanent Admission ID | School is going digital for the first time; existing IDs are already the operational source of truth across registers, CoFee, and parent documents. Introducing a new ID creates two IDs in circulation during transition, risking confusion for staff and parents. |
| D-002 | Migration Mode design | — | Reversible entry/exit state per branch | A one-time switch cannot accommodate post-exit corrections, which are operationally inevitable when entering 105 student records manually. Entry/exit with governance log provides auditability without blocking corrections. |
| D-003 | V1 payment engine | — | CoFee as V1 bridge | School already operates with CoFee; rebuilding the payment lifecycle in V1 adds scope and risk without operational benefit. Financial data model is designed so V2 Razorpay integration writes identical records — no schema migration required at V2. |
| D-004 | Multi-branch architecture | — | Multi-branch from day one | A known second branch is already planned. Retrofitting multi-branch later requires schema migration and re-engineering of role-binding logic. Building it correctly once is lower total cost. |
| D-005 | Staff-to-child ratio | — | Folded into Attendance | Ratio is a direct derivative of present-marked attendance; it has no independent data source. A standalone module would duplicate attendance state with no additional value. |
| D-006 | Meal management activation model | (1) Include as annual Pre-Year Setup step; (2) Branch-level toggle in M2 School Configuration, outside the annual sequence | Option 2 — branch-level toggle, activated once when the branch begins offering meals | Meal management has no admission dependency in V1 — the school does not currently offer meals and parents do not select a meal plan at admission. Including it in the annual setup sequence would force every branch to interact with a feature they may never activate. Consistent with the transport skip-if-not-offered pattern but simpler — no annual reconfiguration required. |
| D-007 | Fee payment model for employer or sponsor-funded students | (1) Create a separate Corporate Sponsored student category with its own fee structure; (2) Payer routing model — same fee structure, payer identity recorded as a student-level attribute at admission | Option 2 — payer routing model | Corporate sponsorship is a billing route, not a fee variant. The fee charged is identical to the Standard category; only who pays differs. Creating a separate fee category for this case conflates fee pricing with payment responsibility, complicating fee structure configuration without operational benefit. Primary payer defaults to parent/guardian; secondary payer (employer, trust, sponsor) is optional and added at admission. |
| D-008 | Teacher profile creation and class slot assignment dependency | (1) Create teacher profiles as part of annual Pre-Year Setup before class creation; (2) Create teacher profiles during Migration Mode as Phase 0 Master Data Setup — prerequisite to the annual sequence | Option 2 — Phase 0 inside Migration Mode | Teacher profiles must exist before classes can be created with teacher slot assignments (Pre-Year Setup Step 4). Creating them inside the annual sequence creates a circular dependency — you cannot complete setup without staff, but staff onboarding is not an annual event. Phase 0 inside Migration Mode resolves this cleanly: master data is entered once at go-live, the annual sequence runs against an already-populated staff registry every year thereafter. Mid-year staff changes (new hires, departures) are operational M4 actions, not setup steps. |

---

## Resolved Questions

Questions raised during discovery that have been answered and closed. These are inputs to the build — treat each finding as a confirmed constraint.

---

### QUESTION-008 — Migration Mode re-entry limit

**Question:** When a Branch Admin re-enters Migration Mode after exit (for corrections), is there a limit on how many times this is permitted, or is re-entry unrestricted with a governance log entry sufficient?

**Finding:** Re-entry is unrestricted. A governance log entry recording the reason is sufficient control. No cap on re-entry attempts is imposed.

**Impact on build:** The Migration Mode entry/exit workflow must support multiple re-entries without blocking. Each re-entry writes a permanent governance log entry (reason required). The system must not prevent re-entry after the first exit.

---

### QUESTION-009 — Fee concession and sibling discount model

**Question:** Does the school currently use a structured fee concession or sibling discount model that must be reflected in fee structures, or are all concessions handled ad hoc?

**Finding:** All concessions are handled ad hoc. There is no structured concession or sibling discount model in current use.

**Impact on build:** The fee management module does not need to model a discount or concession rule engine for V1. Ad-hoc adjustments are handled through the existing student category mechanism (Standard, Staff Child, Subsidised) or as direct fee modifications. No additional concession logic required.

---

### QUESTION-010 — Lesson plan authorship and parent visibility

**Question:** Are lesson plans authored per teacher, per class, or per programme? And is parent visibility of lesson plans opt-in or on by default?

**Finding:** Lesson plans are authored per class and per programme. The timetable is configurable by Admin or Branch Admin at the application level — a programme may have the same timetable applied to all its classes, or different timetables per class within a programme. Both configurations are valid and must be supported.

Parent visibility of lesson plans is configurable at the application level (Admin or Branch Admin controls the setting). It is not hardcoded as opt-in or on by default.

**Impact on build:** The Curriculum & Lesson Planning module must support:
- Lesson plans scoped to a specific class within a programme (not teacher-owned)
- A shared timetable that can be applied to multiple classes under the same programme
- A per-class override that allows a different timetable when the programme-level default is not appropriate
- An application-level toggle (Admin / Branch Admin) for parent visibility of lesson plans

---

### QUESTION-011 — Missing 9th step in Pre-Year Setup

**Question:** The Pre-Year Setup is described as a 9-step configuration sequence but only 8 steps are enumerated (year/term dates, programmes, classes, transport zones, calendar, timetable, milestones, document types). What is the missing 9th step?

**Finding:** There is no missing step. The count of 9 is correct. The summarised entry "year/term dates" collapsed two distinct configuration steps into one label.

**The actual 9 steps:**

| # | Step | How it was summarised |
|---|---|---|
| 1 | Create the academic year — start and end dates | "year/term dates" (Step 1) |
| 2 | Set term dates per term and configure progress card submission windows | "year/term dates" (Step 2 — distinct step) |
| 3 | Configure programme definitions (billing model, attendance model, milestone set) | programmes |
| 4 | Create classes — name, capacity, teacher slots | classes |
| 5 | Configure transport zones (skip if transport not offered) | transport zones |
| 6 | Enter the school calendar (holidays, events) | calendar |
| 7 | Configure weekly timetable per class | timetable |
| 8 | Define developmental milestones per programme | milestones |
| 9 | Specify required document types per programme | document types |

**Impact on build:** The Pre-Year Setup onboarding workflow must implement all 9 steps as a sequential configuration gate. Steps 1 and 2 are separate screens or sub-steps — they must not be merged into a single "dates" entry. Steps must be completed in order; a later step that depends on an earlier one (e.g. Step 3 requires Step 1 to define the active year) must be blocked until its prerequisite is complete.

**Document change:** §6 Pre-Year Setup row in DOMAIN_OVERVIEW.md updated to enumerate all 9 steps explicitly, with Steps 1 and 2 listed separately. Transport zones step restored to include "(skip if not offered)" which had been dropped in a prior edit.

---

### QUESTION-012 — Four enrollment conditions

**Question:** The Admissions and enrollment module references 'four defined enrollment conditions' as established terminology. What are the four conditions under which a student moves from application to enrolled status?

**Finding:** All four conditions must be independently satisfied before enrollment can complete. Partial satisfaction does not unlock enrollment.

| # | Condition | Notes |
|---|---|---|
| 1 | Programme assigned | Set at the Applied → Approved transition; prerequisite for conditions 2–4 to be checkable |
| 2 | Documents cleared or individually waived | Every missing document requires its own waiver with a recorded reason; blanket waiving is not permitted |
| 3 | Class capacity available, or override recorded with reason | Capacity check is a gate, not a hard block — override is permitted but must be permanently logged |
| 4 | At least one active authorised pickup list entry recorded | A past entry that has been deactivated does not satisfy this condition |

**Behavioural rules per condition:**

**Condition 1 — Programme assigned**
Programme assignment happens at the Applied → Approved transition (Step 2 of the admissions workflow). Without a programme, neither the document checklist nor the class capacity check has a reference frame. This condition must be satisfied first.

**Condition 2 — Documents**
The required document list is programme-specific (configured during Pre-Year Setup Step 9). Each document waiver is individual and permanently logged — no bulk waiver path exists. A programme with an empty required-document list satisfies this condition trivially.

**Condition 3 — Class capacity**
Capacity is defined per class at setup. An over-capacity enrollment is allowed only if Branch Admin records a reason at the time of override. The override is permanently logged in the governance log.

**Condition 4 — Authorised pickup list**
Must have at least one active entry at the time of enrollment. This condition persists: the list must remain non-empty while the student is enrolled. A guardian record is distinct from the pickup list — linking a guardian does not automatically satisfy this condition.

**Naming note for build:** The source treats these as an inline numbered list without canonical slugs. For engineering specs, UI error states, and test cases, the following short names are recommended:
- Condition 1 → **Programme Gate**
- Condition 2 → **Document Gate**
- Condition 3 → **Capacity Gate**
- Condition 4 → **Pickup Gate**

The four gates are checked independently but follow a natural processing order (Programme → Documents → Capacity → Pickup). The UI may present them as a sequential checklist or a simultaneous four-way readiness panel — this is a build decision not yet made.
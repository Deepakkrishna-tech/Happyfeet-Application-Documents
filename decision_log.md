
# Happy Feet School ERP — Decision Log & Build Specification

**Authoritative build sources:** `domain-overview.md` v1.2 (narrative source of truth) + this document (decisions, rules, enumerations, state machines, invariants, and the CoFee parser reference). On any conflict, `domain-overview.md` wins.

**Document map:** Decision table (D-001…D-033) → Resolved Questions (Q-008…Q-012) → Architecture Review detail → Governance/Audit/Notification detail (D-027…D-033) → Open Questions (Q-013…Q-016) → CoFee Data Dictionary → Build Specification Register (ENUM/Authority/SM/INV/Boundaries/Index). The pillar index below re-organizes all of it by **domain and business operation**, ordered as a 0→1 build journey.

---

## System Pillars — 0→1 Index

The system decomposes into **four tiers** built in sequence: lay the **Foundation**, run **Operations**, stand both on a **Trust & Insight spine**, all delivered on a defined **Architecture**. Each pillar below links the decisions (`D`), enumerations (`ENUM`), state machines (`SM`), invariants (`INV`), and open questions (`Q`) that own it. New developers/agents should read in pillar order — it mirrors how a school goes live and then operates.

### TIER A — Foundation (the "0": from paper to a configured, populated system)

**P0 · Migration & Go-Live** — _getting a paper-run school's data into the system, once, safely._
D-002, D-008, D-001 (migrated IDs), D-025/D-033 (historical CoFee import) · SM-06 · ENUM-16 · Q-008 · Boundary: paper-import permanently out of scope (staff are the migration engine).

**P1 · Identity, Access & Multi-Branch** — _who the actors are, what they may do, and strict branch isolation._
D-004 · ENUM-01, ENUM-02 · INV-11 · Authority Matrix (§2 of the Register).

**P2 · School Configuration & Time** — _the academic framework every other module derives from: year, terms, programmes, classes, calendar._
D-006, D-009 · ENUM-03, ENUM-07, ENUM-08 · SM-01 · INV-06, INV-07, INV-08, INV-14, INV-15 · Q-011 (9-step Pre-Year Setup).

### TIER B — Operations (the running school)

**P3 · Admissions & Enrollment** — _the intake funnel and the four gates that complete enrollment._
D-015 (admissions track) · ENUM-10 · SM-02 (admissions) · Q-012 (Programme/Document/Capacity/Pickup gates).

**P4 · Student Lifecycle & Records** — _the central entity: permanent records, status, rollover, certificates._
D-001, D-015 (enrolled track) · ENUM-11 · SM-02 (enrolled) · INV-05.

**P5 · Finance & Fee Management** — _the money engine: one canonical ledger, CoFee as a temporary source, invoices, payments, self-serve._
D-003/D-019/D-032 (CoFee→own-ledger), D-007, D-010, D-016, D-023, D-025, D-033 · ENUM-04, ENUM-05, ENUM-06, ENUM-07, ENUM-12, ENUM-17 · SM-03 · INV-09, INV-13, INV-17, INV-19 · Q-009, Q-014, Q-015, Q-016 · **CoFee → HF Ledger Data Dictionary**.

**P6 · Daily Operations: Attendance, Care & Transport** — _the everyday: marking, daily-care feed, day care, school-fleet boarding._
D-005, D-014, D-021, D-024, D-022 (offline), D-011 (attendance compensating events) · ENUM-09 · INV-08.

**P7 · Child Safety & Data Protection** — _the non-negotiable: pickup, allergy, welfare, incidents, sensitive data._
D-013 (pickup hybrid), D-017 (welfare) · ENUM-18, ENUM-19 · SM-04 · INV-01, INV-02, INV-03, INV-18, INV-20 · Q-013 (safety-record retention).

**P8 · Academics: Curriculum & Progress** — _lesson plans and progress cards within term windows._
Q-010 (authorship & parent visibility) · ENUM-08 (term windows) · (capability modules M9/M10).

**P9 · Communication & Notifications** — _parent engagement, and the guarantee that safety messages actually land._
D-027, D-017 (SMS delivery/fallback) · ENUM-14, ENUM-15 · SM-05 · INV-16.

### TIER C — Trust & Insight (the spine and the view)

**P10 · Governance, Audit & Trust** — _one tamper-evident audit spine; immutability; retention; the sanctioned-exception path._
D-011, D-012, D-028, D-029, D-030, D-031 · ENUM-20 · INV-04, INV-10, INV-12 · Q-013.

**P11 · Reporting & Insight** — _owner visibility: the four numbers a preschool lives on._
D-026 · Q-016 (gross vs net) · derives from P5 ledger + live data.

### TIER D — Architecture & Delivery Posture (how it's built)

**PX · Cross-Cutting Architecture** — _modular monolith, bounded contexts, offline-first, complexity matched to operating maturity._
D-018, D-020, D-021 (context placement), D-022 · Bounded-Context Map · Register §6 (Cross-Cutting Build Constraints).

---

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
| D-009 | Academic year activation model | (1) Automatic midnight activation on configured start date with manual Admin override as fallback; (2) Three-state lifecycle (Upcoming → Active → Closed) with Admin-triggered transitions and system prompt on configured start date | Option 2 — three-state lifecycle, Admin-triggered | Automatic midnight activation is operationally fragile: if Pre-Year Setup is incomplete at activation time, an incomplete year goes live with no human check. It also blocks a real-world edge case — a student enrolling for the new year before the current year ends — because only one year can accept admissions under a binary active/inactive model. The three-state model resolves both: Upcoming allows advance admissions and setup completion before go-live; Active is a deliberate admin decision, not a clock event; Closed is confirmed after rollover, not assumed. Every transition is logged. The system prompts Admin on the configured start date but does not act without confirmation. |
| D-010 | V1 canonical financial source of truth | (1) Invoice status owned by whichever payment source writes last; (2) Happy Feet is the single ledger of record, CoFee/Razorpay/manual are tagged sources | Option 2 — single-ledger pattern | Three V1 payment channels (CoFee import, Razorpay links, directly-issued HF invoices) with no canonical owner would be resolved silently by the first developer, corrupting dues-gated workflows (TC, Bonafide, rollover). HF records every money event with `source` + `external_reference_id` (idempotency key); invoice status is **derived** from applied payments, never a hand-toggled flag; unmatched payments go to a suspense/clearing queue, never silently dropped. Matches double-entry ledger practice (Stripe Ledger, Modern Treasury) and makes the D-003 "V2 writes identical records" promise literally true. |
| D-011 | Immutability vs. calendar auto-resolution | (1) Mutate affected attendance when the calendar changes; (2) Append a system compensating (void/supersede) event | Option 2 — compensating events | §9.1 immutability and §9.8 calendar auto-resolution conflict on retroactive holidays — mutating breaks the audit spine, ignoring breaks the calendar. "Auto-resolve" is defined as appending a system-attributed void event referencing the original immutable mark; current state is **derived** (latest event wins). Matches event sourcing and EHR amendment models — append a correction, never erase. |
| D-012 | Logging taxonomy | (1) Three independent log stores; (2) One append-only event store with three typed projections (audit / governance / compliance) | Option 2 — single write path, three views | Three named logs (audit log, governance log, compliance audit entry) with no reconciliation would let each module invent its own mapping, producing duplicate or missing compliance evidence. One canonical append-only store; governance and compliance are **filtered views**; ship a `trigger → event-type → which-views → visibility` matrix. Matches CQRS single-write/multi-read and the standard technical-audit vs. business-exception split (e.g. CloudTrail vs. break-glass log). |
| D-013 | Authorised pickup verification model | (1) Static photo-ID list + manual gate verification as the sole mechanism; (2) Hybrid — standing list for regulars (PIN/QR) + parent-issued one-time code for irregular pickups + Branch-Admin override | Option 2 — hybrid, parent-issued one-time codes | A static list alone is impractical at peak pickup (~105 children in a ~30-min window), maintenance-heavy for rotating Indian-context pickups (drivers, domestic help, relatives), and degrades the two-actor override into a daily-used path that floods the governance log. Hybrid keeps a small stable standing list (still satisfies QUESTION-012 Condition 4) and adds parent-issued one-time codes for one-off pickups — fast, secure (a parent-issued token, not a guard's visual guess), low-maintenance. Pickup override is **added to the §9.2 governance-trigger list** and writes both a governance and a compliance entry. Matches Brightwheel/Procare PIN/QR pickup. See detailed analysis below. |
| D-014 | Concurrent attendance writes by co-slotted teachers | (1) Last-write-wins; (2) Single authoritative marker (Primary slot) + optimistic locking, post-mark changes appended as amendments | Option 2 | Primary/Assistant/Temporary slots carry identical permissions, so co-marking races could fire or suppress the absence SMS wrongly. Primary slot is marker of record; a second writer gets "already marked by X — amend?"; amendments are appended; **absence SMS fires once, only on the verified/locked transition**. Matches the single-roster-owner pattern (Brightwheel/Procare). |
| D-015 | Student lifecycle status enumeration | — | Explicit status sets with named terminal states | Terminal states (Discontinued, Transferred, Completed) were implied, not enumerated; lifecycle/fees/rollover all branch on these enums. **Admissions context:** Enquiry → Applied → Under Review → Waitlisted → Offered → Accepted (handoff at enrollment) \| Rejected \| Application-Withdrawn \| Offer-Expired. **Student context:** Active → Promoted/Rolled-over \| Completed Programme *(terminal)* \| Discontinued *(terminal)* \| Transferred *(terminal)*. Re-admission links to the historical record (preserves Admission ID per D-001). Terminal students cannot be rolled over; parent portal becomes read-only archive for the retention period. Matches SIS enrollment enums (PowerSchool). |
| D-016 | Partial-payment / installment modelling | — | First-class invoice states + payment schedule | §9.2 modelled only binary paid/unpaid; installments imply partial states. Invoice carries `total` / `amount_applied` / `amount_due`; status ∈ {Draft, Issued, PartiallyPaid, Paid, Overdue, Void, Credited} — derived. Installments are a dated payment schedule with per-item state. Dues-gated workflows read `amount_due` (and overdue-installment count), never a binary flag. Matches Stripe/Razorpay invoicing. |
| D-017 | Welfare-concern and SMS terminal/failure states | — | Explicit lifecycle + delivery states with fallback | Welfare concern had no terminal state; absence SMS had no failure handling. **Welfare:** Raised → Acknowledged → Under Review → Resolved \| Escalated \| Closed-no-action (reason required); 3-school-day SLA with reminder then Admin escalation; never parent-visible. **SMS:** Queued → Sent → Delivered \| Failed \| Expired via MSG91 delivery receipts; on Failed, retry with backoff then fall back to in-app feed entry + staff alert; never display "sent" when only queued. |
| D-018 | V1 complexity posture | (1) Expose governance/audit machinery as user-facing workflow; (2) Governance/immutability as invisible infrastructure, progressive disclosure, adoption-led rollout | Option 2 | Enterprise-grade machinery (governance log, event-sourced immutability, DPDP) risks exceeding the operating maturity of a paper-based 105-student preschool. The spine stays in the data model, but front-line V1 surfaces are the parent feed, attendance, fees, and communication; governance/audit/migration controls are Admin-only back office. Ship with templates/defaults; roll out parent app + attendance first. Matches Brightwheel's adoption strategy (lead with the parent feed, hide admin complexity). |
| D-019 | CoFee build-vs-buy posture (refines D-003) | (1) V2 builds a full native billing engine; (2) V2 owns the ledger/AR and integrates a processor (Razorpay/CoFee) behind an adapter | Option 2 — own the ledger, integrate the processor | "Make CoFee redundant" was an undefended foregone conclusion, and building tax/proration/refund rails is high-defect. HF owns invoices/ledger; the gateway is a processor. CoFee retirement is gated on a real trigger (reconciliation cost or on-time-collection uplift), recorded when met. The D-010 single ledger makes the eventual switch a non-event. |
| D-020 | Module architecture model | (1) Treat the 16 modules as 16 data-owning boundaries (and fold Admissions into Student Records); (2) Keep 16 as a capability catalog, add a ~12-context bounded-context map + Migration as cross-cutting, build as a modular monolith | Option 2 | The 16-module list mixes three abstraction levels — capability, bounded context, and system state. Folding Admissions into Student was **rejected**: Admissions is a distinct context owning the Application/Waitlist aggregate, and folding it pollutes the permanent immutable student store (D-001/§9.1); PowerSchool and Brightwheel both ship admissions as a separate product. Keep M1–M16 numbering (referenced across the doc set), add a 12-context map for data ownership; a modular monolith removes the "reaching into another module's data" risk the consolidation argument feared. See map below. |
| D-021 | Transport module status | (1) Dissolve M12 into M2 (route config) + M3/M5 (assignment) + M7 (billing); (2) Standalone Transport context | Option 2 — standalone | The school operates its own fleet ~90% of the time, so Transport owns persistent entities (vehicles, drivers, routes, stops, zones, compliance docs) and ongoing operations — clearing the data-ownership test that earlier made it conditional. Third-party vehicles become a vehicle-type flag. Route *definitions* live in M2; *billing* in M7; *operations* in M12. |
| D-022 | Offline-first attendance & daily-log | — | Cross-cutting architectural constraint on M8/M15 | Indian preschool connectivity makes offline capture non-negotiable and the single biggest adoption blocker; retrofitting after a server-first build is a near-rewrite. Pattern: local write → queue → sync on reconnect; conflicts resolved via D-014 optimistic locking; records show a `pending-sync` state. Stated as a design constraint at the same tier as "single source of time" (§9.8). |
| D-023 | Self-serve fee status + one-tap payment (ROI) | — | In V1 — parent portal (M1) + M7 | Shows current dues from the D-010 ledger with a Razorpay one-tap payment link. Reduces accountant reconciliation load and improves on-time payment. High-value, low-complexity parent-facing feature. |
| D-024 | Transport boarding/drop confirmation (ROI) | (1) Live GPS tracking; (2) Staff-marked boarding/drop confirmation, no GPS | Option 2 in V1; live GPS deferred | Boarding/drop confirmation is an attendance-style staff-marked event needing no GPS, delivering most of the safety/trust value (child got on/off safely). Live GPS tracking is not currently available and is deferred to a later version. |
| D-025 | CoFee integration discipline | — | Idempotent, reconciled, provenance-tracked, recurring import | CoFee stays as the collection mechanism and a **live recurring source**; HF is the ledger (D-010), so active payers are undisrupted. Imports are idempotent (keyed on CoFee txn/invoice ID); every row maps to an HF invoice or to the suspense queue; each import records provenance (when/who/row counts). CoFee is a recurring feed, **not** a one-time migration. Historical CoFee data is imported with provenance during Phase 0 (not re-keyed). Dashboards and dues-gated actions display "fee data as of [last import]"; gated actions (TC/Bonafide/rollover) require a fresh import or explicit Accountant confirmation. **Pre-build check:** confirm CoFee XLSX schema stability + import cadence. |
| D-026 | Reporting & dashboard model | — | Role-filtered dashboards derived from the canonical ledger and derived states | Owner KPIs: collection rate, dues aging (0–30 / 31–60 / 60+), occupancy vs. capacity, admissions funnel/conversion, re-enrollment & discontinuation, open welfare concerns/incidents past SLA, DPDP consent & document completeness. Branch Admin / Teacher / Accountant / Parent receive scoped subsets. Operational dashboards on-demand; governance digests scheduled (§6). Every widget carries an "as of" timestamp; financial widgets show "fee data as of [last CoFee import]". Computationally trivial at 105 students — the value is aggregated visibility replacing verbal updates. |
| D-027 | Notification delivery-state model & reach guarantee | (1) Fire-and-forget dispatch, with opt-out bypass treated as "guaranteed reach"; (2) Per-channel delivery lifecycle + multi-channel redundancy + human-fallback escalation for the safety-critical class | Option 2 | §6/§9 imply safety notifications "always reach parents," but opt-out bypass only guarantees **dispatch, not delivery** — a wrong number or a downed SMS gateway silently breaks the invariant. Every notification gets a delivery lifecycle (Queued → Sent → Delivered → Read \| Failed \| Expired) with provider receipts (MSG91 DLR, email bounce/webhook, in-app read receipt); WhatsApp deeplinks log only "Generated" (manual, unconfirmable). Notifications carry a priority class — **Standard** vs **Safety-critical** (portal invite, enrollment, Bonafide, incident publication, pickup-list change, absence). Safety-critical are sent multi-channel (in-app + SMS), delivery-tracked, and are **not "complete" until delivered/acknowledged or escalated to a staff action item** — reframing the unenforceable "always reach" invariant into an enforceable "tracked-and-escalated-until-confirmed" one. Separates two conflated concepts: opt-out bypass (a preference rule) vs delivery guarantee (a reliability mechanism). Matches healthcare critical-result alerting and emergency-notification escalation (PagerDuty/Everbridge-style). Extends D-017's SMS-only fallback to all channels. |
| D-028 | Log taxonomy made authoritative (promotes D-012) | — | Audit log = canonical spine; governance & compliance logs = filtered projections | Promotes the correct pattern into the authoritative set, reconciling §9.1/§9.2/§7.1. **Audit Log** = single append-only spine of every write across every module and role incl. Admin; schema: UTC timestamp, actor (id/name/role), branch, module, action type, target entity (type+id), before-state, after-state, reason (where required), correlation id; access Admin (all branches) + Branch Admin (own branch). **Governance Log** = a *filtered view* where action ∈ the override-class set, Admin-only — **a curation / cross-branch view, not an access boundary** (the same override actions already appear in the Branch Admin's own-branch audit log; state this so no one assumes redaction). **Compliance entries** (allergy confirmation, pickup override, consent capture, sensitive-doc reads) = audit entries tagged with a compliance category, surfaced via a compliance view — not a separate store. One `trigger → event-type → views → visibility` matrix governs all. CQRS single-write/multi-read. |
| D-029 | Access logging for sensitive-data reads (distinct 4th log) | — | Dedicated read/disclosure log, schema-distinct from the audit log | The audit-log schema is write-shaped (before/after state); reads have no before/after, so sensitive reads cannot fit it. §7.4 specifies access *control* but no access *log*. A dedicated **Access Log** records every read/export/print of a defined sensitive category (Aadhaar, medical/vaccination, PEN/APAAR, passport photo, financial): UTC timestamp, actor (id/role), branch, data category, subject id, access type (view/export/print), purpose/context where feasible, correlation id — no before/after. Immutable, append-only, retained with the data it protects. Required for DPDP "who accessed my child's data and why"; mirrors HIPAA accounting-of-disclosures and GDPR Art. 30. Generalises the existing export access-logging to all sensitive reads. Implemented as a distinct event type within the same append-only infrastructure (D-028), surfaced via a disclosure view. |
| D-030 | Audit / governance / access log retention | (1) Inherit per-record-type retention only; (2) Explicit log-spine retention = longest downstream obligation, never auto-purged | Option 2 | §9.6 sets retention per record type but not for the log spine; since the governance log is a *view* of the audit log, if audit entries age out the governance history vanishes. The audit/governance/access logs are retained for **at least the longest applicable record retention** — minimum the 7-year financial tier (+ margin → 8y) — and for **safety-tagged entries**, until the child reaches majority plus a claims-limitation tail (default proposal: age 18 + 7y, aligning with minor-claimant limitation patterns and POCSO defensibility). Logs are **never auto-purged**; deletion only via an exceptional, logged Admin governance action. Access-log retention aligns with the underlying data's retention (DPDP). **Open item:** confirm the child-safety record retention period with Indian legal counsel. |
| D-031 | Tamper-evidence for immutable logs | (1) Application-level "no edit/delete" rule only; (2) Hash-chaining + DB-level append-only + external anchor | Option 2 | Immutability (§9.1) is asserted as a *policy*, not enforced by a *mechanism* — a privileged DB/deploy actor can alter rows, too weak for POCSO/DPDP/financial evidentiary value. Each log entry stores `hash(entry_content + previous_hash)` so any retroactive edit/delete breaks the chain and is detectable. Revoke UPDATE/DELETE on log tables at the DB role level (append-only). Emit the latest chain-head hash in the monthly governance digest as an **external witness/anchor**, so even a full-table rewrite is detectable. Converts "immutable by policy" into "immutable by mechanism." Proportionate for V1; defer managed WORM / ledger DB (AWS QLDB, S3 Object Lock) to scale or compliance demand. Matches Certificate Transparency and hash-chained fintech/healthtech audit logs. |
| D-032 | CoFee framing reconciliation (aligns §5/§9.5 with D-010) | (1) Keep §5/§9.5 CoFee-centric ("CoFee owns invoice generation and payment collection"); (2) Single-ledger framing — CoFee is a temporary V1 collection source feeding the HF canonical ledger, fully retired in V2 | Option 2 | Confirmed by product intent: CoFee is temporary; Happy Feet must be stable enough to **replace CoFee completely at V2**. HF is the canonical ledger from V1 (D-010); CoFee is one tagged, temporary source (D-003/D-025); V2 removes CoFee entirely (D-019). Parent fee visibility follows D-023 (self-serve from the HF ledger), **not** "check CoFee directly." **Document-sync action (next session):** reword domain-overview §5 ("CoFee owns invoice generation and payment collection") and §9.5 ("CoFee Is a V1 Bridge") to the single-ledger framing — staged here, applied to domain-overview after verification. |
| D-033 | CoFee XLSX ingestion specification (concretises D-025) | — | Field→ledger mapping + idempotency strategy + reconciliation exception taxonomy | Client shared the actual CoFee export schema (~26 fields). Each row = a payment event against an `Invoice id` / `Fee head` for a student (`Admission number`). Mapped to the HF canonical ledger (D-010): CoFee `Amount paid` + `Transaction Id`/`UTR` are the ledger **event**; CoFee `Status` / `Amount paid till date` / `Remaining amount` are **reconciliation controls, not source of truth** — HF derives its own invoice state (D-016) from the payment events and *asserts* it against CoFee's summary; divergence → suspense ("Ledger Mismatch"). Idempotency key = `Transaction Id` (online) → `UTR` (bank transfer) → **synthesised deterministic key for offline Cash/Cheque that have neither** = `hash(Invoice id + Admission number + Fee head + Amount paid + Paid on)`, because `S.no` is per-file and unstable. Student resolved by `Admission number` (the permanent key, D-001); unresolved → suspense — expected and held during Migration Mode (§9.4 relaxed validation), a hard error in normal ops. The raw schema implies three model elements made explicit here: (a) **collection vs settlement are two events** — gross `Amount paid`/`Paid on` vs net `Total amount settled`/`Settled on` (gateway fees deducted); parent dues use gross, bank reconciliation uses net; (b) **advance/unapplied credit** — `Type=Advance` payments exist as on-account credit before applying to an invoice; (c) **refunds** (`Status=Refunded`) are appended compensating events (D-011), never deletions or reductions. Adopts the client's four validation rules as ingestion gates; non-INR `Currency` and unmappable `Fee head`/`Branch name` → exception. See detailed spec below. |

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

---

## Stage 1 Architecture Review — Pre-Design Resolutions

Decisions D-010 through D-026 were produced by a Stage-1 architect review of `domain-overview.md` against eight design lenses (boundary integrity, state-machine completeness, invariant completeness, actor authority, temporal consistency, scalability, tradeoff visibility, product concept). Each resolution names the design decision a developer would otherwise make in its absence, and is grounded in a named production pattern. The decision table above is the authoritative one-line record; the detail below preserves the recommendation, industry basis, and build impact for the items that need more than a row.

These resolutions map directly onto the recommended pre-design-session priority order:

- **P0** — D-010 (canonical financial ledger), D-011 (immutability vs. calendar)
- **P1** — D-012 (log taxonomy), D-013 (pickup override + model), D-015/D-016 (status & partial-payment enums)
- **P2** — D-014/D-017 (attendance write authority & SMS fallback), D-018 (V1 complexity posture)

---

### Authorised Pickup — Practicality Analysis (basis for D-013)

**Question raised:** Is the static authorised-pickup list practical as the sole gate mechanism?

**Finding:** No — practical for *regular* pickups, impractical as the *sole* mechanism. Three failure modes:

1. **Peak-window throughput** — ~105 children released in a compressed ~30-minute window (3–4/minute at peak). Manual photo-matching against an on-screen list is a bottleneck that creates queues and pressure to skip verification.
2. **Maintenance burden in the Indian context** — pickups are frequently rotating drivers, domestic help, grandparents, and neighbours. Keeping a current photo-ID list for every possible person goes stale fast, making the list either over-permissive or constantly blocking legitimate pickups.
3. **Override becomes the norm** — a stale list pushes the two-actor override into daily use, defeating the security purpose and flooding the governance log.

**Resolution (hybrid model — D-013):**

| Path | Mechanism | Who | Logging |
|---|---|---|---|
| Regular pickup | Standing list entry + persistent PIN/QR + photo (secondary) | Parent / regular driver | Routine release record |
| Irregular / one-off pickup | Parent-issued **one-time code** generated in the app, shared with the pickup person | Anyone the parent authorises for that day | Release record (parent authorisation is the security) |
| Exception (no code, not on list) | Hard block → Branch Admin authorisation + parent phone/OTP confirmation + photo capture | Branch Admin (two-actor) | **Governance + compliance entry** |

**Why it satisfies existing constraints:** the standing list remains non-empty, so QUESTION-012 Condition 4 (Pickup Gate) is unaffected. The one-time code is an *addition* for non-regulars, not a replacement.

**Stakeholder value:** Owner — defensible security without gate chaos, lower staff time, reduced liability. Coordinator — fast, unambiguous flow, no confrontation with parents. Parent — authorise anyone in seconds; assurance that no one can collect their child without a parent-issued code.

**Industry basis:** modern childcare platforms (Brightwheel, Procare) use PIN/QR pickup with one-time authorisation via the parent app; the token-not-visual-guess model mirrors delivery/locker OTP security.

**Build impact:** §7.1 and §9.2 updated so the pickup override is an enumerated governance trigger writing both a governance and a compliance entry. The pickup module must support (a) standing list with persistent PIN/QR, (b) parent-issued time-boxed one-time codes, (c) the two-actor override path.

---

### High-Value ROI Features Confirmed for V1

Features added to V1 scope during this review for high end-user value, grounded in industry practice (cross-referenced to decisions):

| Feature | Decision | Primary value | Owner-level ROI |
|---|---|---|---|
| Offline-first attendance & daily-log capture | D-022 | Teachers mark without connectivity; sync on reconnect | Removes the single biggest adoption blocker — without it, front-line staff abandon the tool |
| Self-serve fee status + one-tap payment link | D-023 | Parents see dues and pay in one tap | Improves on-time collection; cuts accountant reconciliation load |
| Transport boarding/drop confirmation (no GPS) | D-024 | "Child boarded / dropped safely" event for parents | Safety/trust feature on the school-owned fleet; low cost, high parent confidence |
| One-time parent-issued pickup code | D-013 | Authorise any one-off pickup from the app | Converts the scary override into a clean consented path; reduces gate friction |
| Parent daily feed as the hero surface | D-018 | Daily confidence the child is safe, seen, cared for | Drives retention and referrals — the primary acquisition channel for preschools |

**Stakeholder value summary — Owner (the buyer):** the product maps to the four numbers a preschool business lives on — **occupancy** (enrolled vs. capacity), **fee collection** (collection rate + dues aging), **retention** (re-enrollment, low churn, driven by parent trust), and **safety/reputation** (audit trail, pickup enforcement, welfare SLAs). It converts the owner's current blind spot — reliance on verbal staff updates (§2) — into real-time, cross-branch visibility, which is also what justifies "multi-branch from day one" (D-004) as the second branch opens.

---

### Module Architecture — Bounded-Context Map (basis for D-020/D-021)

The M1–M16 capability catalog is retained for stakeholder communication and traceability. For data-ownership boundaries (schemas, aggregates), the system resolves to **12 contexts + 1 cross-cutting state**. Build target: **modular monolith**.

| Context | Capability modules | Notes |
|---|---|---|
| Identity & Access | M1 | |
| School Configuration | M2 | route *definitions* live here; transport *operations* in Transport |
| Student | M3 + M6 | M3 owns the student aggregate; M6 lifecycle transitions on it |
| Admissions | M5 | **separate context** — owns Application/Waitlist; hands off at enrollment (do not fold into Student) |
| Staff | M4 | |
| Finance | M7 | + transport & meal *billing*; canonical ledger of record (D-010) |
| Attendance & Safety | M8 | + allergy confirmation + pickup verification |
| Academic | M9 + M10 | |
| Daily Care | M15 + M11 (logging) | meal *billing* → Finance; allergy *confirmation* → Attendance & Safety |
| **Transport** | **M12** | **standalone — school owns the fleet ~90%** (D-021) |
| Communication | M13 | |
| Reporting | M14 | cross-cutting read layer; dashboards derive from the ledger (D-026) |
| *Migration Mode* | *M16* | **cross-cutting state, not a context** — modulates validation/suppression across M5/M7/M8 |

---

## Governance, Audit & Notification Resolutions (D-027–D-032)

Produced by a focused review of the notification/communication and governance/audit surfaces of `domain-overview.md` (the authoritative doc). These close five forward gaps and one internal reconciliation that `domain-overview` under-specifies. Staged here only; `domain-overview` to be updated next session after verification.

---

### D-027 — Notification Delivery-State Model

**Problem:** `domain-overview` defers the notification catalog to the Communication module spec and never models delivery. The "safety notifications always reach parents" guarantee is unenforceable — bypassing opt-out guarantees the system *sends*, not that the parent *receives*.

**Decision — every notification has a delivery lifecycle, and the safety class escalates to a human until confirmed:**

Delivery state machine (per channel attempt):

```
Queued → Sent → Delivered → Read        (happy path, where the channel supports receipts)
              ↘ Failed (provider reason) → retry(backoff) → Fallback channel → Staff action item
              ↘ Expired (no confirmation within SLA)      → Staff action item
```

Channels and what they can confirm:

| Channel | Confirmable state | Mechanism |
|---|---|---|
| In-app | Delivered + Read | read receipt |
| SMS | Delivered | MSG91 delivery receipt (DLR) |
| Email | Delivered / Bounced | provider webhook |
| WhatsApp deeplink | **Generated only** | manual tap-to-send — never claim "Delivered" |

Priority classes:

| Class | Examples | Rule |
|---|---|---|
| **Safety-critical** | portal invite, enrollment, Bonafide, incident publication, **pickup-list change**, **absence** | sent **multi-channel** (in-app + SMS); delivery-tracked; **not "complete" until Delivered/Read/acknowledged, else escalated to a staff action item**; bypasses opt-out |
| **Standard** | progress-card published, announcements, overdue (staff) | single channel; opt-out applies; no escalation |

**Key reframing:** separate **opt-out bypass** (a *preference* rule — ensures sent) from **delivery guarantee** (a *reliability* mechanism — ensures received). The two were conflated. Replace the unenforceable invariant "safety notifications always reach parents" with the enforceable "safety-critical notifications are delivery-tracked and escalated to a human fallback until confirmed delivered or acknowledged."

**Industry basis:** healthcare critical-result notification (closed-loop until acknowledged), emergency mass-notification escalation ladders (Everbridge), on-call alerting (PagerDuty ack-or-escalate).

**Build impact:** notification entity carries `priority_class`, `channels[]`, and a per-attempt delivery record (`state`, `provider_ref`, `failure_reason`, `timestamp`). Safety-critical notifications drive a staff action-item queue when not confirmed within SLA.

---

### D-028 — Log Taxonomy (audit spine + projections)

**Problem:** §9.1/§9.2/§7.1 name three logs (governance, audit, compliance audit entry) without reconciling them. Left open, each module invents its own mapping → duplicate or missing compliance evidence.

**Decision — one append-only spine, three read projections:**

| Log | Nature | Contents | Visibility |
|---|---|---|---|
| **Audit Log** | canonical append-only spine | every write, every module, every role incl. Admin | Admin (all branches), Branch Admin (own branch) |
| **Governance Log** | *filtered view* of the audit log | action ∈ override-class set | Admin-only **curation/cross-branch view — not an access boundary** |
| **Compliance view** | *tagged subset* of the audit log | allergy confirmation, pickup override, consent capture, sensitive reads | Branch Admin + Admin |

Audit entry schema: UTC timestamp · actor (id, name, role) · branch · module · action type · target entity (type + id) · before-state · after-state · reason (where required) · correlation id.

**Critical clarification:** because the Governance Log is a *view* over the audit log, and Branch Admin can read their own branch's audit log, the override actions are already visible to Branch Admin at branch scope. "Admin-only governance log" therefore means a curated, cross-branch override view — **not** that override actions are hidden from the acting Branch Admin. Do not design redaction against this.

Governance is driven by one `trigger → event-type → which-views → visibility` matrix (single source of truth for what logs where). Example rows:

| Trigger | Event type | Appears in | Visibility |
|---|---|---|---|
| Attendance correction > 30 days | `attendance.correction.late` | Audit + Governance | Admin (gov), Branch Admin (audit) |
| Pickup override (no code, not on list) | `pickup.override` | Audit + Governance + Compliance | Admin, Branch Admin |
| Allergy protocol confirmed | `meal.allergy.confirmed` | Audit + Compliance | Admin, Branch Admin |
| Aadhaar/medical read | `sensitive.read` | Access Log (D-029) | Admin, Branch Admin |

**Industry basis:** CQRS single-write/multi-read; CloudTrail (technical record) plus curated governance/break-glass views.

---

### D-029 — Access Log for Sensitive-Data Reads (distinct fourth log)

**Problem:** the audit-log schema is write-shaped (before/after state). Reads don't mutate, so they don't fit it — yet DPDP requires a trail of *who read* a child's Aadhaar/medical data and why. §7.4 covers access *control*, not access *logging*.

**Decision — a dedicated, read-shaped Access Log:**

Schema: UTC timestamp · actor (id, role) · branch · **data category** (Aadhaar / medical-vaccination / PEN-APAAR / passport photo / financial) · subject id (student/staff) · **access type** (view / export / print) · purpose/context (where feasible) · correlation id. No before/after.

- Fires on every read/export/print of a defined sensitive category. Immutable, append-only, retained with the data it protects (D-030).
- Generalises the existing export access-logging to **all** sensitive reads.
- Implemented as a distinct event type within the same append-only infrastructure (D-028) and hash-chained (D-031); surfaced via a disclosure view.

**Industry basis:** HIPAA "accounting of disclosures," GDPR Art. 30 records of processing, DPDP data-principal access transparency.

---

### D-030 — Log Retention

**Problem:** §9.6 sets retention per record type but never for the log spine. Since the Governance Log is a *view* of the Audit Log, if audit entries age out, governance and compliance history vanish with them.

**Decision:**

| Log | Retention |
|---|---|
| Audit / Governance | ≥ the longest downstream obligation — minimum the 7-year financial tier + margin → **8 years** |
| Safety-tagged audit/compliance entries (incident, welfare, pickup) | **child's age 18 + 7 years** (minor-claimant limitation tail; POCSO defensibility) — *pending Indian-counsel confirmation* |
| Access Log | aligned with the underlying data's retention (DPDP) |

- Logs are **never auto-purged**; deletion only via an exceptional, logged Admin governance action.
- **Open item:** confirm the child-safety retention period with Indian legal counsel (limitation periods for claims by minors).

**Industry basis:** SOX/financial 7-year, HIPAA 6-year, child-safety "age of majority + N" retention patterns.

---

### D-031 — Tamper-Evidence for Immutable Logs

**Problem:** immutability is asserted as a *policy* ("no role can edit or delete"), not enforced by a *mechanism*. A privileged DB or deploy actor can still alter rows — too weak for logs that may serve as legal evidence (POCSO, DPDP, financial audit).

**Decision — make immutability mechanical, proportionate to V1:**

1. **Hash-chain** every log entry: store `hash(canonical_entry_content + previous_entry_hash)`. Any retroactive edit/delete breaks the chain and is detectable.
2. **DB-level append-only:** revoke UPDATE/DELETE on the log tables for the application's runtime DB role.
3. **External anchor:** emit the latest chain-head hash in the monthly governance digest (and/or a separate store), so even a full-table rewrite is detectable against an outside witness.
4. **Defer** managed WORM / ledger DB (AWS QLDB, S3 Object Lock, Azure immutable blob) until scale or a compliance audit demands it.

Converts "immutable by policy" into "immutable by mechanism."

**Industry basis:** Certificate Transparency logs, hash-chained audit logs in fintech/healthtech, AWS QLDB cryptographic verification.

---

### D-032 — CoFee Framing Reconciliation

**Problem:** `domain-overview` §5 ("CoFee owns invoice generation and payment collection") and §9.5 ("CoFee Is a V1 Bridge") read CoFee-centric, while D-010 makes Happy Feet the canonical ledger. Both are authoritative; they now disagree.

**Decision (confirmed by product intent — CoFee is temporary, fully replaced at V2):**
- Happy Feet is the canonical financial ledger from V1 (D-010).
- CoFee is one **tagged, temporary** collection source feeding that ledger (D-003, D-025).
- V2 retires CoFee entirely (D-019).
- Parent fee visibility comes from the HF ledger via self-serve (D-023) — **not** "check CoFee directly."

**Document-sync action (next session, after verification):** reword `domain-overview` §5 and §9.5 to the single-ledger framing — CoFee described as a temporary V1 source, not the owner of the financial picture. Staged here only.

---

### D-033 — CoFee XLSX Ingestion Specification

The client provided the real CoFee export schema. This concretises D-025 (import discipline) and binds CoFee to the HF canonical ledger (D-010) and invoice states (D-016).

**Governing principle — events are truth, summary fields are controls.** CoFee gives us both the *transaction* (`Amount paid`) and its own *derived state* (`Status`, `Amount paid till date`, `Remaining amount`). Per D-010, HF must **derive its own ledger state from the payment events** and treat CoFee's summary fields as a **reconciliation cross-check**, not as the source of truth. Summing HF's applied `Amount paid` events for an invoice/fee-head must equal CoFee's `Amount paid till date`; if not, a payment row was missed or double-applied → suspense. This is the check that catches dropped/duplicated rows *across* imports.

#### A. Field → HF ledger mapping

| CoFee field | HF mapping | Role |
|---|---|---|
| `Transaction Id` / `UTR` | `external_reference_id` | idempotency key (online / bank) |
| `Invoice id` | invoice key | ledger grouping |
| `Admission number` | Student (D-001 permanent ID) | student resolution |
| `Branch name` | Branch entity | branch binding (isolation) |
| `Fee head` | canonical fee head | needs a mapping table (free-text → canonical) |
| `Currency` | assert = INR | non-INR → exception |
| `Total amount` | invoice line total | reconciliation |
| `Amount paid` | **payment event amount (gross)** | the ledger event |
| `Amount paid till date` | reconciliation control | assert vs HF-derived sum |
| `Remaining amount` | reconciliation control | assert = Total − derived paid |
| `Status` | reconciliation control | assert vs HF-derived state (D-016) |
| `Due on` | invoice due date | drives Overdue derivation |
| `Paid on` | collection timestamp | gross collection event |
| `Settled on` | settlement timestamp | settlement event |
| `Total amount settled` | **net settled amount** | bank reconciliation |
| `Paid via` / `Type` | payment method / class | metadata + advance detection |
| `Account number/name` | destination account | settlement reconciliation |
| `Quick collect notes` / `Event name` / `Offline payment reminder` | payment annotations | dashboard (revenue-by-event), audit |

#### B. Idempotency key resolution (the one schema gap)

`Transaction Id` and `UTR` are **both nullable**, and offline Cash/Cheque rows can have neither — so the client's stated dedup rule has no key for them, and a re-imported file would **double-count cash payments**. Resolution: a deterministic synthesised key `hash(Invoice id + Admission number + Fee head + Amount paid + Paid on)` for rows lacking both. `S.no` must **not** be used — it is per-file row sequence, not stable across exports.

#### C. CoFee `Status` → HF invoice state (D-016)

| CoFee Status | HF derived state | Note |
|---|---|---|
| Paid | Paid | |
| Partially Paid | PartiallyPaid | cumulative via `Amount paid till date` (installments) |
| Pending | Issued → Overdue | Overdue derived if past `Due on` |
| Failed | *no payment applied* | record as failed attempt; must **not** reduce remaining |
| Refunded | Credited (compensating event) | appended per D-011, never a deletion |

#### D. Three model additions the schema implies

1. **Collection vs settlement are distinct events.** Gross (`Amount paid`, `Paid on`) is what the parent paid; net (`Total amount settled`, `Settled on`) is what reached the school bank after gateway fees. Parent dues and the collection-rate dashboard (D-026) use **gross**; cash/bank reconciliation uses **net**. The ledger stores both, with two timestamps.
2. **Advance / unapplied credit.** `Type=Advance` payments can exist before an invoice — modelled as on-account credit (standard AR "unapplied cash"), later applied to an invoice. Relevant to advance admissions into an Upcoming year (D-009).
3. **Refunds as compensating events.** `Status=Refunded` appends a negative compensating ledger event (D-011); the original payment event is never altered.

#### E. Exception taxonomy (all route to the suspense queue, D-010)

| Exception | Trigger | Migration Mode | Normal ops |
|---|---|---|---|
| Duplicate Transaction Conflict | idempotency key already ingested | skip (idempotent) | skip (idempotent) |
| Unrecognized Student | `Admission number` not in system | **expected — hold in suspense** | hard error → suspense |
| Ledger Mismatch | `Remaining + Paid-till-date ≠ Total`, or HF-derived sum ≠ CoFee summary | suspense | suspense |
| State Constraint Violation | `Status=Paid` & `Paid on` null; `Type=Online` & `Transaction Id` null | suspense | suspense |
| Unmapped Fee Head / Branch / non-INR Currency | no canonical mapping | suspense | suspense |

Client's four validation rules (ID dedup, student resolution, mathematical invariance, state constraints) are adopted verbatim as ingestion gates, extended with the offline idempotency key (B), the events-vs-controls reconciliation (governing principle), and Migration-Mode-aware student resolution.

---

## Open Questions — Staged for domain-overview §10 (next session)

Recorded here for the client; to be added to `domain-overview` §10 after verification.

**QUESTION-013 — Child-safety record retention period (basis: D-030).**
The provisional default is the child's age 18 + 7 years for safety-tagged audit/incident/welfare entries (minor-claimant limitation tail; POCSO defensibility). Confirm the correct statutory retention period for child-safety records with Indian legal counsel before it is fixed in the audit/retention design.

**QUESTION-014 — CoFee offline payment reference guarantee (basis: D-033).**
Does CoFee guarantee a stable, unique reference (`UTR`, receipt no., or other) for every offline Cash/Cheque payment? If yes, that becomes the idempotency key; if no, HF must rely on the synthesised composite key, which is weaker against legitimate same-amount same-day duplicates.

**QUESTION-015 — Fee-head canonical mapping (basis: D-033).**
`Fee head` arrives as free text ("Tuition Fee", "Transport"). What is the authoritative list of CoFee fee heads in use, so HF can build the free-text → canonical fee-head mapping table? Unmapped heads will route to suspense.

**QUESTION-016 — Dues figure basis: gross collected vs net settled (basis: D-033).**
For parent-facing dues and the owner collection dashboard, should the figure reflect gross amount paid (what the parent paid) or net settled (what reached the bank)? Default assumption: dues = gross; cash reconciliation = net.

---

## CoFee → HF Ledger Data Dictionary (parser reference)

Standalone, code-from-this reference for the CoFee XLSX parser. Authoritative schema per D-033. One XLSX row = one **payment event** against an invoice/fee-head for a student. Trim whitespace on all strings; treat empty string as null.

### Field dictionary

| # | CoFee column | Type / Nullable | HF target | Transform / validation | On failure |
|---|---|---|---|---|---|
| 1 | `S.no` | int | — (ignore) | per-file sequence only; **never** use as a key | — |
| 2 | `Transaction Id` | string / nullable | `payment.external_ref` (online) | required when `Type=Online` | State Constraint Violation → suspense |
| 3 | `Invoice id` | string / not null | `payment.invoice_ref` | required; groups events into an invoice | Reject row → suspense |
| 4 | `UTR` | string / nullable | `payment.external_ref` (bank) | bank-transfer reference | — |
| 5 | `Name` | string | `student.name_snapshot` | confirmation only; **never** create/resolve student by name | — |
| 6 | `Admission number` | string / not null | resolve → `student_id` (D-001) | exact match to existing Admission ID | Unrecognized Student → suspense (expected in Migration Mode) |
| 7 | `Phone number` | string | `payment.payer_phone_snapshot` | optional secondary confirmation | — |
| 8 | `Group name` | string | `student.group_snapshot` | informational | — |
| 9 | `Branch name` | string / not null | resolve → `branch_id` | map to Branch entity (binds ledger row to branch) | Unmapped Branch → suspense |
| 10 | `Fee head` | string / not null | resolve → `fee_head_id` | free-text → canonical via mapping table (QUESTION-015) | Unmapped Fee Head → suspense |
| 11 | `Currency` | string (ISO-3) | assert `= INR` | V1 is INR-only | Non-INR Currency → suspense |
| 12 | `Total amount` | decimal | `invoice_line.total` | `>= 0`; `>= Amount paid till date` | Ledger Mismatch → suspense |
| 13 | `Amount paid` | decimal | **`payment.amount_gross`** | the ledger event amount; `>= 0` | Reject → suspense |
| 14 | `Amount paid till date` | decimal | reconciliation control | assert `== Σ HF applied gross for (invoice+fee head)` | Ledger Mismatch → suspense |
| 15 | `Remaining amount` | decimal | reconciliation control | assert `Remaining + Amount paid till date == Total amount` | Ledger Mismatch → suspense |
| 16 | `Status` | enum | drives `invoice.state` (D-016) derivation + reconciliation | Paid·Partially Paid·Pending·Failed·Refunded (see state map) | Unknown enum → suspense |
| 17 | `Due on` | date | `invoice.due_date` | parse to date; drives Overdue derivation | Bad date → suspense |
| 18 | `Paid on` | datetime / nullable | `payment.collected_at` (UTC) | **required when `Status=Paid`**; store UTC, display IST | State Constraint Violation → suspense |
| 19 | `Settled on` | date / nullable | `payment.settled_at` | settlement event timestamp | — |
| 20 | `Total amount settled` | decimal / nullable | **`payment.amount_net`** | net after gateway fees; bank reconciliation | — |
| 21 | `Paid via` | enum | `payment.method` | UPI·Credit Card·NetBanking·Cash·Cheque·None | Unknown enum → suspense |
| 22 | `Type` | enum | `payment.class` | Online·Offline·Partial·Advance (Advance → unapplied credit) | Unknown enum → suspense |
| 23 | `Account number` | string | `payment.dest_account_no` | settlement reconciliation | — |
| 24 | `Account name` | string | `payment.dest_account_name` | settlement reconciliation | — |
| 25 | `Offline payment reminder` | bool/string | `payment.offline_reminder_flag` | normalise truthy/empty → bool | — |
| 26 | `Quick collect notes` | string / nullable | `payment.note` | manual-collection remark | — |
| 27 | `Event name` | string / nullable | `payment.event_tag` | links to event/one-off fee; feeds revenue-by-event (D-026) | — |

### Derived / computed values

| Derived | Rule |
|---|---|
| `idempotency_key` | `Transaction Id` if present → else `UTR` if present → else `hash(Invoice id + '|' + Admission number + '|' + Fee head + '|' + Amount paid + '|' + Paid on)` |
| `payment.amount_gross` | `Amount paid` (what the parent paid; drives dues + collection rate) |
| `payment.amount_net` | `Total amount settled` (what reached the bank; drives cash reconciliation) |
| `invoice.state` (D-016) | **derived from HF applied events**, then asserted against CoFee `Status` / `Remaining amount`; mismatch → suspense |
| Refund | `Status=Refunded` → append a **negative compensating** payment event (D-011); never edit/delete the original |
| Failed | `Status=Failed` → record as a failed attempt; **do not** apply amount or reduce remaining |
| Advance | `Type=Advance` → on-account **unapplied credit** until applied to an invoice |

### Ingestion pipeline (per row, in order)

1. **Dedup** — compute `idempotency_key`; if already ingested → *Duplicate Transaction Conflict*, skip (idempotent).
2. **Resolve** — `Admission number` → student; `Branch name` → branch; `Fee head` → canonical head. Any miss → suspense (student-miss is expected in Migration Mode).
3. **Validate** — Currency=INR; state constraints (Paid⇒`Paid on` not null; Online⇒`Transaction Id` not null); enum membership.
4. **Math invariance** — `Remaining + Paid-till-date == Total`; `Total >= Paid-till-date`. Fail → *Ledger Mismatch*.
5. **Apply** — write the immutable payment event (gross + net + timestamps + idempotency key + source=`COFEE_IMPORT`) to the ledger; Advance → unapplied credit; Refunded → compensating event.
6. **Reconcile** — assert HF-derived paid-till-date and state == CoFee summary; divergence → suspense.
7. **Provenance** — record import batch (file id, when, who, row counts, accepted/suspense tallies) per D-025.

All suspense rows surface in the Accountant reconciliation queue; none are silently dropped. The whole batch is idempotent and safely re-runnable.

---

# Build Specification Register

**Derived from `domain-overview.md` v1.2 — the authoritative source.** This register consolidates *every* build-relevant decision, rule, enumeration, state machine, and invariant from the completed domain overview into a form directly consumable by coding agents and junior developers. The decision table (D-001…D-033) above gives the **why**; this register gives the **what to build**, in testable terms. Items in §1–§8 of the overview that were always domain facts (and never numbered decisions) are captured here for the first time. On any conflict, `domain-overview.md` wins and this register is corrected.

> **How to use this (agents & developers):** treat every `ENUM-*` as a closed set, every `SM-*` as the allowed state graph (no transition outside it), every `INV-*` as an assertion that must hold at all times (write a test for each), and the Authority Matrix as the permission gate. Cross-references point to the decision (`D-*`) and overview section (`§*`) that own the rule.

---

## 1. Canonical Enumerations (ENUM)

Closed sets. Do not extend in code without a new decision. All are V1 unless noted.

| ID | Enumeration | Values | Source |
|---|---|---|---|
| ENUM-01 | System roles | Admin, Branch Admin, Coordinator, Teacher, Accountant, Parent/Guardian (Child = non-user subject) | §4 |
| ENUM-02 | Teacher slot types | Primary, Assistant, Temporary — **identical permissions**; Primary is the authoritative attendance marker | §4 / D-014 |
| ENUM-03 | Programmes | Play Group, Nursery, PP1, PP2, Day Care — **extensible** (config-driven, not hardcoded) | §1 |
| ENUM-04 | Student categories | Standard, Staff Child, Subsidised — determine applicable fee structure | §5 |
| ENUM-05 | Payer types | Primary (parent/guardian, default), Secondary (employer/trust/sponsor, optional) | §5 / D-007 |
| ENUM-06 | Revenue streams (billing model) | Academic (term-based, **no pro-rating**), Day Care (plan: Monthly/Weekly/Day-wise/Hour-wise/Term-wise), Transport (fixed monthly by zone, opt-in per student), Meal (plan-based, optional per branch), Event (one-off) | §5 / §7.2 |
| ENUM-07 | Fee billing cycles | Monthly, Termly, Annual | §6 |
| ENUM-08 | Academic year states | Upcoming, Active, Closed | §9.7 / D-009 |
| ENUM-09 | Attendance marks | Present, Absent, Late, Half-Day | §7.2 |
| ENUM-10 | Admissions states | Enquiry, Applied, Under Review, Waitlisted, Offered, Accepted, Rejected, Application-Withdrawn, Offer-Expired | §9.11 / D-015 |
| ENUM-11 | Student (enrolled) states | Active, Promoted/Rolled-over, Completed Programme *(T)*, Discontinued *(T)*, Transferred *(T)* — *(T)*=terminal | §9.11 / D-015 |
| ENUM-12 | Invoice states | Draft, Issued, PartiallyPaid, Paid, Overdue, Void, Credited — **derived, never hand-set** | §9.9 / D-016 |
| ENUM-13 | Welfare concern states | Raised, Acknowledged, Under Review, Resolved, Escalated, Closed-no-action *(reason required)* | §7.1 / D-017 |
| ENUM-14 | Notification delivery states | Queued, Sent, Delivered, Read, Failed, Expired | §9.12 / D-027 |
| ENUM-15 | Notification priority class | Standard (opt-out applies), Safety-critical (bypass opt-out, multi-channel, escalates) | §9.12 / D-027 |
| ENUM-16 | Migration Mode (per branch) | Not-entered, Active, Exited, Re-entered — **reversible, unlimited re-entry** | §9.4 / D-002 / Q-008 |
| ENUM-17 | Payment source | COFEE_IMPORT, RAZORPAY_LINK, HF_NATIVE | §9.9 / D-010 |
| ENUM-18 | Sensitive data categories | Aadhaar, Medical/vaccination, PEN/APAAR, Passport photo, Financial | §7.4 / D-029 |
| ENUM-19 | Pickup authorisation paths | Standing-list (PIN/QR + photo), One-time parent code, Two-actor override | §7.1 / D-013 |
| ENUM-20 | Log types | Audit (spine), Governance (filtered view), Compliance (tagged view), Access (distinct read log) | §9.10 / D-028/29 |

---

## 2. Actor Authority Matrix

Who may perform each gated action. Enforce as the permission layer. "Two-actor" = requires a second, higher-authority actor.

| Action | Authorised actor | Notes |
|---|---|---|
| Promote academic year Upcoming→Active; Close Active year | **Admin** | manual, logged; never automatic (INV-07) |
| Enter holidays | **Admin** | blocks attendance, no override (INV-08) |
| Trigger year-end rollover | **Branch Admin** | per-invoice dues acknowledgment; in step with Admin year promotion |
| Publish progress card | **Branch Admin** | Teacher drafts/submits |
| Record incident + publish decision | **Branch Admin** | internal notes never shown to parents |
| Review welfare concern (≤3 school days) | **Branch Admin** | reminder then Admin escalation |
| Capacity override / document waiver | **Branch Admin** | governance log entry, per-item reason |
| Authorised-pickup override (unauthorised person) | **Branch Admin (two-actor)** | + parent OTP + photo; governance + compliance entry |
| Issue TC / Bonafide — no dues | **Branch Admin** | irreversible; parent portal revoked |
| Issue TC / Bonafide — dues exist | **Admin** | governance log entry |
| Mark attendance | **Teacher (Primary slot)** | authoritative marker; others rejected/amend (D-014) |
| Raise welfare concern; allergy confirmation; author lesson plans; draft progress | **Teacher** | within class scope |
| Gate release (initiate) | **Coordinator** | escalates override to Branch Admin |
| CoFee import; fee management; billing comms | **Accountant** | reconciliation queue ownership |
| Issue one-time pickup code; self-serve fee + pay | **Parent** | child-scoped, read-only elsewhere |
| View governance log | **Admin only** | curated cross-branch view (not a redaction boundary, D-028) |
| View audit log | **Admin (all branches), Branch Admin (own branch)** | §9.10 |
| Migration Mode entry/exit/re-entry | **Admin** (with Branch Admin readiness confirmation) | logged; unlimited re-entry (Q-008) |

---

## 3. Canonical State Machines (SM)

Allowed transitions only. Any transition outside the graph is a defect. All transitions are append-only (INV-12) and audit-logged.

**SM-01 Academic Year** (`Upcoming → Active → Closed`) — one Active at a time (INV-06); every transition manual + logged (INV-07); no midnight automation.

**SM-02 Student** — Admissions track: `Enquiry → Applied → Under Review → {Waitlisted →} Offered → Accepted` (handoff to enrolled at enrollment) with exits `Rejected | Application-Withdrawn | Offer-Expired`. Enrolled track: `Active → Promoted/Rolled-over (loops yearly)` and terminals `Completed Programme | Discontinued | Transferred`. Terminal students cannot be rolled over; re-admission links to the historical record (preserves Admission ID, D-001).

**SM-03 Invoice** (`Draft → Issued → {PartiallyPaid →} Paid`; `Issued → Overdue` if past due; `→ Void`; `→ Credited` on refund) — state **derived** from applied payments (D-016); refunds append compensating entries (D-011), never reduce/delete.

**SM-04 Welfare Concern** (`Raised → Acknowledged → Under Review → {Resolved | Escalated | Closed-no-action}`) — 3-school-day SLA; reminder then Admin escalation; permanent, immutable, never parent-visible.

**SM-05 Notification Delivery** (`Queued → Sent → Delivered → Read`; `Sent → Failed → retry → Fallback channel → Staff action item`; `Sent → Expired → Staff action item`) — safety-critical class not "complete" until Delivered/Read/acknowledged or escalated (D-027). WhatsApp deeplink terminates at `Generated`.

**SM-06 Migration Mode** (`Not-entered → Active → Exited → (Re-entered → Active …)`) — reversible, unlimited re-entry, each entry/exit logged to governance (D-002, Q-008); Phase 0 master-data setup runs inside it (D-008).

---

## 4. Invariant Register (INV)

Always-true assertions. Each should have an automated test. Severity: 🔴 Critical (safety/legal/financial), 🟠 High.

| ID | Invariant | Sev | Source |
|---|---|---|---|
| INV-01 | Every enrolled student has ≥1 active standing pickup-list entry at all times | 🔴 | §7.1 / Q-012 |
| INV-02 | No child is released to a non-authorised person without a two-actor override that writes a governance **and** compliance entry | 🔴 | §7.1 / D-013 |
| INV-03 | Aadhaar, medical records, and contact numbers never appear in any notification, export, or deeplink | 🔴 | §7.4 |
| INV-04 | Audit/governance/compliance/access log entries are immutable and hash-chained; any retroactive edit is detectable | 🔴 | §9.10 / D-031 |
| INV-05 | A student's Admission ID is permanent and never changes once assigned | 🟠 | §9.1 / D-001 |
| INV-06 | At most one academic year is Active per branch at any time | 🟠 | §9.7 |
| INV-07 | No state transition is automatic — year activation, rollover, and Migration exit are manual, confirmed, and logged | 🟠 | §9.7 |
| INV-08 | A holiday blocks attendance for all roles on that date with no override | 🟠 | §6 |
| INV-09 | Invoice status is derived from applied payments — never a hand-set flag | 🔴 | §9.9 / D-010 |
| INV-10 | Every override action writes a permanent governance log entry | 🔴 | §9.2 |
| INV-11 | Branch data isolation is enforced on every data request; no cross-branch access below Admin | 🔴 | §9.3 |
| INV-12 | Immutable records are appended, never overwritten; corrections are compensating entries; current value = latest entry | 🔴 | §9.1 / D-011 |
| INV-13 | Dues-gated actions (TC, Bonafide, rollover) read the derived outstanding balance as of the last import; require a fresh import or explicit Accountant confirmation | 🟠 | §9.5 / D-025 |
| INV-14 | All time-bound logic derives from the M2 academic calendar — no module stores independent date logic | 🟠 | §9.8 |
| INV-15 | All timestamps are stored in UTC and displayed in IST (UTC+5:30) | 🟠 | §7.4 |
| INV-16 | Safety-critical notifications are delivery-tracked and escalate to a staff action item until confirmed delivered or acknowledged | 🔴 | §9.12 / D-027 |
| INV-17 | A payment is recorded once (idempotent on source + external reference); duplicates are skipped, unmatched rows go to suspense, none are dropped | 🔴 | §9.9 / D-025/33 |
| INV-18 | Sensitive-data reads/exports/prints are written to the access log | 🔴 | §7.4 / D-029 |
| INV-19 | Academic fees are full-term with no pro-rating for mid-term admissions | 🟠 | §5 |
| INV-20 | Internal incident notes and welfare records are never surfaced to parents under any circumstance | 🔴 | §7.1 |

---

## 5. Domain Boundaries (build scope)

**V1 — In scope (16 capability modules):** Identity & access management · School configuration · Student records (incl. PTM notes) · Staff records (incl. visitor log) · Admissions & enrollment (incl. waitlist, four enrollment gates Q-012) · Student lifecycle (rollover, TC, Bonafide) · Fee management (canonical ledger, CoFee import, Razorpay links, Day Care plans, self-serve fee+pay) · Attendance management (incl. ratio monitoring, absence SMS) · Curriculum & lesson planning · Progress & assessments · Meal management (optional, branch toggle) · Transport management (school fleet + boarding/drop confirmation) · Communication (incl. notification delivery model) · Daily Care Log · Reporting & governance (audit/governance/access logs, dashboards) · Migration Mode.

**V2 — Out of scope:** Pre-admission enquiry/lead management · Full Razorpay API auto-collection · Automated WhatsApp Business API dispatch · Budget/expenditure tracking · AI/predictive analytics · Payroll export · Photo sharing/distribution (DPDP-gated) · Full multi-branch management UI · Retention/cohort reporting · APAAR/PEN/UDISE+ submission workflows · Live GPS transport tracking (D-024).

**Permanently out of scope:** Historical attendance import from paper (manual marker only) · Programmatic parsing of paper/legacy spreadsheets (staff are the migration engine) · Payroll processing (export only).

---

## 6. Cross-Cutting Build Constraints

| Constraint | Rule | Source |
|---|---|---|
| Architecture | Modular monolith; 16 capability modules → 12 bounded contexts + Migration as cross-cutting state; modules share aggregates via a domain layer, never reach into each other's data | D-020 / §9.14 |
| Admissions boundary | Admissions is a separate context from Student Records; transient applicant data never enters the permanent student store | D-020 |
| Multi-branch | Branch-aware from day one (role binding, branch isolation); no per-branch pricing/tenancy in V1 | D-004 / §9.3 |
| Offline-first | Attendance & daily-care capture work offline (local write → queue → sync); records show `pending-sync`; conflicts via single-marker rule | D-022 / §9.13 |
| Time | UTC storage, IST display; calendar from M2 only | INV-14/15 |
| Logging | One append-only audit spine; governance/compliance are filtered views; access log is distinct; all hash-chained, anchored in monthly digest | D-028/29/31 / §9.10 |
| Financial truth | HF canonical ledger; CoFee/Razorpay/manual are tagged sources; CoFee temporary, retired at V2 | D-010/19/32 / §9.9 |
| Retention | Logs retained ≥8y, never auto-purged; safety-tagged provisional age-18+7y pending counsel (Q-013) | D-030 / §9.6 |
| Complexity posture | Governance/audit machinery is invisible infrastructure; front-line surfaces = parent feed, attendance, fees, communication; ship templates/defaults | D-018 / §9.15 |

---

## 7. Decision → Implementation Index

Where each decision lands, for navigation by agents and developers. Context names per the Bounded-Context Map.

| Decision | Primary context(s) | Overview anchor |
|---|---|---|
| D-001 Admission ID permanence | Student | §9.1 |
| D-002 Migration Mode reversible | Migration (cross-cutting) | §9.4 |
| D-003/D-019/D-032 CoFee bridge → own-ledger | Finance | §5, §9.5, §9.9 |
| D-004 Multi-branch | Identity & Access | §9.3 |
| D-005 Ratio in Attendance | Attendance & Safety | §8 |
| D-006 Meal toggle | School Config / Daily Care | §8 |
| D-007 Payer routing | Finance | §5 |
| D-008 Phase 0 in Migration | Staff / Migration | §6, §9.4 |
| D-009 Three-state year | School Config | §9.7 |
| D-010/D-016/D-025/D-033 Ledger, invoice states, CoFee import | Finance | §5, §9.5, §9.9 |
| D-011 Compensating events | Attendance & Safety / Finance (cross-cutting) | §9.1, §9.8 |
| D-012/D-028 Logging taxonomy | Reporting & Governance | §9.2, §9.10 |
| D-013 Pickup hybrid | Attendance & Safety / Communication | §7.1, §9.2 |
| D-014 Attendance marker authority | Attendance & Safety | §8 |
| D-015 Lifecycle states | Student / Admissions | §9.11 |
| D-017 Welfare + SMS states | Attendance & Safety / Communication | §7.1, §9.12 |
| D-018 Complexity posture | All (UX/rollout) | §9.15 |
| D-020/D-021 Context map, Transport | Architecture / Transport | §9.14, §8 |
| D-022 Offline-first | Attendance & Safety / Daily Care | §9.13 |
| D-023 Self-serve fee + pay | Finance / Identity (parent portal) | §8 |
| D-024 Boarding/drop confirmation | Transport | §8 |
| D-026 Dashboards | Reporting & Governance | §8 |
| D-027 Notification delivery | Communication | §9.12 |
| D-029 Access log | Reporting & Governance | §7.4, §9.10 |
| D-030 Log retention | Reporting & Governance | §9.6 |
| D-031 Tamper-evidence | Reporting & Governance | §9.1, §9.10 |

---

## 8. Open Items Gating Build (must-resolve)

| Item | Blocks | Owner | Ref |
|---|---|---|---|
| Child-safety retention period | audit/retention design | Indian legal counsel | Q-013 / D-030 |
| CoFee offline payment reference guarantee | import idempotency key | Client | Q-014 / D-033 |
| Canonical CoFee fee-head list | fee-head mapping table | Client | Q-015 / D-033 |
| Dues basis: gross vs net | dashboard + parent dues | Client | Q-016 / D-033 |
| CoFee XLSX schema stability + import cadence | fee import module | Client | D-025 |

*Until resolved, the affected paths route unmappable/unconfirmable data to the suspense queue rather than guessing — the system is safe to build now and tightens as answers land.*
# Module: Identity & Access

> Build reference for the Identity & Access module. For agents and devs.
> Companion to `AI-DECISIONS.md` (decisions 4-primitive, 13, 17, 18). V1 module, Phase A.

---

## 1. Purpose

The spine every other module calls to answer two questions: **"who is this user?"** and **"what may they do, on which data?"** It owns identities, **role assignments**, the branch hierarchy, sessions, MFA/RBAC, invitations, de-provisioning, and the single `authorize()` gate.

Nothing in the app checks a role or a branch inline — it calls this module.

---

## 2. Organizational Structure

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

Every level can have **multiple people** (multiple admins, multiple branch admins per branch, many teachers, etc.). Branch is a first-class entity — Branch B "drops in" with no rework.

---

## 3. Roles (V1)

| Role | Scope | In one line |
|---|---|---|
| **Admin** (owner) | all branches | full authority; manages branches + admins; ≥2 required |
| **Branch Admin** (principal) | one branch | runs the branch; invites its staff; verifies docs |
| **Coordinator** (front desk) | branch | intake, gate, communications; **cannot delete**; handles & verifies docs |
| **Teacher** | assigned classes | attendance (Primary slot = authoritative marker), daily log, lessons; Temporary slot = reduced profile |
| **Accountant** | branch finance | CoFee import, fee view/export, billing comms |
| **Parent / Guardian** | own child(ren) | read-only, child-scoped; multi-guardian with capability flags |

> **Transport Staff** (driver/attendant, route-scoped, minimal child info) is a **V2 role** — it ships when transport management is built. It will slot in as just another `user_role` row with a `route/trip` binding; no model change needed.

> Full per-role capabilities/limitations live in `domain-overview.md` §4 + §9.16 and `ENGINEERING-SPEC.md` (RBAC & Actor Model).

---

## 4. Data Model

```
user            { id, clerk_id, email, name, status: invited|active|deactivated }
branch          { id, name, status }
user_role       { user_id, role, branch_id }          -- MANY rows per user
class_teacher   { class_id, user_id, slot_type }       -- teacher ↔ class
guardian_child  { user_id, child_id, can_pickup, can_receive_comms,
                  payer_role, access_level, is_emergency_contact }  -- parent ↔ child
```

**The load-bearing rule: a role is an *assignment* (`user_role` row), never a column on `user`.** This is what makes every multiplicity case below fall out for free, and it is painful to retrofit — do it from line 1.

---

## 5. Access Model — Role × Scope × Relationship

`authorize(action, scope)` is **deny-by-default** and resolves as:

```
ALLOW ⟺  a user_role grants the action for the target's branch   (role × scope)
      ∧  for child data: a class_teacher or guardian_child edge exists  (relationship)
```

- **Role (what):** look up `user_role` for the user.
- **Scope (which):** the target's `branch_id` must match a `user_role.branch_id` (Admin matches all).
- **Relationship (whose):** teacher → only their `class_teacher` classes; parent → only their `guardian_child` children.

Examples:
- *Teacher marks attendance* → has `user_role(teacher, branchA)` **and** `class_teacher(class, teacher)` for that class.
- *Parent views child* → has `guardian_child(parent, child)` with `access_level ≠ excluded`.
- *Branch Admin issues TC* → has `user_role(branch_admin, branchA)` and the student is in branch A.

No ABAC/ReBAC engine — these are three small tables + `WHERE` clauses. (Supabase RLS optional later as a DB-layer safety net.)

---

## 6. Provisioning & Login — the invite chain

Top-down, invite-based. Clerk sends the invite email; the person accepts, sets up MFA (staff), and lands with their `user_role` assigned.

| Role | Provisioned by | Login |
|---|---|---|
| **Admin** (owner) | First account at go-live (dev-provisioned / self-signup as first super-admin) | Email + password → **MFA required** |
| **Branch Admin** | **Admin** (email invite) | Email invite → accept → **MFA required** |
| **Coordinator / Teacher / Accountant** | **Branch Admin** of their branch | Email invite → accept → **MFA required** |
| **Parent / Guardian** | System / Coordinator **at enrollment** | Email magic-link or OTP → child-scoped; MFA optional |

**Delegated invitations:** Admin invites Admins + Branch Admins; Branch Admin invites their branch's staff. The developer is never the person creating every account.

---

## 7. Parent Onboarding — invite → submit docs → verify

1. **Invite at enrollment** (not first payment — fees are external/CoFee in V1). The parent app is useful from day one.
2. Parent signs in (email magic-link / OTP) → child-scoped access.
3. Parent **submits sensitive documents** (Aadhaar, medical, passport photo) via the portal — stored in the **separate PII table** (Mumbai), access-logged.
4. **Admin or Branch Admin verifies** the documents → document state `Uploaded → Verified` *(People/Documents module)*.

Multi-guardian: a child can have several guardians, each their own login + capability flags (`can_pickup`, `can_receive_comms`, `payer_role`, `access_level` incl. `excluded` for custody/safeguarding, `is_emergency_contact`).

---

## 8. Authentication (Clerk)

- **Clerk** handles identity, invitation emails, MFA, and sessions. Session **persists** — log in once, not per task.
- **Email** is the channel for all auth workflows (invites, sign-in/out, verification).
- **MFA required for staff roles** (admin, branch admin, accountant, coordinator, teacher) via **authenticator app + backup codes — not SMS**. Parents: email, MFA optional.
- **Step-up re-authentication (mandatory, V1):** before a sensitive action the user must **re-verify** (re-enter MFA / confirm credentials via Clerk re-verification) **even within an active session**. Required before: **revealing a child's regulated data** (Aadhaar / medical / photo), **data exports**, **admissions enroll/reject decisions**, **TC/Bonafide acknowledgment & discontinuation**, and **user/role management** (invite / deactivate / role change). Limits damage from a hijacked or left-open session.
- **SMS (MSG91) is reserved for parent communications** (absence alerts, etc.), never auth.
- **We own `user_role`** (role + branch) in our DB; Clerk owns identity only. Clerk's *Organizations* feature could model branches but couples us to Clerk + can hit paid tiers — revisit only for true multi-*customer* SaaS.
- **Residency:** Clerk holds auth identities (email/phone) on US infra (DPDP-permissible today); all sensitive student data stays in Supabase Mumbai.

---

## 9. Multiple people per role — the whole point

| Case | How it's expressed |
|---|---|
| Multiple admins (owner + trusted) | several `user_role(admin, …)` rows — **≥2 required** to avoid single-owner lock-in |
| Multiple branch admins / teachers / accountants | just more `user_role` rows for that branch |
| Teacher across two branches | two `user_role` rows (+ `class_teacher` per branch) |
| One person, two roles (Accountant + Coordinator) | two `user_role` rows |
| Staff-as-parent (teacher's own child attends) | a `user_role` row **and** a `guardian_child` row — same login, two contexts; own-child sensitive access goes through the guardian edge, not the staff role |

---

## 10. De-provisioning / Offboarding

- **Staff exit (M4 exit gates):** confirm handover (classes reassigned, etc.) → **revoke the Clerk session + deactivate `user_role` rows.** Set `user.status = deactivated`.
- **Deactivate, never delete** — preserve the audit trail and authored records.
- **Parent access** is revoked when the child exits (becomes read-only archive per the retention rule).

---

## 11. Audit

- Every write across the app carries the **specific `user_id`**, not just a role — so with multiple people per role you always know *who* ("Priya marked attendance"), not just *what role*.
- Sensitive-data reads/exports are access-logged.
- The audit log is the cross-cutting `admin` module's `logAudit()`.

---

## 12. User Management screen

- **Admin** (all branches) / **Branch Admin** (own branch) see all users with role, branch, and status (invited / active / deactivated) and can **invite / deactivate / reassign roles**.
- This is how "multiple people per role" is managed day-to-day. Small screen, high value.

---

## 13. Exposes (service interface)

```
getCurrentUser()                       → { user, roles[], branches[] }
authorize(action, scope)               → boolean (deny-by-default)
currentBranch()                        → the session's working branch
inviteUser(email, role, branch)        → sends Clerk invite + pending user_role
assignRole(userId, role, branch)       → adds a user_role row
deactivateUser(userId)                 → revokes session + deactivates roles
listUsers(branchId?)                   → for the user-management screen
requireStepUp(action)                  → ensures recent re-verification; else prompt re-auth
```

Other modules import **only** this service — never check roles or `branch_id` inline.

---

## 14. Key rules / invariants

- **Deny-by-default**; every authorization goes through `authorize()`.
- **Role = `user_role` row**; a user may have many.
- **≥1 (ideally ≥2) active Admins** at all times.
- **Branch isolation** — no cross-branch access below Admin (every query scoped by `branch_id`).
- **MFA required for staff**; email is the auth channel; SMS is comms-only.
- **Sensitive actions require step-up re-auth** (re-verify even within an active session).
- **De-provision = deactivate, never delete** (audit/records survive).
- **Every write carries `user_id`.**

---

## 15. V1 vs later

- **V1:** invite chain, `user_role`/`class_teacher`/`guardian_child`, MFA for staff, **step-up re-auth before sensitive actions**, branch isolation, user-management screen, parent onboarding + doc verification, de-provision on exit, ≥2 admins.
- **Later (V2):** **Transport Staff role** (route/trip-scoped, with transport management), delegation/deputy, break-glass emergency access, read-only Viewer/Auditor grant, ReBAC engine, Clerk Organizations. *(All specified in `ENGINEERING-SPEC.md`.)*

---

## 16. Notes for AI agents / devs

- Always call `identity.authorize()` / `identity.getCurrentUser()` — **never** decode a JWT or check a role inline.
- A role is a `user_role` row, not a field; write code that handles **many** roles per user.
- **Deactivate, don't delete** users.
- `branch_id` is on every tenant-scoped table; `authorize()` enforces the scope — don't reimplement it per query.
- When a feature touches child data, check the **relationship** edge (`class_teacher` / `guardian_child`), not just the role.
- Gate sensitive actions with **`requireStepUp(action)`**, not just a role check — child-data reveal, exports, admissions decisions, certificates/discontinuation, and user/role management all require fresh re-verification.

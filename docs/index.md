# Happy Feet — Product Manual

---

## Table of Contents

1. [What Is This Product?](#1-what-is-this-product)
2. [How the Product Works](#2-how-the-product-works)
3. [Business Model](#3-business-model)
4. [How Your Business Runs Inside the System](#4-how-your-business-runs-inside-the-system)
5. [Who Uses the System — Roles and Actors](#5-who-uses-the-system-roles-and-actors)
6. [Features and Specifications](#6-features-and-specifications)
7. [The System's Modules and the Data They Own](#7-the-systems-modules-and-the-data-they-own)
8. [Key Information the System Manages](#8-key-information-the-system-manages)
9. [How Your Data Is Stored and Protected](#9-how-your-data-is-stored-and-protected)
10. [How the System Is Organised](#10-how-the-system-is-organised)
11. [How People Get Access — Invitations and Accounts](#11-how-people-get-access-invitations-and-accounts)
12. [Services and Subscriptions](#12-services-and-subscriptions)
13. [Your Responsibilities as Application Owner](#13-your-responsibilities-as-application-owner)
14. [Assumptions to Verify](#14-assumptions-to-verify)
15. [Glossary](#15-glossary)

---

## 1. What Is This Product?

Happy Feet is an administration platform for your preschool. It brings admissions, fees, attendance, daily care, and parent communication into one place. Today these run on paper registers, spreadsheets, CoFee, and WhatsApp — each separate from the others. Happy Feet replaces that scattered setup with a single system where every staff member and every parent works from the same live information.

The platform is built for one privately owned preschool in Hyderabad. It currently serves 105 children across one branch, with room to add more branches as the school grows. It is made for the people who run the school day to day: the owner, the principal, teachers, the front-desk coordinator, and the accountant — and for the parents of every enrolled child.

It replaces four things that don't talk to each other today. Paper admission forms become searchable online records. Verbal updates to the owner become real-time visibility. Informal WhatsApp messages become a structured, logged communication channel that both the school and parents can rely on. And fee tracking — currently disconnected from student records — sits alongside attendance and student information, so the full picture of each child is in one screen.

You open Happy Feet as a single app on your phone or laptop. You sign in once and stay signed in. Staff and parents simply tap the icon to pick up where they left off.

---

## 2. How the Product Works

### 2.1 The Big Picture

Happy Feet is one system made up of a few connected areas, each handling a part of school life. There is an area for **student and staff records**, where every child's profile, documents, and medical notes live alongside staff details. There is an area for **admissions**, where new applications come in and become enrolled students. There is an area for **attendance and daily care**, where teachers mark who is present and log each child's meals, naps, and activities. There is an area for **fees**, where the school's payment data from CoFee is brought in and shown next to each student. There is an area for **communication**, for announcements and messages to parents. And there is an area for **school setup**, where the academic year, classes, timetable, and calendar are configured.

These areas are not separate apps. They are sections of one platform, and they share the same information. A child enrolled in admissions becomes a record everyone else works from — the teacher who marks their attendance, the accountant who tracks their fees, and the parent who follows their day are all looking at the same child, the same facts, kept current.

Underneath all of it is a layer that controls **who can see and do what**. Every person signs in as themselves, and the system shows each role only what belongs to them. The owner sees everything across the school. A teacher sees their own classes. A parent sees only their own child. This keeps sensitive information — medical records, identity documents, fees — in front of the people who need it and away from everyone else.

### 2.2 What a Typical Day Looks Like

A typical day runs through the front-line staff. Each teacher opens the app and marks attendance for their class. The moment a child is marked absent, the system can alert that child's parent — no separate phone call needed. Through the day, the teacher logs what each child did: what they ate, when they napped, the activities they joined. Parents see this as a running feed on their own phones, so they know their child is safe and cared for without having to ask.

The principal — the Branch Admin — uses the day to keep operations moving. They review new admission applications, check that documents parents have uploaded are correct, post announcements, and watch the day's numbers: who is in, who is absent, what fees are outstanding. The owner doesn't need to be on site. They open the app from a laptop and see the same live picture across the school, instead of waiting for a verbal update at the end of the day.

What the system removes is the manual stitching. No paper register to tally by hand. No spreadsheet to reconcile against CoFee. No WhatsApp thread to scroll through to find what was said. The work that used to live in four disconnected places now happens once, in one place, and is recorded as it happens.

### 2.3 How the System Connects Everything

The connections are what make Happy Feet more than a set of digital forms. When a child is enrolled, their record immediately becomes available everywhere it is needed. The teacher can mark their attendance. The accountant can see their fee status once the CoFee data is brought in. The parent is invited to follow their child from day one. Nothing has to be re-entered from one area to the next.

Time ties the system together as well. The school's calendar — the academic year, the terms, the holidays — is set in one place, and every other area follows it. Attendance knows which days are school days. Fees know which term they belong to. Progress reports open and close on the dates the school configures. Because there is a single shared calendar, the different areas never drift out of step with one another.

The result is one continuous flow rather than separate islands of work. A new admission, a day's attendance, a fee payment, and a message to a parent are all part of the same connected record of each child — kept live, kept consistent, and visible to exactly the right people.

---

## 3. Business Model

This section explains how the school earns its income — what it charges for, how each charge is worked out, and the key rule that applies to each.

| Revenue Stream | How It Is Billed | Key Rule |
|---|---|---|
| **Academic programmes** (Play Group, Nursery, PP1, PP2) | Per term | The full term fee applies even when a child joins partway through a term. Fees are not split for a partial term. |
| **Day Care** | By a chosen plan | Billed on the child's assigned Day Care plan — monthly, weekly, day-wise, hour-wise, or term-wise. Staff record check-in and check-out times, which support the plans that bill by the day or hour. |
| **Transport** (optional, school-run) | Fixed monthly, by zone | Charged by distance band and trip type, and chosen per child. In this version it is a simple optional fee item the Owner or Principal can switch on for a child — full transport management comes in a later version. |
| **Meals** (optional) | By a chosen plan | A simple optional fee item, off by default and switched on only when a branch starts offering meals. The school does not offer meals today. Full meal management comes in a later version. |
| **Events and one-offs** | One-off charge | Single charges for things like Annual Day or field trips. |

**Fee structure by student category.** Each child belongs to a category — Standard, Staff Child, or Subsidised — and the category decides which fee structure applies to that child.

**Who pays.** By default, the parent or guardian is the payer. A second payer — an employer, trust, or sponsor — can be added for a child whose fees are covered by someone else. The fee is the same no matter who pays; only the payer differs.

**The fee agreed at enrollment stays fixed.** The fee structure recorded when a child enrolls is the agreed basis for their billing. It is not overwritten when fees change in later years.

**Tax on extras.** Tuition is largely tax-exempt. Extra services — transport, meals, materials, and events — may carry GST. Where it applies, the tax is shown on the invoice and on the parent's receipt.

**A check on fee reductions.** Any fee waiver, concession, or refund needs a second person to approve it. The person who records money is never the one who reduces it.

**How fees are collected in this version.** The school continues to collect fees through CoFee. Happy Feet brings that data in so fees sit alongside students and attendance. Collecting payments inside Happy Feet itself — with its own invoices and receipts — comes in a later version.

---

## 4. How Your Business Runs Inside the System

This section follows the school's year from start to finish and shows where each part lives in the system. Read it as the full journey: setting up, taking in new children, running each day, reporting on progress, and moving children forward at year-end.

### 4.1 Setting Up the System at Go-Live

**What happens:** Before the school can use Happy Feet for the first time, its existing information is entered. The Owner and Principal accounts are created. Staff profiles are added — teachers, coordinators, and the accountant. Existing children are entered with their current records and opening fee balances, and the school's CoFee payment data is brought in. This is a one-time setup for each branch. While it runs, the system relaxes its usual checks so part-finished records can be saved, and it holds back automatic alerts so incomplete data doesn't trigger false reminders.

**Who is responsible:** The Owner and the Principal.

**Information involved:** Owner and staff accounts, staff profiles, existing student records, opening fee balances, and imported CoFee payment history.

**Rules that apply:**
- This setup runs once per branch, at go-live.
- Children moved over from the old paper records keep their existing admission number exactly as it was — it is never changed or reissued.
- Staff profiles must exist before classes can be set up, because classes are linked to the teachers who run them.
- The Principal confirms all records are entered and correct before the school switches to normal day-to-day operation.

---

### 4.2 Setting Up Each School Year

**What happens:** Before each academic year begins, the school is configured for the year ahead. This is a nine-step sequence done in order: create the year with its start and end dates; set the term dates and the windows when progress reports can be submitted; define the programmes; create the classes with their capacity and teacher slots; set the fee structures for each programme and student category; set up transport zones if offered; enter the school calendar; build the weekly timetable for each class; and define the developmental milestones and the documents each programme requires.

**Who is responsible:** The Principal, with the Owner.

**Information involved:** Academic year, terms, programmes, classes, fee structures, transport zones, school calendar, timetable, milestones, and required document types.

**Rules that apply:**
- The steps must be completed in order. A later step that depends on an earlier one is blocked until the earlier one is done.
- The fee structure is set per programme and per student category (Standard, Staff Child, Subsidised).
- Setup is completed before the new year begins, so the year is ready when the session starts.

---

### 4.3 Receiving an Admission Application

**What happens:** A new child's application enters the system one of two ways. A parent applies themselves from a public web link, with no login needed. Or the Principal enters the application directly. Admissions are open all year by default. The Owner or Principal can set optional open and close dates to limit applications to a defined period.

**Who is responsible:** The parent (public form) or the Principal (direct entry).

**Information involved:** The application record — the child's and family's details captured at this stage.

**Rules that apply:**
- An application is only a request to be considered. It does not create an enrolled child.
- If the school has set an admissions window, applications sent outside that window are not accepted.
- The public form is open to anyone, so it is treated carefully — it can only create a pending application, nothing more.

---

### 4.4 Reviewing Documents and Assigning a Programme

**What happens:** The submitted documents are reviewed, and the child is assigned to a programme. The Coordinator can review and check the documents as part of this. The Principal assigns the programme, since the programme decides which documents are required and which class the child can join.

**Who is responsible:** The Coordinator reviews documents; the Principal assigns the programme.

**Information involved:** The application, submitted documents, and the assigned programme.

**Rules that apply:**
- A programme must be assigned before the document and capacity checks can be made.
- The required document list depends on the programme.

---

### 4.5 Completing Enrollment

**What happens:** Enrollment turns an applicant into an enrolled child. Four conditions must all be met first: a programme is assigned; the required documents are cleared or individually waived; a class place is available, or an over-capacity place is recorded with a reason; and the child has at least one approved person on their pickup list.

**Who is responsible:** The Principal.

**Information involved:** The application, programme, class place, document checklist, and authorised pickup list. On enrollment, a permanent student record is created.

**Rules that apply:**
- All four conditions must be satisfied. Meeting some but not all does not complete enrollment.
- Each missing document needs its own waiver with a recorded reason. Waiving everything at once is not allowed.
- Going over a class's capacity is allowed only if the Principal records a reason, which is permanently logged.
- For a new child admitted after go-live, the system generates a new admission number automatically.

---

### 4.6 Activating the Parent and Verifying Documents

**What happens:** When a child is enrolled, the parent is invited by email to the parent app. The parent signs in and submits their child's sensitive documents — identity, medical, and a photo. The Owner, Principal, or Coordinator then checks each document and marks it verified. Letting the Coordinator verify means the school's daily document work doesn't wait on core management.

**Who is responsible:** The parent submits; the Owner, Principal, or Coordinator verifies.

**Information involved:** The parent's account, the child's sensitive documents, and each document's status (submitted, then verified).

**Rules that apply:**
- The parent app is useful from the first day of enrollment.
- Sensitive documents move from "submitted" to "verified" only after a staff member checks them.
- Every time a staff member opens a child's sensitive documents, the system records it — so the school can always show who viewed what.

---

### 4.7 Marking Daily Attendance

**What happens:** Each school day, the teacher marks who is present, absent, late, or on a half day. When a child is marked absent, the system sends an automatic text message to that child's main registered phone number, once the attendance is confirmed.

**Who is responsible:** The Teacher.

**Information involved:** The daily attendance record for each child, linked to their class and the school day.

**Rules that apply:**
- Each class has one teacher who is the authoritative marker, so two teachers cannot overwrite each other's marks.
- The absence text is sent once, after the attendance is confirmed.
- Holidays on the school calendar block attendance for that day, with no exceptions.

---

### 4.8 Logging Daily Care

**What happens:** Through the day, the teacher logs each child's meals, naps, activities, and health checks. Parents see this as a daily feed on their own phones, giving them a clear picture of their child's day.

**Who is responsible:** The Teacher.

**Information involved:** The daily care log for each child — meals, naps, activities, and health checks.

**Rules that apply:**
- The daily feed is the main way the school replaces informal updates with a structured, reliable record.
- For day care children, staff also record check-in and check-out times.

---

### 4.9 Managing Fees

**What happens:** The school continues to collect fees through CoFee. Happy Feet brings CoFee's payment data in so the school sees children, attendance, and fees together in one place. Staff can view fee status, export records, and read dashboards showing collection rates and overdue amounts. Happy Feet does not collect payments or issue invoices itself in this version.

**Who is responsible:** The Accountant, with the Owner reviewing dashboards.

**Information involved:** Imported CoFee payments and invoices, each child's fee status, and fee dashboards.

**Rules that apply:**
- Imported fee data is read-only. It is viewed and reported on, never changed inside Happy Feet.
- Each payment is brought in with its source and reference so figures reconcile and nothing is counted twice.
- Fee figures are shown as of the last import, so a fresh import keeps them current.

---

### 4.10 Communicating with Parents

**What happens:** The school sends announcements and messages to parents through one structured channel — the parent inbox and notice board. This replaces scattered personal WhatsApp messages with a single channel that is logged and accountable.

**Who is responsible:** The Principal and Coordinator, and staff sending class-level updates.

**Information involved:** Announcements, inbox messages, and the record of what was sent.

**Rules that apply:**
- Every message is logged, so the school always has a record of what was communicated.
- WhatsApp links are sent by tapping to send manually, and are recorded as generated rather than confirmed delivered.

---

### 4.11 Curriculum and Progress Reports

**What happens:** Teachers prepare lesson plans linked to a programme and class, and publish them to parents. Each term, within a set submission window, teachers draft and submit a lightweight progress report. The Principal or Coordinator reviews and publishes it. Parents see a simple progress view in the app.

**Who is responsible:** Teachers draft and submit; the Principal or Coordinator reviews and publishes.

**Information involved:** Lesson plans, progress reports, and the term submission windows.

**Rules that apply:**
- Progress reports can only be submitted during the term's open window.
- The person who reviews and publishes is never the teacher who drafted it.
- Formal printed progress cards are handed over offline. The app shows a lightweight view only.

---

### 4.12 Year-End Rollover

**What happens:** At the end of the year, children move up to their next class. The Principal reviews the active children, acknowledges any outstanding dues, and confirms the transitions before the new year begins.

**Who is responsible:** The Principal.

**Information involved:** Each child's status, class assignment, and outstanding dues.

**Rules that apply:**
- Rollover is not automatic. The Principal reviews and confirms it.
- Outstanding dues are acknowledged as part of the review.

---

### 4.13 Student Exit — Certificates, Discontinuation, and Transfer

**What happens:** When a child leaves or completes their programme, the school records the exit. If the family needs a Transfer Certificate or a Bonafide certificate, the school issues the physical certificate offline, and the app records that it was issued — who issued it and when.

**Who is responsible:** The Owner, Principal, or Coordinator records the certificate; the Principal handles discontinuation.

**Information involved:** The child's status (completed, discontinued, or transferred) and the record of any certificate issued.

**Rules that apply:**
- Transfer and Bonafide certificates are issued on paper by the school. The app acknowledges and logs them — it does not produce the document.
- A child who has left keeps their original admission number. If they return, the system links back to their existing record rather than creating a new one.
- Once a child has left, the parent's view becomes a read-only archive.

---

### 4.14 Staff Joining, Leave, and Exit

**What happens:** Staff are added by email invitation. The Owner invites Principals, and each Principal invites their own branch's staff. Staff attendance and leave are tracked, and approved leave shows on the timetable as a coverage gap to fill. When a staff member leaves, their access is removed and their open work is reassigned.

**Who is responsible:** The Owner and the Principal.

**Information involved:** Staff profiles, role assignments, attendance, leave balances, certifications, and exit records.

**Rules that apply:**
- Invitations flow top-down: Owner to Principal, Principal to branch staff.
- When someone leaves, their access is switched off, but their record and past work are kept, never deleted.
- Staff certifications are tracked with expiry dates, and the system flags ones that are due to expire.

---

## 5. Who Uses the System — Roles and Actors

Six kinds of people use Happy Feet. Five are staff inside the school. The sixth is the parent. Each plain-English name below is paired with the **system role** — the exact label the system uses for that person. The guiding idea across all of them: the system shows each person only what belongs to their job and their relationship to a child. Authority sits with the Owner and the Principal. The other staff roles carry out the daily work within clear limits.

### 5.1 Owner

**System role:** Admin

**Who they are:** The school owner. The highest authority in the system, across every branch.

**What they can see:** Everything. All branches, all children, all staff, all fees, attendance, communication, and the full record of who did what in the system.

**What they can do:** Run the whole school. Create branches, invite Principals, set up the system, review dashboards across every branch, and step in on any decision. The Owner also sets the rules the other roles work within.

**What they cannot do:** Nothing is closed to the Owner by role. Even so, opening a child's sensitive records or running an export requires re-confirming their identity first, and every such action is recorded.

**Type:** Internal staff.

---

### 5.2 Principal

**System role:** Branch Admin

**Who they are:** The principal, or branch head. The main person running a single branch day to day.

**What they can see:** Everything within their own branch — its children, staff, fees, attendance, communication, and reports.

**What they can do:** Run the branch end to end. Set up the school year, create classes, set fee structures, invite the branch's staff, approve enrollments, verify documents, discontinue a child, acknowledge certificates, review and publish progress reports, adjust the timetable, and approve staff leave.

**What they cannot do:** See or affect any other branch. A few actions stay with the Owner alone — creating new branches and inviting other Principals.

**Type:** Internal staff.

---

### 5.3 Teacher

**System role:** Teacher (assigned as Primary, Assistant, or Temporary on a class)

**Who they are:** A class teacher, assigned to one or more classes. A teacher can be the main teacher for a class (Primary), an assistant (Assistant), or a temporary substitute (Temporary).

**What they can see:** Their own classes. For each child in those classes, they see what they need to do their job — attendance, the daily care log, the child's allergy and medical-alert flag, and their class's progress. They do not see a child's full medical record or identity documents.

**What they can do:** Mark daily attendance for their class, log each child's meals, naps, activities, and health checks, prepare lesson plans, and draft and submit progress reports. The Primary teacher is the one who marks the class's attendance of record.

**What they cannot do:** See other classes or branches. Publish their own progress reports — someone else reviews and publishes them. Change the school's setup or touch fees. A Temporary substitute sees even less — attendance and the daily log only — to keep sensitive information away from rotating staff.

**Type:** Internal staff.

---

### 5.4 Coordinator

**System role:** Coordinator

**Who they are:** The front-desk assistant for a branch. The Coordinator keeps the daily paperwork and front-desk work moving so the Principal isn't pulled into every task.

**What they can see:** The branch's day-to-day operational information — applications, student documents, the timetable, and communication. They have limited access to the school's setup.

**What they can do:** Handle intake, collect documents, review and verify the documents parents submit, run gate operations, send communications, adjust the timetable to cover substitutions, and review and publish progress reports.

**What they cannot do:** Edit or delete anything in the school's setup. Make the final enrollment decision or approve an over-capacity place — those stay with the Principal. Write curriculum or progress reports themselves. Manage fees or staff accounts. Work across other branches.

**Type:** Internal staff.

---

### 5.5 Accountant

**System role:** Accountant

**Who they are:** The finance staff member for a branch.

**What they can see:** The branch's fee picture — payments brought in from CoFee, each child's fee status, and the fee dashboards showing collection rates and overdue amounts.

**What they can do:** Bring in CoFee payment data, view and export fee records, read the fee dashboards, and handle billing-related communication.

**What they cannot do:** Change the imported fee data — it is view-only. Mark attendance, write progress, or change the school's setup.

**Type:** Internal staff.

---

### 5.6 Parent / Guardian

**System role:** Parent / Guardian

**Who they are:** A child's family. A child can have more than one guardian, and each guardian has their own login. Each guardian's access is set by their relationship to the child — whether they can collect the child, whether they receive communications, and whether they are a fee payer.

**What they can see:** Only their own child or children. For each, they see attendance, the daily care feed, a simple progress view, fee status, and school announcements.

**What they can do:** Submit their child's documents, follow the daily feed, view attendance, progress, and fees, and receive school messages. Their access to records is view-only.

**What they cannot do:** See any other child. Change any school record. A guardian marked as excluded — for custody or safeguarding reasons — cannot collect the child and is kept off the pickup list.

**Type:** External user.

---

## 6. Features and Specifications

This is the full list of what Happy Feet does, grouped by area. Everything below is part of the current version unless it appears under **Planned for Later Versions** at the end — those are designed and documented, but built in a future version.

### Access and Security
- Installs on a phone's home screen and opens like any other app; the same app also runs on an office laptop
- Sign in once and stay signed in — a tap to return, not a login every task
- Staff are added by email invitation, top-down (Owner invites Principals; Principals invite their branch's staff)
- A second sign-in step for all staff; optional for parents
- Identity re-confirmation before sensitive actions, such as opening a child's protected records
- Access decided by role, branch, and relationship to a child; nothing is shown unless explicitly allowed
- Built for multiple branches from day one, with each branch's data kept separate
- One person can hold more than one role, or work across more than one branch
- Several guardians per child, each with their own login and permissions (collect the child, receive messages, pay fees, or be excluded)
- At least two Owner accounts kept active; access removed when someone leaves, with their records preserved

### School Setup
- Nine-step start-of-year setup, completed in order
- Academic year with three stages — upcoming, active, and closed
- Terms, with progress-report windows per term
- Programmes: Play Group, Nursery, PP1, PP2, and Day Care
- Classes with capacity and teacher slots
- Weekly timetable per class, with same-day coverage and substitution changes
- School calendar with holidays that block attendance
- Developmental milestones defined per programme
- Required document types defined per programme
- Fee structures set per programme and per student category

### Admissions and Enrollment
- Public self-apply web link for parents, with no login needed
- Applications also entered directly by staff
- Admissions open year-round, with optional open and close windows
- Document review and programme assignment
- Four enrollment checks: programme assigned, documents cleared, class capacity, and pickup list
- Over-capacity place allowed with a recorded reason; each missing document waived individually with a reason
- Waitlist
- A new admission number generated automatically; a migrated child keeps their existing number

### Children's Records and Lifecycle
- Permanent student profiles
- Sensitive documents — identity, medical, and photo — stored separately and access-controlled
- Document verification, from submitted to verified
- Clear, named lifecycle stages from application through to exit
- Year-end rollover to the next class
- Discontinuation and re-admission, with re-admission linking back to the existing record
- Transfer and Bonafide certificate acknowledgment — issued offline, logged in the app with who and when
- Parent-Teacher Meeting notes, kept private to staff

### Staff Records
- Staff profiles, roles, and branch assignment
- Staff attendance
- Leave balances and approval, shown as coverage gaps on the timetable
- Certifications with expiry alerts
- Exit workflow that reassigns open work before access is removed

### Attendance, Daily Care, and Safety
- Daily attendance — present, absent, late, or half-day
- One authoritative marker per class, so edits never clash
- Automatic absence text message to the parent
- Attendance corrections
- Repeated-absence alerts
- Live staff-to-child ratio monitoring, with alerts when a ratio is breached
- Daily care log: meals, naps, activities, and health checks
- Day-care check-in and check-out times
- Parent daily feed
- Authorised pickup: a standing list, parent-issued one-time codes, and a two-person override for exceptions
- Allergy protocol confirmation at meal time
- Welfare concern pathway, with a review deadline and escalation, kept private to staff
- Incident reporting, shared with parents or kept internal at the Principal's decision
- Works on patchy connectivity — saves the entry and syncs when the connection returns

### Curriculum and Progress
- Lesson plans per programme and class, published to parents
- Progress reports, submitted within each term's window
- A simple in-app progress view for parents
- Draft, review, and publish steps — the person who publishes is never the one who drafted

### Fees
- Brings in CoFee payment data, matched and reconciled, with no double-counting
- Each child's fee status in one place
- Fee dashboards: collection rate and overdue aging
- Export of fee records
- Unmatched payments held for review rather than dropped
- Student categories (Standard, Staff Child, Subsidised) and payer routing (parent, employer, or sponsor)

### Communication
- Announcements, including ones scheduled to send later
- Parent inbox and notice board
- WhatsApp tap-to-send links
- Text-message alerts, such as absence
- In-app push notifications
- A record of what was sent, with failed messages shown to staff to follow up

### Reporting, Oversight, and Data Protection
- Role-filtered dashboards — the Owner sees the key numbers: collection, dues, occupancy, admissions, and open safety items
- CSV exports
- An activity log of who did what, across the system
- An Owner-only view of override actions
- A separate log of who viewed sensitive data
- All data stored and backed up in India
- Daily backups with point-in-time recovery
- Records preserved rather than overwritten, keeping a full history
- Removal of a child's personal data on a valid request, keeping a trace that the record once existed

### Planned for Later Versions
*Designed and documented, but built in a future version — not part of the current build.*

- Native fee billing inside Happy Feet — invoices, in-app payment, receipts, and tax handling — which replaces CoFee
- Full transport management: routes, stops, boarding and drop confirmation, and a transport staff role. In the current version, transport is only an optional fee item
- Full meal management: daily menus, allergy-checked meal entries, and consumption tracking. In the current version, meals are only an optional fee item
- Sharing children's photos with parents, after a data-protection review
- Automated WhatsApp messaging
- Custom staff roles built by the Owner
- Government submissions (APAAR, PEN, and UDISE+)
- Predictive analytics and additional reporting
- An app-store version of the app

---

## 7. The System's Modules and the Data They Own

Behind the areas described earlier, the system is built as a set of self-contained modules. Each module owns its own records and is fully responsible for them. Modules share information through clear connections — never by reaching into one another's records. This keeps the system organised, keeps each kind of information consistent, and makes the school safe to grow. Here is each module and the information it owns.

| Module | What It Handles | Information It Owns |
|--------|----------------|---------------------|
| **Identity & Access** | Who each person is and what they are allowed to do | User accounts, role assignments, branches, sign-in sessions, and the access rules |
| **People & Records** | Children, families, and staff | Student records, guardian records, staff records, and the separate sensitive-document store |
| **School Setup** | The academic framework and teaching plans | Academic year, terms, calendar, programmes, classes, timetable, milestones, lesson plans, and progress reports |
| **Admissions** | Taking in new children, up to the point of enrollment | Admission applications and the waitlist |
| **Attendance, Daily Care & Safety** | The school day and child safety | Attendance records, daily care logs, authorised pickup lists, allergy confirmations, welfare concerns, and incident reports |
| **Fees** | The money picture, brought in from CoFee | Imported payments and invoices, each child's fee status, and the fee dashboards |
| **Communication** | Reaching parents | Announcements, inbox messages, the notice board, and notification records |
| **Administration & Reporting** | Oversight across the whole system | Dashboards, exports, settings, and the activity log |

Each module is the only one that writes its own records. Only People & Records creates a student; only Fees touches imported payments; only Attendance marks a child present. When one area needs information from another, it asks through a defined connection rather than editing the other's records directly. This is what keeps the school's information trustworthy and is why a second branch — or a future feature — can be added without disturbing what already works.

---

## 8. Key Information the System Manages

The system keeps a set of connected records. Each one represents something real in the school — a child, a class, a payment, a day's attendance — and they link together so the full picture of each child is always in one place.

| Information | What It Represents | Key Details It Holds | How It Connects |
|------------|-------------------|----------------------|-----------------|
| **Student Record** | A child enrolled at, or applying to, the school | Name, date of birth, admission number, programme, class, student category, status | The centre of the system — linked to guardians, attendance, daily care, fees, progress, and documents |
| **Guardian Record** | A parent or family member connected to a child | Contact details, login, and what they may do — collect the child, receive messages, pay fees | Linked to one or more children; sets what each guardian can see and do |
| **Staff Record** | A person employed at the school | Name, role, branch, attendance, leave balance, certifications and their expiry dates | Linked to the classes they run and the branch they work in |
| **Admission Application** | A request to admit a child, before they become a student | Child and family details, chosen programme, submitted documents, application status | Becomes a Student Record once enrollment is completed |
| **Sensitive Documents** | A child's protected personal records | Identity documents, medical records, and photo | Kept apart from the main record and shown only to staff who need them; every view is recorded |
| **Programme** | A course of study the school offers | Name (Play Group, Nursery, PP1, PP2, Day Care), billing model, required documents, milestones | Linked to classes, fee structures, and students |
| **Class** | A group of children taught together | Name, capacity, assigned teachers, weekly timetable | Linked to a programme, its teachers, and its students |
| **Academic Year & Calendar** | The school's yearly framework | Year start and end, terms, holidays, progress-report windows | The single source of dates for attendance, fees, and progress across the system |
| **Attendance Record** | A child's daily presence | Date, child, class, status (present, absent, late, half-day) | Linked to the child and class; triggers the absence message to the parent |
| **Daily Care Log** | The record of a child's day | Meals, naps, activities, health checks, and day-care check-in and check-out times | Linked to the child; shown to parents as the daily feed |
| **Authorised Pickup List** | The people allowed to collect a child | Approved collectors and their verification details; parent-issued one-time pickup codes | Linked to the child; checked at the gate before release |
| **Fee Record** | A child's fee and payment picture | Imported payments, invoice references, amounts paid and due, status | Brought in from CoFee, linked to the child; feeds the fee dashboards |
| **Progress Report** | A child's progress for a term | The teacher's assessment against the programme's milestones | Linked to the child and term; reviewed, published, and shown to the parent |
| **Lesson Plan** | A teacher's plan for a class | Activities and learning content for a programme and class | Linked to the programme and class; published to parents |
| **Announcement & Message** | School communication to parents | Announcements, inbox messages, and the record of what was sent | Linked to the branch or class; logged for accountability |
| **Incident & Welfare Record** | A safety or wellbeing record about a child | What happened, who recorded it, its status, and follow-up | Linked to the child; permanent. Welfare records are never shown to parents; an incident is shared only if the Principal decides to |
| **Activity Log** | The system's record of who did what | Each action, the person who did it, and when | Spans every area of the system; the Owner's record of accountability |
| **Branch** | A school location | Name and status | The top unit that children, staff, and fees belong to |

---

## 9. How Your Data Is Stored and Protected

The school holds personal records about children — names, documents, medical notes, photos. How that information is stored and protected matters, both by law and for the trust parents place in the school. This section explains, in plain terms, how Happy Feet handles it.

**Your data is stored in India.** All of the school's records — children, staff, fees, attendance, and documents — are stored and backed up on infrastructure located in India. This meets India's data-protection rules on keeping data within the country. The one exception is the sign-in service, which holds only email addresses and phone numbers used to log in; those are held by the login provider outside India. Every sensitive record about a child stays in India.

**Your data is backed up every day.** The system makes automatic daily backups and can be restored to an earlier point in time if something goes wrong. A school cannot afford to lose its financial or child records, so backups are treated as non-negotiable, not optional.

**The most sensitive records are kept apart.** A child's identity documents, medical records, and photo are stored separately from everyday records and are locked down more tightly. Only staff with a genuine need can open them. Each time a staff member opens one, the system records who looked, at what, and when — so the school can always answer who has seen a child's sensitive data. Opening these records also requires the staff member to confirm their identity again.

**Records are preserved, not overwritten.** When something is corrected, the system adds the correction alongside the original rather than erasing it. This keeps a complete and trustworthy history of what happened and when — which protects both the school and the parent.

**Each person sees only what they should.** The system decides what someone can see by combining their role, their branch, and their relationship to a child. Sensitive information is shown only inside the system, to staff who are authorised to see it.

**Parents have rights over their child's data.** Under India's data-protection law, a parent can ask to see, correct, or remove their child's personal information. The school handles these requests. When information must be removed, the system can erase the sensitive content while keeping a simple record that the entry once existed — so the history stays intact without holding on to data that should be gone.

**How long data is kept.** Records are kept for as long as the school needs them to meet its legal and operational obligations, and are removed only by a deliberate, recorded decision — never silently. ⚠️ Needs client input: confirm how long the school must keep financial records and child-safety records, so exact retention periods can be set.

---

## 10. How the System Is Organised

### 10.1 Organisational Structure

The system is organised around a single owner account at the top. Beneath the Owner sit the school's branches. Each branch runs its own daily operations through its own staff, while the Owner sees across all of them.

Today the school has one active branch, with a second branch planned. The system is built for more than one branch from the start, so the next branch can be added without rebuilding anything.

Each branch has a Principal who runs it. Under the Principal are the branch's Coordinators, Teachers, and Accountant. Parents sit alongside the branch their child belongs to, with access to their own child only.

```
ADMIN [Owner] (across all branches)
   │
   ├── Branch 1  (active)
   │      ├──BRANCH ADMIN [Principal] 
   │      │     ├── Coordinator(s)
   │      │     ├── Teacher(s)
   │      │     └── Accountant
   │      └── Parents  (each linked to their own child)
   │
   └── Branch 2  (planned)
          └── (same structure once it opens)
```

Every level can have more than one person. There can be more than one Owner, more than one Principal per branch, and many teachers. A staff member can also work across two branches if needed.

### 10.2 How Data Is Managed and Separated Between Branches

Every operational record in the system is tagged with the branch it belongs to. A child, a class, an attendance mark, a fee record, a message to a parent — each one carries the name of its branch. That tag stays with the record wherever it goes. This is the foundation of how branches are kept apart.

When a staff member uses the system, it checks three things together before showing anything: what their role allows, which branch they are assigned to, and their relationship to a particular child. It then shows only the records whose branch tag matches their assignment. So a Teacher in Branch 1 only ever sees Branch 1 records — and only their own classes within it. A Principal sees their whole branch. No staff member below the Owner can see or touch another branch's data.

The Owner is the single exception. The Owner is assigned to every branch, so the Owner sees everything. The Owner's dashboards add the branches together into one school-wide picture — total enrollment, collection, occupancy — while each Principal sees those same numbers for their own branch only.

Some staff legitimately work in more than one branch. When that happens, they are given a separate assignment for each branch. They see each branch's data only when working in that branch, and only for the branches they are assigned to — never any others.

Each branch is set up with its own programmes, classes, and fee structures, following the same overall approach across the school. The day-to-day records — children, staff, attendance, fees, and communication — stay specific to their own branch. Branches do not share these records with one another.

It is important to understand that this is one system, not separate copies. The separation comes from the branch tag and the access rules, not from running a different system per branch. That is what keeps each branch's data private to it while still letting the Owner see the whole school in one place. And because the tag is on every record from day one, opening the second branch needs no rebuilding — the new branch is created, its staff are invited, its children are entered, and the same separation applies to it automatically.

---

## 11. How People Get Access — Invitations and Accounts

Access to Happy Feet is given by invitation. No one signs up on their own. A person is invited by someone above them, accepts the invitation, and lands in the system with access limited to their role and branch. Everyone signs in once and stays signed in — they tap the app icon to return, rather than logging in for every task.

### 11.1 How Staff Are Invited

Staff are invited from the top down. The Owner invites Principals. Each Principal then invites their own branch's staff — Coordinators, Teachers, and the Accountant. This keeps each branch's team under the person who runs that branch.

1. The Owner or Principal creates the new staff member's profile, enters their email address, and chooses their role and branch.
2. The system sends an invitation email with a secure link.
3. The staff member opens the link, signs in, and sets up a second sign-in step for security — a code from an authenticator app on their phone, along with backup codes.
4. They land in the system with access limited to the exact role and branch they were given.

Until the staff member accepts, their invitation stays pending and their access is not active. The second sign-in step is required for all staff, because their accounts reach fees, admissions, and children's records. For certain sensitive actions — such as opening a child's protected documents — the system asks the staff member to confirm their identity again, even while they are already signed in.

The school always keeps at least two Owner accounts. This makes sure the school is never locked out if one Owner is unavailable.

### 11.2 How External Users Get Access

Parents are the external users, and they are invited automatically. When a child is enrolled, the system emails that child's parent an invitation to the parent app. The parent does not apply for access — enrollment creates it.

The parent signs in with their email, using a secure link or a one-time code. The extra security step is available to parents but not required. Once signed in, the parent sees only their own child — attendance, the daily feed, a simple progress view, fee status, and school announcements. They can submit their child's documents, but their access to records is view-only.

A child can have more than one guardian. Each guardian receives their own invitation and their own login, and each one's access matches their relationship to the child.

### 11.3 Removing Access

When a staff member leaves, their access is removed by the Owner or Principal. Any work still in their hands — open concerns, unfinished progress drafts, classes they marked attendance for — is reassigned to someone else first. Their account is then switched off. Their profile and everything they recorded stay in the system. Records are never deleted, so the school keeps a complete history.

When a child leaves the school, that child's parent access changes to a read-only archive. The parent can still see their child's past records for the period the school keeps them, but the account is no longer active for day-to-day use.

---

## 12. Services and Subscriptions

> **This section will change.** The final list depends on deployment choices still being worked out — in particular, where the application is hosted. New services may be added at any time as the build progresses, and this list is updated here whenever that happens. Treat it as a living list, not a final one.

Happy Feet runs on a few outside services. Some are set up by the developer but billed to you, the school. Others are accounts the school owns directly. This is the list of what you pay for to keep the product running. The app itself installs on phones with no app-store fee, and parent notifications inside the app are free — so they are not listed here.

| Service | What It Does | Who Manages It | Cost | If It Lapses |
|---------|-------------|----------------|------|--------------|
| **Application Hosting** *(not yet decided)* | Keeps the application running and reachable on the internet for all staff and parents | Developer sets it up; billed to the school | ⚠️ To be decided — the hosting approach is still being worked out. One option (Vercel) is under consideration, but its fit for a school of this scale has not been confirmed. We will reason through this and settle it later | The application goes offline for everyone |
| **Database, Storage & Backups** (Supabase, Mumbai) | Stores all the school's data — children, staff, fees, attendance, documents — with daily backups, all held in India | Developer sets it up; billed to the school | Monthly subscription, kept small for the current size · ⚠️ Needs client input: confirm the monthly figure | The data cannot be reached until the service is restored |
| **Secure Login** (Clerk) | Manages secure sign-in, the extra security step for staff, and keeping people signed in | Developer sets it up; billed to the school | Free for basic sign-in and authenticator-app security; paid as the number of users grows · ⚠️ Needs client input: confirm the tier | No one can sign in to the system |
| **Text Message Service** (MSG91) | Sends text messages to parents, such as absence alerts | The school owns the account | Charged per message sent · ⚠️ Needs client input: confirm the monthly message budget | Text alerts to parents stop sending. In-app notifications still work |
| **Fee Collection Software** (CoFee) | The school's existing software for collecting and recording fees. Happy Feet imports its data so fees sit alongside students and attendance | The school owns the account (already in use today) | The cost the school already pays for CoFee · ⚠️ Needs client input: confirm | Happy Feet can no longer bring in up-to-date fee data, and the fee dashboards become out of date |
| *(open)* | *Reserved for a service added later as the build progresses* | — | — | — |
| *(open)* | *Reserved for a service added later as the build progresses* | — | — | — |

**A note on a future service:** When the school later moves fee collection into Happy Feet itself, a payment gateway (Razorpay) will be needed to take payments directly. That is not required for this version — fees stay on CoFee for now — so there is nothing to pay for it yet. It will be added to the list above when the time comes.

⚠️ Needs client input: confirm who owns and pays each account above, so billing details and renewals sit with the right person.

---

## 13. Your Responsibilities as Application Owner

Happy Feet handles the heavy lifting, but a few things stay the school's responsibility. These keep the system accurate, secure, and running. They are grouped by how often they come up.

### 13.1 Ongoing — Day to Day

- Make sure teachers mark attendance every school day, and follow up on any class that hasn't been marked.
- Check the day's picture — who is present, who is absent, and any open safety items that need attention.
- Verify the documents parents submit, so each child's record is complete and trusted. The Coordinator or Principal can handle this.
- Keep parent communication flowing through the system's inbox and announcements, rather than personal WhatsApp.
- Review any exception a staff member records — such as an over-capacity place or a pickup override — and confirm it was justified.

### 13.2 Regular — Monthly or Per Term

- Bring in the latest CoFee fee data regularly, so the fee status and dashboards stay current.
- Review the collection and overdue dashboards, and follow up on outstanding fees.
- Open and close each term's progress-report window, and make sure progress reports are reviewed and published.
- Add or deactivate staff accounts as people join or leave the team.
- Check which staff certifications are due to expire, and arrange renewals.
- Confirm the outside service invoices — hosting, database, login, and text messages — are being paid on time.

### 13.3 Periodic — Annually or at Key Milestones

- Complete the nine-step setup before each new school year, in order.
- Run the year-end rollover — review active children, acknowledge outstanding dues, and confirm each child's move to the next class.
- Renew the outside services and confirm billing details are up to date.
- Review all user accounts and remove anyone who no longer needs access.
- Archive or export the records of children who have left, in line with how long the school keeps them.
- Confirm at least two Owner accounts are active, so the school is never locked out.

### 13.4 What to Do When Something Goes Wrong

When the system has a problem, the path depends on what kind of problem it is. Some issues a Principal or Owner can fix directly from inside the system. Others need the developer. When contacting the developer, describe what happened, what you expected, and include a screenshot where possible.

| Situation | Who to Contact | How | Expected Response |
|-----------|---------------|-----|-------------------|
| The system is down or won't open for anyone | Developer | ⚠️ Needs client input: agree a channel (e.g. email or WhatsApp) | ⚠️ Needs client input: agree a response time |
| A staff member or parent cannot sign in | Owner or Principal resets their access first; Developer if it persists | From inside the system | Usually immediate — no developer needed |
| Data looks wrong or is missing | Developer | Email with a screenshot and the child or record affected | ⚠️ Needs client input: agree a response time |
| Fee figures look out of date | Accountant brings in a fresh CoFee import first; Developer if the import fails | From inside the system | After a successful import |
| Absence texts are not reaching parents | Check the text-message account has balance; Developer if it still fails | ⚠️ Needs client input: agree a channel | ⚠️ Needs client input: agree a response time |

⚠️ Needs client input: confirm the developer's support contact, the hours support is available, and the expected response times — then fill these into the table above.

---

## 14. Assumptions to Verify

> **Instructions for the client:** Please read each item below and confirm whether it is correct. If any assumption is wrong, note the correction so the system can be adjusted before it affects your operations.

### 14.1 No separate admission fee

**What was assumed:** The school does not charge a separate registration or admission deposit. Fees are term-based instalments tied to the programme.

**Why it matters:** If a separate admission fee or deposit exists, the fee setup must include it, or it will be missed at admission.

- [ ] Correct as stated
- [ ] Needs correction: _______________

### 14.2 Full term fee for mid-term admissions

**What was assumed:** A child who joins partway through a term is charged the full term fee. Fees are not split for partial terms.

**Why it matters:** If the school charges part of a term for mid-term joiners, the fee approach is different from what is set up.

- [ ] Correct as stated
- [ ] Needs correction: _______________

### 14.3 The second branch matches the first

**What was assumed:** The planned second branch is under the same ownership and uses the same programmes and fee approach as the current branch.

**Why it matters:** If the second branch will have its own programmes or its own fees, the setup must allow each branch to differ.

- [ ] Correct as stated
- [ ] Needs correction: _______________

### 14.4 Transport is optional and billed separately

**What was assumed:** Transport is chosen per child and billed on its own, not bundled into a single programme fee.

**Why it matters:** If some children receive transport as part of a combined fee, billing must be handled differently.

- [ ] Correct as stated
- [ ] Needs correction: _______________

### 14.5 Meals are not offered yet

**What was assumed:** The school does not currently offer meals. The meal option stays switched off until the school decides to offer them.

**Why it matters:** If meals are needed at go-live, the meal option must be set up from the start.

- [ ] Correct as stated
- [ ] Needs correction: _______________

### 14.6 Fees stay on CoFee in this version

**What was assumed:** The school keeps collecting fees through CoFee. Happy Feet only brings in CoFee's data to view and report on — it does not take payments or issue invoices itself yet.

**Why it matters:** If the school expects to collect payments inside Happy Feet now, that is a larger scope than this version covers.

- [ ] Correct as stated
- [ ] Needs correction: _______________

### 14.7 Absence alerts go out by text message

**What was assumed:** When a child is marked absent, the parent is alerted by text message, which is charged per message.

**Why it matters:** Text messages cost money. The school may prefer free in-app alerts instead, which changes how parents are notified.

- [ ] Correct as stated
- [ ] Needs correction: _______________

### 14.8 The child's Aadhaar may not be collected

**What was assumed:** No part of this version requires a child's national identity number (Aadhaar), so it may not be collected at all.

**Why it matters:** Collecting a young child's Aadhaar carries legal responsibility. If it is not needed, it is safer not to hold it.

- [ ] Correct as stated
- [ ] Needs correction: _______________

### 14.9 Government student IDs are not handled yet

**What was assumed:** The national student identifiers (APAAR and PEN) and government portal submissions are not collected or filed through the system in this version.

**Why it matters:** If the school must collect or submit these now, that is additional work to plan for.

- [ ] Correct as stated
- [ ] Needs correction: _______________

### 14.10 Photos are stored but not shared

**What was assumed:** Children's photos are stored for identification and pickup, but are not shared with parents through the system in this version.

**Why it matters:** Sharing photos needs a data-protection review first. If the school expects photo sharing now, that review must happen before it can be turned on.

- [ ] Correct as stated
- [ ] Needs correction: _______________

---

## 15. Glossary

| Term | Plain English Definition |
|------|--------------------------|
| **Academic Year** | The school's yearly cycle, divided into terms, that all dates and operations follow. |
| **Admission Number** | The unique number that identifies a child at the school; kept for life and never changed. |
| **Application** | A request to admit a child, recorded before they become an enrolled student. |
| **Authenticator App** | A free app on a staff member's phone that generates the security code used as the second sign-in step. |
| **Authorised Pickup List** | The list of people approved to collect a child from the school. |
| **Bonafide Certificate** | A formal letter confirming a child is enrolled, often needed for banks, travel, or a sibling's admission. |
| **Branch** | A single school location; the school has one active branch and a second planned. |
| **CoFee** | The school's existing fee-collection software, whose data Happy Feet brings in. |
| **Coordinator** | The front-desk staff member who handles intake, documents, gate, and communication for a branch. |
| **Daily Care Log (Daily Feed)** | The running record of a child's day — meals, naps, activities, health checks — shown to parents. |
| **Dashboard** | An on-screen summary of key numbers, such as fee collection or attendance. |
| **Day Care** | Childcare that runs on its own billing plan and daily record, sharing the same platform. |
| **Enrollment** | The point at which an applicant becomes an enrolled child, once all four conditions are met. |
| **Go-Live Setup (Migration Mode)** | The one-time setup when a branch's existing records are first entered into the system. |
| **Guardian** | A parent or family member linked to a child, with their own login and set permissions. |
| **Incident** | A formally recorded event involving a child; the Principal decides whether to share it with parents. |
| **Installable App** | An app added to a phone's home screen from the web, opening like any other app without an app store. |
| **Milestone** | A developmental goal set for a programme, used to assess a child's progress. |
| **Owner (Admin)** | The school owner; the highest authority, across all branches. |
| **Parent App** | The part of the system parents use to follow their own child. |
| **Pickup Code** | A one-time code a parent creates in the app to authorise a one-off pickup. |
| **Principal (Branch Admin)** | The person who runs a single branch day to day. |
| **Programme** | A course of study the school offers — Play Group, Nursery, PP1, PP2, or Day Care. |
| **Progress Report** | A child's termly assessment against milestones, reviewed and published to the parent. |
| **Rollover** | The year-end process of moving children up to their next class. |
| **Sensitive Documents** | A child's protected records — identity, medical, and photo — kept apart and access-controlled. |
| **Student Category** | The grouping (Standard, Staff Child, Subsidised) that decides which fee structure applies to a child. |
| **Submission Window** | The set period each term when teachers can submit progress reports. |
| **Term** | One of the three parts the school year is divided into. |
| **Transfer Certificate** | A formal document the school issues when a child leaves, confirming their transfer. |
| **Two-Step Sign-In** | A sign-in that needs a password plus a security code; required for all staff. |
| **Welfare Concern** | A teacher-raised concern about a child's wellbeing; a permanent record never shown to parents. |

---

> **Note to client:** This document describes your system as it was designed and built. It is not a legal contract. If anything in this document does not match how your business actually works, please flag it so the system can be corrected before it causes problems in your operations.
>
> **For technical documentation** — architecture, data models, business rules, and developer references — refer to the separate technical documentation maintained by the development team. This document intentionally contains no technical detail.

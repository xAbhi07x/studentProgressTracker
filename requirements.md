School Student Progress Tracker
Product Requirements and Functional Specification
Version: 1.5 — Consolidated Requirements Baseline with Approved Planning Decisions
Date: 27 October 2026
Status: Approved for implementation planning. Application implementation has not started.
Audience: School leadership, client stakeholders, developers, QA, and AI coding assistants, including GitHub Copilot
1. Purpose and Document Use
This document defines the agreed Version 1 requirements for the School Student Progress Tracker. It serves as the shared reference for product decisions, implementation, testing, and future maintenance.
Developers and AI coding assistants must use this document as the requirements baseline. They must not silently invent business rules when a requirement is unclear. Unresolved technical decisions and proposed changes should be documented separately. This specification must be updated whenever an approved requirement changes.
Related documents: decision-log.md records approved decisions, provisional operational defaults, and items awaiting legal or security review. tech-stack.md summarises the approved technology stack. .github/copilot-instructions.md contains persistent instructions for AI coding assistants. If these documents conflict with this specification, raise the conflict for resolution rather than choosing silently.
1.1 Requirement Status Labels
•	Confirmed requirement: Agreed product behavior.
•	Recommended default: A practical rule adopted to close a requirements gap; it may be revisited if the school has a conflicting real-world need.
•	Deferred technical decision: Intentionally not decided in this document; resolve during technical design.
•	Pre-launch action: Must be completed before real student data is used.
1.2 Source and Reconciliation Note
This specification consolidates the supplied product and technical specification with the decisions recorded during its review. References to the original Sections 10 and 12 could not be recovered from the supplied file. Their topics have been reconstructed as reliability/error handling and future features; they are not represented as recovered original text.
2. Product Overview
The School Student Progress Tracker is a mobile-first web application that replaces paper-based skill-progress registers. Teachers record individual students' learning progress, review observations and history, and use AI-supported student and class insights. The principal administers school setup and reviews progress across classes.
2.1 School Context
•	Approximately 100 students.
•	Four classes (grades): Pre-Nursery, Nursery, LKG, and UKG, with one section per grade in Version 1 (see Section 4.5).
•	Approximate age range: 2–6 years.
•	Current process: paper registers with skill checkpoints rather than examination marks.
•	Teachers primarily use smartphones.
•	The school has a low-to-medium budget and no dedicated technical staff.
•	The interface must support English and Hindi.
•	The application requires internet connectivity. Version 1 is not offline-first.
•	School time zone: Asia/Kolkata. Dates such as “today”, backdating limits, note dates, and monthly AI-usage periods are determined in this time zone.
2.2 Goals
1.	Replace paper registers with a per-student skill record.
2.	Preserve every progress change with its observation date and author.
3.	Show progress over time and by subject, including charts.
4.	Let teachers ask questions about an individual student's progress and receive evidence-based explanations and teaching suggestions.
5.	Provide class-level dashboards, AI insights, and conversational class-level AI.
6.	Give the principal school-wide oversight and the ability to manage classes, subjects, skills, users, academic years, and student promotion without developer intervention.
2.3 Version 1 Scope
Included
•	Teacher and principal/admin accounts.
•	Student, guardian, class, enrollment, academic-year, and term administration.
•	Class-specific skill lists under school-wide subjects.
•	Skill assessment and append-only progress history.
•	Teacher observations.
•	Student and class dashboards and charts.
•	Student-level AI chat and draft reports.
•	Class-level AI insights and conversational chat.
•	Report editing, Admin approval, revision history, and printing or saving approved reports as PDFs.
•	CSV student import with validation and row-level feedback.
•	Audit log, access controls, backup/recovery procedures, and English/Hindi interface.
Excluded from Version 1
•	Parent login or parent-facing portal.
•	Fees, attendance management, timetable, and examination marks.
•	Offline-first entry or background synchronization.
•	Voice-based assessment entry.
•	Importing old paper registers from photographs.
•	Student photo upload, storage, or display.
•	Bulk student-data exports, CSV downloads, and export formats other than PDFs of approved reports. Student CSV import remains in scope.
3. Users, Roles, and Access
There are exactly two application roles: Teacher and Admin (principal). Parents and guardians are records, not application users. There is no public sign-up.
3.1 Account Management
•	Only an Admin can create accounts, including teacher and Admin accounts.
•	Multiple individual Admin accounts are supported. Each Admin must have a personal account; shared Admin credentials are prohibited.
•	The admin can edit teacher details, assign classes by academic year, and deactivate a teacher.
•	Deactivation disables sign-in and access. It does not automatically delete the profile or historical records; these are kept only for their authorised retention period (Section 11.6) and are not ordinarily deleted through application workflows.
•	Historical assessments, observations, reports, and audit events retain their original author attribution.
•	Class assignments are managed per academic year. A teacher may have multiple classes, and a class may have multiple teachers.
•	Assignment changes take effect immediately. Removing access to one class must not remove access to another class to which the teacher remains assigned.
•	Authorization must be enforced at the database and server layers, not only by hiding UI elements.
•	Password recovery is Admin-managed; see Section 11.2.
3.2 Permission Matrix
Action	Teacher	Admin
View students and their historical records	Only students currently enrolled in currently assigned classes (Section 3.3)	All classes
Create/edit student records, enrollments, transfers, and withdrawals	No	Yes
View guardian information	No	Yes
Assess skills	Assigned classes	All classes
View and add observation notes	Assigned classes	All classes
Delete observation notes (Section 11.6)	Own notes, in assigned classes only	Any note
Use student-level AI chat	Assigned students only	All students
Generate and edit student report drafts	Own drafts, for assigned students only	All students
Approve and finalise student reports	No	Yes
Print or save approved reports as PDF	Assigned students only	All students
View class charts, insights, and class AI chat	Assigned classes only	All classes
View cross-class principal dashboard	No	Yes
Manage subjects and official skills	No	Yes
Manage classes, academic years, and terms	No	Yes
Promote students and manage enrollments	No	Yes
Create/deactivate users, assign classes, and manage password recovery	No	Yes
View audit log	No	Yes
Change school settings, thresholds, and AI usage limits	No	Yes
Security rule: All endpoints, database queries, PDF outputs, cached results, and AI tool calls must enforce the same role and class-assignment boundaries.
3.3 Teacher Access to Students and History
•	Teachers may access a student's records, including all historical assessments, observations, reports, and charts, only while the student is currently enrolled in a class currently assigned to that teacher.
•	When the student is no longer enrolled in an assigned class, or the teacher's assignment ends, the teacher loses access to all records for that student, including historical records.
•	The Admin retains school-wide access.
•	This restriction applies consistently to views, searches, dashboards, reports, PDF outputs, cached results, and AI features.
4. Student, Guardian, and Enrollment Requirements
4.1 Student Record
The student record should contain:
•	Full name: Required.
•	Admission number: Required and unique.
•	Date of birth: Required where available; allow an explicitly marked unknown value rather than inventing a date.
•	Gender: Optional.
•	Admission date: Required.
•	Student status: active, left, or passed_out.
•	Photo: No photo field or photo storage in Version 1.
The student's current class is derived from enrollments, not stored as an independent current-class field.
4.2 Parent/Guardian Records
Guardian information must be stored separately from the general student record.
Fields
•	Guardian name.
•	Relationship to student.
•	Phone number.
•	Email address (optional).
•	Primary-contact flag.
Rules
•	Support multiple guardians per student.
•	One guardian may be designated as the primary contact.
•	Guardian information is visible only to Admin.
•	Teachers must not access guardian details through the UI, database, API, search, exports, logs, or AI tools.
•	Guardian information must never be sent to an AI provider.
•	Do not collect home addresses or government identification in Version 1.
•	No parent account or portal is provided.
Privacy requirement: Before launch, the school must determine and implement appropriate parent/guardian notice, consent, retention, and other legal obligations with qualified advice.
4.3 Enrollment and Academic Years
•	Only the Admin manages enrollments, transfers, withdrawals, promotion, classes, academic years, and terms.
•	Each enrollment period links one student to a class and academic year, with roll number, start date, and optional end date. Enrollment history is dated and preserved.
•	A student may have more than one enrollment period within an academic year, for example after a transfer or a return, but a student's enrollment periods must never overlap.
•	A transfer means moving a student between classes within the school. The Admin closes the current enrollment period and opens a new one in the destination class; existing history stays associated with the original class. Leaving the school is a withdrawal, not a transfer.
•	The Admin configures academic years and terms. Dates must be validated: start dates must precede end dates, academic years must not overlap, and terms must not overlap within an academic year.
•	Terms belong to an academic year.
•	Exactly one academic year is marked current.
•	A student may join mid-year.
•	When a student leaves, mark the student as left and close the enrollment. The student's records are preserved only for their authorised retention period (Section 11.6); they are not ordinarily deleted through application workflows, subject to the approved retention policy and applicable legal requirements.
•	If a former student returns, reuse the existing student record and create a new enrollment period. Do not create a duplicate record solely because the student returned.
•	Historical assessments remain associated with the class/year context in which they were recorded.
4.4 Promotion
The admin reviews the active students in each class and chooses one of the following:
•	Promote: Enroll the student in the next class for the new academic year.
•	Hold back: Enroll the student in the same class for the new academic year.
•	Pass out: Available for UKG; set status to passed_out and do not create a new enrollment.
Rules
•	Promotion displays a review screen and requires confirmation.
•	Promotion runs transactionally; failure must not leave partially promoted records.
•	Close the old enrollment and create the new enrollment.
•	Prevent duplicate or conflicting enrollments.
•	A retry must not create duplicate enrollments.
•	New-year skill progress begins as Not assessed for the new class's skills.
•	Historical progress remains available and is labelled with its original class and academic year.
•	Teacher assignments do not carry over automatically; the admin assigns teachers for each new year.
•	A correction to a mistaken promotion must preserve an audit trail.
4.5 Class Sections
•	Version 1 supports one section per grade: Pre-Nursery, Nursery, LKG, and UKG.
•	Each grade has its own student roster and teacher assignments.
•	The data model must allow multiple sections per grade to be introduced later without a fundamental redesign.
•	Teacher access is restricted to the classes currently assigned to them; the Admin retains school-wide access.
5. Subjects, Skills, and Assessment Model
5.1 Structure
•	A subject is shared across the school and has English and Hindi names.
•	A skill belongs to exactly one subject and one class.
•	The skill list for a subject may differ by class. For example, LKG English and UKG English can have different skills.
•	A learning-area/category grouping may be used in the UI but does not need to be a separate database entity in Version 1.
•	Only the Admin can create, edit, reorder, or archive official subjects and skills.
•	Teachers assess students using the approved official list. Teacher-created skill proposals are not included in Version 1.
5.2 Skill Quality
Each skill must describe a single, specific, observable, assessable ability.
For example, avoid combining multiple abilities into a single skill if they can be assessed independently. The school should review its paper-register checkpoints and split combined checkpoints where necessary before loading the initial skill lists.
Each skill has:
•	English name.
•	Hindi name.
•	Subject.
•	Class.
•	Sort order.
•	Active/archived status.
•	Optional description.
•	Creator attribution.
5.3 Starting Subjects
The initial subject list is editable by the Admin:
1.	English
2.	Hindi
3.	Maths
4.	EVS
5.	Rhymes and Stories
6.	Drawing and Craft
7.	Motor Skills
8.	Behaviour and Social
The actual skill lists must be taken from the school's paper registers and loaded as seed data before launch.
5.4 Assessment Levels
Level	Stored value	Meaning
Not assessed	No entry exists	The skill has not been assessed yet. Excluded from percentages.
Not yet	1	The child cannot yet perform the skill or needs full help.
Developing	2	The child can perform the skill partly or with help.
Achieved	3	The child performs the skill independently.
Critical distinction: Not assessed is not the same as Not yet. A newly added skill must not make every student appear to be performing poorly.
5.5 Recording Progress and History
1.	Every assessment update creates a new row in progress_entries.
2.	Each row records the student, skill, new level, observation date, original recording timestamp, author, and whether it is a confirmation. If a previous-level value is stored, it is only a snapshot of the level displayed to the user when the entry was recorded; it must not be used to derive history, ordering, or decline.
3.	Progress entries are append-only during their authorised retention period: no application user, including admins, can edit or delete them. They are not ordinarily deleted through application workflows, subject to the approved retention policy and applicable legal requirements. Permanent retention must not be assumed. Where deletion or anonymisation is legally required, it is carried out only through the controlled process in Section 11.9.
4.	Assessments for the same student and skill are ordered by observation date, then by the original recording timestamp, then by a stable unique identifier. The recording timestamp is system-generated, immutable, and never supplied or edited by users.
5.	The current level of a skill is the last entry in this ordering. The level as of any past date is the last entry, in this ordering, whose observation date is on or before that date.
6.	All charts, dashboards, metrics, decline indicators, and AI answers must use the same ordering and as-of calculation rule.
7.	A correction is recorded as another entry. The timeline must retain both the original entry and the correction. Recording a backdated assessment must not overwrite or delete an existing assessment.
8.	Teachers may backdate an assessment by up to 30 days.
9.	Admins are not subject to the 30-day backdating limit.
10.	Future observation dates are rejected for all users.
11.	For ordinary assessments, regardless of the user's role, the observation date must fall within the student's relevant enrollment period.
12.	Exceptional historical corrections outside these rules require a separate, explicitly authorised, and audited process. That process must be specified and approved before it is implemented.
13.	Backdating rules apply to assessments only. Observation notes cannot be backdated (Section 6.4).
5.6 Term Review and Confirmation
Teachers can update skills whenever they observe a change. Once per term, the application also offers a Term Review screen listing every skill for a student and its current level.
•	The teacher can select Confirm for a skill whose level has not changed.
•	A confirmation creates a new progress entry with the same level and is_confirmation = true.
•	Confirmations indicate that the skill was rechecked and should not be treated as stale.
•	Confirmations do not count as improvements in charts or improvement metrics.
•	Confirmation entries remain visible in history.
5.7 Decline Indicators
The application maintains two distinct decline indicators. Neither is definitive proof of developmental regression, and both must be presented as changes in recorded data.
Skill-level decline
1.	Compare a student's latest assessment of a skill with the immediately preceding assessment of the same skill, using the ordering rule in Section 5.5, regardless of academic year.
2.	A skill-level decline is detected when the level decreases from the immediately preceding recorded level.
3.	If no previous assessment exists for that skill, no decline is flagged.
4.	Missing or unassessed values must never be treated as declines.
5.	Decline is derived from the ordered assessment history, never from a stored previous-level value or from the order in which records were entered.
Term-level subject-score decline
6.	A term-level decline is detected when a student's subject progress score has fallen compared with the end of the previous term (Section 8.4).
7.	If no previous-term data exists, no decline is shown.
6. Screens and User Workflows
The student profile is the centre of the application. A teacher logs in, selects a class, opens a student, and accesses that student's information, assessment history, reports, charts, notes, and AI chat from one place.
Design for a 360 px-wide smartphone screen first. Desktop is secondary.
6.1 Screen List
Screen	Users	Functionality
Login	All	Email/password login and English/Hindi language toggle.
Class switcher	Teacher, Admin	Teachers see assigned classes only; Admin sees all classes. Remember the last-used class.
Class list	Teacher, Admin	Student cards showing name, admission number, roll number, and needs-attention status. Search by name or admission number. Link to class dashboard.
Student profile	Teacher, Admin	Student details, tabs, and persistent student-scoped AI chat access.
Class dashboard	Teacher, Admin	Class charts, students needing support, and AI class insights/chat.
Principal dashboard	Admin	Cross-class view, teacher activity, data freshness, and school-wide trends.
Admin: Subjects and skills	Admin	Add, rename, reorder, and archive subjects and class-specific skills.
Admin: Classes and years	Admin	Manage classes, academic years, terms, and class order.
Admin: Students	Admin	Add/edit student records, manage guardians, manage enrollments, transfers, and withdrawals, and bulk-import students from CSV.
Admin: Promotion	Admin	Bulk year-end promotion, hold-back, and pass-out workflow.
Admin: Users	Admin	Create teacher and Admin accounts, edit account details, deactivate accounts, assign classes, and manage password recovery.
Admin: Audit log and settings	Admin	Searchable change log, school details, thresholds, AI usage limits, and AI usage.
6.2 Student Profile
The student profile header includes:
•	Full name.
•	Admission number.
•	Roll number.
•	Date of birth, where available.
•	Gender, where available.
•	Current class.
•	Current academic year.
Student photos are not included in Version 1.
The profile contains the following tabs:
Tab	Content
Overview	Progress ring per subject showing percent achieved, latest teacher note, skills changed in the last 30 days, and a needs-attention flag with its reason.
Assess Skills / Subjects	One collapsible section per subject. Each skill shows its current level with a three-way selector: Not yet, Developing, Achieved.
History / Timeline	Every skill change and note in date order, newest first, with author attribution. Filters by subject, teacher, and date range.
Notes	Free-text observations, optionally tagged to a subject. Shows author and date. Users can add notes; teachers can delete their own notes; Admins can delete any note.
Reports	Saved AI reports with Draft or Approved status, revision, reporting period, author, approver, and approval date. Drafts can be previewed on screen; only approved revisions can be printed or saved as PDF.
Charts	Student charts defined in Section 10.
6.3 Skill Assessment Interface
•	Tapping a level saves the assessment using today's date by default.
•	A date picker allows an earlier observation date, subject to the user's backdating permission.
•	Each skill row shows who last assessed it and when.
•	The interface must make the current level clear.
•	If saving fails, the interface must not imply that the new level was saved.
•	A skill that has never been assessed must be displayed as Not assessed, not Not yet.
6.4 Notes Interface
•	Teachers can create free-text observations for students in their currently assigned classes and optionally associate them with a subject.
•	Admins can create, view, and delete observation notes for any student.
•	The system records the author and the recording timestamp automatically. The timestamp is generated by the server and cannot be modified.
•	Observation notes cannot be backdated. A note's date is the date on which it is recorded (school time zone). This rule is enforced on the server, not only in the interface.
•	Teachers can delete only their own notes.
•	Admins can delete any note.
•	Deleted notes are hidden from normal views immediately. Only an Admin can restore a deleted note, within a seven-day recovery window; afterwards its content is purged (Section 11.6). Deleted note content is never stored in the audit log.
•	Deleting a note must not modify or delete assessment history.
•	The interface should remind teachers not to include student surnames, contact details, or unnecessary identifying information in notes.
6.5 Student-Level AI Chat
A persistent chat button opens a bottom sheet over the student profile.
•	The conversation is always scoped to the currently open student.
•	Teachers can ask text-based questions about the student's learning progress.
•	The assistant can show charts inline when requested.
•	Any suitable response can be saved as a report draft.
•	Chat history is maintained per user and per student.
•	A user must not be able to access another student's information by changing an identifier or manipulating a request.
•	The assistant must use verified application data rather than inventing facts.
Example question:
What is this student's progress in English over the last six months?
6.6 Class-Level AI Chat
The class dashboard includes conversational AI that answers questions about the current class using verified class-level data.
Examples include:
•	Which skills appear most difficult for this class?
•	Which students need additional practice?
•	What low-cost classroom activities could help with a particular skill?
•	What changed compared with the previous term?
Teachers can use class-level AI only for their assigned classes. Admin can use it for all classes.
6.7 Key Workflows
Workflow 1: Daily assessment
1.	Log in.
2.	Select a class.
3.	Open a student.
4.	Open the Assess Skills/Subjects tab.
5.	Tap the appropriate level.
Target: Under 10 seconds per skill for the standard assessment action.
Workflow 2: Add a note
1.	Open the student.
2.	Select Notes.
3.	Enter the observation.
4.	Save.
The system adds the author and timestamp automatically.
Workflow 3: Ask the student AI assistant
1.	Open the student profile.
2.	Open Chat.
3.	Enter a question.
4.	Review the evidence-based answer and any chart.
Workflow 4: Generate a report
1.	Open the student's Reports tab.
2.	Select the reporting period and language.
3.	Generate the report draft.
4.	Review and edit the draft. Teachers may edit only their own drafts.
5.	An Admin reviews and approves the report. Teachers cannot approve reports. An Admin who generated the report must still explicitly review and approve it.
6.	Print or save the approved revision as a PDF if needed.
Workflow 5: Year-end promotion
1.	Open the Promotion screen.
2.	Review the students in a class.
3.	Select Promote, Hold back, or Pass out for each eligible student.
4.	Review the proposed changes.
5.	Confirm the promotion.
6.8 UI Requirements
•	Touch targets must be at least 44 px.
•	Assessment levels must use colour plus text or an icon; colour alone is insufficient.
•	Every list and chart must have a useful empty state, such as “No skills assessed yet.”
•	All user-facing interface text must live in translation files for English and Hindi.
•	Subject and skill names must be stored in both languages.
•	Show clear loading, error, success, and retry states where appropriate.
•	A failed save must never look like a successful save.
6.9 Language Support
1.	The application must support both English and Hindi from the initial release.
2.	Users must be able to switch between English and Hindi through a language selector.
3.	All user-facing interface text, including navigation, buttons, form labels, validation messages, and system notifications, must be available in both languages.
4.	Language support must be implemented consistently across teacher and Admin interfaces.
5.	Changing the interface language must not change stored student records, assessment data, or the meaning of assessment levels.
6.	The implementation should make it straightforward to add more languages in the future.
7.	The system must handle Hindi text correctly in the database, user interface, and generated reports, including printed and PDF reports.
7. Logical Data Model and Database Requirements
The logical data model below defines the information the application must store. The database is PostgreSQL and the backend is Django (Section 15.1). The final schema, constraints, and migrations are deferred technical decisions (Section 15.2).
All primary entities should use stable identifiers. Tables should include creation timestamps where appropriate. Database changes must be managed through version-controlled migrations, not manual production database edits.
Tables containing historical records, especially progress_entries and audit_log, must be append-only for all application users. Records are retained only for their authorised retention period (Section 11.6); permanent retention must not be assumed. Removal or anonymisation of records happens only through the controlled, audited processes in Sections 11.6 and 11.9, never through ordinary application workflows.
7.1 Core Entities
Entity	Important fields	Constraints and notes
profiles	id, full_name, role, preferred_language, is_active	User ID corresponds to the authentication identity. Roles are teacher or admin. Multiple individual Admin accounts are allowed. Created by Admin. Deactivated rather than deleted in ordinary workflows; retained only for the authorised retention period (Section 11.6). Must support Admin-managed password recovery with a mandatory password change and server-enforced per-user session invalidation (Section 11.2).
academic_years	label, start_date, end_date, is_current	Exactly one academic year is current. Academic years must not overlap.
terms	academic_year_id, name, start_date, end_date	Terms must not overlap within an academic year.
classes	name_en, name_hi, sort_order, next_class_id, is_active	One section per grade in Version 1; the model must allow multiple sections per grade later (Section 4.5). The next-class relationship supports promotion. UKG has no next class.
students	admission_no, full_name (primary name), name_hi (Hindi/Devanagari name, optional), date_of_birth, gender, admission_date, status	Admission number is unique. No photo fields. No guardian contact fields in this table. Additional known nicknames and spelling variants are stored as separate related records (see student_name_variants).
student_name_variants	student_id, variant, script	Any number of nicknames or spelling variants per student, in any script. Used only for server-side redaction (Section 9.10); never sent to an AI provider.
Name-variant note: Redaction (Section 9.10) also uses guardian names and teacher/staff names, including first names and known nicknames, in English and Hindi/Devanagari forms. Guardian and staff name variants must be stored in a flexible, access-controlled schema with no fixed limit on the number of variants. Guardian variants follow the same Admin-only access as guardian records. The exact schema is finalised during schema design.
guardians	full_name, relationship, phone, email, is_primary	Separate guardian records. Admin-only access. Support multiple guardians per student.
student_guardians	student_id, guardian_id	Links students and guardians; support multiple guardians per student.
enrollments	student_id, class_id, academic_year_id, roll_no, started_on, ended_on	Dated enrollment history is preserved. A student may have several enrollment periods in one academic year, but periods for the same student must never overlap. Current class is derived from the open enrollment period.
teacher_classes	teacher_id, class_id, academic_year_id	Unique combination of teacher, class, and academic year.
subjects	name_en, name_hi, sort_order, is_archived	Archive rather than delete.
skills	subject_id, class_id, name_en, name_hi, description, sort_order, is_archived, created_by	A skill belongs to one subject and one class. Archive rather than delete.
progress_entries	student_id, skill_id, level, observed_on, recorded_at, author_id, is_confirmation, previous_level (optional snapshot)	Append-only for application users: no updates or deletes. Level is 1, 2, or 3. Future observation dates are rejected. recorded_at is system-generated and immutable. Ordering and decline follow Sections 5.5 and 5.7; previous_level, if retained, is a display snapshot only.
observations	student_id, subject_id, body, observed_on, created_at, author_id, deleted_at, deleted_by	Subject is optional. observed_on is the recording date; created_at is system-generated and immutable. Deleted content is handled under Section 11.6 and never copied into the audit log.
ai_reports	student_id, revision, period_start, period_end, language, content, input_facts, status, model, requested_by, approved_by, approved_at	Report status is Draft or Approved. Store the exact facts supplied to the AI for auditability. Approved revisions are immutable; editing an approved report creates a new draft revision (Section 9.8).
chat_sessions	student_id or class_id, user_id, started_at	Support student-scoped and class-scoped conversations. Access is restricted to the owning user and permitted student/class scope.
chat_messages	session_id, role, content, tool_calls, tokens_in, tokens_out	Users can read only their own chat history.
class_insights	class_id, period_start, period_end, content, generated_by	Cached to avoid unnecessary AI cost. Must be isolated by class.
ai_usage	user_id, request type, requested_at, outcome, tokens, estimated cost	Records each AI model request for limit enforcement and Admin reporting (Section 9.9).
audit_log	actor_id, action, entity_type, entity_id, occurred_at, reason, changed field names	Append-only for application users. Records the names of changed fields only, never before/after values. Written for relevant admin-managed changes, note deletions and restorations, password-recovery events, promotion changes and corrections, and retention purges. Must not contain passwords, secrets, deleted note content, or unnecessary student information. Access restricted to Admin. Retention per Section 11.6.
settings	key, value, updated_by, updated_at	Stores settings such as school name, stale-days threshold, attention threshold, and AI usage limits (Section 9.9).
Data-model note: The exact schema and database constraints must be finalised during technical design. The logical requirements above, including separate guardian records and the absence of photo fields, must be preserved.
7.2 Required Database Functions or Equivalent Server-Side Logic
The application requires shared, consistent calculation logic for:
•	skill_levels_as_of(student_id, date) — Returns the latest applicable level for every relevant skill for a student as of a given date.
•	student_subject_summary(student_id, from, to) — Returns subject-level counts at each assessment level, progress score, and coverage.
•	class_subject_summary(class_id, from, to) — Returns corresponding class summaries, including student-level distributions.
•	students_needing_attention(class_id) — Returns flagged students with clear reasons.
These can be implemented as database functions, views, or equivalent server-side services, but the definitions must be centralised and reused across dashboards, reports, flags, and AI tools.
Archived skills remain available for historical queries and historical reports. They are excluded from current-state skill lists and current-state coverage calculations.
8. Business Rules and Edge Cases
Every business rule in this section must have an appropriate test.
8.1 Authorship and History
1.	The author of an assessment, note, report, or administrative change is determined from the authenticated server session. The client must never be trusted to provide the author ID.
2.	Skill levels are never overwritten. A change or correction creates a new progress_entries row.
3.	Notes are soft-deleted. The deletion is recorded as audit metadata (actor, timestamp, action, record ID, and an appropriate reason). Deleted note content is kept outside the audit log and purged according to Section 11.6.
4.	Users are deactivated, not deleted, in ordinary application workflows. A deactivated user cannot sign in, but their name remains associated with historical records for those records' authorised retention period (Section 11.6).
5.	Teachers may backdate assessments by up to 30 days. Admins are not subject to the 30-day limit. Future observation dates are rejected. Ordinary assessment dates must fall within the student's relevant enrollment period (Section 5.5). Observation notes cannot be backdated (Section 6.4).
8.2 Subjects and Skills
6.	Removing a subject or skill means archiving it. Archived items disappear from entry screens and current-state percentages but remain visible in history, timelines, and relevant past-period charts.
7.	A newly added skill starts as Not assessed for every student and is excluded from percentages until assessed.
8.	Renaming a skill changes its displayed name everywhere because historical records reference the skill ID. If the meaning of a skill changes, archive the old skill and create a new one.
9.	Subjects and skills are reordered through sort_order. A skill cannot be moved to another class; archive it and create a new skill in the destination class.
8.3 Promotion and Academic Years
10.	A student's class is derived from enrollments, never stored as an independent current-class value on the student record. Previous-year progress remains associated with the previous class and academic year.
11.	The Promotion screen lists active students with three choices: Promote, Hold back, or Pass out. Pass out is available only for UKG. The default selection is Promote, subject to confirmation.
12.	Promotion runs as one transaction and closes the old enrollment. Retrying it must not create duplicate enrollments.
13.	After promotion, the new class's skills are Not assessed for the new academic year until assessed. Teacher assignments are not automatically carried forward.
14.	A returning student reuses their existing student record and receives a new enrollment period.
15.	Promotion changes and corrections must be recorded in the audit trail.
8.4 Attention Flags
The application flags students who meet any of the following default conditions:
16.	Low subject score: Any subject progress score is below the configurable attention threshold. Default: 40.
17.	Stale assessment data: No progress entry or confirmation has been recorded for 21 days while the student is active.
18.	Declining subject score: The subject score has fallen compared with the end of the previous term (the term-level indicator in Section 5.7).
Rules:
•	Every flag must explain the reason.
•	Thresholds and the stale-days period must be configurable by Admin.
•	The attention-flag threshold is independent of the fixed dashboard chart thresholds of 40 and 70 (Section 10.6). Changing it must not change chart colours or bands.
•	The skill-level decline indicator (Section 5.7) is a separate indicator from condition 18. It is shown as an indicator only and does not raise a needs-attention flag.
•	Decline indicators describe changes in recorded data and must not be presented as proof of developmental regression.
•	Confirmations count as recent review activity for stale-data purposes.
•	Confirmations do not count as improvements.
•	If no previous-term data exists, do not invent a comparison or display a decline flag based on missing data.
•	A student with no assessments must be handled explicitly rather than assigned an artificial score.
8.5 Imports, Archives, and Other Edge Cases
•	CSV imports must validate required fields, duplicate admission numbers, class/year references, and malformed rows before committing records.
•	The import interface must show row-level errors and make corrections possible.
•	Retrying an import must not create duplicate students or enrollments.
•	Archived skills cannot receive new assessments.
•	Concurrent assessment submissions must retain their history. The current level follows the ordering rule in Section 5.5 (observation date, recording timestamp, stable unique identifier).
•	Removing a teacher's class assignment immediately revokes access to that class.
•	Cached results must never cross student, user, or class authorization boundaries.
•	Empty datasets must produce a clear “No data yet” state instead of misleading zero values or invented trends.
9. AI Requirements and Safeguards
AI features support teachers by explaining recorded progress and suggesting simple educational activities. They must not replace the underlying database as the source of truth.
9.1 General Architecture
•	AI calls are made from server-side routes or services, not directly from the browser.
•	The server retrieves authorised data and computes numerical metrics.
•	The AI receives only the minimum necessary, privacy-filtered facts.
•	The AI explains the facts and may suggest activities; it does not calculate authoritative scores or invent data.
•	The application renders charts using verified data returned by the server.
•	AI-generated outputs must respect the user's role, assigned classes, language preference, and permitted data scope.
•	Student-level and class-level AI are both required in Version 1.
•	AI output is a suggestion only. It must never automatically modify student records or assessment history (Section 9.11).
•	The AI provider is the Google Gemini API (Section 15.1). AI features are implemented in the AI milestone, after the core non-AI application.
9.2 Student-Level AI Tools
The student assistant must have access to authorised tools or equivalent server-side functions for:
•	Retrieving a student's skill levels as of a date.
•	Retrieving progress history over a specified period.
•	Retrieving subject summaries, coverage, and level breakdowns.
•	Retrieving relevant observations after privacy filtering.
•	Retrieving changes and improvements over time.
•	Requesting a chart based on server-computed data.
•	Preparing a structured report draft from verified facts.
The server must enforce student and class permissions before returning any tool result.
9.3 Reports and Evidence Validation
AI-generated reports must be structured and evidence-based.
Recommended report structure:
{
  "summary": "...",
  "strengths": [
    {
      "text": "...",
      "evidence": ["skill:<id>", "note:<id>"]
    }
  ],
  "needs_support": [
    {
      "text": "...",
      "evidence": ["skill:<id>"]
    }
  ],
  "suggestions": [
    {
      "text": "...",
      "activity": "A simple classroom activity",
      "evidence": ["skill:<id>"]
    }
  ],
  "data_gaps": [
    "Subjects or skills with too little data"
  ]
}
The server must:
1.	Validate the generated structure.
2.	Verify that every referenced evidence ID exists in the supplied input facts.
3.	Reject or remove any claim whose evidence references are invalid.
4.	Save the result as a Draft.
5.	Allow editing only by the draft's author (a teacher, for students in their currently assigned classes) or by an Admin. Teachers cannot edit another user's draft.
6.	Allow only an Admin to approve the draft, and record who approved it and when.
7.	Preserve every approved revision unchanged. Editing an approved report creates a new draft revision; it must never overwrite the approved revision.
Only approved revisions are labelled Approved and treated as final reports that can be printed or saved as PDF. See Section 9.8.
9.4 Class Insights and Class-Level Chat
The server computes class summaries, including:
•	Average progress scores by subject.
•	Distribution of assessment levels.
•	Hardest skills.
•	Students needing attention.
•	Changes since the previous term.
•	Coverage and data freshness.
The AI uses these summaries to return 3–6 insights, each with a concise suggested action.
Class insights are cached to avoid unnecessary AI cost. Regenerate them when the user requests a refresh or when the underlying data has changed since the last generation. Each generation or refresh that makes a model request counts toward the AI usage limits; serving a cached result does not (Section 9.9).
Class-level conversational AI must use the same verified class data and permission boundaries. Teachers may access only assigned classes; Admin may access all classes.
9.5 System Prompt Requirements
All AI features must follow these requirements:
•	The audience is a teacher of children aged 2–6 in an Indian school.
•	Suggestions must be simple, low-cost classroom or play activities.
•	Use only data returned by authorised tools. If a tool returns no data, say so.
•	Never guess or fill gaps in the data.
•	Do not state numerical figures that did not come from a verified tool result.
•	Describe skills and recorded progress only. Do not diagnose, label, or predict a child's abilities.
•	If data suggests a persistent difficulty, recommend discussing it with the principal.
•	Refer to the child as “the student.” Use “he” or “she” only if the application supplies gender.
•	Reply in the user's preferred language.
•	Keep answers short by default and use bullets for lists.
•	Refuse requests unrelated to the student's learning progress.
9.6 Privacy and Safety Controls
Risk	Required control
Student personal data reaches the AI	Never send student names, admission numbers, dates of birth, photos, guardian information, or class rosters (Section 11.4).
A note includes a personal name or identifier	Before sending notes to the AI, apply the redaction rules in Section 9.10, covering students, guardians, teachers and staff, other named people, and common direct identifiers. If redaction is uncertain, block the request. The interface should remind teachers not to include surnames or contact details. Residual re-identification risk must be acknowledged.
AI invents numbers	All authoritative calculations come from the database or server-side functions.
AI makes unsupported claims	Validate report evidence IDs against the input facts.
Unreviewed report is treated as final	Reports remain Draft until approved by an Admin (Section 9.8).
AI usage costs become excessive	Enforce the server-side usage limits in Section 9.9 and cache class insights.
Prompt injection through notes	Treat notes as quoted untrusted data, never as instructions. The system prompt must explicitly say not to follow instructions contained in notes.
API key exposure	Keep the AI provider key in server-side environment variables. Never send it to the browser.
Cross-class information leakage	Enforce permissions before tool execution and isolate caches and conversations by authorised scope.
AI usage limits are defined in Section 9.9. Show a friendly message when a limit is reached. Assessment, reporting access, and student management must continue to work when AI is unavailable or a limit has been reached.
9.7 Model and Configuration
The AI provider is the Google Gemini API. The specific Gemini model and version, and the SDK or API interface used to call it, are selected during the AI milestone (Section 15.2).
Requirements:
•	The model name must be configurable without requiring application code changes (for example, through an environment variable).
•	Provider credentials must remain server-side.
•	Prompts must be stored in version-controlled files.
•	The model name must be recorded with every saved report.
•	Before real student data is sent, verify and document Gemini's current pricing, data-handling terms, and usage reporting (Section 15.3).
9.8 Report Approval, Revisions, and Printing
1.	Teachers may generate report drafts and edit their own drafts only for students in their currently assigned classes. Teachers cannot approve or finalise reports, and cannot edit another user's draft.
2.	Admins may generate, edit, and approve reports school-wide. An Admin who generates a report must still explicitly review and approve it; generation is never approval.
3.	All AI-generated report content remains a draft until reviewed and approved by an Admin.
4.	Editing an approved report creates a new draft revision. The previous approved revision remains immutable.
5.	The system records the author (preparer), approver, approval timestamp, reporting period, language, and revision for every report.
6.	Only approved revisions may be printed or saved as final PDFs. Drafts may be previewed on screen but cannot be printed or exported as final reports.
7.	Access to reports, including PDF output, follows the role and class-assignment rules in Section 3.
8.	Unauthorised users must not be able to approve, finalise, or produce final PDFs by modifying requests or directly invoking server endpoints.
9.	Bulk exports and CSV downloads are out of scope for Version 1.
10.	The detailed report workflow must be specified and approved before the reports milestone begins (Section 15.5).
9.9 AI Usage Limits and Cost Controls
1.	The application enforces three configurable application-level limits. Initial values:
•	30 AI messages per user per hour.
•	100 AI messages per user per month.
•	₹500 estimated school-wide AI spend per month.
2.	One message equals one actual outbound AI model request. This includes requests for chat responses, report generation or regeneration, and class-insight generation or refresh.
•	If one user action causes several model requests, each request counts separately.
•	A retry counts only when another request is actually sent to the provider.
•	Requests rejected by validation before any model request is sent do not count.
•	Cached results that require no model request do not count.
•	The hourly and monthly limits apply consistently across all AI features.
3.	When the outcome of a request is uncertain and the request may have reached the provider, count it conservatively.
4.	Usage tracking and enforcement occur on the server, with atomic quota updates that resist concurrent requests. Limits must not be bypassable by modifying requests, and the system must not rely on estimates supplied by the browser.
5.	Usage must be tracked sufficiently to monitor school-wide estimated spend and per-user consumption. The Admin can view AI usage and configure the limits.
6.	When a limit is reached, further AI requests covered by that limit are blocked and the user is informed. Non-AI functionality remains available.
7.	Monthly periods follow the school time zone (Asia/Kolkata). Cost estimation and detailed reset behaviour are defined during the AI milestone.
8.	The ₹500 threshold is an application-level estimate. It must not be described as a guaranteed Google billing cap unless provider-side controls are verified to support that claim.
9.	The detailed AI request-accounting rules must be specified and approved before the AI milestone begins (Section 15.5).
9.10 Redaction Before Sending Data to Gemini
Scope
1.	Before any free-text content (such as observation notes, chat input, or report text) is sent to Gemini, the server must redact personal names and direct identifiers, including:
•	Student names: each student's primary name, Hindi/Devanagari name where available, and all known nicknames and spelling variants, for the current student and for other students.
•	Guardian and parent names and their known variants.
•	Teacher and staff names, including first names and known nicknames.
•	Names of any other people identified in free-text notes, even when they are not in a known name list.
•	Common direct identifiers: phone numbers, email addresses, admission numbers, and other configured direct identifiers.
Method
2.	Redaction is layered:
•	Layer 1, known names: match the known names and variants held in the application (students, guardians, teachers/staff), in English and Hindi/Devanagari forms (Section 7.1). Matching must not rely exclusively on exact-text replacement.
•	Layer 2, direct identifiers: detect phone numbers, email addresses, admission numbers, and other configured direct identifiers.
•	Layer 3, possible unknown names: detect text that may be a personal name not in any known list. Any indication of a possible unknown name is treated as uncertainty (item 7).
Named-entity recognition (NER) libraries are an unapproved dependency pending evaluation and approval.
3.	Name lists and variant lists are used only on the server. Student rosters, name lists, and student identifiers are never sent to Gemini.
4.	Teacher names are never sent. Teacher attribution, where needed, is added by the server after the AI response.
5.	Messages shown to a teacher about blocked or redacted content must not disclose guardian information or any other personal information, including which known name or identifier was matched.
Limits and blocking
6.	Redaction is not guaranteed to detect every identifying detail and must never be described as infallible, in documentation or in the interface. This residual risk must be acknowledged, minimised by sending only the data each task needs (Section 11.4), and reduced by reminding users not to include identifying details in notes (Section 6.4).
7.	If redaction is uncertain, for example because text appears to contain an unrecognised name or identifier, block the AI request and ask the user to review the content. Potentially identifying text must not be sent.
Testing and approval
8.	Redaction must be tested with English, Hindi, and mixed-script examples, covering student, guardian, teacher/staff, and other names and the common direct identifiers above, using synthetic data only (Section 11.8). These tests must pass before the AI milestone is approved.
9.	The detailed redaction specification must be approved before the AI milestone begins (Section 15.5).
9.11 AI-Generated Insights and Human Review
1.	AI-generated insights are suggestions only. They must not automatically modify student records or assessment history.
2.	AI insights must be grounded in available assessment data and relevant recorded observations. The system must not present unsupported claims as facts.
3.	AI-generated content must be clearly identified as AI-generated and must not be treated as a definitive judgment of a child's abilities or development.
4.	Teachers may review and use AI suggestions only for students in their currently assigned classes.
5.	The system must preserve the distinction between recorded observations, assessment results, and AI-generated interpretations.
6.	AI output must not bypass existing authorization rules, data privacy requirements, or audit controls.
10. Charts, Metrics, and Dashboards
Charts must be rendered in the browser using verified data returned by database functions or equivalent server-side services. The AI may request a chart through a tool such as show_chart, but it must never supply the chart's authoritative numerical values.
10.1 Metric Definitions
These definitions must be implemented once and reused by charts, attention flags, reports, and AI tools.
Metric	Definition
Level score	Not yet = 0, Developing = 0.5, Achieved = 1. Not assessed is excluded.
Progress score (0–100)	Average level score across all assessed skills in the subject or overall, multiplied by 100.
Percent achieved	Number of assessed skills at Achieved divided by total assessed skills, multiplied by 100.
Coverage	Number of assessed skills divided by the number of active skills in that subject for the student's class, multiplied by 100.
Improvement	A skill whose level is higher at the end of a period than at the start. Confirmations do not count.
Hardest skill	A skill for which at least 50% of assessed students in the class are at Not yet.
As-of level	The level of the last progress entry, in the ordering defined in Section 5.5, whose observed_on is on or before the chosen date.
The progress score is calculated as:
\[ \text{Progress score} = 100 \times \frac{\sum_{s \in \text{assessed}} \text{levelscore}(s)} {|\text{assessed}|} \]
Coverage must be shown alongside every progress score. A progress score of 90 with only 10% coverage is not meaningful. Charts and AI answers must explicitly identify low coverage when it is below 50%.
If no skills have been assessed, the application must display a “No data yet” state rather than divide by zero or show a misleading score.
10.2 Student Charts
Available from the Charts tab and through student-level AI chat.
Chart	Type	Data
Progress over time	Line chart	Progress score at the end of each month, with one line per subject. Default range: last six months.
Current profile	Radar chart	Current progress score per subject.
Level breakdown	Stacked bar chart	Counts of Not yet, Developing, and Achieved per subject.
Skill timeline	Heatmap	Skills as rows, months as columns, and the level as of month-end as the cell value. Tapping a cell shows who assessed it.
10.3 Class Charts
Available on the class dashboard to authorised teachers and Admin.
Chart	Type	Data
Subject averages	Bar chart	Average progress score per subject, comparing the current period with the previous term.
Level distribution	Stacked bar chart	Number of students whose subject score is Low (below 40), Medium (40–70), or High (above 70). These bands are fixed (Section 10.6).
Class trend	Line chart	Class average progress score at the end of each month.
Hardest skills	Ranked list	Skills meeting the hardest-skill definition, with the share of students at Not yet.
Students needing attention	List	Flagged students and the reasons for their flags.
Most improved	List	Students with the most qualifying improvements during the period.
The AI class-insights card appears near the top of the class dashboard and must refer to the same verified figures shown in these charts.
10.4 Principal Dashboard
Admin-only, school-wide view.
Chart or component	Type	Data
Class by subject	Heatmap	Average progress score for each class and subject.
Class trend comparison	Line chart	Class average progress score by month, with one line per class.
Term-over-term comparison	Grouped bar chart	Each class's average score for the current term versus the previous term.
Data freshness	Table	Per class and teacher: last update date and number of entries in the last 30 days. Highlight classes with no update within the configured stale-days threshold.
School summary	Number tiles	Student count, active teachers, average coverage, and flagged students.
10.5 Comparison Caution
Classes have different skills and different ages. A higher UKG score than a Nursery score does not necessarily indicate better teaching or greater overall achievement.
The principal dashboard must label cross-class comparisons with this warning. The most prominent comparison should be each class against its own previous term.
10.6 Chart Requirements
•	Use consistent assessment-level colours: Not yet = red, Developing = amber, Achieved = green.
•	Colour must not be the only way of conveying a level. Include text labels, patterns, or equivalent accessible indicators.
•	Charts must be readable on a 360 px-wide phone.
•	Heatmaps may scroll horizontally within their containers.
•	Every chart must have loading, empty, and error states where appropriate.
•	Every chart must have a text summary suitable for screen readers.
•	Charts must be included in the printable report view.
•	Dashboard chart score bands are fixed: Low (below 40), Medium (40–70), and High (above 70). They are independent of the Admin-configurable attention-flag threshold (Section 8.4); changing that threshold must not change chart colours or bands.
•	The same threshold rules must be applied consistently across relevant dashboards and reports.
11. Security, Privacy, and Data Handling
The application stores personal information about young children. Access must be enforced in the database and server, not only in the interface. A UI bug or AI chat request must not expose another class's data.
11.1 Database Access Control
Access to protected student data is enforced by two complementary layers: Django server-side authorization and PostgreSQL Row-Level Security (RLS). RLS supplements Django authorization and must never replace it. Default to deny and grant only explicitly required access.
Table or data	Teacher access	Admin access
Students and enrollments	Read rows for students currently enrolled in currently assigned classes	Read and write all
Guardian records	No access	Read and write all
Progress entries	Read and insert for students in currently assigned classes; no update or delete	Read and insert for all; no update or delete
Observations	Read and insert for currently assigned classes; delete own notes	Read, insert, and delete any
AI reports	Read reports for students in currently assigned classes; create and edit own drafts for those students; no approval	Read, create, edit, and approve all
Chat sessions and messages	Read and write own conversations, within authorised student/class scope	Read and write own conversations, within authorised scope
Subjects, skills, classes, terms, academic years	Read only	Read and write
Profiles	Read own profile and the names needed for attribution	Read and manage all
Teacher-class assignments and settings	Read own applicable assignments/settings only	Read and manage all
Audit log	No access	Read only
The assigned-class policy must check the teacher's assignment for the applicable academic year.
Database security requirements:
1.	The application runtime must use a restricted database account that is separate from the migration/owner account. The runtime account must not own protected tables and must not be able to bypass RLS.
2.	RLS policies must deny access by default and explicitly authorise permitted access.
3.	User identity and role context must be established securely for each database transaction and must not leak between requests or transactions.
4.	The RLS identity mechanism, any privileged helper functions, database grants, audit-log immutability controls, the retention purge and anonymisation process, and any privileged database role used by that process are unapproved until they have been reviewed and tested.
5.	Enabling RLS alone does not prove the design is secure.
Automated tests must log in as Teacher A and verify that Teacher A cannot read or insert data for Teacher B's unassigned students, nor reach those students through AI chat, reports, server endpoints, or cached results. Tests must also cover cross-class access, guessed identifiers, deactivated users, absent database identity, identity-context leakage between transactions, access to historical student records, and direct SQL access under the runtime database account.
11.2 Authentication and Account Recovery
•	Use email/password authentication.
•	Admins create accounts. There is no self-sign-up.
•	Multiple individual Admin accounts are supported. Shared Admin credentials are prohibited.
•	Minimum password length: 10 characters.
•	Password recovery is Admin-managed. Teachers cannot reset passwords through email. The Admin verifies the user's identity, then issues a secure, one-time reset mechanism; the user must change their password at the next sign-in.
•	Existing passwords must never be displayed. Temporary credentials or reset tokens must never be stored in plaintext.
•	Recovery events are recorded in the audit log without recording credentials.
•	A separate, documented manual recovery procedure for Admin accounts is required. It must be documented and tested before deployment.
•	Sessions expire after 60 minutes of inactivity. The timeout is enforced on the server and is reset by valid user activity. After expiry, the user must authenticate again to access protected resources.
•	Provide a “sign out of all devices” action backed by a server-enforced per-user session invalidation mechanism.
•	A deactivated user must not be able to sign in or continue using an existing session.
11.3 Data Handling
•	Student photos are not collected or stored in Version 1.
•	Guardian names and contact information are stored only in separate, Admin-restricted records.
•	All traffic uses HTTPS.
•	Database keys, privileged service keys, and AI credentials must be stored in environment variables or a hosting secrets manager.
•	Secrets must never be committed to the repository or included in browser-delivered code, such as templates or static JavaScript.
•	Do not log student names, guardian contact information, observation text, or chat messages in routine application logs.
•	Back up the database. If the chosen plan does not provide restorable automatic backups, schedule at least weekly exports to separate storage.
•	Backup retention must be documented separately from audit and note-content retention, during the deployment milestone.
•	Test at least one backup restoration before launch.
11.4 Data Sent to AI
Only the minimum data required for the requested task may be sent to an AI provider.
Permitted information may include:
•	Skill levels and relevant assessment dates.
•	Subject names.
•	Relevant notes after redaction (Section 9.10).
Apply data minimisation to every request: send only what the task needs. Teacher attribution, where needed, is added by the server and is never sent to the provider.
The following must never be sent to the AI provider:
•	Student names, including Hindi/Devanagari names, nicknames, spelling variants, and the names of other students.
•	Admission numbers or other student identifiers.
•	Date of birth.
•	Student photo.
•	Guardian names or contact details.
•	Teacher names, including first names.
•	Class roster or student name lists.
•	Any other unnecessary personal identifier.
Before real student data is sent, verify and document Gemini's current pricing, data-handling terms, and usage reporting (Section 15.3).
11.5 Legal and Policy Review
India's Digital Personal Data Protection Act, 2023, includes provisions relevant to processing children's personal data. Before real student data is used, the school must consult a suitable advisor to determine applicable notice, consent, and other child-data obligations, even though parents are not application users. The review must cover at least:
•	Requirements for children's educational records.
•	Retention periods, including those in Section 11.6 and the retention period for assessment history. Permanent retention must not be claimed unless this review establishes that it is permitted.
•	The final retention and deletion policy, including when deletion or anonymisation is required.
•	Handling of deletion requests (Section 11.9).
•	Legal holds and other preservation obligations (Section 11.9).
•	Backups, including backup retention and deletion from backups.
•	Data protection obligations, including AI provider processing.
This specification is not legal advice.
11.6 Audit Log and Data Retention
1.	Audit metadata includes only necessary fields: actor, timestamp, action, entity type, record ID, an appropriate reason (for example, a deletion reason), and, for edits, the names of the changed fields. Before/after values are never recorded. Audit metadata must not contain passwords, secrets, deleted note content, or unnecessary student information.
1a.	Audit records refer to people and records only through opaque, stable identifiers, where references are necessary. Personal information, such as names, must not be embedded in audit records. How an audit reference is handled after the referenced record is deleted, pseudonymised, or anonymised must be defined in the deletion procedure (Section 11.9) before the purge process is implemented.
2.	Audit metadata is retained for three years. This period is provisional and subject to legal review (Section 11.5).
3.	Deleted note content is kept outside the audit log, with access restricted to Admins, for a recovery window of seven days. During the window, only an Admin can restore a deleted note; restorations are audited. After seven days, the content must be purged through a secure deletion process, except where a documented legal hold or applicable preservation requirement applies.
4.	Audit records must be protected against unauthorised modification or deletion.
5.	Purging records after their retention period expires must be a controlled, authorised process, and each purge must itself be audited. Purges are suspended for records under a legal hold or preservation obligation (Section 11.9).
6.	Deleting an observation note must not modify or delete assessment records. Assessment history remains append-only.
7.	Assessment history: permanent retention must not be assumed. Assessment records remain append-only during their authorised retention period and are not ordinarily deleted through application workflows. The retention period, and when deletion or anonymisation is required, are decided only after the legal review (Section 11.5). Any resulting deletion or anonymisation follows the controlled process in Section 11.9.
8.	Profiles of deactivated users and records of students who have left are retained only for their authorised retention period, then handled according to the approved retention policy.
9.	The final retention and deletion policy for all record types requires legal review before real student data is used.
10.	Backup retention is documented separately (Section 11.3).
11.7 Pre-Deployment Requirements
Before deployment with real student data:
•	HTTPS is configured for all traffic.
•	A backup restoration has been tested.
•	The Admin account recovery procedure is documented and tested.
•	The privacy and legal review (Section 11.5) is complete, every legal-review item recorded in decision-log.md (LR-1 to LR-7) is resolved, and the final retention and deletion policy is approved. This is a mandatory gate.
•	The deletion-request and legal-hold procedure (Section 11.9) is documented and approved.
•	Gemini pricing, data-handling terms, and usage reporting have been verified and documented (Section 15.3).
•	A pilot using synthetic data has been completed.
•	The project owner has explicitly approved real-data use after the legal and security reviews (Section 11.8).
11.8 Synthetic Data and Approval of Real Data
1.	Development, automated tests, demos, and development databases must use synthetic student data only.
2.	Synthetic data must not be derived from identifiable real students.
3.	Real student data must not be used anywhere until the project owner explicitly approves it after the required legal (Section 11.5) and security reviews. Until then, synthetic data is the only permitted data.
11.9 Deletion Requests and Legal Holds
Deletion requests
1.	Records are not ordinarily deleted through application workflows. Deletion or anonymisation outside the approved retention schedule happens only through a documented deletion-request process.
2.	The process must cover, at minimum:
•	Receiving: record each request, its date, and the requester, through a defined channel.
•	Verifying: confirm the requester's identity and authority to make the request.
•	Evaluating: assess the request against the approved retention policy, applicable legal requirements, legal holds, and preservation obligations.
•	Authorising: an authorised approver, as defined in the approved procedure, approves or refuses the request, with the reason recorded. It must not be assumed that every Admin may approve every request. The procedure defines the authorised approver, conflict-of-interest handling, and whether a second approval is required.
•	Executing: carry out approved deletion, pseudonymisation, or anonymisation through a controlled, authorised, and audited process, not through ordinary application screens. Assessment history stays append-only until such an approved action.
•	Recording: record the decision and action as audit metadata (Section 11.6), without retaining the deleted content.
3.	How approved deletions apply to backups is defined by the legal review and the backup retention policy (Sections 11.3 and 11.5).
Legal holds and preservation obligations
4.	When a documented legal hold or applicable preservation obligation applies to records, scheduled purges, retention-based deletion, and deletion-request execution for those records are suspended.
5.	Placing and releasing a hold requires explicit authorisation and is recorded as audit metadata.
6.	A legal hold preserves the affected records without changing normal access permissions: held records remain subject to the normal role and class-assignment controls.
Pseudonymisation, anonymisation, and the purge process
7.	Pseudonymisation replaces identifying details with identifiers that can be re-linked using separately protected information; it is reversible and the data remains personal data. Anonymisation is irreversible. The procedure must state which technique applies to each record type.
8.	After irreversible anonymisation, permanent attribution (for example, which teacher recorded an assessment, or which student a record belonged to) is not promised and may be lost.
9.	The retention-purge and anonymisation process must be narrowly scoped to the records and actions it is authorised to change, and every run must be audited. Any privileged database role it uses must be reviewed and approved before implementation (Section 11.1).
10.	The detailed procedure, including who may receive, verify, and authorise requests, conflict-of-interest handling, any second approval, how requesters are informed, and how audit references are handled after deletion or anonymisation (Section 11.6), must be documented and approved after legal review and before real student data is used (Section 15.5).
12. Reliability and Error Handling
This section reconstructs the reliability topic referenced by the original specification's missing Section 10.
•	The app requires internet connectivity; offline-first entry and background synchronization are not included in Version 1.
•	If the connection is lost, show a clear message and disable actions that cannot be completed safely.
•	A failed save must never look successful.
•	Retrying a failed write must not create accidental duplicate records.
•	If AI is unavailable, show a friendly error and allow retry. Normal non-AI functions must remain usable.
•	If an AI usage limit is reached (Section 9.9), explain the limit. Assessment and student management must continue to work.
•	If a session expires, require re-authentication without exposing protected data.
•	If access is revoked, block subsequent database, API, export, cache, and AI access immediately.
•	CSV import errors must be clear and recoverable.
•	Every screen should provide a useful empty state, loading state, error state, and retry path where retry is safe.
13. Future Features (Not Version 1)
This section reconstructs the future-features topic referenced by the original specification's missing Section 12.
Potential later additions:
•	Voice-based assessment entry.
•	Importing historical paper registers from photographs.
These features are not part of Version 1 acceptance. Do not build them into the initial release unless separately approved.
14. Acceptance Criteria and Test Checklist
The application is not considered ready for school use until the following are demonstrated.
14.1 Permissions and Privacy
•	Teacher A cannot read, edit, print, or query Teacher B's unassigned students through the UI, server endpoints, database, cached results, PDF outputs, or AI.
•	Teachers lose access to a student's historical records when the student is no longer enrolled in one of their currently assigned classes.
•	Guessed or altered identifiers do not grant access to unauthorised records.
•	Under the restricted runtime database account, direct SQL queries without a valid identity context return no protected data, and identity context does not leak between transactions.
•	Removing a teacher from one class revokes access immediately but preserves historical attribution.
•	Teachers cannot access guardian information through any route.
•	Guardian contact information is never sent to the AI provider.
•	Student names (including Hindi/Devanagari names, nicknames, spelling variants, and other students' names in English, Hindi, and mixed script), guardian and parent names, teacher and staff names (including first names and nicknames), other people's names in notes, phone numbers, email addresses, admission numbers, dates of birth, photos, and class rosters are not sent to AI. AI requests are blocked when redaction is uncertain.
•	Deactivated users cannot sign in or continue using existing sessions.
•	Sessions expire after 60 minutes of inactivity, enforced by the server; “sign out of all devices” invalidates every session for that user.
•	Admin-managed password recovery uses a one-time mechanism, forces a password change, and never displays or stores credentials in plaintext. The Admin recovery procedure has been tested.
14.2 Assessment and History
•	Each assessment creates a dated, attributed entry with a system-generated, immutable recording timestamp.
•	Assessment history cannot be updated or deleted through application workflows during its authorised retention period.
•	Retention-based deletion and deletion requests run only through the controlled, audited processes in Sections 11.6 and 11.9, and are suspended for records under a legal hold.
•	Current/as-of calculations follow the ordering rule: observation date, recording timestamp, then stable unique identifier.
•	Teachers cannot backdate assessments by more than 30 days; future dates are rejected for all users; ordinary assessment dates fall within the student's enrollment period.
•	Observation notes cannot be backdated, and their recording timestamps cannot be modified.
•	Skill-level and term-level decline indicators follow Section 5.7; missing data is never treated as decline.
•	Not assessed is distinct from Not yet.
•	Confirmations do not count as improvements but do refresh stale-data status.
•	Archived skills are excluded from current state but remain available in historical views.
•	A student with no assessed skills displays “No data yet.”
14.3 Enrollment and Promotion
•	A student cannot have overlapping enrollment periods.
•	A transfer between classes closes the old enrollment period, opens a new one, and keeps earlier history with the original class.
•	Promotion preserves previous-year history.
•	Promotion is transactional and safe to retry.
•	Hold back, promote, and UKG pass-out outcomes work as specified.
•	Returning students reuse the existing student record and receive a new enrollment period.
•	Academic years do not overlap, and terms do not overlap within an academic year.
14.4 Metrics, Dashboards, and AI
•	All score, coverage, improvement, hardest-skill, and attention calculations follow Sections 5.7, 8, and 10.
•	Dashboard chart bands remain fixed at 40 and 70 when the attention threshold changes.
•	Low coverage is clearly disclosed.
•	AI answers use verified tool data and do not invent figures.
•	Student AI cannot access another student by changing an identifier.
•	Class AI is restricted to assigned classes for teachers.
•	Report evidence references are validated.
•	Reports remain Draft until explicitly approved by an Admin; teachers cannot approve reports or edit another user's draft.
•	Approved revisions are immutable; editing creates a new draft revision.
•	Only approved revisions can be printed or saved as final PDFs.
•	AI usage limits (Section 9.9) are enforced on the server, including under concurrent requests.
•	Cross-age and cross-class comparisons include caution text.
14.5 Usability and Reliability
•	Core assessment flow is usable on a 360 px-wide phone.
•	English and Hindi are available throughout the interface.
•	Touch targets are at least 44 px.
•	Every list and chart has appropriate loading, empty, and error states.
•	Failed saves never appear successful.
•	CSV import validates rows and prevents duplicate creation on retry.
•	Backup restoration has been tested before launch.
•	Pre-deployment requirements in Section 11.7 are complete.
•	Critical permission, promotion, and calculation tests run automatically.
15. Technical Decisions: Approved and Deferred
The following technology choices have been approved for the project. Remaining implementation decisions are intentionally deferred to the milestone where they are needed. The decision log (decision-log.md) records the status of each decision.
15.1 Approved Technology Stack
1.	Backend: Python and Django.
2.	Frontend: Django Templates, HTML, and plain JavaScript where needed. React is not part of the stack.
3.	Styling: Tailwind CSS, compiled using the Tailwind CLI with Node.js/npm. Node.js/npm is used only for the CSS build.
4.	Database: PostgreSQL.
5.	AI provider: Google Gemini API, implemented in the AI milestone.
6.	Administration: custom Admin screens built in the application. The Django Admin site is disabled in production.
7.	Django REST Framework is not required for Version 1.
8.	Testing: Django's built-in test framework, initially.
9.	Dependencies: pinned versions in requirements.txt.
10.	Secrets: environment variables (or a hosting secrets manager); secrets are never committed.
11.	Languages: English and Hindi in Version 1.
12.	School time zone: Asia/Kolkata.
These choices are fixed unless explicitly reconsidered and approved by the project owner. They must not be changed silently. Additional frameworks, libraries, services, or architectural patterns require a clear need and approval.
15.2 Deferred Technical Decisions
The following decisions remain open and must not be treated as fixed requirements.
1.	Database schema and migrations: design the final schema and migration strategy to implement the logical requirements in Section 7, including separate guardian records and role-restricted access.
2.	RLS implementation: the identity mechanism, privileged helper functions, grants, audit immutability controls, and retention purge process remain unapproved until reviewed and tested (Section 11.1).
3.	URL and view structure, and any JSON endpoints needed for charts or AI chat: define in the relevant milestone.
4.	Gemini model and version, and the SDK or API interface: select during the AI milestone.
5.	Charting library and any additional JavaScript libraries: select during the dashboards milestone. The required chart behaviour and metrics remain fixed.
6.	Hosting, monitoring, backup mechanism, backup retention, and production static-file configuration: select during the deployment milestone.
7.	CI provider: select when automated pipelines are introduced.
15.3 AI Data-Handling Review
Before sending real student data to Gemini, verify and document the selected model's current pricing, usage reporting, and data-handling terms, and document approval of the proposed approach. The ₹500 application-level threshold must not be described as a Google billing cap unless provider-side controls are verified to support that claim.
The AI integration must comply with the privacy, security, and data-minimisation requirements in this specification.
15.4 Governing Principle
Technical decisions must not weaken the functional, authorization, privacy, security, or data-integrity requirements in this document. Any significant decision that conflicts with these requirements must be raised for explicit approval before implementation.
15.5 Specifications Required Before Later Milestones
The following rules are provisionally defined in this document but must be specified in detail and approved before their milestones begin:
1.	Redaction specification (Section 9.10): before the AI milestone.
2.	AI request accounting, cost estimation, and reset behaviour (Section 9.9): before the AI milestone.
3.	Detailed report workflow (Section 9.8): before the reports milestone.
4.	Exceptional historical-correction process for assessments (Section 5.5): before it is implemented.
5.	Deletion-request and legal-hold procedure (Section 11.9), including the authorised approver, verification, conflict-of-interest handling, any second approval, pseudonymisation versus anonymisation, the retention-purge process and its privileged database role, and audit-reference handling: after legal review, before the purge process is implemented, and before real student data is used.
16. Developer and AI Coding Assistant Instructions
When using this document with developers or GitHub Copilot:
1.	Treat this document as the requirements baseline.
2.	Read the relevant section before implementing a feature.
3.	Do not invent roles, permissions, metrics, or workflows.
4.	Enforce authorization server-side and at the database layer; frontend checks alone are insufficient.
5.	Preserve append-only assessment history and auditability.
6.	Never expose guardian information to teachers or AI.
7.	Keep secrets out of source control and browser-delivered code.
8.	Add automated tests for every business rule and permission boundary.
9.	If a requirement conflicts with the existing code, explain the conflict before changing behavior.
10.	If an implementation detail is undecided, propose options and document the decision instead of silently choosing.
11.	Update this specification and decision-log.md when the client approves a change.
12.	Do not treat deferred technical decisions as approved technology choices.
Persistent instructions for GitHub Copilot are maintained in .github/copilot-instructions.md.
16.1 Definition of Done for a Feature
A feature is complete only when:
•	It matches the relevant requirements.
•	Permissions are enforced and tested.
•	Loading, empty, error, and success states are handled.
•	Relevant edge cases have automated tests.
•	Sensitive data is protected.
•	Documentation is updated if behavior or decisions have changed.
17. Change Log
Version	Date	Change
1.0	26 October 2026	Consolidated the original specification and recorded review decisions; removed student-photo scope; added Admin-only guardian records; made student- and class-level conversational AI explicit; added enrollment edge cases, report revision behavior, reliability requirements, and acceptance criteria.
1.1	27 October 2026	Recorded the approved stack (Django, Django Templates, Tailwind CLI via npm, PostgreSQL, Gemini) and removed the Claude/Sonnet proposal; merged decisions previously appended after the end of the specification into numbered sections; added teacher historical-access rule (3.3), class sections (4.5), decline indicators (5.7), language support (6.9), report approval and revisions (9.8), AI usage limits (9.9), redaction (9.10), AI human review (9.11), audit and retention (11.6), and pre-deployment requirements (11.7); made report approval Admin-only; prohibited backdating of observation notes; defined assessment ordering and enrollment-period date rules; set 60-minute server-side inactivity timeout and Admin-managed password recovery; allowed multiple individual Admin accounts; limited export to PDFs of approved reports; set the Asia/Kolkata time zone; corrected the chart-section cross-reference.
1.2	27 October 2026	Renamed the specification file to requirements.md. Allowed multiple non-overlapping enrollment periods per academic year and defined transfer as a move between classes within the school. Audit log records changed field names only, never values. Added a seven-day Admin-only recovery window for deleted notes. Skill-level decline is an indicator only and does not raise attention flags. Marked name-variant storage, AI message mapping, and teacher-name handling as pending.
1.3	27 October 2026	Synthetic data only until explicit real-data approval (11.8). Flexible student name model: primary name, optional Hindi/Devanagari name, and unlimited nicknames/variants used for redaction (7.1, 9.10). One AI message equals one outbound model request (9.9). Teacher names are never sent to Gemini (11.4). Permanent retention of assessment history is no longer assumed and is subject to legal review (5.5, 7, 11.5, 11.6).
1.4	27 October 2026	Replaced absolute "never delete" wording: records are not ordinarily deleted through application workflows, subject to the approved retention policy and legal requirements (3.1, 4.3, 5.5, 7, 8.1, 11.6). Added the deletion-request and legal-hold process (11.9). Final retention and deletion policy requires legal review before real data (11.5, 11.7). Expanded redaction to guardian, teacher/staff, and other personal names and common direct identifiers, with English, Hindi, and mixed-script tests required before AI milestone approval (9.6, 9.10, 14.1).
1.5	27 October 2026	Deletion approval is not assumed for every Admin; the approver, verification, conflict-of-interest handling, and any second approval are defined in the deletion procedure (11.9). Legal holds preserve records without changing access permissions. Distinguished pseudonymisation from anonymisation; attribution is not promised after irreversible anonymisation; the purge process and its privileged role require review (11.1, 11.9). Audit records use opaque identifiers without embedded personal information (11.6). Layered redaction with NER unapproved; blocked-AI messages disclose no personal information; redaction is not described as infallible (9.10). Flexible, access-controlled storage for guardian and staff name variants (7.1). Legal-review items LR-1 to LR-7 are a mandatory gate before real data (11.7).
End of specification.

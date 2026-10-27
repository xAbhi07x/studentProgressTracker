# Decision Log — School Student Progress Tracker

This log records the status of project decisions. The requirements specification ([requirements.md](requirements.md)) remains the source of truth for product behaviour; this log explains *which* decisions are settled, which are provisional, and which still need review.

Last updated: 27 October 2026 (specification version 1.2).

## Status categories

| Code | Meaning |
|---|---|
| **C** | Confirmed decision. Change only with explicit project-owner approval. |
| **P** | Provisional operational default. Adopted for now; may change after review or real-world use. |
| **LR** | Requires legal review before real student data is used. |
| **SR** | Requires security design review and testing before it is treated as approved. |
| **S** | Provisionally defined, but must be specified in detail and approved before its milestone. |
| **OI** | Open item. Not yet decided; must be resolved before the affected work. |

## Confirmed decisions (C)

### Architecture and tooling

| ID | Decision | Spec |
|---|---|---|
| C-01 | Backend: Python and Django. | 15.1 |
| C-02 | Frontend: Django Templates, HTML, plain JavaScript where needed. No React. | 15.1 |
| C-03 | Styling: Tailwind CSS compiled with the Tailwind CLI using Node.js/npm. | 15.1 |
| C-04 | Database: PostgreSQL. | 15.1 |
| C-05 | AI provider: Google Gemini API, implemented in the AI milestone. The Claude/Sonnet proposal is withdrawn. | 9.7, 15.1 |
| C-06 | Django REST Framework is not required for v1. | 15.1 |
| C-07 | Custom Admin screens; Django Admin disabled in production. | 15.1 |
| C-08 | Django's built-in test framework, initially. | 15.1 |
| C-09 | Pinned dependency versions in `requirements.txt`. | 15.1 |
| C-10 | Secrets in environment variables (or a hosting secrets manager); never committed or sent to the browser. | 11.3, 15.1 |
| C-11 | English and Hindi in v1. | 6.9 |
| C-12 | School time zone: Asia/Kolkata. | 2.1 |

### Access, enrollment, and assessment

| ID | Decision | Spec |
|---|---|---|
| C-20 | Admins create accounts; no public sign-up. | 3, 11.2 |
| C-21 | Multiple individual Admin accounts are supported; shared Admin credentials are prohibited. | 3.1, 11.2 |
| C-22 | Teachers access students and all of their historical records only while the students are currently enrolled in classes assigned to them. Admins have school-wide access. | 3.3 |
| C-23 | Only Admins manage enrollment, transfers, withdrawals, promotion, users, classes, academic years, and terms. | 3.2, 4.3 |
| C-24 | Dated enrollment history is preserved; a returning student gets a new enrollment period on the existing student record. | 4.3 |
| C-25 | Admins configure academic years and terms; dates are validated; academic years must not overlap; terms must not overlap within an academic year. | 4.3 |
| C-26 | Teachers may backdate assessments by up to 30 days; Admins have no 30-day limit; future-dated assessments are prohibited. | 5.5 |
| C-27 | Ordinary assessment observation dates must fall within the student's relevant enrollment period, for all roles. | 5.5 |
| C-28 | Exceptional historical corrections require a separate, explicitly authorised, audited process (see S-04). | 5.5 |
| C-29 | Observation notes cannot be backdated; their date is the recording date and their timestamp is server-generated and immutable. | 6.4 |
| C-30 | Assessment history is append-only; corrections create new entries. | 5.5 |
| C-31 | Assessment ordering: observation date, then original immutable recording timestamp, then stable unique ID. | 5.5 |
| C-32 | Skill-level decline is derived from ordered history, not a stored `previous_level`. A retained `previous_level` is only a display snapshot. | 5.5, 5.7 |
| C-33 | Two distinct decline indicators: skill-level (consecutive assessments of the same skill) and term-level subject score (against the previous term). Missing data is not decline; neither indicator proves regression. | 5.7, 8.4 |
| C-34 | Chart bands fixed at 40 and 70; attention-flag threshold separately configurable by Admin. | 8.4, 10.6 |
| C-35 | One section per grade in v1; data model must allow multiple sections later. | 4.5 |
| C-36 | A student may have multiple dated enrollment periods within one academic year, but a student's periods must never overlap. (Option chosen by the assistant at the project owner's request: editing a single enrollment would rewrite the class context of past assessments.) | 4.3, 7.1 |
| C-37 | "Transfer" means moving a student between classes within the school: close the current enrollment period and open a new one. Leaving the school is a withdrawal. | 4.3 |
| C-38 | Skill-level decline is shown as an indicator only and does not raise a needs-attention flag. | 8.4 |

### Notes, audit, and retention

| ID | Decision | Spec |
|---|---|---|
| C-40 | Admins can create, view, and delete any note. Teachers manage notes only for currently assigned classes and may delete only their own notes. | 3.2, 6.4 |
| C-41 | Deleted note content is kept outside the audit log. Audit metadata contains only necessary fields (actor, timestamp, action, record ID, appropriate reason) and, for edits, the names of changed fields — never before/after values. | 11.6 |
| C-42 | Audit records are protected against unauthorised modification; authorised purging after retention expiry is controlled and audited. | 11.6 |
| C-43 | Backup retention is documented separately from audit and note retention. | 11.3, 11.6 |
| C-44 | Deleted notes have a seven-day recovery window; only an Admin can restore them, and restorations are audited. | 6.4, 11.6 |
| C-45 | The audit log covers relevant Admin-managed changes, note deletions and restorations, password-recovery events, promotion changes and corrections, and retention purges. | 7.1 |

### Accounts and sessions

| ID | Decision | Spec |
|---|---|---|
| C-50 | Admin-managed password recovery: identity verification, secure one-time reset mechanism, mandatory password change. No email-based reset. | 11.2 |
| C-51 | Existing passwords are never displayed; temporary credentials are never stored in plaintext; recovery events are logged without credentials. | 11.2 |
| C-52 | A documented manual Admin recovery procedure must be documented and tested before deployment. | 11.2, 11.7 |
| C-53 | Session inactivity timeout: 60 minutes, server-enforced, reset by valid activity. | 11.2 |
| C-54 | Sign out of all devices via server-enforced per-user session invalidation. | 11.2 |
| C-55 | Minimum password length: 10 characters. | 11.2 |

### AI and reports

| ID | Decision | Spec |
|---|---|---|
| C-60 | Each actual AI model request counts toward the limits (chat, report generation/regeneration, class-insight generation/refresh). Cached results without a model request do not count. Uncertain outcomes that may have reached the provider count conservatively. How requests map onto the message limits is OI-8. | 9.9 |
| C-61 | Limits are enforced server-side with atomic quota updates that resist concurrent requests. An `ai_usage` record is kept for each AI model request to enforce the limits. | 7.1, 9.9 |
| C-62 | The ₹500 threshold is an application-level estimate, not a guaranteed Google billing cap. | 9.9, 15.3 |
| C-63 | Never send student names (including other children), admission numbers, dates of birth, photos, rosters, or guardian information to Gemini. Prefer adding teacher attribution server-side. | 11.4 |
| C-64 | If redaction is uncertain, block the AI request and ask the user to review the note. | 9.10 |
| C-65 | AI output is always a draft until reviewed and approved by an Admin; AI never modifies student records. | 9.8, 9.11 |
| C-66 | Teachers may generate and edit their own drafts for assigned students; they cannot approve reports or edit another user's draft. | 9.8 |
| C-67 | Admins can generate, edit, and approve reports school-wide; an Admin who generates a report must explicitly review and approve it. | 9.8 |
| C-68 | Editing an approved report creates a new draft revision; the approved revision stays immutable. | 9.8 |
| C-69 | Only approved revisions can be printed or saved as final PDFs; drafts are on-screen preview only. | 9.8 |
| C-70 | Reports store author, approver, approval timestamp, reporting period, language, and revision. | 7.1, 9.8 |
| C-71 | No bulk exports or CSV downloads in v1; student CSV import remains. | 2.3 |

### Database security

| ID | Decision | Spec |
|---|---|---|
| C-80 | Django authorization plus PostgreSQL RLS for protected student data; RLS never replaces Django checks. | 11.1 |
| C-81 | Restricted runtime database account, separate from the migration/owner account; it must not own protected tables or bypass RLS. | 11.1 |
| C-82 | Automated tests for cross-class access, guessed IDs, deactivated users, absent database identity, context leakage, and direct SQL under the runtime role. | 11.1, 14.1 |

### Deployment prerequisites

| ID | Decision | Spec |
|---|---|---|
| C-90 | Before deployment: HTTPS, tested backup restoration, documented and tested recovery procedure, privacy/legal review, synthetic-data pilot, and Gemini verification. | 11.7 |
| C-91 | Deferred to their milestones: Gemini model/version and SDK/API interface (AI), charting library (dashboards), hosting, monitoring, backups and production static files (deployment), CI provider. | 15.2 |

## Provisional operational defaults (P)

| ID | Default | Spec | Notes |
|---|---|---|---|
| P-01 | Audit metadata retention: three years. | 11.6 | Subject to LR-1. |
| P-02 | Deleted note content: seven-day Admin-only recovery window, then purge, unless a documented legal hold or preservation requirement applies. | 11.6 | Subject to LR-1. |
| P-03 | AI limits: 30 messages per user per hour; 100 messages per user per month; ₹500 estimated school-wide spend per month. All configurable by Admin. | 9.9 | Values may change after Gemini pricing is verified (LR-4). |
| P-04 | Attention-flag threshold 40; stale-data period 21 days. Both configurable by Admin. | 8.4 | Unchanged from v1.0. |

## Items requiring legal review (LR)

| ID | Item | Spec | Required before |
|---|---|---|---|
| LR-1 | Retention periods for audit metadata (P-01) and deleted note content (P-02). | 11.5, 11.6 | Real student data |
| LR-2 | Parent/guardian notice, consent, and child-data obligations under India's DPDP Act, 2023. | 4.2, 11.5 | Real student data |
| LR-3 | Permanent retention of assessment history (progress entries can never be deleted). If legal review requires deletion after a period, this conflicts with Section 5.5 and must be raised for a decision. | 5.5, 7, 11.5 | Real student data |
| LR-4 | Gemini current pricing, data-handling terms (including any use of submitted content), and usage reporting. | 9.7, 15.3 | Real student data sent to Gemini |

## Items requiring security review and testing (SR)

| ID | Item | Spec | Required before |
|---|---|---|---|
| SR-1 | RLS design: per-transaction identity mechanism, privileged helper functions, grants, audit immutability controls, retention purge process. Unapproved until reviewed and tested. Enabling RLS alone is not proof of security. | 11.1, 15.2 | Protected student data tables are implemented |
| SR-2 | Redaction reliability for English, Hindi, and mixed-script content, including other children's names and nicknames. | 9.10 | AI milestone |
| SR-3 | Admin account recovery procedure (documented and tested). | 11.2, 11.7 | Deployment |

## Specifications required before later milestones (S)

These rules are provisionally defined in the specification but must be specified in detail and approved before the milestone begins.

| ID | Specification | Spec | Required before |
|---|---|---|---|
| S-01 | Redaction specification: name variants, detection rules, uncertainty criteria, blocking behaviour, test corpus. | 9.10 | AI milestone |
| S-02 | AI request accounting: what is recorded per request, cost estimation, hourly/monthly reset behaviour, concurrency handling, uncertain-outcome rules. | 9.9 | AI milestone |
| S-03 | Detailed report workflow: states, revisions, edit rights, approval, preview vs final PDF. | 9.8 | Reports milestone |
| S-04 | Exceptional historical-correction process for assessments: who authorises it, what is audited, which rules it may bypass. | 5.5 | Its implementation |

## Open items (OI)

| ID | Issue | Consequence | Needed before |
|---|---|---|---|
| OI-7 | How known student name variants (Devanagari spellings, nicknames) needed for redaction are stored — for example, as a field on the student record — and who maintains them. | Affects the student data model and Admin screens. Redaction of variants (C-64, Section 9.10) is confirmed; only the storage approach is pending. | Student/enrollment milestone |
| OI-8 | Whether each AI model request counts as exactly one "message" toward the 30-per-hour and 100-per-month limits, or whether some requests are weighted differently. | Affects quota accounting (part of S-02). | AI milestone |
| OI-9 | Whether teacher first names may ever be sent to Gemini for attribution, or attribution is always added on the server. Current wording: first names only when needed; server-side attribution preferred. | Affects data sent to the provider. | AI milestone |
| OI-10 | Whether tests and development must use synthetic data only. (The pre-deployment pilot must use synthetic data; that is confirmed.) | Affects test fixtures and developer setup. | M1 |

### Resolved open items

| ID | Resolution | Recorded as |
|---|---|---|
| OI-1 | Multiple non-overlapping enrollment periods per academic year; transfer means moving between classes within the school. | C-36, C-37 |
| OI-2 | Skill-level decline is an indicator only. | C-38 |
| OI-3 | Seven-day Admin-only recovery window for deleted notes. | C-44, P-02 |
| OI-4 | Audit log records changed field names only, never before/after values. | C-41 |
| OI-5 | Specification renamed to `requirements.md`. | — |
| OI-6 | Git repository initialised. | — |

## Change history

| Date | Change |
|---|---|
| 27 October 2026 | Log created with specification version 1.1. |
| 27 October 2026 | Resolved OI-1 to OI-6; added OI-7 to OI-10 for interpretations not yet confirmed; specification version 1.2. |

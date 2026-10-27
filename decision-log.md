# Decision Log — School Student Progress Tracker

This log records the status of project decisions. The requirements specification ([requirements.md](requirements.md)) remains the source of truth for product behaviour; this log explains *which* decisions are settled, which are provisional, and which still need review.

Last updated: 27 October 2026 (specification version 1.3).

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
| C-60 | One message equals one actual outbound AI model request (chat responses, report generation/regeneration, class-insight generation/refresh). Each request from a multi-request action counts separately; retries count only when another request is actually sent; requests rejected by validation before sending, and cached results, do not count. Uncertain outcomes that may have reached the provider count conservatively. Hourly and monthly limits apply consistently across AI features. | 9.9 |
| C-61 | Limits are enforced server-side with atomic quota updates that resist concurrent requests. An `ai_usage` record is kept for each AI model request to enforce the limits. | 7.1, 9.9 |
| C-62 | The ₹500 threshold is an application-level estimate, not a guaranteed Google billing cap. | 9.9, 15.3 |
| C-63 | Never send student names or variants (including other students'), admission numbers or other student identifiers, dates of birth, photos, rosters, guardian information, or teacher names (including first names) to Gemini. Teacher attribution is added on the server. Apply data minimisation to all other personal identifiers. | 11.4 |
| C-64 | If redaction is uncertain, block the AI request and ask the user to review the note. | 9.10 |
| C-64a | Flexible student name model: primary name, optional Hindi/Devanagari name, and any number of nicknames or spelling variants (no fixed limit). All are used for server-side redaction, including references to other students in notes. | 7.1, 9.10 |
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

### Data handling and environment

| ID | Decision | Spec |
|---|---|---|
| C-95 | Development, tests, demos, and development databases use synthetic student data only. Synthetic data must not be derived from identifiable real students. | 11.8 |
| C-96 | Real student data may not be used until the project owner explicitly approves it after the required legal and security reviews. | 11.7, 11.8 |
| C-97 | Permanent retention of assessment history is not assumed. Assessment records stay append-only during the authorised retention period; permanence or deletion is decided only after legal review (LR-3). | 5.5, 7, 11.6 |
| C-98 | `.env.example` variable names remain placeholders until M1, when configuration is finalised and documented. Actual secrets are never committed. | 15.1 |

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
| LR-2 | Parent/guardian notice, consent, and child-data obligations under India's DPDP Act, 2023, including requirements for children's educational records. | 4.2, 11.5 | Real student data |
| LR-3 | Retention of assessment history: whether it may be retained permanently or when deletion may be required, and the approved deletion process. Until decided, records stay append-only and permanent retention is not assumed (C-97). | 5.5, 7, 11.5, 11.6 | Real student data |
| LR-4 | Gemini current pricing, data-handling terms (including any use of submitted content), and usage reporting. | 9.7, 15.3 | Real student data sent to Gemini |
| LR-5 | Handling of deletion requests and legal holds. | 11.5, 11.6 | Real student data |
| LR-6 | Backups: backup retention and how deletion or retention obligations apply to backups. | 11.3, 11.5 | Real student data |

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

No open items. New ones will be added here as they arise.

### Resolved open items

| ID | Resolution | Recorded as |
|---|---|---|
| OI-1 | Multiple non-overlapping enrollment periods per academic year; transfer means moving between classes within the school. | C-36, C-37 |
| OI-2 | Skill-level decline is an indicator only. | C-38 |
| OI-3 | Seven-day Admin-only recovery window for deleted notes. | C-44, P-02 |
| OI-4 | Audit log records changed field names only, never before/after values. | C-41 |
| OI-5 | Specification renamed to `requirements.md`. | — |
| OI-6 | Git repository initialised. | — |
| OI-7 | Flexible name model with primary name, optional Devanagari name, and unlimited variants, used for redaction. | C-64a |
| OI-8 | One message equals one outbound model request, with the counting rules in C-60. | C-60 |
| OI-9 | Teacher names, including first names, are never sent to Gemini. | C-63 |
| OI-10 | Synthetic data only until explicit approval after legal and security reviews. | C-95, C-96 |

## Change history

| Date | Change |
|---|---|
| 27 October 2026 | Log created with specification version 1.1. |
| 27 October 2026 | Resolved OI-1 to OI-6; added OI-7 to OI-10 for interpretations not yet confirmed; specification version 1.2. |
| 27 October 2026 | Resolved OI-7 to OI-10; added C-95 to C-98; broadened LR-2 and LR-3; added LR-5 and LR-6; specification version 1.3. |

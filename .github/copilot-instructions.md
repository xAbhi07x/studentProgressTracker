# Copilot Instructions — School Student Progress Tracker

These instructions apply to every coding task in this repository.

## Sources of truth
- `requirements.md` (the requirements specification) is the source of truth for product behaviour. Read the relevant section before implementing a feature.
- `decision-log.md` records confirmed decisions (C), provisional defaults (P), items needing legal (LR) or security (SR) review, specifications required before milestones (S), and open items (OI).
- If documents conflict, a requirement is ambiguous, or a decision is missing, stop and raise the specific issue before making consequential changes. Never invent requirements or silently choose an interpretation.
- Do not implement anything that depends on an open item (OI) or an unapproved specification (S) until it is resolved.

## Approved stack — preserve it
- Backend: Python + Django. Frontend: Django Templates, HTML, plain JavaScript where needed.
- Styling: Tailwind CSS compiled with the Tailwind CLI via Node.js/npm (CSS build only).
- Database: PostgreSQL. AI: Google Gemini API, only in the AI milestone.
- Custom Admin screens; Django Admin disabled in production.
- Django's built-in test framework. Pinned versions in `requirements.txt`.
- Do not add React, Django REST Framework, or any other framework, library, service, or architectural pattern without a clear need and explicit approval. Ask first.
- Deferred until their milestone: Gemini model/SDK, charting library, hosting, backups, production static files, CI provider.

## Security and privacy (non-negotiable)
- Enforce authentication, roles, and teacher class assignments on the server with Django authorization **and** PostgreSQL Row-Level Security. Never rely on hiding UI elements. RLS supplements Django checks; it never replaces them.
- Teachers may access a student, including history, only while the student is currently enrolled in a class currently assigned to them. Admins have school-wide access.
- The runtime database account must be separate from the migration/owner account, must not own protected tables, and must not bypass RLS.
- Treat the RLS identity mechanism, privileged helper functions, grants, audit immutability, and retention purge as unapproved until reviewed and tested. Enabling RLS alone does not prove security.
- Guardian information is Admin-only. Never expose it to teachers through views, search, logs, PDFs, or AI.
- Never send student names (including other children's names in English or Hindi), admission numbers, dates of birth, photos, rosters, or guardian data to Gemini. If redaction is uncertain, block the request.
- Determine authors from the server session; never trust client-supplied author IDs or timestamps.
- Keep API keys and database credentials in environment variables. Never commit secrets or place them in templates or static JavaScript.
- Do not log student names, guardian contact details, note text, chat content, or credentials.

## Data integrity
- Assessment history (`progress_entries`) is append-only: no updates or deletes, including by Admins. Corrections are new entries.
- Order assessments by observation date, then immutable recording timestamp, then stable unique ID. Derive decline from this ordering, never from a stored `previous_level`.
- Teachers may backdate assessments by up to 30 days; future dates are rejected; ordinary assessment dates must fall within the enrollment period. Observation notes cannot be backdated.
- Audit records are protected from modification and record changed field names only, never before/after values. Deleted note content never goes into the audit log.
- Only Admins approve reports. Approved revisions are immutable; only approved revisions may be printed or saved as PDF.
- Use version-controlled migrations; never edit production data manually.

## Working style
- Make small, focused changes that address the requested task. Avoid unrelated refactoring, unrequested features, and speculative abstractions.
- Ask before changes that conflict with the requirements or need a significant new technology or dependency.
- Explain important decisions briefly.

## Testing and reporting
- Add or update automated tests for business rules, authorization boundaries, and changed behaviour. Permission tests must include cross-class access, guessed IDs, deactivated users, absent database identity, context leakage, and direct SQL under the runtime role, where relevant.
- Never use real student data in tests or development unless explicitly approved (see decision-log.md OI-10).
- Run the relevant tests and report exactly what was run and the actual results. Never claim a test passed if it was not run; state clearly what was not run and why.

## After each task
- List changed files, summarise important decisions, report verification results, and name unresolved decisions or risks.
- Update `requirements.md` and `decision-log.md` when an approved decision changes behaviour.
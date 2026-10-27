# Approved Technology Stack

| Layer | Technology |
|---|---|
| Backend | Python + Django |
| Frontend | Django Templates + HTML + plain JavaScript where needed |
| Styling | Tailwind CSS, compiled with the Tailwind CLI via Node.js/npm |
| Database | PostgreSQL |
| AI | Google Gemini API (implemented in the AI milestone) |

Supporting decisions:

- No React. Django REST Framework is not required for v1.
- Custom Admin screens; the Django Admin site is disabled in production.
- Django's built-in test framework, initially.
- Pinned dependency versions in `requirements.txt`.
- Secrets in environment variables; never committed.
- English and Hindi in v1. School time zone: Asia/Kolkata.

Deferred to their milestones: Gemini model/version and SDK/API interface, charting library, hosting, monitoring, backups, production static-file configuration, and CI provider.

Any additional framework, library, service, or architectural pattern requires a clear need and project-owner approval. See [requirements.md](requirements.md) Section 15 and [decision-log.md](decision-log.md).
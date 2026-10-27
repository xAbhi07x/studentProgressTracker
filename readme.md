# School Student Progress Tracker

A web application designed to help schools track, analyse, and understand student learning progress through skill-based assessments and AI-assisted insights.

## Overview

The School Student Progress Tracker aims to replace paper-based student progress registers with a centralised digital system.

The application will allow teachers to record student progress against specific skills, review historical assessments, identify areas requiring support, and use AI-assisted insights to make informed teaching decisions.

The principal will have a school-wide view of student progress, classroom trends, and teaching activity.

The application is being designed primarily for early childhood education, covering Pre-Nursery, Nursery, LKG, and UKG.

## Objectives

- Digitise paper-based student progress registers.
- Maintain a historical record of skill-based assessments.
- Track student progress across subjects and over time.
- Help teachers identify students and skills requiring additional support.
- Provide AI-assisted student reports, insights, and teaching suggestions.
- Give the principal a consolidated view of progress across classes.
- Provide a simple, mobile-friendly interface suitable for everyday classroom use.

## Planned Features

### Student Management
- Student profiles and academic records.
- Class and academic-year management.
- Parent and guardian information management.
- Student promotion and enrollment management.

### Skill-Based Assessment
- Subject-wise and class-specific skills.
- Three assessment levels: Not yet, Developing, and Achieved.
- Historical assessment records with dates and teacher attribution.
- Teacher observations and notes.
- Term-wise progress reviews.

### Progress Dashboards
- Individual student progress dashboards.
- Class-level performance summaries.
- Progress charts and assessment-level distributions.
- Identification of students and skills requiring attention.
- School-wide progress monitoring for the principal.

### AI-Assisted Insights
- Conversational AI for individual student progress.
- Class-level AI chat and insights.
- Evidence-based teaching suggestions.
- AI-generated student report drafts, reviewed and approved by an Admin before they become final.

### User Roles and Access Control
- **Teacher:** Access to students currently enrolled in their assigned classes, including those students' history.
- **Admin (Principal):** School-wide access and administrative controls. Multiple individual Admin accounts are supported.

### Language Support
- English and Hindi interface support.

> These are planned capabilities. Their implementation status will be updated as development progresses.

## Technology Stack

| Component | Technology |
|---|---|
| Backend | Python + Django |
| Frontend | Django Templates + HTML + plain JavaScript where needed |
| Styling | Tailwind CSS (compiled with the Tailwind CLI via Node.js/npm) |
| Database | PostgreSQL |
| AI Integration | Google Gemini API (implemented in the AI milestone) |

React and Django REST Framework are not part of the v1 stack. The Django Admin site is disabled in production; administration uses custom screens. Additional libraries, frameworks, hosting services, and development tools require a clear need and project-owner approval. See [tech-stack.md](tech-stack.md) and the [decision log](decision-log.md).

## Project Status

**Current status:** Planning and initial setup. Application implementation has not started.

The product requirements (specification version 1.3) and planning decisions have been documented. Repository setup and a minimal Django foundation are the next stages. Only synthetic student data may be used until real-data use is explicitly approved after legal and security reviews.

## Project Documentation

| Document | Purpose |
|---|---|
| [requirements.md](requirements.md) | Product requirements and functional specification — the source of truth for product behaviour, permissions, assessment rules, data model, AI safeguards, metrics, and acceptance criteria. |
| [decision-log.md](decision-log.md) | Confirmed decisions, provisional defaults, items needing legal or security review, and open items. |
| [tech-stack.md](tech-stack.md) | Approved technology stack summary. |
| [.github/copilot-instructions.md](.github/copilot-instructions.md) | Persistent instructions for AI coding assistants. |

## Getting Started

Installation instructions, prerequisites, environment configuration, and commands to run the application will be added once the initial project structure and development environment have been established.

## Configuration and Security

- API keys and database credentials must be stored securely in environment variables or a secrets manager.
- Secrets must never be committed to the repository or included in templates or static JavaScript.
- Access must be enforced by Django server-side authorization and PostgreSQL Row-Level Security, including role-based and class-based permissions.
- Guardian information is Admin-only and must never be shown to teachers.
- Identifying student information (including names in English or Hindi) must be redacted before any content is sent to the AI provider; uncertain cases are blocked.
- Before real student data is used: HTTPS, a tested backup restoration, a documented and tested Admin recovery procedure, a privacy/legal review, verified Gemini terms and pricing, and a synthetic-data pilot. See the specification, Section 11.7.

## Development Principles

- Keep the architecture simple and maintainable.
- Prioritise correctness, data integrity, and security.
- Preserve historical assessment records; assessment history is append-only.
- Validate inputs on the backend.
- Test important business rules and access-control boundaries.
- Document significant technical decisions as the project evolves.

## Contributors

Maintained by the project development team.

## License

A license has not yet been selected. Usage and distribution terms will be documented once the project owners decide on a license.

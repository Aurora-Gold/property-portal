# Agent guidance

This file contains durable repository boundaries and technical decisions. Product behavior belongs in [SPEC.md](SPEC.md); user-facing setup belongs in the relevant README.

## Repository boundaries

The workspace contains three sibling projects maintained as separate repositories:

- `api/` — the backend API and persistence layer.
- `app/` — the web application.
- `mobile/` — the later-phase mobile application.

Keep project-specific source, configuration, dependencies, tests, and documentation inside its project directory. Do not import source files across projects. Shared domain types must be published or generated as a versioned API contract.

## Technology choices

- API: TypeScript, Node.js, Express 5, Prisma 7, and SQLite initially.
- Web: TypeScript, React, Vite, and shadcn/ui preset `b7CNFsdU`.
- Mobile: React Native, Expo, and TypeScript.
- API style: REST JSON under `/api/v1`; OpenAPI is required before public beta.
- Database: SQLite initially, with schema choices kept portable to PostgreSQL.

## Architectural boundaries

### API

Use layers for routes/controllers, validation, services/use cases, repositories/data access, policies, and infrastructure. Enforce authentication, authorization, ownership, and access checks server-side on every write and management endpoint. Keep media behind a storage abstraction.

Use short-lived access tokens with refresh-token rotation. Passwords must be salted and hashed. Use consistent error envelopes, request IDs, structured logs, secure CORS, and rate limits on authentication and contact endpoints.

### Web and mobile

Consume the API contract rather than duplicating domain rules. Keep platform-specific navigation, storage, and UI in the owning project. The web UI must remain responsive and accessible; use loading, error, empty, and authorization states consistently. Format currency as MMK, use metric units, keep text Burmese/English-ready, and handle timestamps in a timezone-safe way.

## Data and security rules

- Use opaque/UUID identifiers, timestamps, appropriate indexes, and soft deletion/archive semantics where user-facing records require them.
- Store prices and areas using decimal-safe representations; never use floating-point arithmetic for money.
- Do not expose private moderation notes.
- Validate and sanitize input, restrict upload MIME types and sizes, prevent IDOR, and keep secrets and real personal data out of the repository.
- Seed data must be repeatable and idempotent and must use development-only credentials.

## Quality bar

Use TypeScript strict mode, lint/format checks, unit tests for domain rules and policies, API integration tests for authentication/lifecycle/filters/role boundaries, and web end-to-end coverage for primary journeys. Target WCAG 2.1 AA-oriented keyboard navigation, labels, focus states, contrast, and non-color status cues.

Document migrations, seed execution, environment setup, and reproducible checks in the owning project README. Update [SPEC.md](SPEC.md) when product behavior or acceptance scope changes; update this file only when a durable technical boundary or choice changes.

<!--
Sync Impact Report (review scratch material; remove before committing)
Version change: 1.0.0 → 1.1.0. Previous version and ratification date recovered
from the prior constitution in this conversation; current file lacked metadata.
Modified principles: I, IV, V, VI, VIII, IX, X retain their titles; clarified mandatory
security, citations, identity, privacy, simplicity, delivery, and telemetry requirements.
Added sections: restored project title, Product Scope and Constraints,
Development Workflow and Quality Gates, Governance, and version/date metadata.
Removed sections: none. Existing ten-principle list structure preserved.
Follow-up TODOs: none. Dependent templates remain unchanged.
-->
# Knowledge-as-a-Service Constitution

## Core Principles

### I. Security First

- All customer knowledge MUST be treated as sensitive and untrusted input.
- Access MUST follow least privilege.
- Authorization MUST deny access by default.
- All external inputs MUST be validated.
- Designs MUST identify trust boundaries before implementation.
- Designs MUST identify relevant abuse cases before implementation.
- Uploads MUST enforce file type and file size restrictions.
- Uploaded content MUST be processed in an isolated manner.
- Retrieved document content MUST be treated as evidence, never as trusted system instructions.
- Retrieved content MUST NOT override system security policies.
- Secrets MUST NOT exist in source code, frontend bundles, or logs.
- Secrets MUST be stored in an approved secret-management system.
- Known exploitable security vulnerabilities MUST block release.

### II. Strict Multi-Tenant Isolation

- Every customer resource MUST belong to an explicit tenant.
- Workspace-owned resources MUST also have an explicit workspace owner.
- Tenant context MUST be established from authenticated identity and verified membership.
- Client-supplied `tenant_id` or `workspace_id` MUST NOT grant access by itself.
- Authorization MUST apply to:
  - APIs
  - document downloads
  - hosted chat
  - background jobs
  - object storage
  - caches
  - embeddings
  - vector retrieval
- Tenant/workspace restrictions MUST be applied before retrieval and LLM generation.
- Shared infrastructure MUST preserve tenant isolation.
- Missing or ambiguous tenant context MUST fail closed.
- Automated negative tests MUST prove that Tenant A cannot read, modify, retrieve, or infer Tenant B's information.

### III. Grounded Answers and Evidence-Based Abstention

- Answers MUST be based only on authorized workspace knowledge.
- Only successfully processed and accessible sources may be used as evidence.
- LLM/model memory MUST NOT substitute for missing customer evidence.
- If evidence is missing, irrelevant, contradictory, or insufficient, the system MUST abstain.
- The system MUST clearly tell the user when available knowledge is insufficient.
- Partial answers MUST distinguish supported information from unanswered portions.
- The system MAY request clarification when the user's question is ambiguous.
- Retrieval sufficiency MUST have measurable evaluation criteria.
- Abstention behavior MUST have repeatable evaluation criteria.
- Model confidence alone MUST NOT be considered evidence.

### IV. Verifiable Source Citations

- Every substantive factual claim MUST be supported by retrieved evidence.
- Answers MUST provide citations for supporting sources.
- Citations MUST identify the source document/version.
- Citations MUST identify a usable source location when available, such as:
  - PDF page
  - document passage
  - video timestamp
- The system MUST NOT invent:
  - sources
  - page numbers
  - passages
  - timestamps
  - quotations
- Citation access MUST enforce the same tenant/workspace permissions as retrieval.
- If a claim cannot be supported with valid evidence, the claim MUST be omitted or the system MUST abstain.

### V. Customer Data Privacy and Encryption

- The following MUST be treated as customer data:
  - source documents
  - extracted text
  - document chunks
  - embeddings
  - questions
  - answers
  - processing metadata
- Customer data MUST only be collected and processed for documented product purposes.
- Customer data MUST NOT be shared between tenants.
- Customer data MUST NOT be used to train models without explicit customer authorization.
- External AI/model providers MUST have documented data-use and retention controls consistent
  with these privacy requirements.
- Customer data MUST be encrypted in transit.
- Customer data MUST be encrypted at rest.
- Encryption applies to:
  - object storage
  - databases
  - vector indexes
  - backups
- Encryption keys MUST have restricted access.
- Key rotation procedures MUST be documented.
- Logs and traces MUST exclude raw customer content by default.
- Credentials and secrets MUST NOT appear in logs.
- Data deletion MUST include source data and derived artifacts.
- Deleted/revoked sources MUST immediately stop participating in retrieval.
- Backup retention/deletion behavior MUST be documented.

### VI. OAuth2/OIDC Identity and Explicit Authorization

- Interactive authentication MUST use OAuth2/OIDC.
- Authentication MUST use a vetted identity provider or standards-compliant identity service.
- Interactive clients MUST use Authorization Code with PKCE.
- Tokens MUST be validated for:
  - signature
  - issuer
  - audience
  - expiration
  - applicable scopes
- Authorization MUST additionally verify current workspace membership and role.
- API credentials MUST be scoped.
- API credentials MUST be revocable.
- API credentials MUST be associated with an authorized tenant context.
- The application MUST NOT invent a proprietary authentication protocol.
- The application MUST NOT implement custom password storage.
- Authentication and authorization failures MUST fail closed.

### VII. API-First Design

- Core functionality MUST have documented API contracts before implementation.
- API contracts are required for:
  - workspace management
  - document ingestion
  - processing status
  - knowledge queries
- Hosted chat and customer-facing interfaces MUST consume these contracts.
- Contracts MUST define:
  - authentication
  - authorization
  - request schemas
  - response schemas
  - error responses
  - processing states
  - citation format
  - abstention outcomes
- Mutating APIs MUST define idempotency behavior where applicable.
- Asynchronous operations MUST define retry behavior.
- Breaking API changes MUST use explicit versioning.
- Breaking changes MUST provide a migration path.
- Contract tests MUST protect supported client behavior.

### VIII. Simple Architecture and Incremental Vertical Slices

- Architecture MUST use the fewest components necessary to satisfy verified requirements.
- Architecture MUST maintain clear boundaries between:
  - identity
  - ingestion
  - retrieval
  - answer generation
- New services MUST have a recorded requirement and justification.
- New frameworks MUST have a recorded justification.
- Provider abstractions MUST have a verified requirement before introduction.
- Distributed coordination MUST have a recorded requirement and justification.
- Functionality MUST be delivered as small end-to-end vertical slices.
- Every slice MUST connect customer behavior through:
  - API
  - persistence
  - security
  - automated tests
- Every slice MUST have observable acceptance criteria.
- Each completed slice MUST preserve a working customer journey.
- Customers MUST NOT need to understand or configure:
  - embeddings
  - chunking
  - vector databases/search
  - LLM orchestration

### IX. Infrastructure as Code and Reproducible Delivery

- Persistent infrastructure MUST be defined as Infrastructure as Code.
- Access policies MUST be defined as code.
- Encryption configuration MUST be defined as code.
- Non-secret environment configuration MUST be version controlled.
- Secrets MUST be referenced, never embedded in infrastructure code.
- Development, test, and production environments MUST be explicitly isolated.
- Infrastructure changes MUST be repeatable.
- Deployments MUST include automated checks.
- Database/schema migrations MUST be controlled.
- Rollback or recovery procedures MUST be documented.
- Emergency manual infrastructure changes MUST be recorded.
- Manual changes MUST be reconciled into Infrastructure as Code before the next routine deployment.
- Durable customer data MUST be backed up.
- Restore procedures MUST be tested.

### X. Automated Testing and Privacy-Preserving Observability

- Every vertical slice MUST include appropriate automated tests.
- Unit tests MUST cover important business rules.
- Integration tests MUST cover storage and processing boundaries.
- API contract tests MUST protect external behavior.
- End-to-end tests MUST cover critical customer journeys.
- Regression tests MUST cover:
  - tenant isolation
  - authorization
  - invalid credentials
  - citation correctness
  - insufficient evidence
  - prompt injection
- Retrieval and generation changes MUST pass repeatable AI evaluations.
- AI evaluations MUST include:
  - supported questions
  - unsupported questions
  - conflicting-evidence questions
- Services MUST emit structured logs that allow requests and jobs to be correlated.
- Services MUST emit operational metrics. Operations crossing service or job boundaries
  MUST provide correlated traces.
- Telemetry MUST NOT expose raw customer knowledge.
- Observability MUST track:
  - ingestion failures
  - query failures
  - ingestion latency
  - query latency
  - abstention outcomes
  - citation validation
  - model/token usage
- Tenant identifiers appearing in telemetry MUST be access controlled.
- Production services MUST expose health checks.
- Production services MUST have actionable alerts.
- Operational/recovery procedures MUST be documented.

## Product Scope and Constraints

The product MUST turn private organizational knowledge into a secure knowledge API and
hosted chat without customer-managed RAG infrastructure.

V1 scope MUST follow `docs/product-vision.md`: customer registration, workspace creation,
PDF upload, video, document processing, embeddings, vector retrieval, natural-language
questions, grounded answers, citations, and tenant isolation. Video specifications MUST
define supported inputs, processing behavior, and citation locations before implementation.

Audio, SharePoint, Google Drive, Confluence, database connectors, autonomous agents, billing,
and multiple LLM providers are outside V1. Adding them MUST first update the product scope
and an approved feature specification. Video support MUST NOT silently expand into standalone
audio ingestion.

V1 acceptance MUST demonstrate account creation → workspace creation → PDF upload → processing
completion → question → grounded answer → source citation, without manual configuration by
the platform team. Hosted chat MUST enforce the knowledge API's identity, authorization,
isolation, and evidence guarantees.

## Development Workflow and Quality Gates

Specifications MUST define user outcomes, scope, tenant boundaries, evidence and citation
behavior, and testable acceptance criteria. Plans MUST include a Constitution Check covering
applicable principles, trust boundaries, privacy, API contracts, operational needs, and
complexity. Tasks MUST organize work into vertical slices with their verification activities.

Every change MUST receive review for constitutional compliance. Required automated tests,
static checks, dependency and secret scanning, and applicable AI evaluations MUST pass before
merge. Failed isolation, unsupported factual answers, invalid citations, and known exploitable
security defects MUST block release. Infrastructure changes MUST have a reviewed change plan.
Production readiness MUST include documented operational targets, telemetry, recovery
procedures, and verification of the relevant customer journey.

## Governance

This constitution takes precedence over conflicting specifications, plans, tasks, and local
implementation conventions. `docs/product-vision.md` supplies product intent and scope;
conflicts MUST be resolved explicitly before implementation proceeds.

Amendments MUST document rationale, affected principles, customer and security impact,
migration work, and required updates to dependent artifacts. A project maintainer MUST approve
amendments before they take effect. Feature-level exceptions MUST NOT waive non-negotiable
principles; changing a principle requires a constitutional amendment.

Versioning MUST follow semantic versioning: MAJOR for incompatible removal or redefinition
of principles or governance, MINOR for new principles or materially expanded requirements,
and PATCH for clarifications that do not change obligations. Amendments MUST update the
version and last-amended date while preserving the original ratification date.

Compliance MUST be checked during specification, planning, code review, and release readiness.
Violations MUST be recorded and corrected. Security, isolation, privacy, and answer-grounding
violations MUST block the affected release. Maintainers MUST review this constitution when
product scope or trust boundaries change.

**Version**: 1.1.0 | **Ratified**: 2026-10-03 | **Last Amended**: 2026-10-04

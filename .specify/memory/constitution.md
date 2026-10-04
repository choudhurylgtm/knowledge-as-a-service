<!--
Sync Impact Report (review scratch material; remove before committing)
Version change: 1.0.0 → 1.0.0 (revalidation; no governance changes).
Modified principles: none; all ten principles retained.
Added sections: none.
Removed sections: none.
Follow-up TODOs: none. Dependent templates remain unchanged and read this document at runtime.
-->
# Knowledge-as-a-Service Constitution

## Core Principles

### I. Security First

Every feature MUST treat customer knowledge as sensitive, untrusted input and enforce
least privilege, deny-by-default authorization, input validation, and safe failure behavior.
Designs MUST identify trust boundaries and abuse cases before implementation. Uploads MUST
have enforced type and size limits and isolated processing. Retrieved content MUST be treated
as evidence, never as instructions that override policies or grant access. Secrets MUST be
managed outside source code, client bundles, and logs through a secret store. Known exploitable
vulnerabilities MUST block release until resolved.

### II. Strict Multi-Tenant Isolation

Every customer resource MUST have an explicit tenant and workspace owner. The server MUST
establish tenant context from verified identity and membership; client-supplied identifiers
alone MUST never confer access. Authorization MUST cover APIs, downloads, hosted chat,
background jobs, storage, caches, embeddings, and vector retrieval. Tenant and workspace
restrictions MUST be enforced before retrieval and generation, including in shared
infrastructure. Missing or ambiguous tenant context MUST fail closed. Automated negative
tests MUST prove that one tenant cannot read, modify, retrieve, or infer another tenant's
knowledge through supported interfaces.

### III. Grounded Answers and Evidence-Based Abstention

Knowledge answers MUST rely only on accessible, successfully processed evidence retrieved
from the authorized workspace. Model memory MUST NOT substitute for missing evidence.
When evidence is absent, irrelevant, contradictory, or insufficient, the service MUST
explicitly state the limitation and abstain from unsupported claims. Partial answers MUST
distinguish supported findings from unanswered parts and request clarification when useful.
Retrieval sufficiency and abstention MUST have documented, repeatable evaluation criteria;
model confidence alone MUST NOT establish factual support.

### IV. Verifiable Source Citations

Every substantive factual claim in a knowledge answer MUST cite evidence that supports it.
Citations MUST identify the source version and resolve to an authorized source and usable
location, such as a PDF page, passage, or video timestamp when available. Sources, locations,
and quotations MUST NOT be invented. Citation access MUST enforce the same tenant and
workspace permissions as retrieval. If support or citation validity cannot be established,
the associated claim MUST be withheld or the answer MUST abstain.

### V. Customer Data Privacy and Encryption

Documents, extracted content, embeddings, questions, answers, and processing metadata MUST
be treated as customer data. Collection, access, retention, and external transmission MUST
be limited to the documented service purpose. Customer data MUST NOT be used for model
training or shared across customers without explicit customer authorization. External
processors MUST have documented data-use and retention controls consistent with these rules.

Customer data MUST be encrypted in transit and at rest, including object storage, databases,
vector indexes, and backups. Keys MUST have restricted access and documented rotation
procedures. Logs and traces MUST exclude raw customer content and credentials by default.
Retention and deletion MUST cover source data, derived artifacts, and backup expiration.
Deleted or revoked sources MUST cease being retrievable before deletion or revocation is
reported as complete; backup retention limits MUST be disclosed.

### VI. OAuth2/OIDC Identity and Explicit Authorization

Customer registration and interactive authentication MUST use OAuth2/OIDC through a vetted
identity provider or standards-compliant identity service. Interactive clients MUST use
Authorization Code with PKCE. Token validation MUST verify signature, issuer, audience,
expiry, and applicable scopes. Authorization MUST additionally verify current workspace
membership and role. API credentials MUST be scoped, revocable, and bound to an authorized
tenant context. The product MUST NOT invent authentication protocols or implement its own
password storage. Authentication and authorization failures MUST fail closed.

### VII. API-First Design

Workspace management, ingestion, processing status, and knowledge queries MUST have documented
API contracts before implementation. Hosted chat and customer interfaces MUST use these
contracts. Contracts MUST define authentication, authorization, schemas, errors, processing
states, citations, and abstention outcomes. Mutations and asynchronous processing MUST define
retry and idempotency behavior. Breaking changes MUST use explicit versioning and a documented
migration path. Contract tests MUST protect supported client behavior.

### VIII. Simple Architecture and Incremental Vertical Slices

The architecture MUST use the fewest components needed to meet verified requirements, with
clear boundaries between identity, ingestion, retrieval, and answer generation. New services,
frameworks, provider abstractions, or distributed coordination MUST have a recorded requirement
and justification. Delivery MUST use small, independently verifiable vertical slices connecting
customer behavior to APIs, persistence, security, and tests. Each slice MUST have observable
acceptance criteria and preserve a working customer journey. Customers MUST NOT need to
configure embeddings, chunking, vector search, or LLM orchestration to use the knowledge
API and hosted chat.

### IX. Infrastructure as Code and Reproducible Delivery

Persistent infrastructure, access policies, encryption settings, and environment configuration
MUST be defined in version-controlled infrastructure as code and applied through repeatable
automation. Secrets MUST be referenced rather than embedded. Development, test, and production
MUST have explicit isolation. Deployments MUST include automated checks, controlled migrations,
and documented rollback or recovery procedures. Emergency manual changes MUST be recorded
and reconciled into code before the next routine deployment. Durable customer data MUST have
backups and a tested restore procedure.

### X. Automated Testing and Privacy-Preserving Observability

Each slice MUST include risk-appropriate automated tests: unit tests for business rules,
integration tests for storage and processing boundaries, API contract tests, and end-to-end
coverage of the supported customer journey. Isolation, authorization, invalid credentials,
citation correctness, insufficient evidence, and prompt injection MUST have regression coverage.
Changes to retrieval or generation MUST pass repeatable AI evaluations using representative
supported, unsupported, and conflicting-evidence questions.

Structured logs, metrics, and traces MUST follow requests and jobs through ingestion,
retrieval, and generation without exposing customer content. Telemetry MUST track processing
and query failures and latency, abstention and citation-validation outcomes, and model usage.
Tenant identifiers in telemetry MUST be access-controlled. Production services MUST have
health checks, documented operational targets, actionable alerts, and recovery runbooks.

## Product Scope and Constraints

The product turns an organization's private knowledge into a secure working AI API and hosted
chat in minutes without customer-managed RAG infrastructure.

V1 MUST follow `docs/product-vision.md`: registration, workspace creation, PDF upload, video,
document processing, embeddings, vector retrieval, natural-language questions, grounded
answers, citations, and tenant isolation. Video specifications MUST define supported inputs,
processing behavior, and citation locations before implementation.

Audio, SharePoint, Google Drive, Confluence, database connectors, autonomous agents, billing,
and multiple LLM providers are outside V1. Adding them MUST first update product scope and
an approved feature specification. Video support MUST NOT silently expand into a standalone
audio ingestion feature.

V1 acceptance MUST demonstrate: create account → create workspace → upload PDF → observe
processing completion → ask question → receive grounded answer → inspect source citation,
without manual configuration by the platform team. Hosted chat MUST retain the knowledge
API's identity, authorization, isolation, and evidence guarantees.

## Development Workflow and Quality Gates

Specifications MUST define user outcomes, scope, tenant boundaries, evidence and citation
behavior, and testable acceptance criteria. Plans MUST include a Constitution Check covering
applicable principles, trust boundaries, privacy, API contracts, operations, and proposed
complexity. Tasks MUST organize implementation into vertical slices and include verification.

Every change MUST receive review for constitutional compliance. Required automated tests,
static checks, dependency and secret scanning, and applicable AI evaluations MUST pass before
merge. Failed isolation, unsupported factual answers, invalid citations, and unresolved
exploitable security defects MUST block release. Infrastructure changes MUST have a reviewed
change plan. Production readiness MUST include telemetry, recovery procedures, and verification
of the relevant customer journey.

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
Violations MUST be recorded and corrected; security, isolation, privacy, and answer-grounding
violations MUST block the affected release. Maintainers MUST review the constitution when
product scope or trust boundaries change.

**Version**: 1.0.0 | **Ratified**: 2026-10-03 | **Last Amended**: 2026-10-03

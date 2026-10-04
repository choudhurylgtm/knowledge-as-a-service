## Core Principles

### I. Security First

- Treat all customer knowledge as sensitive and untrusted input.
- Apply least-privilege access.
- Authorization MUST deny access by default.
- Validate all external inputs. 
- Identify trust boundaries before implementation.
- Identify relevant abuse cases before implementation.
- Enforce file type and file size restrictions for uploads.
- Process uploaded content in an isolated manner.
- Treat retrieved document content as data/evidence, never as trusted system instructions.
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
- Citations SHOULD identify a usable source location when available, such as:
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

- Treat the following as customer data:
  - source documents
  - extracted text
  - document chunks
  - embeddings
  - questions
  - answers
  - processing metadata
- Customer data MUST only be collected and processed for documented product purposes.
- Customer data MUST NOT be shared between tenants.
- Customer data MUST NOT be used to train shared models without explicit customer authorization.
- External AI/model providers MUST have documented data-use and retention policies.
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
- Authentication SHOULD use a vetted identity provider or standards-compliant identity service.
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

- Use the fewest components necessary to satisfy verified requirements.
- Maintain clear boundaries between:
  - identity
  - ingestion
  - retrieval
  - answer generation
- Do NOT introduce a new service without a clear requirement.
- Do NOT introduce a new framework without justification.
- Do NOT introduce provider abstractions before they are needed.
- Avoid unnecessary distributed coordination.
- Build functionality as small end-to-end vertical slices.
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
- Environment configuration MUST be version controlled where appropriate.
- Secrets MUST be referenced, never embedded in infrastructure code.
- Development, test, and production environments MUST be explicitly isolated.
- Infrastructure changes MUST be repeatable.
- Deployments MUST include automated checks.
- Database/schema migrations MUST be controlled.
- Rollback or recovery procedures MUST be documented.
- Emergency manual infrastructure changes MUST be recorded.
- Manual changes MUST subsequently be reconciled into Infrastructure as Code.
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
- Use structured logging.
- Use metrics and distributed tracing where appropriate.
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
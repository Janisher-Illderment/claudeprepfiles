# PROJECT CLAUDE CONFIGURATION TEMPLATE v2.1
## Per-Project Setup — Customize for Each Repository
## File: [your-repo]/.claude/CLAUDE.md or [your-repo]/CLAUDE.md

> **Instructions**: Replace all `[CONFIGURE: ...]` sections with project-specific content.
> Delete sections that don't apply. The more context you provide, the better Claude performs.
>
> **Version**: 2.1 (March 2026)

---

## SECTION 1: PROJECT IDENTITY

```
Project Name:    [CONFIGURE: e.g., "Payment Service"]
Repository:      [CONFIGURE: e.g., "https://gitlab.com/org/payment-service"]
Team:            [CONFIGURE: e.g., "Platform Team — 4 engineers"]
Primary Language:[CONFIGURE: e.g., "TypeScript / Node.js 20"]
Tech Stack:      [CONFIGURE: e.g., "Express, PostgreSQL 15, Redis 7, Docker"]
Environment:     [CONFIGURE: e.g., "dev / staging / prod on AWS ECS"]
```

### Project Purpose
[CONFIGURE: 2-3 sentences. What does this service do? Who uses it? What's its role in the larger system?]

Example:
> This is the payment processing microservice responsible for handling all financial transactions. It integrates with Stripe for card processing and PayPal for digital wallets. It is called by the checkout service and the subscription manager.

---

## SECTION 2: ARCHITECTURE OVERVIEW

### System Design
[CONFIGURE: Brief description of the system's key components and how they connect]

```
[CONFIGURE: ASCII diagram or text representation of architecture]

Example:
API Gateway → Payment Service → [Stripe API]
                             → [PayPal API]
                             → PostgreSQL (transactions)
                             → Redis (idempotency keys)
                             → Event Bus (payment.completed events)
```

### Key Directories
```
[CONFIGURE: Map of important directories]

Example:
src/
├─ api/          # HTTP route handlers
├─ services/     # Business logic
├─ repositories/ # Database layer
├─ integrations/ # External API clients
├─ events/       # Event publishing/consuming
└─ config/       # Configuration and env validation

tests/
├─ unit/
├─ integration/
└─ e2e/
```

### Key Files
[CONFIGURE: List files that Claude should know about for this project]

Example:
- `src/config/index.ts` — All configuration, pulled from env vars
- `src/services/PaymentService.ts` — Core payment orchestration logic
- `prisma/schema.prisma` — Database schema (Prisma ORM)
- `docker-compose.yml` — Local development environment

---

## SECTION 3: CODE STANDARDS

### Language & Version
[CONFIGURE: Language version and any version-specific features in use]

Example: TypeScript 5.3 with strict mode. We use decorators (stage 3).

### Style Guide
[CONFIGURE: ESLint/Prettier/language-specific style rules that matter]

Example:
- ESLint config: `.eslintrc.js` (extends `@company/eslint-config`)
- Prettier: `.prettierrc` (single quotes, 100 char line limit)
- No `any` types — use `unknown` and type guard
- All functions must have explicit return types

### Naming Conventions
[CONFIGURE: Critical naming rules that affect code generation]

Example:
- Files: `PascalCase.ts` for classes, `camelCase.ts` for utilities
- Functions: `camelCase`, prefix booleans with `is/has/can`
- DB columns: `snake_case`, mapped to `camelCase` in code
- Events: `domain.action.past_tense` (e.g., `payment.charge.succeeded`)

### Code Patterns We Use
[CONFIGURE: Patterns to follow when generating code for this project]

Example:
- Repository pattern for all DB access (never query DB directly in services)
- Result type `{ data, error }` instead of throwing for expected failures
- Zod schemas for all external input validation
- All async functions must handle their own errors (no unhandled rejections)

### Code We Avoid
[CONFIGURE: Anti-patterns specific to this project]

Example:
- No `console.log` in production code — use the Logger service
- No direct `Date.now()` — use `clock.now()` (injectable for testing)
- No magic strings — use constants from `src/constants/`
- No synchronous file I/O

---

## SECTION 4: TESTING REQUIREMENTS

### Test Framework
[CONFIGURE: Testing stack details]

Example: Jest 29 + Supertest for integration tests. Coverage via c8.

### Coverage Requirements
[CONFIGURE: Thresholds and what they apply to]

Example:
- Minimum: 80% line coverage overall
- Services layer: 95% (business logic must be tested)
- API handlers: 100% for happy paths + common error paths

### Test Patterns
[CONFIGURE: How tests are structured in this project]

Example:
- Unit tests: test business logic with mocked dependencies
- Integration tests: use `docker-compose.test.yml` (real DB, real Redis)
- E2E tests: run against staging environment only
- Use `describe/it` blocks, not `test()` directly

### What We Test
[CONFIGURE: Specific testing priorities]

Example:
- Every payment state transition must have a test
- All idempotency scenarios must be tested
- Webhook signature validation must be tested with real/fake signatures
- Database transaction rollbacks must be tested

---

## SECTION 5: GIT WORKFLOW

### Branch Strategy
[CONFIGURE: Branching model]

Example: Trunk-based development. Short-lived feature branches off `main`. No `develop` branch.

### Commit Message Format
[CONFIGURE: Required commit format]

Example:
```
type(scope): short description

Body (optional): longer explanation

Refs: JIRA-123
```
Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`

### PR Requirements
[CONFIGURE: What PRs must include before merge]

Example:
- 1 approving review required (2 for changes to payment processing core)
- All CI checks must pass
- No merge commits — rebase only
- PR description must include: what changed, why, how to test

### Protected Branches
[CONFIGURE: Branches Claude should never force-push or delete]

Example: `main`, `release/*` — never force push, never delete.

---

## SECTION 6: DEPLOYMENT CONTEXT

### Environments
[CONFIGURE: Environment details]

```
dev     → local Docker Compose
staging → AWS ECS, auto-deployed on merge to main
prod    → AWS ECS, manual deploy via pipeline
```

### Deployment Method
[CONFIGURE: How code gets deployed]

Example: GitLab CI pipeline. `staging` auto-deploys. `prod` requires manual approval in GitLab.

### CI/CD Pipeline
[CONFIGURE: Pipeline stages and what each does]

Example:
1. `lint` — ESLint + TypeScript compile check
2. `test` — Unit tests + integration tests (Docker-in-Docker)
3. `build` — Docker image build and push to ECR
4. `deploy-staging` — Auto-deploys to staging
5. `deploy-prod` — Manual approval required

### Infrastructure
[CONFIGURE: Key infrastructure details Claude should know]

Example:
- Database: RDS PostgreSQL 15 (managed migrations with Prisma)
- Cache: ElastiCache Redis 7 (cluster mode disabled)
- Secrets: AWS Secrets Manager (never in env files in repo)

---

## SECTION 7: SECURITY CONSTRAINTS

### Authentication
[CONFIGURE: How auth works in this service]

Example: All requests must include a valid JWT in `Authorization: Bearer` header. JWTs are validated against the auth service's public key (loaded at startup from Secrets Manager).

### Secrets Management
[CONFIGURE: Where secrets live and how they're accessed]

Example:
- All secrets in AWS Secrets Manager
- Loaded at startup via `src/config/secrets.ts`
- NEVER in `.env` files committed to git
- NEVER logged, even partially

### Input Validation
[CONFIGURE: How inputs are validated]

Example: All external inputs (API requests, webhook payloads) validated with Zod schemas before entering business logic. Validation failures return 400 with sanitized error messages (no internal details).

### Data Classification
[CONFIGURE: What sensitive data this service handles]

Example:
- PCI-DSS scope: We handle card tokens (never raw card numbers)
- PII: email, name, address — must be encrypted at rest
- Audit log: all payment state changes logged with actor + timestamp

---

## SECTION 8: KNOWN ISSUES & GOTCHAS

[CONFIGURE: Things Claude should know to avoid creating new bugs or confusion]

Example:
- **Idempotency keys**: The `processPayment()` function is NOT idempotent by default. Always pass `idempotencyKey` in options. See `src/services/PaymentService.ts:45`.
- **Redis TTL bug**: There's a known race condition in `CacheService.getOrSet()` under high load (JIRA-892). Don't use it for payment state — use DB transactions instead.
- **Webhook retries**: Stripe retries webhooks for 3 days. Our handlers must be idempotent and must NOT re-process already-processed events.
- **Legacy endpoint**: `/api/v1/charge` is deprecated but still in use by mobile app v1. Do not remove it until mobile v1 usage drops to 0 (tracked in JIRA-1034).
- **Timezone**: All timestamps stored as UTC. Never use `new Date()` directly — use `clock.now()` so tests can control time.

---

## SECTION 9: EXTERNAL DEPENDENCIES

### APIs We Call
[CONFIGURE: External services this project depends on]

```
Service      | Version/Endpoint         | Purpose
-------------|--------------------------|------------------
Stripe       | API v2024-01             | Card processing
PayPal       | Orders API v2            | Digital wallet
Auth Service | Internal gRPC            | Token validation
Notification | Internal HTTP            | Send receipts
```

### Database Dependencies
[CONFIGURE: Database schema dependencies]

Example:
- `transactions` table owns this service — don't modify from other services
- `users` table is read-only in this service (owned by User Service)
- Cross-service queries must go through service APIs, not direct DB access

### Message Bus
[CONFIGURE: Events produced and consumed]

Example:
Produces: `payment.charge.succeeded`, `payment.charge.failed`, `payment.refund.completed`
Consumes: `subscription.renewal.requested`, `order.checkout.completed`

---

## SECTION 10: TEAM CONTEXT

### Code Ownership
[CONFIGURE: Who owns what — Claude can use this for review routing suggestions]

Example:
- `src/integrations/stripe/` — @alice (Stripe expert)
- `src/integrations/paypal/` — @bob
- `infrastructure/` — @carol (DevOps lead)
- `src/services/` — whole team, @alice is default reviewer

### Review Requirements
[CONFIGURE: Special review rules]

Example:
- Changes to `PaymentService.ts` require @alice + 1 other
- Changes to database migrations require @carol review
- Any change to error handling requires test coverage demonstration

### Communication
[CONFIGURE: How the team communicates about this codebase]

Example:
- Bug reports: GitLab issues with `bug` label
- Architecture decisions: ADR in `docs/adr/`
- Incidents: PagerDuty + Slack #payments-incidents
- Sprint work: GitLab milestones

---

## SECTION 11: MODEL ROUTING HINTS (NEW — March 2026)

> Guide Claude to use the right reasoning depth for tasks in this specific project.

### Use Opus 4.6 for:
[CONFIGURE: Task types in this project that warrant deep reasoning]

Example:
- Any change to `PaymentService.ts` core flow
- Database schema migrations
- Security-related changes (auth, secrets, PCI scope)
- Diagnosing production incidents
- Architecture changes affecting multiple services
- Performance analysis of slow queries

### Use Sonnet 4.6 for:
[CONFIGURE: Standard daily tasks for this project]

Example:
- Feature implementation from spec
- PR reviews for non-critical paths
- Writing tests for existing functionality
- API handler changes
- Documentation updates

### Use Haiku 4.5 for:
[CONFIGURE: Simple, repetitive tasks in this project]

Example:
- Generating CRUD endpoints from the established pattern
- Formatting/linting fixes
- Adding JSDoc comments to existing functions
- Simple type changes
- Renaming variables to match conventions

---

## SECTION 12: MONTHLY REVIEW CHECKLIST (NEW — March 2026)

> Run this review monthly to keep the CLAUDE.md accurate and useful.

### Configuration Accuracy
```
☐ Are the tech stack versions still current?
☐ Are the known issues still valid? (close resolved ones)
☐ Are the named engineers still on the team?
☐ Are the external dependencies still the same?
☐ Has the architecture changed significantly?
```

### New Information to Add
```
☐ Any new patterns that have become standard?
☐ Any new gotchas discovered this month?
☐ Any changes to testing requirements?
☐ Any new security constraints?
☐ Any deprecated features to flag?
```

### Model Routing Calibration
```
☐ Were there tasks where we used too expensive a model?
☐ Were there tasks where we should have used Opus but didn't?
☐ Update the routing hints based on this month's experience
```

### Team Changes
```
☐ Update code ownership if team changed
☐ Update review requirements if processes changed
☐ Update communication channels if they changed
```

---

*Template v2.1 — March 2026 | ClaudePrepFiles | MIT License*
*Source: https://gitlab.com/smorente.syntax/claudeprepfiles*

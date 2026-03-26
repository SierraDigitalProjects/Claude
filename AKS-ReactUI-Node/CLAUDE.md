<!--
  CLAUDE.md — AI Instruction File for AKS React & Node.js Microservices Project
  ==============================================================================
  This file is automatically loaded by Claude Code at the start of every session
  in this directory. It defines the rules, standards, and constraints that Claude
  must follow when generating, modifying, or reviewing code in this project.

  WHO THIS IS FOR:
    - Claude AI (primary consumer — read and enforced automatically)
    - Developers onboarding to this project (reference for standards)

  HOW IT WORKS:
    - Claude reads this file before every task and applies all rules without
      being asked. You do not need to repeat these instructions in your prompts.
    - The "AI Efficiency & Context Rules" section at the top is the most critical
      — always specify the target layer (Controller / Service / Model) in prompts
      to prevent Claude from generating code that bleeds across boundaries.

  SCOPE: Azure Kubernetes Service (AKS) — React frontend + Node.js microservices
         with gRPC internal communication and Azure Service Bus for async events.
  STACK: React 18 | Node.js 20 LTS | TypeScript 5 | Prisma 5 | Helm 3 | gRPC
  LAST UPDATED: 2026-03-26
-->

# AKS Enterprise Project: React & Node.js Microservices (MVC)

## 🤖 AI Efficiency & Context Rules (CRITICAL)
- **Context Pinning:** When asking AI to create a feature, specify the **Layer** (e.g., "Create a Service layer method for...") to prevent code bloating the Controller.
- **Delta Only:** If the AI suggests a full file, ask it to "Provide only the delta/diff" for existing microservices to save tokens and prevent silent removals.
- **DRY/SOLID Enforcement:** AI must prioritize **Dependency Injection**. Services should be injected into Controllers to facilitate easy mocking.
- **Type Safety:** Always use **TypeScript**. AI must generate Interfaces for all API responses to ensure the React frontend remains type-synced with Node.js.
- **Error Handling:** AI must use a global error-handling middleware pattern. Do not allow `try-catch` blocks to be duplicated in every controller.
- **No Hallucinated APIs:** If unsure whether an SDK method or Kubernetes API exists, flag it and ask — never invent plausible-sounding calls.
- **No New Libraries:** Do not introduce a new npm package or Helm dependency without flagging it and confirming with the user first.
- **No Hardcoded Values:** Never hardcode secrets, image tags, service URLs, or cluster-specific config — always reference Key Vault or Helm values.

## 📌 Dependency Versions (AI must target these)
- Node.js: 20 LTS
- TypeScript: ^5.x
- React: ^18.x
- `@sap/xssec`: ^3.x
- `@sap-cloud-sdk/http-client`: ^3.x
- Jest: ^29.x / Vitest: ^1.x
- Prisma: ^5.x
- `opossum`: ^8.x (circuit breaker)
- Helm: ^3.x
- Kubernetes API: 1.28+

## 📐 Naming Conventions
- **Files:** kebab-case (`order-service.ts`, `purchase-order-controller.ts`)
- **Classes:** PascalCase (`OrderService`, `PurchaseOrderController`)
- **Interfaces:** PascalCase with `I` prefix (`IOrderResponse`, `IUserClaims`)
- **REST Routes:** kebab-case, versioned (`/api/v1/purchase-orders`)
- **Kubernetes resources:** kebab-case (`order-service-deployment`, `api-gateway-ingress`)
- **Helm values:** camelCase (`replicaCount`, `imageTag`, `keyVaultSecretName`)
- **Proto messages:** PascalCase (`GetOrderRequest`, `OrderResponse`)
- **gRPC service methods:** PascalCase (`GetOrder`, `CreatePurchaseRequisition`)

## 🏗 Microservice & MVC Architecture
- **Separation of Concerns:**
  - **View:** React.js (Client) — Logic-less components, state managed by hooks.
  - **Controller:** Node.js API routes — Orchestrates data flow and validates input.
  - **Model:** Node.js Services/Data Access Layer — Handles HANA/Postgres logic and External APIs.
- **Service Boundaries:** Each Microservice must have its own database schema (Loose Coupling).
- **Communication:** Use **gRPC** for synchronous internal calls and **Azure Service Bus** for async events.

## 📐 TypeScript Rules
- `strict: true` in all `tsconfig.json` files — no exceptions.
- All API response shapes must have a shared Interface in `shared/types/`.
- Never use `any` — use `unknown` with type guards, or proper generics.
- Use **Zod** for runtime validation of all external API responses (S/4HANA, 3rd party).
- AI must generate types for all new API contracts before writing implementation code.

## 📡 gRPC / Service Contracts
- `.proto` files are the source of truth — never deviate from them in implementation.
- Use **Buf** for linting proto files: `buf lint`.
- Breaking proto changes (removing fields, changing types) require a major version bump.
- AI must never modify a `.proto` file and implementation file in a single step — always confirm the contract change first.

## 🔐 Security & RBAC (BTP XSUAA + AKS)
- **JWT Validation:** Every Node.js microservice must use `@sap/xssec` to verify tokens.
- **Scope Check:** Enforce RBAC at the Controller level: `req.authInfo.checkLocalScope('Admin')`.
- **Secrets:** Use **Azure Key Vault** references in Kubernetes manifests — never hardcode env vars or plain Helm values.
- **Network Policies:** Every microservice must have a Kubernetes `NetworkPolicy` that restricts ingress to known services only.
- **Image Scanning:** Container images must be scanned with Trivy before pushing to ACR.

## ⚠️ Error Handling
- Use a **global error-handling middleware** in each Express service (`middleware/error-handler.ts`) — no duplicated `try-catch` in controllers.
- Controllers catch errors and pass them to `next(err)` — never handle errors inline in routes.
- Map known error types (XSUAA 401, gRPC status codes, circuit breaker open) to appropriate HTTP status codes.
- API errors must follow **RFC 7807 Problem Details** format.
- Never return stack traces or internal error details to the client.

## 🧪 Testing & Quality (Mandatory)
- **Unit Tests:** Frontend: Vitest/React Testing Library. Backend: Jest for Service/Model layers.
- **Contract Testing:** Use **Pact** for Microservice-to-Microservice API stability.
- **Performance:** Every Node.js service must include a `/health` (liveness) and `/ready` (readiness) endpoint for AKS probes.
- **Coverage:** Minimum 80% line coverage on Service and Model layers.
- **gRPC:** Use `grpc-mock` to test gRPC client calls in unit tests.

## 💻 Service & Logic Standards
- **Clean Architecture:** Business logic stays in "Service" classes — never in Controllers or route handlers.
- **Resilience:** Use `opossum` for Circuit Breakers on all external SAP/S4 calls.
- **Persistence:** Use Prisma. No raw SQL — use Prisma query builder or `$queryRaw` only for documented edge cases.
- **API Design:** Version all routes (`/api/v1/`). Follow REST conventions. Responses follow RFC 7807 on error.

## 🗄 Database Migrations
- Use **Prisma Migrate** for all schema changes — never manual SQL or ad-hoc scripts.
- Migrations must be **backwards-compatible** (additive only) to support zero-downtime rolling deploys on AKS.
- AI must never generate a migration that drops a column or table without an explicit confirmation from the user.
- Migration files are committed to source control and reviewed in PRs like application code.

## 🔄 CI/CD (GitHub Actions / Azure DevOps)
- Every PR must pass: lint, type-check (`tsc --noEmit`), unit tests, contract tests (Pact), and Docker build.
- Image tags must be the **Git commit SHA** — never use `latest` in production Kubernetes manifests.
- Helm values for secrets must reference Azure Key Vault — never plain string values.
- Staging deployment is automatic on merge to `main`; production requires manual approval gate.

## 🛠 Deployment & Workflow (AKS)
- **Containerization:** Multistage `Dockerfile` (Node 20 Alpine builder → Node 20 Alpine runtime).
- **Orchestration:** Helm charts for all Kubernetes manifests.
- **Ingress:** NGINX Ingress for routing and SSL termination.
- **Commands:**
  - `docker build -t <service-name>:<git-sha> .`
  - `helm upgrade --install <release> ./charts --set image.tag=<git-sha>`
  - `kubectl logs -f <pod-name>`
  - `buf lint` (validate proto files)
  - `npx prisma migrate deploy` (apply migrations)

## 📊 Observability
- **Logging:** Use `winston` with JSON formatter — never `console.log()` in production.
- **Log Fields:** Every entry must include `correlationId`, `service`, `userId`, `timestamp`, `traceId`.
- **Tracing:** Use OpenTelemetry SDK. Propagate `traceparent` header across all gRPC and HTTP calls.
- **Metrics:** Expose Prometheus metrics at `/metrics` using `prom-client`.
- **Alerts:** Every service must have an Azure Monitor alert for error rate > 1% and p99 latency > 2s.

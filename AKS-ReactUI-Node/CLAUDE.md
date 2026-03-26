# AKS Enterprise Project: React & Node.js Microservices (MVC)

## 🏗 Microservice & MVC Architecture
- **Separation of Concerns:** - **View:** React.js (Client) - Logic-less components, state managed by hooks.
  - **Controller:** Node.js API routes - Orchestrates data flow and validates input.
  - **Model:** Node.js Services/Data Access Layer - Handles HANA/Postgres logic and External APIs.
- **Service Boundaries:** Each Microservice must have its own database schema (Loose Coupling).
- **Communication:** Use **gRPC** for synchronous internal calls and **Azure Service Bus** for async events.

## 🤖 AI Efficiency & Context Rules (IMPORTANT)
- **Context Pinning:** When asking AI to create a feature, specify the **Layer** (e.g., "Create a Service layer method for...") to prevent code bloating the Controller.
- **Dry/Solid Enforcement:** AI must prioritize **Dependency Injection**. Services should be injected into Controllers to facilitate easy mocking.
- **Boilerplate Suppression:** If the AI suggests a full file, ask it to "Provide only the delta/diff" for existing microservices to save tokens.
- **Type Safety:** Always use **TypeScript**. AI must generate Interfaces for all API responses to ensure the React frontend remains type-sync'ed with Node.js.
- **Error Handling:** AI must use a global error-handling middleware pattern. Do not allow `try-catch` blocks to be duplicated in every controller.

## 🔐 Security & RBAC (BTP XSUAA + AKS)
- **JWT Validation:** Every Node.js microservice must use `@sap/xssec` to verify tokens.
- **Scope Check:** Enforce RBAC at the Controller level: `req.authInfo.checkLocalScope('Admin')`.
- **Secrets:** Use **Azure Key Vault** references in Kubernetes manifests; never hardcode env vars.

## 🧪 Testing & Quality (Mandatory)
- **Unit Tests:** - Frontend: Vitest/React Testing Library. - Backend: Jest for Service/Model layers.
- **Contract Testing:** Use **Pact** for Microservice-to-Microservice API stability.
- **Performance:** Every Node.js service must include a `/health` and `/ready` endpoint for AKS Liveness/Readiness probes.

## 💻 Service & Logic Standards
- **Clean Architecture:** Business logic stays in "Service" classes.
- **Resilience:** Use `opossum` for Circuit Breakers on all external SAP/S4 calls.
- **Persistence:** Use Prisma or Sequelize. No raw SQL.

## 🛠 Deployment & Workflow (AKS)
- **Containerization:** Multistage `Dockerfile` (Node-Alpine).
- **Orchestration:** Helm charts for Kubernetes manifests.
- **Ingress:** NGINX Ingress for routing and SSL.
- **Commands:**
  - `docker build -t <service-name> .`
  - `helm upgrade --install <release> ./charts`
  - `kubectl logs -f <pod-name>`
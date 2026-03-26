<!--
  CLAUDE.md — AI Instruction File for SAP BTP CAP & Fiori Project
  ================================================================
  This file is automatically loaded by Claude Code at the start of every session
  in this directory. It defines the rules, standards, and constraints that Claude
  must follow when generating, modifying, or reviewing code in this project.

  WHO THIS IS FOR:
    - Claude AI (primary consumer — read and enforced automatically)
    - Developers onboarding to this project (reference for standards)

  HOW IT WORKS:
    - Claude reads this file before every task and applies all rules without
      being asked. You do not need to repeat these instructions in your prompts.
    - The "AI Behavior Rules" section at the top is the most critical — it
      defines how Claude should approach every code change in this repo.

  SCOPE: SAP BTP — CAP (CDS) backend services + Fiori Elements / UI5 frontend.
  STACK: @sap/cds ^8.x | Node.js 20 LTS | HANA Cloud | SAP Fiori Elements
  LAST UPDATED: 2026-03-26
-->

# SAP BTP Enterprise Project: Modular CAP, Fiori, RBAC & Quality Standards

## 🤖 AI Behavior Rules (CRITICAL)
- **Delta Only:** For existing files, provide ONLY the changed lines/functions — never rewrite an entire file unless explicitly asked.
- **No Hallucinated APIs:** If unsure whether a CDS API or SAP SDK method exists, flag it and ask — never invent plausible-sounding calls.
- **No Silent Degradation:** Do not silently remove annotations, `@restrict` rules, or service restrictions when modifying a file.
- **Layer Discipline:** Before generating code, confirm the target layer (`db/`, `srv/`, `app/`) — never bleed logic across layers.
- **No Hardcoded Values:** Never hardcode tenant IDs, service URLs, credentials, or environment-specific config.
- **Ask Before Cross-Layer:** If a feature requires changes across more than one layer, list the affected files and confirm before proceeding.

## 📌 Dependency Versions (AI must target these)
- `@sap/cds`: ^8.x
- `@sap/cds-dk`: ^8.x
- `@sap-cloud-sdk/http-client`: ^3.x
- `@sap/xssec`: ^3.x
- Node.js: 20 LTS
- HANA Cloud: latest dialect (use `hana` profile in `package.json`)

## 📐 Naming Conventions
- **CDS Entities:** PascalCase singular (`Order`, `Employee`, `PurchaseRequisition`)
- **CDS Services:** PascalCase with `Service` suffix (`AdminService`, `ProcurementService`)
- **Handler Files:** Match service name in kebab-case (`admin-service.js`, `procurement-service.js`)
- **CDS Namespaces:** Reverse-domain format (`com.company.module`)
- **Action/Function names:** camelCase (`submitOrder`, `cancelRequest`)
- **i18n keys:** `snake_case` (`order_status`, `employee_name`)

## 🏗 Modular Architecture Rules
- **Layered Separation (SoC):**
  - `db/`: Core domain models & persistence logic (HANA-optimized). No UI or Service logic.
  - `srv/`: Persona-based services (e.g., `AdminService`, `ProcurementService`, `EmployeeService`).
  - `app/`: Fiori Elements/UI5 modules. One subfolder per standalone application.
- **Cross-Module Usage:** Use `using { ... } from './common'` to share types/aspects across modules.
- **Service Partitioning:** Break large services into smaller, functional OData services to optimize metadata loading, performance, and security.

## 🛠 Commands & Workflow
- **Start Dev:** `cds watch`
- **Build MTA:** `mbt build`
- **Deploy BTP:** `cf deploy mta_archives/*.mtar`
- **Modular Mocking:** Use `db/data/` for shared mocks; `srv/external/` for API mocks.
- **Linting:** `cds lint` (Enforce SAP-standard model quality) and `eslint .`.

## 💻 Service & Logic Standards
- **Thin Services:** `.cds` files should only contain projections and minimal annotations.
- **Handler Structure:** Place logic in `srv/*.js` or `srv/*.ts` using `async/await`.
  - Use **Service-to-Service calls** (`cds.connect.to`) for cross-service logic.
- **External APIs:** Always import S/4HANA or 3rd party metadata via `cds import`. Store in `srv/external/`.
- **HANA-Specifics:** Use `Decimal(15,2)` for currency. No raw SQL; use **CQL** (CDS Query Language).

## ⚠️ Error Handling
- Throw `new cds.error(message, { status: 400 })` — never throw raw JS errors in handlers.
- Use `req.reject()` for business validation failures, not exceptions.
- Use `req.error()` to accumulate multiple validation messages before returning.
- Place global error logging in `srv/lib/error-handler.js` — not inline per-handler.
- Never expose internal error details or stack traces to the OData response.

## 📨 Events & Messaging
- Use `cds.emit()` for domain events within the same service.
- For cross-service async events, use SAP Event Mesh via `messaging` config in `package.json`.
- Event payloads must match CloudEvents spec format.
- Never emit events inside DB transactions unless explicitly required and confirmed by the user.
- Subscribe to events in `srv/` handlers using `messaging.on('topic', handler)` pattern.

## 🎨 UI & Annotation Modularity
- **Decoupled UI:**
  - `db/`: Zero UI annotations.
  - `srv/`: Minimal UI annotations (only those required for basic OData display).
  - `app/<module>/annotations.cds`: ALL Fiori Elements layout/UI logic goes here.
- **Shared UI:** Use `app/common.cds` for global Value Helps and common Labels.
- **I18n:** Use separate `_i18n` folders in `db`, `srv`, and `app` for granular translations.

## 🔐 Security & Compliance
- **Instance Security:** Annotate all services with `@requires: 'authenticated-user'`.
- **Role-Based Access (RBAC):** Use `@restrict` on entities to map scopes to CRUD actions:
  ```cds
  annotate MyService.Orders with @(restrict: [
    { grant: ['READ'], to: 'Viewer', where: 'buyer = $user' },
    { grant: ['*'], to: 'Admin' }
  ]);
  ```
- **Audit Logging:** Use `@sap/audit-logging` for all data access on sensitive entities (PII, financial).
- **Input Validation:** Validate all user-supplied filter and query params in `before` handlers.
- **Never expose draft-enabled entities** without explicit RBAC — drafts bypass some OData filters.

## 🧪 Testing
- Use `@sap/cds/lib/test` utilities for unit testing CAP service handlers.
- Use `cds.test()` to spin up an in-process CAP server for integration tests — no real HANA needed.
- Mock external destinations with `cds.env.requires.<service>.credentials` overrides in test config.
- Every handler must have a test covering: success (2xx), authorization failure (403), and validation error (400).
- Use `db/data/*.csv` fixtures for consistent test data — never hardcode test data inline.

## 📊 Observability
- **Logging:** Use `cds.log()` — never `console.log()` in production handlers.
- **Structured Logs:** Ensure logs include `correlationId` (from `req.headers['x-correlationid']`) and `user`.
- **Tracing:** Propagate the SAP Correlation ID header (`x-correlationid`) through all external calls.
- **Performance:** Log duration of all S/4HANA / external API calls at `DEBUG` level.

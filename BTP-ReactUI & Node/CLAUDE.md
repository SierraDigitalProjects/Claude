<!--
  CLAUDE.md — AI Instruction File for SAP BTP React & Node.js Project
  ====================================================================
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

  SCOPE: SAP BTP — React.js frontend + Node.js (Express) backend + SAP Approuter.
  STACK: React 18 | Node.js 20 LTS | TypeScript 5 | @sap-cloud-sdk ^3.x | XSUAA
  LAST UPDATED: 2026-03-26
-->

# SAP BTP Enterprise Project: React Frontend & Node.js Backend

## 🤖 AI Behavior Rules (CRITICAL)
- **Delta Only:** For existing files, provide ONLY the changed lines/functions — never rewrite an entire file unless explicitly asked.
- **No Hallucinated APIs:** If unsure whether an SDK method exists, flag it and ask — never invent plausible-sounding calls.
- **No Silent Degradation:** Do not silently remove middleware, auth checks, or error handlers when modifying a file.
- **Layer Discipline:** Specify the layer when requesting code (e.g., "Add a Service layer method for...") — never mix Controller and Service logic.
- **No Hardcoded Values:** Never hardcode URLs, tenant IDs, credentials, or environment-specific config. Always use Destination Service or env vars.
- **No New Libraries:** Do not introduce a new npm package without flagging it and confirming with the user first.

## 📌 Dependency Versions (AI must target these)
- Node.js: 20 LTS
- `@sap/xssec`: ^3.x
- `@sap-cloud-sdk/http-client`: ^3.x
- `@sap-cloud-sdk/connectivity`: ^3.x
- React: ^18.x
- TypeScript: ^5.x
- `@ui5/webcomponents-react`: ^2.x
- `@tanstack/react-query`: ^5.x
- Zustand: ^4.x
- Jest: ^29.x / Vitest: ^1.x
- Playwright: ^1.x

## 📐 Naming Conventions
- **Files:** kebab-case (`order-service.ts`, `purchase-order-list.tsx`)
- **Classes / React Components:** PascalCase (`OrderService`, `PurchaseOrderList`)
- **Interfaces / Types:** PascalCase with `I` prefix for interfaces (`IOrderResponse`), plain PascalCase for types (`OrderStatus`)
- **REST Routes:** kebab-case (`/api/v1/purchase-orders`)
- **Node.js services:** camelCase methods (`getOrderById`, `createPurchaseRequisition`)
- **React hooks:** `use` prefix camelCase (`useOrderData`, `useAuthInfo`)

## 🏗 Modular Architecture Rules
- **Layered Separation:**
  - `client/`: React.js application (Vite). No backend logic.
  - `server/`: Node.js API (Express). Handles business logic & S/4HANA connectivity.
  - `router/`: SAP Approuter (Essential for XSUAA authentication and routing).
- **API Design:** Follow the **Controller-Service-Repository** pattern in Node.js.
- **Shared Contracts:** TypeScript interfaces in `shared/types/` to sync data shapes between Client and Server.

## 🔗 API Design Standards
- Version all APIs: `/api/v1/orders` — never break existing route contracts without a version bump.
- Use HTTP status codes correctly: `200`/`201`/`204`/`400`/`401`/`403`/`404`/`422`/`500`.
- API errors must follow **RFC 7807 Problem Details** format:
  ```json
  { "type": "...", "title": "...", "status": 400, "detail": "...", "instance": "..." }
  ```
- Never return stack traces or internal error details to the client.
- All list endpoints must support pagination (`?limit=&offset=` or cursor-based).

## 📐 TypeScript Rules
- `strict: true` in all `tsconfig.json` files — no exceptions.
- All API response shapes must have a shared Interface in `shared/types/`.
- Never use `any` — use `unknown` with type guards, or proper generics.
- Use **Zod** for runtime validation of all external API responses (S/4HANA, 3rd party).
- AI must generate types for all new API contracts before writing implementation code.

## 🔐 Security & RBAC (XSUAA)
- **Authentication:** All requests must pass through the **SAP Approuter**.
- **JWT Validation:** Use `@sap/xssec` in Node.js to validate the incoming JWT from Approuter.
- **RBAC Enforcement:**
  - Create middleware in Node.js to check scopes: `req.authInfo.checkLocalScope('Admin')`.
  - In React, use a `ProtectedRoute` component to hide UI elements based on JWT scopes.
- **CORS:** Ensure BTP Approuter handles CORS; do not hardcode allowed origins in Node.js.
- **Input Sanitization:** Validate and sanitize all incoming request bodies using Zod schemas in the Controller layer.

## ⚠️ Error Handling
- Use a **global error-handling middleware** in Express (`server/middleware/error-handler.ts`) — no duplicated `try-catch` in every controller.
- Controllers catch errors and pass them to `next(err)` — never handle errors inline.
- Map known error types (XSUAA 401, S/4HANA 404) to appropriate HTTP status codes in the global handler.
- React: Use an `ErrorBoundary` component at the route level to catch render errors.

## 🧪 Testing & Quality
- **Backend (Unit/Integration):** Use `Jest` and `Supertest`. Every API endpoint must have a test for Success (2xx) and Unauthorized (401/403).
- **Frontend (UI Testing):** Use `Vitest` + `React Testing Library` for component unit tests.
- **E2E Testing:** Use `Playwright` to test the full flow from Approuter → React → Node.js.
- **Mocking:** Use `msw` (Mock Service Worker) for frontend testing to simulate Node.js API responses.
- **Coverage:** Minimum 80% line coverage on `server/services/` and `server/controllers/`.

## 🗂 State Management Rules
- **Server state** (API data, loading, caching): React Query (TanStack) ONLY — no Redux for API data.
- **Global UI state** (auth info, theme, notifications): Zustand.
- **Local component state**: `useState` / `useReducer`.
- AI must not introduce a new state management library — use only what is listed above.
- Never store sensitive JWT data in `localStorage` — use in-memory state only.

## 🎨 React UI Standards
- **Component Library:** Use **SAP UI5 Web Components for React** (`@ui5/webcomponents-react`) to maintain Fiori Horizon design language.
- **Styling:** CSS Modules only. No inline styles. Follow Fiori design guidelines for spacing and typography.
- **Accessibility:** All interactive components must have `aria-label` or `aria-labelledby`.

## 💻 Development & Connectivity
- **SAP Cloud SDK:** Use `@sap-cloud-sdk/resilience` and `@sap-cloud-sdk/connectivity` for all S/4HANA or external API calls.
- **Destinations:** Never hardcode URLs. Fetch connection details via the BTP Destination Service.
- **HANA Persistence:** Use `hdb` or `sequelize` with `hdb-pool` for HANA Cloud connectivity.

## 📊 Observability
- **Logging:** Use a structured logger (e.g., `winston` with JSON format) — never `console.log()` in production.
- **Log Fields:** Every log entry must include `correlationId`, `userId`, `service`, `timestamp`.
- **Tracing:** Read and forward the `x-correlationid` header from Approuter through all downstream calls.
- **Health Endpoints:** Every Node.js service must expose `/health` (liveness) and `/ready` (readiness) endpoints.

## 🛠 Commands & Workflow
- **Local Client:** `cd client && npm run dev`
- **Local Server:** `cd server && npm run dev`
- **Local Approuter:** Use `xs-approuter` locally with a `default-env.json` for XSUAA mocking.
- **Build:** `mbt build` (Generates the MTA archive).
- **Deploy:** `cf deploy mta_archives/*.mtar`.
- **Linting:** `eslint . --ext .ts,.tsx,.js` and `tsc --noEmit` for type checking.

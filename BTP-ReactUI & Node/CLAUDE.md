# SAP BTP Enterprise Project: React Frontend & Node.js Backend

## 🏗 Modular Architecture Rules
- **Layered Separation:**
  - `client/`: React.js application (Vite/CRA). No backend logic.
  - `server/`: Node.js API (Express/Fastify). Handles business logic & S/4HANA connectivity.
  - `router/`: SAP Approuter (Essential for XSUAA authentication and routing).
- **API Design:** Use REST or GraphQL. Follow the **Controller-Service-Repository** pattern in Node.js.
- **Shared Contracts:** Use TypeScript interfaces to sync data shapes between Client and Server.

## 🔐 Security & RBAC (XSUAA)
- **Authentication:** All requests must pass through the **SAP Approuter**.
- **JWT Validation:** Use `@sap/xssec` in Node.js to validate the incoming JWT from Approuter.
- **RBAC Enforcement:**
  - Create middleware in Node.js to check scopes: `req.authInfo.checkLocalScope('Admin')`.
  - In React, use a `ProtectedRoute` component to hide UI elements based on JWT scopes.
- **CORS:** Ensure BTP Approuter handles CORS; do not hardcode allowed origins in Node.js.

## 🧪 Testing & Quality
- **Backend (Unit/Integration):** Use `Jest` and `Supertest`. Every API endpoint must have a test case for Success (200) and Unauthorized (401/403).
- **Frontend (UI Testing):** Use `Vitest` + `React Testing Library` for component unit tests.
- **E2E Testing:** Use `Playwright` or `Cypress` to test the full flow from Approuter -> React -> Node.js.
- **Mocking:** Use `msw` (Mock Service Worker) for frontend testing to simulate Node.js API responses.

## 💻 Development & Connectivity
- **SAP Cloud SDK:** Use `@sap-cloud-sdk/resilience` and `@sap-cloud-sdk/connectivity` for all S/4HANA or external API calls.
- **Destinations:** Never hardcode URLs. Fetch connection details via the BTP Destination Service.
- **HANA Persistence:** Use `hdb` or `sequelize` with the `hdb-pool` for HANA Cloud connectivity.

## 🎨 React UI Standards
- **Component Library:** Use **SAP UI5 Web Components for React** to maintain Fiori Horizon design language.
- **State Management:** Use `React Query` (TanStack Query) for server state and `Zustand` or `Context API` for local UI state.
- **Styling:** Use CSS Modules or Tailwind CSS. Follow the Fiori design guidelines for spacing and typography.

## 🛠 Commands & Workflow
- **Local Client:** `cd client && npm run dev`
- **Local Server:** `cd server && npm run dev`
- **Local Approuter:** Use `xs-approuter` locally with a `default-env.json` for XSUAA mocking.
- **Build:** `mbt build` (Generates the MTA archive).
- **Deploy:** `cf deploy mta_archives/*.mtar`.
- **Linting:** `eslint . --ext .ts,.tsx,.js`.
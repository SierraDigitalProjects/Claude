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
- **No Silent Degradation:** Do not silently remove middleware, auth checks, or error handlers when modifying a file.
- **Layer Discipline:** Specify the layer when requesting code (e.g., "Add a Service layer method for...") — never mix Controller and Service logic.
- **Destination Service:** Always use the BTP Destination Service for URLs — never hardcode them directly.

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

## 🛠 Commands & Workflow
- **Local Client:** `cd client && npm run dev`
- **Local Server:** `cd server && npm run dev`
- **Local Approuter:** Use `xs-approuter` locally with a `default-env.json` for XSUAA mocking.
- **Build:** `mbt build` (Generates the MTA archive).
- **Deploy:** `cf deploy mta_archives/*.mtar`.
- **Linting:** `eslint . --ext .ts,.tsx,.js` and `tsc --noEmit` for type checking.

## 💻 Development & Connectivity
- **SAP Cloud SDK:** Use `@sap-cloud-sdk/resilience` and `@sap-cloud-sdk/connectivity` for all S/4HANA or external API calls.
- **HANA Persistence:** Use `hdb` or `sequelize` with `hdb-pool` for HANA Cloud connectivity.

## 📊 Observability
- **Logging:** Use `winston` with JSON format for structured logging.
- **Log Fields:** Include `service` and `timestamp` in every log entry (plus the global `correlationId` and `userId`).
- **Health Endpoints:** Every Node.js service must expose `/health` (liveness) and `/ready` (readiness) endpoints.

@rules/coding-style.md
@rules/testing.md
@rules/security.md

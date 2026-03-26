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
- **No Silent Degradation:** Do not silently remove annotations, `@restrict` rules, or service restrictions when modifying a file.
- **Layer Discipline:** Before generating code, confirm the target layer (`db/`, `srv/`, `app/`) — never bleed logic across layers.
- **Ask Before Cross-Layer:** If a feature requires changes across more than one layer, list the affected files and confirm before proceeding.

## 📌 Dependency Versions (AI must target these)
- `@sap/cds`: ^8.x
- `@sap/cds-dk`: ^8.x
- `@sap-cloud-sdk/http-client`: ^3.x
- `@sap/xssec`: ^3.x
- Node.js: 20 LTS
- HANA Cloud: latest dialect (use `hana` profile in `package.json`)

## 🛠 Commands & Workflow
- **Start Dev:** `cds watch`
- **Build MTA:** `mbt build`
- **Deploy BTP:** `cf deploy mta_archives/*.mtar`
- **Modular Mocking:** Use `db/data/` for shared mocks; `srv/external/` for API mocks.
- **Linting:** `cds lint` (Enforce SAP-standard model quality) and `eslint .`.

## 📊 Observability
- **Logging:** Use `cds.log()` — never `console.log()` in production handlers.
- **Structured Logs:** Include `correlationId` from `req.headers['x-correlationid']` in every log entry.
- **Tracing:** Propagate `x-correlationid` through all external calls.
- **Performance:** Log duration of all S/4HANA / external API calls at `DEBUG` level.

@rules/coding-style.md
@rules/testing.md
@rules/security.md

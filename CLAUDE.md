<!--
  CLAUDE.md — Global AI Instruction File (Workspace Root)
  ========================================================
  This file is automatically loaded by Claude Code for every project in this
  workspace. Each subdirectory has its own CLAUDE.md with project-specific
  rules that extend and override these where needed.

  WHO THIS IS FOR:
    - Claude AI (primary consumer — read and enforced automatically)
    - Developers onboarding to any project in this workspace

  SCOPE: All projects — BTP CAP/Fiori, BTP React/Node, AKS Microservices, Python MCP
  LAST UPDATED: 2026-03-26
-->

# Global Standards (All Projects)

## 🤖 AI Behavior Rules (CRITICAL)
- **Delta Only:** For existing files, provide ONLY the changed lines/functions — never rewrite an entire file unless explicitly asked.
- **No Hallucinated APIs:** If unsure whether an SDK or framework method exists, flag it and ask — never invent plausible-sounding calls.
- **No Hardcoded Values:** Never hardcode credentials, secrets, tenant IDs, service URLs, or environment-specific config — always use environment variables, secret stores, or platform config services.
- **No New Dependencies:** Do not introduce a new package or library without flagging it and confirming with the user first.

## 🧪 Testing Standards
- Minimum **80% line coverage** on all service and business logic layers.
- Never call real external systems (SAP, Azure, databases) in unit tests — always mock or stub them.

## 📊 Observability Standards
- Never use `console.log()` or `print()` in production code — use the project's designated structured logger.
- Every log entry must include a `correlationId` (or equivalent trace header) and `userId`.
- Always read and forward the correlation ID header through all downstream and external calls.

## 🔐 Security Standards
- Never return stack traces or internal error details to API clients or tool consumers.

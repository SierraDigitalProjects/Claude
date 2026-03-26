<!--
  CLAUDE.md — AI Instruction File for SAP BTP Python MCP Server Project
  ======================================================================
  This file is automatically loaded by Claude Code at the start of every session
  in this directory. It defines the rules, standards, and constraints that Claude
  must follow when generating, modifying, or reviewing code in this project.

  WHO THIS IS FOR:
    - Claude AI (primary consumer — read and enforced automatically)
    - Developers onboarding to this project (reference for standards)

  HOW IT WORKS:
    - Claude reads this file before every task and applies all rules without
      being asked. You do not need to repeat these instructions in your prompts.
    - The "AI Behavior Rules" and "Tool & Resource Development" sections are the
      most critical — MCP tool docstrings are the LLM's API contract and must
      always be written before the implementation.

  SCOPE: SAP BTP — Python MCP Server exposing SAP S/4HANA data and actions as
         Model Context Protocol tools and resources for AI assistant consumption.
  STACK: Python 3.11+ | MCP SDK ^1.x | Pydantic v2 | SAP Cloud SDK | Docker
  DEPLOYMENT: SAP BTP Kyma (Kubernetes) or Cloud Foundry
  LAST UPDATED: 2026-03-26
-->

# SAP BTP Project: Python MCP Server (Docker-based)

## 🤖 AI Behavior Rules (CRITICAL)
- **Tool Atomicity:** AI must keep MCP tools atomic. One tool = One specific action. Never combine unrelated actions into one tool.
- **Docstrings First:** AI must write the complete tool docstring before writing the implementation — the docstring is the LLM's API contract.

## 📌 Dependency Versions (AI must target these)
- Python: 3.11+
- `mcp`: ^1.x (FastMCP or low-level API)
- `pydantic`: ^2.x
- `sap-cloud-sdk-python`: latest
- `sap-xssec`: latest
- `cfenv`: ^0.x (Cloud Foundry env parsing)
- `hdbcli`: latest (HANA connectivity)
- `tenacity`: ^8.x (retry logic)
- `pybreaker`: ^1.x (circuit breaker)
- `pytest`: ^7.x / `pytest-asyncio`: ^0.x

## 🛠 Deployment & Workflow
- **Docker:** Use `python:3.11-slim` as the base image. Avoid `alpine` — C-extension issues with SAP drivers (`hdbcli`).
- **Multistage Build:** Use a builder stage for `pip install` and a clean runtime stage to minimize image size.
- **Commands:**
  - `mcp dev main.py` (Local debug with Inspector)
  - `docker build -t mcp-server-sap:<git-sha> .`
  - `cf push` or `kubectl apply -f k8s/`
  - `pytest --cov=. --cov-report=term-missing`
  - `buf lint` (if proto files are used for gRPC tools)

## 📊 Observability
- **Structured Logging:** JSON logs with `timestamp`, `level`, `tool_name`, `correlationId`, `user`, `duration_ms`.
- **Tool Metrics:** Log tool invocation count, success/failure rate, and duration for each tool.

@rules/coding-style.md
@rules/testing.md
@rules/security.md

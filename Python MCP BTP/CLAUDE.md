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
- **Delta Only:** For existing files, provide ONLY the changed functions/classes — never rewrite the entire file unless explicitly asked.
- **No Hallucinated APIs:** If unsure whether a SAP SDK or MCP SDK method exists, flag it and ask — never invent plausible-sounding calls.
- **Tool Atomicity:** AI must keep MCP tools atomic. One tool = One specific action. Never combine unrelated actions into one tool.
- **No Hardcoded Values:** Never hardcode tenant IDs, service URLs, credentials, or environment-specific config.
- **No New Dependencies:** Do not introduce a new Python package without flagging it and confirming with the user first.
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

## 📐 Naming Conventions
- **Files:** snake_case (`order_tools.py`, `s4_connector.py`)
- **Classes:** PascalCase (`OrderService`, `S4HanaConnector`)
- **Tool functions:** snake_case, verb-first (`get_order_details`, `create_purchase_requisition`)
- **Resource URIs:** kebab-case path segments (`sap://orders/{id}`, `sap://employees/active`)
- **Pydantic models:** PascalCase (`OrderResponse`, `PurchaseRequisitionInput`)
- **Constants:** UPPER_SNAKE_CASE (`DEFAULT_TIMEOUT_SECONDS`, `MAX_PAGE_SIZE`)

## 🏗 Architecture & MCP Standards
- **Framework:** Python 3.11+ using the `mcp` SDK (FastMCP or Low-level API).
- **Protocol:** Model Context Protocol (MCP) over Stdio (for local) or SSE (for BTP/HTTP).
- **Deployment:** Dockerized container running on **SAP BTP Kyma** or **Cloud Foundry**.
- **MVC Pattern for MCP:**
  - **Model:** Python Data Classes / Pydantic models and SAP Cloud SDK for data fetching.
  - **Controller (Tools):** High-level functions decorated with `@mcp.tool()`.
  - **View (Resources):** Structured URI-based data provided via `@mcp.resource()`.

## 🤖 Tool & Resource Development (CRITICAL)
- **Tool Granularity:** One tool = One specific action (e.g., `get_user_details` vs. `manage_users`).
- **Docstring Protocol:** Provide exhaustive Python docstrings for every tool — MCP uses these to explain the tool to the LLM. Include: purpose, all params, return shape, and example usage.
- **Type Hinting:** Use Pydantic v2 models or Python type hints strictly (`str`, `int`, `List[str]`). MCP uses these to generate the JSON Schema for tool calls. Never use `Any`.
- **Error Propagation:** Raise `mcp.shared.exceptions.McpError` for protocol-level errors. Never let a raw Python exception crash the MCP server.
- **Versioning:** Never remove a tool — mark it `@deprecated` in the docstring. When a tool's signature changes, create a new versioned tool (`get_orders_v2`) and keep the old one for one release cycle.

## 🧠 LLM Context Management (CRITICAL)
- **Response Size Limit:** Every tool must limit its response to < 4,000 tokens. Use pagination params (`limit`, `offset`) for large datasets.
- **Summarize, Don't Dump:** Tools must return structured summaries, not raw SAP API payloads. Filter to only the fields the LLM needs.
- **Streaming:** Use `async` generators (`yield`) for tools that return large lists or long-running results.
- **Pagination Required:** AI must never design a tool that returns an entire SAP table — always require a filter, date range, or page size parameter.
- **Field Selection:** Tools should accept an optional `fields: List[str]` param to let the caller request only needed attributes.

## 🔐 SAP BTP Security & Connectivity
- **Authentication:** Use `sap-xssec` for JWT validation if the MCP server is exposed via HTTP/SSE.
- **Cloud SDK:** Use `sap-cloud-sdk-python` for Destination lookups and S/4HANA OData consumption.
- **Environment:** Use `cfenv` (for CF) or Kubernetes secrets (for Kyma) to manage `VCAP_SERVICES` — never read raw env vars directly.
- **Principal Propagation:** Always forward the user's identity token when calling back-end SAP systems — never use a technical user for user-facing tools.

## 🛡 Resilience
- All SAP OData / HTTP calls must have an explicit timeout (default: 10 seconds).
- Use `tenacity` for retry logic with exponential backoff on `429` and `503` responses:
  ```python
  @retry(wait=wait_exponential(min=1, max=10), stop=stop_after_attempt(3), retry=retry_if_exception_type(TransientError))
  ```
- Use `pybreaker` for Circuit Breaker pattern on all external S/4HANA calls.
- Every tool must handle the circuit-open state gracefully and return a clear error message to the LLM.

## ⚠️ Error Handling
- Raise `mcp.shared.exceptions.McpError` for protocol-level errors — never raw exceptions.
- Use custom exception classes (`S4HanaError`, `AuthorizationError`) that map to `McpError` in a central handler.
- Log all errors with full context before raising — the MCP server should never silently swallow exceptions.
- Return user-friendly error messages in tool responses — the LLM will relay these to the end user.

## 🧪 Testing & Quality
- **Unit Tests:** Use `pytest` and `pytest-asyncio` for all async tool functions.
- **MCP Inspector:** Use the `mcp-inspector` tool to debug tool definitions locally before deploying to BTP.
- **Mocking:** Use `responses` or `pytest-mock` to simulate SAP OData responses — never call real SAP systems in tests.
- **Coverage:** Minimum 80% line coverage on all tool and service modules.
- **Docstring Validation:** Test that every tool has a non-empty description and all params are documented.

## 💻 Service Standards
- **Async First:** Use `asyncio` and `async/await` for all I/O bound operations (API calls to SAP).
- **Logging:** Use standard Python `logging` with JSON formatter for BTP Application Logging service compatibility.
  ```python
  import logging, json
  logger = logging.getLogger(__name__)
  # Output: {"timestamp": "...", "level": "INFO", "tool": "get_orders", "correlationId": "..."}
  ```
- **Correlation ID:** Extract and propagate `x-correlationid` from incoming context through all SAP calls.
- **Persistence:** If state is needed, use **SAP HANA Cloud** via `hdbcli` or `sqlalchemy` with the `hana` dialect.

## 📊 Observability
- **Structured Logging:** JSON logs with `timestamp`, `level`, `tool_name`, `correlationId`, `user`, `duration_ms`.
- **Tool Metrics:** Log tool invocation count, success/failure rate, and duration for each tool.
- **Never** use `print()` in production code — always use the `logging` module.

## 🛠 Deployment & Workflow
- **Docker:** Use `python:3.11-slim` as the base image. Avoid `alpine` — C-extension issues with SAP drivers (`hdbcli`).
- **Multistage Build:** Use a builder stage for `pip install` and a clean runtime stage to minimize image size.
- **Commands:**
  - `mcp dev main.py` (Local debug with Inspector)
  - `docker build -t mcp-server-sap:<git-sha> .`
  - `cf push` or `kubectl apply -f k8s/`
  - `pytest --cov=. --cov-report=term-missing`
  - `buf lint` (if proto files are used for gRPC tools)

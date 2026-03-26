# Coding Style — SAP BTP Python MCP Server

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

## 💻 Service Standards
- **Async First:** Use `asyncio` and `async/await` for all I/O bound operations (API calls to SAP).
- **Logging:** Use standard Python `logging` with JSON formatter for BTP Application Logging service compatibility.
  ```python
  import logging
  logger = logging.getLogger(__name__)
  # Output: {"timestamp": "...", "level": "INFO", "tool": "get_orders", "correlationId": "..."}
  ```
- **Correlation ID:** Extract `x-correlationid` from incoming context and propagate through all SAP calls.
- **Persistence:** If state is needed, use **SAP HANA Cloud** via `hdbcli` or `sqlalchemy` with the `hana` dialect.

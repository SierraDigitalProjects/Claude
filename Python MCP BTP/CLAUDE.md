# SAP BTP Project: Python MCP Server (Docker-based)

## 🏗 Architecture & MCP Standards
- **Framework:** Python 3.11+ using the `mcp` SDK (FastMCP or Low-level API).
- **Protocol:** Model Context Protocol (MCP) over Stdio (for local) or SSE (for BTP/HTTP).
- **Deployment:** Dockerized container running on **SAP BTP Kyma** or **Cloud Foundry**.
- **MVC Pattern for MCP:**
  - **Model:** Python Data Classes and SAP Cloud SDK for data fetching.
  - **Controller (Tools):** High-level functions decorated with `@mcp.tool()`.
  - **View (Resources):** Structured URI-based data provided via `@mcp.resource()`.

## 🤖 AI Efficiency & Tool Development (CRITICAL)
- **Tool Granularity:** AI must keep MCP tools atomic. One tool = One specific action (e.g., `get_user_details` vs. `manage_users`).
- **Docstring Protocol:** AI must provide exhaustive Python docstrings for every tool. MCP uses these docstrings to explain the tool's purpose to the LLM.
- **Type Hinting:** Use Pydantic or Python type hints (`str`, `int`, `List`) strictly. The AI uses these to generate the JSON Schema for tool calls.
- **Error Propagation:** Use `mcp.types.McError` for protocol-level errors. AI should never let a raw Python exception crash the server.

## 🔐 SAP BTP Security & Connectivity
- **Authentication:** Use `sap-xssec` for JWT validation if exposed via HTTP/SSE.
- **Cloud SDK:** Use `sap-cloud-sdk-python` for Destination lookups and S/4HANA OData consumption.
- **Environment:** Use `cfenv` (for CF) or Kubernetes secrets (for Kyma) to manage VCAP_SERVICES.
- **Principal Propagation:** Ensure the MCP server forwards the user's identity when calling back-end SAP systems.

## 🧪 Testing & Quality
- **Unit Tests:** Use `pytest` and `pytest-asyncio`.
- **MCP Inspector:** Use the `mcp-inspector` tool to debug tool definitions locally before deploying to BTP.
- **Mocking:** Use `responses` or `pytest-mock` to simulate SAP OData responses.

## 💻 Service Standards
- **Async First:** Use `asyncio` for all I/O bound operations (API calls to SAP).
- **Logging:** Use standard Python `logging` with JSON formatter for BTP Application Logging service compatibility.
- **Persistence:** If state is needed, use **SAP HANA Cloud** via `hdbcli` or `sqlalchemy`.

## 🛠 Deployment & Workflow
- **Docker:** Use `python:3.11-slim` as the base image. Avoid `alpine` due to C-extension issues with SAP drivers.
- **Commands:**
  - `mcp dev main.py` (Local debug with Inspector).
  - `docker build -t mcp-server-sap .`
  - `cf push` or `kubectl apply -f k8s/`
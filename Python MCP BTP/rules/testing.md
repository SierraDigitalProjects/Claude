# Testing — SAP BTP Python MCP Server

## 🧪 Testing & Quality
- **Unit Tests:** Use `pytest` and `pytest-asyncio` for all async tool functions.
- **MCP Inspector:** Use the `mcp-inspector` tool to debug tool definitions locally before deploying to BTP.
- **Mocking:** Use `responses` or `pytest-mock` to simulate SAP OData responses — never call real SAP systems in tests.
- **Docstring Validation:** Test that every tool has a non-empty description and all params are documented.

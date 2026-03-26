# Security — SAP BTP Python MCP Server

## 🔐 SAP BTP Security & Connectivity
- **Authentication:** Use `sap-xssec` for JWT validation if the MCP server is exposed via HTTP/SSE.
- **Cloud SDK:** Use `sap-cloud-sdk-python` for Destination lookups and S/4HANA OData consumption.
- **Environment:** Use `cfenv` (for CF) or Kubernetes secrets (for Kyma) to manage `VCAP_SERVICES` — never read raw env vars directly.
- **Principal Propagation:** Always forward the user's identity token when calling back-end SAP systems — never use a technical user for user-facing tools.

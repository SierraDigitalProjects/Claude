# Testing — SAP BTP CAP & Fiori

## 🧪 Testing
- Use `@sap/cds/lib/test` utilities for unit testing CAP service handlers.
- Use `cds.test()` to spin up an in-process CAP server for integration tests — no real HANA needed.
- Mock external destinations with `cds.env.requires.<service>.credentials` overrides in test config.
- Every handler must have a test covering: success (2xx), authorization failure (403), and validation error (400).
- Use `db/data/*.csv` fixtures for consistent test data — never hardcode test data inline.

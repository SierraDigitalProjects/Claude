# Security — SAP BTP CAP & Fiori

## 🔐 Security & Compliance
- **Instance Security:** Annotate all services with `@requires: 'authenticated-user'`.
- **Role-Based Access (RBAC):** Use `@restrict` on entities to map scopes to CRUD actions:
  ```cds
  annotate MyService.Orders with @(restrict: [
    { grant: ['READ'], to: 'Viewer', where: 'buyer = $user' },
    { grant: ['*'], to: 'Admin' }
  ]);
  ```
- **Audit Logging:** Use `@sap/audit-logging` for all data access on sensitive entities (PII, financial).
- **Input Validation:** Validate all user-supplied filter and query params in `before` handlers.
- **Never expose draft-enabled entities** without explicit RBAC — drafts bypass some OData filters.

# Security — SAP BTP React & Node.js

## 🔐 Security & RBAC (XSUAA)
- **Authentication:** All requests must pass through the **SAP Approuter**.
- **JWT Validation:** Use `@sap/xssec` in Node.js to validate the incoming JWT from Approuter.
- **RBAC Enforcement:**
  - Create middleware in Node.js to check scopes: `req.authInfo.checkLocalScope('Admin')`.
  - In React, use a `ProtectedRoute` component to hide UI elements based on JWT scopes.
- **CORS:** Ensure BTP Approuter handles CORS; do not hardcode allowed origins in Node.js.
- **Input Sanitization:** Validate and sanitize all incoming request bodies using Zod schemas in the Controller layer.

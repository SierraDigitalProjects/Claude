# Coding Style — SAP BTP CAP & Fiori

## 📐 Naming Conventions
- **CDS Entities:** PascalCase singular (`Order`, `Employee`, `PurchaseRequisition`)
- **CDS Services:** PascalCase with `Service` suffix (`AdminService`, `ProcurementService`)
- **Handler Files:** Match service name in kebab-case (`admin-service.js`, `procurement-service.js`)
- **CDS Namespaces:** Reverse-domain format (`com.company.module`)
- **Action/Function names:** camelCase (`submitOrder`, `cancelRequest`)
- **i18n keys:** `snake_case` (`order_status`, `employee_name`)

## 🏗 Modular Architecture Rules
- **Layered Separation (SoC):**
  - `db/`: Core domain models & persistence logic (HANA-optimized). No UI or Service logic.
  - `srv/`: Persona-based services (e.g., `AdminService`, `ProcurementService`, `EmployeeService`).
  - `app/`: Fiori Elements/UI5 modules. One subfolder per standalone application.
- **Cross-Module Usage:** Use `using { ... } from './common'` to share types/aspects across modules.
- **Service Partitioning:** Break large services into smaller, functional OData services to optimize metadata loading, performance, and security.

## 💻 Service & Logic Standards
- **Thin Services:** `.cds` files should only contain projections and minimal annotations.
- **Handler Structure:** Place logic in `srv/*.js` or `srv/*.ts` using `async/await`.
  - Use **Service-to-Service calls** (`cds.connect.to`) for cross-service logic.
- **External APIs:** Always import S/4HANA or 3rd party metadata via `cds import`. Store in `srv/external/`.
- **HANA-Specifics:** Use `Decimal(15,2)` for currency. No raw SQL; use **CQL** (CDS Query Language).

## ⚠️ Error Handling
- Throw `new cds.error(message, { status: 400 })` — never throw raw JS errors in handlers.
- Use `req.reject()` for business validation failures, not exceptions.
- Use `req.error()` to accumulate multiple validation messages before returning.
- Place global error logging in `srv/lib/error-handler.js` — not inline per-handler.
- Never expose internal error details or stack traces to the OData response.

## 📨 Events & Messaging
- Use `cds.emit()` for domain events within the same service.
- For cross-service async events, use SAP Event Mesh via `messaging` config in `package.json`.
- Event payloads must match CloudEvents spec format.
- Never emit events inside DB transactions unless explicitly required and confirmed by the user.
- Subscribe to events in `srv/` handlers using `messaging.on('topic', handler)` pattern.

## 🎨 UI & Annotation Modularity
- **Decoupled UI:**
  - `db/`: Zero UI annotations.
  - `srv/`: Minimal UI annotations (only those required for basic OData display).
  - `app/<module>/annotations.cds`: ALL Fiori Elements layout/UI logic goes here.
- **Shared UI:** Use `app/common.cds` for global Value Helps and common Labels.
- **I18n:** Use separate `_i18n` folders in `db`, `srv`, and `app` for granular translations.

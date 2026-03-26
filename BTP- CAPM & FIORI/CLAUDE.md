# SAP BTP Enterprise Project: Modular CAP, Fiori, RBAC & Quality Standards

## 🏗 Modular Architecture Rules
- **Layered Separation (SoC):**
  - `db/`: Core domain models & persistence logic (HANA-optimized). No UI or Service logic.
  - `srv/`: Persona-based services (e.g., `AdminService`, `ProcurementService`, `EmployeeService`).
  - `app/`: Fiori Elements/UI5 modules. One subfolder per standalone application.
- **Cross-Module Usage:** Use `using { ... } from './common'` to share types/aspects across modules.
- **Service Partitioning:** Break large services into smaller, functional OData services to optimize metadata loading, performance, and security.

## 🛠 Commands & Workflow
- **Start Dev:** `cds watch`
- **Build MTA:** `mbt build`
- **Deploy BTP:** `cf deploy mta_archives/*.mtar`
- **Modular Mocking:** Use `db/data/` for shared mocks; `srv/external/` for API mocks.
- **Linting:** `cds lint` (Enforce SAP-standard model quality) and `eslint .`.

## 💻 Service & Logic Standards
- **Thin Services:** `.cds` files should only contain projections and minimal annotations.
- **Handler Structure:** - Place logic in `srv/*.js` or `srv/*.ts` using `async/await`.
  - Use **Service-to-Service calls** (`cds.connect.to`) for cross-service logic.
- **External APIs:** Always import S/4HANA or 3rd party metadata via `cds import`. Store in `srv/external/`.
- **HANA-Specifics:** Use `Decimal(15,2)` for currency. No raw SQL; use **CQL** (CDS Query Language).

## 🎨 UI & Annotation Modularity
- **Decoupled UI:** - `db/`: Zero UI annotations.
  - `srv/`: Minimal UI annotations (only those required for basic OData display).
  - `app/<module>/annotations.cds`: ALL Fiori Elements layout/UI logic goes here.
- **Shared UI:** Use `app/common.cds` for global Value Helps and common Labels.
- **I18n:** Use separate `_i18n` folders in `db`, `srv`, and `app` for granular translations.

## 🔐 Security & Compliance
- **Instance Security:** Annotate all services with `@requires: 'authenticated-user'`.
- **Role-Based Access (RBAC):** Use `@restrict` on entities to map scopes to CRUD actions:
  ```cds
  annotate MyService.Orders with @(restrict: [
    { grant: ['READ'], to: 'Viewer', where: 'buyer = $user' },
    { grant: ['*'], to: 'Admin' }
  ]);
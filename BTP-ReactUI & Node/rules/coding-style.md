# Coding Style — SAP BTP React & Node.js

## 📐 Naming Conventions
- **Files:** kebab-case (`order-service.ts`, `purchase-order-list.tsx`)
- **Classes / React Components:** PascalCase (`OrderService`, `PurchaseOrderList`)
- **Interfaces / Types:** PascalCase with `I` prefix for interfaces (`IOrderResponse`), plain PascalCase for types (`OrderStatus`)
- **REST Routes:** kebab-case (`/api/v1/purchase-orders`)
- **Node.js services:** camelCase methods (`getOrderById`, `createPurchaseRequisition`)
- **React hooks:** `use` prefix camelCase (`useOrderData`, `useAuthInfo`)

## 🏗 Modular Architecture Rules
- **Layered Separation:**
  - `client/`: React.js application (Vite). No backend logic.
  - `server/`: Node.js API (Express). Handles business logic & S/4HANA connectivity.
  - `router/`: SAP Approuter (Essential for XSUAA authentication and routing).
- **API Design:** Follow the **Controller-Service-Repository** pattern in Node.js.
- **Shared Contracts:** TypeScript interfaces in `shared/types/` to sync data shapes between Client and Server.

## 🔗 API Design Standards
- Version all APIs: `/api/v1/orders` — never break existing route contracts without a version bump.
- Use HTTP status codes correctly: `200`/`201`/`204`/`400`/`401`/`403`/`404`/`422`/`500`.
- API errors must follow **RFC 7807 Problem Details** format:
  ```json
  { "type": "...", "title": "...", "status": 400, "detail": "...", "instance": "..." }
  ```
- All list endpoints must support pagination (`?limit=&offset=` or cursor-based).

## 📐 TypeScript Rules
- `strict: true` in all `tsconfig.json` files — no exceptions.
- All API response shapes must have a shared Interface in `shared/types/`.
- Never use `any` — use `unknown` with type guards, or proper generics.
- Use **Zod** for runtime validation of all external API responses (S/4HANA, 3rd party).
- AI must generate types for all new API contracts before writing implementation code.

## ⚠️ Error Handling
- Use a **global error-handling middleware** in Express (`server/middleware/error-handler.ts`) — no duplicated `try-catch` in every controller.
- Controllers catch errors and pass them to `next(err)` — never handle errors inline.
- Map known error types (XSUAA 401, S/4HANA 404) to appropriate HTTP status codes in the global handler.
- React: Use an `ErrorBoundary` component at the route level to catch render errors.

## 🗂 State Management Rules
- **Server state** (API data, loading, caching): React Query (TanStack) ONLY — no Redux for API data.
- **Global UI state** (auth info, theme, notifications): Zustand.
- **Local component state**: `useState` / `useReducer`.
- AI must not introduce a new state management library — use only what is listed above.
- Never store sensitive JWT data in `localStorage` — use in-memory state only.

## 🎨 React UI & CSS Standards

### Component Library
- Use **SAP UI5 Web Components for React** (`@ui5/webcomponents-react`) for all UI elements — never reimplement buttons, inputs, dialogs, tables, or modals from scratch.
- Prefer Fiori-native components: `Button`, `Table`, `Panel`, `Dialog`, `Input`, `Select`, `FlexBox`, `Title`, `Text`.
- Do not mix UI5 components with third-party component libraries (e.g., MUI, Ant Design) — flag any such request.

### CSS Authoring Rules
- **CSS Modules only** — one `.module.css` file per component, colocated in the same directory.
- **No inline styles** — `style={{}}` is forbidden except for truly dynamic computed values (e.g., chart widths). Flag any other use.
- **No global stylesheets** for component styles — only `src/styles/global.css` may contain resets and body-level defaults.
- **No `!important`** — flag any use and confirm with the user before adding.
- **No hardcoded values** — never write raw hex colors, pixel font sizes, or magic spacing numbers. Always use SAP CSS variables.

### SAP Fiori Design Tokens (use these, never raw values)
- **Colors:** Use SAP semantic variables — `var(--sapContent_LabelColor)`, `var(--sapButton_Background)`, `var(--sapErrorColor)`, `var(--sapSuccessColor)`, `var(--sapNeutralColor)`.
- **Typography:** `var(--sapFontFamily)`, `var(--sapFontSize)`, `var(--sapFontLargeSize)`, `var(--sapFontSmallSize)`, `var(--sapFontBoldSize)`.
- **Spacing:** Use `var(--sapElement_Height)`, `var(--sapContent_ElementHeight)`, or consistent multiples of `0.25rem` (4px grid).
- **Elevation / Shadows:** Use `var(--sapContent_Shadow0)` through `var(--sapContent_Shadow3)` — never write raw `box-shadow` values.
- **Border radius:** Use `var(--sapButton_BorderCornerRadius)` or `var(--sapField_BorderCornerRadius)` — never hardcode `px` values.

### Naming Within CSS Modules
- Use **kebab-case** for class names: `.order-header`, `.status-badge--error`.
- Follow BEM-style modifier pattern within modules: `.card`, `.card__title`, `.card__title--highlighted`.
- Class names must describe **purpose**, not appearance: `.primary-action` not `.blue-button`.

### Responsive Design
- **Mobile-first:** Write base styles for small screens, use `@media (min-width: ...)` for larger breakpoints.
- Use UI5's `FlexBox` and `Grid` components for layout before reaching for custom CSS flex/grid.
- Never hardcode pixel widths for layout containers — use `%`, `rem`, or UI5 layout components.

### Overriding UI5 Components
- Use `className` prop to add a CSS Module class alongside the UI5 component — never target UI5 internal class names directly.
- `::part()` selectors may be used only for documented UI5 shadow DOM parts — flag any use and confirm.
- Do not use `!important` to override UI5 default styles.

### Accessibility
- All interactive components must have `aria-label` or `aria-labelledby`.
- UI5 components handle most ARIA natively — only add explicit ARIA when building custom interactive patterns.
- Never remove focus outlines — if the default is visually unacceptable, replace with a visible custom focus style.

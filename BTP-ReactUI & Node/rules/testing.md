# Testing — SAP BTP React & Node.js

## 🧪 Testing & Quality
- **Backend (Unit/Integration):** Use `Jest` and `Supertest`. Every API endpoint must have a test for Success (2xx) and Unauthorized (401/403).
- **Frontend (UI Testing):** Use `Vitest` + `React Testing Library` for component unit tests.
- **E2E Testing:** Use `Playwright` to test the full flow from Approuter → React → Node.js.
- **Mocking:** Use `msw` (Mock Service Worker) for frontend testing to simulate Node.js API responses.

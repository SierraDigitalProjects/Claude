# Claude Code — AI Instruction Templates

A set of ready-to-use `CLAUDE.md` instruction files for Claude Code across different SAP BTP and cloud-native project types. Each template encodes coding style, testing, and security standards so you don't have to repeat them in every prompt.

## Available Templates

| Folder | Stack |
|--------|-------|
| `BTP- CAPM & FIORI/` | SAP CAP (CDS) + Fiori Elements + HANA Cloud |
| `BTP-ReactUI & Node/` | React 18 + Node.js (Express) + SAP Approuter + XSUAA |
| `AKS-ReactUI-Node/` | React 18 + Node.js Microservices + AKS + gRPC + Prisma |
| `Python MCP BTP/` | Python MCP Server + SAP S/4HANA + BTP Kyma/CF |

Global rules that apply to all projects live in the root `CLAUDE.md`.

---

## Using a Template in a New Project

### Step 1 — Copy the template into your project root

Pick the template that matches your stack and copy its contents into your project:

```bash
# Example: AKS React/Node project
cp -r "AKS-ReactUI-Node/." /path/to/your-new-project/
```

Your project will now have:

```
your-new-project/
├── CLAUDE.md
└── rules/
    ├── coding-style.md
    ├── testing.md
    └── security.md
```

### Step 2 — Open the project in Claude Code

```bash
cd /path/to/your-new-project
claude
```

Claude Code automatically reads `CLAUDE.md` at session start. The `@rules/` imports are resolved automatically — no extra configuration needed.

### Step 3 — Customize before you start coding

**In `CLAUDE.md`:**
- Update `LAST UPDATED` to today's date
- Adjust dependency versions to match your `package.json` / `requirements.txt`
- Update project-specific commands (registry names, cluster names, deploy targets)

**In `rules/coding-style.md`:**
- Set the correct path for your design token file (default: `src/styles/tokens.css`)
- Set the correct path for your breakpoints file (default: `src/styles/breakpoints.css`)
- Add any design tokens you already have defined

**In `rules/security.md`:**
- Set your Key Vault / XSUAA scope names specific to this project

---

## How It Works

Claude Code loads `CLAUDE.md` automatically before every task. Because the rules are already loaded, you write shorter, cleaner prompts:

**Without templates:**
> "Add a service method to fetch purchase orders — use TypeScript strict mode, follow BEM for CSS, don't hardcode URLs, use the global error handler, write a Jest test with 80% coverage..."

**With templates:**
> "Add a service method to fetch purchase orders by date range"

Claude already knows all the rules.

---

## Referencing a Specific Rules File

Point Claude at a single rules file when working in a specific area:

```
Following @rules/security.md, add RBAC middleware to the orders controller
```

```
Following @rules/coding-style.md, review this component for CSS violations
```

---

## Adding a New Project Type

1. Copy the closest existing template as a base
2. Update the `SCOPE` and `STACK` lines in the `CLAUDE.md` header comment
3. Replace stack-specific sections in `rules/coding-style.md` (e.g., swap Prisma rules for TypeORM)
4. Update `rules/security.md` with the relevant auth/secret management approach
5. The root `CLAUDE.md` global rules (Delta Only, No Hallucinated APIs, 80% coverage, etc.) apply automatically — no changes needed there

---

## File Structure Reference

```
workspace/Claude/
├── README.md                          ← this file
├── CLAUDE.md                          ← global rules (all projects inherit these)
│
├── BTP- CAPM & FIORI/
│   ├── CLAUDE.md                      ← behavior rules, deps, commands, observability
│   └── rules/
│       ├── coding-style.md            ← naming, CAP architecture, service standards, events, UI annotations
│       ├── testing.md                 ← CAP test utilities, fixtures
│       └── security.md               ← @restrict RBAC, audit logging, draft entity rules
│
├── BTP-ReactUI & Node/
│   ├── CLAUDE.md
│   └── rules/
│       ├── coding-style.md            ← naming, API design, TypeScript, state management, UI5/CSS
│       ├── testing.md                 ← Jest, Playwright, msw, Vitest
│       └── security.md               ← XSUAA, Approuter, ProtectedRoute, Zod sanitization
│
├── AKS-ReactUI-Node/
│   ├── CLAUDE.md
│   └── rules/
│       ├── coding-style.md            ← naming, MVC, TypeScript, gRPC, Prisma, CI/CD, CSS tokens
│       ├── testing.md                 ← Pact, grpc-mock, health endpoints
│       └── security.md               ← XSUAA, Azure Key Vault, NetworkPolicy, Trivy
│
└── Python MCP BTP/
    ├── CLAUDE.md
    └── rules/
        ├── coding-style.md            ← naming, MCP architecture, tool/resource dev, LLM context, resilience
        ├── testing.md                 ← pytest-asyncio, mcp-inspector, docstring validation
        └── security.md               ← sap-xssec, Cloud SDK destinations, principal propagation
```

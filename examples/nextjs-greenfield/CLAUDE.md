# CLAUDE.md — Acme Dashboard

> Internal analytics dashboard for Acme Corp
> Started: 2026-04-01

---

## Profile

```yaml
active_profile: greenfield
```

---

## 1. Principles

1. **YAGNI** — Build only what is needed now. No speculative abstractions.
2. **Interface first** — Define API contracts before implementation.
3. **TDD mandatory** — No production code without preceding tests.
4. **No `any`** — TypeScript strict mode everywhere.
5. **Conventional Commits** — `feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`.
6. **Max 300 lines per file, max 20 lines per function**.
7. **Accessibility** — WCAG 2.1 AA minimum on all UI components.
8. **Update this file after every feature**.

---

## 2. Stack

| Layer | Technology | License |
|-------|-----------|---------|
| **Framework** | Next.js 15 (App Router) | MIT |
| **Language** | TypeScript 5.5 | Apache-2.0 |
| **Database** | PostgreSQL 16 | -- |
| **ORM** | Prisma 6 | Apache-2.0 |
| **Auth** | NextAuth.js 5 | ISC |
| **Validation** | Zod | MIT |
| **Styling** | Tailwind CSS 4 + shadcn/ui | MIT |
| **State** | Zustand (client) + TanStack Query v5 (server) | MIT |
| **Test** | Vitest + Testing Library + Playwright | MIT |
| **Logging** | Pino | MIT |
| **Deploy** | Vercel | -- |

---

## 3. Architecture

```
src/
├── app/
│   ├── (auth)/           # Auth routes (login, register)
│   ├── (dashboard)/      # Protected dashboard routes
│   │   ├── analytics/    # Analytics pages
│   │   ├── reports/      # Report pages
│   │   └── settings/     # Settings pages
│   ├── api/              # API routes
│   └── layout.tsx        # Root layout
├── components/
│   ├── ui/               # shadcn/ui components
│   ├── charts/           # Chart components (Recharts)
│   ├── data-table/       # DataTable with sorting/filtering
│   └── forms/            # Form components
├── lib/
│   ├── db/               # Prisma client + helpers
│   ├── auth/             # NextAuth config
│   ├── validations/      # Zod schemas
│   └── utils/            # Shared utilities
├── hooks/                # Custom React hooks
├── stores/               # Zustand stores
└── types/                # Shared TypeScript types
```

### Patterns

- **Data fetching**: Server Components with Prisma, Client Components with TanStack Query
- **Validation**: Zod schemas shared between client forms and API routes
- **Auth**: NextAuth.js with middleware-based route protection
- **Error handling**: Error boundaries per route segment
- **API pattern**: Next.js API routes with Zod validation middleware

---

## 4. Development Process

11 steps active (greenfield profile, no parity test):

| Step | Active | Notes |
|------|--------|-------|
| 1. Analysis | YES | Codebase scan, understand existing patterns |
| 2. Functional doc | YES | docs/functional/ |
| 3. Workflow doc | YES | docs/workflows/ |
| 4. Plan | YES | Approval gate |
| 5. Unit test | YES | TDD, Vitest |
| 6. Develop | YES | Server Components, API routes, Prisma |
| 7. API docs | YES | OpenAPI via next-swagger |
| 8. Integration test | YES | Vitest + Testcontainers |
| 9. Parity test | -- | N/A (greenfield) |
| 10. E2E | YES | Playwright |
| 11. Manuals | YES | Dev + user docs |
| 12. Security scan | YES | Full checklist |

---

## 5. Rules for Claude

### When building

- YAGNI — do not add features not in the current scope
- Server Components by default, Client Components only when needed (interactivity, hooks)
- All data mutations through Server Actions or API routes
- Form validation: Zod schema shared between client and server
- Every page must have a loading.tsx and error.tsx

---

## 6. Development Index

### Feature Progress (11-step, no parity)

| Feature | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 10 | 11 | 12 | Complete? |
|---------|---|---|---|---|---|---|---|---|----|----|-----|-----------|
| auth | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y | YES |
| analytics-dashboard | Y | Y | Y | Y | Y | Y | - | - | - | - | - | NO |
| reports | Y | Y | - | - | - | - | - | - | - | - | - | NO |
| settings | - | - | - | - | - | - | - | - | - | - | - | NO |

### Test Counts

| Type | Count |
|------|-------|
| Unit test | 45 |
| Integration test | 18 |
| E2E test | 12 |
| **Total** | **75** |

### Security Findings

| # | Severity | Module | Status | Description | Fix |
|---|----------|--------|--------|-------------|-----|
| 1 | MEDIUM | api | FIXED | Missing rate limiting on auth endpoints | Added rate limiter middleware |

---

## 7. Decisions

| # | Decision | Answer |
|---|----------|--------|
| 1 | Rendering | Server Components by default, Client when needed |
| 2 | Auth | NextAuth.js v5 with database sessions |
| 3 | Data fetching | Prisma in Server Components, TanStack Query in Client |
| 4 | Styling | Tailwind CSS 4 + shadcn/ui (no custom CSS) |
| 5 | Deployment | Vercel with preview deployments per PR |

---

## 8. Open Questions

- [ ] Chart library: Recharts vs Nivo for complex visualizations
- [ ] Export format: PDF vs Excel for report downloads

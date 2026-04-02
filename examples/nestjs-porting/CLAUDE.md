# CLAUDE.md — Inventory API Rework

> Inventory management API — porting from Spring Boot / Java to NestJS
> Started: 2026-01-15

---

## Profile

```yaml
active_profile: porting
```

---

## 1. Principles

1. **API Parity** — Endpoint-by-endpoint parity with the existing Spring Boot service. Same request/response contracts, same behaviour.
2. **DB parity is king** — The NestJS endpoint MUST produce the EXACT SAME writes on DB as the legacy. Same tables, same columns, same values.
3. **TDD mandatory** — No production code without preceding tests.
4. **No `any`** — TypeScript strict mode everywhere.
5. **Swagger mandatory** — Every ported endpoint MUST have complete OpenAPI decorators.
6. **Conventional Commits** — `feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`.
7. **Max 300 lines per file, max 20 lines per function**.
8. **No skip, no magic, no silent side effects** — If input is invalid, throw with a clear message.
9. **Update this file after every feature**.

---

## 2. Stack

| Layer          | Technology                          | License    |
| -------------- | ----------------------------------- | ---------- |
| **Backend**    | NestJS 11, Node 22, TypeORM 0.3     | MIT        |
| **Database**   | PostgreSQL 16 (shared schema)       | --         |
| **Auth**       | JWT via Passport                    | MIT        |
| **Validation** | Zod                                 | MIT        |
| **Swagger**    | @nestjs/swagger                     | MIT        |
| **Test**       | Jest (unit) + Supertest (e2e)       | MIT        |
| **Build**      | Nest CLI                            | MIT        |
| **Logging**    | Pino (via nestjs-pino)              | MIT        |
| **Deploy**     | Docker                              | --         |

---

## 3. Architecture

```
src/
├── config/
├── core/
│   ├── auth/         # JWT strategy, guard, interceptors
│   ├── cache/        # Redis module
│   └── database/     # TypeORM config
├── entities/
│   ├── base/         # base.entity
│   └── models/       # ~40 TypeORM entities
├── modules/
│   ├── product/
│   ├── warehouse/
│   ├── order/
│   └── shipment/
├── shared/           # response formatters, utils, constants
└── main.ts
```

### Patterns

- **ORM**: TypeORM 0.3 — entities use @Entity(), @Column(), @ManyToOne()
- **Validation**: Zod schemas
- **HTTP adapter**: Fastify
- **Auth**: Global JwtGuard via APP_GUARD — all routes protected by default
- **Response format**: Global interceptors for OK and Error formatting
- **Module pattern**: feature modules export services

---

## 4. Development Process

All 12 steps active (porting profile):

| Step | Active | Notes |
|------|--------|-------|
| 1. Analysis | YES | Legacy Java analysis |
| 2. Functional doc | YES | docs/functional/ |
| 3. Workflow doc | YES | docs/workflows/ |
| 4. Plan | YES | Approval gate |
| 5. Unit test | YES | TDD, .service.spec.ts |
| 6. Develop | YES | Entity, DTO, Service, Controller |
| 7. API docs | YES | @nestjs/swagger decorators |
| 8. Integration test | YES | Supertest, test/*.e2e-spec.ts |
| 9. Parity test | YES | Dual-endpoint comparison |
| 10. E2E | YES | Behaviour tests |
| 11. Manuals | YES | Dev + user manuals |
| 12. Security scan | YES | Full checklist |

---

## 5. Rules for Claude

### When porting

- **Only listed endpoints** — Port ONLY endpoints listed in ENDPOINTS.md.
- **Parity first** — match legacy behaviour exactly, improve later.
- **One block at a time** — close and test before moving to next.
- **If the new behaves differently from the old, it is a bug in the new.**
- Follow existing TypeORM entity patterns (match column names/types from DB).
- Java enums to TypeScript enums or string unions.
- JPQL / Criteria API to TypeORM QueryBuilder.

### Transactions

- Use TypeORM `@Transaction()` or `queryRunner` for 2+ write operations.
- Never multi-write without transaction.
- Never raw SQL for writes.

---

## 6. Development Index

### Module Progress (12-step)

| Module    | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | Complete? |
|-----------|---|---|---|---|---|---|---|---|---|----|----|-----|-----------|
| product   | Y | Y | Y | Y | Y | Y | Y | Y | Y | Y  | Y  | Y   | YES       |
| warehouse | Y | Y | Y | Y | Y | Y | - | Y | WIP | - | - | -  | NO        |
| order     | Y | - | - | - | - | - | - | - | - | -  | -  | -   | NO        |
| shipment  | - | - | - | - | - | - | - | - | - | -  | -  | -   | NO        |

### Test Counts

| Type            | Count  |
| --------------- | ------ |
| Unit test       | 142    |
| Integration     | 38     |
| E2E             | 0      |
| Parity          | 12     |
| **Total**       | **192**|

### Security Findings

| #   | Severity | Module | Status | Description                 |
| --- | -------- | ------ | ------ | --------------------------- |
| 1   | HIGH     | shared | TODO   | Remove open-ended DTO types |
| 2   | MEDIUM   | all    | TODO   | Clamp pagination to max 100 |

---

## 7. Decisions

| #   | Decision        | Answer                             |
| --- | --------------- | ---------------------------------- |
| 1   | DB schema       | Same as legacy (shared DB)         |
| 2   | Auth strategy   | JWT via Passport                   |
| 3   | Endpoint naming | Same paths as legacy               |
| 4   | Error format    | Standardized (global interceptors) |
| 5   | ORM             | TypeORM 0.3                        |
| 6   | Validation      | Zod                                |
| 7   | HTTP adapter    | Fastify                            |

---

## 8. Open Questions

- Auth provider integration: evaluation pending
- Deployment strategy: Docker vs PM2

---

## 9. Legacy Reference

The original Java source code is at `legacy/inventory-service/` in this repo.

- Java sources: `legacy/inventory-service/src/main/java/com/example/inventory/`
- ~25 REST controllers across 8 domain packages
- Pattern: `<domain>/controller/<Name>Controller.java` + `<domain>/service/<Name>Service.java`

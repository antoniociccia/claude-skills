---
name: profile-porting
description: >
  Profile for porting an existing codebase from one stack to another.
  All 12 steps active. Legacy analysis, DB parity, API parity mandatory.
  Use when migrating endpoints, services, or modules from a legacy system
  to a new stack while maintaining identical behaviour.
---

# Profile: Porting

## When to Use

- Migrating endpoints from one backend to another (e.g., Java to Node, Python to Go)
- Re-implementing services in a new framework while keeping the same API contract
- Strangler Fig pattern — replacing legacy piece by piece

## Active Steps

All 12 steps are active:

| Step | Active | Scope |
|------|--------|-------|
| 1. Analysis | YES | Legacy analysis: read original controller, service, entity. Extract HTTP method, path, request/response body, validations, error codes, DB queries, business rules. |
| 2. Functional doc | YES | Business rationale, actors, input/output, business rules, integration points, status transitions, configuration. |
| 3. Workflow doc | YES | Step-by-step operational flow, API calls with expected request/response, DB state changes, error scenarios, edge cases. |
| 4. Plan | YES | Implementation plan: entity mapping, service methods, controller routes, API doc decorators, test strategy. |
| 5. Unit test | YES | TDD — write tests first. Mock DB, test business logic + validations. Tests must be RED before writing implementation. |
| 6. Develop | YES | Make tests pass. Entity, DTO/schema, service, controller, module registration. Follow existing project patterns. |
| 7. API docs | YES | OpenAPI/Swagger decorators on every endpoint. The API docs ARE the official contract. |
| 8. Integration test | YES | Real DB, test full HTTP request/response cycle. |
| 9. Parity test | YES | Dual-endpoint comparison: call legacy endpoint, call new endpoint with same input, compare DB state and response shape. |
| 10. E2E | YES | End-to-end tests against live/staging environment. |
| 11. Manuals | YES | Developer manual (API reference, DTOs, examples) + User/operator manual (functional description, workflows, business rules). |
| 12. Security scan | YES | Full security checklist. |

## Parity Hierarchy (in order of priority)

### 1. DB Parity (MANDATORY — highest priority)

The new endpoint MUST produce the EXACT SAME writes on DB as the legacy endpoint.
Same tables, same columns, same values, same order of operations.
Any difference is a bug.

Verified with parity tests: call legacy endpoint, call new endpoint with same input, compare DB state.

### 2. DTO Parity (MANDATORY)

Request DTO (input) and Response DTO (output) MUST match the legacy contract exactly.
Same field names, same types, same nesting.
Both DTOs explicitly defined — no generic record types or `any`.

### 3. Functional Parity

Same business logic, same validations, same error codes.
If the legacy code has a bug that writes wrong data, replicate the bug first, then fix in a separate commit with documentation.

## Parity Checklist (per endpoint)

- [ ] **DB writes identical** (parity test: same input → same DB state)
- [ ] Request DTO explicitly defined and matching legacy contract
- [ ] Response DTO explicitly defined and matching legacy contract
- [ ] Same HTTP method + path
- [ ] Same HTTP status codes (200, 201, 400, 404, 409, etc.)
- [ ] Same error messages / error codes
- [ ] Same validation rules
- [ ] API docs complete
- [ ] Unit test written
- [ ] Integration test with DB parity assertion written
- [ ] Endpoint marked done in tracking document

## Legacy Analysis Checklist

When analyzing a legacy endpoint, extract:

1. **Route**: HTTP method + path + path params + query params
2. **Request body**: field names, types, required/optional, nested objects
3. **Response body**: field names, types, shape, status codes
4. **Validations**: what gets checked, error messages, error codes
5. **DB operations**: tables read, tables written, join conditions, order
6. **Business rules**: conditional logic, state transitions, calculations
7. **Dependencies**: other services called, external systems, config params
8. **Error paths**: what can go wrong, how errors propagate

## Iron Rule

**No block of work is complete until ALL 12 steps are done.**
If a step is done but another is missing, the work is INCOMPLETE.
Do not move to the next block until all steps for the current block are verified.

## Porting Rules

- **Parity first** — match legacy behaviour exactly, improve later
- **One block at a time** — close and test before moving to next
- **If the new behaves differently from the old, it is a bug in the new**
- **Do not improve during porting** — first it works, then refactor in a separate commit
- **Only port listed endpoints** — do not invent, add, or port endpoints not in the tracking document
- **Mark done only when ALL steps are complete** — not before

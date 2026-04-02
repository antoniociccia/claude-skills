# CLAUDE.md — OrderFlow API

> OrderFlow — Order management REST API
> Started: 2026-03-15

---

## Profile

```yaml
active_profile: bugfix
```

---

## 1. Principles

1. **Reproduce first** — No fix without reproducing the bug first.
2. **Regression test RED before fix** — The test must FAIL proving the bug exists.
3. **Minimal fix** — Smallest diff that correctly fixes the bug.
4. **No side effects** — Fix must not change behaviour for non-buggy cases.
5. **Conventional Commits** — `fix(scope): description`.
6. **Update this file after every fix**.

---

## 2. Stack

| Layer | Technology | License |
|-------|-----------|---------|
| **Backend** | Express 4.19, Node 22 | MIT |
| **Database** | MongoDB 7 (Mongoose 8) | Apache-2.0 |
| **Auth** | JWT (jsonwebtoken) | MIT |
| **Validation** | Joi | BSD-3-Clause |
| **Test** | Jest 30 + Supertest | MIT |
| **Logging** | Winston | MIT |
| **Deploy** | Docker + PM2 | MIT |

---

## 3. Architecture

```
src/
├── config/           # Environment config
├── middleware/        # Auth, validation, error handler
├── models/           # Mongoose models
├── routes/           # Express route handlers
├── services/         # Business logic
├── utils/            # Shared utilities
└── app.js            # Express app setup
```

### Patterns

- **ORM**: Mongoose 8 with schema validation
- **Validation**: Joi schemas in middleware
- **Auth**: JWT middleware on protected routes
- **Error handling**: Global error handler middleware
- **Module pattern**: route -> service -> model

---

## 4. Development Process

6 steps active (bugfix profile):

| Step | Active | Notes |
|------|--------|-------|
| 1. Analysis | YES | Reproduce the bug |
| 2. Functional doc | -- | N/A for bugfix |
| 3. Workflow doc | -- | N/A for bugfix |
| 4. Plan | YES | Root cause + fix plan |
| 5. Unit test | YES | Regression test (RED first) |
| 6. Develop | YES | Apply fix (test goes GREEN) |
| 7. API docs | conditional | Only if API contract changes |
| 8. Integration test | YES | Verify with real MongoDB |
| 9. Parity test | -- | N/A for bugfix |
| 10. E2E | YES | Verify end-to-end |
| 11. Manuals | -- | N/A for bugfix |
| 12. Security scan | conditional | Only if security-related |

---

## 5. Rules for Claude

### When fixing bugs

- **Fix only the bug** — do not refactor, clean up, or "improve while you're there"
- **Minimal change** — smallest diff that fixes correctly
- **Regression test MUST be RED before writing the fix**
- **All existing tests must still pass after the fix**
- **Document root cause in commit message**

---

## 6. Development Index

### Bug Fix Progress (bugfix profile: steps 1, 4, 5, 6, 8, 10)

| Bug | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | Done? |
|-----|---|---|---|---|---|---|---|---|---|----|----|-----|-------|
| #142 order total rounding | Y | -- | -- | Y | Y | Y | -- | Y | -- | Y | -- | -- | YES |
| #157 duplicate webhook fire | Y | -- | -- | Y | Y | Y | -- | Y | -- | - | -- | -- | NO |
| #163 auth token expiry race | Y | -- | -- | Y | - | - | -- | - | -- | - | -- | -- | NO |

### Test Counts

| Type | Count |
|------|-------|
| Unit test | 89 |
| Integration test | 34 |
| E2E test | 15 |
| **Total** | **138** |

### Security Findings

| # | Severity | Module | Status | Description | Fix |
|---|----------|--------|--------|-------------|-----|
| 1 | HIGH | auth | FIXED | Token expiry not checked on refresh | Added expiry validation |

---

## 7. Decisions

| # | Decision | Answer |
|---|----------|--------|
| 1 | DB | MongoDB 7 (existing, not changing) |
| 2 | Auth | JWT with 15-minute expiry |
| 3 | Error format | `{ error: string, message: string, statusCode: number }` |

---

## 8. Open Questions

- [ ] Should we add rate limiting to webhook endpoints?
- [ ] MongoDB Atlas vs self-hosted for staging environment

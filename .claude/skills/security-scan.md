---
name: security-scan
description: >
  Step 12 of the gate process. Framework-agnostic 10-item security checklist.
  Covers input validation, auth, injection, data exposure, error handling,
  dependencies, and audit. Produces a findings table with severity ratings.
---

# Security Scan — Step 12

## Purpose

Systematic security review of completed work. Framework-agnostic checklist
that catches the most common security issues before code reaches production.

## When to Invoke

- After Step 11 (Manuals) or the last active step for the profile
- Active in profiles: porting, greenfield, infra, bugfix (if security-related)

## The 10-Item Checklist

### 1. Input Validation

- [ ] ALL user input is validated before processing
- [ ] Validation happens at the boundary (controller/handler), not deep in business logic
- [ ] Schema validation rejects unknown fields (no open-ended objects)
- [ ] String inputs have max length constraints
- [ ] Numeric inputs have min/max constraints
- [ ] Array inputs have max size constraints
- [ ] File uploads have type and size restrictions

**Fail if**: any user input reaches business logic or DB without validation.

### 2. Authentication

- [ ] All endpoints require authentication (unless explicitly public)
- [ ] Public endpoints are documented and justified
- [ ] Auth tokens are validated on every request (not just checked for existence)
- [ ] Token expiration is enforced
- [ ] No hardcoded credentials anywhere in the codebase

**Fail if**: an endpoint is accessible without valid credentials and is not explicitly marked public.

### 3. Authorization

- [ ] Users can only access their own data (or data they are authorized for)
- [ ] Role checks are in place for privileged operations
- [ ] No IDOR (Insecure Direct Object Reference) — user cannot access resources by guessing IDs
- [ ] Admin endpoints are restricted to admin roles
- [ ] No privilege escalation paths

**Fail if**: a user can access or modify data they should not have access to.

### 4. Injection Prevention

- [ ] No string interpolation in database queries
- [ ] All queries use parameterized statements or ORM methods
- [ ] Dynamic field names are validated against an allowlist
- [ ] Dynamic sort/filter fields are validated against an allowlist
- [ ] No eval(), Function(), or equivalent dynamic code execution with user input
- [ ] No template injection (server-side template rendering with user input)

**Fail if**: any user input is interpolated into queries, commands, or templates.

### 5. Data Exposure

- [ ] Responses do not include sensitive fields (passwords, tokens, internal IDs)
- [ ] Error responses do not leak stack traces, SQL errors, or internal paths
- [ ] Logs do not contain sensitive data (passwords, tokens, PII)
- [ ] Debug endpoints are disabled in production configuration
- [ ] Database connection strings are not exposed

**Fail if**: sensitive information is visible in responses, errors, or logs.

### 6. Rate Limiting & Resource Control

- [ ] Pagination is enforced (max page size clamped)
- [ ] No unbounded queries (SELECT * without LIMIT)
- [ ] File upload sizes are limited
- [ ] Request body size is limited
- [ ] Expensive operations have rate limiting or queuing

**Fail if**: a single request can cause unbounded resource consumption.

### 7. Error Handling

- [ ] All errors return a consistent, clean JSON format
- [ ] No stack traces in production error responses
- [ ] No database error details in responses
- [ ] Unhandled exceptions are caught by a global error handler
- [ ] Error messages are helpful but do not reveal internals

**Fail if**: an error response contains implementation details.

### 8. Dependency Security

- [ ] No dependencies with known critical vulnerabilities
- [ ] No dependencies with restrictive licenses (GPL, LGPL, AGPL, SSPL)
- [ ] Dependencies are pinned to specific versions (no floating ranges)
- [ ] Lock file is committed and up to date

**Fail if**: a dependency has a known critical CVE or a restrictive license.

### 9. Secrets Management

- [ ] No secrets in source code (API keys, passwords, tokens)
- [ ] No secrets in configuration files checked into version control
- [ ] Secrets are loaded from environment variables or a secret manager
- [ ] `.env` files are in `.gitignore`
- [ ] Docker images do not contain secrets

**Fail if**: any secret is found in version-controlled files.

### 10. Audit Trail

- [ ] Write operations on sensitive data are logged with: who, what, when
- [ ] Logs include enough context to reconstruct what happened
- [ ] Logs are immutable (append-only)
- [ ] Failed authentication attempts are logged
- [ ] Admin/privileged operations are logged

**Fail if**: a write operation on sensitive data has no audit trail.

## Findings Table

Report findings in this format:

| # | Severity | Check | Status | Description | Recommendation |
|---|----------|-------|--------|-------------|----------------|
| 1 | CRITICAL | Auth | FAIL | Endpoint X accessible without auth | Add auth guard |
| 2 | HIGH | Injection | FAIL | Dynamic field name not validated | Add allowlist |
| 3 | MEDIUM | Rate Limit | WARN | No page size limit on list endpoint | Clamp to max 100 |
| 4 | LOW | Error | WARN | Stack trace visible in dev mode | Verify prod config |
| 5 | INFO | Audit | PASS | All write operations logged | - |

### Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| CRITICAL | Exploitable now, data at risk | Fix before merge. Block release. |
| HIGH | Significant risk, likely exploitable | Fix before merge. |
| MEDIUM | Moderate risk, conditionally exploitable | Fix within sprint. |
| LOW | Minor risk, defense in depth | Fix when convenient. |
| INFO | Observation, no immediate risk | Track for future. |

## Security Column in Development Index

After completing the scan, update the Security column in the Development Index table
in CLAUDE.md with one of: PASS, WARN (low/medium findings), FAIL (high/critical findings).

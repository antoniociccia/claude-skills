---
name: profile-greenfield
description: >
  Profile for building new features from scratch. Steps 1-8, 10-12 active.
  No parity test (step 9). Codebase scan for analysis. YAGNI enforced.
  Use when creating new modules, features, or services with no legacy equivalent.
---

# Profile: Greenfield

## When to Use

- Building new features, modules, or services from scratch
- Creating a new project or application
- Adding functionality with no legacy equivalent to match
- Designing new APIs, data models, or workflows

## Active Steps

11 of 12 steps active (no parity test):

| Step | Active | Scope |
|------|--------|-------|
| 1. Analysis | YES | Codebase scan: read existing project structure, patterns, conventions, dependencies. Understand what already exists. |
| 2. Functional doc | YES | Business rationale, actors, input/output, business rules, integration points, status transitions, configuration. |
| 3. Workflow doc | YES | Step-by-step operational flow, API calls with expected request/response, state changes, error scenarios, edge cases. |
| 4. Plan | YES | Implementation plan: components, data flow, service boundaries, test strategy, risk areas. |
| 5. Unit test | YES | TDD — write tests first. Mock dependencies, test business logic + validations. Tests must be RED before writing implementation. |
| 6. Develop | YES | Make tests pass. Follow existing project patterns. Build incrementally. |
| 7. API docs | YES | OpenAPI/Swagger, JSDoc, type exports — whatever fits the project's documentation standard. |
| 8. Integration test | YES | Real dependencies (DB, cache, queues). Full request/response cycle. |
| 9. Parity test | -- | Not applicable — no legacy to compare against. |
| 10. E2E | YES | End-to-end tests against live/staging environment. |
| 11. Manuals | YES | Developer manual (API reference, architecture, examples) + User/operator manual (functional description, workflows). |
| 12. Security scan | YES | Full security checklist. |

## Analysis Phase (Step 1)

For greenfield work, analysis means understanding the existing codebase:

1. **Project structure**: file organization, naming conventions, module boundaries
2. **Existing patterns**: how services are structured, how DTOs work, how errors are handled
3. **Dependencies**: what libraries are already in use, what patterns they enforce
4. **Configuration**: how environment variables, feature flags, and config work
5. **Testing patterns**: how existing tests are structured, what tools are used
6. **Adjacent features**: what related functionality already exists that this feature connects to

## Design Principles

### YAGNI — You Aren't Gonna Need It

- Build only what is needed NOW for the current requirement
- Do not add "might be useful later" abstractions
- Do not over-engineer for hypothetical future requirements
- If a feature is not in the current scope, it does not exist

### Incremental Delivery

- Build the smallest useful slice first
- Each increment should be testable and demonstrable
- Prefer working software over comprehensive documentation

### Interface First

- Define the API contract (input/output) before implementation
- Write the types/DTOs before the business logic
- The contract is the source of truth — implementation follows

## Greenfield Checklist (per feature)

- [ ] Existing codebase patterns understood and followed
- [ ] Functional doc written and approved
- [ ] Workflow doc written and approved
- [ ] Implementation plan approved
- [ ] Unit tests written (RED) before implementation
- [ ] Implementation makes tests GREEN
- [ ] API docs complete
- [ ] Integration tests pass with real dependencies
- [ ] E2E tests pass against live environment
- [ ] Dev + user manuals written
- [ ] Security scan clean
- [ ] Feature marked done in tracking document

## Iron Rule

**No block of work is complete until ALL 11 active steps are done.**
If a step is active and not done, the work is INCOMPLETE.
Do not move to the next feature until all steps for the current one are verified.

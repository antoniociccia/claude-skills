---
name: gate-process
description: >
  Use at the start of any development work. Reads the active profile from
  CLAUDE.md and enforces the corresponding gate process. This is the master
  workflow controller that defines all 12 configurable steps.
---

# Gate Process — Configurable Development Workflow

## How It Works

1. Read `active_profile` from the project's CLAUDE.md
2. Load the corresponding profile skill (porting, greenfield, bugfix, infra)
3. Follow the activated steps in order — skip steps marked `--` for the profile
4. Enforce approval gates — no code before plan approval, no exceptions

## Step Catalog

STEP  1: ANALYSIS           Understand what exists before changing anything.
                             The step name is always "Analysis" but the SCOPE
                             varies by profile: legacy analysis (porting),
                             codebase scan (greenfield), bug reproduction
                             (bugfix), or infrastructure audit (infra).

STEP  2: FUNCTIONAL DOC     Business rationale, actors, input/output, rules.
                             → Invoke functional-doc skill.
                             ⚠ APPROVAL GATE

STEP  3: WORKFLOW DOC       Step-by-step flow, API calls, state changes,
                             error scenarios, edge cases.
                             → Invoke workflow-doc skill.
                             ⚠ APPROVAL GATE

STEP  4: PLAN               Implementation plan: components, data flow,
                             test strategy, risk areas.
                             ⛔ APPROVAL GATE — no code before confirmation.

STEP  5: UNIT TEST (TDD)    Write tests FIRST. Tests RED before implementation.

STEP  6: DEVELOP            Make tests pass. Follow existing patterns.

STEP  7: API DOCS           OpenAPI/Swagger, JSDoc, type exports.
                             Framework auto-detected.
                             → Use swagger-generator agent if available.

STEP  8: INTEGRATION TEST   Real dependencies (DB, cache, queues).
                             Full request/response cycle.

STEP  9: PARITY TEST        Dual-endpoint comparison (porting profile only).
                             → Invoke parity-check skill.
                             ⛔ Must be green before proceeding.

STEP 10: E2E / BEHAVIOUR    End-to-end tests against live/staging.
                             → Use test-runner agent.

STEP 11: MANUALS            Dev manual + user/operator manual.
                             → Use manual-writer agent.

STEP 12: SECURITY SCAN      → Invoke security-scan skill.

## Step Activation Matrix

| Step | Porting | Greenfield | Bugfix | Infra |
|------|---------|------------|--------|-------|
| 1. Analysis | legacy | codebase | reproduce | audit |
| 2. Functional doc | YES | YES | -- | -- |
| 3. Workflow doc | YES | YES | -- | -- |
| 4. Plan | YES | YES | YES | YES |
| 5. Unit test | YES | YES | YES | -- |
| 6. Develop | YES | YES | YES | YES |
| 7. API docs | YES | YES | if changed | -- |
| 8. Integration test | YES | YES | YES | validate |
| 9. Parity test | YES | -- | -- | -- |
| 10. E2E | YES | YES | YES | optional |
| 11. Manuals | YES | YES | -- | runbook |
| 12. Security scan | YES | YES | if security | YES |

`--` = explicitly off, not optional.

## Approval Gates

| After step | Gate | Rule |
|------------|------|------|
| Step 2 | Functional doc | Developer must say "ok" or request changes |
| Step 3 | Workflow doc | Developer must say "ok" or request changes |
| Step 4 | Plan | Developer confirms. **No code before confirmation.** |
| Step 9 | Parity | All parity tests green (porting only) |

Gates are rigid. When a step is active, its gate cannot be skipped.

## Iron Rule

**No block of work is complete until ALL active steps for the profile are done.**
If a step is active and not done, the work is INCOMPLETE.

## Block-by-Block Verification

After completing a block of work, run all applicable tests. All tests must pass before moving to the next block.

## Development Index

Track progress in CLAUDE.md using a table with one column per active step.
Update after every completed step — do not accumulate.

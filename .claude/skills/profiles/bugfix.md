---
name: profile-bugfix
description: >
  Profile for fixing bugs with regression test discipline. Steps 1, 4-6, 8, 10.
  Reproduce first, regression test RED before fix. No functional/workflow docs.
  Use when fixing reported bugs, regressions, or unexpected behaviour.
---

# Profile: Bugfix

## When to Use

- Fixing a reported bug or defect
- Addressing a regression (something that used to work and stopped)
- Correcting unexpected behaviour discovered during testing
- Fixing production incidents

## Active Steps

6 of 12 steps active:

| Step | Active | Scope |
|------|--------|-------|
| 1. Analysis | YES | Reproduce the bug: understand the trigger, expected vs actual behaviour, isolate root cause. |
| 2. Functional doc | -- | Not needed for bugs. |
| 3. Workflow doc | -- | Not needed for bugs. |
| 4. Plan | YES | Fix plan: root cause, proposed fix, affected areas, regression risk. |
| 5. Unit test | YES | Regression test — write a test that FAILS with the current bug, proving the bug exists. |
| 6. Develop | YES | Fix the bug. The regression test must turn GREEN. No other tests may break. |
| 7. API docs | conditional | Only if the fix changes the API contract. |
| 8. Integration test | YES | Verify the fix works with real dependencies. |
| 9. Parity test | -- | Not applicable. |
| 10. E2E | YES | Verify the fix works end-to-end. |
| 11. Manuals | -- | Not needed for bugs. |
| 12. Security scan | conditional | Only if the bug is security-related. |

## Bug Reproduction Protocol (Step 1)

### Required Information

1. **Trigger**: What input/action causes the bug?
2. **Expected behaviour**: What should happen?
3. **Actual behaviour**: What happens instead?
4. **Environment**: Where was the bug observed? (version, config, data state)
5. **Frequency**: Always? Intermittent? Under specific conditions?

### Reproduction Steps

1. Set up the same conditions as the bug report
2. Execute the trigger action
3. Observe the actual behaviour
4. Confirm it differs from expected behaviour
5. Document the exact reproduction steps

### Root Cause Analysis

1. Trace the execution path from trigger to failure
2. Identify the exact line/logic where behaviour diverges
3. Understand WHY it fails — do not just find WHERE
4. Check for related bugs — same root cause may affect other areas

## Regression Test Protocol (Step 5)

### The Golden Rule

**The regression test MUST be RED (failing) before you write the fix.**

This proves:
1. The test actually catches the bug
2. The fix is what makes the test pass (not a coincidence)
3. The bug cannot silently return in the future

### Steps

1. Write a test that exercises the exact trigger condition
2. Assert the EXPECTED (correct) behaviour
3. Run the test — it MUST FAIL (proving the bug exists)
4. Only then write the fix
5. Run the test again — it MUST PASS
6. Run ALL existing tests — none may break

## Fix Discipline

### Rules

- **Fix only the bug** — do not refactor, clean up, or "improve while you're there"
- **Minimal change** — the smallest diff that fixes the bug correctly
- **No side effects** — the fix must not change behaviour for non-buggy cases
- **All existing tests must still pass** — if a test breaks, the fix is wrong or the test was wrong
- **Document the root cause** — in the commit message, explain WHY the bug existed

### If the fix requires a larger change

1. Fix the bug minimally first (commit 1)
2. Refactor in a separate commit (commit 2)
3. Each commit must leave tests green

## Bugfix Checklist

- [ ] Bug reproduced consistently
- [ ] Root cause identified and documented
- [ ] Fix plan approved
- [ ] Regression test written and RED (fails without fix)
- [ ] Fix applied — regression test GREEN
- [ ] All existing tests still pass
- [ ] Integration test verifies fix with real dependencies
- [ ] E2E test verifies fix end-to-end
- [ ] API docs updated (if contract changed)
- [ ] Security scan done (if security-related)

## Iron Rule

**No bugfix is complete until ALL 6 active steps are done.**
A fix without a regression test is not a fix — it is a guess.

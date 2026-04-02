---
name: parity-check
description: >
  Step 9 of the gate process. Dual-endpoint comparison between legacy (v1)
  and new (v2) implementations. Compares response SHAPE (keys + types, not
  values), DB state after writes, and status codes. Generates a parity log.
  Active only in the porting profile.
---

# Parity Check — Step 9

## Purpose

Verify that the new implementation produces identical behaviour to the legacy
implementation. Same input must produce the same DB state and the same response
shape. This is the final gate before E2E testing.

## When to Invoke

- After Step 8 (Integration Test) passes
- Before Step 10 (E2E)
- Active only in profile: porting

## Gate Rule

All parity tests must be GREEN before proceeding to Step 10.
A parity failure is a bug in the new implementation.

## Dual-Endpoint Pattern (v2)

### Concept

For each endpoint being ported:

1. Call the **legacy** endpoint with a known input
2. Call the **new** endpoint with the same input
3. Compare:
   - **Response shape**: same keys, same types (not same values — timestamps, IDs may differ)
   - **DB state**: same rows written, same columns, same values
   - **Status codes**: same HTTP status for success and error cases

### Shape Comparison Rules

Compare response SHAPE, not exact values:

| Check | Match? | Example |
|-------|--------|---------|
| Key names | MUST match | `{"name": "..."}` vs `{"name": "..."}` |
| Value types | MUST match | `string` vs `string` |
| Nesting structure | MUST match | `{"a": {"b": 1}}` vs `{"a": {"b": 2}}` |
| Array element shape | MUST match | `[{"id": 1}]` vs `[{"id": 2}]` |
| Exact values | NOT compared | `"2026-01-01"` vs `"2026-01-02"` is OK |
| Auto-generated IDs | NOT compared | UUIDs, auto-increment IDs may differ |
| Timestamps | NOT compared | Creation/modification times may differ |
| Order of keys | NOT compared | `{"a":1,"b":2}` = `{"b":2,"a":1}` |

### DB State Comparison

For write operations, compare the database state after both calls:

1. Start from the same DB state (snapshot or transaction rollback)
2. Execute legacy endpoint
3. Capture affected rows (tables, columns, values)
4. Rollback to the same starting state
5. Execute new endpoint
6. Capture affected rows
7. Compare: same tables, same columns, same values (except auto-generated fields)

## Parity Log

Maintain a parity log in `docs/parity/parity-log.md`:

```markdown
# Parity Log

## <Module Name>

### Endpoint: METHOD /path

| Aspect | Legacy | New | Match | Notes |
|--------|--------|-----|-------|-------|
| Status code | 200 | 200 | YES | |
| Response keys | [list] | [list] | YES | |
| Response types | [list] | [list] | YES | |
| DB table X rows | 1 inserted | 1 inserted | YES | |
| DB table X values | col1=A, col2=B | col1=A, col2=B | YES | |
| Error: missing field | 400 | 400 | YES | |
| Error: not found | 404 | 404 | YES | |

**Result: PASS / FAIL**
**Failures**: [list of mismatches]
**Action**: [what needs to be fixed]
```

## Discovery Mode

When starting parity checks for a new module, use discovery mode:

1. **List all endpoints** in the module
2. **Categorize by type**: read-only, write (create), write (update), write (delete)
3. **Prioritize writes** — DB parity on writes is the highest priority
4. **Start with reads** — easier to verify, builds confidence
5. **Then writes** — more complex, higher stakes

## Test Structure

```
test/parity/
├── <module-name>/
│   ├── <endpoint-name>.parity.test.ts
│   └── helpers/
│       ├── legacy-client.ts    # calls legacy endpoint
│       ├── new-client.ts       # calls new endpoint
│       └── comparator.ts       # shape + DB comparison
```

### Parity Test Template

```
describe('<Module> Parity', () => {
  describe('GET /path', () => {
    it('response shape matches legacy', async () => {
      // 1. Call legacy endpoint
      // 2. Call new endpoint with same input
      // 3. Compare response shape (keys + types)
      // 4. Assert match
    });
  });

  describe('POST /path', () => {
    it('DB state matches legacy after write', async () => {
      // 1. Snapshot DB state
      // 2. Call legacy endpoint
      // 3. Capture DB changes
      // 4. Rollback to snapshot
      // 5. Call new endpoint with same input
      // 6. Capture DB changes
      // 7. Compare DB changes
      // 8. Assert match
    });
  });
});
```

## Handling Intentional Differences

If the new implementation intentionally differs from legacy (security fix, bug fix):

1. Document the difference in the parity log with tag: `INTENTIONAL`
2. Explain WHY the difference exists
3. The parity test should assert the NEW (correct) behaviour
4. Reference the commit that introduced the fix

## When Parity Fails

1. **Do not proceed** to Step 10
2. Investigate the mismatch
3. Determine: is the new code wrong, or is this an intentional improvement?
4. If wrong: fix the new code, re-run parity test
5. If intentional: document as `INTENTIONAL`, update parity test
6. Re-run all parity tests — all must be GREEN

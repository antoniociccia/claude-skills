---
name: behaviour-test
description: >
  Step 10 of the gate process. Framework-agnostic guide for writing end-to-end
  and behaviour tests. Covers what to verify, anti-flaky principles, and
  test structure. Delegates to test-runner agent when available.
---

# Behaviour / E2E Test — Step 10

## Purpose

Verify that the feature works correctly in a real environment, end-to-end.
Not unit tests (mocked), not integration tests (partial) — full system tests
that exercise the feature as a user would.

## When to Invoke

- After Step 9 (Parity Test) or Step 8 (Integration Test) depending on profile
- Active in profiles: porting, greenfield, bugfix, infra (optional)
- Delegates to `test-runner` agent when available

## What to Verify

### Functional Correctness

- [ ] The feature works as specified in the functional doc
- [ ] All success paths produce correct results
- [ ] All error paths return appropriate errors
- [ ] Edge cases behave as documented

### API Contract

- [ ] Request format matches the documented contract
- [ ] Response format matches the documented contract
- [ ] Status codes match the documented contract
- [ ] Error response format is consistent

### Data Integrity

- [ ] Data is persisted correctly after write operations
- [ ] Data is retrieved correctly after read operations
- [ ] Related data is consistent (foreign keys, references)
- [ ] Concurrent operations do not corrupt data

### Integration Points

- [ ] External service calls work (DB, cache, file storage, queues)
- [ ] Authentication and authorization work correctly
- [ ] Configuration parameters affect behaviour as expected

### UI Integration (if applicable)

- [ ] The page that consumes the API loads correctly
- [ ] API calls from the UI return expected responses
- [ ] No JavaScript errors in the browser console
- [ ] Navigation between views works
- [ ] Forms submit correctly

## Anti-Flaky Principles

Flaky tests are worse than no tests. They erode trust and waste time.

### 1. No Sleep-Based Waits

Never use `sleep(3000)` or fixed-time waits. Instead:
- Wait for a specific condition (element visible, response received, state changed)
- Use built-in wait mechanisms of your test framework
- Set reasonable timeouts on waits

### 2. Isolate Test Data

Each test must:
- Create its own test data (or use a dedicated test fixture)
- Not depend on data created by other tests
- Clean up after itself (or use transaction rollback)
- Not fail because another test ran first or did not run

### 3. Deterministic Setup

- Tests must produce the same result every time
- No dependency on external state that changes (time, random, network)
- If time matters, mock it or use relative assertions
- If randomness matters, seed it

### 4. Retry with Caution

- Retrying a test to make it pass is masking a problem
- If a test needs retries, it is flaky — fix the flakiness
- Acceptable retry: network-dependent tests against external services (with documentation)

### 5. Independent Execution

- Each test file must be runnable independently
- Test order must not matter
- No shared mutable state between tests
- Parallel execution must be safe

### 6. Fast Feedback

- Each E2E test should complete in under 30 seconds
- The full E2E suite should complete in under 10 minutes
- If tests are slow, investigate: is it the test or the system?

## Test Structure

```
test/e2e/
├── <module-name>/
│   ├── <feature>.e2e-spec.ts
│   ├── <feature>.e2e-spec.ts
│   └── helpers/
│       ├── setup.ts            # test data creation
│       ├── teardown.ts         # cleanup
│       └── assertions.ts       # custom assertions
├── helpers/
│   ├── auth.ts                 # authentication helpers
│   ├── client.ts               # HTTP client setup
│   └── fixtures.ts             # shared test fixtures
└── config/
    └── test.config.ts          # environment configuration
```

## Test Template

```
describe('<Module> E2E', () => {
  // Setup: create test data, authenticate
  beforeAll(async () => { ... });

  // Cleanup: remove test data
  afterAll(async () => { ... });

  describe('Success paths', () => {
    it('should <expected behaviour> when <condition>', async () => {
      // Arrange: prepare input
      // Act: call the endpoint / perform the action
      // Assert: verify the result
    });
  });

  describe('Error paths', () => {
    it('should return <error> when <condition>', async () => {
      // Arrange: prepare invalid input
      // Act: call the endpoint
      // Assert: verify error response
    });
  });

  describe('Edge cases', () => {
    it('should handle <edge case>', async () => {
      // Arrange: prepare edge case input
      // Act: call the endpoint
      // Assert: verify correct handling
    });
  });
});
```

## Environment Configuration

- E2E tests run against a live or staging environment, not mocked
- Environment URL is configurable via environment variable
- Credentials are configurable via environment variable
- Tests must not modify production data
- Tests should use a dedicated test tenant/namespace if available

## Reporting

After running E2E tests, report:
- Total tests: X
- Passed: X
- Failed: X (with failure details)
- Skipped: X (with reason)
- Duration: X seconds

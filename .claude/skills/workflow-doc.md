---
name: workflow-doc
description: >
  Step 3 of the gate process. Generates a workflow document with step-by-step
  operational flows, API calls, expected request/response, DB state changes,
  error scenarios, and edge cases. Includes an approval gate.
---

# Workflow Document — Step 3

## Purpose

Capture the HOW of a feature — the exact sequence of operations, API calls,
state changes, and error handling. The workflow doc bridges business requirements
(functional doc) and implementation (code).

## When to Invoke

- After Step 2 (Functional Doc) is approved
- Before Step 4 (Plan)
- Active in profiles: porting, greenfield

## Approval Gate

After generating the workflow doc:

1. Present it to the developer
2. Wait for explicit approval ("ok", "approved", "proceed", or equivalent)
3. If changes are requested, revise and present again
4. **Do NOT proceed to Step 4 until the developer approves**

## Template

Create in `docs/workflows/<module-name>.md`:

```markdown
# Workflow: <Module Name>

## Overview

Brief description of what this module does and its business purpose.

## Preconditions

- User role required
- Data state required (e.g., "at least one record in ACTIVE status")
- Configuration parameters needed
- External services that must be available

## Step-by-step Workflow

### Step 1: <Action Name>

- **Actor**: Who performs this action
- **API called**: `METHOD /path/to/endpoint`
- **Request body**:
  ```json
  {
    "field": "value"
  }
  ```
- **Validations performed**:
  - Validation 1: condition, error if failed
  - Validation 2: condition, error if failed
- **DB reads**: What data is fetched, from which tables, with what conditions
- **Business logic**: What computation or transformation happens
- **DB writes**: What data is written, to which tables, which columns
- **Expected API response**: HTTP status, response body:
  ```json
  {
    "field": "value"
  }
  ```
- **Side effects**: Notifications, cache invalidation, event emission

### Step 2: <Action Name>

...

## Error Scenarios

### Scenario: <Error Condition Name>

- **Trigger**: What the client sends or what state causes the error
- **Detection point**: Where in the flow the error is caught
- **Expected result**: HTTP status, error response body
- **DB change**: None (or rollback details)
- **Recovery**: What the client should do to recover

### Scenario: <Error Condition Name>

...

## Edge Cases

| Case | Input/State | Expected Behaviour |
|------|------------|-------------------|
| Empty list | No records match | Return empty array, HTTP 200 |
| Concurrent edit | Two users edit same record | Last write wins / conflict error |
| ... | ... | ... |

## State Machine (if applicable)

```
[CREATED] --submit--> [PENDING] --approve--> [ACTIVE]
                                 --reject---> [REJECTED]
[ACTIVE] --deactivate--> [INACTIVE]
[INACTIVE] --reactivate--> [ACTIVE]
```

## Data Flow Diagram (if helpful)

```
Client --> Controller --> Service --> Repository --> DB
                      --> External Service
                      --> Cache
```
```

## Guidelines

- **Be sequential**: number the steps in exact execution order
- **Be concrete**: use actual field names, actual HTTP methods, actual status codes
- **Show the data**: include request/response JSON examples
- **Cover failures**: every step that can fail must have an error scenario
- **DB state matters**: for each write, specify exactly what changes in the database
- **No ambiguity**: if a step has conditional logic, show each branch

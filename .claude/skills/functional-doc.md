---
name: functional-doc
description: >
  Step 2 of the gate process. Generates a functional document that captures
  business rationale, actors, inputs/outputs, business rules, and integration
  points. Includes an approval gate — developer must approve before proceeding.
---

# Functional Document — Step 2

## Purpose

Capture the WHAT and WHY of a feature before any code is written.
The functional doc is the single source of truth for business requirements.

## When to Invoke

- After Step 1 (Analysis) is complete
- Before Step 3 (Workflow Doc)
- Active in profiles: porting, greenfield

## Approval Gate

After generating the functional doc:

1. Present it to the developer
2. Wait for explicit approval ("ok", "approved", "proceed", or equivalent)
3. If changes are requested, revise and present again
4. **Do NOT proceed to Step 3 until the developer approves**

## Template

Create in `docs/functional/<module-name>.md`:

```markdown
# Functional Doc: <Module Name>

## Business Rationale

Why this function exists. What business problem it solves.
Context within the larger system workflow.

## Actors

- Who uses this function (roles, profiles)
- What permissions are required
- What context the actor is in when using this

## Input

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| ... | ... | ... | ... | ... |

## Output

### Success Response

| Field | Type | Description |
|-------|------|-------------|
| ... | ... | ... |

### Error Responses

| Status | Code | Condition |
|--------|------|-----------|
| 400 | VALIDATION_ERROR | Invalid input |
| 404 | NOT_FOUND | Resource does not exist |
| 409 | CONFLICT | State conflict |
| ... | ... | ... |

## Business Rules

1. **Rule name**: Description of the rule, when it applies, what happens if violated.
2. **Rule name**: Description.
...

## Integration Points

### Depends On
- Which other modules/services this feature calls
- What data it reads from other domains

### Depended On By
- Which other modules/services call this feature
- What UI views consume this endpoint

## Status Transitions (if applicable)

```
[STATE A] --action--> [STATE B] --action--> [STATE C]
```

Describe each transition: what triggers it, who can trigger it, what side effects occur.

## Configuration

- Configuration parameters that affect behaviour
- Feature flags
- Environment-specific settings

## Parity Notes (porting profile only)

- Differences from legacy behaviour (if any)
- Security fixes applied (if any)
- Error handling improvements (if any)
```

## Guidelines

- **Be precise**: use exact field names, types, and status codes
- **Be complete**: cover all success and error paths
- **Be honest**: if something is unclear, mark it as `TBD` and ask
- **No implementation details**: this doc describes WHAT, not HOW
- **No code**: business rules in plain language, not pseudocode
- **Actors matter**: always specify WHO can do WHAT

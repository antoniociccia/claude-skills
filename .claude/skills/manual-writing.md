---
name: manual-writing
description: >
  Step 11 of the gate process. Generates two mandatory documents: a developer
  manual (API reference, architecture, integration guide) and a user/operator
  manual (functional description, workflows, plain language). Delegates to
  manual-writer agent when available.
---

# Manual Writing — Step 11

## Purpose

Every completed feature needs two documents:
1. **Developer manual** — technical reference for other developers
2. **User/operator manual** — functional guide for end users, in plain language

## When to Invoke

- After Step 10 (E2E) or the last active test step
- Active in profiles: porting (both manuals), greenfield (both manuals), infra (runbook)
- Delegates to `manual-writer` agent when available

## Developer Manual Template

Create in `docs/manuals/dev/<module-name>.md`:

```markdown
# Developer Manual: <Module Name>

## Overview

Module purpose, responsibilities, dependencies.
Where it fits in the system architecture.

## API Reference

### `METHOD /path/to/endpoint`

**Description**: What this endpoint does.
**Auth**: Required / Public
**Tags**: [module tag]

#### Request

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| ... | ... | ... | ... | ... |

#### Response (200)

| Field | Type | Description |
|-------|------|-------------|
| ... | ... | ... |

#### Error Responses

| Status | Code | Description |
|--------|------|-------------|
| 400 | VALIDATION_ERROR | Invalid input |
| 404 | NOT_FOUND | Resource not found |

#### Example

**Request:**
```json
{ ... }
```

**Response:**
```json
{ ... }
```

### `METHOD /path/to/other-endpoint`

...

## Data Model

- Entity name, table, key fields
- Relationships to other entities
- Constraints and indexes

## Configuration

- Environment variables used
- Configuration parameters that affect behaviour
- Feature flags

## Integration Guide

- How to call this module from other services
- Dependencies and prerequisites
- Common patterns and idioms

## Error Handling

- How errors propagate
- Custom error codes and their meaning
- Retry strategies (if applicable)
```

## User/Operator Manual Template

Create in `docs/manuals/user/<module-name>.md`:

```markdown
# User Manual: <Module Name>

## Overview

What this module does in plain language.
What benefit it provides to the user.

## Prerequisites

- Role required to use this feature
- Data or state that must exist before using this feature
- Configuration that must be in place

## Step-by-step Guide

### 1. <Action>

Description of the action in plain, non-technical language.
What the user sees, what they click, what they enter.
What happens after the action.

### 2. <Action>

...

## Business Rules

- Rule 1: explanation in plain language
- Rule 2: explanation in plain language

## Common Errors and Solutions

| Problem | Cause | Solution |
|---------|-------|----------|
| Cannot save | Missing required field | Fill in all required fields |
| Record not found | Deleted or moved | Check with administrator |
| ... | ... | ... |

## Glossary

| Term | Meaning |
|------|---------|
| ... | ... |

## FAQ

**Q: Can I undo this action?**
A: Yes/No, because...

**Q: Who can see my changes?**
A: Users with role X can see...
```

## Runbook Template (infra profile)

Create in `docs/runbooks/<component-name>.md`:

```markdown
# Runbook: <Component Name>

## Overview

What this component does, where it runs, why it matters.

## Operations

### Deploy / Apply

Step-by-step instructions to deploy or apply changes.

### Verify

How to verify the component is healthy after changes.

### Monitor

What to watch, where to look, what is normal vs abnormal.

### Scale

How to scale up/down (if applicable).

## Troubleshooting

### Symptom: <Description>

- **Likely cause**: ...
- **Diagnostic commands**: ...
- **Fix**: ...

### Symptom: <Description>

...

## Recovery

### Rollback

How to revert to the previous state.

### Restore from Backup

How to restore data (if applicable).

### Rebuild from Scratch

Disaster recovery procedure.

## Contacts

- On-call team / owner
- Escalation path
```

## Guidelines

- **Developer manual**: precise, technical, complete. Include all field names, types, status codes.
- **User manual**: plain language, no jargon, no code. Write for someone who does not know the internals.
- **Runbook**: actionable, copy-pasteable commands, no ambiguity.
- **Keep updated**: when the feature changes, update the manuals in the same commit.

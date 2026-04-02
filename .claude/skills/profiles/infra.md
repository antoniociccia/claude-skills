---
name: profile-infra
description: >
  Profile for infrastructure, DevOps, and configuration work.
  Steps 1, 4, 6, 8, 11, 12 active. Current state audit, runbook,
  rollback plan. Use for CI/CD, Docker, deployments, monitoring, migrations.
---

# Profile: Infra

## When to Use

- Setting up or modifying CI/CD pipelines
- Docker configuration (Dockerfiles, docker-compose, multi-stage builds)
- Database migrations and schema changes
- Monitoring, alerting, and observability setup
- Deployment configuration and automation
- Infrastructure as code changes
- Environment configuration and secrets management
- Performance tuning and scaling

## Active Steps

6 of 12 steps active:

| Step | Active | Scope |
|------|--------|-------|
| 1. Analysis | YES | Current state audit: what exists now, what works, what does not, what is the gap. |
| 2. Functional doc | -- | Not needed for infra. |
| 3. Workflow doc | -- | Not needed for infra. |
| 4. Plan | YES | Implementation plan with rollback strategy. What changes, what could break, how to revert. |
| 5. Unit test | -- | Infra rarely has unit tests. |
| 6. Develop | YES | Implement the changes. Follow existing conventions. |
| 7. API docs | -- | Not applicable to infra. |
| 8. Integration test | YES | Validate: does it actually work? Can you deploy, connect, build, run? |
| 9. Parity test | -- | Not applicable. |
| 10. E2E | optional | Smoke test the deployed result if applicable. |
| 11. Manuals | YES | Runbook: how to operate, troubleshoot, and recover. |
| 12. Security scan | YES | Secrets exposure, permissions, network policies, image vulnerabilities. |

## Current State Audit (Step 1)

### What to Document

1. **Existing infrastructure**: what services, tools, and configurations are in place
2. **Current state**: is it working? partially? broken? never set up?
3. **Dependencies**: what depends on this infra? what does this infra depend on?
4. **Configuration**: environment variables, secrets, config files
5. **Access**: who has access? what credentials are needed?
6. **Known issues**: what problems exist today?

### Gap Analysis

1. What is the desired end state?
2. What is the delta between current and desired?
3. What is the minimal change to close the gap?

## Plan Requirements (Step 4)

Every infra plan MUST include:

### 1. Change Description

What exactly will change. Be specific: file paths, config keys, service names.

### 2. Impact Analysis

- What services will be affected?
- Will there be downtime?
- What could break?

### 3. Rollback Strategy

**Every infra change must be reversible.**

- How to revert if the change fails
- How long does rollback take?
- Is data loss possible during rollback?
- Who needs to be notified?

### 4. Validation Steps

How to verify the change worked:
- Health checks
- Smoke tests
- Log inspection
- Metric verification

## Runbook Requirements (Step 11)

Every infra change must produce or update a runbook with:

### Operations

- How to deploy / apply the change
- How to verify it is working
- How to monitor ongoing health
- How to scale up/down (if applicable)

### Troubleshooting

- Common failure modes and symptoms
- Diagnostic commands
- Log locations
- Escalation path

### Recovery

- How to rollback
- How to restore from backup (if applicable)
- How to rebuild from scratch (disaster recovery)
- Recovery time expectations

## Security Checklist (Step 12)

For infra changes, the security scan focuses on:

- [ ] No secrets in code, config files, or Docker images
- [ ] Environment variables used for sensitive values
- [ ] Least-privilege permissions on all service accounts
- [ ] Network policies restrict access to necessary ports only
- [ ] Docker images use specific version tags (no `latest`)
- [ ] Docker images based on minimal base (alpine, distroless)
- [ ] SSL/TLS configured for all external endpoints
- [ ] Logging does not capture sensitive data
- [ ] Backup encryption enabled (if applicable)
- [ ] Access audit trail enabled (if applicable)

## Infra Checklist

- [ ] Current state audited and documented
- [ ] Plan includes rollback strategy
- [ ] Plan approved
- [ ] Changes implemented
- [ ] Validation confirms changes work
- [ ] Runbook written or updated
- [ ] Security scan clean
- [ ] Smoke test passed (if applicable)

## Iron Rule

**No infra change is complete without a rollback strategy and a runbook.**
A change without a way back is not a change — it is a gamble.

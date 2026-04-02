# claude-skills

A generic, reusable development framework for Claude Code. Configurable 12-step gate process with 4 project profiles, 6 step skills, and 6 specialized agents.

Drop it into any project. Pick a profile. Claude follows the process.

**Who is this for?** Teams and solo developers using Claude Code who want structured, repeatable workflows with quality gates. If your project is a quick script or prototype, this framework is overkill -- just code. If you're building production software, this keeps Claude disciplined.

## Quick Start

1. **Copy the `.claude/` directory** into your project root
2. **Copy `CLAUDE.md.template`** to your project root as `CLAUDE.md`
3. **Set `active_profile`** in your CLAUDE.md to one of: `porting`, `greenfield`, `bugfix`, `infra`

That's it. Claude will read the profile and enforce the corresponding gate process.

## Profiles

| Profile | Use Case | Active Steps | Key Feature |
|---------|----------|-------------|-------------|
| **porting** | Migrating from one stack to another | All 12 | Parity tests, legacy analysis |
| **greenfield** | Building new features from scratch | 11 (no parity) | YAGNI, interface-first |
| **bugfix** | Fixing reported bugs | 6 | Regression test RED before fix |
| **infra** | DevOps, CI/CD, deployments | 6 | Rollback strategy, runbook |

## The 12-Step Gate Process

| Step | Name | Porting | Greenfield | Bugfix | Infra |
|------|------|---------|------------|--------|-------|
| 1 | Analysis | legacy | codebase | reproduce | audit |
| 2 | Functional Doc | YES | YES | -- | -- |
| 3 | Workflow Doc | YES | YES | -- | -- |
| 4 | Plan | YES | YES | YES | YES |
| 5 | Unit Test (TDD) | YES | YES | YES | -- |
| 6 | Develop | YES | YES | YES | YES |
| 7 | API Docs | YES | YES | if changed | -- |
| 8 | Integration Test | YES | YES | YES | validate |
| 9 | Parity Test | YES | -- | -- | -- |
| 10 | E2E / Behaviour | YES | YES | YES | optional |
| 11 | Manuals | YES | YES | -- | runbook |
| 12 | Security Scan | YES | YES | if security | YES |

`--` = explicitly off. Steps marked YES have mandatory approval gates at steps 2, 3, 4, and 9.

## Skills

Skills are invoked automatically by the gate process at the appropriate step.

| Skill | Step | Purpose |
|-------|------|---------|
| `gate-process` | -- | Master workflow controller, reads profile, enforces steps |
| `functional-doc` | 2 | Generates business requirement documents |
| `workflow-doc` | 3 | Generates step-by-step operational flow documents |
| `parity-check` | 9 | Dual-endpoint comparison (shape + DB state) |
| `security-scan` | 12 | 10-item security checklist with severity ratings |
| `manual-writing` | 11 | Dev manual + user manual templates |
| `behaviour-test` | 10 | E2E test guidelines and anti-flaky principles |

## Agents

Agents are specialized sub-agents that handle specific tasks.

| Agent | Model | Purpose |
|-------|-------|---------|
| `test-runner` | sonnet | Detect framework, run/debug/fix tests, classify failures |
| `manual-writer` | haiku | Analyze code, generate dev + user manuals |
| `legacy-analyzer` | sonnet | Analyze any legacy codebase, extract contracts |
| `parity-tester` | sonnet | Generate dual-endpoint parity test scripts |
| `swagger-generator` | sonnet | Detect API framework, generate OpenAPI annotations |
| `security-scanner` | sonnet | Framework-aware static analysis, 10 checks |

> **Model selection:** Agents use `sonnet` by default for complex reasoning tasks. `manual-writer` uses `haiku` for faster, cost-effective document generation. Override models in agent frontmatter to match your needs.

## Customization

### Adding a new profile

Create a file in `.claude/skills/profiles/<name>.md` with:
- Frontmatter (name, description)
- Active steps table
- Profile-specific rules and checklists
- Iron rule statement

Then reference it in your CLAUDE.md's `active_profile` field.

### Modifying steps

Edit `.claude/skills/gate-process.md` to:
- Add new steps to the catalog
- Modify the activation matrix
- Change approval gate rules

### Adding project-specific skills

Create additional `.md` files in `.claude/skills/` with frontmatter. They will be available alongside the framework skills.

## Upgrade Path

To update the framework in an existing project:

1. Back up any customizations you made to `.claude/` files
2. Copy the new `.claude/` directory over the old one
3. Re-apply your customizations
4. Your `CLAUDE.md` does not need to change (it references profiles by name)

## Examples

Three example CLAUDE.md files are provided:

| Example | Profile | Stack | Path |
|---------|---------|-------|------|
| NestJS Porting | porting | NestJS + TypeORM + Fastify | `examples/nestjs-porting/CLAUDE.md` |
| Next.js Greenfield | greenfield | Next.js 15 + Prisma + Tailwind | `examples/nextjs-greenfield/CLAUDE.md` |
| Express Bugfix | bugfix | Express + MongoDB + Jest | `examples/express-bugfix/CLAUDE.md` |

## License

MIT

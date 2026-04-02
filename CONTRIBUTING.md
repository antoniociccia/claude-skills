# Contributing to claude-skills

## Adding a new profile

1. Create a new file in `.claude/skills/profiles/<name>.md`
2. Include frontmatter with `name` and `description`
3. Define the active steps table (which of the 12 steps are on/off)
4. Write profile-specific rules, checklists, and the iron rule
5. Add the profile to the Step Activation Matrix in `.claude/skills/gate-process.md`

## Adding a new skill

1. Create a new `.md` file in `.claude/skills/`
2. Include frontmatter with `name` and `description`
3. Write the skill content: what it does, when it runs, inputs/outputs
4. Reference it from the appropriate step in `gate-process.md` if it maps to a step

## Adding a new agent

1. Create a new `.md` file in `.claude/agents/`
2. Include frontmatter with `name`, `description`, and `model` (e.g., `sonnet`, `haiku`)
3. Define the agent's responsibilities, inputs, outputs, and example invocations
4. Add the agent to the Agents table in `README.md`

## Adding an example

1. Create a new directory in `examples/<name>/`
2. Add a `CLAUDE.md` file following `CLAUDE.md.template`
3. Fill in realistic data for the chosen profile (stack, architecture, development index)
4. Use step numbers (1-12) in the Development Index table, with `--` for inactive steps
5. Add the example to the Examples table in `README.md`

## PR guidelines

- One change per PR -- do not bundle unrelated fixes
- Test with Claude Code before submitting: load the skill/profile/agent in a real project and verify Claude follows it correctly
- Follow Conventional Commits: `feat:`, `fix:`, `docs:`, `chore:`
- Update `README.md` if you add profiles, skills, agents, or examples

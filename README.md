# Skill Vault

Personal library of Claude agent skills, personas, and workflows used daily across coding, planning, review, and design work. Each skill is a self-contained `SKILL.md` (+ optional bundled resources) following the [Agent Skills](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) format — droppable into any project's `.claude/skills/` or a user-level `~/.claude/skills/` dir.

## Structure

```
skill-vault/
├── README.md              this file
├── docs/
│   ├── PROPOSAL.md         vision, scope, maintenance rules for this repo
│   └── CATALOG.md          full categorized index: skills, agents, workflows
├── .agents/
│   ├── skills/
│   │   └── <skill-name>/
│   │       ├── SKILL.md    required — name, description, instructions
│   │       └── ...         optional: scripts, templates, reference docs
│   ├── agent/
│   │   └── <persona>.md    specialist personas (backend, frontend, security, ...)
│   └── workflows/
│       └── <workflow>.md   slash-command-style entry points (plan, debug, deploy, ...)
└── .gitignore
```

## Categories

43 skills across: meta/always-on, orchestration & routing, context management, skill authoring, planning, code quality, frontend, backend, Python, SEO, git & code review — plus 19 agent personas and 13 workflows.

Full index with descriptions → [`docs/CATALOG.md`](docs/CATALOG.md).

## Usage

Symlink or copy what you want into a project:

```bash
ln -s ~/path/to/skill-vault/.agents/skills/tdd .claude/skills/tdd
```

Or point Claude Code at this repo directly if your setup supports a shared skills path.

## Adding a skill

1. `.agents/skills/<kebab-case-name>/SKILL.md` with YAML frontmatter (`name`, `description`, optional `allowed-tools`).
2. Keep `description` specific — it drives auto-routing, so state what the skill does *and* when to use it.
3. Add a row to `docs/CATALOG.md` under the right category.
4. If the skill needs helper files (scripts, templates), put them alongside `SKILL.md` in the same dir, not at repo root.

See [`.agents/skills/write-a-skill`](.agents/skills/write-a-skill) for the full authoring guide, and [`docs/PROPOSAL.md`](docs/PROPOSAL.md) for repo scope/rules.

## License

Personal use. No license granted for redistribution unless stated otherwise.

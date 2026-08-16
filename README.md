# Skill Vault

Personal library of Claude Agent Skills used daily across coding, planning, review, and design work. Each skill is a self-contained `SKILL.md` (+ optional bundled resources) following the [Agent Skills](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) format — droppable into any project's `.claude/skills/` or a user-level `~/.claude/skills/` dir.

## Structure

```
skill-vault/
├── README.md            this file
├── docs/
│   ├── PROPOSAL.md       vision, scope, maintenance rules for this repo
│   └── CATALOG.md        full categorized skill index
├── skills/
│   └── <skill-name>/
│       ├── SKILL.md      required — name, description, instructions
│       └── ...           optional: scripts, templates, reference docs
└── .gitignore
```

## Categories

44 skills across: meta/always-on, orchestration & routing, context management, skill authoring, planning, code quality, frontend, backend, Python, SEO, git & code review.

Full index with descriptions → [`docs/CATALOG.md`](docs/CATALOG.md).

## Usage

Symlink or copy the skills you want into a project:

```bash
ln -s ~/path/to/skill-vault/skills/tdd .claude/skills/tdd
```

Or point Claude Code at this repo directly if your setup supports a shared skills path.

## Adding a skill

1. `skills/<kebab-case-name>/SKILL.md` with YAML frontmatter (`name`, `description`, optional `allowed-tools`).
2. Keep `description` specific — it drives auto-routing, so state what the skill does *and* when to use it.
3. Add a row to `docs/CATALOG.md` under the right category.
4. If the skill needs helper files (scripts, templates), put them alongside `SKILL.md` in the same dir, not at repo root.

See [`skills/write-a-skill`](skills/write-a-skill) for the full authoring guide, and [`docs/PROPOSAL.md`](docs/PROPOSAL.md) for repo scope/rules.

## License

Personal use. No license granted for redistribution unless stated otherwise.

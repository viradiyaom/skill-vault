# Proposal: Skill Vault

## Problem

Daily-use Claude skills (agent workflows, coding standards, review checklists) were scattered, undocumented, and only local — no version history, no way to diff changes, no single source of truth on what exists or why. `SKILLS_DOC.md` had drifted from actual dir names (e.g. `gh-stage-review` renamed to `code-review`, `zoom-out` listed w/o corresponding dir at time of audit).

## Goal

One versioned repo holding every skill actively in use, browsable and diffable, with a catalog that stays accurate because it's generated from source (`SKILL.md` frontmatter), not hand-maintained prose.

## Scope

**In scope:**
- Skill definitions (`SKILL.md` + bundled scripts/templates/refs) for personal agent workflows.
- Catalog/index docs describing what each skill does and when Claude should use it.
- Light governance: naming convention, required frontmatter fields, category taxonomy.

**Out of scope:**
- Skill *execution* history, transcripts, or session logs — those stay local/ephemeral.
- Project-specific skills that only make sense inside one codebase (those live in that project's own `.claude/skills/`).
- Secrets, API keys, tokens — skills that need them read from env vars, never commit values.

## Structure decisions

| Decision | Reasoning |
|---|---|
| Flat `skills/<name>/` dirs, no deeper nesting by category | Categories change over time; dir moves break symlinks/scripts pointing at skills. Category info lives in `docs/CATALOG.md` instead — one indirection layer, zero churn. |
| `docs/` holds meta-docs, not `skills/` | Keeps `skills/` a pure, scannable list of actual skills — no README noise mixed in. |
| Catalog generated from frontmatter, reviewed by hand | Frontmatter `description` is already required (drives Claude's auto-routing) — reusing it as the catalog source avoids two places to update. |
| No skill renames without updating `docs/CATALOG.md` in the same change | Prevents doc drift that caused this proposal in the first place. |

## Maintenance rules

1. Every skill dir needs `SKILL.md` with `name` + `description` frontmatter at minimum.
2. `description` states both *what* the skill does and *when* to use it — this is what routes auto-invocation.
3. New skill → add catalog row in same commit.
4. Skill removed/renamed → update catalog + README skill count in same commit.
5. No secrets, tokens, or personal data in any file under `skills/` or `docs/`.

## Open questions

- Publish as public repo (share skills w/ others) or keep private? Default: private until reviewed for anything sensitive.
- Add automated catalog-generation script (parse frontmatter → regenerate `CATALOG.md`) vs. manual upkeep? Manual for now given repo size (44 skills); revisit if it grows past ~75 or drifts again.

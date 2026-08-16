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
| `.agents/{skills,agent,workflows}/` split, no deeper nesting within each | Three distinct artifact types — reusable skills, specialist personas, slash-command entry points — get three flat dirs. Categories still change over time; dir moves break symlinks/scripts pointing at skills, so category info lives in `docs/CATALOG.md` instead, not folder nesting. |
| `docs/` holds meta-docs, not `.agents/` | Keeps `.agents/` a pure, scannable set of actual skills/agents/workflows — no README noise mixed in. |
| Catalog generated from frontmatter/content, reviewed by hand | Frontmatter `description` is already required for skills (drives Claude's auto-routing) — reusing it as the catalog source avoids two places to update. Agent/workflow files without frontmatter get a hand-written one-liner instead. |
| No renames without updating `docs/CATALOG.md` in the same change | Prevents doc drift that caused this proposal in the first place. |

## Maintenance rules

1. Every skill dir needs `SKILL.md` with `name` + `description` frontmatter at minimum.
2. `description` states both *what* the skill does and *when* to use it — this is what routes auto-invocation.
3. New skill/agent/workflow → add catalog row in same commit.
4. Skill removed/renamed → update catalog + README counts in same commit.
5. No secrets, tokens, or personal data in any file under `.agents/` or `docs/`.
6. No third-party tool/product branding embedded in skill content (e.g. a prior "AG Kit" reference was found and stripped from 3 files — replaced w/ generic phrasing, no functional change). Keep it that way: skill text should describe *behavior*, not name the tool it happened to be authored under.

## Change log

- **2026-08-16** — Repo restructured from flat `skills/` to `.agents/{skills,agent,workflows}/` to also track the 19 specialist agent personas and 13 workflow entry points already in daily use, not just skills. All paths in README/CATALOG updated accordingly. Also swept full repo for "AG Kit" branding leftover from original skill authoring tool; found + removed 5 mentions across 3 files (`parallel-agents/SKILL.md`, `workflows/plan.md`, `workflows/orchestrate.md`), no skill/file/dir names changed.

## Open questions

- Publish as public repo (share skills w/ others) or keep private? Default: private until reviewed for anything sensitive.
- Add automated catalog-generation script (parse frontmatter → regenerate `CATALOG.md`) vs. manual upkeep? Manual for now given repo size (43 skills + 19 agents + 13 workflows); revisit if it grows much further or drifts again.

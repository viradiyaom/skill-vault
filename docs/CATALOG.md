# Skill Catalog

Full list of skills in `skills/`, grouped by category. Each skill = one dir w/ `SKILL.md` (Claude Agent Skill format).

## Meta / Always-On

| Skill | Purpose |
|---|---|
| [caveman](../skills/caveman) | Ultra-compressed communication mode. Cuts token usage ~75% by speaking terse while keeping full technical accuracy. |
| [fable-5-brain](../skills/fable-5-brain) | Reasoning-discipline protocol run on every answer to prevent confident-but-wrong output: decompose, verify facts by second route, label Certain/Likely/Assumption, self-attack draft. |

## Orchestration & Routing

| Skill | Purpose |
|---|---|
| [batch-operations](../skills/batch-operations) | Apply pattern-based bulk modifications across many files at once. |
| [coordinator-mode](../skills/coordinator-mode) | Multi-agent orchestration: parallel workers, synthesis protocols, coordinator lifecycle. |
| [intelligent-routing](../skills/intelligent-routing) | Auto-selects best specialist agent for a request w/o explicit mention. |
| [parallel-agents](../skills/parallel-agents) | Patterns for running multiple agents in parallel across independent tasks. |
| [performance-profiling](../skills/performance-profiling) | Measurement + analysis techniques for perf issues (Lighthouse, bundle size, Core Web Vitals). |
| [to-prd](../skills/to-prd) | Turn conversation context into a PRD, publish to issue tracker. |

## Context Management

| Skill | Purpose |
|---|---|
| [context-compression](../skills/context-compression) | Summarize/compress long-session context, prevent degradation. |
| [memory-system](../skills/memory-system) | Persistent cross-session memory: MEMORY.md index + topic files. |
| [handoff](../skills/handoff) | Compact current conversation into handoff doc for another agent. |
| [zoom-out](../skills/zoom-out) | Give broader/higher-level perspective on unfamiliar code section. |

## Skill Authoring

| Skill | Purpose |
|---|---|
| [skillify](../skills/skillify) | Extract repetitive multi-step workflows into new reusable skills. |
| [write-a-skill](../skills/write-a-skill) | Create new skills w/ proper structure + progressive disclosure. |

## Planning & Project Builder

| Skill | Purpose |
|---|---|
| [grill-me](../skills/grill-me) | Interview user relentlessly on plan/design until shared understanding reached. |
| [grill-with-docs](../skills/grill-with-docs) | Grilling session that also updates CONTEXT.md / ADRs inline. |
| [brainstorming](../skills/brainstorming) | Socratic questioning protocol for unclear requirements (mandatory for complex asks). |
| [plan-writing](../skills/plan-writing) | Structured task plans: breakdowns, dependencies, verification criteria. |
| [app-builder](../skills/app-builder) | Orchestrator that scaffolds full-stack apps from natural-language requests. |
| [systematic-debugging](../skills/systematic-debugging) | 4-phase debugging methodology w/ root-cause analysis. |
| [diagnose](../skills/diagnose) | Reproduce → minimise → hypothesise → instrument → fix → regression-test loop. |
| [tdd](../skills/tdd) | Test-driven development, red-green-refactor. |
| [setup-matt-pocock-skills](../skills/setup-matt-pocock-skills) | Wires AGENTS.md/CLAUDE.md + docs/agents/ so engineering skills know repo's issue tracker/labels/doc layout. |

## Code Quality

| Skill | Purpose |
|---|---|
| [clean-code](../skills/clean-code) | Concise, direct coding standards — no over-engineering, no filler comments. |
| [improve-codebase-architecture](../skills/improve-codebase-architecture) | Find refactor/deepening opportunities informed by CONTEXT.md + ADRs. |
| [simplify-code](../skills/simplify-code) | Strip unnecessary abstractions, dead code, deep nesting. |

## Frontend

| Skill | Purpose |
|---|---|
| [nextjs-react-expert](../skills/nextjs-react-expert) | React/Next.js perf optimization (Vercel Eng patterns). |
| [frontend-design](../skills/frontend-design) | Web UI design thinking: layout, color, typography. |
| [game-development](../skills/game-development) | Orchestrator routing to Unity/Godot/Unreal/Phaser skills. |
| [mobile-design](../skills/mobile-design) | Mobile-first design for iOS/Android, React Native, Flutter. |
| [web-design-guidelines](../skills/web-design-guidelines) | Audit UI against Web Interface Guidelines / accessibility. |
| [webapp-testing](../skills/webapp-testing) | E2E testing principles, Playwright, deep app audits. |
| [taste-skill-v1](../skills/taste-skill-v1) | Legacy design-taste skill, kept for backward compat. |

## Backend

| Skill | Purpose |
|---|---|
| [nodejs-best-practices](../skills/nodejs-best-practices) | Node.js framework selection, async patterns, security, architecture. |
| [api-patterns](../skills/api-patterns) | REST vs GraphQL vs tRPC, response formats, versioning, pagination. |
| [database-design](../skills/database-design) | Schema design, indexing, ORM selection, serverless DBs. |

## Python

| Skill | Purpose |
|---|---|
| [python-patterns](../skills/python-patterns) | Framework selection, async patterns, type hints, project structure. |

## SEO

| Skill | Purpose |
|---|---|
| [seo-fundamentals](../skills/seo-fundamentals) | E-E-A-T, Core Web Vitals, Google algorithm principles. |

## Git & Code Review

| Skill | Purpose |
|---|---|
| [git-ops](../skills/git-ops) | Commit, push, PR creation, branch mgmt via natural language. Includes vuln scan + AI review gate. |
| [code-review](../skills/code-review) | Reviews diff/file/snippet, returns prioritized issue list. Framework-specific rules via `techs/`. |
| [git-review-pr](../skills/git-review-pr) | Reviews GitHub PR from URL, posts review comment. |
| [git-review-stage](../skills/git-review-stage) | Reviews staged git changes, returns prioritized issues. |
| [vulnerability-scanner](../skills/vulnerability-scanner) | OWASP 2025 vuln analysis, supply-chain security, attack-surface mapping. |

---

**Count:** 44 skills. Source of truth = frontmatter `description:` in each `skills/<name>/SKILL.md`, not this table — re-run generation if skills change.

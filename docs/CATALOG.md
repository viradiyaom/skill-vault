# Skill Catalog

Full list of skills in `.agents/skills/`, grouped by category. Each skill = one dir w/ `SKILL.md` (Claude Agent Skill format).

## Meta / Always-On

| Skill | Purpose |
|---|---|
| [caveman](../.agents/skills/caveman) | Ultra-compressed communication mode. Cuts token usage ~75% by speaking terse while keeping full technical accuracy. |
| [fable-5-brain](../.agents/skills/fable-5-brain) | Reasoning-discipline protocol run on every answer to prevent confident-but-wrong output: decompose, verify facts by second route, label Certain/Likely/Assumption, self-attack draft. |

## Orchestration & Routing

| Skill | Purpose |
|---|---|
| [batch-operations](../.agents/skills/batch-operations) | Apply pattern-based bulk modifications across many files at once. |
| [coordinator-mode](../.agents/skills/coordinator-mode) | Multi-agent orchestration: parallel workers, synthesis protocols, coordinator lifecycle. |
| [intelligent-routing](../.agents/skills/intelligent-routing) | Auto-selects best specialist agent for a request w/o explicit mention. |
| [parallel-agents](../.agents/skills/parallel-agents) | Patterns for running multiple agents in parallel across independent tasks. |
| [performance-profiling](../.agents/skills/performance-profiling) | Measurement + analysis techniques for perf issues (Lighthouse, bundle size, Core Web Vitals). |
| [to-prd](../.agents/skills/to-prd) | Turn conversation context into a PRD, publish to issue tracker. |

## Context Management

| Skill | Purpose |
|---|---|
| [context-compression](../.agents/skills/context-compression) | Summarize/compress long-session context, prevent degradation. |
| [memory-system](../.agents/skills/memory-system) | Persistent cross-session memory: MEMORY.md index + topic files. |
| [handoff](../.agents/skills/handoff) | Compact current conversation into handoff doc for another agent. |
| [zoom-out](../.agents/skills/zoom-out) | Give broader/higher-level perspective on unfamiliar code section. |

## Skill Authoring

| Skill | Purpose |
|---|---|
| [skillify](../.agents/skills/skillify) | Extract repetitive multi-step workflows into new reusable skills. |
| [write-a-skill](../.agents/skills/write-a-skill) | Create new skills w/ proper structure + progressive disclosure. |

## Planning & Project Builder

| Skill | Purpose |
|---|---|
| [grill-me](../.agents/skills/grill-me) | Interview user relentlessly on plan/design until shared understanding reached. |
| [grill-with-docs](../.agents/skills/grill-with-docs) | Grilling session that also updates CONTEXT.md / ADRs inline. |
| [brainstorming](../.agents/skills/brainstorming) | Socratic questioning protocol for unclear requirements (mandatory for complex asks). |
| [plan-writing](../.agents/skills/plan-writing) | Structured task plans: breakdowns, dependencies, verification criteria. |
| [app-builder](../.agents/skills/app-builder) | Orchestrator that scaffolds full-stack apps from natural-language requests. |
| [systematic-debugging](../.agents/skills/systematic-debugging) | 4-phase debugging methodology w/ root-cause analysis. |
| [diagnose](../.agents/skills/diagnose) | Reproduce → minimise → hypothesise → instrument → fix → regression-test loop. |
| [tdd](../.agents/skills/tdd) | Test-driven development, red-green-refactor. |
| [setup-matt-pocock-skills](../.agents/skills/setup-matt-pocock-skills) | Wires AGENTS.md/CLAUDE.md + docs/agents/ so engineering skills know repo's issue tracker/labels/doc layout. |

## Code Quality

| Skill | Purpose |
|---|---|
| [clean-code](../.agents/skills/clean-code) | Concise, direct coding standards — no over-engineering, no filler comments. |
| [improve-codebase-architecture](../.agents/skills/improve-codebase-architecture) | Find refactor/deepening opportunities informed by CONTEXT.md + ADRs. |
| [simplify-code](../.agents/skills/simplify-code) | Strip unnecessary abstractions, dead code, deep nesting. |

## Frontend

| Skill | Purpose |
|---|---|
| [nextjs-react-expert](../.agents/skills/nextjs-react-expert) | React/Next.js perf optimization (Vercel Eng patterns). |
| [frontend-design](../.agents/skills/frontend-design) | Web UI design thinking: layout, color, typography. |
| [game-development](../.agents/skills/game-development) | Orchestrator routing to Unity/Godot/Unreal/Phaser skills. |
| [mobile-design](../.agents/skills/mobile-design) | Mobile-first design for iOS/Android, React Native, Flutter. |
| [web-design-guidelines](../.agents/skills/web-design-guidelines) | Audit UI against Web Interface Guidelines / accessibility. |
| [webapp-testing](../.agents/skills/webapp-testing) | E2E testing principles, Playwright, deep app audits. |
| [taste-skill-v1](../.agents/skills/taste-skill-v1) | Legacy design-taste skill, kept for backward compat. |

## Backend

| Skill | Purpose |
|---|---|
| [nodejs-best-practices](../.agents/skills/nodejs-best-practices) | Node.js framework selection, async patterns, security, architecture. |
| [api-patterns](../.agents/skills/api-patterns) | REST vs GraphQL vs tRPC, response formats, versioning, pagination. |
| [database-design](../.agents/skills/database-design) | Schema design, indexing, ORM selection, serverless DBs. |

## Python

| Skill | Purpose |
|---|---|
| [python-patterns](../.agents/skills/python-patterns) | Framework selection, async patterns, type hints, project structure. |

## SEO

| Skill | Purpose |
|---|---|
| [seo-fundamentals](../.agents/skills/seo-fundamentals) | E-E-A-T, Core Web Vitals, Google algorithm principles. |

## Git & Code Review

| Skill | Purpose |
|---|---|
| [git-ops](../.agents/skills/git-ops) | Commit, push, PR creation, branch mgmt via natural language. Includes vuln scan + AI review gate. |
| [code-review](../.agents/skills/code-review) | Reviews diff/file/snippet, returns prioritized issue list. Framework-specific rules via `techs/`. |
| [git-review-pr](../.agents/skills/git-review-pr) | Reviews GitHub PR from URL, posts review comment. |
| [git-review-stage](../.agents/skills/git-review-stage) | Reviews staged git changes, returns prioritized issues. |
| [vulnerability-scanner](../.agents/skills/vulnerability-scanner) | OWASP 2025 vuln analysis, supply-chain security, attack-surface mapping. |

## Agent Personas (`.agents/agent/`)

Specialist personas invoked directly or by the orchestrator for domain-specific work.

| Agent | Purpose |
|---|---|
| [orchestrator](../.agents/agent/orchestrator.md) | Multi-agent coordination across security, backend, frontend, testing, DevOps for complex tasks. |
| [project-planner](../.agents/agent/project-planner.md) | Breaks requests into tasks, plans file structure, builds dependency graph, assigns agents. |
| [explorer-agent](../.agents/agent/explorer-agent.md) | Codebase discovery, architectural analysis, initial audits, refactor research. |
| [backend-specialist](../.agents/agent/backend-specialist.md) | Node.js/Python/serverless API dev, server-side logic, DB integration, auth. |
| [frontend-specialist](../.agents/agent/frontend-specialist.md) | React/Next.js components, styling, state management, responsive design. |
| [mobile-developer](../.agents/agent/mobile-developer.md) | React Native/Flutter cross-platform apps, native features. |
| [game-developer](../.agents/agent/game-developer.md) | Unity/Godot/Unreal/Phaser/Three.js games across PC/Web/Mobile/VR-AR. |
| [database-architect](../.agents/agent/database-architect.md) | Schema design, query optimization, migrations, serverless DBs. |
| [debugger](../.agents/agent/debugger.md) | Systematic debugging, root-cause analysis, crash/production issue investigation. |
| [code-archaeologist](../.agents/agent/code-archaeologist.md) | Reading legacy/undocumented code, reverse engineering, modernization planning. |
| [performance-optimizer](../.agents/agent/performance-optimizer.md) | Profiling, Core Web Vitals, bundle size, runtime perf. |
| [test-engineer](../.agents/agent/test-engineer.md) | Writing tests, TDD, coverage, debugging test failures. |
| [qa-automation-engineer](../.agents/agent/qa-automation-engineer.md) | E2E test infra: Playwright, Cypress, CI pipelines. |
| [security-auditor](../.agents/agent/security-auditor.md) | OWASP 2025, supply-chain security, zero-trust review. |
| [penetration-tester](../.agents/agent/penetration-tester.md) | Offensive security, red team simulations, exploit-finding. |
| [devops-engineer](../.agents/agent/devops-engineer.md) | Deployment, CI/CD, server access, rollback — high-risk production ops. |
| [seo-specialist](../.agents/agent/seo-specialist.md) | SEO/GEO audits, Core Web Vitals, E-E-A-T, AI-search visibility. |
| [product-manager](../.agents/agent/product-manager.md) | Requirements, user stories, acceptance criteria. |
| [product-owner](../.agents/agent/product-owner.md) | Roadmap, backlog prioritization, requirements elicitation, PRDs. |
| [documentation-writer](../.agents/agent/documentation-writer.md) | README/API docs/changelog — only on explicit request. |

## Workflows (`.agents/workflows/`)

Slash-command-style entry points that invoke skills/agents in a fixed sequence.

| Workflow | Purpose |
|---|---|
| [orchestrate](../.agents/workflows/orchestrate.md) | Coordinate multiple agents for multi-perspective/multi-domain tasks. |
| [coordinate](../.agents/workflows/coordinate.md) | Parallel dispatch + synthesis across specialist agents. |
| [plan](../.agents/workflows/plan.md) | Generate plan file via project-planner agent — no code writing. |
| [brainstorm](../.agents/workflows/brainstorm.md) | Structured option exploration before implementation. |
| [create](../.agents/workflows/create.md) | Kick off App Builder skill, start new-app dialogue. |
| [enhance](../.agents/workflows/enhance.md) | Add/update features in an existing app. |
| [debug](../.agents/workflows/debug.md) | Activate systematic debugging investigation. |
| [test](../.agents/workflows/test.md) | Generate and run tests. |
| [verify](../.agents/workflows/verify.md) | Prove changes work by executing them, not just inspecting. |
| [deploy](../.agents/workflows/deploy.md) | Pre-flight checks + production deployment. |
| [preview](../.agents/workflows/preview.md) | Start/stop/status local dev server. |
| [status](../.agents/workflows/status.md) | Show agent/project progress status board. |
| [remember](../.agents/workflows/remember.md) | Save preferences/conventions/decisions to persistent memory. |

---

**Count:** 43 skills, 19 agent personas, 13 workflows. Source of truth = frontmatter/content in each file under `.agents/`, not this table — re-run generation if contents change.

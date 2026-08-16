---
name: code-review
description: Reviews ANY given code (diff, file, or snippet) and returns a prioritized list of issues. Use when code is provided for review — whether from a diff file, a PR, or directly. Supports framework-specific rules via techs/ folder.
allowed-tools: Bash, Read
---

# Code Review

## Before Starting

1. **Activate caveman skill** — apply caveman mode (full) for all your own responses throughout this session.

2. **Load project context** — check if `.agent/rules/claude-mem-context.md` exists in the project root:
   ```bash
   cat .agent/rules/claude-mem-context.md 2>/dev/null
   ```
   If found, read it and apply project-specific rules. If not found, continue.

3. **Detect framework** — run these checks to identify the stack:
   ```bash
   # Check for framework signals
   cat package.json 2>/dev/null | grep -E '"next"|"@nestjs/core"|"express"|"fastify"|"react"'
   ls next.config.* nest-cli.json 2>/dev/null
   ```

   | Signal found | Framework | Load rules from |
   |---|---|---|
   | `next.config.*` or `"next"` in deps | **Next.js** | `techs/next.md` |
   | `nest-cli.json` or `"@nestjs/core"` | **NestJS** | `techs/nest.md` |
   | `"react"` in deps (no next) | **React** | `techs/react.md` |
   | `"express"` / `"fastify"` / plain node | **Node.js** | `techs/node.md` |

   Read the matched file from the skill's `techs/` folder and apply all its rules during review. If no framework is detected, skip this step.

---

## Step 1 — Load the Code to Review

Accept input in any of these forms:
- **Diff file**: Read `.ai-review-diff.txt` if it exists in the project root.
- **Explicit path**: Read the file or diff provided by the user.
- **Inline snippet**: Use the code pasted directly in the user's message.

If no code is found → tell user "No code provided for review." and stop.

---

## Step 2 — Review & Report

Analyze every changed file using:
- The **priority definitions** below
- The **framework rules** loaded in "Before Starting" step 3
- Any **project context** from `.agent/rules/claude-mem-context.md`

Output:

```
╔══════════════════════════════════════════════════════════╗
║   🤖  AI Code Review  —  <N> files changed               ║
║   Stack: <detected framework>                            ║
╚══════════════════════════════════════════════════════════╝

🔴 CRITICAL  (<N> issues)
────────────────────────────────────────────────
  ● <file>:<line>
    <What the issue is>
    💊 Fix: <Exact fix>

🟡 MAJOR  (<N> issues)
────────────────────────────────────────────────
  ● <file>:<line>
    <What the issue is>
    💊 Fix: <Exact fix>

🟢 MINOR  (<N> issues)
────────────────────────────────────────────────
  ● <file>
    <What the issue is>

💡 SUGGESTIONS  (<N>)
────────────────────────────────────────────────
  ● <file>
    <Suggestion>

──────────────────────────────────────────────────
📊 Summary: <N> critical, <N> major, <N> minor, <N> suggestions
✅ Review complete.
```

### Priority Definitions (Universal)

| Badge | Priority | Look for |
|---|---|---|
| 🔴 | CRITICAL | Hardcoded secrets, XSS, SQL injection, `eval()`, auth bypass, data loss |
| 🟡 | MAJOR | Logic bugs, unhandled errors, framework anti-patterns (from tech rules) |
| 🟢 | MINOR | Magic numbers, dead code, unused imports, naming issues |
| 💡 | SUGGESTIONS | Refactoring ideas, better patterns |

### Skip these files (noise)
`*.lock`, `*.min.js`, `*.min.css`, `dist/`, `build/`, `.next/`, `node_modules/`

If no issues found → output: `✅ All clear! No issues found.`

---

## Step 3 — Cleanup

After review is printed, delete the diff file if it was used:

```bash
rm -f .ai-review-diff.txt
```

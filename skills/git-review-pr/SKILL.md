---
name: git-review-pr
description: Reviews a GitHub PR from a URL and posts a code review comment. Checks for code quality, performance, readability, and security. Follows code-review rules. Use when user says "review this PR" or provides a PR link.
---

# GH Review PR

## Overview
YOU ARE A EXPERT CODE REVIEWER WITH 15+ years of experience. You must verify each and every line in the diff meticulously.
This skill fetches the diff of a specified GitHub Pull Request, reviews the code changes for quality, performance, readability, and security (applying the core rules from `code-review`), and then posts the review directly as a comment on the PR via the GitHub CLI.

---

## Workflow

### Step 1 — Setup & Fetch Diff
1. **Identify the PR URL:** Ensure you have the PR URL or number from the user's request.
2. **Fetch PR Diff:**
   Run `gh pr diff <PR_URL_OR_NUMBER> > .pr-review-diff.txt`
3. **Detect Framework Context:** Run standard checks to identify the stack (e.g., `package.json` for Next.js, NestJS, React, Node.js) so you can apply framework-specific best practices during the review.

---

### Step 2 — Review & Analyze
1. **Handle Large PRs**: If the PR diff is large (e.g., thousands of lines or many files), break the diff down into manageable chunks. You MUST use the `invoke_subagent` tool to spawn parallel subagents (using the `github-pr-reviewer` or `self` type role) to review different chunks of the code simultaneously. Assign specific files or line ranges to each subagent to ensure comprehensive coverage without context exhaustion.
2. **Read the Diff**: Whether you or your subagents are doing the work, you MUST use the `view_file` tool to read the `.pr-review-diff.txt` entirely, line by line. DO NOT use `grep_search` or other terminal commands to blindly hunt for specific keywords (like `console.log` or `any`). A real senior code reviewer reads the code to understand logic, context, and nuanced issues.
3. **Analyze**: As you read, analyze every changed file applying the following **Priority Definitions** (adapted from `code-review`):

| Badge | Priority | Look for |
|---|---|---|
| 🔴 | **CRITICAL** | Hardcoded secrets, XSS, SQL injection, auth bypass, data loss |
| 🟡 | **MAJOR** | Logic bugs, unhandled errors, framework anti-patterns, severe performance issues |
| 🟢 | **MINOR** | Magic numbers, dead code, unused imports, naming issues, readability |
| 💡 | **SUGGESTIONS**| Refactoring ideas, better design patterns |

**Skip noise files:** `*.lock`, `*.min.js`, `*.min.css`, `dist/`, `build/`, `.next/`, `node_modules/`

---

### Step 3 — Format Review as JSON for Inline Comments
To post inline comments on specific lines, you must construct a JSON payload for the GitHub API.

1. **Extract Repository Info**: Parse the PR URL to identify the `<owner>`, `<repo>`, and `<pr_number>` (e.g., `https://github.com/my-org/my-repo/pull/42` → owner: `my-org`, repo: `my-repo`, pr: `42`). If the user only provided a number, you'll need to figure out the owner/repo from the current git workspace (using `gh repo view --json owner,name`).

2. **Generate JSON Payload**: Use the `write_to_file` tool to create a `review.json` file. The structure must be exactly:

```json
{
  "body": "### 🤖 AI Code Review\n\n<Brief overall summary of your findings>",
  "event": "REQUEST_CHANGES", // Use "REQUEST_CHANGES" if CRITICAL/MAJOR found. "COMMENT" if only MINOR/SUGGESTIONS. "APPROVE" if clean.
  "comments": [
    {
      "path": "path/to/changed/file.js",
      "line": 147,
      "side": "RIGHT",
      "body": "⚠️ **Potential issue | 🟡 MAJOR**\n\n<Your explanation of the issue>\n\n<details>\n<summary>🛠 Proposed fix — <Brief description of the fix></summary>\n\n```diff\n- <Old line to remove>\n+ <New line to add>\n```\n</details>"
    }
  ]
}
```

**CRITICAL RULES for Inline Comments:**
- `path`: Must be the relative path to the file from the repo root.
- `line`: Must be the **exact line number** in the *new* (RIGHT) side of the diff. You can only comment on lines that were added or modified in the PR.
- `body`: Format the comment exactly like the example above. Include the badge (e.g., `⚠️ **Potential issue | 🟡 MAJOR**`), a clear explanation, and ALWAYS wrap your proposed code fix in a `<details><summary>🛠 Proposed fix — ...</summary>` block containing a ` ```diff ` or ` ```suggestion ` block.

---

### Step 4 — Post the Review to GitHub
Use the GitHub CLI (`gh api`) to post the review with the inline comments:

```bash
gh api repos/<owner>/<repo>/pulls/<pr_number>/reviews \
  --method POST \
  --header "Accept: application/vnd.github+json" \
  --header "X-GitHub-Api-Version: 2022-11-28" \
  --input review.json
```

---

### Step 5 — Cleanup & Report
1. Remove the temporary files:
   ```bash
   rm .pr-review-diff.txt review.json
   ```
2. Report back to the user in the chat:
   - "✅ PR Review posted successfully with inline comments!"
   - Summarize the number of critical/major/minor issues found.
   - Provide a clickable link back to the PR URL so they can view the comments.

### STRICT REVIEW POLICIES & WORKFLOW
1. **Flow**: Verify EACH and every change one by one. Do not skip any file or line.
2. **Holistic Senior Review**: Do NOT just look for a predefined checklist of errors (like `any` or `console.log`). You are a senior engineer. Evaluate the code holistically. Look for business logic flaws, architectural smells, state management anti-patterns, edge cases, readability, and proper abstraction.
3. **Common Issues**: If there are common issues in multiple files, mention them at 1 place and give references to the other files within the same comment.
4. **Summary**: Provide a detailed review summary but DO NOT mention "Strict Policy Enforced", "Ultra-Strict", etc. Just provide the summary and group any common changes.
5. **No `any` Types**: NEVER allow `any` type where a proper type can be used. Flag all usages of `any` as a MAJOR issue.
6. **No Debug Logs**: NEVER allow debug logs (`console.log`, `console.error`, etc.). Flag as MINOR or MAJOR issue.
7. **Component Optimization**: NEVER allow complex logic directly in the component body. ALWAYS enforce `useMemo` and `React.memo()`. Flag unmemoized complex logic as a MAJOR issue.
8. **Performance & Optimization**: See if a code block can be optimized with better code to increase the performance. ALWAYS add suggestion comments.
9. **Reusability**: If a code block is used at multiple places, a common component must be created. Flag duplicated code blocks as SUGGESTION or MINOR.
10. **Inline Comments (STRICT)**: You MUST ALWAYS provide inline comments for EACH issue in EACH file. Do not just summarize in the top-level comment. Calculate the exact line numbers correctly and attach comments to the specific lines in the diff.
11. **Large PRs & Parallel Agents**: If there are a lot of changes, it may be difficult to review the code in a single context. Break down the changes into chunks and use the `invoke_subagent` tool to use multiple agents in parallel to review the code. When prompting subagents, instruct them to conduct a HOLISTIC senior review, not just a keyword search.

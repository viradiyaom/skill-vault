---
name: git-review-stage
description: Reviews staged git changes and returns a prioritized list of issues. Use when user says "review my staged code", "review my changes", "check my code", or "ai-review".
allowed-tools: Bash, Read
---

# Git Review Stage

## Step 1 — Collect Staged Changes

Run and save staged diff to file:

```bash
git diff --cached --unified=5 > .ai-review-diff.txt
```

If `.ai-review-diff.txt` is empty → tell user "No staged changes found." and stop.

---

## Step 2 — Delegate to `code-review` Skill

Invoke the **`code-review`** skill to perform the full review.

- The `code-review` skill will read `.ai-review-diff.txt`, detect the framework, apply tech rules, and output the prioritized issue report.
- The `code-review` skill also handles cleanup of `.ai-review-diff.txt` after the review.

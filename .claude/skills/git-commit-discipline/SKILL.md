---
name: git-commit-discipline
description: Use when committing code changes, writing commit messages, or user requests a commit - ensures commits are based on actual changes (not conversation memory), messages are concise and describe why (not what), and prevents automatic future commits
---

# Git Commit Discipline

## Overview

Commits must be based on actual file changes, not conversation memory. Commit messages must be concise (max 100 chars) and explain WHY, not WHAT.

**Core principle:** Always review actual changes before committing. Never auto-commit in future messages.

## When to Use

Use when:

- User requests a commit (explicit: "commit this", "create a commit")
- Writing a commit message
- About to run `git commit`

Do NOT use for:

- Deciding when to commit during development (that's your judgment)
- Commit strategies (atomic commits, etc.)

## Core Pattern

### Before ANY Commit

```bash
# ALWAYS run these first
git status              # See all changes
git diff               # See unstaged changes
git diff --staged      # See staged changes
git log --oneline -5   # See recent commit style
```

### Analyze the Diff, Don't Trust Memory

**CRITICAL:** After running git diff, ANALYZE the changes to understand their PURPOSE.

❌ **WRONG** - Using conversation history:

```markdown
User: "I'm getting validation error for DISABLE_REQUEST_LOGS"
You: [fixes validation error with z.coerce.boolean()]
You: "I'll commit as 'Fix environment variable validation for boolean values'"
[focuses on the symptom, not the overall change]
```

✅ **CORRECT** - Analyzing actual changes:

```markdown
User: "I'm getting validation error for DISABLE_REQUEST_LOGS"
You: [fixes validation error]
You: [runs git diff, sees: new env var + middleware changes + logging removal]
You: "Looking at the diff, this adds a feature to disable request logging"
You: "Committing as 'Add option to disable request logging for cleaner dev output'"
[focuses on the PURPOSE of all changes together]
```

**Key questions to ask when analyzing the diff:**
1. What is the overall purpose of ALL these changes together?
2. What capability does this add or problem does it solve?
3. Why would a developer care about this?

**Don't just focus on:**
- The last thing you fixed
- The error message the user reported
- The technical detail you implemented

### Never Skip This

❌ **WRONG** - Committing based on conversation:

```markdown
User: "I just added OAuth"
You: "I'll commit with message 'Add OAuth authentication'"
[commits without checking files]
```

✅ **CORRECT** - Check actual changes:

```markdown
User: "I just added OAuth"
You: "Let me check what actually changed..."
[runs git status && git diff]
You: "I see OAuth plus 2 bug fixes. Committing as 'Add OAuth with session fix'"
```

## Commit Message Rules

### 1. Keep It Short and Simple

**Single-line commit messages.** No paragraphs, no long explanations.

```
✅ GOOD: "Fix auth middleware rejecting valid tokens"
✅ GOOD: "Add OAuth with Google provider"
❌ BAD:  "Fix authentication middleware that was incorrectly rejecting valid tokens in certain edge cases when..."
❌ BAD:  Multi-paragraph messages explaining business context
```

**Max 100 characters.** If you need more, the commit is probably too large.

### 2. Convey WHY, Not WHAT

The diff shows WHAT changed. The message explains WHY (in one concise line).

**CRITICAL: Focus on the user-facing PURPOSE, not implementation details.**

```
Code change: `if (token && token !== 'expired')`

❌ BAD (what):  "Add check for expired token"
✅ GOOD (why):  "Prevent validation of expired tokens"
✅ GOOD (why):  "Fix auth accepting expired tokens"
```

**More examples:**

```
Code change: Adding lastLoginAt and loginAttempts fields

❌ BAD (what):  "Add login tracking fields to users table"
✅ GOOD (why):  "Track user login activity for security monitoring"
✅ GOOD (why):  "Enable login history audit compliance"
```

```
Code change: Moving ESLint configs to packages/config

❌ BAD (what):  "Move ESLint configs to different package"
✅ GOOD (why):  "Consolidate linting configuration in single package"
✅ GOOD (why):  "Simplify ESLint config maintenance"
```

```
Code change: Added DISABLE_REQUEST_LOGS env var with z.coerce.boolean() + modified middleware

❌ BAD (implementation):  "Fix environment variable validation for boolean values"
✅ GOOD (purpose):  "Add option to disable request logging for cleaner dev output"
✅ GOOD (purpose):  "Allow disabling verbose request logs via environment variable"
```

**Ask yourself:** "Why would a developer care about this change?" not "What technical thing did I change?"

**Don't describe the implementation detail - describe the capability or problem solved.**

**Keep it to ONE LINE.** Don't add explanatory paragraphs.

### 3. Use Imperative Mood

```
✅ "Fix bug"  "Add feature"  "Update config"
❌ "Fixed"    "Added"        "Updates"
❌ "Fixes"    "Adds"         "Updating"
```

### 4. Include Required Attribution

```
Commit message format:

<descriptive message in imperative mood>

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

## Critical Rule: One-Time Action Only

```
User: "Commit these changes"
You: [review changes, create commit]
User: "Great! Now I added a new feature"
You: [DO NOT commit automatically]
```

**After committing once, NEVER commit again unless explicitly asked.**

## Common Mistakes

| Mistake                                             | Fix                                            |
| --------------------------------------------------- | ---------------------------------------------- |
| "I remember we worked on X, so I'll commit as X"    | Check git diff - memory is unreliable          |
| "User said they added X, so message is 'Add X'"     | Verify actual changes match user's description |
| "I'll write detailed message (200 chars)"           | Single line, max 100 chars, no paragraphs      |
| "Let me explain the business context in the commit" | Keep it to ONE concise line                    |
| "User finished feature, I'll commit it"             | Wait for explicit commit request               |
| "I committed earlier, so I'll commit this too"      | Each commit needs explicit request             |

## Quick Reference

| Step | Action                        | Why                               |
| ---- | ----------------------------- | --------------------------------- |
| 1    | `git status && git diff`      | See what actually changed         |
| 2    | Review all files              | Catch unexpected changes          |
| 3    | Write message (max 100 chars) | Concise, imperative, why not what |
| 4    | Add attribution               | Required by project               |
| 5    | Stop                          | Don't auto-commit next changes    |

## Red Flags

If you catch yourself thinking:

- "I remember we worked on X, so..."
- "User said X, so the message is X"
- "I'll be helpful and commit this too"
- "The message should be comprehensive and detailed"
- "Let me add a paragraph explaining the business context"
- "I'll write a multi-line message to be thorough"
- "Since I committed before, I'll commit again"
- "I'll describe the technical fix I made" (describe the PURPOSE instead)
- "The message should explain the implementation detail" (explain the CAPABILITY instead)

**STOP. These all violate commit discipline.**

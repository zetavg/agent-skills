---
name: git-commit-message-follow-project-convention
description: "Use when creating a git commit and no explicit commit message convention has been given by the user in the conversation, project instructions (AGENTS.md, CLAUDE.md, etc.), contributing docs, or commit templates/linters. Infers the convention from recent git history. Skip this skill if any of those already define the format. Triggers on: git commit, /commit, creating commits, or any task that results in a git commit."
---

# Git Commit Message — Follow Project Convention

Use this skill only when no other source explicitly defines the commit message convention. In that case, analyze the project's recent commit history to learn its conventions. Explicit project guidelines always take precedence over conventions inferred from history, and both take precedence over general-purpose commit message guidelines.

## Step 0: Check for Explicit Conventions (Important!)

Before anything else, check whether the commit message convention is already defined by any of these sources:

- The user's instructions in the current conversation
- Project instructions already in context (e.g., `AGENTS.md`, `CLAUDE.md`, or similar agent instruction files)
- Contributing or developer docs (e.g., `CONTRIBUTING.md`, `docs/`, `README.md` sections on commits)
- A commit template or linter config (e.g., `.gitmessage`, `commit.template`, `commitlint.config.*`, `.commitlintrc*`)

**If any of these explicitly defines the commit message format, follow it and stop here — do not continue to Step 1.** Do not override it with conventions inferred from history.

Only if none of them specify a convention, continue to Step 1.

If a `git-commit` skill is also available, refer to it for general commit workflow guidance (staging, safety checks, etc.) — but always override its message formatting rules with the conventions identified below. For example, if this project does not use conventional commits, you should consider not using it even if the `git-commit` skill recommends it.

## Step 1: Retrieve Recent Commits

Use a specialized tool if available. Otherwise, run:

```bash
git --no-pager log --oneline -30
```

## Step 2: Identify Conventions

Examine the retrieved commit messages and determine:

### 1. Commit Message Format

- **Conventional Commits**: Do messages follow the `type(scope): description` pattern (e.g., `feat: add login page`, `fix(auth): resolve token expiry`)?
- **Free-form**: Are messages plain sentences without a structured prefix?
- **Other format**: Is there a different structured pattern (e.g., ticket IDs like `[PROJ-123] Fix bug`, tagged formats like `[Feature] Add search`)?

### 2. Capitalization

- Is the first word of the description lowercase (`fix: add validation`) or uppercase (`fix: Add validation`)?
- If free-form, do messages start with a capital letter or not?
- Are there any other capitalization patterns (e.g., `GitHub` or `github`)?

### 3. Other Formatting

- **Scope usage**: If conventional commits are used, are scopes common or rare? What are the typical scopes?
  - Tip: run `git log -3000 --format='%s' | sed -nE 's/^(([a-z]+)?(\([^)]+\)|\[[^]]+\])?)!?:.*/\1/p' | grep . | sort | uniq -c | sort -rn | head -50` to see the most common types and scopes.
- **Ticket/issue references**: Are issue numbers or ticket IDs included? Where (prefix, suffix, footer)?
- **Tense and mood**: Are messages in imperative mood (`add feature`) or past tense (`added feature`)?
- **Usage of code fences**: "update README.md" or "update `README.md`", "fix bug in `process_data()`"?
- **Message length**: Are descriptions typically terse or verbose?
- **Punctuation**: Do messages end with a period or not?

## Step 3: Write the Commit Message

Apply the observed conventions to the new commit message. Match the project's style exactly.

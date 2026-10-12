---
name: git-commit-message-follow-project-convention
description: "Fallback for writing git commit messages. Do NOT load this skill if the commit convention is already specified by the user (including documents or skills they told you to use), by project instructions already in context (e.g., AGENTS.md, CLAUDE.md), or by a project-specific skill — follow those instead. Load it only when none of these apply, to infer the convention from recent git history."
---

# Git Commit Message — Follow Project Convention

This skill is a catch-all fallback. Use it only when no authoritative source defines the commit message convention. In that case, infer the convention from the project's recent commit history. Priority order: explicit instructions and project-specific sources > conventions inferred from history > general-purpose commit message guidelines.

## Step 0: Check for Explicit Conventions (Important!)

Before anything else, check whether the commit message convention is already defined by an authoritative source, including but not limited to:

1. **The user's explicit instructions in the current conversation**, including any document or skill the user told you to use. If it defines the commit message format, follow it. This also covers special cases the user states explicitly (e.g., a patch that will not go upstream).
2. **Project-specific sources**:
   - Project instructions (e.g., `AGENTS.md`, `CLAUDE.md`, or similar agent instruction files)
   - Contributing or developer docs (e.g., `CONTRIBUTING.md`, `docs/`, `README.md` sections on commits)
   - A commit template or linter config (e.g., `.gitmessage`, `commit.template`, `commitlint.config.*`, `.commitlintrc*`)
   - A skill specific to this project that defines the commit message format

**If an authoritative source explicitly defines the commit message format, follow it and stop here — do not continue to Step 1.** Do not override it with conventions inferred from history.

The following are **hints only**, not authoritative. Do not stop at them; continue to Step 1 and cross-check them against the commit history:

- **Memory** (user-specific or project-specific). A user's personal preference must not override a project's established convention, especially in a repository the user does not own. Project notes may be outdated. Follow a memory only if it records an explicit instruction (not an inferred habit) and it is corroborated by the commit history, or the user restates it in the current conversation. A timestamp, if present, can support that it is recent, but its absence means it is unverified. If a memory conflicts with the history, the history wins.
- **General-purpose skills** (e.g., a generic `git-commit` skill) that the user did not explicitly direct you to use. Refer to them for workflow guidance only (staging, safety checks, etc.). Do not adopt their message formatting rules; for example, if this project does not use conventional commits, do not use them even if such a skill recommends it. (A skill the user explicitly told you to use is authoritative; see item 1 above.)

Only if no authoritative source specifies a convention, continue to Step 1.

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

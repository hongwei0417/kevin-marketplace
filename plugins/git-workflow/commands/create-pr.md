---
allowed-tools: Bash(git:*), Bash(gh:*), Read, Grep, Glob
argument-hint: [title] | [--draft]
description: Create a new branch, commit changes, and submit a pull request
---

# Create Pull Request Command

Create a new branch, commit changes, and submit a pull request: $ARGUMENTS

## Current Repository State

- Current branch: !`git branch --show-current`
- Git status: !`git status --porcelain`
- Remote info: !`git remote -v`
- Recent commits: !`git log --oneline -5`

## Behavior

- Creates a new branch based on current changes
- Formats modified files using Biome
- Analyzes changes and automatically splits into logical commits when appropriate
- Each commit focuses on a single logical change or feature
- Creates descriptive commit messages for each logical unit
- Pushes branch to remote
- Creates pull request with proper summary and test plan

## Guidelines for Automatic Commit Splitting

- Split commits by feature, component, or concern
- Keep related file changes together in the same commit
- Separate refactoring from feature additions
- Ensure each commit can be understood independently
- Multiple unrelated changes should be split into separate commits

## Process

### 1. Analyze Current Changes

- Check for unstaged and staged changes
- Identify files that have been modified, added, or deleted
- Group related changes together

### 2. Create Feature Branch (if not already on one)

- Generate branch name from changes or provided title
- Follow branch naming conventions (feature/, fix/, etc.)
- Push branch to remote

### 3. Stage and Commit Changes

- Group related changes into logical commits
- Use conventional commit format with emoji
- Each commit should be atomic and focused

### 4. Create Pull Request

- Generate PR title from changes
- Create summary section with bullet points
- Add test plan section
- Set as draft if `--draft` flag is provided

## PR Format

```markdown
## Summary

- <1-3 bullet points describing the changes>

## Test plan

- [ ] <Checklist of testing steps>
```

## Example Usage

```bash
# Create PR with auto-generated title
/create-pr

# Create PR with custom title
/create-pr "Add user authentication feature"

# Create draft PR
/create-pr --draft

# Create draft PR with title
/create-pr "WIP: Refactor database layer" --draft
```

## Integration Tips

### With /commit Command

Use `/commit` first if you want more control over individual commits

### With /create-branch Command

Use `/create-branch` first if you want to set up a worktree

### With /code-review Command

After creating PR, use `/code-review` to self-review before requesting reviews

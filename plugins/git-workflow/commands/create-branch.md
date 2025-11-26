---
allowed-tools: Bash(git branch:*), Bash(git checkout:*), Bash(git worktree:*), Bash(git status:*), Bash(git diff:*), Bash(basename:*), Bash(pwd:*), AskUserQuestion
argument-hint: [description]
description: Create a new git branch from description, with optional worktree setup
---

# Create Git Branch from Description

Create a new git branch based on description: $ARGUMENTS

## Current Repository State

- Project directory: !`basename $(pwd)`
- Current branch: !`git branch --show-current`
- Git status: !`git status --short`
- Unstaged changes: !`git diff --stat`
- Staged changes: !`git diff --cached --stat`
- Existing branches: !`git branch --list`
- Existing worktrees: !`git worktree list`

## Task

Create a well-named git branch based on the provided description (or auto-generated from code changes) and optionally set up a worktree.

## Process

### 0. Determine Description Source

**If description is provided ($ARGUMENTS is not empty):**

- Use the provided description directly

**If no description provided ($ARGUMENTS is empty):**

- Analyze uncommitted changes using `git diff` and `git status`
- Review modified files and their changes
- Generate a description based on:
  - Type of changes (new features, bug fixes, refactoring)
  - Files affected
  - Nature of modifications
- Present the auto-generated description to the user for confirmation
- Examples:
  - Modified `src/auth/login.ts` with validation logic → "add login validation"
  - Fixed bug in `components/Header.tsx` → "fix header rendering issue"
  - Multiple test files changed → "update test suite"
  - Documentation updates → "update documentation"

### 1. Generate Branch Name

Analyze the description (provided or auto-generated) and create a branch name following these conventions:

**Branch Naming Patterns:**

- `feature/<name>` - For new features or enhancements
- `fix/<name>` - For bug fixes
- `refactor/<name>` - For code refactoring
- `docs/<name>` - For documentation changes
- `test/<name>` - For testing-related changes
- `chore/<name>` - For maintenance tasks
- `hotfix/<name>` - For urgent production fixes
- `experiment/<name>` - For experimental work

**Branch Name Guidelines:**

- Use lowercase letters and hyphens
- Keep it concise but descriptive (2-5 words)
- Use present tense verbs when applicable
- Examples:
  - "add user authentication" → `feature/add-user-auth`
  - "fix login bug" → `fix/login-bug`
  - "refactor database layer" → `refactor/database-layer`
  - "update API docs" → `docs/update-api`
  - "improve performance" → `feature/improve-performance`

### 2. Validate Branch Name

Before creating the branch:

- Check if a branch with the same name already exists
- Ensure the name follows conventions
- Verify no special characters that could cause issues
- Suggest alternative names if conflicts exist

### 3. Choose Creation Method

Use the AskUserQuestion tool to ask the user which approach they prefer:

**Option 1: Create Branch Only**

- Creates a new branch from the current branch
- Switches to the new branch
- Keeps working in the current directory
- Best for: Quick feature work, simple changes

**Option 2: Create Branch with Worktree**

- Creates a new branch
- Sets up a separate worktree directory
- Allows parallel work on multiple branches
- Best for: Long-running features, maintaining multiple versions

### 4. Execute Creation

**For Branch Only:**

```bash
git checkout -b <branch-name>
```

**For Branch with Worktree:**

```bash
# Determine worktree location using naming convention:
# [project-name].worktrees.[branch-name]
# Example: my-project.worktrees.feature/add-auth

# Get project directory name
PROJECT_NAME=$(basename $(pwd))

# Create worktree with proper naming (preserves branch name including slashes)
git worktree add ../${PROJECT_NAME}.worktrees.${BRANCH_NAME} -b ${BRANCH_NAME}
```

**Worktree Naming Convention:**

- Format: `<project-name>.worktrees.<branch-name>` (branch name preserved as-is)
- The worktree directory is created at the same level as the project directory
- Branch slashes create subdirectories automatically
- Example structure:
  ```
  parent-dir/
  ├── my-project/                      # Main repository
  └── my-project.worktrees.feature/
      ├── add-auth/                    # Worktree for feature/add-auth
      ├── dark-mode/                   # Worktree for feature/dark-mode
      └── user-profile/                # Worktree for feature/user-profile
  ├── my-project.worktrees.fix/
      ├── login-bug/                   # Worktree for fix/login-bug
      └── memory-leak/                 # Worktree for fix/memory-leak
  └── my-project.worktrees.refactor/
      └── api/                         # Worktree for refactor/api
  ```

### 5. Provide Next Steps

After creation, inform the user of:

- The created branch name
- Current location (for worktree: the new directory path)
- Suggested next steps
- How to switch between branches/worktrees

## Worktree Management

### Worktree Benefits

- Work on multiple features simultaneously
- Keep separate build artifacts per branch
- Avoid git stash/checkout cycle
- Independent node_modules/dependencies per worktree

### Worktree Directory Structure

```
parent-directory/
├── my-project/                          # Main repository
└── my-project.worktrees.feature/
    ├── add-auth/                        # Worktree for feature/add-auth
    └── dark-mode/                       # Worktree for feature/dark-mode
└── my-project.worktrees.fix/
    └── memory-leak/                     # Worktree for fix/memory-leak
└── my-project.worktrees.docs/
    └── update-api/                      # Worktree for docs/update-api
```

### Managing Worktrees

```bash
# List all worktrees
git worktree list

# Remove a worktree (use the full path)
git worktree remove ../<project-name>.worktrees.<branch-name>

# Prune deleted worktrees
git worktree prune

# Navigate to worktree
cd ../<project-name>.worktrees.<branch-name>
```

## Safety Checks

Before creating:

- Ensure working directory is clean (or stash changes if needed)
- Verify git repository is initialized
- Check for adequate disk space (for worktree option)
- Confirm base branch is up to date

## Examples

### Example 1: Feature Branch with Description

```
Input: "add dark mode toggle"
Generated branch: feature/add-dark-mode-toggle
Options: 1) Branch only, 2) Branch + Worktree
If worktree: Creates ../my-project.worktrees.feature/add-dark-mode-toggle/
```

### Example 2: Bug Fix with Description

```
Input: "fix memory leak in parser"
Generated branch: fix/memory-leak-parser
Options: 1) Branch only, 2) Branch + Worktree
If worktree: Creates ../my-project.worktrees.fix/memory-leak-parser/
```

### Example 3: Auto-generated from Code Changes

```
Input: (no description provided)
Detected changes: Modified src/components/Theme.tsx, added dark mode logic
Auto-generated: "add dark mode support"
Generated branch: feature/add-dark-mode-support
Options: 1) Branch only, 2) Branch + Worktree
If worktree: Creates ../my-project.worktrees.feature/add-dark-mode-support/
```

### Example 4: Auto-generated from Bug Fix

```
Input: (no description provided)
Detected changes: Fixed null pointer in src/parser/index.ts
Auto-generated: "fix parser null pointer"
Generated branch: fix/parser-null-pointer
Options: 1) Branch only, 2) Branch + Worktree
If worktree: Creates ../my-project.worktrees.fix/parser-null-pointer/
```

### Example 5: Documentation Update

```
Input: "update API documentation"
Generated branch: docs/update-api
Options: 1) Branch only, 2) Branch + Worktree
If worktree: Creates ../my-project.worktrees.docs/update-api/
```

## Best Practices

### When to Use Branch Only

- Quick fixes or small features
- Short-lived branches (< 1 day)
- Single focus work
- Limited context switching

### When to Use Worktree

- Long-running feature development
- Need to switch between features frequently
- Maintaining multiple versions simultaneously
- Want isolated build/dependency environments
- Working on features that require different Node versions

### Branch Hygiene

- Create branches from an up-to-date main/master
- Keep branch names descriptive but concise
- Delete branches after merging
- Regularly sync with upstream changes

## Troubleshooting

### Common Issues

**Branch name already exists:**

- Suggest variations or numbered suffixes
- Ask if user wants to checkout existing branch

**Working directory not clean:**

- Offer to stash changes
- Show unstaged changes
- Ask user to commit or discard

**Insufficient disk space (worktree):**

- Fall back to branch-only option
- Suggest cleanup of old worktrees

**Not in a git repository:**

- Provide clear error message
- Guide user to initialize git if needed

## Cleanup and Maintenance

### After Work is Complete

For branch only:

```bash
git checkout main
git branch -d <branch-name>
```

For worktree:

```bash
# Remove worktree from main repo
git worktree remove ../<project-name>.worktrees.<branch-name>

# Or navigate back and remove
cd ../<project-name>
git worktree remove ../<project-name>.worktrees.<branch-name>

# Delete branch
git branch -d <branch-name>
```

## Integration Tips

### With /commit Command

After creating a branch, use `/commit` to make structured commits

### With /create-pr Command

When ready, create a pull request from the new branch

### With /branch-cleanup Command

Regularly clean up merged branches and removed worktrees

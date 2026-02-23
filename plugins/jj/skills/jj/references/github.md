# GitHub Workflows with jj

## Setup

### Colocated Repository (Recommended)

When a repo was cloned with `git clone`, jj can be initialized on top:

```shell
jj git init --colocate
```

This lets `gh` CLI work normally since Git's `.git` directory is still present.

### Non-Colocated Repository

When using `jj git clone`, set `GIT_DIR` for the `gh` CLI:

```shell
export GIT_DIR="$(jj workspace root)/.jj/repo/store/git"
gh pr create
```

Or configure direnv in `.envrc`:

```
export GIT_DIR="$(jj workspace root)/.jj/repo/store/git"
```

## Creating a Pull Request

### Standard Workflow

```shell
# 1. Make changes (files auto-tracked)
# 2. Set a commit message
jj describe -m "feat: add new feature"

# 3. Create a named bookmark for the PR
jj bookmark create my-feature-branch

# 4. Push to remote
jj git push --bookmark my-feature-branch

# 5. Create the PR
gh pr create --title "feat: add new feature" --body "$(cat <<'EOF'
## Summary
- Description of changes

## Test plan
- [ ] Verify behavior X
- [ ] Check edge case Y

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

### Quick Workflow (Auto-generated bookmark name)

```shell
jj git push --change @    # Pushes with auto-generated bookmark like "push-abc123"
gh pr create
```

## Updating a PR

When reviewers request changes:

```shell
# Option 1: Amend the existing commit
jj describe -m "feat: add new feature (address review)"
# or just edit the files — jj auto-amends

# Push the updated bookmark (force push is safe in jj)
jj git push --bookmark my-feature-branch
```

### Adding a New Commit to the PR

```shell
jj new -m "fix: address review comments"
# make changes
jj git push --bookmark my-feature-branch
```

## Keeping Up to Date with Main

```shell
# Fetch latest changes
jj git fetch

# Rebase your change onto main
jj rebase -d main

# Push the rebased change (creates a force push)
jj git push --bookmark my-feature-branch
```

## Stack of PRs

jj excels at stacked PRs:

```shell
# Create a stack
jj new main -m "feat: base feature"
jj bookmark create base-feature

jj new -m "feat: extension of base"
jj bookmark create extended-feature

# Push all
jj git push --bookmark base-feature
jj git push --bookmark extended-feature

# Create PRs targeting each other
gh pr create --base main --head base-feature
gh pr create --base base-feature --head extended-feature
```

## Checking PR Status

```shell
gh pr status          # Show status of your PRs
gh pr list            # List open PRs
gh pr view <number>   # View a specific PR
gh pr checks          # Check CI status
```

## Merging and Cleanup

After PR is merged:

```shell
jj git fetch                     # Fetch the merge
jj bookmark delete my-feature    # Delete local bookmark
jj new main -m "next task"       # Start fresh from main
```

## Fork Workflow

```shell
# Add upstream remote
jj git remote add upstream https://github.com/original/repo.git

# Fetch from upstream
jj git fetch --remote upstream

# Rebase onto upstream/main
jj rebase -d upstream/main
```

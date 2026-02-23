---
name: jj
description: Guidance for using Jujutsu (jj) for version control. Use this skill when you would use git in order to make and edit commits properly, or when creating pull requests, pushing code, managing branches, or doing any version control operation. Also trigger when users mention "jujutsu", "commit", "branch", "PR", or any git-like workflow.
allowed-tools: Bash(jj *), Bash(gh *)
user-invocable: false
---

# Jujutsu (jj) Commit Guide

Use `jj` instead of `git` for ALL version control operations. Never use `git` commands directly.

## Making Commits

**No staging area** — files are automatically tracked. Just edit files and describe the change.

```shell
# Describe current change (set a commit message without finalizing)
jj describe -m "Add feature X"

# Finalize current change and start a new empty one
jj commit -m "Add feature X"

# Start a new change on top of current
jj new

# Start a new change with a message
jj new -m "Next task"
```

## Key Concepts

- `@` = working copy (current change)
- `@-` = parent of working copy
- Changes auto-amend — editing files automatically updates the current change
- Use `jj squash` to fold working copy changes into parent (like `git commit --amend`)
- Use `jj squash -i` for interactive selection of which changes to squash

## Viewing Status and History

```shell
jj st              # Show working copy status
jj diff            # Show working copy diff
jj log             # Show commit graph
jj show            # Show current change details
jj show @-         # Show parent change
```

## Branching with Bookmarks

jj uses "bookmarks" instead of "branches":

```shell
jj bookmark create feature-name        # Create bookmark at current change
jj bookmark create feature-name -r @   # Explicitly at working copy
jj bookmark list                        # List all bookmarks
jj bookmark move feature-name --to @   # Move bookmark to current change
jj bookmark delete feature-name        # Delete bookmark
```

## Syncing with Remote

```shell
jj git fetch                           # Fetch from remote (like git pull --fetch-only)
jj git fetch --remote origin           # Fetch from specific remote
jj rebase -d main                      # Rebase current change onto main
jj git push --bookmark feature-name   # Push a named bookmark
jj git push --change @                 # Push current change (auto-creates bookmark)
```

## Creating Pull Requests

For GitHub PRs, combine `jj git push` with the `gh` CLI:

```shell
# 1. Create a bookmark for the PR
jj bookmark create my-feature

# 2. Push the bookmark to remote
jj git push --bookmark my-feature

# 3. Create the PR
gh pr create --title "PR title" --body "$(cat <<'EOF'
## Summary
- Change 1
- Change 2

## Test plan
- [ ] Test step 1

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

Or use `--change @` to auto-generate a bookmark name:

```shell
jj git push --change @
gh pr create
```

## Quick Reference

| Git task                  | jj command                                     |
| ------------------------- | ---------------------------------------------- |
| `git status`              | `jj st`                                        |
| `git diff`                | `jj diff`                                      |
| `git log`                 | `jj log`                                       |
| `git add -A && git commit`| `jj commit -m "message"`                       |
| `git commit --amend`      | `jj squash` or `jj describe -m "new message"`  |
| `git push`                | `jj git push --bookmark <name>` or `--change @`|
| `git pull`                | `jj git fetch && jj rebase -d main`            |
| `git checkout -b branch`  | `jj new -m "task"` + `jj bookmark create name` |
| `git stash`               | `jj new` (changes stay in parent change)       |
| `git reset --hard`        | `jj abandon` or `jj restore`                  |
| `git rebase -i`           | `jj rebase -r @ -d <dest>` / `jj squash -i`   |

## Undoing Operations

```shell
jj undo            # Undo the last operation (very safe to use)
jj op log          # View operation history
jj op undo <id>    # Undo a specific operation
```

## References

Load these reference files for detailed guidance when needed:

- **`references/git-to-jj-commands.md`** — Comprehensive Git → jj command mapping
- **`references/github.md`** — GitHub PR workflows, fork setup, and `gh` CLI integration

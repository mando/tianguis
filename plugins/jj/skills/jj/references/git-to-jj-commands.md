# Git to Jujutsu Command Reference

Comprehensive mapping of Git commands to jj equivalents.

## Repository Management

| Git | jj |
| --- | -- |
| `git init` | `jj git init` |
| `git init` (no colocate) | `jj git init --no-colocate` |
| `git clone <url>` | `jj git clone <url>` |
| `git remote add origin <url>` | `jj git remote add origin <url>` |
| `git remote -v` | `jj git remote list` |

## Status and Inspection

| Git | jj |
| --- | -- |
| `git status` | `jj st` |
| `git diff` | `jj diff` |
| `git diff HEAD~1` | `jj diff -r @-` |
| `git diff <hash>` | `jj diff -r <revset>` |
| `git log` | `jj log` |
| `git log --oneline` | `jj log -T 'commit_id.short() ++ " " ++ description.first_line() ++ "\n"'` |
| `git show` | `jj show` |
| `git show <hash>` | `jj show <revset>` |

## Making Changes

| Git | jj |
| --- | -- |
| `git add -A && git commit -m "msg"` | `jj commit -m "msg"` |
| `git commit --amend -m "msg"` | `jj describe -m "msg"` |
| `git commit --amend` (add files) | `jj squash` (auto-amends working copy into parent) |
| `git commit --amend -i` | `jj squash -i` |
| `git reset HEAD <file>` | `jj restore --from @- --to @ <file>` |
| `git checkout -- <file>` | `jj restore <file>` |
| `git rm <file>` | `rm <file>` (jj auto-tracks deletions) |

## Branches / Bookmarks

jj uses "bookmarks" where Git uses "branches":

| Git | jj |
| --- | -- |
| `git branch` | `jj bookmark list` |
| `git branch feature` | `jj bookmark create feature` |
| `git branch feature <hash>` | `jj bookmark create feature -r <revset>` |
| `git checkout -b feature` | `jj new -m "task"` then `jj bookmark create feature` |
| `git checkout feature` | `jj edit feature` |
| `git branch -d feature` | `jj bookmark delete feature` |
| `git branch -m old new` | `jj bookmark rename old new` |
| `git branch -f feature HEAD` | `jj bookmark move feature --to @` |
| `git switch main` | `jj new main` (creates new change on top) |

## Syncing with Remote

| Git | jj |
| --- | -- |
| `git fetch` | `jj git fetch` |
| `git fetch origin` | `jj git fetch --remote origin` |
| `git pull` | `jj git fetch && jj rebase -d main` |
| `git push origin feature` | `jj git push --bookmark feature` |
| `git push -u origin feature` | `jj git push --bookmark feature` (tracking is automatic) |
| `git push --force` | `jj git push --bookmark feature --allow-new` |
| `git push` (current branch) | `jj git push --change @` |

## Rebasing and History

| Git | jj |
| --- | -- |
| `git rebase main` | `jj rebase -d main` |
| `git rebase -i HEAD~3` | `jj rebase -r @ -d @---` or use `jj squash -i` |
| `git cherry-pick <hash>` | `jj duplicate <revset> -d @` |
| `git revert <hash>` | `jj backout -r <revset>` |
| `git merge feature` | `jj rebase -s feature -d @` |
| `git stash` | `jj new` (old changes stay in previous change) |
| `git stash pop` | `jj edit @-` |

## Undoing

| Git | jj |
| --- | -- |
| `git reset --hard HEAD` | `jj restore` |
| `git reset --hard HEAD~1` | `jj abandon @` |
| `git reflog` | `jj op log` |
| `git reset --hard <hash>` | `jj op undo <op-id>` |
| undo last operation | `jj undo` |

## Conflict Resolution

| Git | jj |
| --- | -- |
| `git mergetool` | `jj resolve` (interactive) |
| Check for conflicts | `jj st` (conflicts shown in status) |
| `git add <file>` (mark resolved) | `jj resolve <file>` |

## Revsets (jj query syntax)

| Concept | Revset |
| ------- | ------ |
| Current working copy | `@` |
| Parent of working copy | `@-` |
| Grandparent | `@--` |
| All changes from main | `main..@` |
| All heads | `heads(all())` |
| Commits by author | `author("name")` |
| Commits with keyword | `description("keyword")` |

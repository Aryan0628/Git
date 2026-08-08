# 5. Stashing, Tagging & Undoing Changes

---

## 📦 Git Stash — Save Work Temporarily

Stash lets you **save uncommitted changes** (staged + unstaged) without committing them. Think of it as a clipboard for your working directory.

### When to Use Stash
- You need to switch branches but have uncommitted work.
- You want to pull remote changes without committing half-done work.
- You need a clean working directory temporarily.

```bash
# Basic stashing
git stash                          # Stash tracked changes (staged + modified)
git stash -u                       # Include untracked files
git stash -a                       # Include untracked + ignored files
git stash save "WIP: login form"   # With a descriptive message (deprecated, use push)
git stash push -m "WIP: login"     # Modern syntax with message

# Viewing stashes
git stash list                     # List all stashes
git stash show                     # Show summary of latest stash
git stash show -p                  # Show full diff of latest stash
git stash show stash@{2}           # Show a specific stash

# Applying stashes
git stash pop                      # Apply latest stash + remove from stash list
git stash apply                    # Apply latest stash (keep in stash list)
git stash apply stash@{1}          # Apply a specific stash
git stash pop stash@{2}            # Pop a specific stash

# Cleaning up
git stash drop                     # Delete latest stash
git stash drop stash@{1}           # Delete specific stash
git stash clear                    # Delete ALL stashes (⚠️ irreversible)

# Advanced
git stash branch <new-branch>      # Create branch from stash + apply it
```

### Stash Stack
```
stash@{0}  ← Most recent (top of stack)
stash@{1}
stash@{2}  ← Oldest
```

> `pop` = apply + drop (removes from stack)  
> `apply` = apply only (keeps in stack for reuse)

---

## 🏷️ Git Tags — Marking Important Points

Tags mark specific commits as important — typically used for **releases** (v1.0.0, v2.1.3).

### Lightweight vs Annotated Tags

| | Lightweight | Annotated |
|-|------------|-----------|
| Command | `git tag v1.0` | `git tag -a v1.0 -m "Release 1.0"` |
| Stored as | Just a pointer (like a branch that doesn't move) | Full Git object with tagger, date, message |
| Use for | Temporary/private markers | Official releases |

```bash
# Creating tags
git tag v1.0                                   # Lightweight
git tag -a v1.0 -m "First stable release"      # Annotated (recommended)
git tag -a v1.0 <commit-hash>                  # Tag a past commit

# Listing & viewing
git tag                            # List all tags
git tag -l "v1.*"                  # Filter tags
git show v1.0                      # Show tag details + commit

# Pushing tags (tags are NOT pushed by default!)
git push origin v1.0               # Push a specific tag
git push origin --tags             # Push all tags

# Deleting tags
git tag -d v1.0                    # Delete local tag
git push origin --delete v1.0      # Delete remote tag
```

---

## ↩️ Undoing Changes — The Big Picture

This is a **critical interview topic**. Different situations call for different commands:

```
Situation                          →  Command
──────────────────────────────────────────────────────
Discard changes in working dir     →  git restore <file>
Unstage a file                     →  git restore --staged <file>
Undo last commit (keep changes)    →  git reset --soft HEAD~1
Undo last commit (unstage changes) →  git reset HEAD~1 (or --mixed)
Undo last commit (discard all)     →  git reset --hard HEAD~1
Undo a pushed commit (safely)      →  git revert <commit>
Fix the last commit                →  git commit --amend
```

---

### `git restore` (Git 2.23+)
```bash
git restore <file>                 # Discard working directory changes
git restore --staged <file>        # Unstage (move from index to working dir)
git restore --source=HEAD~2 <file> # Restore file to how it was 2 commits ago
```

### `git reset` — Moving HEAD

```bash
git reset --soft HEAD~1            # Move HEAD back, keep changes STAGED
git reset --mixed HEAD~1           # Move HEAD back, keep changes UNSTAGED (default)
git reset --hard HEAD~1            # Move HEAD back, DISCARD all changes (⚠️ DANGEROUS)
git reset <file>                   # Unstage a file (same as git restore --staged)
```

#### Visual Comparison

```
                         Working Dir    Staging Area    Repository (HEAD)
                         ───────────    ────────────    ─────────────────
git reset --soft HEAD~1  Unchanged      Unchanged       Moved back ✓
git reset --mixed HEAD~1 Unchanged      Cleared ✓       Moved back ✓
git reset --hard HEAD~1  Cleared ✓      Cleared ✓       Moved back ✓
```

> 🔑 **Interview Tip**: "Soft keeps everything staged, mixed unstages but keeps files, hard discards everything. Never use `--hard` on shared history."

### `git revert` — Safe Undo for Pushed Commits

```bash
git revert <commit-hash>           # Create a NEW commit that undoes the given commit
git revert HEAD                    # Undo the most recent commit
git revert HEAD~3..HEAD            # Revert last 3 commits (creates 3 revert commits)
git revert -n <commit>             # Revert without auto-committing
```

**Key difference from reset:**
- `reset` **rewrites history** (removes commits) — dangerous for shared branches.
- `revert` **adds new history** (a new "undo" commit) — safe for shared branches.

---

## 🕐 Reflog — Your Safety Net

The **reflog** (reference log) records every time HEAD moves — even for commands like `reset --hard` that seem destructive.

```bash
git reflog                        # Show reflog
git reflog show main              # Reflog for a specific branch
```

Output:
```
3a1f2b4 HEAD@{0}: commit: feat: add dashboard
7c8d9e0 HEAD@{1}: reset: moving to HEAD~1
2b3c4d5 HEAD@{2}: commit: feat: add sidebar
```

### Recovering "Lost" Commits
```bash
# Accidentally did git reset --hard? Find the commit in reflog:
git reflog
# Find the SHA you want, then:
git reset --hard HEAD@{2}     # Go back to that point
# or
git checkout -b recovery HEAD@{2}   # Create a branch at that point
```

> 🔑 **Interview Tip**: "Even after a `git reset --hard`, commits aren't immediately deleted. They're in the reflog for ~90 days and can be recovered."

---

## 🧹 `git clean` — Remove Untracked Files

```bash
git clean -n                  # Dry run — show what would be deleted
git clean -f                  # Force delete untracked files
git clean -fd                 # Delete untracked files + directories
git clean -fX                 # Delete only ignored files
git clean -fx                 # Delete ignored + untracked
```

---

## ❓ Interview Questions

### Q1: What is the difference between `git reset` and `git revert`?
<details>
<summary>Show Answer</summary>

| | `git reset` | `git revert` |
|-|------------|-------------|
| **How** | Moves HEAD backward, removing commits | Creates a new commit that undoes changes |
| **History** | **Rewrites** history | **Preserves** history |
| **Safety** | Dangerous on shared branches | Safe on shared branches |
| **Use when** | Undoing local, unpushed commits | Undoing pushed/shared commits |

Rule of thumb: **If it's pushed, revert. If it's local, reset.**
</details>

### Q2: Explain `git reset --soft`, `--mixed`, and `--hard`.
<details>
<summary>Show Answer</summary>

All three move HEAD back by the specified number of commits. They differ in what happens to your changes:

- `--soft`: Changes stay **staged** (in the index). You can immediately re-commit.
- `--mixed` (default): Changes are **unstaged** but remain in the working directory. You need to `git add` again.
- `--hard`: Changes are **discarded entirely** from both staging and working directory. **Destructive.**

Example: You made a bad commit and want to redo it:
```bash
git reset --soft HEAD~1     # Undo commit, changes still staged
# Fix whatever you need
git commit -m "Better commit"
```
</details>

### Q3: You accidentally did `git reset --hard` and lost commits. How do you recover?
<details>
<summary>Show Answer</summary>

Use **`git reflog`** — it records every HEAD movement:

```bash
git reflog                          # Find the SHA of the commit before the reset
git reset --hard <sha>              # Reset back to that commit
# or
git checkout -b recovery <sha>      # Create a new branch at that commit
```

Reflog entries are kept for ~90 days by default, so you have time to recover. This is why Git rarely truly loses data.
</details>

### Q4: What is `git stash` and when would you use it?
<details>
<summary>Show Answer</summary>

`git stash` temporarily saves uncommitted changes (both staged and unstaged) and reverts your working directory to a clean state.

**Use cases:**
1. Need to switch branches but don't want to commit half-done work.
2. Need to pull remote changes on a dirty working directory.
3. Want to quickly test something on a clean state.

```bash
git stash                  # Save changes
# do other work...
git stash pop              # Restore changes
```

`pop` applies and removes from the stash stack; `apply` applies but keeps it in the stack.
</details>

### Q5: What is the difference between a lightweight and annotated tag?
<details>
<summary>Show Answer</summary>

- **Lightweight tag**: Just a pointer to a commit (like a branch that doesn't move). No extra metadata.
  ```bash
  git tag v1.0
  ```

- **Annotated tag**: A full Git object with tagger name, email, date, and a message. Can be signed with GPG.
  ```bash
  git tag -a v1.0 -m "Release 1.0"
  ```

**Best practice**: Use annotated tags for releases (more info, can be verified), lightweight for personal/temporary markers.
</details>

### Q6: How do you undo a commit that has already been pushed to a shared remote?
<details>
<summary>Show Answer</summary>

Use `git revert`, **not** `git reset`:

```bash
git revert <commit-hash>
git push origin main
```

`git revert` creates a **new commit** that undoes the changes, preserving history. This is safe because other developers who already pulled won't have their history disrupted.

Using `git reset` + `git push --force` on a shared branch would **rewrite history** and break other developers' local repos.
</details>

---

[← Previous: Remote Repos](../04-remote-repos/04-remote-repos.md) | [Back to README](../README.md) | [Next: Rebase & Cherry-Pick →](../06-rebase-cherry-pick/06-rebase-cherry-pick.md)

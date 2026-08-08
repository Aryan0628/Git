# 10. Interview Questions — Rapid-Fire Cheat Sheet 🔥

> 50 questions covering everything. Test yourself — try answering before expanding.

---

## 🟢 Basics (Questions 1–10)

### Q1: What is version control?
<details>
<summary>Answer</summary>

A system that records changes to files over time so you can recall specific versions later. It lets multiple people collaborate, tracks history, and enables reverting to previous states.
</details>

### Q2: What is the difference between Git and GitHub?
<details>
<summary>Answer</summary>

**Git** = distributed version control tool (local, CLI).  
**GitHub** = cloud platform for hosting Git repos + collaboration features (PRs, Issues, Actions).
</details>

### Q3: What does `git init` do?
<details>
<summary>Answer</summary>

Creates a new Git repository by initializing a `.git/` directory in the current folder. This directory contains all the metadata and object database for the repo.
</details>

### Q4: What is the staging area?
<details>
<summary>Answer</summary>

An intermediate area (also called the "index") between the working directory and the repository. `git add` moves changes to the staging area; `git commit` saves the staged snapshot to the repository. It lets you selectively choose what to include in each commit.
</details>

### Q5: What is the difference between `git add .` and `git add -A`?
<details>
<summary>Answer</summary>

`git add .` — stages changes in the **current directory** and below.  
`git add -A` — stages changes **across the entire repo**, regardless of current directory.  
In modern Git (2.x), both handle new, modified, and deleted files. The difference is scope.
</details>

### Q6: How do you check the status of your working directory?
<details>
<summary>Answer</summary>

`git status` — shows staged, unstaged, and untracked files.  
`git status -s` — short format.  
`git status -sb` — short + branch info.
</details>

### Q7: What is `.gitignore`?
<details>
<summary>Answer</summary>

A file listing patterns of files/directories Git should ignore (not track). Common entries: `node_modules/`, `.env`, `*.log`, `dist/`, `.DS_Store`.
</details>

### Q8: How do you see commit history?
<details>
<summary>Answer</summary>

`git log` — full history.  
`git log --oneline` — compact.  
`git log --oneline --graph --all` — visual graph of all branches.  
`git log -p` — with diffs.  
`git log --stat` — with file change summaries.
</details>

### Q9: What is a SHA-1 hash in Git?
<details>
<summary>Answer</summary>

A 40-character hexadecimal string that uniquely identifies every Git object (blob, tree, commit, tag). It's computed from the object's content, providing data integrity — any change produces a completely different hash.
</details>

### Q10: What does `git clone` do internally?
<details>
<summary>Answer</summary>

`git init` → `git remote add origin <url>` → `git fetch origin` → `git checkout main`. It creates a local repo, links the remote, downloads all data, and checks out the default branch.
</details>

---

## 🟡 Intermediate (Questions 11–25)

### Q11: What is a branch? How is it stored?
<details>
<summary>Answer</summary>

A branch is a **lightweight movable pointer** to a commit. Stored as a small file in `.git/refs/heads/<branch-name>` containing the SHA-1 of the commit it points to. Creating a branch is O(1) — just writing 41 bytes.
</details>

### Q12: What is HEAD?
<details>
<summary>Answer</summary>

A pointer to the current branch (or commit in detached HEAD state). Stored in `.git/HEAD`. Normally contains `ref: refs/heads/main`. When you commit, HEAD follows the branch to the new commit.
</details>

### Q13: What is a detached HEAD?
<details>
<summary>Answer</summary>

When HEAD points directly to a commit instead of a branch. Happens when you `git checkout <commit-hash>`. Commits made here are "orphaned" — they'll be garbage collected if you switch away without creating a branch.
</details>

### Q14: Fast-forward merge vs three-way merge?
<details>
<summary>Answer</summary>

**Fast-forward**: Target has no new commits → pointer just moves forward. No merge commit.  
**Three-way**: Both branches have new commits → creates a merge commit with two parents, using the common ancestor for comparison.
</details>

### Q15: How do you resolve a merge conflict?
<details>
<summary>Answer</summary>

1. Open conflicted files (look for `<<<<<<<`, `=======`, `>>>>>>>`).  
2. Choose or combine changes, remove markers.  
3. `git add <file>` to stage resolved version.  
4. `git commit` to complete the merge.  
Or: `git merge --abort` to cancel.
</details>

### Q16: `git fetch` vs `git pull`?
<details>
<summary>Answer</summary>

`fetch` = download from remote, update remote-tracking branches, **don't touch** working directory.  
`pull` = `fetch` + `merge` (or `rebase` with `--rebase`). Changes working directory.  
`fetch` is safer; `pull` is more convenient.
</details>

### Q17: What is `origin`?
<details>
<summary>Answer</summary>

The default name for the primary remote repository, set automatically by `git clone`. It's just a convention — you can rename it or add multiple remotes.
</details>

### Q18: `git reset --soft` vs `--mixed` vs `--hard`?
<details>
<summary>Answer</summary>

All move HEAD back.  
`--soft` → keeps changes **staged**.  
`--mixed` (default) → keeps changes **unstaged**.  
`--hard` → **discards** all changes (working dir + staging).
</details>

### Q19: `git reset` vs `git revert`?
<details>
<summary>Answer</summary>

`reset` = rewrites history (removes commits). For local/unpushed commits.  
`revert` = creates a new "undo" commit. Safe for shared/pushed commits.  
Rule: If pushed, revert. If local, reset.
</details>

### Q20: What is `git stash`?
<details>
<summary>Answer</summary>

Temporarily saves uncommitted changes and gives you a clean working directory. Use `stash pop` to restore. Common use: switching branches without committing half-done work.
</details>

### Q21: What is `git rebase`?
<details>
<summary>Answer</summary>

Replays your branch's commits on top of another branch, creating a linear history. Unlike merge (which creates a merge commit), rebase creates new commits with new SHAs.
</details>

### Q22: When should you NOT rebase?
<details>
<summary>Answer</summary>

Never rebase commits that have been **pushed to a shared branch**. Rebase creates new SHAs, which conflicts with other developers' copies of the original commits.
</details>

### Q23: What is interactive rebase?
<details>
<summary>Answer</summary>

`git rebase -i HEAD~N` lets you edit, reword, squash, fixup, reorder, or drop commits. Used to clean up commit history before creating a PR.
</details>

### Q24: What is `git cherry-pick`?
<details>
<summary>Answer</summary>

Copies a specific commit from one branch and applies it to the current branch as a new commit. Used for hotfixes, backporting, or selectively moving changes.
</details>

### Q25: What is the reflog?
<details>
<summary>Answer</summary>

A log of every time HEAD moved — including resets, rebases, and other operations that seem destructive. Acts as a safety net: even after `git reset --hard`, you can find and recover "lost" commits from the reflog for ~90 days.
</details>

---

## 🔴 Advanced (Questions 26–40)

### Q26: How does Git store data internally?
<details>
<summary>Answer</summary>

As a content-addressable filesystem with four object types:  
1. **Blob** — file contents  
2. **Tree** — directory (pointers to blobs/trees + filenames)  
3. **Commit** — snapshot (tree + parent + metadata)  
4. **Tag** — annotated tag (pointer + metadata)  

All stored in `.git/objects/`, identified by SHA-1 hashes. Forms a DAG (Directed Acyclic Graph).
</details>

### Q27: Does Git store diffs or snapshots?
<details>
<summary>Answer</summary>

**Snapshots** conceptually — each commit points to a complete tree of the project state. But Git uses **delta compression** in packfiles for efficient storage, so physically it does store diffs between similar objects. The data model is snapshots; the storage layer uses deltas.
</details>

### Q28: What are packfiles?
<details>
<summary>Answer</summary>

Compressed files in `.git/objects/pack/` that store multiple objects using delta compression. Git initially stores each object as a "loose" file. `git gc` packs them into packfiles for efficiency — huge storage savings for large repos.
</details>

### Q29: What is `git bisect`?
<details>
<summary>Answer</summary>

A binary search tool for finding the commit that introduced a bug:
```bash
git bisect start
git bisect bad                    # Current commit is bad
git bisect good <known-good-sha> # Mark a known good commit
# Git checks out the middle commit — you test it
git bisect good/bad              # Mark and continue
# Repeat until the culprit commit is found
git bisect reset                 # Return to normal
```
Can even be automated with a test script: `git bisect run ./test.sh`
</details>

### Q30: What are Git hooks?
<details>
<summary>Answer</summary>

Scripts in `.git/hooks/` that run automatically at certain Git events:

**Client-side:**
- `pre-commit` — before commit (lint, format)
- `commit-msg` — validate commit message
- `pre-push` — before push (run tests)

**Server-side:**
- `pre-receive` — before accepting push
- `post-receive` — after accepting push (deploy)

Tools like **Husky** make managing hooks easier in team projects.
</details>

### Q31: What is `git submodule`?
<details>
<summary>Answer</summary>

A way to include one Git repo inside another as a subdirectory, pinned to a specific commit. Used for shared libraries or dependencies.

```bash
git submodule add <url> <path>
git submodule update --init --recursive
```

The parent repo tracks the submodule's commit SHA, not its contents. Alternative: `git subtree` (simpler but merges history).
</details>

### Q32: What is `git worktree`?
<details>
<summary>Answer</summary>

Lets you check out multiple branches simultaneously in different directories, sharing the same `.git` database:

```bash
git worktree add ../hotfix-dir hotfix-branch
```

Useful when you need to work on a hotfix while keeping your feature branch intact, without stashing.
</details>

### Q33: Explain `~` vs `^` in Git references.
<details>
<summary>Answer</summary>

`~N` = go back N commits along the **first-parent** chain.  
`^N` = select the **Nth parent** (relevant for merge commits).  

`HEAD~2` = grandparent (first parent of first parent).  
`HEAD^2` = second parent of HEAD (the merged-in branch).
</details>

### Q34: What is `git filter-branch` / `git filter-repo`?
<details>
<summary>Answer</summary>

Tools for rewriting repository history at scale. Use cases:
- Remove sensitive data (passwords, keys) from all commits.
- Change author information across history.
- Remove large files from history.

`git filter-repo` (third-party) is the recommended modern replacement for `git filter-branch` (deprecated).
</details>

### Q35: What is a bare repository?
<details>
<summary>Answer</summary>

A repo without a working directory — just the `.git` contents. Created with `git init --bare` or `git clone --bare`. Used as **central/server repos** (like what GitHub hosts). You can't edit files directly in a bare repo; you can only push to and fetch from it.
</details>

### Q36: What is `git rerere`?
<details>
<summary>Answer</summary>

**Re**use **re**corded **re**solution. When enabled, Git remembers how you resolved a merge conflict and automatically applies the same resolution if it encounters the same conflict again.

```bash
git config --global rerere.enabled true
```
Useful when you're rebasing frequently and hitting the same conflicts.
</details>

### Q37: How does Git handle binary files?
<details>
<summary>Answer</summary>

Git stores binary files as blobs, but can't diff them meaningfully (it sees them as opaque data). Every change stores a new full copy. For large binary files, use **Git LFS (Large File Storage)** which stores pointers in Git and the actual files on a separate server.
</details>

### Q38: What is Git LFS?
<details>
<summary>Answer</summary>

**Large File Storage** — an extension that replaces large files (images, videos, datasets) with text pointers in Git, while storing the actual content on a remote server.

```bash
git lfs install
git lfs track "*.psd"
```

Benefits: Keeps the repo small, faster cloning, doesn't bloat history.
</details>

### Q39: What is `git blame -w -M -C`?
<details>
<summary>Answer</summary>

Advanced blame options:
- `-w` — ignore whitespace changes
- `-M` — detect moved lines within the file
- `-C` — detect lines moved from other files

This gives a more accurate picture of who *originally* wrote code, not just who last reformatted it.
</details>

### Q40: What is `ORIG_HEAD`?
<details>
<summary>Answer</summary>

A ref that stores the previous value of HEAD before a dangerous operation (merge, rebase, reset). Acts as a safety bookmark:

```bash
git reset --hard ORIG_HEAD    # Undo the last merge/rebase/reset
```
</details>

---

## 🔥 Scenario-Based Questions (Questions 41–50)

### Q41: You committed sensitive data (API key). How do you remove it from history?
<details>
<summary>Answer</summary>

1. **Immediately**: Rotate/revoke the exposed key.
2. Use `git filter-repo` to remove the file from all commits:
   ```bash
   git filter-repo --path secrets.env --invert-paths
   ```
3. Force push to remote: `git push --force-with-lease --all`
4. Tell all collaborators to re-clone (their local copies still have it).
5. Add the file to `.gitignore`.

Note: The key should be considered **compromised** regardless — anyone who cloned before the fix has it.
</details>

### Q42: Your `git push` is rejected because the remote has newer commits. What do you do?
<details>
<summary>Answer</summary>

```bash
# Option A: Pull and merge
git pull origin main
# Resolve any conflicts, then push
git push origin main

# Option B: Pull with rebase (cleaner)
git pull --rebase origin main
git push origin main
```

**Never** use `git push --force` on a shared branch just because push was rejected.
</details>

### Q43: You need to switch branches but have uncommitted changes. What do you do?
<details>
<summary>Answer</summary>

```bash
# Option A: Stash
git stash
git switch other-branch
# ... do work ...
git switch original-branch
git stash pop

# Option B: Commit as WIP
git commit -am "WIP: save progress"
git switch other-branch
# ... do work ...
git switch original-branch
git reset --soft HEAD~1   # Undo the WIP commit, keep changes staged
```
</details>

### Q44: You want to undo the last 3 commits but keep the changes. How?
<details>
<summary>Answer</summary>

```bash
git reset --soft HEAD~3    # Undo 3 commits, keep all changes staged
# Now you can recommit differently
git commit -m "Combined commit message"
```

If you want the changes unstaged: `git reset HEAD~3` (mixed, default).
</details>

### Q45: A teammate force-pushed and your local branch diverged. How do you fix it?
<details>
<summary>Answer</summary>

```bash
git fetch origin
git reset --hard origin/main    # Reset local to match remote
```

If you had local commits to save:
```bash
git fetch origin
git stash                        # Save local changes
git reset --hard origin/main     # Sync with remote
git stash pop                    # Reapply your changes
```

Or rebase your local commits on top of the new remote:
```bash
git fetch origin
git rebase origin/main
```
</details>

### Q46: How do you squash the last 4 commits into one?
<details>
<summary>Answer</summary>

```bash
# Option A: Interactive rebase
git rebase -i HEAD~4
# Change 'pick' to 'squash' or 'fixup' for commits 2-4
# Save and edit the combined message

# Option B: Soft reset
git reset --soft HEAD~4
git commit -m "New combined commit message"
```
</details>

### Q47: You accidentally committed to `main` instead of a feature branch. How do you move the commit?
<details>
<summary>Answer</summary>

```bash
# 1. Create a new branch at current position (saves your commit)
git branch feature/my-work

# 2. Reset main back to before your commit
git reset --hard HEAD~1

# 3. Switch to the feature branch
git checkout feature/my-work
# Your commit is now on the feature branch, not main
```
</details>

### Q48: How do you find which commit introduced a bug?
<details>
<summary>Answer</summary>

Use `git bisect` — binary search through commits:

```bash
git bisect start
git bisect bad                    # Current has the bug
git bisect good v1.0              # v1.0 was bug-free
# Git checks out middle commit — test it
git bisect good/bad               # Tell Git the result
# Repeat (log₂(N) steps to find the culprit)
git bisect reset                  # Done
```

Can be automated: `git bisect run npm test`
</details>

### Q49: How do you contribute to an open-source project on GitHub?
<details>
<summary>Answer</summary>

1. **Fork** the repo on GitHub.
2. **Clone** your fork locally.
3. Add **upstream** remote: `git remote add upstream <original-url>`.
4. Create a **feature branch**.
5. Make changes, commit with good messages.
6. **Push** to your fork.
7. Open a **Pull Request** from your fork to the original repo.
8. Respond to review feedback.
9. Keep your fork **synced**: `git fetch upstream && git merge upstream/main`.
</details>

### Q50: Your teammate asks you to review their PR. What do you look for?
<details>
<summary>Answer</summary>

1. **Does it do what it claims?** — Read the PR description, then the code.
2. **Logic errors** — Edge cases, null handling, off-by-one errors.
3. **Security** — SQL injection, XSS, exposed secrets.
4. **Performance** — N+1 queries, unnecessary loops, memory leaks.
5. **Readability** — Clear naming, appropriate comments, consistent style.
6. **Tests** — Are there tests? Do they cover edge cases?
7. **Architecture** — Does it fit the codebase patterns? Any unnecessary complexity?
8. **Breaking changes** — Does it affect other parts of the system?
</details>

---

## 📋 One-Line Cheat Sheet

```
git init / clone              → Start a repo
git add / commit              → Save changes
git branch / switch           → Manage branches
git merge / rebase            → Combine branches
git push / pull / fetch       → Sync with remote
git stash                     → Temporary save
git reset / revert            → Undo changes
git log / diff / blame        → Inspect history
git cherry-pick               → Copy a commit
git bisect                    → Find bug commit
git reflog                    → Safety net
```

---

**You've covered everything. Go crush that interview! 🚀**

[← Previous: Collaboration Workflows](../09-collaboration-workflows/09-collaboration-workflows.md) | [Back to README](../README.md)

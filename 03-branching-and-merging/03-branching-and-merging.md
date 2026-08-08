# 3. Branching & Merging

---

## 📌 Why Branches?

Branches let you **diverge** from the main line of development to work on features, fixes, or experiments in **isolation** — without affecting stable code.

> 🔑 **Key Insight**: In Git, a branch is just a **lightweight movable pointer** to a commit. Creating a branch is nearly instant and costs almost no storage.

---

## 🌿 Branch Basics

```bash
# List branches
git branch                  # Local branches (* marks current)
git branch -r               # Remote-tracking branches
git branch -a               # All branches (local + remote)
git branch -v               # With last commit info

# Create a branch
git branch <name>           # Create (don't switch)
git checkout -b <name>      # Create + switch (classic)
git switch -c <name>        # Create + switch (modern, Git 2.23+)

# Switch branches
git checkout <name>         # Classic
git switch <name>           # Modern

# Rename
git branch -m <old> <new>   # Rename a branch
git branch -m <new>         # Rename current branch

# Delete
git branch -d <name>        # Delete (safe — refuses if unmerged)
git branch -D <name>        # Force delete (even if unmerged)
```

---

## 🧠 How Branches Work Internally

```
                    main
                      │
                      ▼
  C1 ◄── C2 ◄── C3 ◄── C4
                  │
                  ▼
               feature
```

- Each branch is a file in `.git/refs/heads/` containing a **40-character SHA-1** hash.
- **HEAD** is a special pointer that tells Git which branch you're currently on.
- `HEAD → main → C4` means you're on `main` and the latest commit is C4.

```bash
cat .git/HEAD                     # ref: refs/heads/main
cat .git/refs/heads/main          # 3a1f2b4c... (SHA of latest commit)
```

---

## 🔀 Merging

Merging integrates changes from one branch into another.

```bash
# Merge feature into main
git checkout main           # Switch to receiving branch
git merge feature           # Merge feature into main
```

### Types of Merges

#### 1. Fast-Forward Merge
When there are **no new commits on the target branch** since the source branched off.

```
Before:
main:      C1 ── C2
                   \
feature:            C3 ── C4

After (fast-forward):
main:      C1 ── C2 ── C3 ── C4
                                ↑
                              main (just moved the pointer)
```

- **No merge commit** is created — Git simply moves the pointer forward.
- The history is **linear**.

```bash
git merge feature              # Fast-forward by default
git merge --no-ff feature      # Force a merge commit even if FF is possible
```

#### 2. Three-Way Merge (Recursive Merge)
When **both branches have new commits** — they've diverged.

```
Before:
main:      C1 ── C2 ── C5
                   \
feature:            C3 ── C4

After (three-way merge):
main:      C1 ── C2 ── C5 ── M (merge commit)
                   \         /
feature:            C3 ── C4
```

- Creates a **merge commit** (M) with **two parents**.
- Git uses the **common ancestor** (C2) + both branch tips to compute the merge.

> 🔑 **Interview Tip**: "A fast-forward merge moves the pointer; a three-way merge creates a new commit with two parents."

---

## ⚔️ Merge Conflicts

Conflicts happen when **both branches modify the same lines** in the same file.

### What a Conflict Looks Like
```
<<<<<<< HEAD
console.log("Hello from main");
=======
console.log("Hello from feature");
>>>>>>> feature
```

| Marker | Meaning |
|--------|---------|
| `<<<<<<< HEAD` | Start of current branch's changes |
| `=======` | Separator |
| `>>>>>>> feature` | End of incoming branch's changes |

### How to Resolve
1. Open the conflicted file
2. Choose which code to keep (or combine both)
3. Remove the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
4. Stage the resolved file: `git add <file>`
5. Commit: `git commit` (Git auto-generates merge commit message)

```bash
# Useful during conflicts
git status                  # Shows conflicted files
git diff                    # Shows conflict details
git merge --abort           # Cancel the merge entirely
git log --merge             # Show commits causing the conflict
```

---

## 🔁 `git merge --no-ff` vs `git merge --ff-only`

```bash
git merge --no-ff feature     # Always create a merge commit (even if FF possible)
git merge --ff-only feature   # Only merge if fast-forward is possible, else abort
```

| Flag | Behavior | Use Case |
|------|----------|----------|
| (default) | FF if possible, else merge commit | General use |
| `--no-ff` | Always merge commit | Want to preserve branch topology |
| `--ff-only` | Only allow FF, abort otherwise | CI/CD pipelines, clean history |

---

## 🌳 Visualizing Branches

```bash
git log --oneline --graph --all --decorate
```

Output example:
```
*   e5f6g7h (HEAD -> main) Merge branch 'feature'
|\
| * c3d4e5f (feature) feat: add search bar
| * a1b2c3d feat: add header component
|/
* 9z8y7x6 initial setup
```

---

## ❓ Interview Questions

### Q1: What is a branch in Git? How is it implemented internally?
<details>
<summary>Show Answer</summary>

A branch is a **lightweight, movable pointer** to a commit. Internally, it's just a file in `.git/refs/heads/` containing the SHA-1 hash of the commit it points to.

Creating a branch simply creates a 41-byte file (40 chars + newline). This is why branching in Git is extremely fast and cheap — unlike SVN where branches copy entire directories.

`HEAD` is a special ref that points to the current branch (or directly to a commit in detached HEAD state).
</details>

### Q2: What is the difference between a fast-forward merge and a three-way merge?
<details>
<summary>Show Answer</summary>

**Fast-Forward Merge:**
- Happens when the target branch has **no new commits** since the source branched off.
- Git simply **moves the branch pointer forward** — no merge commit is created.
- Results in a **linear** history.

**Three-Way Merge:**
- Happens when **both branches have diverged** (both have new commits).
- Git finds the **common ancestor**, compares both branch tips against it, and creates a **merge commit** with two parents.
- Preserves the **branching topology** in history.
</details>

### Q3: How do you resolve a merge conflict?
<details>
<summary>Show Answer</summary>

1. Run `git merge <branch>` — Git will report conflicts.
2. Open conflicted files — look for `<<<<<<<`, `=======`, `>>>>>>>` markers.
3. Decide which changes to keep (or combine both).
4. Remove all conflict markers.
5. Stage resolved files: `git add <resolved-file>`.
6. Complete the merge: `git commit`.

If you want to abort: `git merge --abort` restores the pre-merge state.
</details>

### Q4: What is a detached HEAD state?
<details>
<summary>Show Answer</summary>

Normally, `HEAD` points to a **branch**, which points to a commit:
```
HEAD → main → commit-abc123
```

In **detached HEAD** state, `HEAD` points directly to a **commit**, not a branch:
```
HEAD → commit-abc123
```

This happens when you:
- `git checkout <commit-hash>`
- `git checkout <tag>`
- During a rebase

Any commits made in this state are **not on any branch** and can be garbage-collected if you switch away without creating a branch. To save your work:
```bash
git branch <new-branch-name>     # Create a branch at current position
```
</details>

### Q5: What does `git merge --no-ff` do and why would you use it?
<details>
<summary>Show Answer</summary>

`--no-ff` (no fast-forward) forces Git to create a **merge commit** even when a fast-forward merge is possible.

**Why use it:**
- Preserves the **visual branch topology** in the history graph.
- Makes it clear that a group of commits came from a feature branch.
- Easy to **revert an entire feature** by reverting the single merge commit.
- Commonly enforced in team workflows (GitHub's "merge pull request" does this by default).

Without `--no-ff`, the history looks linear and you lose the context of which commits belonged to which feature.
</details>

### Q6: What is the difference between `git checkout` and `git switch`?
<details>
<summary>Show Answer</summary>

`git checkout` is an **overloaded** command — it does both:
- Switch branches: `git checkout main`
- Restore files: `git checkout -- file.txt`

Git 2.23 split these into two clearer commands:
- `git switch` — for switching/creating branches
- `git restore` — for restoring file contents

`git switch` is safer because it won't accidentally overwrite files. Both are still valid, but `git switch`/`git restore` are recommended for clarity.
</details>

---

[← Previous: Core Workflow](../02-core-workflow/02-core-workflow.md) | [Back to README](../README.md) | [Next: Remote Repos →](../04-remote-repos/04-remote-repos.md)

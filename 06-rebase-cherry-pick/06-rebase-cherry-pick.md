# 6. Rebasing, Cherry-Pick & Advanced Merging

---

## 🔄 Git Rebase — Rewriting History for Clean Commits

Rebase **replays** your branch's commits on top of another branch's tip, creating a **linear history**.

### Basic Rebase

```bash
# You're on 'feature', want to rebase onto 'main'
git checkout feature
git rebase main
```

**Before rebase:**
```
main:      C1 ── C2 ── C5
                   \
feature:            C3 ── C4
```

**After rebase:**
```
main:      C1 ── C2 ── C5
                          \
feature:                   C3' ── C4'    ← new commits (different SHAs!)
```

> **C3' and C4' are NEW commits** — same changes, but different SHAs because their parent changed. This is what "rewriting history" means.

---

## 🆚 Merge vs Rebase

| | Merge | Rebase |
|-|-------|--------|
| **History** | Non-linear (preserves branch topology) | Linear (clean, flat history) |
| **Creates** | Merge commit (with two parents) | Re-applied commits (new SHAs) |
| **Safety** | Never rewrites history | Rewrites history (new commits) |
| **When to use** | Shared branches, preserving context | Feature branches, cleaning up before merge |
| **Conflict resolution** | Resolve once | May need to resolve per commit |

### The Golden Rule of Rebasing

> ⚠️ **NEVER rebase commits that have been pushed to a shared/public branch.**

If other people have based work on those commits, rebasing creates **new commits with different SHAs**, making their history diverge from yours.

```
✅ Safe:   Rebase your LOCAL feature branch onto updated main
❌ Unsafe: Rebase main (or any shared branch) 
```

---

## 🔧 Interactive Rebase — Editing Commit History

This is **extremely powerful** and a common interview topic.

```bash
git rebase -i HEAD~4          # Interactively edit last 4 commits
git rebase -i main            # Interactively rebase onto main
```

This opens an editor with:
```
pick 1a2b3c4 feat: add header
pick 5e6f7g8 fix: header typo
pick 9h0i1j2 feat: add footer
pick 3k4l5m6 fix: footer color
```

### Available Commands
| Command | Effect |
|---------|--------|
| `pick` (p) | Keep the commit as-is |
| `reword` (r) | Keep commit, but edit the message |
| `edit` (e) | Pause at this commit for amendments |
| `squash` (s) | Combine with previous commit, edit combined message |
| `fixup` (f) | Combine with previous commit, discard this commit's message |
| `drop` (d) | Remove the commit entirely |
| `reorder` | Change the order of lines to reorder commits |

### Common Use Cases

#### Squash multiple commits into one:
```
pick 1a2b3c4 feat: add header
squash 5e6f7g8 fix: header typo        ← squash into previous
pick 9h0i1j2 feat: add footer
fixup 3k4l5m6 fix: footer color         ← merge into previous, discard message
```

Result: 4 commits → 2 clean commits.

#### Reword a commit message:
```
pick 1a2b3c4 feat: add header
reword 5e6f7g8 fix: haeder typo        ← will prompt you to edit this message
pick 9h0i1j2 feat: add footer
```

#### Drop a commit:
```
pick 1a2b3c4 feat: add header
drop 5e6f7g8 debug: temp logging       ← remove entirely
pick 9h0i1j2 feat: add footer
```

> 🔑 **Interview Tip**: "I use interactive rebase to clean up my feature branch before creating a PR — squashing fix-up commits, rewriting unclear messages, and ensuring each commit is atomic and meaningful."

---

## 🍒 Cherry-Pick — Copy Specific Commits

Cherry-pick **copies** a commit from one branch and applies it to another.

```bash
git cherry-pick <commit-hash>              # Apply a single commit
git cherry-pick <hash1> <hash2>            # Apply multiple commits
git cherry-pick <hash1>..<hash3>           # Apply a range (exclusive start)
git cherry-pick <hash1>^..<hash3>          # Apply a range (inclusive start)
git cherry-pick --no-commit <hash>         # Apply changes without committing
```

### When to Use Cherry-Pick
- **Hotfix**: A bug fix is on `develop` but you need it on `main` right now.
- **Selective features**: You want one commit from a branch, not the whole branch.
- **Backporting**: Apply a fix to an older release branch.

```
main:      C1 ── C2 ── C5 ── C3'    ← cherry-picked!
                   \
feature:            C3 ── C4
                    ↑
              (this specific commit was cherry-picked to main)
```

> **Note**: Cherry-pick creates a **new commit** (C3') with a different SHA. The original commit (C3) remains on the source branch.

### Handling Conflicts During Cherry-Pick
```bash
# If a conflict occurs:
# 1. Resolve conflicts in the files
# 2. Stage resolved files
git add <resolved-file>
# 3. Continue
git cherry-pick --continue

# Or abort
git cherry-pick --abort
```

---

## 🔀 Advanced Merge Strategies

### Merge Strategies
```bash
git merge -s recursive feature      # Default for two branches (3-way merge)
git merge -s ours feature           # Keep our version entirely (discard theirs)
git merge -s octopus b1 b2 b3       # Merge 3+ branches at once
```

### Merge Strategy Options
```bash
git merge -X theirs feature         # On conflict, prefer THEIR changes
git merge -X ours feature           # On conflict, prefer OUR changes
git merge -X patience feature       # Better diff algorithm for complex merges
```

> **`-s ours`** vs **`-X ours`**:
> - `-s ours` = ignore the other branch entirely (take nothing from it)
> - `-X ours` = merge normally, but when there's a conflict, prefer our side

---

## 🔃 Rebase with Conflict Resolution

```bash
# During rebase, if conflicts occur:
# 1. Fix conflicts in files
# 2. Stage resolved files
git add <file>
# 3. Continue rebase
git rebase --continue

# Other options:
git rebase --skip              # Skip the current conflicting commit
git rebase --abort             # Cancel the rebase entirely
```

---

## ❓ Interview Questions

### Q1: What is `git rebase` and how does it differ from `git merge`?
<details>
<summary>Show Answer</summary>

**Merge** combines two branches by creating a **merge commit** with two parents. It preserves the exact history of both branches.

**Rebase** takes the commits from your branch and **replays** them on top of another branch. It creates new commits with new SHAs, resulting in a **linear history**.

```
Merge result:     A ── B ── M (merge commit)
                       \   /
                        C ── D

Rebase result:    A ── B ── C' ── D' (linear, no merge commit)
```

Key trade-off: **Merge preserves history truthfully; rebase creates a cleaner but rewritten history.**
</details>

### Q2: What is the "golden rule" of rebasing?
<details>
<summary>Show Answer</summary>

**Never rebase commits that have been pushed to a shared/public branch.**

When you rebase, Git creates **new commits** with different SHAs. If others have based their work on the original commits, their history will diverge from yours, causing chaos.

Safe: Rebase your **local, unpushed** feature branch onto `main`.
Unsafe: Rebase `main` itself or any branch others are working on.
</details>

### Q3: Explain interactive rebase. What operations can you perform?
<details>
<summary>Show Answer</summary>

Interactive rebase (`git rebase -i`) lets you edit, reorder, squash, or drop commits in your history.

Commands:
- **pick**: Keep commit as-is
- **reword**: Edit commit message
- **edit**: Pause to amend the commit
- **squash**: Merge into previous commit (combine messages)
- **fixup**: Merge into previous commit (discard this message)
- **drop**: Remove the commit

Use case: Before creating a PR, you might squash "fix typo" commits into the parent feature commit, resulting in a clean, meaningful history.
</details>

### Q4: What is `git cherry-pick`? When would you use it?
<details>
<summary>Show Answer</summary>

`git cherry-pick <commit>` takes a specific commit from one branch and applies it to your current branch as a **new commit**.

Use cases:
1. **Hotfix**: Apply a critical fix from `develop` to `main` without merging everything.
2. **Backporting**: Apply a fix to an older release branch.
3. **Selective features**: Take only specific commits from a feature branch.

It creates a new commit with a different SHA. The original commit remains unchanged on its source branch.
</details>

### Q5: You have 5 commits on your feature branch, but 2 of them are "fix typo" commits. How do you clean this up before creating a PR?
<details>
<summary>Show Answer</summary>

Use **interactive rebase**:

```bash
git rebase -i HEAD~5
```

In the editor, change `pick` to `fixup` (or `squash`) for the typo-fix commits:

```
pick abc1234 feat: add user profile page
fixup def5678 fix: typo in profile         ← merge into previous, discard msg
pick ghi9012 feat: add avatar upload
pick jkl3456 feat: add profile settings
fixup mno7890 fix: settings button color   ← merge into previous, discard msg
```

Result: 5 commits → 3 clean, meaningful commits.

`squash` lets you edit the combined message; `fixup` just uses the previous commit's message.
</details>

### Q6: What happens if you rebase and encounter conflicts?
<details>
<summary>Show Answer</summary>

During rebase, Git replays each commit one by one. If a commit conflicts with the target branch:

1. Git **pauses** and marks the conflicted files.
2. You **resolve** the conflicts manually.
3. `git add <resolved-files>`
4. `git rebase --continue` to proceed to the next commit.

You may need to resolve conflicts **multiple times** (once per conflicting commit), unlike merge where you resolve once. You can also:
- `git rebase --skip` to skip the current commit
- `git rebase --abort` to cancel and return to the original state
</details>

---

[← Previous: Stash, Tag & Undo](../05-stash-tag-undo/05-stash-tag-undo.md) | [Back to README](../README.md) | [Next: Git Internals →](../07-git-internals/07-git-internals.md)

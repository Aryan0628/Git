# 2. Core Workflow — add, commit, status, log

---

## 📌 The Basic Git Cycle

```
Edit files → Stage changes → Commit → Repeat
```

This is what you'll do hundreds of times. Master these commands and you'll handle 80% of daily Git usage.

---

## 🔍 Checking Status

```bash
git status              # Full status — shows staged, modified, untracked
git status -s           # Short format
git status -sb          # Short + branch info
```

### Short Status Flags
```
 M  file.txt      # Modified (not staged)
M   file.txt      # Modified (staged)
MM  file.txt      # Modified (staged) AND modified again (unstaged)
A   file.txt      # New file (staged)
??  file.txt      # Untracked
D   file.txt      # Deleted
R   file.txt      # Renamed
```

> The **left column** = staging area status, **right column** = working directory status.

---

## ➕ Staging Changes — `git add`

```bash
git add <file>              # Stage a specific file
git add file1.txt file2.txt # Stage multiple files
git add .                   # Stage ALL changes in current directory (recursively)
git add -A                  # Stage ALL changes in entire repo
git add *.js                # Stage by pattern
git add -p                  # Interactive staging — pick chunks (hunks)
```

### `git add -p` (Patch Mode) — Interview Favorite!

This lets you stage **specific lines/hunks** within a file, not the whole file.

```
Stage this hunk [y,n,q,a,d,s,e,?]?
  y  →  Stage this hunk
  n  →  Skip this hunk
  s  →  Split into smaller hunks
  e  →  Manually edit the hunk
  q  →  Quit (don't stage remaining)
```

> 🔑 **Why it matters**: It lets you create **atomic commits** — each commit does one logical thing, even if you made multiple changes in a file.

---

## 💾 Committing — `git commit`

```bash
git commit -m "Add login validation"       # Commit with inline message
git commit                                  # Opens editor for message
git commit -am "Fix typo"                   # Add tracked files + commit (skips staging for TRACKED files)
git commit --amend -m "Better message"      # Rewrite the LAST commit message
git commit --amend --no-edit                # Add staged changes to last commit, keep message
```

### Writing Good Commit Messages

```
<type>: <short summary in imperative mood>    ← subject line (≤ 50 chars)
                                               ← blank line
<optional body — explain WHY, not WHAT>        ← body (wrap at 72 chars)
```

**Conventional commit types:**
| Type | Usage |
|------|-------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting (no code change) |
| `refactor` | Code restructuring (no feature/fix) |
| `test` | Adding tests |
| `chore` | Build, CI, tooling changes |

**Examples:**
```
feat: add user authentication with JWT
fix: resolve null pointer in payment module
docs: update API endpoint documentation
refactor: extract validation logic into helper
```

> 🔑 **Interview Tip**: Mention that you follow conventional commits or write messages in the **imperative mood** ("Add feature" not "Added feature"). This shows professional discipline.

---

## 📊 Viewing History — `git log`

```bash
git log                             # Full log
git log --oneline                   # Compact — one line per commit
git log --oneline --graph           # ASCII graph of branches
git log --oneline --graph --all     # All branches
git log -n 5                        # Last 5 commits
git log --author="Aryan"            # Filter by author
git log --since="2024-01-01"        # Filter by date
git log --grep="fix"                # Search commit messages
git log -- path/to/file             # History of a specific file
git log -p                          # Show diffs in each commit
git log --stat                      # Show files changed + insertions/deletions
```

### Understanding `git log --oneline --graph`
```
* 3a1f2b4 (HEAD -> main) feat: add dashboard
* 7c8d9e0 fix: login redirect
| * 2b3c4d5 (feature/auth) feat: OAuth integration
|/
* 1a2b3c4 initial commit
```
- `*` = a commit
- `|` = branch continues
- `/` or `\` = branch merges or diverges
- `(HEAD -> main)` = current branch and position

---

## 📎 Viewing Differences — `git diff`

```bash
git diff                    # Working directory vs Staging area (unstaged changes)
git diff --staged           # Staging area vs Last commit (what will be committed)
git diff HEAD               # Working directory vs Last commit
git diff branch1..branch2   # Compare two branches
git diff commit1..commit2   # Compare two commits
git diff --stat             # Summary only (files + lines changed)
git diff -- <file>          # Diff for a specific file
```

### Visual Summary
```
Working Dir ──git diff──► Staging Area ──git diff --staged──► Last Commit (HEAD)
       │                                                          │
       └───────────────── git diff HEAD ──────────────────────────┘
```

---

## 🏷️ Other Useful Commands

```bash
git show <commit>           # Show a specific commit's changes
git show HEAD               # Show the latest commit
git show HEAD~2             # Show 2 commits ago
git blame <file>            # Show who changed each line (and when)
git shortlog -sn            # Summary of commits per author
```

### `git blame` Output
```
^3a1f2b4 (Aryan Gupta 2024-03-15 10:30:00 +0530 1) function login() {
7c8d9e0a (Aryan Gupta 2024-03-16 14:20:00 +0530 2)   validate(input);
2b3c4d5f (Aryan Gupta 2024-03-17 09:10:00 +0530 3)   return token;
```

> Useful for understanding when and why a line was changed.

---

## ❓ Interview Questions

### Q1: What is the difference between `git add .` and `git add -A`?
<details>
<summary>Show Answer</summary>

- `git add .` stages new and modified files **in the current directory and subdirectories**.
- `git add -A` (or `git add --all`) stages new, modified, **and deleted** files **across the entire repository**, regardless of your current directory.

In modern Git (2.x+), `git add .` also stages deletions within the current directory, so the practical difference is mainly about **scope** (current dir vs entire repo).
</details>

### Q2: What does `git commit -am "msg"` do? Can it add untracked files?
<details>
<summary>Show Answer</summary>

`git commit -am "msg"` combines `git add` and `git commit` in one step. The `-a` flag automatically stages all **modified and deleted tracked files**, then commits them.

**No, it cannot add untracked (new) files.** You must explicitly `git add <newfile>` first. The `-a` flag only works on files Git already knows about.
</details>

### Q3: How would you rewrite the last commit message?
<details>
<summary>Show Answer</summary>

```bash
git commit --amend -m "New corrected message"
```

This replaces the previous commit with a new one (new SHA). **Never amend commits that have been pushed** to a shared remote, as it rewrites history.

To add forgotten files to the last commit without changing the message:
```bash
git add forgotten-file.txt
git commit --amend --no-edit
```
</details>

### Q4: Explain what `git diff`, `git diff --staged`, and `git diff HEAD` show.
<details>
<summary>Show Answer</summary>

- `git diff` — Shows changes in your **working directory** that are **not yet staged**. (Working Dir vs Index)
- `git diff --staged` (or `--cached`) — Shows changes that are **staged but not yet committed**. (Index vs HEAD)
- `git diff HEAD` — Shows **all changes** since the last commit, whether staged or not. (Working Dir vs HEAD)
</details>

### Q5: What is `git blame` and when would you use it?
<details>
<summary>Show Answer</summary>

`git blame <file>` annotates each line of a file with:
- The **commit hash** that last modified it
- The **author**
- The **timestamp**

Use cases:
- **Debugging**: Find who introduced a bug and when.
- **Understanding context**: See why a line was written by checking the associated commit.
- **Code review**: Understand the evolution of a file.

It's not for "blaming" someone — it's for understanding code history.
</details>

### Q6: What makes a good commit message?
<details>
<summary>Show Answer</summary>

A good commit message:
1. Uses **imperative mood** in the subject: "Add feature" not "Added feature"
2. Subject line is **≤ 50 characters**
3. **Separates subject from body** with a blank line
4. Body explains **why** the change was made, not what (the diff shows what)
5. Wraps body at **72 characters**
6. Optionally uses **conventional commit types** (`feat:`, `fix:`, `docs:`, etc.)

Example:
```
fix: prevent crash when user input is empty

The application crashed with a NullPointerException when the user
submitted the form without filling the name field. Added null check
and user-friendly error message.
```
</details>

---

[← Previous: Git Basics](../01-git-basics/01-git-basics.md) | [Back to README](../README.md) | [Next: Branching & Merging →](../03-branching-and-merging/03-branching-and-merging.md)

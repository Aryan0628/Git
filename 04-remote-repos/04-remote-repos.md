# 4. Remote Repositories — push, pull, fetch

---

## 📌 What Are Remotes?

A **remote** is a reference to another copy of the repository, typically hosted on a server (GitHub, GitLab, Bitbucket).

- **`origin`** is the conventional name for your primary remote (set automatically by `git clone`).
- You can have **multiple remotes** (e.g., `origin` for your fork, `upstream` for the original repo).

```bash
# Managing remotes
git remote                         # List remote names
git remote -v                      # List with URLs (fetch + push)
git remote add <name> <url>        # Add a remote
git remote remove <name>           # Remove a remote
git remote rename <old> <new>      # Rename a remote
git remote set-url origin <url>    # Change URL of existing remote
git remote show origin             # Detailed info about a remote
```

---

## 📡 Fetch vs Pull vs Push

### `git fetch` — Download, Don't Merge
```bash
git fetch                    # Fetch all remotes
git fetch origin             # Fetch from 'origin'
git fetch origin main        # Fetch only 'main' from 'origin'
```

- Downloads new commits, branches, and tags from the remote.
- Updates **remote-tracking branches** (`origin/main`, `origin/feature`).
- Does **NOT** modify your working directory or local branches.
- **Safe** — never causes conflicts.

### `git pull` — Fetch + Merge
```bash
git pull                     # Fetch + merge current tracking branch
git pull origin main         # Fetch origin/main + merge into current branch
git pull --rebase            # Fetch + rebase instead of merge
```

- `git pull` = `git fetch` + `git merge`
- `git pull --rebase` = `git fetch` + `git rebase`
- **Can cause conflicts** if remote and local have diverged.

### `git push` — Upload Local Commits
```bash
git push                           # Push current branch to its upstream
git push origin main               # Push local 'main' to origin
git push -u origin main            # Push + set 'origin/main' as upstream
git push origin --delete feature   # Delete a remote branch
git push --tags                    # Push all tags
git push --force                   # Force push (⚠️ DANGEROUS — rewrites remote history)
git push --force-with-lease        # Safer force push (fails if remote has new commits you don't have)
```

---

## 🗺️ Understanding Remote-Tracking Branches

```
Local branches:              Remote-tracking branches:        Remote (GitHub):
  main                         origin/main                      main
  feature                      origin/feature                   feature
```

- **Remote-tracking branches** (`origin/main`) are **read-only snapshots** of the remote's branches.
- Updated by `git fetch` or `git pull`.
- You can't directly commit to `origin/main` — you commit locally and push.

```bash
git branch -r                     # List remote-tracking branches
git log origin/main               # See remote's commit history
git diff main origin/main         # Compare local vs remote
```

---

## 🔗 Upstream Tracking

An **upstream** (tracking) branch links your local branch to a remote branch.

```bash
# Set upstream
git push -u origin main                    # Set while pushing
git branch --set-upstream-to=origin/main   # Set explicitly

# Check tracking
git branch -vv                             # Shows tracking info
```

Output of `git branch -vv`:
```
* main    3a1f2b4 [origin/main] feat: add dashboard
  feature 7c8d9e0 [origin/feature: ahead 2, behind 1] feat: auth
```

| Status | Meaning |
|--------|---------|
| `ahead 2` | You have 2 local commits not yet pushed |
| `behind 1` | Remote has 1 commit you haven't fetched/merged |
| `gone` | Remote branch was deleted |

---

## 🍴 Cloning

```bash
git clone <url>                     # Clone into folder named after repo
git clone <url> <folder>            # Clone into custom folder
git clone --depth 1 <url>           # Shallow clone — only latest commit (faster)
git clone --branch <name> <url>     # Clone a specific branch
git clone --bare <url>              # Clone without working directory (for servers)
```

### What `git clone` Does Internally
1. `mkdir <repo-name> && cd <repo-name>`
2. `git init`
3. `git remote add origin <url>`
4. `git fetch origin`
5. `git checkout main` (or whatever the default branch is)

---

## 🔄 Typical Remote Workflow

```
1. Clone or add remote
   git clone https://github.com/user/repo.git

2. Create a feature branch
   git checkout -b feature/login

3. Make changes, commit
   git add .
   git commit -m "feat: add login page"

4. Push to remote
   git push -u origin feature/login

5. Create Pull Request on GitHub

6. After PR is merged, update local main
   git checkout main
   git pull origin main

7. Delete feature branch
   git branch -d feature/login
   git push origin --delete feature/login
```

---

## ❓ Interview Questions

### Q1: What is the difference between `git fetch` and `git pull`?
<details>
<summary>Show Answer</summary>

| | `git fetch` | `git pull` |
|-|------------|------------|
| **Action** | Downloads data from remote | Downloads + merges into current branch |
| **Equivalent to** | Just the download step | `git fetch` + `git merge` |
| **Modifies working dir?** | No | Yes |
| **Can cause conflicts?** | No | Yes |
| **Safety** | Always safe | Can overwrite local changes |

**Best Practice**: Use `git fetch` + inspect changes + then `git merge` manually. This gives you control over what gets merged. `git pull` is a convenience shortcut.
</details>

### Q2: What does `git push -u origin main` do?
<details>
<summary>Show Answer</summary>

It pushes the local `main` branch to the remote named `origin` and sets up **upstream tracking** (`-u` or `--set-upstream`).

After this, you can simply use `git push` and `git pull` without specifying the remote and branch — Git remembers the tracking relationship.

Internally, it adds a tracking entry in `.git/config`:
```
[branch "main"]
    remote = origin
    merge = refs/heads/main
```
</details>

### Q3: What is `origin` in Git?
<details>
<summary>Show Answer</summary>

`origin` is the **default name** given to the remote repository when you `git clone`. It's just a **convention** — you can rename it to anything.

```bash
git remote rename origin upstream   # Rename it
git remote add myremote <url>       # Add with any name
```

It's an alias for the remote URL, so `git push origin main` means "push to the URL that 'origin' points to."
</details>

### Q4: How do you delete a remote branch?
<details>
<summary>Show Answer</summary>

```bash
git push origin --delete <branch-name>
# or
git push origin :<branch-name>      # older syntax (push "nothing" to the branch)
```

This removes the branch from the remote. Your local branch and remote-tracking branch remain until you:
```bash
git branch -d <branch-name>         # Delete local
git fetch --prune                    # Clean up stale remote-tracking branches
```
</details>

### Q5: What is `git push --force-with-lease` and why is it better than `--force`?
<details>
<summary>Show Answer</summary>

Both force-push to overwrite the remote branch, but:

- `--force` blindly overwrites the remote — even if someone else pushed commits you don't have. **Data loss risk.**
- `--force-with-lease` checks that the remote branch is at the commit you expect (what you last fetched). If someone else pushed in the meantime, it **refuses** and asks you to fetch first.

**Use case**: After rebasing a feature branch, you need to force-push. `--force-with-lease` ensures you don't accidentally overwrite a teammate's commits.
</details>

### Q6: What is a shallow clone? When would you use it?
<details>
<summary>Show Answer</summary>

```bash
git clone --depth 1 <url>
```

A shallow clone downloads only the latest commit(s) instead of the full history. This:
- **Reduces clone time** and **disk space** significantly for large repos.
- Is useful in **CI/CD pipelines** where you only need the latest code to build/test.
- Is useful for **quick contributions** where you don't need the full history.

Limitation: You can't see full history or do operations that need it (like `git log` or `git blame` going far back). You can "unshallow" later with `git fetch --unshallow`.
</details>

---

[← Previous: Branching & Merging](../03-branching-and-merging/03-branching-and-merging.md) | [Back to README](../README.md) | [Next: Stash, Tag & Undo →](../05-stash-tag-undo/05-stash-tag-undo.md)

# 1. Git Fundamentals — What, Why, Setup

---

## 📌 What is Git?

Git is a **distributed version control system (DVCS)** created by **Linus Torvalds** in **2005** for Linux kernel development.

### Key Characteristics
- **Distributed**: Every developer has a **full copy** of the repository (history + files), not just a snapshot.
- **Fast**: Almost all operations are local — no network needed for commits, diffs, history.
- **Integrity**: Every file and commit is checksummed with **SHA-1** hash — corruption is detectable.
- **Branching model**: Branches are lightweight (just pointers to commits), making branching/merging cheap.

---

## 🆚 Git vs Other Version Control Systems

| Feature | Git (DVCS) | SVN (Centralized VCS) |
|---------|-----------|----------------------|
| Architecture | Distributed — full repo on every machine | Centralized — single server holds the repo |
| Speed | Very fast (local operations) | Slower (network-dependent) |
| Offline Work | Full capability offline | Very limited offline |
| Branching | Cheap, lightweight | Expensive, directory-based |
| Data Model | Snapshots of entire project | Stores file diffs |

---

## 🛠️ Installation & Setup

```bash
# Check if Git is installed
git --version

# Set your identity (required for commits)
git config --global user.name "Aryan Gupta"
git config --global user.email "your.email@example.com"

# Check your config
git config --list

# Set default branch name
git config --global init.defaultBranch main

# Set default editor
git config --global core.editor "code --wait"    # VS Code
```

### Config Levels
```
--system   →  /etc/gitconfig          (all users on machine)
--global   →  ~/.gitconfig            (your user account)
--local    →  .git/config             (this repo only — highest priority)
```

---

## 📁 Initializing a Repository

```bash
# Create a new repo
mkdir my-project && cd my-project
git init
# This creates a hidden .git/ directory — the repo's brain

# Or clone an existing repo
git clone https://github.com/user/repo.git
git clone https://github.com/user/repo.git my-folder   # custom folder name
```

### What's inside `.git/`?
```
.git/
├── HEAD            # Points to current branch
├── config          # Repo-level configuration
├── objects/        # All content (blobs, trees, commits) — the object database
├── refs/           # Branch and tag pointers
│   ├── heads/      # Local branches
│   └── tags/       # Tags
├── hooks/          # Client-side/server-side scripts
├── index           # The staging area
└── logs/           # Reflog data
```

> 🔑 **Interview Tip**: If asked "What happens when you run `git init`?", mention the `.git/` directory and its key components.

---

## 🌳 The Three-Tree Architecture

This is **THE** most important mental model for Git. Every Git operation moves data between these three areas:

```
┌──────────────────┐     git add     ┌──────────────────┐    git commit    ┌──────────────────┐
│                  │ ──────────────► │                  │ ──────────────► │                  │
│  Working         │                 │  Staging Area    │                 │  Repository      │
│  Directory       │                 │  (Index)         │                 │  (.git/objects)   │
│                  │ ◄────────────── │                  │ ◄────────────── │                  │
│  (your files)    │  git restore    │  (next commit)   │  git reset      │  (commit history) │
└──────────────────┘                 └──────────────────┘                 └──────────────────┘
```

| Area | What It Is | Key Point |
|------|-----------|-----------|
| **Working Directory** | The files you see and edit on disk | Untracked & modified files live here |
| **Staging Area (Index)** | A snapshot of what will go into the *next* commit | `git add` puts files here |
| **Repository** | The committed history stored in `.git/objects/` | `git commit` saves the staged snapshot here permanently |

> 🔑 **Interview Tip**: "Git has a staging area between your working directory and the repository. This lets you craft commits carefully — you can stage some changes and not others, even within the same file."

---

## 📄 File Lifecycle in Git

```
Untracked ──► Staged ──► Committed ──► Modified ──► Staged ──► Committed
     │           git add      git commit     (edit file)    git add     git commit
     │
     └── git does not track this file until you explicitly add it
```

### File States
| State | Meaning |
|-------|---------|
| **Untracked** | Git doesn't know about this file |
| **Tracked - Unmodified** | File is committed and hasn't changed |
| **Tracked - Modified** | File has changed since last commit |
| **Staged** | File changes are marked for the next commit |

---

## 🔧 .gitignore

Tells Git which files/patterns to **ignore** (not track).

```bash
# Common .gitignore patterns
node_modules/          # Dependencies
*.log                  # Log files
.env                   # Environment secrets
dist/                  # Build output
.DS_Store              # macOS system file
*.class                # Java compiled files
__pycache__/           # Python cache
```

> **Important**: `.gitignore` only ignores *untracked* files. If a file is already tracked, you need:
> ```bash
> git rm --cached <file>     # Remove from tracking (keeps file on disk)
> ```

---

## ❓ Interview Questions

### Q1: What is Git and how is it different from GitHub?
<details>
<summary>Show Answer</summary>

**Git** is a distributed version control system — a tool that runs locally on your machine to track changes in files.

**GitHub** is a cloud-based hosting platform for Git repositories. It adds collaboration features like Pull Requests, Issues, Actions, and a web UI on top of Git.

**Analogy**: Git is like the engine of a car; GitHub is like a showroom + service center built around that engine.
</details>

### Q2: What is the difference between `git init` and `git clone`?
<details>
<summary>Show Answer</summary>

- `git init` creates a **new, empty** Git repository in the current directory.
- `git clone <url>` **downloads** an existing repository from a remote URL, including its full history, branches, and files.

`git clone` essentially does: `git init` + `git remote add origin <url>` + `git fetch` + `git checkout main`.
</details>

### Q3: Explain the three-tree architecture of Git.
<details>
<summary>Show Answer</summary>

Git uses three "trees" (areas) to manage file changes:

1. **Working Directory** — your actual files on disk that you edit.
2. **Staging Area (Index)** — a buffer where you prepare changes for the next commit using `git add`.
3. **Repository (HEAD)** — the committed history stored in `.git/objects/`.

This architecture allows developers to selectively stage changes, crafting meaningful, atomic commits rather than committing everything at once.
</details>

### Q4: What is `.gitignore` and when would you use it?
<details>
<summary>Show Answer</summary>

`.gitignore` is a file that tells Git which files or directories to exclude from version control. You use it for:
- **Dependencies** (`node_modules/`, `vendor/`)
- **Build artifacts** (`dist/`, `*.class`)
- **Secrets** (`.env`, API keys)
- **OS files** (`.DS_Store`, `Thumbs.db`)
- **IDE configs** (`.idea/`, `.vscode/`)

It only works on **untracked** files. To stop tracking an already-tracked file: `git rm --cached <file>`.
</details>

### Q5: What is a distributed version control system? Why does it matter?
<details>
<summary>Show Answer</summary>

In a DVCS, every developer has a **complete copy** of the repository, including its full history. This means:
- You can work **offline** — commits, branches, diffs, and history are all local.
- There's **no single point of failure** — if the server dies, any clone can restore the repo.
- Operations are **faster** because most don't need network access.
- Developers can work **independently** and sync when ready.

This contrasts with centralized systems (like SVN) where the server is the single source of truth.
</details>

---

[← Back to README](../README.md) | [Next: Core Workflow →](../02-core-workflow/02-core-workflow.md)

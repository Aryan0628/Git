# 🚀 Git & GitHub — SDE Intern Interview Preparation

> A comprehensive, interview-focused study guide covering everything from basics to internals.  
> Each topic includes **notes**, **commands**, **diagrams**, and **practice interview questions**.

---

## 📖 How to Use This Guide

1. **Follow the order** — topics build on each other.
2. **Read the notes** in each section, then try the commands yourself.
3. **Attempt the interview questions** at the end of each file *before* looking at answers.
4. **Star (⭐)** topics you find tricky and revisit them.

---

## 📂 Study Structure

| # | Topic | File | Difficulty |
|---|-------|------|------------|
| 1 | Git Fundamentals — What, Why, Setup | [01-git-basics.md](./01-git-basics/01-git-basics.md) | 🟢 Easy |
| 2 | Core Workflow — add, commit, status, log | [02-core-workflow.md](./02-core-workflow/02-core-workflow.md) | 🟢 Easy |
| 3 | Branching & Merging | [03-branching-and-merging.md](./03-branching-and-merging/03-branching-and-merging.md) | 🟡 Medium |
| 4 | Remote Repositories — push, pull, fetch | [04-remote-repos.md](./04-remote-repos/04-remote-repos.md) | 🟡 Medium |
| 5 | Stashing, Tagging & Undoing Changes | [05-stash-tag-undo.md](./05-stash-tag-undo/05-stash-tag-undo.md) | 🟡 Medium |
| 6 | Rebasing, Cherry-Pick & Advanced Merging | [06-rebase-cherry-pick.md](./06-rebase-cherry-pick/06-rebase-cherry-pick.md) | 🟠 Hard |
| 7 | Git Internals — Objects, Refs, Packfiles | [07-git-internals.md](./07-git-internals/07-git-internals.md) | 🔴 Advanced |
| 8 | GitHub Features — PRs, Issues, Forks, Actions | [08-github-features.md](./08-github-features/08-github-features.md) | 🟡 Medium |
| 9 | Collaboration Workflows & Best Practices | [09-collaboration-workflows.md](./09-collaboration-workflows/09-collaboration-workflows.md) | 🟡 Medium |
| 10 | Interview Questions — Rapid-Fire Cheat Sheet | [10-interview-rapid-fire.md](./10-interview-rapid-fire/10-interview-rapid-fire.md) | 🔥 All |

---

## 🎯 Key Topics Interviewers Love to Ask

- What is the difference between `git merge` and `git rebase`?
- Explain the three-tree architecture (Working Dir → Staging → Repository).
- How does Git store data internally? (blobs, trees, commits)
- What is a detached HEAD?
- How do you resolve a merge conflict?
- `git fetch` vs `git pull` — what's the difference?
- What is a pull request and why is it important?
- Explain Git Flow vs GitHub Flow.

> All of these (and many more) are answered in the topic files above.

---

## ⚡ Quick Command Reference

```bash
# Setup
git init                          # Initialize a new repo
git clone <url>                   # Clone a remote repo

# Day-to-day
git add .                         # Stage all changes
git commit -m "message"           # Commit staged changes
git status                        # Check working tree status
git log --oneline --graph         # Visual commit history

# Branching
git branch <name>                 # Create branch
git checkout -b <name>            # Create + switch
git switch <name>                 # Switch branch (modern)
git merge <branch>                # Merge branch

# Remote
git remote add origin <url>       # Link remote
git push -u origin main           # Push + set upstream
git pull origin main              # Fetch + merge
git fetch                         # Download without merging

# Undo
git restore <file>                # Discard working dir changes
git restore --staged <file>       # Unstage
git reset --soft HEAD~1           # Undo commit, keep staged
git reset --hard HEAD~1           # Undo commit, discard all
git revert <commit>               # Create inverse commit
```

---

## 🗓️ Suggested Study Plan

| Day | Topics | Time |
|-----|--------|------|
| Day 1 | Topics 1–2 (Basics + Core Workflow) | ~1.5 hrs |
| Day 2 | Topics 3–4 (Branching + Remotes) | ~2 hrs |
| Day 3 | Topics 5–6 (Stash/Undo + Rebase/Cherry-pick) | ~2 hrs |
| Day 4 | Topic 7 (Git Internals) | ~1.5 hrs |
| Day 5 | Topics 8–9 (GitHub + Collaboration) | ~1.5 hrs |
| Day 6 | Topic 10 (Rapid-Fire Review) + Weak areas | ~1 hr |

---

**Good luck with your interview! 🎉**

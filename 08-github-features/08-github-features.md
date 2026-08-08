# 8. GitHub Features — PRs, Issues, Forks, Actions

---

## 📌 GitHub ≠ Git

| Git | GitHub |
|-----|--------|
| Version control **tool** (CLI) | Cloud **platform** for hosting Git repos |
| Runs locally | Web-based service |
| Open source | Owned by Microsoft |
| Core: commits, branches, merging | Added: PRs, Issues, Actions, Pages, Packages |

> Alternatives to GitHub: **GitLab**, **Bitbucket**, **Azure DevOps**, **Gitea** (self-hosted)

---

## 🔀 Pull Requests (PRs)

A Pull Request is a **proposal to merge** one branch into another, with built-in **code review** and **discussion**.

### PR Workflow
```
1. Fork or branch from main
2. Make changes, push to remote branch
3. Open a Pull Request on GitHub
4. Team reviews, discusses, requests changes
5. CI/CD runs automated checks
6. Reviewer approves
7. PR is merged (and branch optionally deleted)
```

### PR Merge Options on GitHub

| Option | What It Does | Resulting History |
|--------|-------------|-------------------|
| **Create a merge commit** | `git merge --no-ff` | Preserves branch topology |
| **Squash and merge** | Squashes all PR commits into one | Clean, single commit on main |
| **Rebase and merge** | Rebases PR commits onto main | Linear history, individual commits preserved |

### Best Practices for PRs
- **Small, focused PRs** — one feature or fix per PR.
- **Descriptive title and body** — explain what and why.
- **Link issues** — "Fixes #42" auto-closes the issue when merged.
- **Request specific reviewers** — assign the right people.
- **Draft PRs** — mark as draft when work-in-progress.
- **Review your own PR first** — catch obvious issues before asking others.

---

## 🐛 Issues

Issues are GitHub's **bug tracker and task manager**.

### Issue Features
- **Labels**: Categorize (bug, enhancement, documentation, good first issue)
- **Assignees**: Who's responsible
- **Milestones**: Group issues into releases/sprints
- **Projects**: Kanban-style boards
- **Templates**: Standardized issue forms

### Linking Issues to PRs
```markdown
# In PR description or commit message:
Fixes #42          # Closes issue #42 when PR is merged
Closes #42         # Same effect
Resolves #42       # Same effect
Related to #42     # Links but does NOT auto-close
```

---

## 🍴 Forking

A **fork** is a personal copy of someone else's repository on your GitHub account.

### Fork vs Clone vs Branch

| | Fork | Clone | Branch |
|-|------|-------|--------|
| Where | Creates repo copy **on GitHub** | Downloads repo **to local machine** | Creates new pointer **within a repo** |
| Ownership | Under your GitHub account | On your disk | Same repo, same owner |
| Independence | Fully independent repo | Linked to remote | Same repo |
| Use case | Contributing to others' projects | Working locally | Feature development |

### Fork Workflow (Contributing to Open Source)
```bash
# 1. Fork on GitHub (click "Fork" button)

# 2. Clone YOUR fork locally
git clone https://github.com/YOUR-USER/repo.git
cd repo

# 3. Add original repo as 'upstream'
git remote add upstream https://github.com/ORIGINAL-OWNER/repo.git

# 4. Create a feature branch
git checkout -b fix/typo-in-docs

# 5. Make changes, commit, push to YOUR fork
git add .
git commit -m "fix: typo in installation docs"
git push origin fix/typo-in-docs

# 6. Open a Pull Request from your fork to the original repo

# 7. Keep your fork in sync
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

---

## ⚡ GitHub Actions (CI/CD)

GitHub Actions automates workflows — testing, building, deploying — triggered by events.

### Key Concepts
| Term | Meaning |
|------|---------|
| **Workflow** | An automated process defined in a YAML file |
| **Event** | What triggers the workflow (push, PR, schedule, manual) |
| **Job** | A set of steps that run on the same runner |
| **Step** | An individual task (run a command, use an action) |
| **Runner** | The machine that executes the job (GitHub-hosted or self-hosted) |
| **Action** | A reusable unit of code (from GitHub Marketplace) |

### Basic Workflow Example
```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4          # Check out repo
      - uses: actions/setup-node@v4        # Setup Node.js
        with:
          node-version: '18'
      - run: npm ci                         # Install dependencies
      - run: npm test                       # Run tests
      - run: npm run lint                   # Run linter

  build:
    needs: test                             # Run after test job passes
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run build
```

### Common Triggers
```yaml
on:
  push:                    # On any push
  pull_request:            # On PR events
  schedule:
    - cron: '0 0 * * *'   # Daily at midnight
  workflow_dispatch:        # Manual trigger
  release:
    types: [published]     # On release
```

> 🔑 **Interview Tip**: Even basic knowledge of CI/CD shows you understand modern development workflows. Mention: "I've used GitHub Actions for running tests and linting on every PR."

---

## 📄 Other GitHub Features

### GitHub Pages
- Host static websites directly from a repo.
- Great for documentation, portfolios, project demos.
- Served from `main`, `gh-pages` branch, or `/docs` folder.

### Branch Protection Rules
- Require PR reviews before merging.
- Require status checks (CI) to pass.
- Prevent force pushes.
- Require signed commits.

### CODEOWNERS
```
# .github/CODEOWNERS
*.js    @frontend-team
*.py    @backend-team
/docs/  @docs-team
```
Automatically requests reviews from the right people.

### GitHub CLI (`gh`)
```bash
gh repo clone user/repo          # Clone
gh pr create                     # Create PR from CLI
gh pr list                       # List PRs
gh pr checkout 42                # Check out PR #42 locally
gh issue create                  # Create issue
gh run list                      # List workflow runs
```

---

## ❓ Interview Questions

### Q1: What is a Pull Request and why is it important?
<details>
<summary>Show Answer</summary>

A Pull Request (PR) is a GitHub feature that proposes merging changes from one branch into another. It's important because:

1. **Code Review**: Team members can review, comment on, and suggest changes to the code.
2. **Discussion**: Provides a place for architectural discussions and knowledge sharing.
3. **CI/CD Integration**: Automated tests run against the PR before merging.
4. **History**: Creates a record of why changes were made and who approved them.
5. **Quality Gate**: Prevents untested or unreviewed code from reaching main.

It's the cornerstone of modern collaborative development.
</details>

### Q2: What is the difference between forking and cloning?
<details>
<summary>Show Answer</summary>

- **Fork**: Creates a copy of the repository **on GitHub** under your account. It's a server-side operation. Used for contributing to projects you don't have write access to.

- **Clone**: Downloads a repository **to your local machine**. It's a local operation. Used for working on any repo locally.

Typical open-source workflow: Fork (GitHub) → Clone (local) → Branch → Commit → Push to fork → Open PR to original repo.
</details>

### Q3: What are the three merge options for a Pull Request on GitHub?
<details>
<summary>Show Answer</summary>

1. **Merge commit** (`--no-ff`): Creates a merge commit preserving branch history. Best when you want to see the full branch topology.

2. **Squash and merge**: Combines all PR commits into a single commit on the target branch. Best for keeping a clean, linear history on main.

3. **Rebase and merge**: Replays each PR commit on top of the target branch. Linear history with individual commits preserved. Best when each commit is meaningful.
</details>

### Q4: What is GitHub Actions? Give an example use case.
<details>
<summary>Show Answer</summary>

GitHub Actions is a CI/CD platform built into GitHub. It automates workflows triggered by repository events (push, PR, schedule, etc.).

Example: On every push to `main` or PR:
1. Check out the code
2. Install dependencies
3. Run unit tests
4. Run linter
5. Build the project
6. Deploy to staging (if on main)

This ensures code quality and catches bugs before they reach production. Workflows are defined in YAML files under `.github/workflows/`.
</details>

### Q5: How do you keep a forked repository in sync with the original?
<details>
<summary>Show Answer</summary>

```bash
# Add the original repo as 'upstream' (one-time setup)
git remote add upstream https://github.com/original-owner/repo.git

# Fetch updates from the original
git fetch upstream

# Merge upstream changes into your local main
git checkout main
git merge upstream/main

# Push updated main to your fork
git push origin main
```

You can also sync via GitHub's web UI — there's a "Sync fork" button on the fork's page.
</details>

---

[← Previous: Git Internals](../07-git-internals/07-git-internals.md) | [Back to README](../README.md) | [Next: Collaboration Workflows →](../09-collaboration-workflows/09-collaboration-workflows.md)

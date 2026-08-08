# 9. Collaboration Workflows & Best Practices

---

## 📌 Why Workflows Matter

Without a defined workflow, teams face:
- Merge conflicts everywhere
- Broken `main` branch
- No clear process for code review
- Confusion about what's deployed

A good workflow defines **how branches are used**, **when to merge**, and **how code gets to production**.

---

## 🔀 Popular Branching Strategies

### 1. GitHub Flow (Simple — Recommended for Most Teams)

```
main (always deployable)
  │
  ├── feature/add-login     ← branch off main
  │     │
  │     ├── commits...
  │     │
  │     └── PR → code review → merge into main → deploy
  │
  ├── fix/nav-bug           ← branch off main
  │     │
  │     └── PR → merge into main → deploy
  │
  └── (main is always deployable)
```

**Rules:**
1. `main` is **always deployable**.
2. Create **feature branches** from `main`.
3. Open a **Pull Request** when ready.
4. **Review** and **discuss** the code.
5. **Deploy** from the feature branch (or after merge).
6. **Merge** into `main`.

**Best for**: Web apps, SaaS, small-to-medium teams, continuous deployment.

---

### 2. Git Flow (Complex — For Release-Based Projects)

```
main ────────────────────────────── (production releases only)
  │          ↑ merge                      ↑ merge
  │     ┌────┴──────┐               ┌────┴──────┐
  │     │  release/  │               │  hotfix/   │
  │     │   1.0      │               │  urgent    │
  │     └────┬──────┘               └────┬──────┘
  │          │ merge ↓                    │
develop ─────┴────────────────────────────┴──── (integration branch)
  │          ↑ merge         ↑ merge
  ├── feature/login          ├── feature/search
  └──────────────────────────└─────────────────
```

**Branches:**
| Branch | Purpose | Merges Into |
|--------|---------|-------------|
| `main` | Production releases (tagged) | — |
| `develop` | Integration branch for features | `main` (via release branch) |
| `feature/*` | New features | `develop` |
| `release/*` | Release preparation (bug fixes, docs) | `main` + `develop` |
| `hotfix/*` | Emergency production fixes | `main` + `develop` |

**Best for**: Mobile apps, desktop software, projects with scheduled releases, large teams.

---

### 3. Trunk-Based Development (Modern — Used by Google, Meta)

```
main (trunk) ──────────────────────────── (always releasable)
  │    │    │
  │    │    └── short-lived-branch (< 1 day)
  │    │         └── merged back quickly
  │    │
  │    └── another-short-branch
  │         └── merged back quickly
  │
  └── (feature flags hide incomplete features)
```

**Rules:**
1. Everyone commits to `main` (trunk) frequently.
2. Branches are **very short-lived** (hours, not days).
3. **Feature flags** hide incomplete features from users.
4. Continuous Integration runs on every commit.
5. `main` is **always releasable**.

**Best for**: Large engineering orgs, continuous deployment, high-velocity teams.

---

## 🆚 Comparison

| | GitHub Flow | Git Flow | Trunk-Based |
|-|------------|----------|-------------|
| Complexity | Low | High | Low |
| Branches | main + feature | main + develop + feature + release + hotfix | main + short-lived |
| Release cycle | Continuous | Scheduled | Continuous |
| Best for | Web apps, small teams | Versioned software, large teams | High-velocity, CI/CD mature |
| Branch lifespan | Days | Days–Weeks | Hours |

> 🔑 **Interview Tip**: "For my projects I use GitHub Flow — feature branches off main, PRs with code review, and merge when approved. For larger teams or versioned products, I'd recommend Git Flow."

---

## 📋 Code Review Best Practices

### As a Reviewer
- **Be constructive**, not critical — "Consider using X" not "This is wrong"
- Focus on **logic, security, edge cases**, not style (use linters for that)
- **Ask questions** — "What happens if this is null?"
- **Acknowledge good work** — "Nice refactor here! 👍"
- Review **promptly** — don't block teammates for days

### As an Author
- **Self-review** before requesting others
- **Small PRs** — 200-400 lines max
- **Clear PR description** — what, why, how, and testing done
- **Respond to all comments** — even if just "Done"
- **Don't take feedback personally** — it's about the code

---

## 🛡️ Branch Protection Best Practices

```
main branch protection rules:
├── ✅ Require pull request reviews (1-2 approvals)
├── ✅ Require status checks to pass (CI/CD)
├── ✅ Require branches to be up to date before merging
├── ✅ Prevent force pushes
├── ✅ Prevent deletions
└── ⬜ Require signed commits (optional, for high-security)
```

---

## 📝 Commit Message Conventions

### Conventional Commits (Industry Standard)
```
<type>(<scope>): <subject>

<body>

<footer>
```

| Type | Description |
|------|------------|
| `feat` | New feature (correlates with MINOR in semver) |
| `fix` | Bug fix (correlates with PATCH in semver) |
| `docs` | Documentation only |
| `style` | Code style (formatting, semicolons) |
| `refactor` | Neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or fixing tests |
| `build` | Build system or dependencies |
| `ci` | CI/CD configuration |
| `chore` | Other changes (tooling, scripts) |

### Breaking Changes
```
feat!: drop support for Node 12

BREAKING CHANGE: Minimum Node version is now 16.
```

---

## 🧰 Useful Collaboration Patterns

### 1. Keeping Feature Branch Up to Date
```bash
# Option A: Merge main into feature (preserves history)
git checkout feature
git merge main

# Option B: Rebase feature onto main (linear history)
git checkout feature
git rebase main
```

### 2. Handling Long-Running Feature Branches
- Break into smaller PRs when possible
- Regularly merge/rebase from main to avoid massive conflicts
- Use feature flags for partial features

### 3. Semantic Versioning (SemVer)
```
MAJOR.MINOR.PATCH
  │      │      │
  │      │      └── Bug fixes (backward-compatible)
  │      └──── New features (backward-compatible)  
  └────── Breaking changes (not backward-compatible)

Example: 2.4.1 → 2.4.2 (patch) → 2.5.0 (minor) → 3.0.0 (major)
```

---

## ❓ Interview Questions

### Q1: Explain Git Flow vs GitHub Flow. When would you use each?
<details>
<summary>Show Answer</summary>

**Git Flow:**
- Uses multiple long-lived branches: `main`, `develop`, `feature/*`, `release/*`, `hotfix/*`.
- Suited for projects with **scheduled releases** (mobile apps, desktop software).
- More complex but provides clear release management.

**GitHub Flow:**
- Simple: just `main` + short-lived `feature` branches.
- `main` is always deployable.
- Suited for **continuous deployment** (web apps, SaaS).
- Less ceremony, faster iteration.

I'd use **GitHub Flow** for web applications with CI/CD, and **Git Flow** for projects that need formal release versions (like a mobile app submitted to app stores).
</details>

### Q2: What is trunk-based development?
<details>
<summary>Show Answer</summary>

Trunk-based development is a workflow where all developers commit to a single branch (`main`/trunk) frequently. Key principles:

1. **Short-lived branches** — merged within hours, not days.
2. **Feature flags** — hide incomplete features from users.
3. **Continuous integration** — tests run on every commit.
4. **Always releasable** — main can be deployed at any time.

Used by companies like Google, Meta, and Netflix. It reduces merge conflicts and encourages small, incremental changes.
</details>

### Q3: How do you handle a situation where your feature branch has fallen behind main and has conflicts?
<details>
<summary>Show Answer</summary>

Two approaches:

**Option A — Merge (safer, preserves history):**
```bash
git checkout feature-branch
git merge main
# Resolve any conflicts
git add .
git commit
```

**Option B — Rebase (cleaner, linear history):**
```bash
git checkout feature-branch
git rebase main
# Resolve conflicts commit by commit
git add .
git rebase --continue
# May need to force push if already pushed
git push --force-with-lease
```

I prefer **rebase** for personal feature branches (cleaner PR history) and **merge** for shared branches (no history rewriting).
</details>

### Q4: What makes a good Pull Request?
<details>
<summary>Show Answer</summary>

A good PR:
1. **Small and focused** — one feature/fix per PR (200-400 lines ideally).
2. **Clear title** following conventions (e.g., "feat: add user authentication").
3. **Descriptive body** — what, why, how, screenshots for UI changes.
4. **Links related issues** — "Fixes #42".
5. **Passes all CI checks** — tests, linting, build.
6. **Self-reviewed** — author reviews their own diff before requesting reviews.
7. **Has appropriate reviewers** — assigned to people with relevant knowledge.

Large PRs should be broken into smaller, reviewable chunks.
</details>

### Q5: What are branch protection rules and why are they important?
<details>
<summary>Show Answer</summary>

Branch protection rules are GitHub settings that enforce quality gates on important branches (like `main`):

- **Require PR reviews** — prevents merging without at least one approval.
- **Require status checks** — CI must pass before merging.
- **Prevent force pushes** — protects against history rewriting.
- **Require up-to-date branches** — ensures the PR is current with the target.
- **Require signed commits** — verifies author identity.

They're important because they prevent accidental breakage of production code, ensure code quality, and enforce team collaboration standards.
</details>

---

[← Previous: GitHub Features](../08-github-features/08-github-features.md) | [Back to README](../README.md) | [Next: Rapid-Fire Questions →](../10-interview-rapid-fire/10-interview-rapid-fire.md)

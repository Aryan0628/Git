# 7. Git Internals — Objects, Refs, Packfiles

> This topic is what separates good candidates from great ones. Interviewers love asking "how does Git work under the hood?"

---

## 📌 Git's Data Model — Content-Addressable Filesystem

Git is fundamentally a **content-addressable filesystem** with a VCS user interface on top. Everything is stored as **objects** identified by their **SHA-1 hash**.

```
Key insight: Git doesn't store diffs — it stores SNAPSHOTS.
Each commit is a snapshot of the entire project at that moment.
```

---

## 🧱 The Four Object Types

Everything in Git's object database (`.git/objects/`) is one of four types:

### 1. Blob (Binary Large Object)
- Stores the **contents** of a single file.
- Does NOT store filename or permissions — just raw content.
- SHA-1 hash is computed from the content.
- Two files with **identical content** share the **same blob** (deduplication!).

```bash
# Create a blob manually
echo "Hello World" | git hash-object --stdin      # Just compute hash
echo "Hello World" | git hash-object -w --stdin    # Compute hash + store

# View a blob
git cat-file -p <blob-sha>    # Print content
git cat-file -t <blob-sha>    # Print type ("blob")
git cat-file -s <blob-sha>    # Print size
```

### 2. Tree
- Represents a **directory**.
- Contains pointers to **blobs** (files) and other **trees** (subdirectories).
- Stores: file **mode** (permissions), object **type** (blob/tree), **SHA-1**, and **filename**.

```bash
git cat-file -p <tree-sha>
```

Output:
```
100644 blob a1b2c3d4...    README.md
100644 blob e5f6g7h8...    index.js
040000 tree 9i0j1k2l...    src
```

| Mode | Meaning |
|------|---------|
| `100644` | Regular file |
| `100755` | Executable file |
| `040000` | Directory (tree) |
| `120000` | Symbolic link |
| `160000` | Submodule (gitlink) |

### 3. Commit
- Points to a **tree** (the root directory snapshot).
- Contains: parent commit(s), author, committer, timestamp, and message.

```bash
git cat-file -p <commit-sha>
```

Output:
```
tree 4a5b6c7d...
parent 8e9f0a1b...
author Aryan Gupta <email> 1234567890 +0530
committer Aryan Gupta <email> 1234567890 +0530

feat: add authentication module
```

- **First commit** has no parent.
- **Normal commit** has one parent.
- **Merge commit** has two (or more) parents.

### 4. Tag (Annotated)
- Points to a commit (or any object).
- Contains: tagger, date, message, and the target object.

```bash
git cat-file -p <tag-sha>
```

---

## 🔗 How It All Connects

```
                         ┌─────────────┐
                         │   Commit     │
                         │  (SHA: abc)  │
                         │             │
                         │ tree: xyz    │──────────┐
                         │ parent: def  │          │
                         │ author: ...  │          │
                         │ message: ... │          ▼
                         └─────────────┘   ┌─────────────┐
                                           │    Tree     │
                                           │  (SHA: xyz) │
                                           │             │
                                ┌──────────│ README.md → blob:111  │
                                │          │ src/     → tree:222   │
                                │          └─────────────┘
                                ▼                    │
                         ┌─────────────┐             ▼
                         │    Blob     │      ┌─────────────┐
                         │ (SHA: 111)  │      │    Tree     │
                         │             │      │ (SHA: 222)  │
                         │ # README    │      │             │
                         │ Hello World │      │ app.js → blob:333 │
                         └─────────────┘      └─────────────┘
```

> 🔑 **Interview Tip**: "Git stores data as a directed acyclic graph (DAG) of objects. Commits point to trees, trees point to blobs and other trees. Every object is immutable and identified by its SHA-1 hash."

---

## 📍 References (Refs)

Refs are **human-readable pointers** to commits (so you don't have to remember SHA-1 hashes).

```
.git/refs/
├── heads/          # Local branches
│   ├── main        # Contains SHA of latest commit on main
│   └── feature     # Contains SHA of latest commit on feature
├── tags/           # Tags
│   └── v1.0        # Contains SHA of tagged commit (or tag object)
└── remotes/        # Remote-tracking branches
    └── origin/
        ├── main
        └── feature
```

### Special Refs
| Ref | Location | Meaning |
|-----|----------|---------|
| `HEAD` | `.git/HEAD` | Points to current branch (or commit in detached state) |
| `ORIG_HEAD` | `.git/ORIG_HEAD` | Previous value of HEAD before dangerous operation |
| `MERGE_HEAD` | `.git/MERGE_HEAD` | The commit being merged (during merge) |
| `FETCH_HEAD` | `.git/FETCH_HEAD` | The latest fetch result |

```bash
# Inspect refs
cat .git/HEAD                       # ref: refs/heads/main
cat .git/refs/heads/main            # 3a1f2b4c... (commit SHA)
git rev-parse HEAD                  # Resolve HEAD to SHA
git rev-parse main                  # Resolve branch to SHA
```

### Commit References
```bash
HEAD              # Current commit
HEAD~1            # 1 commit before HEAD (first parent)
HEAD~3            # 3 commits before HEAD
HEAD^             # First parent of HEAD (same as HEAD~1)
HEAD^2            # Second parent of HEAD (used in merge commits)
HEAD~2^2          # Go back 2, then take second parent
```

**`~` vs `^`:**
- `~N` = go back N commits along the **first-parent** chain
- `^N` = select the **Nth parent** of a commit (relevant for merge commits)

---

## 📦 Packfiles & Garbage Collection

### Loose Objects → Packfiles
- Initially, each object is stored as a separate file in `.git/objects/` (**loose objects**).
- Over time, Git compresses them into **packfiles** (`.git/objects/pack/`) using **delta compression**.
- A packfile stores objects as deltas (differences) from similar objects — huge space savings.

```bash
git gc                     # Trigger garbage collection manually
git count-objects -v       # Count objects and see storage stats
git verify-pack -v .git/objects/pack/<file>.idx   # Inspect packfile contents
```

### Garbage Collection (`git gc`)
- Packs loose objects into packfiles.
- Removes unreachable objects (commits with no branch/tag pointing to them that are older than ~2 weeks).
- Runs automatically periodically.

---

## 🔀 The Index (Staging Area) Internally

The index (`.git/index`) is a **binary file** that:
- Lists all tracked files with their **SHA-1**, **mode**, and **timestamps**.
- Acts as a cache between the working directory and the repository.
- Is what gets committed when you run `git commit`.

```bash
git ls-files --stage        # Show the contents of the index
```

Output:
```
100644 a1b2c3d4... 0    README.md
100644 e5f6g7h8... 0    src/app.js
```

The `0` is the **stage number** — used during merge conflicts:
| Stage | Meaning |
|-------|---------|
| 0 | Normal (no conflict) |
| 1 | Common ancestor version |
| 2 | Current branch version (ours) |
| 3 | Incoming branch version (theirs) |

---

## 🧪 Plumbing vs Porcelain Commands

| Porcelain (User-Facing) | Plumbing (Low-Level) |
|------------------------|---------------------|
| `git add` | `git hash-object`, `git update-index` |
| `git commit` | `git write-tree`, `git commit-tree` |
| `git log` | `git rev-list`, `git cat-file` |
| `git diff` | `git diff-tree`, `git diff-files` |
| `git branch` | `git update-ref` |

```bash
# Create a commit using ONLY plumbing commands:
echo "hello" | git hash-object -w --stdin                    # 1. Create blob
git update-index --add --cacheinfo 100644 <sha> hello.txt   # 2. Add to index
git write-tree                                               # 3. Create tree from index
git commit-tree <tree-sha> -m "manual commit"                # 4. Create commit
git update-ref refs/heads/main <commit-sha>                  # 5. Point branch to commit
```

> 🔑 **Interview Tip**: Explaining this shows deep understanding. You can say: "I've worked with Git's plumbing commands to understand the internals — a commit is essentially a tree object (directory snapshot) + metadata + parent pointer."

---

## ❓ Interview Questions

### Q1: How does Git store data internally?
<details>
<summary>Show Answer</summary>

Git uses a **content-addressable filesystem** with four object types:

1. **Blob** — stores file contents (no filename, just raw data)
2. **Tree** — represents a directory; contains pointers to blobs and other trees with filenames and modes
3. **Commit** — points to a root tree (snapshot) + parent commits + metadata (author, message, timestamp)
4. **Tag** — points to a commit with additional metadata (annotated tags only)

All objects are stored in `.git/objects/`, identified by their **SHA-1 hash** (computed from the content). This makes Git a **Merkle tree / DAG** — any change in content changes all ancestor hashes.
</details>

### Q2: Does Git store diffs or snapshots?
<details>
<summary>Show Answer</summary>

Git stores **snapshots**, not diffs. Each commit points to a tree object that represents the **complete state** of the project at that point.

However, Git is smart about storage:
- If a file hasn't changed, the new tree points to the **same blob** (no duplication).
- Over time, `git gc` compresses objects into **packfiles** using **delta compression**, which does store diffs between similar objects — but this is a **storage optimization**, not the data model.

The key insight: Git *thinks* in snapshots but *stores* efficiently using deltas when possible.
</details>

### Q3: What is HEAD in Git?
<details>
<summary>Show Answer</summary>

`HEAD` is a **pointer** that tells Git what you currently have checked out. It's stored in `.git/HEAD`.

**Normal state**: HEAD points to a branch, which points to a commit:
```
HEAD → refs/heads/main → commit abc123
```

**Detached HEAD state**: HEAD points directly to a commit:
```
HEAD → commit abc123
```

HEAD moves when you:
- Make a commit (HEAD follows the branch to the new commit)
- Switch branches (`git checkout`/`git switch`)
- Reset (`git reset`)
</details>

### Q4: Explain the difference between `~` and `^` in commit references.
<details>
<summary>Show Answer</summary>

Both are ways to reference ancestor commits, but they navigate differently:

- **`~N`** (tilde): Goes back N commits along the **first-parent** chain.
  - `HEAD~1` = parent, `HEAD~2` = grandparent, `HEAD~3` = great-grandparent

- **`^N`** (caret): Selects the **Nth parent** of a commit.
  - `HEAD^1` = first parent, `HEAD^2` = second parent (only meaningful for merge commits)

For non-merge commits, `HEAD~1` and `HEAD^` are identical.
For merge commits, `HEAD^1` is the branch you were on, `HEAD^2` is the branch you merged in.
</details>

### Q5: What are plumbing and porcelain commands?
<details>
<summary>Show Answer</summary>

Git commands are divided into two categories:

**Porcelain** (high-level, user-friendly):
- `git add`, `git commit`, `git log`, `git push`, `git merge`
- These are the commands users interact with daily.

**Plumbing** (low-level, internal):
- `git hash-object`, `git cat-file`, `git update-index`, `git write-tree`, `git commit-tree`, `git update-ref`
- These are the building blocks that porcelain commands use internally.

The analogy comes from bathroom plumbing: the porcelain (toilet, sink) is what you see and use; the plumbing (pipes) is the infrastructure underneath.
</details>

### Q6: What is a SHA-1 collision and does it matter for Git?
<details>
<summary>Show Answer</summary>

A SHA-1 collision is when two different inputs produce the same hash. While theoretically possible, it's extremely impractical:
- The probability of a random collision is ~1 in 2^160 (astronomically small).
- Google demonstrated a SHA-1 collision in 2017 (SHAttered), but it required enormous computation.

**For Git in practice**: The risk is negligible. Git is also transitioning to **SHA-256** for future-proofing. In everyday use, SHA-1 collisions are not a practical concern.
</details>

---

[← Previous: Rebase & Cherry-Pick](../06-rebase-cherry-pick/06-rebase-cherry-pick.md) | [Back to README](../README.md) | [Next: GitHub Features →](../08-github-features/08-github-features.md)

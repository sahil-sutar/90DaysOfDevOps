# Git Merge: Core Concepts & Hands-On Notes

## 1. What is a Fast-Forward Merge?

A **Fast-Forward (FF) merge** occurs when there is a direct linear path from the target branch (`main`) to the tip of the feature branch (`feature`). This happens when **no new commits** were added to `main` after the feature branch was created.

### Key Characteristics:
- **No new commit:** Git does not create a new merge commit.
- **Pointer shift:** Git simply moves (fast-forwards) the `main` branch pointer forward to match the latest commit on `feature`.
- **Linear history:** Commit logs remain strictly sequential and uncluttered.

```text
Before Merge:
main:    A --- B
                \
feature:         C --- D

After Fast-Forward Merge (`git merge feature`):
main, feature: A --- B --- C --- D
```

---

## 2. When Does Git Create a Merge Commit Instead?

Git creates a **Merge Commit** when the branches have **diverged**—that is, when new commits have been added to `main` AND to the feature branch independently after branching.

### Key Characteristics:
- **3-Way Merge Strategy:** Git uses three commits to merge:
  1. The **common ancestor commit** (base)
  2. The tip of `main` (ours / HEAD)
  3. The tip of `feature` (theirs)
- **New Commit Created:** A dedicated merge commit is generated having **two parent commits**.
- **Explicit History:** Preserves the exact narrative and lifecycle of the feature branch.

```text
Before Merge:
main:    A --- B --- E
                \
feature:         C --- D

After 3-Way Merge (`git merge feature`):
main:    A --- B --- E ------- M (Merge Commit)
                \             /
feature:         C --- D ----/
```

> **Tip:** You can enforce a merge commit even on linear histories by running:
> ```bash
> git merge --no-ff feature-branch
> ```

---

## 3. What is a Merge Conflict?

A **Merge Conflict** occurs when Git cannot automatically reconcile differences between two branches being merged. This requires human intervention to resolve.

### Common Causes:
- **Competing edits:** The same line(s) in the same file were edited differently on both branches.
- **File removal conflicts:** A file was deleted in one branch but edited/updated in the other.

---

## 4. Hands-On: Intentionally Creating & Resolving a Merge Conflict

### Step 1: Create a feature branch and modify a file
```bash
git checkout main
git checkout -b feature-conflict
echo "User role: Administrator" > config.txt
git add config.txt
git commit -m "Set role to Admin in feature branch"
```

### Step 2: Switch to main and edit the same line
```bash
git checkout main
echo "User role: Superuser" > config.txt
git add config.txt
git commit -m "Set role to Superuser in main"
```

### Step 3: Attempt the merge
```bash
git merge feature-conflict
```

**Git Output:**
```text
Auto-merging config.txt
CONFLICT (content): Merge conflict in config.txt
Automatic merge failed; fix conflicts and then commit the result.
```

### Step 4: Understanding Conflict Markers
When opening `config.txt`, Git highlights the conflicting blocks:
```text
<<<<<<< HEAD
User role: Superuser
=======
User role: Administrator
>>>>>>> feature-conflict
```
- `<<<<<<< HEAD`: Content currently on your active target branch (`main`).
- `=======`: Separator between the competing versions.
- `>>>>>>> feature-conflict`: Content incoming from the branch being merged.

### Step 5: Resolve and complete the merge
1. Open the file in an editor and choose/merge the desired lines:
   ```text
   User role: Superuser
   ```
2. Stage and commit the resolution:
   ```bash
   git add config.txt
   git commit -m "Resolve merge conflict in config.txt"
   ```

---

## 5. Quick Comparison Table

| Feature / Concept | Fast-Forward Merge | 3-Way Merge Commit | Merge Conflict |
| :--- | :--- | :--- | :--- |
| **Trigger Condition** | Target branch has no new commits | Target & feature have diverged | Conflicting edits on same line/file |
| **Creates New Commit?** | ❌ No | ✅ Yes (Merge commit with 2 parents) | ✅ Yes (After manual conflict fix) |
| **History Appearance** | Completely flat/linear | Branched topology preserved | Branched topology preserved |
| **Manual Intervention** | None | None (unless conflict occurs) | Required (edit markers & commit) 


# Task 2: Git Rebase — Core Concepts & Hands-On Notes

---

## 1. Hands-On Walkthrough

### Step 1: Create `feature-dashboard` and Add Commits
```bash
# Ensure you start on main
git checkout main

# Create and switch to feature branch
git checkout -b feature-dashboard

# Add commit 1
echo "<h1>Dashboard Header</h1>" >> dashboard.html
git add dashboard.html
git commit -m "feat: add dashboard header"

# Add commit 2
echo "<div class='widget'>Stats</div>" >> dashboard.html
git add dashboard.html
git commit -m "feat: add stats widget"

# Add commit 3
echo "<div class='widget'>Charts</div>" >> dashboard.html
git add dashboard.html
git commit -m "feat: add charts widget"
```

---

### Step 2: Move `main` Ahead
```bash
# Switch back to main
git checkout main

# Add a commit directly to main to create divergence
echo "# System Changelog" >> CHANGELOG.md
git add CHANGELOG.md
git commit -m "docs: update changelog on main"
```

```text
Divergent State Before Rebase:
      (feature-dashboard) C --- D --- E
                         /
main:             A --- B --- F
```

---

### Step 3: Rebase `feature-dashboard` onto `main`
```bash
git checkout feature-dashboard
git rebase main
```

**Git Terminal Output:**
```text
Successfully rebased and updated refs/heads/feature-dashboard.
```

---

### Step 4: Verify the Log Graph
```bash
git log --oneline --graph --all
```

**Observation:**
- **No branch fork or merge bubble:** The entire history is condensed into a single straight line.
- The feature commits are re-applied *after* the latest commit on `main`.

```text
* e7f8a9b (HEAD -> feature-dashboard) feat: add charts widget
* d4e5f6a feat: add stats widget
* c1b2a3d feat: add dashboard header
* f9e8d7c (main) docs: update changelog on main
* b8a7c6e Initial commit
```

---

## 2. Core Concepts & Questions Answered

### 1. What does `git rebase` actually do to your commits?
`git rebase` **replays** feature commits on top of a new base tip:
1. **Parks commits:** Git temporarily saves feature commits (`C`, `D`, `E`) in a patch area (`.git/rebase-apply/`).
2. **Resets pointer:** The feature branch pointer moves to match the target branch (`main` at `F`).
3. **Re-applies commits:** Each parked commit is applied one by one on top of `F`.
4. **Rewrites SHA hashes:** Because parent hashes and commit timestamps change, Git creates **brand-new commit objects** (`C'`, `D'`, `E'`). Original commits are dereferenced.

```text
Rebase Mechanics:
Before:
main:             A --- B --- F
                         \
feature:                  C --- D --- E

After Rebase:
main:             A --- B --- F
                               \
feature:                        C' --- D' --- E' (New Commit Hashes)
```

---

### 2. How is the history different from a merge?

| Aspect | `git merge` | `git rebase` |
| :--- | :--- | :--- |
| **History Topology** | **Non-linear / Forked:** Branch creation and join points are preserved. | **Strictly Linear:** Appears as though development was sequential. |
| **Merge Commit** | Creates an explicit 3-way merge commit with two parents. | **No merge commit** created. |
| **Commit Hashes** | Original commit hashes remain unchanged. | **Rewrites commit hashes** (`C` $\rightarrow$ `C'`). |
| **Chronology** | Preserves true real-time chronological order. | Fabricates a simplified, linear timeline. |

---

### 3. Why should you never rebase commits that have been pushed and shared?

> **The Golden Rule of Git Rebase:** Never rebase commits that exist outside your local repository and that others may have based work upon.

- **Hash Inconsistency:** Rebasing changes commit IDs (`C` $\rightarrow$ `C'`).
- **Team Desynchronization:** If another developer pulled `C` and built code on top of it, force-pushing `C'` creates duplicate commit histories on their machine.
- **Merge Hell:** Resolving conflicting identical changes with mismatched hashes causes confusing duplicate commits and broken PR histories.

---

### 4. When would you use Rebase vs. Merge?

#### Choose **Git Rebase** when:
- **Updating local feature branches:** Pulling the latest changes from `origin/main` into your local feature branch before creating a Pull Request.
- **Cleaning commit history:** Using interactive rebase (`git rebase -i`) to squash typos, formatting fixes, or WIP commits into clean, atomic commits.
- **Keeping a clean, linear git log** across small, fast-moving teams.

#### Choose **Git Merge** when:
- **Integrating completed features into public branches:** Merging a Pull Request into `main` or `develop`.
- **Preserving full historical context:** Retaining exact branching timelines and team integration points.
- **Collaborating on shared branches:** Multiple developers pushing and pulling to the same feature branch.

# Task 3: Squash Commit vs Merge Commit — Hands-On & Notes

---

## 1. Hands-On Step-by-Step

### Part A: Squash Merge (`feature-profile`)

```bash
# 1. Create and switch to feature-profile
git checkout main
git checkout -b feature-profile

# 2. Add 4-5 small commits (typo fixes, formatting, incremental tweaks)
echo "<h1>User Profile</h1>" > profile.html && git add . && git commit -m "feat: scaffold profile"
echo "<style>.avatar { border-radius: 50%; }</style>" >> profile.html && git add . && git commit -m "style: avatar"
echo "<!-- Fix typo -->" >> profile.html && git add . && git commit -m "fix: typo in profile"
echo "<p>Bio: Software Engineer</p>" >> profile.html && git add . && git commit -m "feat: bio field"
echo "<!-- Format -->" >> profile.html && git add . && git commit -m "chore: format markup"

# 3. Switch to main and perform the squash merge
git checkout main
git merge --squash feature-profile
```

#### What happens during `--squash`?
Git takes **all changes** from those 5 commits, combines them into a single diff, and places them directly into the **staging area (index)** of `main` without creating a commit automatically.

Now, finish the squash merge:
```bash
git commit -m "feat: complete user profile feature"
```

#### How many commits were added to `main`?
Run `git log --oneline -n 3`:
* **Exactly 1 commit** is added to `main`.
* All 5 micro-commits and "fix typo" messages from `feature-profile` are compressed into one neat commit.

---

### Part B: Regular Merge (`feature-settings`)

```bash
# 1. Create feature-settings and add commits
git checkout -b feature-settings
echo "<h1>Settings</h1>" > settings.html && git add . && git commit -m "feat: settings page"
echo "<p>Notifications</p>" >> settings.html && git add . && git commit -m "feat: notifications"

# 2. Advance main and perform a regular merge (with --no-ff)
git checkout main
echo "# Config" >> CONFIG.md && git add . && git commit -m "docs: add config docs"
git merge --no-ff feature-settings -m "Merge branch 'feature-settings' into main"
```

---

### Part C: Comparing the History

Run:
```bash
git log --oneline --graph -n 8
```

```text
*   7a1b2c3 (HEAD -> main) Merge branch 'feature-settings' into main
|\  
| * e4f5g6h feat: notifications
| * a1b2c3d feat: settings page
|/  
* 9d8e7f6 docs: add config docs
* 3b4c5d6 feat: complete user profile feature   <-- (Squashed single commit)
```

* **Squash Merge:** The feature branch history is completely flattened into a single linear commit.
* **Regular Merge:** All individual commits from the feature branch are preserved alongside a dedicated merge commit with two parents.

---

## 2. Answers for Your Notes

### 1. What does squash merging do?
* Combines (**squashes**) all changes from all commits on a feature branch into a single set of staged changes on the target branch.
* Produces **one single commit** with only **one parent** on `main`.
* Discards the individual intermediate commit history (WIPs, typo fixes, formatting commits) from the main branch.

---

### 2. When would you use Squash Merge vs Regular Merge?

| Scenario | Recommended Strategy | Why? |
| :--- | :--- | :--- |
| **Small feature / bugfix branches** with noisy WIP commits | **Squash Merge** | Keeps the `main` history clean, readable, and free of trivial commit noise. |
| **Pull Requests from external/open-source contributors** | **Squash Merge** | Condenses review iterations and fixes into one clean patch. |
| **Large, multi-stage epic features** | **Regular Merge (`--no-ff`)** | Preserves individual subsystem commit milestones and detailed development context. |
| **Long-lived shared / release branches** | **Regular Merge** | Retains branch join points and explicit multi-parent relationships. |

---

### 3. What is the trade-off of Squashing?

#### **Pros / Advantages:**
* **Pristine, linear `main` log:** Every commit on `main` represents a complete, functional feature or bugfix.
* **Simple Rollbacks:** Reverting a feature requires reverting only **1 single commit** (`git revert <hash>`).

#### **Cons / Trade-offs:**
* **Loss of granular context:** Individual commit messages, specific timestamps, and step-by-step reasoning behind decisions are permanently discarded from `main`.
* **Difficult `git bisect` debugging:** If a bug is introduced inside a large squashed commit with 1,000+ line changes, `git bisect` points only to the giant commit rather than isolating the exact 2-line typo commit.
* **Branch re-merge issues:** Because the squashed commit on `main` has a new hash and does not share a merge parent relationship with the source branch, continuing to work on the old branch and merging again later can cause repetitive merge conflicts


## Git Stash Notes

### 1. Difference Between `git stash pop` and `git stash apply`

- **`git stash pop`**
  - Applies the most recent stash to the working directory.
  - Removes the stash from the stash list after successfully applying it.
  - Example:
    ```bash
    git stash pop
    ```

- **`git stash apply`**
  - Applies the stash to the working directory.
  - Keeps the stash in the stash list.
  - Useful when you want to keep the stash as a backup or apply it again later.
  - Example:
    ```bash
    git stash apply stash@{0}
    ```

**In short:**

| Command | Applies Changes | Removes Stash |
|---|---|---|
| `git stash pop` | ✅ Yes | ✅ Yes |
| `git stash apply` | ✅ Yes | ❌ No |

---

### 2. When Would You Use Stash in a Real-World Workflow?

I would use `git stash` when I am working on a feature and have unfinished changes, but suddenly need to switch to another branch to handle an urgent bug, hotfix, code review, or other task.

For example:

```text
Working on Feature A
        ↓
Uncommitted changes
        ↓
Urgent bug needs to be fixed
        ↓
git stash
        ↓
Switch to bug-fix branch
        ↓
Fix the bug and commit changes
        ↓
Switch back to Feature A
        ↓
git stash pop
        ↓
Continue working on Feature A.

# Git Cherry-Pick Notes

## What does `git cherry-pick` do?

`git cherry-pick` takes the changes introduced by a specific commit and applies those changes as a new commit on the current branch.

For example:

```bash
git cherry-pick <commit-hash>

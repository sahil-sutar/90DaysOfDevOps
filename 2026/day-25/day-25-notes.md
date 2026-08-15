# Day 25: Git Reset vs Revert & Branching Strategies

## Task 1: Git Reset — Hands-On

### Hands-On Steps & Observations

1. **Initial Setup (Commits A, B, C):**
   - Created three sequential commits (`A` -> `B` -> `C`).
   - `HEAD` points to commit `C`.

2. **`git reset --soft HEAD~1`:**
   - **What happens:** Moves `HEAD` back one commit to `B`.
   - **File state:** Changes from commit `C` remain in the **Staging Area (Index)**.
   - **`git status`:** Shows changes in green under `"Changes to be committed"`. Working directory files are untouched.

3. **`git reset --mixed HEAD~1` (Default):**
   - **What happens:** Moves `HEAD` back one commit to `B` and clears the staging area.
   - **File state:** Changes from commit `C` remain in the **Working Directory**, but are unstaged.
   - **`git status`:** Shows changes in red under `"Changes not staged for commit"`.

4. **`git reset --hard HEAD~1`:**
   - **What happens:** Moves `HEAD` back one commit to `B`, clears the staging area, and overwrites working tree files.
   - **File state:** Changes from commit `C` are completely **discarded**.
   - **`git status`:** Shows `"working tree clean"`.

---

### Questions & Answers

#### 1. What is the difference between `--soft`, `--mixed`, and `--hard`?

| Flag | Moves `HEAD`? | Updates Index (Staging)? | Modifies Working Directory? | Where do changes go? |
| :--- | :--- | :--- | :--- | :--- |
| **`--soft`** | **Yes** | No | No | Stay in Staging Area (ready to commit) |
| **`--mixed`** *(default)* | **Yes** | **Yes** | No | Stay in Working Directory (unstaged) |
| **`--hard`** | **Yes** | **Yes** | **Yes** | Permanently deleted / wiped |

#### 2. Which one is destructive and why?
* **`--hard` is destructive.**
* **Why:** Both `--soft` and `--mixed` retain your written modifications in local storage. `--hard` overwrites the files in your working directory to match the target commit, permanently discarding any uncommitted changes.

#### 3. When would you use each one?
* **`git reset --soft`:** When you commit too early and want to combine/squash multiple local commits together before creating a Pull Request.
* **`git reset --mixed`:** When you want to redo a commit and split the modifications into smaller, logically separated commits (`git add -p`).
* **`git reset --hard`:** When experimental code completely breaks and you want to wipe all local changes back to a known working commit.

#### 4. Should you ever use `git reset` on commits that are already pushed?
* **No.** Resetting pushed commits rewrites repository history. If you force-push (`git push --force`), you alter the commit tree for everyone else on the team, causing merge conflicts and broken references.

---

## Task 2: Git Revert — Hands-On

### Hands-On Steps & Observations

1. Created three sequential commits (`X` -> `Y` -> `Z`).
2. Executed: `git revert <commit-Y-hash>`
3. **What happens:** Git creates a **brand-new commit** that applies the exact inverse diff of commit `Y`.
4. **History check (`git log --oneline`):**
   ```text
   7a8b9c0 Revert "Commit Y"
   3f4e5d6 Commit Z
   2b3c4d5 Commit Y
   1a2b3c4 Commit X

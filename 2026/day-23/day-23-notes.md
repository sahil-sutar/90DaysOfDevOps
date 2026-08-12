# Day 23 Notes: Understanding Git Branches

## 1. What is a branch in Git?
In Git, a **branch** is simply a lightweight, movable pointer to a specific commit in your repository history. 
* Unlike older version control systems that copied entire project folders, Git branches are just 40-character commit hashes stored in text files inside `.git/refs/heads/`.
* Creating a new branch creates a new pointer without duplicating any project files, making branching virtually instantaneous and computationally cheap.

---

## 2. Why do we use branches instead of committing everything to `main`?
Committing directly to `main` is dangerous and disorganized in production environments. We use branches because:
* **Isolation of Work:** You can work on new features, bug fixes, or experiments without breaking the stable, working code on `main`.
* **Parallel Development:** Multiple team members can work on different features simultaneously in their own isolated branches without stepping on each other's toes.
* **Code Review & Quality Control:** Changes can be tested and reviewed via Pull Requests (PRs) before being merged into the primary branch.
* **Safe Experimentation:** If an experimental feature fails, you can simply delete the branch without affecting the working project history.

---

## 3. What is `HEAD` in Git?
`HEAD` is a special reference pointer in Git that indicates **where you currently are in your repository**.
* **Active Branch Pointer:** In almost all cases, `HEAD` points to the tip of your current active branch (e.g., `HEAD -> main` or `HEAD -> feature-branch`).
* **Detached HEAD State:** If you checkout a specific commit hash instead of a branch name, `HEAD` points directly to that commit rather than a branch pointer.
* You can check where `HEAD` is pointing anytime inside `.git/HEAD`.

---

## 4. What happens to your files when you switch branches?
When you switch branches using `git switch <branch-name>` (or `git checkout <branch-name>`):
1. **Working Directory Update:** Git automatically updates your working directory files to match the exact snapshot of the latest commit on the target branch.
2. **File Addition/Removal/Modification:** Files that exist only in the target branch appear in your folder; files that belong only to your old branch disappear; modified files revert to their target branch state.
3. **`HEAD` Relocation:** The `HEAD` pointer moves to point to the top commit of the newly selected branch.


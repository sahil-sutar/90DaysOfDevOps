# Day 22 Notes: Understanding the Git Workflow

## 1. What is the difference between `git add` and `git commit`?
* **`git add`** moves changes from your **Working Directory** to the **Staging Area**. It selects specific modified or new files to be included in your next snapshot without saving them permanently to the history yet.
* **`git commit`** takes everything currently sitting in the **Staging Area** and permanently saves it into the **Git Repository history** alongside a descriptive message, author name, and timestamp.

---

## 2. What does the staging area do? Why doesn't Git just commit directly?
* **Role:** The staging area (also called the index) acts as a **drafting table** or preparation zone between your active files and your permanent commit history.
* **Why it exists:** It allows you to group related changes together into clean, logical commits. For example, if you modified three files but two belong to a bug fix and one belongs to a separate feature, the staging area lets you stage and commit the bug fix files first, and then commit the feature file separately.

---

## 3. What information does `git log` show you?
`git log` displays the chronological commit history of the repository. For each commit, it reveals:
* **Commit Hash (SHA-1/SHA-256):** The unique identifier string for that commit (e.g., `a1b2c3d...`).
* **Author Info:** The name and email address of the person who created the commit.
* **Date & Time:** Exact timestamp of when the commit was saved.
* **Commit Message:** The descriptive note explaining what changes were made.
* **Branch Markers (HEAD -> main):** Shows which commit active branches are currently pointing to.

---

## 4. What is the `.git/` folder and what happens if you delete it?
* **What it is:** The hidden `.git` folder located in your project's root directory is the actual Git repository database. It stores all commit objects, branches, history logs, local configuration files, and staging index data.
* **What happens if you delete it:** Your project instantly **ceases to be a Git repository**. While your current working source files remain completely safe and untouched, **you permanently destroy all commit history, version tracking, branches, and staging information** for that local repository.

---

## 5. What is the difference between working directory, staging area, and repository?
These are the **Three Trees** of the Git architecture:

1. **Working Directory:** The local folder on your computer's filesystem where you view, create, edit, and save your project files directly using your text editor/IDE.
2. **Staging Area (Index):** A single binary tracking file inside `.git/` that lists all modified changes queued up and formatted to go into the next commit snapshot.
3. **Repository (Local Database):** The permanent `.git/` database containing all committed snapshots, objects, and complete version history of your project.

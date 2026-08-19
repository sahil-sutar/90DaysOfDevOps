# Day 28: Comprehensive Revision Day (Days 1–27)

---

## Task 1: Self-Assessment Checklist Matrix

| Domain | Skill / Topic | Status | Focus Needed |
|---|---|---|---|
| Linux | File system navigation, file CRUD | [x] Confident | None |
| Linux | Process lifecycle (ps, kill, top, background jobs) | [x] Confident | None |
| Linux | systemd service lifecycle & unit files | [x] Confident | Revisit custom unit creation |
| Linux | Vim editor shortcuts & file operations | [x] Confident | None |
| Linux | Resource diagnostics (df, du, free, top, vmstat) | [x] Confident | Inode vs Block exhaustion |
| Linux | Filesystem hierarchy (/etc, /var, /proc, /opt) | [x] Confident | None |
| Linux | User/Group administration & /etc/passwd, /etc/shadow | [x] Confident | Sudoers permissions |
| Linux | Permissions (chmod, chown, chgrp, Sticky Bit, SUID) | [x] Confident | Special permissions |
| Linux | Storage & LVM (pvcreate, vgextend, lvextend) | [ ] Revisit | Dynamic resizing hands-on |
| Linux | Network diagnostics (ss, dig, curl, netstat, ip) | [ ] Revisit | Subnet CIDR calculation |
| Scripting | Variables, CLI args ($1, $@, $#), user input | [x] Confident | None |
| Scripting | Flow control (if/elif/else, case statements) | [x] Confident | Regex matching in conditionals |
| Scripting | Loops (for, while, until, break, continue) | [x] Confident | IFS-based file iteration |
| Scripting | Functions, local scoping, return exit codes | [x] Confident | None |
| Scripting | Text pipelines (grep -E, awk, sed, sort -u) | [ ] Revisit | Advanced awk multi-field filtering |
| Scripting | Defensive bash (set -euo pipefail, trap) | [x] Confident | Signal trapping on exit |
| Scripting | Scheduling & automation with cron | [x] Confident | Environment variables in crontab |
| Git/GitHub | Core lifecycle (stage, commit, amend, log formatting) | [x] Confident | None |
| Git/GitHub | Branching, merging (Fast-Forward vs Non-FF) | [x] Confident | Merge conflict resolution |
| Git/GitHub | Rebase workflow vs Standard Merge | [ ] Revisit | Interactive rebase (git rebase -i) |
| Git/GitHub | Stashing untracked/staged files | [x] Confident | Stash apply vs pop |
| Git/GitHub | State alteration: git reset (soft/mixed/hard) vs git revert | [ ] Revisit | Preserving detached HEAD commits |
| Git/GitHub | Branching strategies (GitFlow, GitHub Flow, Trunk-Based) | [x] Confident | Release branch maintenance |
| Git/GitHub | GitHub CLI (gh repo, gh pr, gh issue) | [x] Confident | Scripting with gh JSON output |

---

## Task 2: Deep-Dive Revisit on Core Weak Spots

### 1. Logical Volume Management (LVM) Architecture
- **Layer 1: Physical Volume (PV)** — Initializes raw disks/partitions for LVM:
  - Command: `sudo pvcreate /dev/sdb /dev/sdc`
  - Check status: `sudo pvs`
- **Layer 2: Volume Group (VG)** — Pools storage from PVs into an aggregate pool:
  - Command: `sudo vgcreate app_pool /dev/sdb /dev/sdc`
  - Check status: `sudo vgs`
- **Layer 3: Logical Volume (LV)** — Slices storage to format as filesystems:
  - Command: `sudo lvcreate -L 20G -n app_data app_pool`
  - Format: `sudo mkfs.ext4 /dev/app_pool/app_data`
  - Mount: `sudo mount /dev/app_pool/app_data /var/www/data`
- **Dynamic Expansion (Zero Downtime):**
  - Command: `sudo lvextend -L +10G -r /dev/app_pool/app_data`

### 2. Stream Processing with awk and sed
- **sed (Stream Editor):**
  - Replace IP across configuration files:
    `sed -i 's/127.0.0.1/192.168.1.50/g' /etc/nginx/nginx.conf`
- **awk (Field Extraction):**
  - Parse access logs for top 404 client IPs:
    `cat /var/log/nginx/access.log | awk '$9 == "404" {print $1}' | sort | uniq -c | sort -nr | head -n 5`

### 3. Git State Resetting & History Management
- **git reset --soft HEAD~1:** Undoes commit, leaves changes staged in the index.
- **git reset --mixed HEAD~1:** Undoes commit and unstages changes, keeping files in working directory.
- **git reset --hard HEAD~1:** Deletes commit, working changes, and stages completely.
- **git revert <commit-hash>:** Creates a safe, inverse commit without rewriting branch history.

---

## Task 3: Quick-Fire Reference Questions

### 1. What does chmod 755 script.sh do?
- Owner (7): Read, Write, Execute (`rwx`)
- Group (5): Read, Execute (`r-x`)
- Others (5): Read, Execute (`r-x`)

### 2. What is the difference between a process and a service?
- **Process:** An instance of a running binary tied to a user session or shell.
- **Service:** A continuous daemon running in the background managed by `systemd`.

### 3. How do you find which process is using port 8080?
- Using ss: `sudo ss -tulpn | grep ':8080'`
- Using lsof: `sudo lsof -i :8080`

### 4. What does set -euo pipefail do in a shell script?
- `-e`: Exit immediately on command failure.
- `-u`: Exit when encountering undefined variables.
- `-o pipefail`: Propagate errors through pipes instead of passing the last command's status.

### 5. What is the difference between git reset --hard and git revert?
- `git reset --hard` erases commit history and working tree changes permanently.
- `git revert` adds a new commit containing inverse changes, preserving shared commit logs.

### 6. What branching strategy would you recommend for a team of 5 developers shipping weekly?
- **GitHub Flow:** Short-lived feature branches off a stable `main`, peer-reviewed PRs, and fast integration.

### 7. What does git stash do and when would you use it?
- Saves uncommitted working directory changes on an internal stack to clear the workspace for emergency branch switches.

### 8. How do you schedule a script to run every day at 3 AM?
- Crontab entry: `0 3 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1`

### 9. What is the difference between git fetch and git pull?
- `git fetch` downloads remote objects without modifying local workspace files.
- `git pull` executes `git fetch` followed immediately by `git merge`.

### 10. What is LVM and why would you use it instead of regular partitions?
- Abstracted software storage pooling that allows dynamic volume resizing and snapshots across multiple disks without repartitioning.

---

## Task 4: Submission & Repository Check
- [x] All submissions for Day 1 through Day 27 pushed to remote fork.
- [x] Git commands summary and shell scripting cheat sheet updated.
- [x] GitHub profile and repository organization verified.

---

## Task 5: Teach It Back

### Explaining Git Branching to a Non-Developer

> Think of software development like writing a book with a team. The approved, printed version is the **main branch**.
>
> If you want to try out a crazy new ending, you don't overwrite the original manuscript. Instead, you make a photocopy of the chapter to write your changes on — this is a **branch**.
>
> You can write, delete, and test your new chapter without messing up the original book. Once your editor reviews and approves the new draft, the photocopy pages are officially merged back into the master manuscript.

---

### Explaining File Permissions (chmod) to a New Linux User

> Every file in Linux has an access badge based on who you are and what you want to do:
>
> 1. **Who are you?**
>    - Owner (u): The file creator.
>    - Group (g): The team members assigned to the file.
>    - Others (o): Anyone else on the system.
>
> 2. **What can you do?**
>    - Read (4): View the file.
>    - Write (2): Modify the file.
>    - Execute (1): Run the file as a program.
>
> In `chmod 755`:
> - Owner gets `4 + 2 + 1 = 7` (Full access).
> - Group gets `4 + 0 + 1 = 5` (Read and Run).
> - Others get `4 + 0 + 1 = 5` (Read and Run).

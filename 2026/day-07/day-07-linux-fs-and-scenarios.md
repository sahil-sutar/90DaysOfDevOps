# Day 07 – Linux File System Hierarchy & Scenario Practice

---

## Part 1: Linux File System Hierarchy

### / (root)
- Starting point of the entire file system.
- Everything exists under this directory.
I would use this when navigating the full system structure.

---

### /home
- Contains user home directories.
- Example: /home/ubuntu
I would use this when accessing user files or scripts.

Command used:
ls -la /home

---

### /root
- Home directory of root user.
I would use this when logged in as root for admin tasks.

Command used:
ls -la /root

---

### /etc
- Contains system configuration files.
- Example: /etc/hostname
I would use this when checking or editing config files.

Command used:
ls -l /etc

---

### /var/log
- Stores system and service logs.
- Important for troubleshooting.
I would use this when checking logs during incidents.

Command used:
ls -l /var/log

---

### /tmp
- Temporary files stored here.
I would use this for testing or temporary scripts.

Command used:
ls -l /tmp

---

### /bin
- Essential system binaries (basic commands).
I would use this when understanding where core commands exist.

---

### /usr/bin
- User-level command binaries.
I would use this when checking installed commands.

---

### /opt
- Optional third-party applications.
I would use this when checking manually installed apps.

---

## Hands-On Tasks

Find largest log files:
du -sh /var/log/* 2>/dev/null | sort -h | tail -5

Check config file:
cat /etc/hostname

Check home directory:
ls -la ~

---

## Part 2: Scenario-Based Practice

---

### Scenario 1: Service Not Starting (myapp)

Step 1:
systemctl status myapp
Why: Check if service failed or inactive.

Step 2:
journalctl -u myapp -n 50
Why: Check logs for errors.

Step 3:
systemctl is-enabled myapp
Why: Check if it starts on boot.

Step 4:
systemctl restart myapp
Why: Try restarting after investigation.

---

### Scenario 2: High CPU Usage

Step 1:
top
Why: Check live CPU usage.

Step 2:
ps aux --sort=-%cpu | head -10
Why: Identify top CPU-consuming processes.

Step 3:
pgrep <process-name>
Why: Get PID of specific process.

---

### Scenario 3: Finding Docker Logs

Step 1:
systemctl status docker
Why: Confirm service state.

Step 2:
journalctl -u docker -n 50
Why: View recent logs.

Step 3:
journalctl -u docker -f
Why: Follow logs in real-time.

---

### Scenario 4: Permission Denied Issue

Step 1:
ls -l /home/user/backup.sh
Why: Check file permissions.

Step 2:
chmod +x /home/user/backup.sh
Why: Add execute permission.

Step 3:
ls -l /home/user/backup.sh
Why: Verify permission change.

Step 4:
./backup.sh
Why: Test execution.

---

## What I Learned

- Knowing file system hierarchy reduces confusion.
- Logs are mostly in /var/log or journald.
- Always check service status first.
- Permissions matter when executing scripts.

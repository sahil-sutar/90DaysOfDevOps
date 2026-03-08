# Day 12 – Breather & Revision (Days 01–11)

## Goal
Take a pause and revise the fundamentals learned from Day 01 to Day 11 to strengthen understanding and retention.

---

## 1️⃣ Mindset & Plan Review

On Day 01, I started the #90DaysOfDevOps challenge with the goal of building strong DevOps fundamentals.

### Initial Goals
- Learn Linux fundamentals
- Understand system processes & services
- Master file permissions and ownership
- Stay consistent with daily practice

### Reflection
After completing the first 11 days, I feel more confident using Linux commands and navigating the system. I now better understand how processes, services, and file permissions work.

Going forward, I want to improve my troubleshooting skills and command speed.

---

## 2️⃣ Processes & Services Revision

Commands practiced:

```
ps aux
systemctl status ssh
journalctl -u ssh
```

### Observations
- `ps aux` shows all running processes in the system.
- `systemctl status ssh` shows whether the SSH service is active or not.
- `journalctl -u ssh` shows logs related to the SSH service.

These commands are useful when checking system health and debugging service issues.

---

## 3️⃣ File Skills Practice

Commands practiced from Days 06–11:

```
echo "Hello DevOps" >> notes.txt
chmod 644 notes.txt
ls -l
cp notes.txt backup.txt
mkdir practice-folder
```

### What I Observed
- `echo >>` appends text to a file.
- `chmod` changes file permissions.
- `ls -l` shows permissions, ownership, and file details.
- `cp` copies files.
- `mkdir` creates directories.

---

## 4️⃣ Cheat Sheet Refresh (Day 03)

Five commands I would use first during an incident:

| Command | Purpose |
|--------|--------|
| `ls -l` | Check files, permissions, ownership |
| `ps aux` | View running processes |
| `systemctl status` | Check service status |
| `df -h` | Check disk usage |
| `journalctl` | View system logs |

---

## 5️⃣ User & Group Scenario Practice

Scenario recreated from Day 09 / Day 11.

Commands used:

```
sudo useradd testuser
sudo chown testuser:testuser notes.txt
id testuser
ls -l notes.txt
```

### Verification
- `id testuser` confirms the user was created.
- `ls -l` confirms the ownership change.

---

## 🧠 Mini Self-Check

### 1️⃣ Which 3 commands save you the most time right now?

- **ls -l** – Quickly shows file permissions and ownership.
- **ps aux** – Helps check running processes.
- **systemctl status** – Instantly shows service status.

---

### 2️⃣ How do you check if a service is healthy?

Commands I would run:

```
systemctl status <service>
ps aux | grep <service>
journalctl -u <service>
```

---

### 3️⃣ How do you safely change ownership and permissions?

Example:

```
sudo chown user:group filename
sudo chmod 644 filename
```

This ensures correct ownership and safe permissions without breaking access.

---

### 4️⃣ What will you focus on improving in the next 3 days?

- Improve Linux command speed
- Learn more about system logs
- Practice real troubleshooting scenarios

---

## 📌 Key Takeaways

- Daily practice improves command familiarity.
- Understanding file permissions is essential in Linux administration.
- Logs and service checks help identify system problems quickly.
- Consistency is the key to learning DevOps.

---

## 🚀 Learn in Public

Today I revised everything from Day 01–11 of the #90DaysOfDevOps challenge.

This helped reinforce Linux basics like process monitoring, service checks, and file permissions.

One command I now remember confidently: `systemctl status`.

#90DaysOfDevOps  
#DevOpsKaJosh  
#TrainWithShubham

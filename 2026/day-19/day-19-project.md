# 📅 Day 19 Project: Log Rotation, Backup & Crontab

## 📌 Project Overview
This project demonstrates automation for essential system administration tasks. By using Bash scripting and Crontab, we ensure that logs are managed, data is backed up, and maintenance is performed automatically.

---

## 🛠 Task 1: Log Rotation Script (`log_rotate.sh`)
This script manages log file growth by:
- Compressing `.log` files older than **7 days**
- Deleting `.gz` archives older than **30 days**

### 💻 Script Code:
```bash
#!/bin/bash
# log_rotate.sh - Rotates logs and manages storage

TARGET_DIR=$1

# Error Handling: Ensure directory exists
if [ ! -d "$TARGET_DIR" ]; then
    echo "Error: Directory '$TARGET_DIR' does not exist."
    exit 1
fi

# 1. Compress .log files older than 7 days
comp_count=$(find "$TARGET_DIR" -type f -name "*.log" -mtime +7 | wc -l)
if [ "$comp_count" -gt 0 ]; then
    find "$TARGET_DIR" -type f -name "*.log" -mtime +7 -exec gzip {} \;
fi

# 2. Delete .gz files older than 30 days
del_count=$(find "$TARGET_DIR" -type f -name "*.gz" -mtime +30 | wc -l)
if [ "$del_count" -gt 0 ]; then
    find "$TARGET_DIR" -type f -name "*.gz" -mtime +30 -delete
fi

# Summary Output
echo "Log Rotation Summary for $TARGET_DIR:"
echo "Files compressed: $comp_count"
echo "Files deleted:    $del_count"
```

---

## 💾 Task 2: Server Backup Script (`backup.sh`)
This script:
- Creates timestamped backups
- Verifies success
- Maintains a **14-day retention policy**

### 💻 Script Code:
```bash
#!/bin/bash
# backup.sh - Creates archives and manages retention

SOURCE=$1
DEST=$2
TIMESTAMP=$(date +%Y-%m-%d)
ARCHIVE_NAME="backup-$TIMESTAMP.tar.gz"

# Error Handling: Check if source exists
if [ ! -d "$SOURCE" ]; then
    echo "Error: Source directory '$SOURCE' does not exist."
    exit 1
fi

# Create destination folder if needed
mkdir -p "$DEST"

# Create compressed archive
tar -czf "$DEST/$ARCHIVE_NAME" "$SOURCE" 2>/dev/null

# Verification
if [ $? -eq 0 ]; then
    SIZE=$(du -sh "$DEST/$ARCHIVE_NAME" | cut -f1)
    echo "Backup Successful: $ARCHIVE_NAME"
    echo "Archive Size: $SIZE"
else
    echo "Error: Backup process failed."
    exit 1
fi

# Cleanup: Delete backups older than 14 days
find "$DEST" -type f -name "backup-*.tar.gz" -mtime +14 -delete
```

---

## ⏰ Task 3: Crontab Configuration

### 📅 Scheduled Jobs:
| Frequency | Task | Cron Command |
|----------|------|-------------|
| Daily at 2 AM | Log Rotation | `0 2 * * * /home/ubuntu/log_rotate.sh /var/log/myapp` |
| Sunday at 3 AM | Server Backup | `0 3 * * 0 /home/ubuntu/backup.sh /home/ubuntu/src /backup` |
| Every 5 mins | Health Check | `*/5 * * * * /home/ubuntu/health_check.sh` |

---

## 🔄 Task 4: Combined Maintenance Script (`maintenance.sh`)
This script runs both tasks and logs output centrally.

### 💻 Script Code:
```bash
#!/bin/bash
# maintenance.sh - Scheduled Maintenance Wrapper

LOGFILE="/var/log/maintenance.log"

log_event() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') : $1" >> "$LOGFILE"
}

log_event "MAINTENANCE START"

# Execute Log Rotation
log_event "Starting Log Rotation..."
/home/ubuntu/log_rotate.sh /var/log/myapp >> "$LOGFILE" 2>&1

# Execute Backup
log_event "Starting Server Backup..."
/home/ubuntu/backup.sh /home/ubuntu/src /backup >> "$LOGFILE" 2>&1

log_event "MAINTENANCE FINISHED"
```

### 🕐 Cron Entry (Daily at 1 AM):
```bash
0 1 * * * sudo /home/ubuntu/maintenance.sh
```

---

## 🎓 Key Learnings
- ✅ **Directory Validation**  
  Used `[ ! -d "$DIR" ]` to prevent scripts from running on invalid paths.

- ✅ **Dynamic Retention**  
  Used `find -mtime` for automatic cleanup of old logs and backups.

- ✅ **Log Redirection**  
  Learned `2>&1` to combine error and output logs for easier debugging.

---

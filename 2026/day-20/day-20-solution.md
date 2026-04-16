# Day 20 – Log Analyzer Script (Advanced Bash Project)

## 📌 Project Overview
On Day 20, I built a **Log Analyzer Script** using Bash that automates log inspection, error detection, and report generation.

This project simulates real-world DevOps tasks like:
- Monitoring logs
- Detecting failures
- Generating reports
- Archiving processed logs

---

## 🛠 Features Implemented

### ✅ Input Validation
- Ensures log file argument is provided
- Checks if file exists before processing

### ✅ Log Analysis
- Counts total lines
- Detects **ERROR / Failed** logs
- Extracts **CRITICAL events**

### ✅ Top Error Detection
- Finds top 5 frequent errors
- Uses `grep`, `awk`, `sort`, `uniq`

### ✅ Report Generation
- Creates structured report with:
  - Date & time
  - Log file name
  - Total lines
  - Error count
  - Top errors
  - Critical events

### ✅ Log Archiving
- Moves processed log file to `archive/`

---

## 📜 Script: `log_analyzer.sh`

```bash
#!/bin/bash

LOG_FILE=$1

if [ -z "$LOG_FILE" ]; then
    echo "Usage: $0 <path_to_log_file>"
    exit 1
fi

if [ ! -f "$LOG_FILE" ]; then
    echo "Error: File '$LOG_FILE' not found!"
    exit 1
fi

DATE=$(date +%Y-%m-%d)
REPORT_FILE="log_report_$DATE.txt"
TOTAL_LINES=$(wc -l < "$LOG_FILE")

echo "Analyzing log file: $LOG_FILE..."

ERROR_COUNT=$(grep -Ei "ERROR|Failed" "$LOG_FILE" | wc -l)
echo "Total Errors/Failures: $ERROR_COUNT"

CRITICAL_EVENTS=$(grep -n "CRITICAL" "$LOG_FILE")

TOP_ERRORS=$(grep "ERROR" "$LOG_FILE" | awk -F'ERROR' '{print $2}' | sed 's/^: *//' | sort | uniq -c | sort -rn | head -5)

{
echo "========================================="
echo "        LOG ANALYSIS REPORT - $DATE"
echo "========================================="
echo "Log File       : $LOG_FILE"
echo "Processed On   : $(date)"
echo "Total Lines    : $TOTAL_LINES"
echo "Error Count    : $ERROR_COUNT"
echo ""

echo "----- Top 5 Error Messages -----"
if [ -z "$TOP_ERRORS" ]; then
    echo "No ERROR messages found."
else
    echo "$TOP_ERRORS"
fi
echo ""

echo "----- Critical Events -----"
if [ -z "$CRITICAL_EVENTS" ]; then
    echo "No CRITICAL events found."
else
    echo "$CRITICAL_EVENTS" | while read -r line; do
        L_NUM=$(echo $line | cut -d: -f1)
        L_MSG=$(echo $line | cut -d: -f2-)
        echo "Line $L_NUM: $L_MSG"
    done
fi

echo "========================================="
} > "$REPORT_FILE"

echo "Report generated: $REPORT_FILE"

mkdir -p archive
mv "$LOG_FILE" archive/
echo "Log file moved to archive directory."

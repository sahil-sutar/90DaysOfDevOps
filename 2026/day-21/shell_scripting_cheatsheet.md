# Shell Scripting Cheat Sheet

A comprehensive quick-reference guide for Linux administration, CI/CD automation, and Bash scripting.

---

## Quick Reference Summary

| Topic | Key Syntax | Example |
| :--- | :--- | :--- |
| **Variable** | `VAR="value"` | `NAME="DevOps"` |
| **Argument** | `$1`, `$2`, `$@`, `$#` | `./script.sh arg1 arg2` |
| **Conditionals** | `if [ condition ]; then ... fi` | `if [ -f file.txt ]; then echo "Found"; fi` |
| **For Loop** | `for i in list; do ... done` | `for i in {1..5}; do echo $i; done` |
| **While Loop** | `while [ condition ]; do ... done` | `while read -r line; do echo "$line"; done < file.txt` |
| **Function** | `name() { ... }` | `greet() { echo "Hello $1"; }` |
| **Grep** | `grep [options] pattern file` | `grep -ri "error" /var/log/` |
| **Awk** | `awk -F'sep' '{action}' file` | `awk -F: '{print $1, $3}' /etc/passwd` |
| **Sed** | `sed 's/search/replace/g' file` | `sed -i 's/HTTP/HTTPS/g' config.conf` |
| **Strict Mode** | `set -euo pipefail` | Top of every production script |

---

## 1. Basics

### Shebang (`#!/bin/bash`)
Tells the OS kernel which interpreter to use for script execution.
```bash
#!/usr/bin/env bash  # Portable shebang searching user's $PATH for bash
```

### Running a Script
```bash
chmod +x script.sh   # Grants executable permissions
./script.sh          # Executes script via shebang interpreter in a child subshell
bash script.sh       # Runs script directly through bash, ignoring execute bit & shebang
source script.sh     # Runs script inside CURRENT shell session (aliases/variables persist)
```

### Comments
```bash
# This is a single-line comment
echo "Deploying..." # Inline comment describing the action
```

### Variables
Declaring, referencing, and controlling scope via double (`"`) and single (`'`) quotes.
```bash
ENV="production"
echo "Deploying to $ENV"   # Double quotes expand variables -> Deploying to production
echo 'Deploying to $ENV'   # Single quotes treat text literally -> Deploying to $ENV
```

### Reading User Input
```bash
read -p "Enter environment name: " ENV_NAME
read -sp "Enter DB Password: " DB_PASS # Silent mode (hides input)
```

### Command-Line Arguments & Special Variables
```bash
echo "Script name: $0"      # Name of the executed script
echo "First argument: $1"   # First positional argument
echo "Total args: $#"       # Total count of passed arguments
echo "All args: $@"         # All arguments passed as individual words
echo "Last Exit Status: $?" # Return code of previous command (0 = success)
echo "Process ID: $$"       # PID of current running script
```

---

## 2. Operators and Conditionals

### String Comparisons
```bash
[ "$A" = "$B" ]   # Equal
[ "$A" != "$B" ]  # Not equal
[ -z "$A" ]       # True if string is empty (zero length)
[ -n "$A" ]       # True if string is NOT empty (non-zero length)
```

### Integer Comparisons
```bash
[ "$X" -eq "$Y" ]  # Equal
[ "$X" -ne "$Y" ]  # Not equal
[ "$X" -lt "$Y" ]  # Less than
[ "$X" -gt "$Y" ]  # Greater than
[ "$X" -le "$Y" ]  # Less than or equal
[ "$X" -ge "$Y" ]  # Greater than or equal
```

### File Test Operators
```bash
[ -e "$PATH" ]  # True if file/directory exists
[ -f "$FILE" ]  # True if regular file exists
[ -d "$DIR" ]   # True if directory exists
[ -r "$FILE" ]  # True if readable
[ -w "$FILE" ]  # True if writable
[ -x "$FILE" ]  # True if executable
[ -s "$FILE" ]  # True if file exists and size > 0 bytes
```

### `if`, `elif`, `else` Syntax
```bash
if [ -f "/etc/nginx/nginx.conf" ]; then
    echo "Nginx config found."
elif [ -d "/etc/nginx" ]; then
    echo "Directory exists, missing config."
else
    echo "Nginx not installed."
fi
```

### Logical Operators
```bash
[ -f "$FILE" ] && echo "File exists"              # AND: Run 2nd command if 1st succeeds
[ -d "$DIR" ] || mkdir -p "$DIR"                  # OR: Run 2nd command if 1st fails
if [ ! -f "$FILE" ] && [ -w "$DIR" ]; then ... fi # NOT (!), AND (&&) inside condition
```

### Case Statements
```bash
case "$1" in
    start)  systemctl start app ;;
    stop)   systemctl stop app ;;
    *)      echo "Usage: $0 {start|stop}"; exit 1 ;;
esac
```

---

## 3. Loops

### For Loop (List-Based & C-Style)
```bash
# List-Based
for SERVER in web01 web02 db01; do
    echo "Pinging $SERVER..."
done

# C-Style
for ((i=1; i<=5; i++)); do
    echo "Attempt $i"
done
```

### While Loop
```bash
COUNTER=1
while [ $COUNTER -le 3 ]; do
    echo "Count: $COUNTER"
    ((COUNTER++))
done
```

### Until Loop
Executes until condition evaluates to **true** (opposite of `while`).
```bash
until pgrep nginx > /dev/null; do
    echo "Waiting for Nginx to launch..."
    sleep 2
done
```

### Loop Control
```bash
for num in {1..5}; do
    [ $num -eq 2 ] && continue  # Skip iteration when num is 2
    [ $num -eq 4 ] && break     # Terminate loop entirely when num is 4
    echo "$num"
done
```

### Looping Over Files & Command Output
```bash
# Looping over files matching a pattern
for log_file in /var/log/*.log; do
    echo "Processing $log_file"
done

# Stream reading line-by-line safely
while read -r line; do
    echo "Config entry: $line"
done < config.txt
```

---

## 4. Functions

### Defining and Calling Functions
```bash
# Function Definition
check_status() {
    echo "Checking service: $1" # $1 inside function refers to 1st argument passed to it
    systemctl is-active --quiet "$1"
    return $? # Returns exit code of previous command
}

# Function Call
check_status "docker"
```

### Return Values vs. Output
* **`return N`**: Returns an integer status code (`0`-`255`) meant for execution checks.
* **`echo "data"`**: Standard way to return data strings/objects to caller.

```bash
get_uptime() {
    local uptime_val=$(uptime -p) # Local variable, isolated from global scope
    echo "$uptime_val"
}

# Capturing function output
CURRENT_UPTIME=$(get_uptime)
```

---

## 5. Text Processing Commands

### `grep` (Pattern Matching)
```bash
grep -i "error" app.log     # Case-insensitive match
grep -r "TIMEOUT" /var/log/ # Recursive search in directory
grep -c "FAIL" build.log    # Count matching line instances
grep -n "FATAL" app.log     # Show line numbers of matches
grep -v "DEBUG" app.log     # Invert match (exclude lines containing DEBUG)
grep -E "err|crit" app.log  # Extended regex (OR pattern)
```

### `awk` (Field Extraction & Processing)
```bash
awk '{print $1}' access.log                 # Print 1st column (space delimited)
awk -F':' '{print $1, $3}' /etc/passwd      # Custom field delimiter (:)
awk '/ERROR/ {print $3}' app.log            # Filter by pattern and print 3rd column
awk 'BEGIN {print "--- START ---"} {print $0} END {print "--- END ---"}' file.txt
```

### `sed` (Stream Editor / Text Manipulation)
```bash
sed 's/http/https/g' config.txt     # Replace all occurrences (preview to stdout)
sed -i 's/v1/v2/g' config.txt        # Edit file in-place
sed -i '/DEBUG/d' app.log           # Delete all lines matching "DEBUG" in-place
```

### `cut` (Column Extraction)
```bash
cut -d',' -f1,3 data.csv # Extract 1st and 3rd column delimited by comma
cut -c1-10 file.txt      # Extract character positions 1 through 10 per line
```

### `sort` & `uniq`
```bash
sort -r names.txt         # Reverse alphabetical sort
sort -n -k2 data.txt      # Numeric sort based on 2nd column
sort IPs.txt | uniq       # Remove consecutive duplicate lines
sort IPs.txt | uniq -c    # Count frequency of occurrence per unique line
```

### `tr` (Translate/Delete Characters)
```bash
echo "hello devops" | tr 'a-z' 'A-Z' # Convert lowercase to uppercase -> HELLO DEVOPS
echo "file name.txt" | tr -d ' '      # Delete all space characters -> filename.txt
```

### `wc`, `head`, `tail`
```bash
wc -l access.log          # Count total lines in file
head -n 20 app.log        # Output first 20 lines
tail -n 50 app.log        # Output last 50 lines
tail -f /var/log/syslog   # Stream/follow file additions in real time
```

---

## 6. Useful Patterns and One-Liners

### 1. Delete files older than N days
```bash
find /var/log/app -type f -name "*.log" -mtime +30 -exec rm -f {} \;
```

### 2. Count total lines across all `.log` files recursively
```bash
find . -name "*.log" -exec wc -l {} + | awk '{total += $1} END {print total}'
```

### 3. Replace a string across multiple files in-place
```bash
find . -type f -name "*.env" -exec sed -i 's/DB_PORT=5432/DB_PORT=5433/g' {} +
```

### 4. Check if a service is active and restart if down
```bash
systemctl is-active --quiet nginx || systemctl restart nginx
```

### 5. Monitor disk usage and alert when usage exceeds 85%
```bash
df -H | awk '{ print $5 " " $1 }' | grep -v Use | while read -r output; do usage=$(echo "$output" | awk '{print $1}' | cut -d'%' -f1); partition=$(echo "$output" | awk '{print $2}'); if [ "$usage" -ge 85 ]; then echo "ALERT: $partition is at ${usage}%"; fi; done
```

### 6. Parse JSON value via command line (native `grep`/`sed`)
```bash
curl -s https://api.github.com/users/octocat | grep '"public_repos":' | sed -E 's/.*: ([0-9]+),/\1/'
```

---

## 7. Error Handling and Debugging

### Exit Codes
```bash
exit 0 # Terminate script with SUCCESS
exit 1 # Terminate script with GENERAL ERROR
```

### Strict Mode Settings (`set -euo pipefail`)
Place these at the top of every production script to handle failures safely.

```bash
set -e          # Exit immediately if any command returns a non-zero exit status
set -u          # Treat unset variables as an error and exit immediately
set -o pipefail # Pipeline exit status is the value of the LAST failed command in pipe
set -x          # Debug mode: Trace script execution by printing each command before running
```

### Cleanup Traps
Executes cleanup logic automatically when a script terminates or receives signals (e.g., `EXIT`, `SIGINT`, `SIGTERM`).

```bash
#!/usr/bin/env bash
set -euo pipefail

TMP_DIR=$(mktemp -d)

# Function to run automatically on exit
cleanup() {
    echo "Cleaning up temporary directory: $TMP_DIR"
    rm -rf "$TMP_DIR"
}

# Register cleanup function on EXIT signal
trap cleanup EXIT

# Script logic here...
echo "Working in $TMP_DIR..."
```

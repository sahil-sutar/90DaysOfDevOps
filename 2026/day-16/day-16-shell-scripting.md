# Day 16 – Shell Scripting Basics

## Overview
Today I started learning **Shell Scripting**, which is an essential skill for DevOps engineers.  
Shell scripts help automate repetitive tasks and manage systems efficiently.

In this challenge I practiced:
- Understanding the **Shebang (`#!/bin/bash`)**
- Using **variables, echo, and read**
- Writing **basic if-else conditions**

---

# Task 1: Your First Script

## hello.sh

```bash
#!/bin/bash

echo "Hello, DevOps!"
```

## Make the script executable and run it

```bash
chmod +x hello.sh
./hello.sh
```

## Output

```
Hello, DevOps!
```

## What happens if the shebang line is removed?

The shebang (`#!/bin/bash`) tells the system which interpreter should run the script.

If the shebang is removed:

- The script may still run when executed with:
  ```bash
  bash hello.sh
  ```
- But when running:
  ```bash
  ./hello.sh
  ```
  the system may not know which interpreter to use.

So the **shebang ensures the script runs using Bash.**

---

# Task 2: Variables

## variables.sh

```bash
#!/bin/bash

NAME="Sahil"
ROLE="DevOps Engineer"

echo "Hello, I am $NAME and I am a $ROLE"

echo 'Hello, I am $NAME and I am a $ROLE'
```

## Output

```
Hello, I am Sahil and I am a DevOps Engineer
Hello, I am $NAME and I am a $ROLE
```

## Difference between Single Quotes and Double Quotes

Double quotes `" "`  
- Variables are **expanded**.

Example:
```
echo "Hello $NAME"
```

Single quotes `' '`  
- Variables are **not expanded** and printed as plain text.

Example:
```
echo '$NAME'
```

---

# Task 3: User Input with read

## greet.sh

```bash
#!/bin/bash

read -p "Enter your name: " NAME
read -p "Enter your favourite tool: " TOOL

echo "Hello $NAME, your favourite tool is $TOOL"
```

## Example Output

```
Enter your name: Sahil
Enter your favourite tool: Git
Hello Sahil, your favourite tool is Git
```

---

# Task 4: If-Else Conditions

## check_number.sh

```bash
#!/bin/bash

read -p "Enter a number: " NUMBER

if [ "$NUMBER" -gt 0 ]; then
    echo "The number is positive"
elif [ "$NUMBER" -lt 0 ]; then
    echo "The number is negative"
else
    echo "The number is zero"
fi
```

## Example Output

```
Enter a number: -10
The number is negative
```

---

## file_check.sh

```bash
#!/bin/bash

read -p "Enter filename: " FILE

if [ -f "$FILE" ]; then
    echo "File exists"
else
    echo "File does not exist"
fi
```

## Example Output

```
Enter filename: test.txt
File exists
```

---

# Task 5: Combine Everything

## server_check.sh

```bash
#!/bin/bash

SERVICE="nginx"

read -p "Do you want to check the status of $SERVICE? (y/n): " ANSWER

if [ "$ANSWER" = "y" ]; then

    if systemctl is-active --quiet $SERVICE; then
        echo "$SERVICE service is running"
    else
        echo "$SERVICE service is not running"
    fi

elif [ "$ANSWER" = "n" ]; then
    echo "Skipped"

else
    echo "Invalid input"
fi
```

## Example Output

```
Do you want to check the status of nginx? (y/n): y
nginx service is running
```

---

# What I Learned

1. The **shebang (`#!/bin/bash`)** tells Linux which interpreter should run the script.
2. **Variables and `read` command** allow scripts to store and take user input.
3. **If-else conditions** help scripts make decisions such as checking numbers, files, or service status.

---

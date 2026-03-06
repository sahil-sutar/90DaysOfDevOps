# Day 10 Challenge – Linux File Permissions

## Overview
In this challenge, I practiced Linux file management and permission handling.  
I created different files, modified their permissions using the `chmod` command, and tested how Linux security works when permissions are restricted.

This exercise helped me understand how Linux controls **read, write, and execute access** for different users.

---

# Files Created

### 1. devops.txt
This file was created as an empty file using the `touch` command.  
It was mainly used to practice modifying permissions and testing read-only behavior.

Command used:
touch devops.txt

Purpose:
To test how Linux behaves when write permissions are removed.

---

### 2. notes.txt
This file was created to store some sample text content using the `echo` command.

Command used:
echo "These are my DevOps notes" > notes.txt

Purpose:
To practice reading file contents using commands like:
- cat
- head
- tail

---

### 3. script.sh
This file was created using `vim` and contains a simple shell script.

Content of script:
echo "Hello DevOps"

Command used:
vim script.sh

Purpose:
To understand how **execute permission works for scripts** in Linux.

---

### 4. project/ Directory
A directory named `project` was created to practice directory permissions.

Command used:
mkdir project

Purpose:
To understand how directory permissions affect access and navigation.

---

# Permission Changes

Linux permissions follow the structure:

rwxrwxrwx

Which represents:

Owner | Group | Others

Values:
r = read (4)  
w = write (2)  
x = execute (1)

---

## 1. script.sh

Before change:

rw-r--r--

Meaning:
Owner → read, write  
Group → read  
Others → read  

The script could not run because execute permission was missing.

Command used:

chmod +x script.sh

After change:

rwxr-xr-x

Meaning:
Owner → read, write, execute  
Group → read, execute  
Others → read, execute  

Now the script can be executed using:

./script.sh

Output:

Hello DevOps

---

## 2. devops.txt

Before change:

rw-r--r--

Meaning:
Owner could read and write.

Command used:

chmod a-w devops.txt

After change:

r--r--r--

Meaning:
Owner → read only  
Group → read only  
Others → read only  

Now the file is **read-only**, so writing to it will produce an error.

Example test:

echo "test" >> devops.txt

Error message:

Permission denied

---

## 3. notes.txt

Before change:

rw-r--r--

Command used:

chmod 640 notes.txt

After change:

rw-r-----

Meaning:
Owner → read, write  
Group → read  
Others → no permission  

This type of permission is often used for **sensitive configuration files**.

---

## 4. project Directory

Directory created using:

mkdir project

Permission set using:

chmod 755 project

Final permission:

rwxr-xr-x

Meaning:
Owner → read, write, execute  
Group → read, execute  
Others → read, execute  

Execute permission on directories allows users to **enter the directory**.

---

# Commands Used

touch devops.txt  
echo "These are my DevOps notes" > notes.txt  
vim script.sh  
ls -l  
cat notes.txt  
vim -R script.sh  
head -n 5 /etc/passwd  
tail -n 5 /etc/passwd  
chmod +x script.sh  
chmod a-w devops.txt  
chmod 640 notes.txt  
mkdir project  
chmod 755 project  

---

# What I Learned

### 1. Linux Permission Structure
Linux permissions are divided into three categories:
- Owner
- Group
- Others

Each category can have **read, write, and execute** permissions.

---

### 2. Numeric Permission System
Permissions can also be represented using numbers:

r = 4  
w = 2  
x = 1  

Examples:
644 → rw-r--r--  
640 → rw-r-----  
755 → rwxr-xr-x  

This numeric system makes it easier to assign permissions using `chmod`.

---

### 3. Importance of Execute Permission
A script cannot run unless it has **execute permission**.

Without execute permission:

./script.sh

Error:
Permission denied

After adding execute permission:

chmod +x script.sh

The script runs successfully.

---

# Conclusion

This challenge helped me understand how Linux manages file security using permissions.  
Learning to modify and test permissions is an essential skill for **Linux administration and DevOps**, especially when managing servers, scripts, and configuration files.

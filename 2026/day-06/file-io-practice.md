# Day 06 – Linux Fundamentals: Read and Write Text Files

## Purpose
The purpose of Day 06 was to practice basic file read and write operations using
fundamental Linux commands. This practice is important for handling logs,
configuration files, and scripts in real DevOps environments.

## Environment
- OS: Ubuntu (WSL)
- Working Directory: 90DaysOfDevOps/2026/day-06

## What I Practiced Today

### 1. Checking existing directories and disk usage
I first checked the existing folders and their sizes to understand the current
directory structure and disk usage.

Commands used:
ls 
du -sh *

Learning:
- `ls` lists directories and files
- `du -sh *` shows the size of each folder in a readable format

### 2. Creating Day 06 directory and notes file
I created a new folder for Day 06 and an empty text file.

Commands used:
mkdir day-06 
touch notes.txt

Learning:
- `mkdir` creates a new directory
- `touch` creates an empty file

### 3. Moving file into Day 06 directory
I moved the notes file into the Day 06 folder and switched to that directory.

Commands used:
mv notes.txt day-06/ 
cd day-06 
ls

Learning:
- `mv` is used to move files
- `cd` changes the working directory

### 4. Writing content to file using redirection
I wrote the first line into the file using overwrite redirection.

Command used:
echo "This is my Day 06 of Linux practice." > notes.txt

Learning:
- `>` overwrites existing content in a file

### 5. Appending content to file
I added another line without removing existing content.

Command used:
echo "Learning file read and write commands." >> notes.txt

Learning:
- `>>` appends content safely to a file

### 6. Writing using tee command
I used the tee command to display output on terminal and write it to the file
at the same time.

Command used:
echo "These commands are useful for logs and configs." | tee -a notes.txt

Learning:
- `tee` is useful when you want to see output and save it together

### 7. Reading full file content
I displayed the entire file content.

Command used:
cat notes.txt

Output:
This is my Day 06 of Linux practice.
Learning file read and write commands. 
These commands are useful for logs and configs.

Learning:
- `cat` shows the complete content of a file

### 8. Reading partial file content
I checked specific parts of the file using head and tail.

Commands used:
head -n 2 notes.txt 
tail -n 2 notes.txt 
tail -n 5 notes.txt

Learning:
- `head` is useful to see the beginning of a file
- `tail` is useful for checking recent logs or last lines

## Final Learning
- Learned difference between `>` and `>>`
- Understood practical use of `tee`
- Practiced reading files efficiently using `cat`, `head`, and `tail`
- These commands are commonly used while working with logs and configs

## Conclusion
This practice helped strengthen my understanding of basic Linux file handling.
These fundamentals are essential for troubleshooting, automation, and daily
DevOps tasks.

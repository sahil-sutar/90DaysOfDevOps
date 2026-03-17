# Day 18 – Shell Scripting: Functions & Intermediate Concepts

## Overview
Today I learned how to write cleaner and reusable shell scripts using functions, strict mode, and real-world scripting patterns.

---

## Task 1: Basic Functions

### Script: functions.sh

    #!/bin/bash

    greet() {
        echo "Hello, $1!"
    }

    add() {
        sum=$(( $1 + $2 ))
        echo "The addition of two numbers is $sum"
    }

    greet "$1"
    add 10 5

### Output

    Hello India!
    The addition of two numbers is 15

---

## Task 2: Disk & Memory Check

### Script: disk_check.sh

    #!/bin/bash

    check_disk() {
        usage=$(df -h / | awk 'NR==2 {print $5}')
        echo "Disk usage is: $usage"
    }

    check_memory() {
        mem=$(free -h | awk 'NR==2 {print $4}')
        echo "Available memory: $mem"
    }

    check_disk
    check_memory

### Output

    Disk usage is: 27%
    Available memory: 377Mi

---

## Task 3: Strict Mode

### Script: strict_demo.sh

    #!/bin/bash
    set -euo pipefail

    echo "Strict mode enabled"

    # Uncomment to test
    # echo $UNDEFINED_VAR
    # false
    # cat file.txt | grep text

### Explanation

- set -e → Exit immediately if any command fails  
- set -u → Error when using undefined variables  
- set -o pipefail → Fail if any command in a pipeline fails  

---

## Task 4: Local Variables

### Script: local_demo.sh

    #!/bin/bash

    echo "--- Testing Local Variables ---"

    test_local() {
        local var="I am hidden"
        echo "Inside local function: $var"
    }

    test_global() {
        var="I am everywhere"
        echo "Inside global function: $var"
    }

    test_local
    echo "Outside local function: '$var'"

    echo ""

    echo "--- Testing Global Variables ---"

    test_global
    echo "Outside global function: '$var'"

### Output

    --- Testing Local Variables ---
    Inside local function: I am hidden
    Outside local function: ''

    --- Testing Global Variables ---
    Inside global function: I am everywhere
    Outside global function: 'I am everywhere'

---

## Task 5: System Info Reporter

### Script: system_info.sh

    #!/bin/bash
    set -euo pipefail

    print_header() {
        echo -e "\n=============================="
        echo "$1"
        echo "=============================="
    }

    system_info() {
        print_header "Hostname & OS Info"
        hostnamectl
    }

    uptime_info() {
        print_header "System Uptime"
        uptime
    }

    memory_usage() {
        print_header "Memory Usage"
        free -h
    }

    cpu_usage() {
        print_header "Top 5 CPU-Consuming Processes"
        ps -eo %cpu,%mem,cmd --sort=-%cpu | head -n 6
    }

    disk_usage() {
        print_header "Top 5 Largest Directories (Root)"
        du -ah / 2>/dev/null | sort -rh | head -n 5
    }

    main() {
        echo "******** SYSTEM INFORMATION REPORT ********"
        echo "Date: $(date)"
        system_info
        uptime_info
        memory_usage
        cpu_usage
        disk_usage
    }

    main

### Output (Sample)

    ******** SYSTEM INFORMATION REPORT ********
    Date: 2026-03-17

    ==============================
    Hostname & OS Info
    ==============================
    Ubuntu 24.04

    ==============================
    System Uptime
    ==============================
    up 1 hour

    ==============================
    Memory Usage
    ==============================
    Mem: 914Mi total, 350Mi used, 312Mi free

    ==============================
    Top 5 CPU-Consuming Processes
    ==============================
    ...

    ==============================
    Top 5 Largest Directories (Root)
    ==============================
    ...

---

## What I Learned

- Functions make scripts reusable and clean  
- Strict mode improves script reliability  
- Local variables prevent unexpected bugs  

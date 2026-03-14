# Day 17 – Shell Scripting: Loops, Arguments & Error Handling

## Task
Level up your scripting by using loops, handling arguments, automating package installation, and adding basic error handling.

---

# Task 1: For Loop

## for_loop.sh

```bash
#!/bin/bash

fruits=("Apple" "Banana" "Mango" "Orange" "Grapes")

for fruit in "${fruits[@]}"
do
    echo "Fruit: $fruit"
done
```

### Output

```
Fruit: Apple
Fruit: Banana
Fruit: Mango
Fruit: Orange
Fruit: Grapes
```

---

## count.sh

```bash
#!/bin/bash

for i in {1..10}
do
    echo $i
done
```

### Output

```
1
2
3
4
5
6
7
8
9
10
```

---

# Task 2: While Loop

## countdown.sh

```bash
#!/bin/bash

read -p "Enter a number: " NUMBER

while [ $NUMBER -ge 0 ]
do
    echo $NUMBER
    ((NUMBER--))
done

echo "Done!"
```

### Output

```
Enter a number: 5
5
4
3
2
1
0
Done!
```

---

# Task 3: Command-Line Arguments

## greet.sh

```bash
#!/bin/bash

if [ -z "$1" ]
then
    echo "Usage: ./greet.sh <name>"
else
    echo "Hello, $1!"
fi
```

### Output

```
./greet.sh Sahil
Hello, Sahil!
```

If no argument:

```
Usage: ./greet.sh <name>
```

---

## args_demo.sh

```bash
#!/bin/bash

echo "Script name: $0"
echo "Total arguments: $#"
echo "All arguments: $@"
```

### Output

```
./args_demo.sh apple banana mango

Script name: ./args_demo.sh
Total arguments: 3
All arguments: apple banana mango
```

---

# Task 4: Install Packages via Script

## install_packages.sh

```bash
#!/bin/bash

if [ "$EUID" -ne 0 ]
then
    echo "Please run this script as root"
    exit 1
fi

packages=("nginx" "curl" "wget")

for pkg in "${packages[@]}"
do
    if dpkg -s "$pkg" &> /dev/null
    then
        echo "$pkg is already installed. Skipping..."
    else
        echo "$pkg is not installed. Installing..."
        apt install -y "$pkg"
    fi
done

echo "Package installation completed."
```

### Output Example

```
nginx is already installed. Skipping...
curl is already installed. Skipping...
wget is already installed. Skipping...
Package installation completed.
```

---

# Task 5: Error Handling

## safe_script.sh

```bash
#!/bin/bash

set -e

mkdir /tmp/devops-test || echo "Directory already exists"

cd /tmp/devops-test || echo "Failed to enter directory"

touch testfile.txt || echo "Failed to create file"

echo "Script completed successfully"
```

### Output

```
Directory already exists
Script completed successfully
```

---

# What I Learned

- How to write **for loops and while loops in Bash**
- How to use **command-line arguments ($1, $# , $@)**
- How to implement **basic error handling using set -e and ||**

---

# Conclusion

Day 17 helped me understand how shell scripting can automate repetitive tasks using loops, arguments, and error handling. These are important skills for DevOps engineers to build automation and manage systems efficiently.

---

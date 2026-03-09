# Day 13 – Linux Volume Management (LVM)

## Objective
Learn how to manage storage in Linux using Logical Volume Manager (LVM).  
In this challenge, I created Physical Volumes, Volume Groups, Logical Volumes, mounted them, and extended the storage.

---

## Task 1: Check Current Storage

### Commands Used
```bash
lsblk
pvs
vgs
lvs
df -h
```

### Output
(Add screenshot of the outputs here)

---

## Task 2: Create Physical Volumes

### Commands
```bash
pvcreate /dev/nvme1n1
pvcreate /dev/nvme2n1
```

### Verify
```bash
pvs
```

### Output
(Add screenshot here)

---

## Task 3: Create Volume Group

### Command
```bash
vgcreate devops-vg /dev/nvme1n1 /dev/nvme2n1
```

### Verify
```bash
vgs
```

### Output
(Add screenshot here)

---

## Task 4: Create Logical Volume

### Command
```bash
lvcreate -L 500M -n App-data devops-vg
```

### Verify
```bash
lvs
```

### Output
(Add screenshot here)

---

## Task 5: Format and Mount Logical Volume

### Format Filesystem
```bash
mkfs.ext4 /dev/devops-vg/App-data
```

### Create Mount Directory
```bash
mkdir -p /mnt/app-data
```

### Mount Volume
```bash
mount /dev/devops-vg/App-data /mnt/app-data
```

### Verify Mount
```bash
df -h
```

### Output
(Add screenshot here)

---

## Task 6: Extend Logical Volume

### Extend Volume
```bash
lvextend -L +200M /dev/devops-vg/App-data
```

### Resize Filesystem
```bash
resize2fs /dev/devops-vg/App-data
```

### Verify New Size
```bash
df -h
```

### Output
(Add screenshot here)

---

## What I Learned

1. Logical Volume Manager (LVM) allows flexible storage management in Linux.
2. Storage can be extended dynamically without stopping applications.
3. LVM works in three layers: Physical Volume (PV) → Volume Group (VG) → Logical Volume (LV).

---

## Conclusion

This challenge helped me understand how DevOps engineers manage scalable and flexible storage in Linux systems.  
LVM allows storage expansion without downtime, which is important in real production environments.

---

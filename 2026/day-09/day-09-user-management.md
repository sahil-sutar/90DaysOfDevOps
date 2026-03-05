# Day 09 Challenge

## Users & Groups Created

Users:
- tokyo
- berlin
- professor
- nairobi

Groups:
- developers
- admins
- project-team

---

## Group Assignments

tokyo:
- developers
- project-team

berlin:
- developers
- admins

professor:
- admins

nairobi:
- project-team

---

## Directories Created

/opt/dev-project  
- Group: developers  
- Permissions: 775 (rwxrwxr-x)

/opt/team-workspace  
- Group: project-team  
- Permissions: 775 (rwxrwxr-x)

---

## Commands Used

```bash
# Create users
sudo useradd -m tokyo
sudo useradd -m berlin
sudo useradd -m professor
sudo useradd -m nairobi

# Set passwords
sudo passwd tokyo
sudo passwd berlin
sudo passwd professor
sudo passwd nairobi

# Create groups
sudo groupadd developers
sudo groupadd admins
sudo groupadd project-team

# Add users to groups
sudo usermod -aG developers tokyo
sudo usermod -aG developers berlin
sudo usermod -aG admins berlin
sudo usermod -aG admins professor
sudo usermod -aG project-team tokyo
sudo usermod -aG project-team nairobi

# Create shared directories
sudo mkdir /opt/dev-project
sudo mkdir /opt/team-workspace

# Set group ownership
sudo chown :developers /opt/dev-project
sudo chown :project-team /opt/team-workspace

# Set permissions
sudo chmod 775 /opt/dev-project
sudo chmod 775 /opt/team-workspace

# Verify groups
groups tokyo
groups berlin
groups professor
groups nairobi

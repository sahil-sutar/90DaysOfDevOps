# Day 11 Challenge – File Ownership (chown & chgrp)

## Files & Directories Created

Files:
- devops-file.txt
- team-notes.txt
- project-config.yaml
- heist-project/vault/gold.txt
- heist-project/plans/strategy.conf
- bank-heist/access-codes.txt
- bank-heist/blueprints.pdf
- bank-heist/escape-plan.txt

Directories:
- app-logs/
- heist-project/
- heist-project/vault/
- heist-project/plans/
- bank-heist/

Groups Created:
- heist-team
- planners
- vault-team
- tech-team

---

## Ownership Changes

- devops-file.txt: ubuntu:ubuntu → tokyo:ubuntu → berlin:ubuntu  
- team-notes.txt: ubuntu:ubuntu → ubuntu:heist-team  
- project-config.yaml: ubuntu:ubuntu → professor:heist-team  
- app-logs/: ubuntu:ubuntu → berlin:heist-team  
- bank-heist/access-codes.txt: ubuntu:ubuntu → tokyo:vault-team  
- bank-heist/blueprints.pdf: ubuntu:ubuntu → berlin:tech-team  
- bank-heist/escape-plan.txt: ubuntu:ubuntu → nairobi:vault-team  

---

## Commands Used

```bash
touch devops-file.txt
ls -l devops-file.txt

sudo chown tokyo devops-file.txt
sudo chown berlin devops-file.txt

touch team-notes.txt
ls -l team-notes.txt

sudo groupadd heist-team
sudo chgrp heist-team team-notes.txt

touch project-config.yaml
sudo chown professor:heist-team project-config.yaml

mkdir app-logs/
sudo chown berlin:heist-team app-logs/

mkdir -p heist-project/vault
mkdir -p heist-project/plans

touch heist-project/vault/gold.txt
touch heist-project/plans/strategy.conf

sudo groupadd planners
sudo chown professor:planners heist-project/

ls -lR heist-project/

sudo groupadd vault-team
sudo groupadd tech-team

mkdir bank-heist/

touch bank-heist/access-codes.txt
touch bank-heist/blueprints.pdf
touch bank-heist/escape-plan.txt

sudo chown tokyo:vault-team bank-heist/access-codes.txt
sudo chown berlin:tech-team bank-heist/blueprints.pdf
sudo chown nairobi:vault-team bank-heist/escape-plan.txt

ls -l bank-heist/

```

## What I Learned

- Understanding File Ownership in Linux

In Linux, every file and directory is associated with an owner (user) and a group. The owner is the user who has primary control over the file, while the group allows multiple users to share access to the file depending on the permissions assigned. This ownership model helps maintain security and proper access control in multi-user systems.

- Using chown and chgrp to Manage Ownership

The chown command is used to change the owner of a file or directory and can also change both the owner and group at the same time using the syntax owner:group. The chgrp command is used specifically to change the group ownership. These commands are important for managing access rights and ensuring that the correct users and teams can access project files.

- Importance of Ownership in DevOps Workflows

Proper file ownership is very important in real-world DevOps environments. Applications, configuration files, logs, and deployment directories often require specific users or groups to access them. Incorrect ownership can cause permission errors, application failures, or issues in CI/CD pipelines. Learning how to manage file ownership ensures smoother deployments and better system security.

---

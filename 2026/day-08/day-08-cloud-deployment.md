# Day 08 – Cloud Server Setup: Docker, Nginx & Web Deployment

## Cloud Provider Used
AWS EC2 (Free Tier)

---

## Commands Used

### Connect to Server
ssh -i my-key.pem ubuntu@<public-ip>

### Update System
sudo apt update
sudo apt upgrade -y

### Install Nginx
sudo apt install nginx -y

### Check Nginx Status
sudo systemctl status nginx

### Enable Nginx on Boot
sudo systemctl enable nginx

### Check Server IP
curl ifconfig.me

### View Logs
sudo journalctl -u nginx -n 50
sudo cat /var/log/nginx/access.log

### Save Logs to File
sudo cp /var/log/nginx/access.log ~/nginx-logs.txt

### Download Logs to Local Machine
scp -i my-key.pem ubuntu@<public-ip>:~/nginx-logs.txt .

---

## Security Group Configuration

- Allowed inbound HTTP (Port 80)
- Allowed SSH (Port 22)

---

## Challenges Faced

- Webpage was not loading at first.
- Realized port 80 was not open in security group.
- After allowing HTTP traffic, Nginx page worked.

---

## What I Learned

- How to launch and connect to a cloud instance
- How to install and manage Nginx
- Importance of security groups
- How to read and extract logs
- Basic cloud troubleshooting flow

---

## Why This Matters

- Real server management experience
- Understanding public IP and firewall rules
- Log-based debugging
- Core production skill for DevOps

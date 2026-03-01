# Day 05 – Linux Troubleshooting Runbook (SSH Service)

## 🎯 Target Service
ssh (OpenSSH Daemon)

---

## 🖥️ Environment Check

Verified kernel version, architecture, and Ubuntu OS details.

![Environment Check](env-chech.jpg)

---

## 🧠 Memory Check

Checked system memory usage using `free -h` and `top`.

![Memory Check](Memory-Check.png)

---

## 💾 Disk Usage Check

Verified available disk space using `df -h`.

![Disk Usage](Disk.png)

---

## 🔎 Process Check

Checked running SSH process using `ps aux | grep ssh`.

![Process Check](Process-Check.png)

---

## 🌐 Port Listening Check

Verified SSH is listening on port 22 using `ss -tulpn`.

![Port Listening](Port-Listening-Check.png)

---

## 📜 SSH Service Logs

Checked logs using:

`sudo journalctl -u ssh -n 50`

![SSH Logs](SSH-Service-Logs.png)

---

## 🌍 Connectivity / Web Check

Verified connectivity and service behavior.

![Web Check](Web-check.png)

---

## ⚠️ Connection Observation

Observed session disconnect behavior.

![Connection Issue](connection.png)

---

## 📚 Key Learnings

- Always check logs before restarting services  
- Validate system health before assuming failure  
- Structured troubleshooting saves time  
- Never blindly restart SSH on production  

---

#90DaysOfDevOps  
#DevOpsKaJosh  
#TrainWithShubham

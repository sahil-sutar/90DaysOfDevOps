Day 04 – Linux Practice: Processes and Services

👀 Process Checks

1] ps aux | head -10

Observation:
Shows all running processes with CPU and memory usage.

2]top

Observation:
Real-time CPU and memory usage.
Useful to detect high CPU consuming process.

3] pgrep ssh

Output:
1023

Observation:
Returns PID of ssh process if running.

⚙️ Service Checks

1] sudo systemctl status ssh

Output:
ssh.service - OpenBSD Secure Shell server
Active: active (running)

Observation:
SSH service is running successfully.

2] systemctl list-units

Observation:
Displays all active running services.

📜 Log Checks

1] journalctl -u nginx

Observation:
Shows log entries of nginx service.

2] tail -n 20 /var/log/syslog

Observation:
Useful for troubleshooting system issues.

🔧 Mini Troubleshooting Flow

Problem:
SSH service not responding.

Steps Taken:
1. Checked service status → sudo systemctl status ssh
2. Restarted service → sudo systemctl restart ssh
3. Checked logs → journalctl -u ssh
4. Verified port listening → sudo ss -tulnp | grep 22

Result:
Service restarted successfully and is running.



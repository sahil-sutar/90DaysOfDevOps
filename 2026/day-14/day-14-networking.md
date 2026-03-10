# Day 14 – Networking Fundamentals & Hands-on Checks

## Quick Concepts

### OSI Model vs TCP/IP Model

**OSI Model (7 Layers)**  
1. Physical – Handles cables, signals, and hardware transmission.  
2. Data Link – Responsible for MAC addressing and switching.  
3. Network – Handles IP addressing and routing between networks.  
4. Transport – Provides end-to-end communication using TCP or UDP.  
5. Session – Manages sessions between applications.  
6. Presentation – Handles data format, encryption, and compression.  
7. Application – Provides services directly used by applications like HTTP and DNS.

**TCP/IP Model (4 Layers)**  
1. Link Layer – Network interface communication (Ethernet, Wi-Fi).  
2. Internet Layer – Handles IP addressing and routing.  
3. Transport Layer – Handles communication using TCP or UDP.  
4. Application Layer – Protocols like HTTP, HTTPS, DNS, FTP operate here.

### Where Common Protocols Sit

- **IP** → Internet Layer  
- **TCP / UDP** → Transport Layer  
- **HTTP / HTTPS** → Application Layer  
- **DNS** → Application Layer  

### Example

Example request:


curl https://example.com


Application Layer → HTTP/HTTPS  
Transport Layer → TCP  
Internet Layer → IP  

This shows how a web request moves through the network stack.

---

# Hands-on Checklist

**Target Host:** google.com

---

## 1. Identity

**Command**


hostname -I


or


ip addr show


**Observation**

This command displays the private IP address assigned to my machine (for example 192.168.x.x). This IP is used for communication within the local network.

---

## 2. Reachability

**Command**


ping google.com


**Observation**

The host responded successfully with low latency (~20–40 ms) and no packet loss, confirming that my system has internet connectivity.

---

## 3. Path to Destination

**Command**


traceroute google.com


or


tracepath google.com


**Observation**

The output shows multiple network hops between my machine and Google’s servers. Some hops may have slightly higher latency depending on routing distance.

---

## 4. Ports / Listening Services

**Command**


ss -tulpn


or


netstat -tulpn


**Observation**

One listening service detected is **SSH running on port 22**, which allows remote login access to the system.

---

## 5. Name Resolution (DNS)

**Command**


dig google.com


or


nslookup google.com


**Observation**

The domain google.com successfully resolves to a public IP address (for example 142.250.x.x), confirming DNS resolution is working properly.

---

## 6. HTTP Check

**Command**


curl -I https://google.com


**Observation**

The response returned an HTTP status code such as **200 OK** or **301 Moved Permanently**, indicating that the web server is reachable.

---

## 7. Connection Snapshot

**Command**


netstat -an | head


**Observation**

The output displays multiple connection states such as **LISTEN** and **ESTABLISHED**. LISTEN indicates services waiting for connections, while ESTABLISHED indicates active network connections.

---

# Mini Task – Port Probe & Interpret

## Identify Listening Port

**Command**


ss -tulpn


Example listening service:

SSH service running on **port 22**.

---

## Test the Port

**Command**


nc -zv localhost 22


**Result**

Connection to port 22 succeeded, which confirms that the SSH service is reachable on the local system.

If the port was not reachable, the next checks would be:

Check service status:


sudo systemctl status ssh


Check firewall rules:


sudo ufw status


Verify if the service is listening:


ss -tulpn | grep 22


---

# Reflection

### 1. Which command gives the fastest signal when something is broken?

The **ping command** usually provides the fastest signal when something is wrong because it quickly checks basic connectivity and packet loss.

### 2. What layer would you inspect next?

If **DNS fails**, I would inspect the **Application Layer** and verify DNS configuration.  
If an **HTTP 500 error occurs**, I would investigate the **Application Layer**, focusing on the web server or application logs.

### 3. Two follow-up checks in a real incident

1. Verify DNS resolution using:


dig domain.com


2. Check open ports and services using:


ss -tulpn

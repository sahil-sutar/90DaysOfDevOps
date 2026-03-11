# Day 15 – Networking Concepts: DNS, IP, Subnets & Ports

## Task 1: DNS – How Names Become IPs

When we type `google.com` in a browser, the system first checks its local DNS cache.  
If the IP is not found, it asks a DNS resolver which queries DNS servers to find the IP address.  
The DNS server returns the IP mapped to the domain name.  
The browser then uses this IP to connect to Google's server.

### DNS Record Types

A Record – Maps a domain name to an IPv4 address.  
AAAA Record – Maps a domain name to an IPv6 address.  
CNAME Record – Creates an alias from one domain to another domain.  
MX Record – Specifies the mail server responsible for receiving emails for a domain.  
NS Record – Indicates the authoritative name servers for a domain.

### Command Used


dig google.com


### Example Output (Important Fields)


;; ANSWER SECTION:
google.com. 300 IN A 142.250.182.14


A Record IP: `142.250.182.14`  
TTL: `300`

---

# Task 2: IP Addressing

### What is an IPv4 Address?

An IPv4 address is a unique 32-bit numeric identifier assigned to devices on a network.  
It is written in dotted decimal format such as `192.168.1.10`.  
It contains four numbers (octets), each ranging from 0 to 255.

### Public vs Private IP

Public IP  
Used on the internet and globally unique.  
Example: `8.8.8.8`

Private IP  
Used inside local networks and not accessible directly from the internet.  
Example: `192.168.1.5`

### Private IP Ranges


10.0.0.0 – 10.255.255.255
172.16.0.0 – 172.31.255.255
192.168.0.0 – 192.168.255.255


### Command Used


ip addr show


Example Output:


inet 192.168.56.101/24


Private IP detected: `192.168.56.101`

---

# Task 3: CIDR & Subnetting

### What does /24 mean?

`/24` means the first **24 bits are used for the network portion** of the IP address.  
The remaining **8 bits are used for host addresses**.

Example:
`192.168.1.0/24`

### Usable Hosts

/24 → 254 usable hosts  
/16 → 65,534 usable hosts  
/28 → 14 usable hosts  

### Why do we subnet?

Subnetting helps divide large networks into smaller logical networks.  
This improves security, network performance, and IP address management.

### CIDR Table

| CIDR | Subnet Mask | Total IPs | Usable Hosts |
|-----|-------------|-----------|--------------|
| /24 | 255.255.255.0 | 256 | 254 |
| /16 | 255.255.0.0 | 65,536 | 65,534 |
| /28 | 255.255.255.240 | 16 | 14 |

---

# Task 4: Ports – The Doors to Services

### What is a Port?

A port is a logical communication endpoint used by applications to send and receive data over a network.  
Ports allow multiple services to run on the same IP address.

### Common Ports

| Port | Service |
|-----|--------|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |
| 53 | DNS |
| 3306 | MySQL |
| 6379 | Redis |
| 27017 | MongoDB |

### Command Used


ss -tulpn


Example Output


tcp LISTEN 0 128 0.0.0.0:22
tcp LISTEN 0 128 0.0.0.0:80


Listening Ports:

Port 22 → SSH service  
Port 80 → HTTP service

---

# Task 5: Putting It Together

### Scenario 1

Command:

curl http://myapp.com:8080


Networking concepts involved:

DNS resolves `myapp.com` into an IP address.  
The request connects to port `8080` on that server.  
HTTP protocol is used to retrieve the response from the application server.

### Scenario 2

Database connection:

10.0.1.50:3306


Things to check first:

Verify network connectivity to the IP address.  
Ensure MySQL service is running on port `3306`.  
Check firewall rules or security groups blocking the connection.

---

# What I Learned

1. DNS translates human-readable domain names into IP addresses.
2. CIDR notation helps define network size and host capacity.
3. Ports allow multiple services like SSH, HTTP, and databases to run on a single server.

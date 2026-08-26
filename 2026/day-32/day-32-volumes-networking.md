# Day 32: Docker Volumes & Networking — Persistence & Inter-Container Communication

## 📋 Challenge Overview
- **Goal:** Solve two foundational container challenges: **Data Persistence** and **Inter-Container Communication**.
- **Core Realities:** 
  1. Containers are *ephemeral* by design — writable layer data is discarded when a container is removed.
  2. By default, containers on the default bridge network lack built-in DNS name resolution and cannot easily communicate by hostname.
- **Challenge Series:** #90DaysOfDevOps | #DevOpsKaJosh | #TrainWithShubham

---

## 🛠️ Task 1: The Problem — Ephemeral Containers & Data Loss

### Objective
Demonstrate that removing a container deletes all data stored within its writable layer unless persistent storage is explicitly attached.

### Step 1: Run an unmounted MySQL container
```bash
docker run -d \
  --name mysql-ephemeral \
  -e MYSQL_ROOT_PASSWORD=test@123 \
  -e MYSQL_DATABASE=appdb \
  -p 3306:3306 \
  mysql:8.0
```

### Step 2: Insert sample data
```bash
docker exec -i mysql-ephemeral mysql -u root -ptest@123 appdb <<EOF
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL
);

INSERT INTO users (name, email) VALUES
  ('Alice Johnson', 'alice@example.com'),
  ('Bob Smith', 'bob@example.com');

SELECT * FROM users;
EOF
```

*Output:*
```text
+----+---------------+-------------------+
| id | name          | email             |
+----+---------------+-------------------+
|  1 | Alice Johnson | alice@example.com |
|  2 | Bob Smith     | bob@example.com   |
+----+---------------+-------------------+
```

### Step 3: Stop and remove the container
```bash
docker stop mysql-ephemeral
docker rm mysql-ephemeral
```

### Step 4: Run a fresh container with the exact same name
```bash
docker run -d \
  --name mysql-ephemeral \
  -e MYSQL_ROOT_PASSWORD=test@123 \
  -e MYSQL_DATABASE=appdb \
  -p 3306:3306 \
  mysql:8.0
```

### Step 5: Check if data survived
```bash
docker exec -i mysql-ephemeral mysql -u root -ptest@123 appdb -e "SELECT * FROM users;"
```

*Output:*
```text
ERROR 1146 (42S02) at line 1: Table 'appdb.users' doesn't exist
```

### 📝 What Happened and Why?
- **What happened:** The `users` table and all records completely disappeared.
- **Why:** Docker containers use an ephemeral writable container layer (via Copy-on-Write storage drivers like `overlay2`). When a container is removed (`docker rm`), its top writable layer is permanently purged from the host filesystem. Without a volume, database files created inside `/var/lib/mysql` do not survive container deletion.

```bash
docker rm -f mysql-ephemeral
```

---

## 💾 Task 2: Named Volumes — Reliable Persistent Storage

### Objective
Create a Docker-managed named volume, persist database records across container lifecycles, and verify volume metadata.

### Step 1: Create and inspect a named volume
```bash
# Create volume
docker volume create mysql_data

# List volumes
docker volume ls

# Inspect volume metadata
docker volume inspect mysql_data
```

*Volume metadata output:*
```json
[
    {
        "CreatedAt": "2026-08-26T10:00:00Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/mysql_data/_data",
        "Name": "mysql_data",
        "Options": {},
        "Scope": "local"
    }
]
```

### Step 2: Run a MySQL container attached to the named volume
```bash
docker run -d \
  --name mysql-vol-1 \
  -e MYSQL_ROOT_PASSWORD=test@123 \
  -e MYSQL_DATABASE=company_db \
  -v mysql_data:/var/lib/mysql \
  -p 3306:3306 \
  mysql:8.0
```

### Step 3: Populate sample records
```bash
docker exec -i mysql-vol-1 mysql -u root -ptest@123 company_db <<EOF
CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50),
    role VARCHAR(50)
);

INSERT INTO employees (name, role) VALUES 
  ('Devin', 'DevOps Engineer'),
  ('Sara', 'Cloud Architect');

SELECT * FROM employees;
EOF
```

### Step 4: Destroy the container
```bash
docker rm -f mysql-vol-1
```

### Step 5: Launch a completely new container pointing to `mysql_data`
```bash
docker run -d \
  --name mysql-vol-2 \
  -e MYSQL_ROOT_PASSWORD=test@123 \
  -v mysql_data:/var/lib/mysql \
  -p 3306:3306 \
  mysql:8.0
```

### Step 6: Verify data persistence
```bash
docker exec -i mysql-vol-2 mysql -u root -ptest@123 -e "SELECT * FROM company_db.employees;"
```

*Output:*
```text
+----+-------+-----------------+
| id | name  | role            |
+----+-------+-----------------+
|  1 | Devin | DevOps Engineer |
|  2 | Sara  | Cloud Architect |
+----+-------+-----------------+
```

### 📝 Key Takeaway
- **Is the data still there?** **Yes.**
- The data is stored in the host filesystem under `/var/lib/docker/volumes/mysql_data/_data`. Named volumes exist outside the container lifecycle and remain intact even when containers are stopped, killed, or removed.

---

## 📂 Task 3: Bind Mounts — Live Code Sharing & Development

### Objective
Mount a specific host folder into an Nginx web container to serve an HTML page and observe immediate updates without container rebuilds or restarts.

### Step 1: Create local directory and index.html
```bash
mkdir -p ~/nginx-demo && cd ~/nginx-demo

cat <<EOF > index.html
<!DOCTYPE html>
<html>
<head>
    <title>Docker Bind Mount Demo</title>
</head>
<body style="font-family: Arial, sans-serif; text-align: center; margin-top: 50px;">
    <h1 style="color: #0288d1;">Hello from Host Bind Mount!</h1>
    <p>Version 1.0 — Initial Deployment</p>
</body>
</html>
EOF
```

### Step 2: Run Nginx container with bind mount
```bash
docker run -d \
  --name nginx-web \
  -p 8080:80 \
  -v "$(pwd)":/usr/share/nginx/html:ro \
  nginx:alpine
```

### Step 3: Access the page
```bash
curl http://localhost:8080
```

### Step 4: Update the file directly on host
```bash
cat <<EOF > index.html
<!DOCTYPE html>
<html>
<head>
    <title>Docker Bind Mount Demo</title>
</head>
<body style="font-family: Arial, sans-serif; text-align: center; margin-top: 50px; background-color: #e8f5e9;">
    <h1 style="color: #2e7d32;">Updated Instantly from Host!</h1>
    <p>Version 2.0 — Live reload without container restart.</p>
</body>
</html>
EOF
```

### Step 5: Verify updated content
```bash
curl http://localhost:8080
```

### 📝 Notes: Named Volume vs. Bind Mount

| Feature | Named Volume (`-v volume_name:/path`) | Bind Mount (`-v /host/path:/path`) |
| :--- | :--- | :--- |
| **Location on Host** | Managed by Docker daemon (`/var/lib/docker/volumes/`) | Explicit user-defined path anywhere on the host filesystem |
| **Management via CLI**| Managed with `docker volume` commands (`create`, `ls`, `prune`) | Managed using regular host OS file tools |
| **Host System Coupling**| Fully abstracted and portable across operating systems | Highly tied to host directory layout, permissions, and paths |
| **Initial Container Files** | Automatically copies existing container files into the volume on creation | Overrides container folder with host folder contents directly |
| **Primary Use Cases** | Databases (MySQL, PostgreSQL, MongoDB), stateful production persistence | Local development environments, live code editing, config file mounting |

---

## 🌐 Task 4: Docker Networking Basics — Default Bridge Limitations

### Objective
Explore Docker's network architecture and demonstrate why containers on the default bridge network cannot communicate by hostname.

### Step 1: List all Docker networks
```bash
docker network ls
```

*Output:*
```text
NETWORK ID     NAME      DRIVER    SCOPE
1b635fb8997a   bridge    bridge    local
8ce6605b0d87   host      host      local
9622d9c02d6b   none      null      local
```

### Step 2: Inspect default bridge network
```bash
docker network inspect bridge
```
*Key configuration points:*
- Subnet: `172.17.0.0/16`
- Gateway: `172.17.0.1`

### Step 3: Run two containers on default bridge
```bash
docker run -dit --name c1 alpine sh
docker run -dit --name c2 alpine sh
```

### Step 4: Test 1 — Ping by container name
```bash
docker exec -it c1 ping -c 2 c2
```
*Result:*
```text
ping: bad address 'c2'
```
❌ **Failed:** The default bridge does not have built-in DNS name resolution enabled.

### Step 5: Test 2 — Ping by IP address
```bash
# Get IP of c2
C2_IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' c2)
echo "c2 IP Address: $C2_IP"

# Ping by IP from c1
docker exec -it c1 ping -c 2 $C2_IP
```
*Result:*
```text
PING 172.17.0.3 (172.17.0.3): 56 data bytes
64 bytes from 172.17.0.3: seq=0 ttl=64 time=0.076 ms
64 bytes from 172.17.0.3: seq=1 ttl=64 time=0.068 ms
```
✅ **Succeeded:** IP-level network packet routing works across the bridge interface.

### Cleanup
```bash
docker rm -f c1 c2
```

---

## 🔗 Task 5: Custom Networks — Automatic Service Discovery

### Objective
Create a user-defined bridge network and verify automatic DNS service discovery between containers.

### Step 1: Create custom network
```bash
docker network create my-app-net
docker network ls
```

### Step 2: Run containers on `my-app-net`
```bash
docker run -dit --name app-c1 --network my-app-net alpine sh
docker run -dit --name app-c2 --network my-app-net alpine sh
```

### Step 3: Test ping by name
```bash
docker exec -it app-c1 ping -c 2 app-c2
```

*Output:*
```text
PING app-c2 (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.068 ms
64 bytes from 172.18.0.3: seq=1 ttl=64 time=0.057 ms
--- app-c2 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
```

```bash
docker exec -it app-c2 ping -c 2 app-c1
```
✅ **Both directions resolve seamlessly!**

### 📝 Notes: Why custom networks allow name-based resolution vs. default bridge

1. **Embedded DNS Resolver (`127.0.0.11`):**
   - User-defined networks start an internal DNS resolver daemon inside every container.
   - When `app-c1` tries to reach `app-c2`, Docker intercepts the request at `127.0.0.11` and dynamically resolves it to `app-c2`'s current IP address (`172.18.0.3`).
2. **Backward Compatibility:**
   - The default `bridge` network maintains legacy behavior from Docker v1.9 and earlier, where DNS service discovery was absent and host mapping relied strictly on static `/etc/hosts` and deprecated `--link` flags.
3. **Network Isolation & Security:**
   - Default bridge pools all containers without explicit boundaries. Custom bridge networks provide strict container isolation, network aliases, and hot-plugging (`docker network connect/disconnect`).

### Cleanup
```bash
docker rm -f app-c1 app-c2
```

---

## 🚀 Task 6: Put It Together — Production-Style Full Stack Isolation

### Objective
Integrate both concepts: deploy a persistent database with a named volume and an application container communicating securely over a custom bridge network.

```
+-------------------------------------------------------------+
|                      my-custom-net                          |
|                                                             |
|   +-------------------+              +------------------+   |
|   |    app-client     | -----------> |     db-server    |   |
|   |  (alpine/curl)    |  DNS Lookup  |     (MySQL 8)    |   |
|   +-------------------+              +--------+---------+   |
+-----------------------------------------------|-------------+
                                                | Mounts
                                       +--------v--------+
                                       |   mysql_db_data |
                                       |  (Named Volume) |
                                       +-----------------+
```

### Step 1: Create a dedicated production network & volume
```bash
docker network create my-custom-net
docker volume create mysql_db_data
```

### Step 2: Run Database on `my-custom-net` with `mysql_db_data` volume
```bash
docker run -d \
  --name db-server \
  --network my-custom-net \
  -e MYSQL_ROOT_PASSWORD=securepass123 \
  -e MYSQL_DATABASE=production_db \
  -v mysql_db_data:/var/lib/mysql \
  mysql:8.0
```

### Step 3: Populate sample records in the database
```bash
# Allow a few seconds for MySQL initialization
sleep 10

docker exec -i db-server mysql -u root -psecurepass123 production_db <<EOF
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    price DECIMAL(10,2)
);

INSERT INTO products (name, price) VALUES 
  ('DevOps Masterclass', 49.99),
  ('Docker in Production', 29.99);
EOF
```

### Step 4: Run an application client on the same network
```bash
docker run -dit \
  --name app-client \
  --network my-custom-net \
  alpine sh
```

### Step 5: Verify the app container reaches the database by hostname
```bash
# Test 1: ICMP Ping by hostname
docker exec -it app-client ping -c 2 db-server

# Test 2: Network port connectivity (Port 3306) using nc
docker exec -it app-client nc -zv -w 3 db-server 3306
```

*Output:*
```text
PING db-server (172.19.0.2): 56 data bytes
64 bytes from 172.19.0.2: seq=0 ttl=64 time=0.083 ms
64 bytes from 172.19.0.2: seq=1 ttl=64 time=0.061 ms

db-server (172.19.0.2:3306) open
```

✅ **Verification Complete:** The application container successfully resolved and connected to `db-server` by container name, while the database writes persist safely in `mysql_db_data`.

---

## 🧹 Complete Cleanup Command Reference
```bash
# Stop and remove all exercise containers
docker rm -f db-server app-client nginx-web mysql-vol-2 2>/dev/null

# Clean up custom networks
docker network rm my-app-net my-custom-net 2>/dev/null

# (Optional) Clean up volumes if complete teardown is desired
# docker volume rm mysql_data mysql_db_data
```

---

## 🌟 Learn in Public Snippet (LinkedIn / Twitter)
> **Day 32 of #90DaysOfDevOps with @TrainWithShubham** 🚀
> 
> Today tackled the two most common Docker headaches: **data persistence** and **container communication**.
> 
> 💡 **The Ephemeral Aha-Moment:** Removing a database container without a volume means your data is gone forever with its writable layer.
> 
> 🔑 **Key Takeaways:**
> 1. **Named Volumes (`-v name:/path`)**: Best for production databases. Storage lives outside the container lifecycle under Docker daemon management.
> 2. **Bind Mounts (`-v /host:/container`)**: Best for local dev environments and config overrides with instantaneous hot-reloading.
> 3. **Default Bridge vs Custom Networks**: The default bridge lacks built-in DNS! Creating a custom bridge (`docker network create`) activates Docker's embedded DNS server (`127.0.0.11`), allowing containers to discover each other via hostname out of the box.
>
> #DevOps #Docker #DevOpsKaJosh #TrainWithShubham #Containers #Linux

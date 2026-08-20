# Day 29: Docker Basics

---

## 1. What is Docker?

**Docker** is an open-source platform that packages applications and their dependencies into lightweight, portable units called **containers**[cite: 1]. It ensures software runs consistently across development, staging, and production environments by eliminating the "it works on my machine" problem[cite: 1].

---

## 2. Containers vs. Virtual Machines

| Feature | Containers | Virtual Machines (VMs) |
| :--- | :--- | :--- |
| **Architecture** | Shares the host OS kernel[cite: 1] | Runs a separate, full Guest OS[cite: 1] |
| **Hypervisor Needed** | No (uses container engine / daemon)[cite: 1] | Yes (Type 1 or Type 2 Hypervisor)[cite: 1] |
| **Startup Time** | Milliseconds to seconds[cite: 1] | Minutes[cite: 1] |
| **Size Footprint** | Megabytes (MBs)[cite: 1] | Gigabytes (GBs)[cite: 1] |
| **Resource Utilization** | Minimal overhead, dynamically allocated[cite: 1] | Heavy, pre-allocated CPU and RAM[cite: 1] |
| **Isolation** | Process-level (via Linux namespaces & cgroups)[cite: 1] | Hardware-level virtualization[cite: 1] |

---

## 3. Docker Architecture

+-------------------------+             +---------------------------------------+             +-------------------+
|      Docker Client      |             |              Docker Host              |             |  Docker Registry  |
|                         |             |                                       |             |   (Docker Hub)    |
|   $ docker build ...  ---------------> |  [ Docker Daemon (dockerd) ]          |             |                   |
|   $ docker pull ...   ---------------> |      |                                | <---------> |  nginx:latest     |
|   $ docker run ...    ---------------> |      +---> Local Images               |   (pull)    |  ubuntu:latest    |
|                         |             |      |       (nginx, ubuntu)          |             +-------------------+
+-------------------------+             |      |                                |
                                        |      +---> Containers                 |
                                        |              [my-nginx]  [my-ubuntu]  |
                                        +---------------------------------------+

* **Docker Client (`docker` CLI):** The primary user interface used to issue commands (e.g., `run`, `build`, `pull`, `ps`)[cite: 1].
* **Docker Daemon (`dockerd`):** The background service listening for API requests to manage containers, images, volumes, and networks[cite: 1].
* **Docker Images:** Read-only blueprints and layer-based templates used to create container instances[cite: 1].
* **Docker Containers:** Isolated, runnable execution instances created from Docker images[cite: 1].
* **Docker Registry:** Central repository (e.g., Docker Hub, AWS ECR) used to store and distribute images[cite: 1].

---

## 4. Key Takeaways & Troubleshooting Notes

* **Foreground vs. Detached (`-d`):** 
  * Default runs in the foreground, locking the terminal and streaming logs[cite: 1].
  * `-d` runs the process in the background and prints the Container ID[cite: 1].
* **Container Lifecycle (PID 1 Rule):**
  * A container lives **only as long as its foreground process (PID 1) is active**[cite: 1].
  * Services like `nginx` stay alive in the background because the web server listens continuously[cite: 1].
  * OS images like `ubuntu` exit immediately on `docker run ubuntu` because the default shell (`/bin/bash`) receives an `EOF` without an interactive terminal[cite: 1].
  * Fix for interactive OS containers: `docker run -it ubuntu` or `docker run -dit --name my-ubuntu ubuntu`[cite: 1].
* **Port Mapping (`-p host:container`):**
  * `-p 8080:80` maps port `8080` on the host to port `80` inside the container[cite: 1].

---

## 5. Cheat Sheet: Essential Commands

### Container Management
```bash
# Run a web server in detached mode with port mapping
docker run -d --name my-nginx -p 8080:80 nginx

# Run an interactive Ubuntu terminal
docker run -it --name my-ubuntu ubuntu

# List active containers
docker ps

# List all containers (including stopped/exited ones)
docker ps -a

# Stop a running container
docker stop <container_name_or_id>

# Remove a stopped container
docker rm <container_name_or_id>

# Force-remove a running container
docker rm -f <container_name_or_id>

# Remove all stopped containers
docker container prune
```

### Logs, Debugging & Inspection
```bash
# View and follow real-time logs
docker logs -f <container_name_or_id>

# Open a shell inside an already running container
docker exec -it <container_name_or_id> /bin/bash

# View real-time container resource usage (CPU/RAM)
docker stats <container_name_or_id>

# Inspect low-level JSON metadata (IP, mounts, status)
docker inspect <container_name_or_id>
```

### Image Management
```bash
# List locally cached images
docker images

# Pull an image from Docker Hub
docker pull <image_name>

# Remove a local image
docker rmi <image_name_or_id>
```

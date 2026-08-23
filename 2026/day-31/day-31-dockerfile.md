# Day 31: Dockerfile – Build Your Own Images

## Overview
This document contains the complete documentation, Dockerfiles, command executions, and conceptual notes for Day 31 of the **#90DaysOfDevOps** challenge.

---

## Task 1: Your First Dockerfile

### Objective
Create a custom Docker image based on Ubuntu, install `curl`, and set a default echo message.

### Directory & Files
* **Directory:** `my-first-image/`
* **File:** `Dockerfile`

```dockerfile
# Use the official Ubuntu base image
FROM ubuntu:latest

# Update package index, install curl, and remove cache to reduce image size
RUN apt-get update && \
    apt-get install -y curl && \
    rm -rf /var/lib/apt/lists/*

# Default command to execute on container launch
CMD ["echo", "Hello from my custom image!"]
```

### Build & Run Commands
```bash
# Build and tag the image
docker build -t my-ubuntu:v1 .

# Run container and verify output
docker run --rm my-ubuntu:v1
```

### Output Verification
```text
Hello from my custom image!
```

---

## Task 2: Dockerfile Instructions

### Objective
Demonstrate the fundamental Dockerfile instructions: `FROM`, `WORKDIR`, `RUN`, `COPY`, `EXPOSE`, and `CMD`.

### Application Code (`server.js`)
```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello from Node container running on port 8080!\n');
});

server.listen(8080, '0.0.0.0', () => {
  console.log('Server is running on port 8080...');
});
```

### Dockerfile (`task2/Dockerfile`)
```dockerfile
# 1. FROM: Base image
FROM node:20-alpine

# 2. WORKDIR: Set the working directory inside container
WORKDIR /usr/src/app

# 3. RUN: Execute build-time command
RUN apk add --no-cache curl

# 4. COPY: Transfer file from host to container
COPY server.js .

# 5. EXPOSE: Document the container port
EXPOSE 8080

# 6. CMD: Default runtime command
CMD ["node", "server.js"]
```

### Build & Run Commands
```bash
# Build the image
docker build -t node-image:latest .

# Run in detached mode with host:container port mapping
docker run -d -p 8080:8080 --name node-apk node-image:latest

# Verify endpoint
curl http://localhost:8080
```

### Instruction Reference Table
| Instruction | Stage | Purpose |
| :--- | :--- | :--- |
| `FROM` | Build | Defines the starting parent image/layers. |
| `WORKDIR` | Build / Runtime | Sets the working directory for subsequent instructions. |
| `RUN` | Build | Executes commands and commits a new image layer. |
| `COPY` | Build | Copies files from the host context into the container. |
| `EXPOSE` | Metadata | Documents intended listening ports (requires `-p` to publish). |
| `CMD` | Runtime | Sets the default command executed when running the container. |

---

## Task 3: CMD vs ENTRYPOINT

### 1. `CMD` Behavior Test
* **Dockerfile:**
  ```dockerfile
  FROM alpine:latest
  CMD ["echo", "hello"]
  ```
* **Test Commands:**
  ```bash
  docker build -f Dockerfile.cmd -t test-cmd .
  docker run --rm test-cmd
  # Output: hello

  docker run --rm test-cmd echo "world"
  # Output: world
  ```
* **Behavior:** Any CLI arguments supplied after the image name **completely override and replace** the `CMD` instruction.

### 2. `ENTRYPOINT` Behavior Test
* **Dockerfile:**
  ```dockerfile
  FROM alpine:latest
  ENTRYPOINT ["echo"]
  ```
* **Test Commands:**
  ```bash
  docker build -f Dockerfile.entrypoint -t test-entrypoint .
  docker run --rm test-entrypoint
  # Output: (blank line)

  docker run --rm test-entrypoint hello world
  # Output: hello world
  ```
* **Behavior:** Any CLI arguments supplied after the image name are **appended** as arguments to the binary specified in `ENTRYPOINT`.

### Summary & Best Practices
| Feature | `CMD` | `ENTRYPOINT` |
| :--- | :--- | :--- |
| **Override capability** | Easily overridden by CLI parameters | Fixed binary; arguments append by default (requires `--entrypoint` to override) |
| **Ideal Use Case** | General application servers or default arguments | Dedicated CLI tools, single-purpose utilities |
| **Combined Pattern** | `ENTRYPOINT ["executable"]` + `CMD ["default_param"]` allows a fixed executable with easily overridable default arguments. |

---

## Task 4: Static Web App Image (Nginx)

### Objective
Serve a custom static web page using an Nginx Alpine container.

### Web Page (`index.html`)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Docker Website</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #f4f6f8; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; }
        .card { background: white; padding: 30px 40px; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); text-align: center; }
        h1 { color: #0284c7; }
        p { color: #475569; }
    </style>
</head>
<body>
    <div class="card">
        <h1>Welcome to My Website!</h1>
        <p>This static site is served from an Nginx Alpine container.</p>
    </div>
</body>
</html>
```

### Dockerfile
```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

### Build & Run Commands
```bash
docker build -t my-website:v1 .
docker run -d -p 8080:80 --name my-nginx-site my-website:v1

# Test
curl http://localhost:8080
```

---

## Task 5: .dockerignore Optimization

### Objective
Exclude sensitive or heavy files and directories from the Docker build context.

### `.dockerignore` Configuration
```text
node_modules
.git
*.md
.env
```

### Verification Steps
```bash
# Dockerfile to inspect directory listing
FROM alpine:latest
WORKDIR /app
COPY . .
CMD ["ls", "-la"]
```
* **Result:** `node_modules`, `.git`, `.env`, and Markdown files (`*.md`) are excluded from the build context and omitted from the final image, reducing image size and preventing secret leakage.

---

## Task 6: Build Optimization & Layer Caching

### Why Layer Order Matters for Build Speed
1. **Layer Caching:** Docker creates a cached read-only layer for each instruction (`RUN`, `COPY`, `ADD`).
2. **Cache Invalidation:** When Docker encounters a modified file during a step, that layer's cache is invalidated along with **all subsequent layers**.
3. **Layer Splitting Strategy:**
   * Place static, slow-changing instructions (system dependencies, package manifests) near the top.
   * Place frequently modified application source code near the bottom.

### Optimized Dockerfile Example
```dockerfile
FROM node:20-alpine
WORKDIR /app

# Step 1: Copy only dependency manifests (changes rarely)
COPY package*.json ./

# Step 2: Install dependencies (layer stays cached unless package.json changes)
RUN npm install

# Step 3: Copy frequently changing application source code
COPY . .

EXPOSE 8080
CMD ["node", "app.js"]
```

---

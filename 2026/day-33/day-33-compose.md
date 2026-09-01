# Day 33: Docker Compose Deep Dive

## 1. Overview
Docker Compose is a tool for defining and running multi-container Docker applications using a single YAML configuration file (`docker-compose.yml`). It handles container lifecycle management, network isolation, service discovery, and volume mapping declaratively.

---

## 2. Task 1: Installation & Verification

Check if the Docker Compose plugin (Compose V2) is available:

```bash
# Check V2 plugin
docker compose version

# Check legacy V1 binary (if applicable)
docker-compose --version
```

### Installation (Ubuntu / Debian)
```bash
sudo apt update
sudo apt install -y docker-compose-plugin
```

---

## 3. Task 2: Single-Service Setup (Nginx)

**Directory:** `compose-basics/`  
**File:** `docker-compose.yml`

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
```

### Operations
* **Start:** `docker compose up -d`
* **Access:** Navigate to `http://localhost:8080`
* **Stop:** `docker compose down`

---

## 4. Task 3: Multi-Container Setup (WordPress + MySQL)

### Key Features
* **Automatic Networking:** Containers communicate over the default bridge network using service names (`mysql`, `wordpress`) as hostnames.
* **Data Persistence:** Uses a named volume (`db_data`) so database state survives container restarts and deletions.

**File:** `docker-compose.yml`

```yaml
services:
  mysql:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: lucky
      MYSQL_PASSWORD: test123
      MYSQL_ROOT_PASSWORD: test@123
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: lucky
      WORDPRESS_DB_PASSWORD: test123
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - mysql

volumes:
  db_data:
```

### Verification & Persistence Test
1. Start containers: `docker compose up -d`
2. Access `http://localhost:8080`, configure WordPress, and publish a post.
3. Stop containers: `docker compose down`
4. Restart: `docker compose up -d`
5. Refresh browser: Content and settings remain intact.
6. **Full Reset (wipe volume):** `docker compose down -v`

---

## 5. Task 4: Essential Compose Commands

| Command | Description |
| :--- | :--- |
| `docker compose up -d` | Start all services in the background (detached mode) |
| `docker compose ps` | View status and port mappings of managed containers |
| `docker compose logs` | View logs across all services |
| `docker compose logs -f <service>` | Follow/stream logs for a specific service (e.g., `wordpress`) |
| `docker compose stop` | Pause/stop services without destroying containers or networks |
| `docker compose start` | Resume stopped services |
| `docker compose down` | Stop and remove containers, networks, and internal resources |
| `docker compose down -v` | Stop and remove containers, networks, **and named volumes** |
| `docker compose up -d --build` | Rebuild images and restart updated containers |
| `docker compose exec <service> <cmd>` | Execute a command inside a running service container |

---

## 6. Task 5: Environment Variables & Configuration

### A. Environment Configuration (`.env`)
```bash
# Database Configuration
DB_NAME=wordpress
DB_USER=lucky
DB_PASS=test123
DB_ROOT_PASS=test@123

# Web Configuration
WEB_PORT=8080
```

### B. Interpolated Compose File (`docker-compose.yml`)
```yaml
services:
  mysql:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_DATABASE: ${DB_NAME}
      MYSQL_USER: ${DB_USER}
      MYSQL_PASSWORD: ${DB_PASS}
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASS}
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "${WEB_PORT}:80"
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: ${DB_USER}
      WORDPRESS_DB_PASSWORD: ${DB_PASS}
      WORDPRESS_DB_NAME: ${DB_NAME}
    depends_on:
      - mysql

volumes:
  db_data:
```

### C. Verification Commands
```bash
# 1. Preview resolved YAML with substituted variables (dry-run)
docker compose config

# 2. Inspect active environment variables inside container
docker compose exec wordpress env
```

---

## 7. Troubleshooting Notes

* **`authentication required - incorrect username or password` during image pull:**  
  Run `docker logout` or clear stale token cache with `rm -f ~/.docker/config.json`.
* **`Invalid operation get`:**  
  Use `sudo apt update && sudo apt install -y docker-compose-plugin` (Ubuntu syntax uses `apt` or `apt-get`, not `apt get`).
* **Database Connection Errors:**  
  Verify that database credentials in the web service match the environment variables assigned to the database container. If credentials change, wipe the old volume using `docker compose down -v`.

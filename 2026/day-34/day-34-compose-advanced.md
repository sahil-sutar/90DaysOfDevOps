# Day 34: Docker Compose – Real-World Multi-Container Apps[cite: 1]

## Architecture Overview[cite: 1]
This multi-container deployment models a production architecture consisting of:[cite: 1]
- **Web App:** Python Flask web server connected to Redis and PostgreSQL.[cite: 1]
- **Database:** PostgreSQL 16 Alpine running on an isolated backend network with a persistent volume.[cite: 1]
- **Cache:** Redis 7 Alpine caching request count metrics.[cite: 1]
- **Network Isolation:** Dual-bridge architecture (`frontend-net` and `backend-net`). The database and cache cannot be reached directly by public host ports.[cite: 1]

---

## Task Verification & Technical Analysis[cite: 1]

### Task 1 & 4: Application Stack & Custom Dockerfile[cite: 1]
The web application builds directly from `./app/Dockerfile`. Rebuilding after any code update runs in a single step:[cite: 1]
```bash
docker compose up -d --build
```
`--build` ensures Docker recreates the image with latest application changes before booting containers.[cite: 1]

---

### Task 2: Service Dependencies & Healthchecks[cite: 1]

#### 1. Why `service_healthy` is Essential[cite: 1]
Standard `depends_on` only tracks whether the target container has *started* its initial process (PID 1)[cite: 1]. It does **not** ensure PostgreSQL has initialized sockets, loaded tables, and begun accepting TCP connections[cite: 1]. Without health checks, the Flask app crashes with connection refusal errors[cite: 1].

With `condition: service_healthy`:[cite: 1]
```yaml
depends_on:
  db:
    condition: service_healthy
```
Docker Compose checks the database status via `pg_isready -U postgres -d appdb`[cite: 1]. Flask starts only after the probe returns exit code `0`[cite: 1].

#### 2. Verification Command[cite: 1]
```bash
docker compose ps
```
*Output demonstrates the explicit `healthy` state before dependent containers initialize:*[cite: 1]
```text
NAME                 IMAGE               COMMAND                  SERVICE   STATUS                    PORTS
day-34-cache-1       redis:7-alpine      "docker-entrypoint.s…"   cache     running                   6379/tcp
day-34-db-1          postgres:16-alpine  "docker-entrypoint.s…"   db        running (healthy)         5432/tcp
day-34-web-1         day-34-web          "python app.py"          web       running                   0.0.0.0:5000->5000/tcp
```

---

### Task 3: Restart Policies in Production[cite: 1]

| Policy | Behavior | When to Use |[cite: 1]
| :--- | :--- | :--- |[cite: 1]
| `no` | Default policy. Never restarts container regardless of exit code. | Batch tasks, CI/CD run-once jobs, one-off migrations. |[cite: 1]
| `on-failure[:max-retries]` | Restarts only if the container exits with a non-zero exit code. | Stateless applications, workers, or web services where dirty crashes need recovery but clean exits (`0`) should be respected. |[cite: 1]
| `always` | Always restarts regardless of exit code or manual stoppage (until daemon shutdown). Re-runs upon Docker daemon restart. | Critical backend daemons, databases, cache services that must remain up at all times. |[cite: 1]
| `unless-stopped` | Like `always`, except it remains stopped if a user explicitly runs `docker stop` prior to daemon reboot. | Production services where manual administrative stops must persist across host server reboots. |[cite: 1]

#### Manual Kill Test:[cite: 1]
1. Identify database container and issue a SIGKILL:[cite: 1]
   ```bash
   docker kill day-34-db-1
   ```
2. Verify container state:[cite: 1]
   ```bash
   docker compose ps db
   ```
3. **Result:** Because `restart: always` was declared, the Docker daemon immediately detects termination and starts a new container instance.[cite: 1]

---

### Task 5: Isolated Networks & Named Volumes[cite: 1]

```yaml
networks:
  frontend-net:
    driver: bridge
  backend-net:
    driver: bridge
```
- **Network Segmentation:** The `web` service bridges both `frontend-net` and `backend-net`[cite: 1]. `db` and `cache` reside strictly within `backend-net`[cite: 1]. This enforces least-privilege networking: internal stores cannot receive external ingress[cite: 1].
- **Named Volumes:** `day34_pg_data` maps directly to `/var/lib/postgresql/data`, guaranteeing persistent relational storage across complete container lifecycle teardowns (`docker compose down`)[cite: 1].

---

### Task 6: Scaling Analysis (Bonus Challenge)[cite: 1]

Executing scaling on the web service:[cite: 1]
```bash
docker compose up -d --scale web=3
```

#### What Happens & What Breaks:[cite: 1]
Docker returns a port binding conflict error:[cite: 1]
```text
Error response from daemon: driver failed programming external connectivity on endpoint day-34-web-2:
Bind for 0.0.0.0:5000 failed: port is already allocated
```

#### Why Simple Scaling Fails with Host Port Mapping:[cite: 1]
In `docker-compose.yml`, the web service declares a static host-to-container port mapping:[cite: 1]
```yaml
ports:
  - "5000:5000"
```
The host operating system can bind port `5000` on interface `0.0.0.0` to **only one** socket at a time[cite: 1]. When replica `web-2` attempts to bind the identical host port, the kernel rejects it with `EADDRINUSE`[cite: 1].

#### The Production Solution:[cite: 1]
1. Remove static host port mapping on the application container.[cite: 1]
2. Put an edge reverse proxy (such as **Nginx**, **Traefik**, or **HAProxy**) on the `frontend-net`.[cite: 1]
3. Expose port `80`/`443` on the reverse proxy, and route traffic internally across all scaled app container IPs via round-robin or load-balancing algorithms.[cite: 1]

# Backend Deployment

> **Relevant source files**
> * [ARCHITECTURE.md](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md)

## Purpose and Scope

This document provides a comprehensive guide for deploying the AxIA FastAPI backend in production environments using Docker Compose. It covers the deployment of three core services: the FastAPI application server, PostgreSQL database, and n8n workflow engine. The deployment architecture ensures proper service orchestration, network isolation, and persistent data storage.

For configuration of environment variables and secrets management, see [Environment Variables](/axchisan/AxIA/12.2-environment-variables). For backend API architecture and endpoint details, see [FastAPI Backend](/axchisan/AxIA/3.2-fastapi-backend).

**Sources:** [ARCHITECTURE.md L199-L219](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L199-L219)

---

## Deployment Architecture

The backend deployment consists of three containerized services running under Docker Compose orchestration. The FastAPI application acts as the primary API gateway, handling authentication, WebSocket connections, and message routing. PostgreSQL provides persistent storage for user data, messages, and session information. The n8n workflow engine processes AI requests and integrates with external services like OpenAI, ElevenLabs, and Whisper.

### Docker Compose Service Architecture

```mermaid
flowchart TD

FASTAPI["FastAPI Application<br>main.py<br>Port: 8000<br>Image: python:3.11"]
WSGI["Uvicorn ASGI Server<br>Workers: 1<br>WebSocket Support"]
PGDB["PostgreSQL 15<br>Port: 5432<br>Database: axia_db"]
PGDATA["/var/lib/postgresql/data<br>Volume: postgres_data"]
N8N["n8n Workflow Engine<br>Port: 5678<br>Webhook: /webhook/axia"]
N8NDATA["/home/node/.n8n<br>Volume: n8n_data"]
NGINX["Reverse Proxy<br>apiaxia.axchisan.com<br>HTTPS/WSS"]
CLIENT["Flutter App<br>Mobile Clients"]

NGINX --> FASTAPI

subgraph subGraph4 ["External Access"]
    NGINX
    CLIENT
    CLIENT --> NGINX
end

subgraph subGraph3 ["Docker Network: axia_network"]
    FASTAPI --> PGDB
    FASTAPI --> N8N
    N8N --> FASTAPI

subgraph subGraph2 ["n8n Container"]
    N8N
    N8NDATA
    N8N --> N8NDATA
end

subgraph subGraph1 ["postgres Container"]
    PGDB
    PGDATA
    PGDB --> PGDATA
end

subgraph subGraph0 ["fastapi Container"]
    FASTAPI
    WSGI
    FASTAPI --> WSGI
end
end
```

**Sources:** [ARCHITECTURE.md L191-L219](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L191-L219)

---

## Docker Compose Configuration

The `docker-compose.yml` file defines three services with specific dependencies and health checks. The FastAPI service depends on both PostgreSQL and n8n being healthy before starting. All services share a custom bridge network for internal communication while exposing only necessary ports to the host.

### Service Definitions

```mermaid
flowchart TD

POSTGRES["postgres<br>Image: postgres:15<br>Restart: always<br>Healthcheck: pg_isready"]
N8N_SVC["n8n<br>Image: n8nio/n8n<br>Restart: always<br>Depends: postgres"]
FASTAPI_SVC["fastapi<br>Build: ./backend<br>Restart: always<br>Depends: postgres, n8n"]
PG_VOL["postgres_data"]
N8N_VOL["n8n_data"]
NET["axia_network<br>Driver: bridge"]

POSTGRES --> PG_VOL
N8N_SVC --> N8N_VOL
POSTGRES --> NET
N8N_SVC --> NET
FASTAPI_SVC --> NET

subgraph Networks ["Networks"]
    NET
end

subgraph Volumes ["Volumes"]
    PG_VOL
    N8N_VOL
end

subgraph subGraph0 ["docker-compose.yml Services"]
    POSTGRES
    N8N_SVC
    FASTAPI_SVC
    FASTAPI_SVC --> POSTGRES
    FASTAPI_SVC --> N8N_SVC
    N8N_SVC --> POSTGRES
end
```

The `docker-compose.yml` structure follows this pattern:

| Service | Image/Build | Exposed Ports | Volumes | Dependencies |
| --- | --- | --- | --- | --- |
| `postgres` | `postgres:15` | `5432:5432` | `postgres_data:/var/lib/postgresql/data` | None |
| `n8n` | `n8nio/n8n` | `5678:5678` | `n8n_data:/home/node/.n8n` | `postgres` (healthy) |
| `fastapi` | Build from `./backend/Dockerfile` | `8000:8000` | None | `postgres` (healthy), `n8n` (healthy) |

**Sources:** [ARCHITECTURE.md L191-L204](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L191-L204)

 [ARCHITECTURE.md L215-L219](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L215-L219)

---

## PostgreSQL Configuration

The PostgreSQL service stores user accounts, authentication tokens, chat messages, calendar events, tasks, and session data. The database is initialized with the schema defined in the FastAPI application's database models.

### Database Connection Parameters

The FastAPI application connects to PostgreSQL using the `DATABASE_URL` environment variable with the following format:

```yaml
postgresql://user:password@postgres:5432/axia_db
```

Key configuration details:

* **Host**: `postgres` (Docker service name for internal DNS resolution)
* **Port**: `5432` (default PostgreSQL port)
* **Database**: `axia_db`
* **Connection Pool**: Managed by SQLAlchemy in the FastAPI application
* **Persistent Storage**: Volume `postgres_data` mapped to `/var/lib/postgresql/data`

### Health Check Configuration

PostgreSQL health checks use `pg_isready` to verify the database is accepting connections:

```yaml
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U postgres"]
  interval: 10s
  timeout: 5s
  retries: 5
```

**Sources:** [ARCHITECTURE.md L218](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L218-L218)

---

## n8n Integration

The n8n workflow engine receives webhook requests from the FastAPI backend, processes them through AI workflows, and returns responses. The integration uses HTTP webhooks for both directions of communication.

### Webhook Communication Flow

```mermaid
sequenceDiagram
  participant FastAPI
  participant main.py
  participant n8n Workflow
  participant http://n8n:5678
  participant External AI Services
  participant OpenAI/Anthropic

  FastAPI->>n8n Workflow: "POST /webhook/axia
  note over n8n Workflow,http://n8n:5678: "Channel detection
  n8n Workflow->>External AI Services: {session_id, user, type, text/audio_base64}"
  External AI Services->>n8n Workflow: "AI Processing Request"
  note over n8n Workflow,http://n8n:5678: "Format response
  n8n Workflow->>FastAPI: "AI Response"
  note over FastAPI,main.py: "Route to WebSocket
```

### Webhook Configuration

The n8n webhook endpoint is configured at:

```yaml
http://n8n:5678/webhook/axia
```

The `N8N_WEBHOOK_URL` environment variable in the FastAPI service must point to this internal Docker network address. The workflow expects the following payload structure:

```json
{
  "session_id": "uuid-v4",
  "user": "username",
  "timestamp": "ISO-8601 datetime",
  "type": "text|audio",
  "text": "message content",
  "audio_base64": "base64-encoded audio data"
}
```

n8n processes the request and returns a response to the `/app-message` endpoint on the FastAPI service.

**Sources:** [ARCHITECTURE.md L91-L117](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L91-L117)

---

## Service Dependencies and Startup Order

Docker Compose orchestrates service startup based on health check conditions. The dependency chain ensures that PostgreSQL is fully initialized before n8n starts, and both are healthy before FastAPI begins accepting requests.

### Startup Sequence

```mermaid
flowchart TD

START["docker-compose up"]
PG_START["Start postgres container"]
PG_HEALTH["Wait for pg_isready"]
N8N_START["Start n8n container"]
FAST_START["Start fastapi container"]
N8N_HEALTH["Wait for n8n HTTP health"]
FAST_WAIT["FastAPI continues startup"]
FAST_READY["FastAPI ready<br>Uvicorn listening on :8000"]
SERVE["Backend Operational<br>WebSocket + REST API"]

START --> PG_START
PG_START --> PG_HEALTH
PG_HEALTH --> N8N_START
PG_HEALTH --> FAST_START
N8N_START --> N8N_HEALTH
N8N_HEALTH --> FAST_WAIT
FAST_START --> FAST_WAIT
FAST_WAIT --> FAST_READY
FAST_READY --> SERVE
```

The `depends_on` configuration with `condition: service_healthy` ensures proper initialization:

1. **PostgreSQL starts first**: Database must be ready before dependent services
2. **n8n starts after PostgreSQL**: Workflow engine may require database for internal storage
3. **FastAPI starts last**: Waits for both PostgreSQL and n8n to be healthy
4. **Health checks repeat**: Services continuously verify upstream dependencies

**Sources:** [ARCHITECTURE.md L199-L204](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L199-L204)

---

## Deployment Process

Follow these steps to deploy the AxIA backend in a production environment:

### Prerequisites

* Docker Engine 20.10+ installed on the host
* Docker Compose 2.0+ installed
* Port 8000 available (or configure reverse proxy)
* Sufficient disk space for PostgreSQL and n8n volumes
* Environment variables configured (see [Environment Variables](/axchisan/AxIA/12.2-environment-variables))

### Step 1: Clone Repository

```
git clone https://github.com/axchisan/AxIA.git
cd AxIA
```

### Step 2: Configure Environment

Create a `.env` file in the project root with production values:

```
SECRET_KEY=your-production-secret-key
N8N_WEBHOOK_URL=http://n8n:5678/webhook/axia
DATABASE_URL=postgresql://postgres:yourpassword@postgres:5432/axia_db
```

See [Environment Variables](/axchisan/AxIA/12.2-environment-variables) for complete configuration details.

### Step 3: Build and Start Services

```
docker-compose up -d
```

This command:

* Builds the FastAPI Docker image from `backend/Dockerfile`
* Pulls the PostgreSQL and n8n images from Docker Hub
* Creates the `axia_network` bridge network
* Creates persistent volumes for `postgres_data` and `n8n_data`
* Starts all three services with health checks

### Step 4: Verify Service Health

Check that all services are running:

```
docker-compose ps
```

Expected output:

```
NAME                COMMAND                  SERVICE     STATUS          PORTS
axia-fastapi-1      "uvicorn main:app ..."   fastapi     Up 2 minutes    0.0.0.0:8000->8000/tcp
axia-postgres-1     "docker-entrypoint..."   postgres    Up 2 minutes    0.0.0.0:5432->5432/tcp
axia-n8n-1          "tini -- /docker-e..."   n8n         Up 2 minutes    0.0.0.0:5678->5678/tcp
```

### Step 5: Test API Health

```
curl http://localhost:8000/health
```

Expected response:

```json
{
  "status": "ok",
  "timestamp": "2024-11-27T12:00:00.000Z"
}
```

### Step 6: Configure Reverse Proxy

For production deployments, configure NGINX or another reverse proxy to handle HTTPS/WSS termination:

```
server {
    listen 443 ssl http2;
    server_name apiaxia.axchisan.com;
    
    location / {
        proxy_pass http://localhost:8000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

**Sources:** [ARCHITECTURE.md L199-L211](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L199-L211)

 [ARCHITECTURE.md L223-L225](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L223-L225)

---

## Production Considerations

### Security

1. **Secret Management**: Use Docker secrets or external secret managers for `SECRET_KEY` and database passwords
2. **Network Isolation**: Keep the `axia_network` internal; only expose FastAPI through a reverse proxy
3. **CORS Configuration**: Restrict allowed origins in FastAPI CORS middleware
4. **JWT Expiration**: Maintain 24-hour token expiration as configured in the authentication system
5. **Database Access**: Ensure PostgreSQL is not exposed to external networks

### Scaling

The current architecture supports vertical scaling:

* **FastAPI**: Can run multiple Uvicorn workers by adjusting `--workers` flag in the Dockerfile CMD
* **PostgreSQL**: Increase connection pool size via `SQLALCHEMY_POOL_SIZE` environment variable
* **n8n**: Workflow engine scales with available CPU/memory resources

For horizontal scaling, consider:

* Load balancer in front of multiple FastAPI instances
* PostgreSQL read replicas for query-heavy workloads
* Redis for session storage and WebSocket connection management

### Monitoring

Implement health monitoring for all services:

| Service | Health Endpoint | Check Interval | Failure Action |
| --- | --- | --- | --- |
| FastAPI | `GET /health` | 30 seconds | Alert + restart container |
| PostgreSQL | `pg_isready -U postgres` | 10 seconds | Alert + restart container |
| n8n | `GET /healthz` | 30 seconds | Alert + restart container |

### Backup Strategy

**PostgreSQL Backups**:

```markdown
# Daily automated backup
docker exec axia-postgres-1 pg_dump -U postgres axia_db > backup_$(date +%Y%m%d).sql

# Volume backup
docker run --rm -v axia_postgres_data:/data -v $(pwd):/backup ubuntu tar czf /backup/postgres_backup.tar.gz /data
```

**n8n Workflow Backups**:

```markdown
# Export n8n workflows
curl -X GET http://localhost:5678/api/v1/workflows -H "X-N8N-API-KEY: your-api-key" > workflows_backup.json

# Volume backup
docker run --rm -v axia_n8n_data:/data -v $(pwd):/backup ubuntu tar czf /backup/n8n_backup.tar.gz /data
```

### Logging

Configure centralized logging for production deployments:

```yaml
# In docker-compose.yml
services:
  fastapi:
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

View logs:

```markdown
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f fastapi

# Last 100 lines
docker-compose logs --tail=100
```

**Sources:** [ARCHITECTURE.md L227-L240](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L227-L240)

---

## Service Restart and Maintenance

### Graceful Restart

To update the backend without downtime:

```markdown
# Pull latest code
git pull origin main

# Rebuild and restart FastAPI only
docker-compose up -d --build fastapi

# Restart all services
docker-compose restart
```

### Database Migrations

When database schema changes are required:

```markdown
# Run migrations inside FastAPI container
docker-compose exec fastapi alembic upgrade head

# Or with direct database access
docker-compose exec postgres psql -U postgres -d axia_db -f migration.sql
```

### Volume Management

```sql
# List volumes
docker volume ls

# Inspect volume
docker volume inspect axia_postgres_data

# Remove all stopped containers and unused volumes (CAUTION)
docker-compose down -v  # This will DELETE all data!
```

**Sources:** [ARCHITECTURE.md L199-L204](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L199-L204)

---

## Troubleshooting

### Common Issues

**Service fails to start due to port conflicts:**

```yaml
# Check port usage
netstat -tuln | grep -E '8000|5432|5678'

# Change ports in docker-compose.yml if needed
ports:
  - "8001:8000"  # Use different host port
```

**WebSocket connection failures:**

* Verify reverse proxy is configured for WebSocket upgrade headers
* Check that JWT token is passed correctly in query parameter: `?token=xxx`
* Ensure CORS middleware allows WebSocket origin

**n8n webhook not receiving requests:**

```css
# Check n8n logs
docker-compose logs n8n

# Verify webhook URL
docker-compose exec fastapi env | grep N8N_WEBHOOK_URL

# Test webhook directly
curl -X POST http://localhost:5678/webhook/axia -H "Content-Type: application/json" -d '{"test": true}'
```

**Database connection errors:**

```markdown
# Verify PostgreSQL is running
docker-compose ps postgres

# Check database connectivity
docker-compose exec fastapi pg_isready -h postgres -U postgres

# View database logs
docker-compose logs postgres
```

**Sources:** [ARCHITECTURE.md L131-L146](https://github.com/axchisan/AxIA/blob/1fe26c44/ARCHITECTURE.md#L131-L146)
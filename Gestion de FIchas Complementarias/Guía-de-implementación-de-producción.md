# Guía de implementación de producción

> **Archivos fuente relevantes**
> * [Archivo Docker](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile)
> * [docker-compose.yml](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml)
> * [siguiente.config.mjs](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs)

## Propósito y alcance

Este documento proporciona una guía paso a paso para la implementación del sistema SENA Gestión Complementarias en un entorno de producción. Abarca la configuración del entorno, la implementación basada en Docker, el fortalecimiento de la seguridad, las operaciones de la base de datos y la monitorización operativa.

Para obtener información detallada sobre los archivos de configuración de Docker, consulte [Configuración de Docker](/axchisan/gestionComplementarias/7.1-docker-configuration) . Para conocer los conceptos generales de la arquitectura de implementación, consulte [Arquitectura de implementación](/axchisan/gestionComplementarias/3.1-deployment-architecture) . Para la configuración local inicial, consulte [Instalación e implementación](/axchisan/gestionComplementarias/2.1-installation-and-deployment) .

---

## Prerrequisitos

Antes de implementar en producción, asegúrese de que se cumplan los siguientes requisitos:

### Requisitos del sistema

| Componente | Especificación mínima | Recomendado |
| --- | --- | --- |
| UPC | 2 núcleos | 4+ núcleos |
| RAM | 4 GB | 8+ GB |
| Almacenamiento | 20 GB | 50+ GB |
| Sistema operativo | Ubuntu 20.04+ / Debian 11+ | Ubuntu 22.04 LTS |
| Estibador | 20.10+ | 24.0+ |
| Docker Compose | 2.0+ | 2.20+ |

### Software requerido

* Motor Docker (con soporte para BuildKit)
* Docker Compose v2
* Git (para la gestión del código fuente)
* OpenSSL (instalado en el contenedor mediante[Dockerfile L15-L29](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L15-L29) )

### Servicios externos

1. **Base de datos Neon PostgreSQL** : instancia de PostgreSQL sin servidor con agrupación de conexiones habilitada
2. **Nombre de dominio** : configurado con un registro DNS que apunta al servidor de producción
3. **Certificado SSL** : Certificado TLS válido para HTTPS (recomendado: Let's Encrypt)

**Fuentes:** [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

 [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

---

## Configuración del entorno

### Variables de entorno requeridas

La aplicación requiere variables de entorno específicas configuradas en un `.env`archivo en la raíz del proyecto. Estas variables se inyectan en el contenedor mediante[docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

#### Configuración del núcleo

```markdown
# Next.js Runtime
NODE_ENV=production

# Application URL (must match production domain)
NEXTAUTH_URL=https://gestioncomplementarias.axchisan.com

# Database Connection
DATABASE_URL="postgresql://username:password@host/database?sslmode=require&pgbouncer=true"
```

#### Secretos de seguridad

Genere secretos criptográficamente seguros utilizando:

```markdown
# Generate NEXTAUTH_SECRET (minimum 32 characters)
openssl rand -base64 32

# Generate JWT_SECRET (minimum 32 characters)
openssl rand -base64 32
```

```markdown
# Authentication Secrets
NEXTAUTH_SECRET=<generated-secret-from-openssl>
JWT_SECRET=<generated-secret-from-openssl>
```

#### Integraciones opcionales

```markdown
# OpenAI Integration (if AI features enabled)
OPENAI_API_KEY=sk-...
```

### Estructura del archivo de entorno

Crear `.env`archivo en la raíz del proyecto:

```
NODE_ENV=production
DATABASE_URL=postgresql://user:pass@ep-example-123456.us-east-2.aws.neon.tech/sena_gestion?sslmode=require
NEXTAUTH_SECRET=your-nextauth-secret-min-32-chars
NEXTAUTH_URL=https://gestioncomplementarias.axchisan.com
JWT_SECRET=your-jwt-secret-min-32-chars
```

### Diagrama del entorno de producción

```mermaid
flowchart TD

EnvFile[".env file<br>Environment Variables"]
DockerCompose["docker-compose.yml<br>Service Orchestration"]
Container["sena-gestion-prod<br>Container Instance"]
NODE_ENV["NODE_ENV=production"]
DATABASE_URL["DATABASE_URL"]
NEXTAUTH_SECRET["NEXTAUTH_SECRET"]
NEXTAUTH_URL["NEXTAUTH_URL"]
JWT_SECRET["JWT_SECRET"]
NeonDB["Neon PostgreSQL<br>ep-*.aws.neon.tech"]
Domain["Production Domain<br>gestioncomplementarias.axchisan.com"]

NODE_ENV --> Container
DATABASE_URL --> Container
NEXTAUTH_SECRET --> Container
NEXTAUTH_URL --> Container
JWT_SECRET --> Container
Container --> NeonDB
Domain --> Container

subgraph subGraph2 ["External Services"]
    NeonDB
    Domain
end

subgraph subGraph1 ["Environment Variables"]
    NODE_ENV
    DATABASE_URL
    NEXTAUTH_SECRET
    NEXTAUTH_URL
    JWT_SECRET
end

subgraph subGraph0 ["Production Server"]
    EnvFile
    DockerCompose
    Container
    EnvFile --> DockerCompose
    DockerCompose --> Container
end
```

**Fuentes:** [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

 [next.config.mjs L33](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L33-L33)

---

## Proceso de implementación

### Paso 1: Preparar el servidor de producción

```sql
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add current user to docker group (avoid sudo)
sudo usermod -aG docker $USER
newgrp docker

# Install Docker Compose
sudo apt install docker-compose-plugin -y

# Verify installations
docker --version
docker compose version
```

### Paso 2: Clonar el repositorio

```markdown
# Clone source code
git clone https://github.com/axchisan/gestionComplementarias.git
cd gestionComplementarias

# Checkout production branch or tag
git checkout main  # or specific release tag
```

### Paso 3: Configurar el entorno

```sql
# Create .env file with production values
nano .env

# Verify required variables are set
cat .env | grep -E "DATABASE_URL|NEXTAUTH_SECRET|JWT_SECRET"
```

### Paso 4: Crear la imagen de producción

El[Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

utiliza un proceso de construcción de varias etapas optimizado para la producción:

```markdown
# Build image with BuildKit optimizations
docker compose build --no-cache

# Verify image creation
docker images | grep sena-gestion
```

#### Diagrama del proceso de construcción de varias etapas

```mermaid
flowchart TD

Base1["node:20-alpine"]
PackageJSON["package.json<br>package-lock.json"]
NpmCI["npm ci --only=production"]
NodeModules1["node_modules/"]
Base2["node:20-alpine"]
NodeModules2["COPY --from=deps"]
SourceCode["COPY . ."]
PrismaGen["npx prisma generate"]
NpmBuild["npm run build"]
NextBuild[".next/standalone<br>.next/static"]
Base3["node:20-alpine"]
Standalone["COPY .next/standalone"]
Static["COPY .next/static"]
ServerJS["server.js"]
User["nextjs:nodejs<br>uid 1001"]

NodeModules1 --> NodeModules2
NextBuild --> Standalone
NextBuild --> Static

subgraph subGraph2 ["Stage 3: runner"]
    Base3
    Standalone
    Static
    ServerJS
    User
    Base3 --> Standalone
    Standalone --> ServerJS
    Static --> ServerJS
    Base3 --> User
    User --> ServerJS
end

subgraph subGraph1 ["Stage 2: builder"]
    Base2
    NodeModules2
    SourceCode
    PrismaGen
    NpmBuild
    NextBuild
    Base2 --> NodeModules2
    NodeModules2 --> SourceCode
    SourceCode --> PrismaGen
    PrismaGen --> NpmBuild
    NpmBuild --> NextBuild
end

subgraph subGraph0 ["Stage 1: deps"]
    Base1
    PackageJSON
    NpmCI
    NodeModules1
    Base1 --> PackageJSON
    PackageJSON --> NpmCI
    NpmCI --> NodeModules1
end
```

**Fuentes:** [Dockerfile L5-L11](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L5-L11)

 [Dockerfile L14-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L14-L25)

 [Dockerfile L28-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L28-L55)

### Paso 5: Migración de la base de datos

Antes de iniciar la aplicación, asegúrese de que el esquema de la base de datos esté actualizado:

```markdown
# Run Prisma migrations (one-time or when schema changes)
docker compose run --rm app npx prisma migrate deploy

# Verify migration status
docker compose run --rm app npx prisma migrate status

# Optional: Seed initial data (users, centers, programs)
docker compose run --rm app npx prisma db seed
```

### Paso 6: Iniciar el contenedor de producción

```markdown
# Start container in detached mode
docker compose up -d

# Verify container is running
docker compose ps

# Check logs for startup errors
docker compose logs -f app
```

Resultado esperado:

```
sena-gestion-prod  | ▲ Next.js 15.2.4
sena-gestion-prod  | - Local:        http://0.0.0.0:3000
sena-gestion-prod  | - Network:      http://172.20.0.2:3000
sena-gestion-prod  | ✓ Ready in 1.2s
```

### Paso 7: Verificar la implementación

```python
# Test internal container port
docker exec sena-gestion-prod wget -O- http://localhost:3000/api/health

# Test external port mapping (from host)
curl http://localhost:3007/api/health

# Test with domain name (if DNS configured)
curl https://gestioncomplementarias.axchisan.com/api/health
```

Respuesta esperada del control de salud:

```json
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:30:00.000Z",
  "database": "connected"
}
```

### Deployment Architecture Flow

```mermaid
sequenceDiagram
  participant Administrator
  participant Production Server
  participant Docker Engine
  participant sena-gestion-prod
  participant Neon PostgreSQL

  Administrator->>Production Server: 1. Clone repository
  Administrator->>Production Server: 2. Configure .env file
  Administrator->>Docker Engine: 3. docker compose build
  Docker Engine->>Docker Engine: Stage 1: Install dependencies
  Docker Engine->>Docker Engine: Stage 2: Build application
  Docker Engine->>Docker Engine: Stage 3: Create runtime image
  Docker Engine-->>Administrator: Build complete
  Administrator->>Docker Engine: 4. docker compose run prisma migrate
  Docker Engine->>sena-gestion-prod: Start temporary container
  sena-gestion-prod->>Neon PostgreSQL: Execute migrations
  Neon PostgreSQL-->>sena-gestion-prod: Migrations applied
  sena-gestion-prod-->>Administrator: Migration complete
  Administrator->>Docker Engine: 5. docker compose up -d
  Docker Engine->>sena-gestion-prod: Start container (detached)
  sena-gestion-prod->>sena-gestion-prod: Load environment variables
  sena-gestion-prod->>Neon PostgreSQL: Test database connection
  sena-gestion-prod->>sena-gestion-prod: Start Next.js server
  sena-gestion-prod-->>Docker Engine: Server ready on port 3000
  Docker Engine-->>Administrator: Container running
  Administrator->>Production Server: 6. curl localhost:3007/api/health
  Production Server->>sena-gestion-prod: Forward to port 3000
  sena-gestion-prod->>Neon PostgreSQL: Check database status
  Neon PostgreSQL-->>sena-gestion-prod: Status OK
  sena-gestion-prod-->>Production Server: {"status": "healthy"}
  Production Server-->>Administrator: Health check passed
```

**Sources:** [docker-compose.yml L4-L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L4-L21)

 [Dockerfile L44-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L44-L46)

 [Dockerfile L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L55-L55)

---

## Security Hardening

### Container Security

The [Dockerfile L35-L48](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L35-L48)

 implements security best practices:

1. **Non-Root User**: Application runs as `nextjs` user (UID 1001) instead of root
2. **Minimal Base Image**: Uses `node:20-alpine` to reduce attack surface
3. **Standalone Output**: Only necessary files copied via [next.config.mjs L3](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L3-L3)

### Network Security

The [docker-compose.yml L20-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L20-L24)

 creates an isolated bridge network:

```markdown
# Verify network isolation
docker network inspect sena-network

# Container is only accessible via published ports
docker port sena-gestion-prod
```

Expected output:

```
3000/tcp -> 0.0.0.0:3007
```

### Security Headers

Production security headers configured in [next.config.mjs L14-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L14-L46)

:

| Header | Value | Purpose |
| --- | --- | --- |
| `X-Frame-Options` | `DENY` | Prevent clickjacking attacks |
| `X-Content-Type-Options` | `nosniff` | Prevent MIME type sniffing |
| `Referrer-Policy` | `origin-when-cross-origin` | Control referrer information |
| `Access-Control-Allow-Origin` | `https://gestioncomplementarias.axchisan.com` | Restrict CORS to production domain |

### SSL/TLS Configuration

For HTTPS termination, use a reverse proxy (Nginx or Traefik):

```
server {
    listen 443 ssl http2;
    server_name gestioncomplementarias.axchisan.com;
    
    ssl_certificate /etc/letsencrypt/live/domain/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/domain/privkey.pem;
    
    location / {
        proxy_pass http://localhost:3007;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Security Checklist

* `.env` file has correct permissions: `chmod 600 .env`
* `NEXTAUTH_SECRET` and `JWT_SECRET` are cryptographically secure (32+ chars)
* Database connection uses SSL: `sslmode=require` in `DATABASE_URL`
* Container runs as non-root user: [Dockerfile L48](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L48-L48)
* Security headers enabled: [next.config.mjs L18-L42](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L18-L42)
* Firewall configured to allow only ports 80, 443, 22
* Docker daemon socket not exposed: `/var/run/docker.sock` not mounted
* Regular security updates: `docker compose pull && docker compose up -d`

**Sources:** [next.config.mjs L14-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L14-L46)

 [Dockerfile L35-L48](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L35-L48)

 [docker-compose.yml L19-L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L19-L21)

---

## Performance Optimization

### Next.js Standalone Mode

The [next.config.mjs L3](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L3-L3)

 enables `output: 'standalone'`, which:

* Creates self-contained deployment with only required dependencies
* Reduces image size from ~1.2GB to ~200MB
* Eliminates `node_modules` bloat in production

### Build Optimizations

```javascript
# Enable BuildKit for faster builds
export DOCKER_BUILDKIT=1
export COMPOSE_DOCKER_CLI_BUILD=1

# Build with build cache
docker compose build

# Multi-platform build (if targeting ARM servers)
docker buildx build --platform linux/amd64,linux/arm64 -t sena-gestion:latest .
```

### Runtime Configuration

#### Telemetry Disabled

[Dockerfile L24-L33](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L24-L33)

 disables Next.js telemetry:

```
ENV NEXT_TELEMETRY_DISABLED 1
```

#### Image Optimization

[next.config.mjs L11-L13](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L11-L13)

 disables built-in image optimization:

```yaml
images: {
  unoptimized: true,
}
```

This is appropriate for Dockerized deployments where images are served statically.

### Resource Limits

Add resource constraints to [docker-compose.yml](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml)

:

```yaml
services:
  app:
    # ... existing config
    deploy:
      resources:
        limits:
          cpus: '2.0'
          memory: 2G
        reservations:
          cpus: '0.5'
          memory: 512M
```

### Database Connection Pooling

Ensure Neon connection string includes PgBouncer:

```
DATABASE_URL="postgresql://user:pass@host/db?sslmode=require&pgbouncer=true"
```

This enables connection pooling for serverless environments.

### Performance Monitoring Endpoints

```javascript
# Check container resource usage
docker stats sena-gestion-prod

# Monitor logs for slow queries
docker compose logs -f app | grep "slow query"

# Check database connection pool
docker exec sena-gestion-prod node -e "console.log(process.env.DATABASE_URL)"
```

**Sources:** [next.config.mjs L3](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L3-L3)

 [next.config.mjs L11-L13](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L11-L13)

 [Dockerfile L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L24-L24)

---

## Monitoring and Health Checks

### Container Health Monitoring

```markdown
# Check container status
docker compose ps

# View real-time logs
docker compose logs -f app

# View last 100 lines
docker compose logs --tail=100 app

# Filter for errors
docker compose logs app | grep -i error
```

### Application Health Checks

Implement periodic health checks:

```php
# Create health check script
cat > /usr/local/bin/health-check.sh << 'EOF'
#!/bin/bash
RESPONSE=$(curl -s -o /dev/null -w "%{http_code}" http://localhost:3007/api/health)
if [ "$RESPONSE" != "200" ]; then
    echo "Health check failed with status $RESPONSE"
    # Send alert notification
    exit 1
fi
echo "Health check passed"
EOF

chmod +x /usr/local/bin/health-check.sh

# Add to crontab (every 5 minutes)
(crontab -l 2>/dev/null; echo "*/5 * * * * /usr/local/bin/health-check.sh") | crontab -
```

### Log Aggregation

Configure log rotation for Docker:

```sql
# Create Docker daemon config
sudo nano /etc/docker/daemon.json
```

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

```markdown
# Restart Docker daemon
sudo systemctl restart docker

# Restart application container
docker compose restart
```

### System Monitoring Diagram

```mermaid
flowchart TD

HealthScript["health-check.sh<br>Cron Job (*/5 * * * *)"]
DockerStats["docker stats<br>Resource Monitoring"]
LogRotation["Log Rotation<br>max-size: 10m"]
Container["sena-gestion-prod"]
HealthAPI["/api/health<br>Health Check Endpoint"]
AppLogs["Application Logs<br>stdout/stderr"]
NeonDB["Neon PostgreSQL"]
AlertService["Alert Service<br>Email/Slack"]

HealthScript --> HealthAPI
HealthAPI --> NeonDB
NeonDB --> HealthAPI
HealthAPI --> HealthScript
HealthScript --> AlertService
DockerStats --> Container
LogRotation --> AppLogs

subgraph subGraph2 ["External Services"]
    NeonDB
    AlertService
end

subgraph subGraph1 ["Production Container"]
    Container
    HealthAPI
    AppLogs
    Container --> AppLogs
end

subgraph subGraph0 ["Monitoring Tools"]
    HealthScript
    DockerStats
    LogRotation
end
```

**Sources:** [docker-compose.yml L10](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L10-L10)

 [docker-compose.yml L19](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L19-L19)

---

## Database Operations

### Running Migrations

Prisma migrations must be executed before deploying new versions:

```markdown
# Deploy pending migrations
docker compose run --rm app npx prisma migrate deploy

# Check migration history
docker compose run --rm app npx prisma migrate status

# Generate Prisma Client (if schema.prisma changed)
docker compose run --rm app npx prisma generate
```

### Database Backup Strategy

Neon PostgreSQL provides automated backups, but manual exports are recommended:

```markdown
# Export database using pg_dump
docker compose run --rm app npx prisma db execute \
  --file backup.sql \
  --stdin < /dev/null

# Alternative: Use Neon's web interface for point-in-time recovery
```

### Database Connection Testing

```javascript
# Test database connectivity
docker compose run --rm app node -e "
  const { PrismaClient } = require('@prisma/client');
  const prisma = new PrismaClient();
  prisma.\$connect()
    .then(() => console.log('Database connected'))
    .catch(err => console.error('Connection failed:', err))
    .finally(() => prisma.\$disconnect());
"
```

### Zero-Downtime Deployments

For critical updates, use blue-green deployment:

```sql
# Start new container with different name
docker compose -p sena-gestion-blue up -d

# Wait for health check
sleep 10
curl http://localhost:3008/api/health

# Update reverse proxy to point to new container
# Then stop old container
docker compose -p sena-gestion-green down
```

### Database Operations Workflow

```mermaid
sequenceDiagram
  participant Admin
  participant sena-gestion-prod
  participant Prisma CLI
  participant Neon PostgreSQL

  note over Admin,Neon PostgreSQL: Pre-Deployment Migration
  Admin->>sena-gestion-prod: docker compose run --rm app
  sena-gestion-prod->>Prisma CLI: npx prisma migrate status
  Prisma CLI->>Neon PostgreSQL: Query _prisma_migrations table
  Neon PostgreSQL-->>Prisma CLI: Current migration version
  Prisma CLI-->>Admin: Show pending migrations
  Admin->>sena-gestion-prod: npx prisma migrate deploy
  sena-gestion-prod->>Prisma CLI: Execute migrations
  Prisma CLI->>Neon PostgreSQL: BEGIN TRANSACTION
  Prisma CLI->>Neon PostgreSQL: CREATE TABLE ... / ALTER TABLE ...
  Prisma CLI->>Neon PostgreSQL: INSERT INTO _prisma_migrations
  Prisma CLI->>Neon PostgreSQL: COMMIT
  Neon PostgreSQL-->>Prisma CLI: Migrations applied
  Prisma CLI-->>Admin: Migration successful
  note over Admin,Neon PostgreSQL: Post-Deployment Verification
  Admin->>sena-gestion-prod: Test database connection
  sena-gestion-prod->>Neon PostgreSQL: SELECT 1
  Neon PostgreSQL-->>sena-gestion-prod: Connection OK
  sena-gestion-prod-->>Admin: Verification passed
```

**Sources:** [Dockerfile L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L21-L21)

 [docker-compose.yml L15](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L15-L15)

---

## Troubleshooting Common Issues

### Container Won't Start

**Symptom**: `docker compose up -d` fails or container exits immediately

```markdown
# Check container logs
docker compose logs app

# Common causes and solutions:
```

| Error Message | Cause | Solution |
| --- | --- | --- |
| `DATABASE_URL is not set` | Missing environment variable | Verify `.env` file exists and is readable |
| `ECONNREFUSED` | Database unreachable | Check `DATABASE_URL` connection string |
| `Error: Invalid JWT secret` | `JWT_SECRET` not set or too short | Generate new secret with `openssl rand -base64 32` |
| `EADDRINUSE: port 3000 already in use` | Port conflict inside container | Check for zombie processes: `docker ps -a` |
| `Permission denied: /app/.next` | File ownership issue | Rebuild image: `docker compose build --no-cache` |

### Database Connection Issues

```javascript
# Test database connection from container
docker exec sena-gestion-prod node -e "
  const { Client } = require('pg');
  const client = new Client({
    connectionString: process.env.DATABASE_URL
  });
  client.connect()
    .then(() => {
      console.log('Connected successfully');
      return client.query('SELECT NOW()');
    })
    .then(result => console.log('Server time:', result.rows[0].now))
    .catch(err => console.error('Connection error:', err))
    .finally(() => client.end());
"
```

### Port Binding Issues

```markdown
# Check if host port 3007 is available
sudo netstat -tulpn | grep 3007

# If port is in use, modify docker-compose.yml port mapping
# Change "3007:3000" to "3008:3000"

# Or stop conflicting service
sudo systemctl stop <service-name>
```

### Build Failures

**Symptom**: `docker compose build` fails during `npm run build`

```markdown
# Check build logs
docker compose build 2>&1 | tee build.log

# Common issues:
```

* **Errores de TypeScript** :[next.config.mjs L8-L10](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L8-L10) Ignora los errores, pero comprueba si hay problemas críticos
* **Errores de ESLint** :[next.config.mjs L5-L7](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L5-L7) Ignora el pelusa, pero soluciona problemas críticos
* **Sin memoria** : aumente el límite de memoria de Docker en la configuración de Docker Desktop

```markdown
# Build with verbose output
docker compose build --progress=plain --no-cache
```

### La variable de entorno no se carga

```markdown
# Verify environment variables are set in container
docker exec sena-gestion-prod env | grep -E "DATABASE_URL|NEXTAUTH_SECRET|JWT_SECRET"

# If missing, check .env file format (no spaces around =)
# Correct: NEXTAUTH_SECRET=abc123
# Wrong:   NEXTAUTH_SECRET = abc123

# Restart container to reload environment
docker compose restart
```

### Fallos de migración

```markdown
# Check migration status
docker compose run --rm app npx prisma migrate status

# If migrations are out of sync:
# 1. Reset database (WARNING: data loss)
docker compose run --rm app npx prisma migrate reset --force

# 2. Or mark migrations as applied (if already run manually)
docker compose run --rm app npx prisma migrate resolve --applied "20240115_migration_name"
```

### Errores del certificado SSL

```markdown
# If Neon connection fails with SSL error
# Ensure DATABASE_URL includes sslmode=require
# Example:
DATABASE_URL="postgresql://user:pass@host/db?sslmode=require"

# For local development (not production), you can use:
DATABASE_URL="postgresql://user:pass@host/db?sslmode=disable"
```

### Árbol de decisiones para la resolución de problemas

```mermaid
flowchart TD

Start["Container Won't Start"]
CheckLogs["Check docker compose logs"]
EnvError["Missing Environment Variables"]
EnvFix["Unsupported markdown: list"]
DBError["Database Connection Error"]
DBFix["Unsupported markdown: list"]
PortError["Port Binding Error"]
PortFix["Unsupported markdown: list"]
BuildError["Build Error"]
BuildFix["Unsupported markdown: list"]
MigrationError["Migration Error"]
MigrationFix["Unsupported markdown: list"]
OtherError["Other Error"]
OtherFix["Unsupported markdown: list"]
Success["Container Running"]

Start --> CheckLogs
CheckLogs --> EnvError
CheckLogs --> DBError
CheckLogs --> PortError
CheckLogs --> BuildError
CheckLogs --> MigrationError
CheckLogs --> OtherError
EnvError --> EnvFix
DBError --> DBFix
PortError --> PortFix
BuildError --> BuildFix
MigrationError --> MigrationFix
OtherError --> OtherFix
EnvFix --> Success
DBFix --> Success
PortFix --> Success
BuildFix --> Success
MigrationFix --> Success
OtherFix --> Success
```

**Fuentes:** [docker-compose.yml L12-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L12-L18)

 [Dockerfile L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L21-L21)

 [next.config.mjs L5-L10](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L5-L10)

---

## Mantenimiento y actualizaciones

### Actualización del código de la aplicación

```markdown
# Pull latest changes
git pull origin main

# Rebuild and restart container
docker compose build --no-cache
docker compose up -d

# Verify new version is running
docker compose logs -f app
```

### Actualización de dependencias

```sql
# Update package.json versions
npm update

# Rebuild Docker image
docker compose build --no-cache

# Test in staging before production
```

### Gestión de contenedores

```markdown
# Stop container
docker compose stop

# Start container
docker compose start

# Restart container (reload environment variables)
docker compose restart

# Remove container and volumes
docker compose down -v

# View container resource usage
docker stats sena-gestion-prod
```

### Procedimiento de reversión

```markdown
# List available image versions
docker images | grep sena-gestion

# Tag current image before deploying new version
docker tag sena-gestion-prod:latest sena-gestion-prod:backup-$(date +%Y%m%d)

# If new deployment fails, rollback to previous image
docker tag sena-gestion-prod:backup-20240115 sena-gestion-prod:latest
docker compose up -d --force-recreate
```

**Fuentes:** [docker-compose.yml L4-L7](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L4-L7)

 [docker-compose.yml L19](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L19-L19)

---

## Lista de verificación de producción

Utilice esta lista de verificación antes de implementar en producción:

### Pre-despliegue

* `.env`archivo configurado con valores de producción
* `DATABASE_URL`apunta a la instancia de producción Neon
* `NEXTAUTH_URL`coincide con el dominio de producción
* `NEXTAUTH_SECRET`y `JWT_SECRET`son seguros (32+ caracteres)
* Certificado SSL instalado y configurado
* El registro DNS A apunta al servidor de producción
* Reglas de firewall configuradas (permitir solo 80, 443, 22)
* Docker y Docker Compose instalados
* Copia de seguridad de la base de datos de producción completada

### Despliegue

* Código extraído de la rama de producción
* `docker compose build`se completa con éxito
* `npx prisma migrate deploy`se completa sin errores
* `docker compose up -d`inicia contenedor
* El estado del contenedor es "Activo" ( `docker compose ps`)
* El punto final de verificación de estado devuelve 200 ( `curl /api/health`)
* Aplicación accesible a través del nombre de dominio
* Las redirecciones HTTPS funcionan correctamente

### Después del despliegue

* Funcionalidad de inicio de sesión probada
* Solicitud creación probada
* Notificaciones funcionando
* Exportación PDF/Excel probada
* Control de acceso basado en roles verificado
* Rotación de registros configurada
* Se agregó un trabajo cron de verificación de estado
* Alertas de monitoreo configuradas
* Estrategia de respaldo verificada
* Documentación actualizada

### Verificación de seguridad

* Encabezados de seguridad presentes ( `curl -I https://domain`)
* No hay secretos expuestos en los registros
* El contenedor se ejecuta como usuario no root
* La conexión a la base de datos utiliza SSL
* No se exponen puertos innecesarios
* `.env`permisos de archivo establecidos en 600
* El socket del demonio Docker no está montado

**Fuentes:** [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

 [next.config.mjs L1-L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L1-L49)
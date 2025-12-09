# Installation and Deployment

> **Relevant source files**
> * [Dockerfile](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile)
> * [docker-compose.yml](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml)

## Purpose and Scope

This document provides step-by-step instructions for deploying the SENA Gestión Complementarias application using Docker containerization. It covers environment setup, Docker build process, container orchestration, and verification procedures.

For detailed information about configuration files and environment variables, see [Configuration](/axchisan/gestionComplementarias/2.2-configuration). For database initialization and migrations, see [Database Setup](/axchisan/gestionComplementarias/2.3-database-setup). For production-specific considerations and monitoring, see [Production Deployment Guide](/axchisan/gestionComplementarias/7.2-production-deployment-guide).

---

## Prerequisites

Before deploying the application, ensure the following tools and services are available:

| Requirement | Version | Purpose |
| --- | --- | --- |
| Docker | 20.10+ | Container runtime |
| Docker Compose | 2.0+ | Multi-container orchestration |
| Node.js | 20.x | Required for local development only |
| Neon PostgreSQL | N/A | External database service |
| Git | 2.x+ | Source code management |

**System Requirements:**

* Linux-based host (recommended) or Windows/macOS with Docker Desktop
* Minimum 2GB RAM for container
* Port 3007 available on host machine
* Network access to Neon database endpoint

---

## Environment Configuration

The application requires four critical environment variables to function. Create a `.env` file in the project root directory:

```

```

### Environment Variable Reference

| Variable | Description | Example Value | Required |
| --- | --- | --- | --- |
| `DATABASE_URL` | Neon PostgreSQL connection string with SSL | `postgresql://user:pass@ep-example.neon.tech/db` | Yes |
| `NEXTAUTH_SECRET` | Secret key for NextAuth.js session encryption | Random 32+ character string | Yes |
| `NEXTAUTH_URL` | Public URL where application is accessible | `http://localhost:3007` or production URL | Yes |
| `JWT_SECRET` | Secret key for JWT token signing | Random 32+ character string | Yes |
| `NODE_ENV` | Runtime environment mode | `production` | Yes |

**Generating Secure Secrets:**

```

```

**Sources:** [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

 [Dockerfile L32](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L32-L32)

---

## Docker Multi-Stage Build Architecture

The application uses a three-stage Docker build process to optimize image size and security.

### Build Process Flow

```

```

**Sources:** [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

### Stage Breakdown

#### Stage 1: Dependencies (deps)

**Purpose:** Install production dependencies in isolation

[Dockerfile L5-L11](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L5-L11)

 defines the dependencies stage:

* Installs `libc6-compat` for Alpine Linux compatibility
* Copies only `package.json` and `package-lock.json`
* Runs `npm ci --only=production` for reproducible builds
* Output: Optimized `node_modules/` directory

#### Stage 2: Builder (builder)

**Purpose:** Generate Prisma client and build Next.js application

[Dockerfile L14-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L14-L25)

 defines the builder stage:

* Installs `openssl` required by Prisma
* Copies `node_modules` from `deps` stage
* Runs `npx prisma generate` to create type-safe database client
* Executes `npm run build` with `NEXT_TELEMETRY_DISABLED=1`
* Produces `.next/standalone` output (self-contained server)

**Key Configuration:** Next.js must be configured with `output: 'standalone'` in `next.config.ts` to enable this optimization.

#### Stage 3: Runner (runner)

**Purpose:** Create minimal production runtime image

[Dockerfile L28-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L28-L55)

 defines the final production stage:

* Creates non-root user `nextjs` with UID 1001 for security
* Copies only necessary files: `public/`, `.next/standalone`, `.next/static`
* Sets ownership to `nextjs:nodejs` user
* Exposes port 3000 internally
* Launches server with `node server.js` instead of `npm start`

**Security Features:**

* Non-root user execution [Dockerfile L48](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L48-L48)
* Minimal attack surface (no build tools in final image)
* Telemetry disabled [Dockerfile L33](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L33-L33)

**Sources:** [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

---

## Container Orchestration with Docker Compose

The application uses Docker Compose for simplified deployment and network configuration.

### Service Architecture

```mermaid
flowchart TD

bridge["Driver: bridge"]
port3007["Port 3007"]
service_app["Service: app"]
build_ctx["Build Context: .<br>Dockerfile: ./Dockerfile"]
env_vars["Environment Variables:<br>- NODE_ENV=production<br>- DATABASE_URL<br>- NEXTAUTH_SECRET<br>- NEXTAUTH_URL<br>- JWT_SECRET"]
ports_map["Port Mapping:<br>3007:3000"]
restart_policy["Restart Policy:<br>unless-stopped"]
nextjs_app["Next.js Application<br>Port 3000"]
neon_db["Neon PostgreSQL<br>DATABASE_URL"]

port3007 --> ports_map
ports_map --> nextjs_app

subgraph external ["External Services"]
    neon_db
end

subgraph container ["Container: sena-gestion-prod"]
    nextjs_app
end

subgraph docker_compose ["docker-compose.yml"]
    service_app
    build_ctx
    env_vars
    ports_map
    restart_policy
    service_app --> build_ctx
    service_app --> env_vars
    service_app --> ports_map
    service_app --> restart_policy
end

subgraph host ["Host Machine"]
    port3007
end

subgraph network ["Docker Network: sena-network"]
    bridge
end
```

**Sources:** [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

### Docker Compose Configuration Reference

**Service Definition:** [docker-compose.yml L4-L19](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L4-L19)

| Configuration | Value | Purpose |
| --- | --- | --- |
| `container_name` | `sena-gestion-prod` | Static container identifier |
| `ports` | `"3007:3000"` | Maps host port 3007 to container port 3000 |
| `restart` | `unless-stopped` | Automatic restart on failure (except manual stop) |
| `networks` | `sena-network` | Isolates container in custom bridge network |

**Network Configuration:** [docker-compose.yml L22-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L22-L24)

* Network name: `sena-network`
* Driver: `bridge` (provides isolated subnet)
* Enables container-to-container communication if additional services are added

**Sources:** [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

---

## Deployment Steps

### 1. Clone Repository

```

```

### 2. Configure Environment

Create `.env` file with required variables (see [Environment Configuration](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Environment Configuration)

 section above):

```

```

### 3. Build and Start Container

Using Docker Compose:

```

```

**Build Process Timeline:**

1. Pull `node:20-alpine` base image
2. Install dependencies (~2-3 minutes)
3. Generate Prisma client (~30 seconds)
4. Build Next.js application (~3-5 minutes)
5. Create final production image
6. Start container on port 3007

### 4. Run Database Migrations

After container is running, execute Prisma migrations:

```

```

**Note:** For detailed database setup including seeding initial data, see [Database Setup](/axchisan/gestionComplementarias/2.3-database-setup).

**Sources:** [docker-compose.yml L4-L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L4-L21)

 [Dockerfile L20-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L20-L25)

---

## Verification and Testing

### Check Container Status

```

```

### Verify Application Accessibility

```

```

**Expected Response:**

* Landing page loads successfully
* Login page accessible at `/login`
* Static assets load without errors

### Inspect Container Logs

```

```

### Verify Network Configuration

```

```

**Sources:** [docker-compose.yml L10-L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L10-L12)

 [docker-compose.yml L22-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L22-L24)

---

## Container Management Commands

### Common Operations

| Command | Purpose |
| --- | --- |
| `docker-compose up -d` | Start container in detached mode |
| `docker-compose down` | Stop and remove container |
| `docker-compose restart` | Restart container without rebuilding |
| `docker-compose logs -f app` | Follow container logs in real-time |
| `docker exec -it sena-gestion-prod sh` | Access container shell |
| `docker-compose build --no-cache` | Rebuild image from scratch |

### Update Deployment

```

```

### Stop and Clean Up

```

```

**Sources:** [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

---

## Troubleshooting

### Container Fails to Start

**Symptom:** Container exits immediately after starting

**Diagnosis:**

```

```

**Common Causes:**

| Error | Cause | Solution |
| --- | --- | --- |
| `DATABASE_URL` invalid | Incorrect database connection string | Verify Neon PostgreSQL credentials and SSL mode |
| Port 3007 already in use | Another service using the port | Change port mapping in [docker-compose.yml L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L12-L12) |
| Missing environment variables | `.env` file not loaded | Ensure `.env` file exists in project root |
| Prisma client not generated | Build failed during `npx prisma generate` | Rebuild with `docker-compose build --no-cache` |

### Application Not Accessible

**Symptom:** Cannot connect to `http://localhost:3007`

**Diagnosis:**

```

```

**Solutions:**

* Verify firewall allows traffic on port 3007
* Check if Docker daemon is running: `systemctl status docker`
* Inspect [docker-compose.yml L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L12-L12)  port mapping configuration

### Database Connection Errors

**Symptom:** Error: "Can't reach database server"

**Diagnosis:**

```

```

**Solutions:**

* Verify `DATABASE_URL` includes `?sslmode=require`
* Check Neon database is active and not paused
* Confirm database user has correct permissions
* Test connectivity: `docker exec sena-gestion-prod ping your-neon-host`

### Build Failures

**Symptom:** Docker build fails during `npm run build`

**Common Issues:**

1. **Out of Memory:** Increase Docker memory limit to 4GB
2. **Prisma Generation Fails:** Ensure `openssl` is installed [Dockerfile L15-L29](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L15-L29)
3. **TypeScript Errors:** Check for compilation errors in source code

**Rebuild from Scratch:**

```

```

**Sources:** [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

---

## Port and Network Architecture

```mermaid
flowchart TD

client["Client Browser"]
curl["curl/API Client"]
port3007["Port 3007<br>HTTPS/HTTP"]
container["Container: sena-gestion-prod<br>IP: 172.x.x.2"]
nextjs["Next.js Server<br>Port 3000<br>HOSTNAME: 0.0.0.0"]
server_js["node server.js"]
neon["Neon PostgreSQL<br>ep-xxx.neon.tech:5432"]

client --> port3007
curl --> port3007
port3007 --> container
container --> nextjs
container --> neon

subgraph ext_services ["External Services"]
    neon
end

subgraph container_int ["Container Internal"]
    nextjs
    server_js
    nextjs --> server_js
end

subgraph docker_net ["Docker Network: sena-network172.x.x.0/24"]
    container
end

subgraph host ["Host Machine Ports"]
    port3007
end

subgraph external ["External Access"]
    client
    curl
end
```

**Key Observations:**

* External traffic reaches host on port 3007 [docker-compose.yml L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L12-L12)
* Docker maps to container's internal port 3000 [Dockerfile L50-L52](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L50-L52)
* Next.js binds to `0.0.0.0` to accept all network interfaces [Dockerfile L53](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L53-L53)
* Container communicates with Neon over public internet (outbound connection)
* Custom bridge network `sena-network` isolates container [docker-compose.yml L20-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L20-L24)

**Sources:** [docker-compose.yml L12-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L12-L24)

 [Dockerfile L50-L53](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L50-L53)

---

## Security Considerations

### Container Security

| Security Feature | Implementation | Location |
| --- | --- | --- |
| Non-root user | Runs as `nextjs` user (UID 1001) | [Dockerfile L35-L48](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L35-L48) |
| Read-only filesystem | Static files owned by `nextjs:nodejs` | [Dockerfile L42-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L42-L46) |
| Minimal base image | Alpine Linux (5MB base) | [Dockerfile L2](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L2-L2) |
| No build tools | Production dependencies only | [Dockerfile L11](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L11-L11) |
| Disabled telemetry | `NEXT_TELEMETRY_DISABLED=1` | [Dockerfile L24-L33](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L24-L33) |

### Environment Variable Security

**Best Practices:**

* Never commit `.env` file to version control
* Use Docker secrets for sensitive production deployments
* Rotate `JWT_SECRET` and `NEXTAUTH_SECRET` periodically
* Use strong, random secrets (minimum 32 characters)

**Production Recommendations:**

* Use environment variable injection from CI/CD pipeline
* Store secrets in secure vault (e.g., AWS Secrets Manager, HashiCorp Vault)
* Enable SSL/TLS on reverse proxy (Nginx, Traefik) in front of container

**Sources:** [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

 [Dockerfile L35-L48](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L35-L48)

---

## Next Steps

After successful deployment:

1. **Configure Application Settings:** See [Configuration](/axchisan/gestionComplementarias/2.2-configuration) for Next.js configuration, security headers, and CORS settings
2. **Initialize Database:** See [Database Setup](/axchisan/gestionComplementarias/2.3-database-setup) for running migrations and seeding initial data
3. **Create Admin User:** Use database seeding or direct SQL to create first administrator account
4. **Configure Production Environment:** See [Production Deployment Guide](/axchisan/gestionComplementarias/7.2-production-deployment-guide) for SSL, monitoring, and backup strategies

**Sources:** [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)
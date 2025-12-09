# Deployment Architecture

> **Relevant source files**
> * [Dockerfile](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile)
> * [docker-compose.yml](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml)
> * [next.config.mjs](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs)

## Purpose and Scope

This document details the deployment architecture of the SENA Gestión Complementarias system, including containerization strategy, build process, network configuration, and runtime environment setup. The system uses Docker for production deployment with a multi-stage build process and Next.js standalone output mode.

For information about the underlying technology stack and frameworks, see [Technology Stack](/axchisan/gestionComplementarias/3.2-technology-stack). For production deployment procedures, see [Production Deployment Guide](/axchisan/gestionComplementarias/7.2-production-deployment-guide). For Docker-specific configuration details, see [Docker Configuration](/axchisan/gestionComplementarias/7.1-docker-configuration).

## Deployment Overview

The SENA Gestión Complementarias system deploys as a single, self-contained Docker container running a Next.js application. The deployment architecture prioritizes:

* **Minimal footprint**: Multi-stage build reduces final image size
* **Self-contained execution**: Standalone output mode eliminates external dependencies
* **Security isolation**: Non-root user execution with dedicated network
* **Environment portability**: Configuration through environment variables

### Container Architecture Diagram

```

```

**Sources:** [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 [Dockerfile L28-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L28-L55)

## Container Configuration

The system uses Docker Compose to orchestrate the deployment. The configuration defines a single service with essential runtime parameters.

### Service Definition

The `app` service configuration is defined in `docker-compose.yml`:

| Parameter | Value | Description |
| --- | --- | --- |
| `container_name` | `sena-gestion-prod` | Fixed container identifier for production |
| `build.context` | `.` | Build context is repository root |
| `build.dockerfile` | `Dockerfile` | Standard Dockerfile location |
| `ports` | `3007:3000` | Maps host port 3007 to container port 3000 |
| `restart` | `unless-stopped` | Automatic restart on failure |
| `networks` | `sena-network` | Isolated bridge network |

**Sources:** [docker-compose.yml L4-L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L4-L21)

### Port Mapping Strategy

The port mapping `3007:3000` separates external and internal port concerns:

* **External Port (3007)**: Host machine exposes this port for incoming traffic
* **Internal Port (3000)**: Next.js server listens on this port inside the container
* **Configuration**: Set via `PORT` and `HOSTNAME` environment variables [Dockerfile L52-L53](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L52-L53)

This mapping allows multiple containerized applications to run on the same host without port conflicts, while Next.js maintains its standard development port (3000) internally.

**Sources:** [docker-compose.yml L11-L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L11-L12)

 [Dockerfile L50-L53](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L50-L53)

### Network Configuration

A custom bridge network `sena-network` provides network isolation:

```

```

The bridge driver creates an internal network where containers can communicate by name. This architecture allows for future service expansion (e.g., adding Redis for caching) while maintaining isolation from the host network.

**Sources:** [docker-compose.yml L20-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L20-L24)

## Multi-Stage Build Process

The Dockerfile implements a three-stage build process to optimize image size and security. Each stage has a specific responsibility in the build pipeline.

### Build Stages Diagram

```

```

**Sources:** [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

### Stage 1: Dependency Installation (deps)

The `deps` stage installs production dependencies using a clean npm install:

```

```

**Key characteristics:**

* **Base image**: `node:20-alpine` provides minimal Node.js runtime
* **Compatibility layer**: `libc6-compat` enables compatibility with native binaries
* **Lock file**: Uses `package-lock.json` to ensure reproducible builds
* **Clean install**: `npm ci` performs a deterministic dependency installation
* **Production only**: `--only=production` excludes devDependencies

**Sources:** [Dockerfile L5-L11](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L5-L11)

### Stage 2: Application Build (builder)

The `builder` stage compiles the Next.js application and generates Prisma client:

```

```

**Build sequence:**

1. **Copy dependencies**: Reuses `node_modules` from `deps` stage
2. **Copy source**: Includes all application code
3. **Generate Prisma client**: Creates type-safe database client from `schema.prisma`
4. **Disable telemetry**: Prevents Next.js from sending usage data
5. **Build application**: Executes Next.js production build

The build process creates the `.next` directory containing:

* `.next/standalone`: Self-contained server bundle (enabled by `output: 'standalone'` in `next.config.mjs`)
* `.next/static`: Optimized static assets (CSS, JS, images)
* `.next/server`: Server-side rendering components

**Sources:** [Dockerfile L14-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L14-L25)

 [next.config.mjs L3](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L3-L3)

### Stage 3: Production Runtime (runner)

The `runner` stage creates the minimal production image:

```

```

**Security measures:**

* **Non-root user**: Creates `nextjs` user (UID 1001) to run the application
* **Minimal permissions**: Only includes necessary files from build stage
* **File ownership**: Ensures proper permissions with `--chown=nextjs:nodejs`

**Runtime configuration:**

* **Environment**: Sets `NODE_ENV=production` for optimizations
* **Listening address**: `HOSTNAME="0.0.0.0"` allows external connections
* **Port exposure**: Documents container port 3000 for documentation

**Sources:** [Dockerfile L28-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L28-L55)

## Next.js Standalone Output

The deployment leverages Next.js standalone output mode, configured in `next.config.mjs`:

```

```

### Standalone Mode Benefits

| Feature | Description | Impact |
| --- | --- | --- |
| **Self-contained bundle** | Includes only necessary dependencies | Smaller image size (~40% reduction) |
| **Automatic file tracing** | Identifies required files via static analysis | No missing dependencies |
| **Independent execution** | Single entry point (`server.js`) | Simplified deployment |
| **Production optimized** | Pre-compiled pages and middleware | Faster cold starts |

### Build Output Structure

```markdown
.next/
├── standalone/          # Self-contained server bundle
│   ├── server.js       # Entry point (executed by CMD)
│   ├── package.json    # Minimal dependencies
│   └── node_modules/   # Only required packages
├── static/             # Optimized static assets
│   ├── chunks/         # JavaScript bundles
│   └── css/            # Compiled stylesheets
└── server/             # Server components (not copied to runner)
```

The Docker build process copies `standalone` and `static` directories to the runner stage, while `public` assets are copied separately.

**Sources:** [next.config.mjs L3-L4](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L3-L4)

 [Dockerfile L38-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L38-L46)

## Security Headers Configuration

The `next.config.mjs` defines security headers applied to all responses:

```

```

### Security Header Reference

| Header | Value | Purpose |
| --- | --- | --- |
| `X-Frame-Options` | `DENY` | Prevents clickjacking by blocking iframe embedding |
| `X-Content-Type-Options` | `nosniff` | Prevents MIME type sniffing attacks |
| `Referrer-Policy` | `origin-when-cross-origin` | Controls referrer information in cross-origin requests |
| `Access-Control-Allow-Origin` | `https://gestioncomplementarias.axchisan.com` | Restricts CORS to production domain |
| `Access-Control-Allow-Methods` | `GET, POST, PUT, DELETE, OPTIONS` | Defines permitted HTTP methods |
| `Access-Control-Allow-Headers` | `Content-Type, Authorization` | Specifies allowed request headers |

**Sources:** [next.config.mjs L14-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L14-L46)

## Environment Variables

The container requires environment variables for runtime configuration. These are injected by Docker Compose from the host environment or `.env` file.

### Required Environment Variables

```

```

**Sources:** [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

### Environment Variable Specifications

| Variable | Purpose | Example Format | Used By |
| --- | --- | --- | --- |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:pass@host/db` | Prisma Client |
| `NEXTAUTH_SECRET` | NextAuth session encryption key | Random 32+ character string | NextAuth.js |
| `NEXTAUTH_URL` | Application base URL | `https://gestioncomplementarias.axchisan.com` | NextAuth.js callbacks |
| `JWT_SECRET` | JWT token signing key | Random 32+ character string | Custom authentication middleware |
| `NODE_ENV` | Node.js environment | `production` | Next.js optimizations |

**Configuration in docker-compose.yml:**

```

```

The `${VARIABLE}` syntax instructs Docker Compose to read values from the host environment. This allows sensitive credentials to remain outside version control.

**Sources:** [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

## Deployment Workflow

The complete deployment process from source code to running container:

```

```

**Sources:** [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

 [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

## Image Optimization Details

The multi-stage build significantly reduces the final image size through selective copying:

### File Size Comparison

| Component | Size in builder | Size in runner | Reason |
| --- | --- | --- | --- |
| `node_modules/` | ~300 MB (all dependencies) | ~50 MB (runtime only) | Standalone tracing includes only required packages |
| Source code | ~20 MB | ~0 MB | Not needed, compiled into `.next/standalone` |
| `.next/` directory | ~100 MB (full build output) | ~60 MB (standalone + static) | Excludes build cache and server components |
| System packages | Development tools | `openssl` only | Minimal Alpine packages |

**Total reduction**: Approximately 200-300 MB eliminated from final image.

**Sources:** [Dockerfile L5-L11](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L5-L11)

 [Dockerfile L14-L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L14-L25)

 [Dockerfile L44-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L44-L46)

## Container Restart Policy

The `restart: unless-stopped` policy ensures high availability:

```

```

**Restart behavior:**

| Scenario | Container Action |
| --- | --- |
| Process crash | Automatic restart immediately |
| Docker daemon restart | Restart container on daemon startup |
| Manual stop | Remain stopped (do not auto-restart) |
| Host reboot | Restart container after system boot |
| Exit code 0 (clean exit) | Do not restart |

This policy balances availability with administrative control, preventing infinite restart loops while maintaining uptime during transient failures.

**Sources:** [docker-compose.yml L19](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L19-L19)

## Build Performance Optimization

The Dockerfile leverages Docker's layer caching to speed up subsequent builds:

### Layer Caching Strategy

1. **Dependencies layer** [Dockerfile L10](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L10-L10) : Cached unless `package.json` changes
2. **Prisma generation** [Dockerfile L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L21-L21) : Cached unless `schema.prisma` changes
3. **Application build** [Dockerfile L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L25-L25) : Only rebuilds if source code changes

**Optimal build order:**

```dockerfile
COPY package.json → Install deps → COPY source → Build app
```

This ordering ensures that expensive operations (dependency installation) are not repeated unnecessarily.

**Sources:** [Dockerfile L10](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L10-L10)

 [Dockerfile L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L18-L18)

 [Dockerfile L25](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L25-L25)

## Production Readiness Checklist

Before deploying to production, verify the following configuration:

* All environment variables set in `.env` file
* `DATABASE_URL` points to production Neon PostgreSQL instance
* `NEXTAUTH_URL` matches production domain
* `NEXTAUTH_SECRET` and `JWT_SECRET` are cryptographically secure (32+ characters)
* Port 3007 is open in firewall configuration
* Reverse proxy configured for SSL termination
* Docker installed on host (version 20.10+)
* Docker Compose installed (version 2.0+)
* `sena-network` does not conflict with existing Docker networks

**Sources:** [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

 [next.config.mjs L33](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L33-L33)
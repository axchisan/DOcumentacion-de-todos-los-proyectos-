# Getting Started

> **Relevant source files**
> * [Dockerfile](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile)
> * [docker-compose.yml](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml)
> * [next.config.mjs](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs)

This document provides a practical guide for setting up and running the SENA Gestión Complementarias application. It covers the essential prerequisites, environment configuration, and basic deployment steps needed to launch the system in either development or production mode.

For detailed deployment instructions and Docker configuration, see [Installation and Deployment](/axchisan/gestionComplementarias/2.1-installation-and-deployment). For comprehensive environment variable documentation, see [Configuration](/axchisan/gestionComplementarias/2.2-configuration). For database schema and migration setup, see [Database Setup](/axchisan/gestionComplementarias/2.3-database-setup).

---

## Prerequisites

Before starting, ensure the following tools are installed on your system:

| Tool | Minimum Version | Purpose |
| --- | --- | --- |
| Docker | 20.x or higher | Container runtime for application deployment |
| Docker Compose | 2.x or higher | Service orchestration |
| Node.js | 20.x | Required for local development (optional for Docker deployment) |
| npm | 9.x or higher | Package manager for local development |
| PostgreSQL Client | 14.x or higher | Database access (optional, for debugging) |

**External Dependencies:**

* Access to a Neon PostgreSQL database instance with connection URL
* SSL certificates (if deploying with HTTPS)

Sources: [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 [Dockerfile L2](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L2-L2)

---

## Architecture Quick Reference

The following diagram shows how the main deployment components relate to each other and the key configuration files involved:

### Deployment Structure and Configuration Files

```

```

Sources: [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

 [next.config.mjs L1-L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L1-L49)

---

## Environment Configuration

The application requires several environment variables to function. Create a `.env` file in the project root with the following structure:

### Required Environment Variables

```

```

### Environment Variable Reference

| Variable | Purpose | Required | Example Value |
| --- | --- | --- | --- |
| `DATABASE_URL` | Neon PostgreSQL connection string | Yes | `postgresql://user:pass@host.neon.tech/db` |
| `NEXTAUTH_SECRET` | NextAuth.js session encryption key | Yes | Random 32+ character string |
| `NEXTAUTH_URL` | Public URL for OAuth callbacks | Yes | `https://yourdomain.com` |
| `JWT_SECRET` | JWT token signing key | Yes | Random 32+ character string |
| `NODE_ENV` | Application environment mode | Yes | `production` or `development` |

**Security Notes:**

* Generate secrets using: `openssl rand -base64 32`
* Never commit `.env` files to version control
* Use different secrets for development and production
* Ensure `DATABASE_URL` includes `?sslmode=require` for secure connections

Sources: [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

---

## Quick Start Guide

### Method 1: Docker Deployment (Recommended)

This method uses Docker Compose to build and run the application in a containerized environment.

#### Step 1: Clone and Configure

```

```

#### Step 2: Build and Launch

```

```

The application will be accessible at `http://localhost:3007`.

#### Step 3: Verify Deployment

```

```

### Container Build Flow

The following diagram illustrates the multi-stage Docker build process and how environment variables flow through the system:

```mermaid
flowchart TD

Start["docker-compose up --build"]
ReadCompose["Read docker-compose.yml"]
LoadEnv["Load .env file"]
Stage1["Stage 1: deps<br>COPY package.json<br>RUN npm ci"]
Stage2["Stage 2: builder<br>COPY node_modules<br>COPY source code"]
PrismaGen["RUN npx prisma generate<br>Generate Prisma Client"]
NextBuild["RUN npm run build<br>Build Next.js app"]
Stage3["Stage 3: runner<br>Create production user<br>COPY build artifacts"]
SetPerms["Set permissions<br>chown nextjs:nodejs"]
Container["sena-gestion-prod<br>Container created"]
InjectEnv["Inject environment variables<br>DATABASE_URL<br>NEXTAUTH_SECRET<br>NEXTAUTH_URL<br>JWT_SECRET"]
StartServer["CMD node server.js<br>Start Next.js server"]
ExposePort["EXPOSE 3000<br>Map to host 3007"]
JoinNetwork["Join sena-network"]
Running["Application running<br>Unsupported markdown: link"]

Start --> ReadCompose
ReadCompose --> LoadEnv
LoadEnv --> Stage1
Stage1 --> Stage2
Stage2 --> PrismaGen
PrismaGen --> NextBuild
NextBuild --> Stage3
Stage3 --> SetPerms
SetPerms --> Container
Container --> InjectEnv
InjectEnv --> StartServer
StartServer --> ExposePort
ExposePort --> JoinNetwork
JoinNetwork --> Running
```

Sources: [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

 [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

---

### Method 2: Local Development

For local development without Docker:

#### Step 1: Install Dependencies

```

```

#### Step 2: Database Setup

```

```

#### Step 3: Run Development Server

```

```

The application will be accessible at `http://localhost:3000`.

Sources: [Dockerfile L10-L11](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L10-L11)

 [Dockerfile L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L21-L21)

---

## Port and Network Configuration

### Port Mapping

The application uses the following port configuration:

| Location | Port | Purpose |
| --- | --- | --- |
| Host Machine | 3007 | External access point (mapped in docker-compose.yml) |
| Container Internal | 3000 | Next.js server listening port |
| Next.js Process | 3000 | Configured via `ENV PORT` in Dockerfile |

**Port Mapping Diagram:**

```mermaid
flowchart TD

Client["Web Browser<br>User"]
HostPort["Host Port 3007"]
ContainerPort["Container Port 3000"]
NextServer["Next.js Server<br>server.js"]
APIRoutes["/api/* endpoints"]
Pages["React pages"]

Client --> HostPort
HostPort --> ContainerPort
ContainerPort --> NextServer
NextServer --> APIRoutes
NextServer --> Pages
```

**Network Configuration:**

The Docker Compose configuration creates a dedicated bridge network named `sena-network` for service isolation. This is defined in [docker-compose.yml L22-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L22-L24)

Sources: [docker-compose.yml L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L12-L12)

 [Dockerfile L50-L53](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L50-L53)

---

## Next.js Configuration Overview

The application uses a custom Next.js configuration with production optimizations:

### Key Configuration Settings

| Setting | Value | Purpose |
| --- | --- | --- |
| `output` | `'standalone'` | Generates self-contained build for Docker |
| `outputFileTracingRoot` | `process.cwd()` | Sets root for file tracing |
| `eslint.ignoreDuringBuilds` | `true` | Skips linting during builds |
| `typescript.ignoreBuildErrors` | `true` | Allows builds with TypeScript warnings |
| `images.unoptimized` | `true` | Disables Next.js image optimization |

**Standalone Output Mode:**

The `output: 'standalone'` configuration is critical for Docker deployment. It generates a minimal, self-contained build in `.next/standalone/` that includes only necessary files, reducing image size and startup time.

This output is copied in the final Docker stage:

* [Dockerfile L45](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L45-L45) : Copies `.next/standalone` directory
* [Dockerfile L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L46-L46) : Copies `.next/static` assets

### Security Headers

The configuration applies security headers to all routes via the `async headers()` function:

```mermaid
flowchart TD

Request["HTTP Request<br>to any route"]
HeaderMiddleware["headers function<br>next.config.mjs"]
XFrame["X-Frame-Options: DENY<br>Prevents clickjacking"]
ContentType["X-Content-Type-Options: nosniff<br>Prevents MIME sniffing"]
Referrer["Referrer-Policy: origin-when-cross-origin<br>Controls referrer info"]
CORS1["Access-Control-Allow-Origin<br>CORS policy"]
CORS2["Access-Control-Allow-Methods<br>Allowed HTTP methods"]
CORS3["Access-Control-Allow-Headers<br>Allowed request headers"]
Response["HTTP Response<br>with security headers"]

Request --> HeaderMiddleware
HeaderMiddleware --> XFrame
HeaderMiddleware --> ContentType
HeaderMiddleware --> Referrer
HeaderMiddleware --> CORS1
HeaderMiddleware --> CORS2
HeaderMiddleware --> CORS3
XFrame --> Response
ContentType --> Response
Referrer --> Response
CORS1 --> Response
CORS2 --> Response
CORS3 --> Response
```

**CORS Configuration:**

The application restricts cross-origin requests to `https://gestioncomplementarias.axchisan.com` as defined in [next.config.mjs L32-L42](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L32-L42)

 This should be updated to match your production domain.

Sources: [next.config.mjs L1-L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L1-L49)

---

## Verification Steps

After starting the application, verify the deployment using these checks:

### 1. Container Health Check

```

```

### 2. Network Connectivity

```

```

### 3. Application Accessibility

```

```

### 4. Database Connection

```

```

### 5. API Endpoints

Test key API endpoints:

```

```

Sources: [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

---

## Common Issues and Troubleshooting

### Issue 1: Port Already in Use

**Symptom:** Error message `bind: address already in use`

**Solution:** Change the host port in [docker-compose.yml L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L12-L12)

:

```

```

### Issue 2: Database Connection Failure

**Symptom:** Error `Can't reach database server`

**Causes:**

1. Invalid `DATABASE_URL` in `.env`
2. Database server not accessible
3. SSL mode misconfiguration

**Solution:** Verify connection string format:

```

```

### Issue 3: Prisma Client Not Generated

**Symptom:** Error `Cannot find module '@prisma/client'`

**Solution:** The Dockerfile generates Prisma Client during build at [Dockerfile L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L21-L21)

 If building locally, run:

```

```

### Issue 4: Permission Denied Errors

**Symptom:** `EACCES: permission denied` during build

**Solution:** The Dockerfile creates a non-root user `nextjs` at [Dockerfile L35-L36](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L35-L36)

 and sets permissions at [Dockerfile L42](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L42-L42)

 Ensure Docker has proper permissions on your host system.

Sources: [docker-compose.yml L10-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L10-L18)

 [Dockerfile L21](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L21-L21)

 [Dockerfile L35-L42](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L35-L42)

---

## Next Steps

After successfully deploying the application:

1. **Configure Database Schema** - See [Database Setup](/axchisan/gestionComplementarias/2.3-database-setup) for running migrations and understanding the data model
2. **Review Environment Variables** - See [Configuration](/axchisan/gestionComplementarias/2.2-configuration) for complete environment variable documentation
3. **Create Initial Users** - Use the registration endpoint or database seeding to create the first admin user
4. **Understand User Roles** - See [User Roles Overview](/axchisan/gestionComplementarias/4.1-user-roles-overview) for RBAC implementation details
5. **Explore API Endpoints** - See [Backend API](/axchisan/gestionComplementarias/6-backend-api) for complete API documentation

For production deployment with SSL, monitoring, and scaling considerations, see [Production Deployment Guide](/axchisan/gestionComplementarias/7.2-production-deployment-guide).

Sources: [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)
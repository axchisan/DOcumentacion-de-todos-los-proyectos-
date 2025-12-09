# Configuration

> **Relevant source files**
> * [docker-compose.yml](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml)
> * [next.config.mjs](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs)

## Purpose and Scope

This document provides detailed information about configuring the SENA Gestión Complementarias application. It covers environment variables, Next.js application settings, security headers, and Docker-based configuration. For deployment procedures using these configurations, see [Installation and Deployment](/axchisan/gestionComplementarias/2.1-installation-and-deployment). For database-specific configuration details, refer to [Database Setup](/axchisan/gestionComplementarias/2.3-database-setup).

## Configuration Overview

The application's configuration is managed through two primary files:

| Configuration File | Purpose | Key Responsibilities |
| --- | --- | --- |
| `docker-compose.yml` | Container orchestration | Environment variables, port mapping, network configuration |
| `next.config.mjs` | Next.js application settings | Build output, security headers, CORS, image optimization |

The configuration follows a layered approach where environment-specific settings are injected at runtime through environment variables, while application-level settings are statically defined in the Next.js configuration.

## Environment Variables

### Required Environment Variables

The application requires four critical environment variables that must be set before deployment. These variables are referenced in [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

 and passed to the containerized application:

```

```

**Sources:** [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

### Environment Variable Reference

| Variable | Purpose | Format | Usage Context |
| --- | --- | --- | --- |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:password@host:port/database?sslmode=require` | Prisma ORM database connection |
| `NEXTAUTH_SECRET` | NextAuth.js session encryption | Random 32+ character string | Session token encryption and validation |
| `NEXTAUTH_URL` | Application base URL | `https://gestioncomplementarias.axchisan.com` | OAuth callback URL and canonical app URL |
| `JWT_SECRET` | Custom JWT signing key | Random 32+ character string | Custom JWT token generation for API authentication |

**Sources:** [docker-compose.yml L15-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L15-L18)

### Environment Variable Configuration

The environment variables are configured in [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

 using variable substitution syntax:

```yaml
environment:
  - NODE_ENV=production
  - DATABASE_URL=${DATABASE_URL}
  - NEXTAUTH_SECRET=${NEXTAUTH_SECRET}
  - NEXTAUTH_URL=${NEXTAUTH_URL}
  - JWT_SECRET=${JWT_SECRET}
```

This syntax reads values from the host system's environment or a `.env` file in the project root. The `.env` file should never be committed to version control and must contain:

```
DATABASE_URL=postgresql://...
NEXTAUTH_SECRET=your-secret-here
NEXTAUTH_URL=https://your-domain.com
JWT_SECRET=your-jwt-secret-here
```

**Sources:** [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

## Next.js Configuration

### Configuration File Structure

The Next.js configuration is defined in [next.config.mjs L1-L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L1-L49)

 as an ES module. The configuration object contains build settings, security headers, and optimization flags.

```mermaid
flowchart TD

NextConfig["next.config.mjs<br>Configuration Object"]
Output["output: 'standalone'<br>Self-contained build"]
OutputTrace["outputFileTracingRoot<br>Dependency tracking"]
Images["images.unoptimized: true<br>Disable optimization"]
ESLint["eslint.ignoreDuringBuilds<br>Skip linting"]
TypeScript["typescript.ignoreBuildErrors<br>Skip type checking"]
Headers["headers() Function<br>Security & CORS headers"]

NextConfig --> Output
NextConfig --> OutputTrace
NextConfig --> Images
NextConfig --> ESLint
NextConfig --> TypeScript
NextConfig --> Headers

subgraph subGraph2 ["Security Configuration"]
    Headers
end

subgraph subGraph1 ["Build Bypasses"]
    ESLint
    TypeScript
end

subgraph subGraph0 ["Build Settings"]
    Output
    OutputTrace
    Images
end
```

**Sources:** [next.config.mjs L1-L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L1-L49)

### Standalone Output Mode

The configuration sets `output: 'standalone'` in [next.config.mjs L3](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L3-L3)

 which enables Next.js to create a self-contained build that includes all necessary dependencies. This is critical for Docker deployment as it:

* Bundles all required Node.js modules
* Excludes development dependencies
* Creates a minimal production runtime
* Enables the `.next/standalone` directory structure used in the Dockerfile

The `outputFileTracingRoot` setting in [next.config.mjs L4](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L4-L4)

 ensures proper dependency resolution during the build process.

**Sources:** [next.config.mjs L3-L4](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L3-L4)

### Build Optimization Settings

The configuration includes several build-time optimizations and bypasses:

| Setting | Location | Purpose |
| --- | --- | --- |
| `eslint.ignoreDuringBuilds: true` | [next.config.mjs L6](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L6-L6) | Skips ESLint validation during production builds to speed up deployment |
| `typescript.ignoreBuildErrors: true` | [next.config.mjs L9](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L9-L9) | Allows builds to complete even with TypeScript errors |
| `images.unoptimized: true` | [next.config.mjs L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L12-L12) | Disables Next.js image optimization for simpler deployment |

These settings prioritize deployment speed and container compatibility over strict validation. For development, these checks should be run manually before committing code.

**Sources:** [next.config.mjs L5-L13](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L5-L13)

## Security Headers

### Security Header Configuration

The application implements security headers through Next.js's `headers()` async function defined in [next.config.mjs L14-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L14-L46)

 These headers are applied to all routes via the wildcard source pattern `'/(.*)'`.

```mermaid
flowchart TD

Request["Incoming HTTP Request<br>Any route: /(.*)"]
NextMiddleware["Next.js Headers Middleware<br>headers() function"]
XFrame["X-Frame-Options: DENY<br>Prevents clickjacking"]
XContent["X-Content-Type-Options: nosniff<br>Prevents MIME sniffing"]
Referrer["Referrer-Policy: origin-when-cross-origin<br>Limits referrer information"]
CORSOrigin["Access-Control-Allow-Origin<br>gestioncomplementarias.axchisan.com"]
CORSMethods["Access-Control-Allow-Methods<br>GET, POST, PUT, DELETE, OPTIONS"]
CORSHeaders["Access-Control-Allow-Headers<br>Content-Type, Authorization"]
Response["HTTP Response with Headers"]

Request --> NextMiddleware
NextMiddleware --> XFrame
NextMiddleware --> XContent
NextMiddleware --> Referrer
NextMiddleware --> CORSOrigin
NextMiddleware --> CORSMethods
NextMiddleware --> CORSHeaders
XFrame --> Response
XContent --> Response
Referrer --> Response
CORSOrigin --> Response
CORSMethods --> Response
CORSHeaders --> Response

subgraph subGraph1 ["CORS Headers"]
    CORSOrigin
    CORSMethods
    CORSHeaders
end

subgraph subGraph0 ["Security Headers"]
    XFrame
    XContent
    Referrer
end
```

**Sources:** [next.config.mjs L14-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L14-L46)

### Security Header Definitions

| Header | Value | Security Purpose |
| --- | --- | --- |
| `X-Frame-Options` | `DENY` | Prevents the application from being embedded in `<iframe>`, `<frame>`, or `<object>` tags, protecting against clickjacking attacks |
| `X-Content-Type-Options` | `nosniff` | Prevents browsers from MIME-sniffing responses, forcing respect of declared `Content-Type` headers |
| `Referrer-Policy` | `origin-when-cross-origin` | Controls referrer information sent with requests - full URL for same-origin, origin only for cross-origin |

**Sources:** [next.config.mjs L18-L30](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L18-L30)

### CORS Configuration

Cross-Origin Resource Sharing (CORS) is configured to allow the production domain to access API endpoints:

| Header | Value | Purpose |
| --- | --- | --- |
| `Access-Control-Allow-Origin` | `https://gestioncomplementarias.axchisan.com` | Restricts API access to the production domain only |
| `Access-Control-Allow-Methods` | `GET, POST, PUT, DELETE, OPTIONS` | Defines allowed HTTP methods for cross-origin requests |
| `Access-Control-Allow-Headers` | `Content-Type, Authorization` | Permits `Content-Type` for JSON requests and `Authorization` for JWT tokens |

The CORS origin in [next.config.mjs L33](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L33-L33)

 must match the `NEXTAUTH_URL` environment variable to ensure proper authentication flow.

**Sources:** [next.config.mjs L32-L42](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L32-L42)

## Docker Environment Configuration

### Container Environment Management

The Docker Compose configuration in [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 orchestrates the application container with environment-specific settings:

```mermaid
flowchart TD

BuildContext["Build Context<br>Project Root Directory"]
Dockerfile["Dockerfile<br>Multi-stage build"]
BuildArgs["Build Args<br>NODE_ENV=production"]
ContainerName["Container Name<br>sena-gestion-prod"]
Ports["Port Mapping<br>3007:3000"]
EnvVars["Environment Variables<br>5 variables"]
Restart["Restart Policy<br>unless-stopped"]
Network["Network Assignment<br>sena-network"]
Container["Running Container<br>sena-gestion-prod"]

BuildContext --> Dockerfile
Dockerfile --> BuildArgs
BuildArgs --> Container
ContainerName --> Container
Ports --> Container
EnvVars --> Container
Restart --> Container
Network --> Container

subgraph subGraph0 ["docker-compose.yml Configuration"]
    BuildArgs
    ContainerName
    Ports
    EnvVars
    Restart
    Network
end
```

**Sources:** [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

### Port Mapping

The container exposes port 3000 internally, which is mapped to port 3007 on the host system via [docker-compose.yml L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L12-L12)

:

```yaml
ports:
  - "3007:3000"
```

This configuration means:

* The Next.js application listens on port 3000 inside the container
* External clients access the application on port 3007
* The `NEXTAUTH_URL` environment variable must reflect the external port

**Sources:** [docker-compose.yml L11-L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L11-L12)

### Restart Policy

The container is configured with `restart: unless-stopped` in [docker-compose.yml L19](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L19-L19)

 which ensures:

* Automatic restart on failure
* Restart on system reboot
* Manual stop requires explicit `docker-compose down` command
* Persistent availability for production deployment

**Sources:** [docker-compose.yml L19](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L19-L19)

### Network Configuration

The application uses a custom bridge network named `sena-network` defined in [docker-compose.yml L22-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L22-L24)

:

```yaml
networks:
  sena-network:
    driver: bridge
```

This network provides:

* Isolated network namespace for the container
* DNS resolution by container name
* Foundation for multi-container architecture if additional services are added
* Network-level security separation from host and other Docker networks

**Sources:** [docker-compose.yml L20-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L20-L24)

## Configuration Deployment Flow

The following diagram illustrates how configuration flows from source files through the build process to the running application:

```mermaid
sequenceDiagram
  participant Developer
  participant .env File
  participant docker-compose.yml
  participant Docker Engine
  participant Build Process
  participant next.config.mjs
  participant Container Runtime
  participant Next.js Application

  Developer->>.env File: "Create .env with secrets"
  Developer->>docker-compose.yml: "Run docker-compose up --build"
  docker-compose.yml->>.env File: "Read environment variables"
  docker-compose.yml->>Docker Engine: "Start build with build args"
  Docker Engine->>Build Process: "Execute Dockerfile stages"
  Build Process->>next.config.mjs: "Read configuration"
  next.config.mjs->>Build Process: "Apply build settings:
  Build Process->>Docker Engine: standalone output, skip linting"
  Docker Engine->>Container Runtime: "Create image with standalone build"
  docker-compose.yml->>Container Runtime: "Start container with env vars"
  Container Runtime->>Next.js Application: "Inject environment variables:
  Next.js Application->>Next.js Application: DATABASE_URL, NEXTAUTH_SECRET, etc."
  Next.js Application->>Next.js Application: "Launch Next.js process"
  Next.js Application->>Next.js Application: "Read process.env variables"
  Next.js Application->>Next.js Application: "Apply headers() configuration"
  note over Next.js Application: "Application ready on port 3000
```

**Sources:** [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 [next.config.mjs L1-L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L1-L49)

## Configuration Validation

To verify the configuration is correctly applied, check the following:

| Validation Point | Command | Expected Result |
| --- | --- | --- |
| Container running | `docker ps` | Container `sena-gestion-prod` with status "Up" |
| Port accessibility | `curl http://localhost:3007` | HTTP 200 response or redirect to login |
| Environment variables | `docker exec sena-gestion-prod env` | All 5 environment variables present |
| Security headers | `curl -I https://gestioncomplementarias.axchisan.com` | Headers `X-Frame-Options`, `X-Content-Type-Options` present |
| Database connection | Check application logs | No Prisma connection errors |

For troubleshooting configuration issues, refer to [Installation and Deployment](/axchisan/gestionComplementarias/2.1-installation-and-deployment) for container management and [Database Setup](/axchisan/gestionComplementarias/2.3-database-setup) for connection string format.
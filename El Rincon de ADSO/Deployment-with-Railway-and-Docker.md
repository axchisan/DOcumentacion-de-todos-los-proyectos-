# Deployment with Railway and Docker

> **Relevant source files**
> * [Dockerfile](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile)
> * [docker-compose.yml](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml)
> * [index.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php)
> * [railway.json](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json)

## Purpose and Scope

This document describes the deployment infrastructure for El Rincón de ADSO, covering both cloud deployment via Railway and local development using Docker containers. The system supports two deployment modes: Railway's cloud platform using Nixpacks for production, and Docker Compose for local development environments.

For database connection configuration details, see [Database Configuration](/axchisan/El-rincon-de-ADSO/2.1-database-configuration). For general setup instructions, see [Getting Started](/axchisan/El-rincon-de-ADSO/2-getting-started).

## Deployment Architecture Overview

El Rincón de ADSO employs a dual-deployment strategy with separate configurations for production and development environments.

```mermaid
flowchart TD

Railway["Railway Platform"]
RailwayConfig["railway.json"]
Nixpacks["Nixpacks Builder"]
PHPBuiltin["PHP Built-in Server<br>Port: $PORT"]
DockerCompose["docker-compose.yml"]
Dockerfile["Dockerfile"]
ApacheContainer["Apache Container<br>el_rincon_adso_app"]
LocalPort["Port 89 → 80"]
AppCode["Application Code<br>/var/www/html"]
Uploads["uploads_volume<br>Persistent Storage"]
EnvVars["Environment Variables<br>PGHOST, PGPORT, etc."]

PHPBuiltin --> AppCode
ApacheContainer --> AppCode
PHPBuiltin --> EnvVars
ApacheContainer --> EnvVars
ApacheContainer --> Uploads

subgraph subGraph2 ["Common Components"]
    AppCode
    Uploads
    EnvVars
end

subgraph subGraph1 ["Local Development"]
    DockerCompose
    Dockerfile
    ApacheContainer
    LocalPort
    DockerCompose --> Dockerfile
    Dockerfile --> ApacheContainer
    ApacheContainer --> LocalPort
end

subgraph subGraph0 ["Production Deployment"]
    Railway
    RailwayConfig
    Nixpacks
    PHPBuiltin
    Railway --> RailwayConfig
    RailwayConfig --> Nixpacks
    Nixpacks --> PHPBuiltin
end
```

**Sources:** [railway.json L1-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L1-L20)

 [docker-compose.yml L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L1-L28)

 [Dockerfile L1-L18](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L1-L18)

## Railway Deployment Configuration

Railway deployment is configured through `railway.json`, which defines the build process and runtime behavior using Nixpacks.

### Build Configuration

The `railway.json` file specifies the Nixpacks builder with custom phases:

```mermaid
flowchart TD

Schema["$schema<br>railway.app/railway.schema.json"]
Build["build.builder<br>NIXPACKS"]
Setup["phases.setup<br>nixPkgs"]
PHP["php"]
PgSQL["php83Extensions.pgsql"]
Start["start.cmd<br>php -S 0.0.0.0:$PORT"]

Schema --> Build
Build --> Setup
Setup --> PHP
Setup --> PgSQL
Build --> Start
```

| Configuration Key | Value | Purpose |
| --- | --- | --- |
| `build.builder` | `NIXPACKS` | Uses Railway's Nixpacks builder system |
| `phases.setup.nixPkgs` | `["php", "php83Extensions.pgsql"]` | Installs PHP and PostgreSQL extension |
| `start.cmd` | `php -S 0.0.0.0:$PORT` | Starts PHP built-in server on Railway's dynamic port |
| `deploy.startCommand` | `php -S 0.0.0.0:$PORT` | Redundant start command for deployment |
| `deploy.restartPolicy` | `ON_FAILURE` | Automatically restarts service on failure |

**Sources:** [railway.json L1-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L1-L20)

### Runtime Behavior

Railway assigns a dynamic `$PORT` environment variable at runtime. The application listens on all interfaces (`0.0.0.0`) to accept incoming traffic routed by Railway's proxy layer.

**Sources:** [railway.json L12-L13](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L12-L13)

 [railway.json L17](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L17-L17)

## Docker Containerization

The Docker setup provides a complete Apache/PHP environment for local development with PostgreSQL extensions.

### Dockerfile Structure

The `Dockerfile` builds a PHP 8.2-Apache image with PostgreSQL support:

```mermaid
flowchart TD

Base["FROM php:8.2-apache"]
AptUpdate["apt-get update"]
InstallDeps["Install libpq-dev"]
ExtInstall["docker-php-ext-install<br>pgsql pdo_pgsql"]
CopyCode["COPY . /var/www/html"]
Chown["chown -R www-data:www-data"]
ServerName["ServerName localhost"]
ModRewrite["a2enmod rewrite"]

Base --> AptUpdate
AptUpdate --> InstallDeps
InstallDeps --> ExtInstall
ExtInstall --> CopyCode
CopyCode --> Chown
Chown --> ServerName
ServerName --> ModRewrite
```

| Dockerfile Instruction | Line | Purpose |
| --- | --- | --- |
| `FROM php:8.2-apache` | 1 | Base image with PHP 8.2 and Apache |
| `apt-get install libpq-dev` | 4-5 | PostgreSQL development libraries |
| `docker-php-ext-install pgsql pdo_pgsql` | 6 | Enable PostgreSQL PDO driver |
| `COPY . /var/www/html` | 9 | Copy application code into container |
| `chown -R www-data:www-data` | 12 | Set correct file ownership for Apache |
| `ServerName localhost` | 15 | Suppress Apache ServerName warning |
| `a2enmod rewrite` | 18 | Enable mod_rewrite for URL routing |

**Sources:** [Dockerfile L1-L18](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L1-L18)

## Docker Compose Configuration

The `docker-compose.yml` orchestrates the application container with volume persistence and environment variable injection.

### Service Definition

```mermaid
flowchart TD

ComposeFile["docker-compose.yml<br>version: 3.8"]
Service["services.web"]
Build["build: .<br>Uses Dockerfile"]
Container["container_name:<br>el_rincon_adso_app"]
Restart["restart: unless-stopped"]
Ports["ports:<br>89:80"]
Volumes["volumes"]
CodeVolume[".: /var/www/html"]
UploadVolume["uploads_volume:<br>/var/www/html/src/uploads"]
Env["environment"]
Command["command:<br>chown + apache2-foreground"]
Network["networks:<br>app_network"]

ComposeFile --> Service
Service --> Build
Service --> Container
Service --> Restart
Service --> Ports
Service --> Volumes
Service --> Env
Service --> Command
Service --> Network
Volumes --> CodeVolume
Volumes --> UploadVolume
```

**Sources:** [docker-compose.yml L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L1-L28)

### Service Configuration Table

| Property | Value | Purpose |
| --- | --- | --- |
| `build` | `.` | Build from Dockerfile in root directory |
| `container_name` | `el_rincon_adso_app` | Container identifier for management |
| `restart` | `unless-stopped` | Auto-restart unless manually stopped |
| `ports` | `"89:80"` | Map host port 89 to container port 80 |
| `volumes[0]` | `.:/var/www/html` | Mount codebase for live development |
| `volumes[1]` | `uploads_volume:/var/www/html/src/uploads` | Persistent named volume for uploads |
| `networks` | `app_network` | Custom bridge network for isolation |

**Sources:** [docker-compose.yml L4-L22](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L4-L22)

## Environment Variable Configuration

Both deployment methods require PostgreSQL connection parameters passed as environment variables.

### Required Environment Variables

```mermaid
flowchart TD

EnvSource[".env or Railway Variables"]
PGHOST["PGHOST"]
PGPORT["PGPORT"]
PGDATABASE["PGDATABASE"]
PGUSER["PGUSER"]
PGPASSWORD["PGPASSWORD"]
Container["Container Environment"]
Config["configuracion.php"]
PDO["PDO Connection"]

EnvSource --> PGHOST
EnvSource --> PGPORT
EnvSource --> PGDATABASE
EnvSource --> PGUSER
EnvSource --> PGPASSWORD
PGHOST --> Container
PGPORT --> Container
PGDATABASE --> Container
PGUSER --> Container
PGPASSWORD --> Container
Container --> Config
Config --> PDO
```

| Variable | Source | Usage |
| --- | --- | --- |
| `PGHOST` | Railway Variables / .env | PostgreSQL server hostname (Aiven) |
| `PGPORT` | Railway Variables / .env | PostgreSQL port (default: 5432) |
| `PGDATABASE` | Railway Variables / .env | Database name |
| `PGUSER` | Railway Variables / .env | Database username |
| `PGPASSWORD` | Railway Variables / .env | Database password |

These variables are read by `configuracion.php` to establish the database connection. See [Database Configuration](/axchisan/El-rincon-de-ADSO/2.1-database-configuration) for detailed connection logic.

**Sources:** [docker-compose.yml L13-L18](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L13-L18)

## Port Configuration and Traffic Flow

The system uses different port configurations for Railway vs Docker deployments.

### Port Mapping Architecture

```mermaid
flowchart TD

RailwayProxy["Railway Proxy<br>HTTPS 443"]
DynamicPort["PHP Server<br>0.0.0.0:$PORT"]
LocalHost["localhost:89"]
DockerBridge["Docker Bridge"]
ApachePort["Apache Container<br>Port 80"]
IndexRoot["index.php"]
Redirect["Location: /src/frontend/inicio/index.php"]

DynamicPort --> IndexRoot
ApachePort --> IndexRoot

subgraph subGraph2 ["Application Entry"]
    IndexRoot
    Redirect
    IndexRoot --> Redirect
end

subgraph subGraph1 ["Docker Local"]
    LocalHost
    DockerBridge
    ApachePort
    LocalHost --> DockerBridge
    DockerBridge --> ApachePort
end

subgraph subGraph0 ["Railway Production"]
    RailwayProxy
    DynamicPort
    RailwayProxy --> DynamicPort
end
```

### Railway Port Behavior

* Railway assigns `$PORT` dynamically (typically 8080 or similar)
* PHP built-in server binds to `0.0.0.0:$PORT` to accept all interfaces
* Railway's proxy terminates SSL and routes traffic to the container

**Sources:** [railway.json L12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L12-L12)

 [railway.json L17](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L17-L17)

### Docker Port Mapping

* Host port 89 maps to container port 80
* Apache listens on port 80 inside the container
* Access via `http://localhost:89` on the host machine

**Sources:** [docker-compose.yml L8-L9](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L8-L9)

### Application Entry Point

The root `index.php` redirects all traffic to the main application:

```yaml
Location: /src/frontend/inicio/index.php
```

This redirect works consistently in both deployment environments.

**Sources:** [index.php L1-L4](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L1-L4)

## Volume Persistence and File Storage

Docker Compose uses named volumes to persist uploaded files across container restarts.

### Volume Configuration

```mermaid
flowchart TD

HostFS["Host Filesystem<br>Project Directory"]
CodeMount["Volume Mount<br>.: /var/www/html"]
NamedVolume["Named Volume<br>uploads_volume"]
UploadMount["Volume Mount<br>/var/www/html/src/uploads"]
Container["Container<br>el_rincon_adso_app"]
AppCode["Application Code<br>(Live Reload)"]
UploadDir["Upload Directory<br>(Persistent)"]

HostFS --> CodeMount
CodeMount --> Container
Container --> AppCode
NamedVolume --> UploadMount
UploadMount --> Container
Container --> UploadDir
```

| Volume Type | Mount | Purpose |
| --- | --- | --- |
| Bind Mount | `.:/var/www/html` | Live code synchronization for development |
| Named Volume | `uploads_volume:/var/www/html/src/uploads` | Persistent storage for user-uploaded files |

The named volume `uploads_volume` ensures uploaded resources (profile images, documents, videos) persist even when the container is recreated.

**Sources:** [docker-compose.yml L10-L12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L10-L12)

 [docker-compose.yml L24-L25](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L24-L25)

### File Permission Management

The `docker-compose.yml` command ensures proper file ownership:

```
sh -c "chown -R www-data:www-data /var/www/html && apache2-foreground"
```

This command:

1. Changes ownership of all files to `www-data:www-data` (Apache user)
2. Starts Apache in foreground mode

**Sources:** [docker-compose.yml L19-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L19-L20)

 [Dockerfile L12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L12-L12)

## Network Configuration

Docker Compose creates an isolated network for the application.

```mermaid
flowchart TD

DefaultBridge["Default Docker Bridge<br>(Not Used)"]
AppNetwork["app_network<br>Custom Bridge Network"]
WebContainer["el_rincon_adso_app<br>Container"]
HostPort89["Host Port 89"]
ContainerPort80["Container Port 80"]

AppNetwork --> WebContainer
WebContainer --> ContainerPort80
HostPort89 --> ContainerPort80
```

| Network Property | Value | Purpose |
| --- | --- | --- |
| Network Name | `app_network` | Custom bridge network for service isolation |
| Network Driver | bridge (default) | Standard Docker bridge networking |
| Connected Services | `web` | Only the web service is connected |

The custom network allows for future expansion (e.g., adding Redis, worker containers) while maintaining network isolation from other Docker containers.

**Sources:** [docker-compose.yml L21-L22](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L21-L22)

 [docker-compose.yml L27-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L27-L28)

## Deployment Workflow

### Local Docker Development

```mermaid
flowchart TD

Start["Developer Machine"]
Clone["Clone Repository"]
EnvFile["Create .env with<br>Database Credentials"]
BuildCmd["docker-compose build"]
UpCmd["docker-compose up -d"]
Running["Container Running<br>localhost:89"]
LogsCmd["docker-compose logs -f"]
StopCmd["docker-compose down"]

Start --> Clone
Clone --> EnvFile
EnvFile --> BuildCmd
BuildCmd --> UpCmd
UpCmd --> Running
Running --> LogsCmd
Running --> StopCmd
```

**Commands:**

1. Build the Docker image: `docker-compose build`
2. Start the container: `docker-compose up -d`
3. View logs: `docker-compose logs -f web`
4. Stop the container: `docker-compose down`
5. Rebuild and restart: `docker-compose up -d --build`

**Sources:** [docker-compose.yml L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L1-L28)

 [Dockerfile L1-L18](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L1-L18)

### Railway Production Deployment

```mermaid
flowchart TD

GitPush["git push to GitHub"]
RailwayDetect["Railway Detects Push"]
ReadConfig["Read railway.json"]
NixpacksBuild["Nixpacks Build Phase<br>Install PHP + pgsql"]
StartServer["Start PHP Server<br>php -S 0.0.0.0:$PORT"]
ProxyRoute["Railway Proxy Routes<br>HTTPS Traffic"]
Live["Production Live"]

GitPush --> RailwayDetect
RailwayDetect --> ReadConfig
ReadConfig --> NixpacksBuild
NixpacksBuild --> StartServer
StartServer --> ProxyRoute
ProxyRoute --> Live
```

**Workflow:**

1. Push code to GitHub repository
2. Railway automatically detects the push
3. Reads `railway.json` build configuration
4. Nixpacks installs PHP and PostgreSQL extension
5. Starts PHP built-in server with dynamic port
6. Railway proxy routes HTTPS traffic to the application

**Environment variables must be configured in Railway's dashboard before deployment.**

**Sources:** [railway.json L1-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L1-L20)

## Build Comparison: Railway vs Docker

| Aspect | Railway (Nixpacks) | Docker Compose |
| --- | --- | --- |
| Builder | Nixpacks with custom phases | Docker with Dockerfile |
| Base Image | Managed by Nixpacks | `php:8.2-apache` |
| Web Server | PHP built-in server | Apache 2.4 |
| Port | Dynamic `$PORT` variable | Fixed 89→80 mapping |
| Code Mount | Immutable build artifact | Live bind mount |
| Uploads Storage | Ephemeral (requires external storage) | Persistent named volume |
| Restart Policy | `ON_FAILURE` | `unless-stopped` |
| Environment | Railway Variables | `.env` file or shell export |
| Use Case | Production deployment | Local development |

**Sources:** [railway.json L1-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L1-L20)

 [docker-compose.yml L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L1-L28)

 [Dockerfile L1-L18](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L1-L18)

## Configuration File Reference

### railway.json Schema

```json
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "NIXPACKS",
    "nixpacksPlan": {
      "phases": {
        "setup": {
          "nixPkgs": ["php", "php83Extensions.pgsql"]
        }
      },
      "start": {
        "cmd": "php -S 0.0.0.0:$PORT"
      }
    }
  },
  "deploy": {
    "startCommand": "php -S 0.0.0.0:$PORT",
    "restartPolicy": "ON_FAILURE"
  }
}
```

**Sources:** [railway.json L1-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L1-L20)

### docker-compose.yml Key Sections

| Section | Lines | Purpose |
| --- | --- | --- |
| `version` | 1 | Compose file format version 3.8 |
| `services.web` | 4-22 | Main application service definition |
| `volumes` | 24-25 | Named volume declarations |
| `networks` | 27-28 | Custom network definition |

**Sources:** [docker-compose.yml L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L1-L28)

### Dockerfile Key Layers

| Layer | Lines | Purpose |
| --- | --- | --- |
| Base image | 1 | PHP 8.2 with Apache |
| System packages | 4-6 | PostgreSQL client libraries |
| Application code | 9 | Copy project files |
| Permissions | 12 | Set www-data ownership |
| Apache config | 15 | Configure ServerName |
| Module enable | 18 | Enable mod_rewrite |

**Sources:** [Dockerfile L1-L18](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L1-L18)

## Troubleshooting

### Common Issues

| Issue | Cause | Solution |
| --- | --- | --- |
| "Connection refused" | Wrong port number | Verify port 89 is accessible |
| "Permission denied" on uploads | Incorrect file ownership | Run `docker-compose down && docker-compose up -d --build` |
| "Could not find driver" | Missing pgsql extension | Rebuild image: `docker-compose build --no-cache` |
| Railway deployment fails | Missing environment variables | Configure PGHOST, PGPORT, PGDATABASE, PGUSER, PGPASSWORD in Railway |
| Files not persisting | Volume not mounted | Verify `uploads_volume` is created: `docker volume ls` |

**Sources:** [Dockerfile L4-L6](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L4-L6)

 [docker-compose.yml L10-L12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L10-L12)

 [railway.json L8](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L8-L8)
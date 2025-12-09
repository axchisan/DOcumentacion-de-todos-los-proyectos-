# Getting Started

> **Relevant source files**
> * [Dockerfile](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile)
> * [docker-compose.yml](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml)
> * [index.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php)
> * [railway.json](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json)
> * [src/database/conexionDB.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php)
> * [src/database/configuracion.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php)

This page provides a step-by-step guide for setting up and running El Rincón de ADSO locally and deploying it to production. It covers prerequisites, environment configuration, local development using Docker, and deployment to Railway platform.

For detailed information about database schema and connection management, see [Database Configuration](/axchisan/El-rincon-de-ADSO/2.1-database-configuration). For production deployment specifics and container orchestration, see [Deployment with Railway and Docker](/axchisan/El-rincon-de-ADSO/2.2-deployment-with-railway-and-docker).

---

## Prerequisites

Before setting up the application, ensure you have the following installed on your development machine:

| Tool | Version | Purpose |
| --- | --- | --- |
| **PHP** | 8.2+ | Application runtime with PostgreSQL extensions |
| **PostgreSQL** | 14+ | Database server (or access to hosted instance) |
| **Docker** | 20.10+ | Container runtime (optional, but recommended) |
| **Docker Compose** | 1.29+ | Container orchestration (optional) |
| **Git** | 2.x | Version control |
| **Composer** | 2.x | PHP dependency management (if needed) |

**Required PHP Extensions:**

* `pgsql` - PostgreSQL native driver
* `pdo_pgsql` - PDO driver for PostgreSQL

Sources: [Dockerfile L1-L18](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L1-L18)

 [railway.json L8](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L8-L8)

---

## Environment Configuration

The application uses environment variables for database connectivity. These variables must be set before the application can connect to PostgreSQL.

### Required Environment Variables

| Variable | Description | Example |
| --- | --- | --- |
| `PGHOST` | PostgreSQL host address | `abc-xyz.a.aivencloud.com` |
| `PGPORT` | PostgreSQL port | `12345` |
| `PGDATABASE` | Database name | `defaultdb` |
| `PGUSER` | Database username | `avnadmin` |
| `PGPASSWORD` | Database password | `your_secure_password` |

The `configuracion.php` file reads these variables directly from the system environment using `getenv()`:

```
DB_HOST     = getenv('PGHOST')
DB_PORT     = getenv('PGPORT')
DB_NAME     = getenv('PGDATABASE')
DB_USER     = getenv('PGUSER')
DB_PASSWORD = getenv('PGPASSWORD')
```

### Environment Variable Flow

```

```

**Diagram: Environment variable flow from system environment through configuration layer to database connection**

Sources: [src/database/configuracion.php L1-L9](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L1-L9)

 [src/database/conexionDB.php L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L1-L28)

---

## Local Development Setup

### Option 1: Docker Compose (Recommended)

The recommended approach for local development uses Docker Compose to orchestrate the PHP application container.

#### Step 1: Create .env File

Create a `.env` file in the project root with your database credentials:

```

```

#### Step 2: Understanding the Docker Configuration

The `docker-compose.yml` defines a single service called `web`:

| Configuration | Value | Purpose |
| --- | --- | --- |
| **Image** | Built from `Dockerfile` | PHP 8.2 with Apache and PostgreSQL extensions |
| **Container Name** | `el_rincon_adso_app` | Identifier for the running container |
| **Port Mapping** | `89:80` | Host port 89 maps to container port 80 |
| **Volume Mount** | `.:/var/www/html` | Live code reloading during development |
| **Uploads Volume** | `uploads_volume:/var/www/html/src/uploads` | Persistent storage for user uploads |
| **Network** | `app_network` | Isolated Docker network |

#### Step 3: Build and Start Containers

```

```

The application will be available at `http://localhost:89`.

#### Step 4: Verify Container Status

```

```

**Docker Architecture Diagram:**

```

```

**Diagram: Docker container architecture showing port mappings, volume mounts, and external dependencies**

Sources: [docker-compose.yml L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L1-L28)

 [Dockerfile L1-L18](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L1-L18)

 [index.php L1-L5](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L1-L5)

---

### Option 2: Native PHP Development Server

For quick testing without Docker:

#### Step 1: Set Environment Variables

**Linux/Mac:**

```

```

**Windows (PowerShell):**

```

```

#### Step 2: Install PHP Extensions

Ensure PostgreSQL extensions are installed:

```

```

#### Step 3: Start PHP Built-in Server

```

```

The application will be available at `http://localhost:8000`.

Sources: [railway.json L12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L12-L12)

---

## Application Initialization Flow

Understanding the application entry point and initialization sequence:

```

```

**Diagram: Application initialization sequence from browser request to database connection establishment**

The root `index.php` immediately redirects to `src/frontend/inicio/index.php`. Any page requiring database access must include the connection file, which implements a singleton pattern in the `conexionDB` class. The `getConexion()` method ensures only one database connection exists throughout the application lifecycle.

Sources: [index.php L1-L5](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/index.php#L1-L5)

 [src/database/conexionDB.php L7-L26](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L7-L26)

 [src/database/configuracion.php L2-L6](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L2-L6)

---

## Verifying Database Connection

After starting the application, verify the database connection is working properly.

### Method 1: Check Application Logs

**Docker Compose:**

```

```

If you see "❌ Error de conexión:", the database credentials are incorrect or the PostgreSQL server is unreachable.

### Method 2: Database Connection Test

The `conexionDB::getConexion()` method includes error handling. On connection failure, it will output:

```
❌ Error de conexión: <PDOException message>
```

Common connection errors:

| Error Message | Cause | Solution |
| --- | --- | --- |
| `SQLSTATE[08006]` | Connection refused | Check `PGHOST` and `PGPORT` |
| `SQLSTATE[28000]` | Authentication failed | Verify `PGUSER` and `PGPASSWORD` |
| `SQLSTATE[3D000]` | Database does not exist | Check `PGDATABASE` value |
| `Connection timed out` | Network/firewall issue | Check firewall rules and network connectivity |

### Method 3: Manual Connection Test

Create a test file `test_connection.php` in the project root:

```

```

Run it:

```

```

Sources: [src/database/conexionDB.php L21-L23](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L21-L23)

---

## Deployment to Railway

The application is configured for seamless deployment to Railway platform using the Nixpacks builder.

### Railway Configuration Overview

The `railway.json` defines the build and deployment strategy:

```

```

**Diagram: Railway deployment configuration and build process using Nixpacks**

Key configuration points:

| Setting | Value | Purpose |
| --- | --- | --- |
| **Builder** | `NIXPACKS` | Automatic build detection and configuration |
| **Nix Packages** | `php`, `php83Extensions.pgsql` | PHP 8.3 runtime with PostgreSQL support |
| **Start Command** | `php -S 0.0.0.0:$PORT` | Built-in PHP server bound to Railway's dynamic port |
| **Restart Policy** | `ON_FAILURE` | Automatic restart if application crashes |

Sources: [railway.json L1-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L1-L20)

### Deployment Steps

#### Step 1: Install Railway CLI (Optional)

```

```

#### Step 2: Connect Repository to Railway

1. Log into [Railway dashboard](https://railway.app)
2. Click "New Project"
3. Select "Deploy from GitHub repo"
4. Authorize Railway to access your GitHub account
5. Select the `El-rincon-de-ADSO` repository

#### Step 3: Configure Environment Variables

In the Railway dashboard:

1. Navigate to your project
2. Click on the service
3. Go to "Variables" tab
4. Add the following variables:

```
PGHOST=<your-aiven-host>
PGPORT=<your-aiven-port>
PGDATABASE=<your-database-name>
PGUSER=<your-aiven-username>
PGPASSWORD=<your-aiven-password>
```

**Note:** Railway automatically injects these as environment variables, which are read by `configuracion.php` using `getenv()`.

#### Step 4: Deploy

Railway will automatically:

1. Detect the `railway.json` configuration
2. Install PHP and PostgreSQL extensions using Nixpacks
3. Start the application using the specified start command
4. Expose the application on a public URL

#### Step 5: Monitor Deployment

Watch the build logs in Railway dashboard:

* Build phase: Installing dependencies
* Start phase: Starting PHP server
* Health check: Application responding to requests

Sources: [railway.json L1-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L1-L20)

 [src/database/configuracion.php L2-L6](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L2-L6)

---

## Troubleshooting Common Issues

### Issue 1: Database Connection Fails

**Symptoms:** Application shows "❌ Error de conexión" message

**Diagnostic Steps:**

1. Verify environment variables are set correctly
2. Test PostgreSQL connectivity from host: ``` ```
3. Check firewall rules allow outbound connections to PostgreSQL port

**Solution:** Ensure all five environment variables (`PGHOST`, `PGPORT`, `PGDATABASE`, `PGUSER`, `PGPASSWORD`) are correctly configured.

Sources: [src/database/conexionDB.php L21-L23](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L21-L23)

### Issue 2: Permission Denied on Uploads Directory

**Symptoms:** File upload operations fail with permission errors

**Docker Compose Solution:**
The startup command includes `chown -R www-data:www-data /var/www/html` to fix permissions:

```

```

Sources: [docker-compose.yml L19-L20](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L19-L20)

 [Dockerfile L11-L12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/Dockerfile#L11-L12)

### Issue 3: Port Already in Use

**Symptoms:** `docker-compose up` fails with "port is already allocated"

**Solution:** Change the host port in `docker-compose.yml`:

```

```

Sources: [docker-compose.yml L8-L9](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L8-L9)

### Issue 4: SSL/TLS Connection Issues

**Symptoms:** PostgreSQL connection fails with SSL error

**Context:** The `configuracion.php` explicitly sets `DB_SSLMODE` to `"disable"` because Railway does not require explicit SSL configuration. The Aiven PostgreSQL connection is still secure, but SSL is handled transparently.

If you need to enable SSL explicitly:

1. Update `configuracion.php`: ``` ```
2. Modify `conexionDB.php` DSN construction to always include sslmode

Sources: [src/database/configuracion.php L7-L8](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/configuracion.php#L7-L8)

 [src/database/conexionDB.php L14-L17](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/database/conexionDB.php#L14-L17)

---

## Next Steps

After successfully setting up the application:

1. **Database Configuration:** Review [Database Configuration](/axchisan/El-rincon-de-ADSO/2.1-database-configuration) for detailed information about connection pooling, query optimization, and database schema initialization.
2. **Deployment Deep Dive:** See [Deployment with Railway and Docker](/axchisan/El-rincon-de-ADSO/2.2-deployment-with-railway-and-docker) for advanced deployment strategies, environment management, and container orchestration.
3. **Authentication Setup:** Navigate to [Authentication System](/axchisan/El-rincon-de-ADSO/3-authentication-system) to understand user login, session management, and security implementation.
4. **Development Workflow:** Consult [Development and Contributing](/axchisan/El-rincon-de-ADSO/12-development-and-contributing) for coding standards, git workflow, and contribution guidelines.

---

## Quick Reference Commands

| Task | Command |
| --- | --- |
| **Start Docker environment** | `docker-compose up -d` |
| **Stop Docker environment** | `docker-compose down` |
| **View logs** | `docker-compose logs -f web` |
| **Rebuild after changes** | `docker-compose up -d --build` |
| **Execute command in container** | `docker exec el_rincon_adso_app <command>` |
| **Start native PHP server** | `php -S 0.0.0.0:8000` |
| **Test database connection** | `php test_connection.php` |
| **Deploy to Railway** | Push to GitHub (auto-deploys if connected) |
| **Railway logs** | `railway logs` (requires CLI) |

Sources: [docker-compose.yml L1-L28](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/docker-compose.yml#L1-L28)

 [railway.json L12](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/railway.json#L12-L12)
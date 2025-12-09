# Environment Variables

> **Relevant source files**
> * [..bfg-report/2025-11-27/02-50-28/deleted-files.txt](https://github.com/axchisan/AxIA/blob/1fe26c44/..bfg-report/2025-11-27/02-50-28/deleted-files.txt)
> * [.gitignore](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore)

## Purpose and Scope

This document describes the production environment variable configuration for the AxIA system. It covers secrets management, required environment variables for each component (FastAPI backend, n8n workflow engine), and best practices for secure deployment.

For development environment setup and local configuration, see [Environment Setup](/axchisan/AxIA/2.2-environment-setup). For secure credential storage on the Flutter client, see [Secure Credential Storage](/axchisan/AxIA/4.2-secure-credential-storage). For Docker Compose deployment procedures, see [Backend Deployment](/axchisan/AxIA/12.1-backend-deployment).

---

## Environment Variable Architecture

The AxIA system requires environment variables at multiple layers to function in production. Each component must be configured independently with its own set of credentials and connection parameters.

### Component Configuration Map

```

```

**Sources:** [.gitignore L18](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L18-L18)

 [.gitignore L50](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L50-L50)

---

## Required Environment Variables by Component

### FastAPI Backend Variables

The FastAPI backend requires configuration for database connectivity, JWT authentication, and n8n integration. These variables are read at application startup and must be available in the container or server environment.

| Variable | Purpose | Example Format | Required |
| --- | --- | --- | --- |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:pass@host:5432/dbname` | Yes |
| `SECRET_KEY` | JWT token signing key | Random 32+ character string | Yes |
| `ALGORITHM` | JWT algorithm | `HS256` | Yes |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | Token lifetime | `1440` (24 hours) | Yes |
| `N8N_WEBHOOK_URL` | n8n webhook endpoint | `http://n8n:5678/webhook/axia` | Yes |

**Sources:** [.gitignore L50](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L50-L50)

### n8n Workflow Variables

The n8n workflow engine requires API credentials for external services used in AI processing. These are configured in the n8n interface or via environment variables.

| Variable | Purpose | Example Format | Required |
| --- | --- | --- | --- |
| `OPENAI_API_KEY` | OpenAI API access | `sk-...` | Yes |
| `ANTHROPIC_API_KEY` | Anthropic Claude API | `sk-ant-...` | Optional |
| `ELEVENLABS_API_KEY` | Text-to-Speech API | `...` | Yes (if audio enabled) |
| `WHISPER_API_KEY` | Speech-to-Text API | `...` | Yes (if audio enabled) |

**Sources:** Based on system architecture diagrams

### PostgreSQL Database Variables

The PostgreSQL database requires initialization credentials that match the `DATABASE_URL` connection string used by FastAPI.

| Variable | Purpose | Example Format | Required |
| --- | --- | --- | --- |
| `POSTGRES_USER` | Database username | `axia_user` | Yes |
| `POSTGRES_PASSWORD` | Database password | Strong random password | Yes |
| `POSTGRES_DB` | Database name | `axia_db` | Yes |

**Sources:** Based on standard PostgreSQL deployment patterns

---

## Security Migration from .env Files

The repository previously contained `.env` files that were removed from Git history using BFG Repo-Cleaner. This section documents the security remediation process.

### Historical .env File Removal

```mermaid
flowchart TD

ENV1[".env<br>395 bytes"]
ENV2[".env<br>287 bytes"]
ENV3[".env<br>667 bytes"]
BACKEND_ENV["backend/.env"]
BFG["BFG Repo-Cleaner<br>2025-11-27"]
GITIGNORE[".gitignore<br>Lines 18, 50"]
NO_ENV["No .env files<br>in repository"]

ENV1 --> BFG
ENV2 --> BFG
ENV3 --> BFG
BACKEND_ENV --> BFG
BFG --> GITIGNORE
BFG --> NO_ENV

subgraph subGraph2 ["After Cleanup"]
    GITIGNORE
    NO_ENV
end

subgraph subGraph1 ["BFG Process"]
    BFG
end

subgraph subGraph0 ["Before Cleanup"]
    ENV1
    ENV2
    ENV3
    BACKEND_ENV
end
```

The BFG cleanup removed three versions of `.env` files (395, 287, and 667 bytes) from the repository history. The `.gitignore` file now explicitly prevents future commits of environment files.

**Sources:** [..bfg-report/2025-11-27/02-50-28/deleted-files.txt L1-L3](https://github.com/axchisan/AxIA/blob/1fe26c44/..bfg-report/2025-11-27/02-50-28/deleted-files.txt#L1-L3)

 [.gitignore L18](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L18-L18)

 [.gitignore L50](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L50-L50)

### Gitignore Protection Patterns

The repository's `.gitignore` includes multiple patterns to prevent accidental commits of sensitive configuration:

```markdown
.env                  # Root-level environment file
backend/.env          # Backend-specific environment file
credentials.json      # Static auth credentials
token.json           # Dynamic JWT tokens
.venv                # Python virtual environment
venv/                # Alternative venv naming
env/                 # Alternative env naming
```

**Sources:** [.gitignore L15-L18](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L15-L18)

 [.gitignore L50-L52](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L50-L52)

---

## Production Configuration Strategy

### Environment Variable Injection Methods

```mermaid
flowchart TD

SECRETS["Secret Management<br>Vault/AWS Secrets Manager"]
CI_CD["CI/CD Pipeline<br>GitHub Actions/GitLab CI"]
DOCKER_ENV["Docker Compose<br>environment: sections"]
K8S_SECRETS["Kubernetes Secrets<br>ConfigMaps"]
CONTAINER["Container Runtime<br>FastAPI/n8n"]
APP["Application Code<br>os.getenv()"]

SECRETS --> CONTAINER
CI_CD --> CONTAINER
DOCKER_ENV --> CONTAINER
K8S_SECRETS --> CONTAINER

subgraph subGraph1 ["Runtime Environment"]
    CONTAINER
    APP
    CONTAINER --> APP
end

subgraph subGraph0 ["Configuration Sources"]
    SECRETS
    CI_CD
    DOCKER_ENV
    K8S_SECRETS
end
```

**Recommended Approach:** Use a secret management system (Vault, AWS Secrets Manager, etc.) in production. For development/staging, Docker Compose with `.env` files (not committed to Git) is acceptable.

**Sources:** [.gitignore L18](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L18-L18)

 [.gitignore L50](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L50-L50)

### Environment Variable Access Pattern

The FastAPI backend accesses environment variables using standard Python `os.environ` or `os.getenv()` calls. Configuration should be loaded at application startup, typically in the main application file or a dedicated configuration module.

**Common Access Pattern:**

```
DATABASE_URL = os.getenv("DATABASE_URL")
SECRET_KEY = os.getenv("SECRET_KEY")
N8N_WEBHOOK_URL = os.getenv("N8N_WEBHOOK_URL")
```

The application should validate that required environment variables are present and fail fast with descriptive errors if they are missing.

**Sources:** Based on FastAPI backend deployment patterns

---

## Secrets Management Best Practices

### Security Requirements

| Practice | Implementation | Rationale |
| --- | --- | --- |
| **No hardcoded secrets** | All credentials via environment variables | Prevents accidental Git commits |
| **Strong random keys** | `SECRET_KEY` minimum 32 characters | Ensures JWT signature security |
| **Separate environments** | Dev/staging/prod use different secrets | Limits blast radius of compromises |
| **Rotation schedule** | Rotate keys quarterly | Reduces window of vulnerability |
| **Least privilege** | Database user has minimal permissions | Limits damage from SQL injection |

### Environment-Specific Configuration

```mermaid
flowchart TD

PROD_SECRETS["Secret Manager<br>Production credentials"]
PROD_DB["Production Database<br>Real user data"]
STAGE_ENV["CI/CD injected<br>Staging secrets"]
STAGE_DB["Staging Database<br>Sanitized data"]
DEV_ENV["Local .env file<br>Not committed"]
DEV_DB["Local PostgreSQL<br>Test data"]

subgraph Production ["Production"]
    PROD_SECRETS
    PROD_DB
    PROD_SECRETS --> PROD_DB
end

subgraph Staging ["Staging"]
    STAGE_ENV
    STAGE_DB
    STAGE_ENV --> STAGE_DB
end

subgraph Development ["Development"]
    DEV_ENV
    DEV_DB
    DEV_ENV --> DEV_DB
end
```

Each environment should use completely separate credentials. Production secrets must never be used in development or staging environments.

**Sources:** Security best practices and [.gitignore L18](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L18-L18)

 [.gitignore L50](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L50-L50)

---

## Client-Side Configuration

### Flutter App Configuration

The Flutter mobile client does **not** use environment variables. Instead, it relies on:

1. **Static Configuration:** Hardcoded API endpoints (development/production builds)
2. **Secure Storage:** User credentials in `credentials.json` and JWT tokens in `token.json`, both stored via `flutter_secure_storage`

The client-side configuration is fundamentally different from backend configuration because:

* Mobile apps cannot securely store environment variables
* API endpoints are compiled into the app binary
* User-specific secrets (JWT tokens) are stored in platform keychains

For detailed information on client-side credential management, see [Secure Credential Storage](/axchisan/AxIA/4.2-secure-credential-storage).

**Sources:** [.gitignore L51-L52](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L51-L52)

---

## Configuration Validation Checklist

### Pre-Deployment Validation

Before deploying to production, verify that all required environment variables are configured:

**FastAPI Backend:**

* `DATABASE_URL` is set and connection is validated
* `SECRET_KEY` is at least 32 random characters
* `ALGORITHM` is set to `HS256` or stronger
* `ACCESS_TOKEN_EXPIRE_MINUTES` is configured (recommend 1440 for 24 hours)
* `N8N_WEBHOOK_URL` points to the correct n8n instance

**n8n Workflow:**

* `OPENAI_API_KEY` is set and has sufficient credits
* `ELEVENLABS_API_KEY` is set (if text-to-speech is enabled)
* `WHISPER_API_KEY` is set (if speech-to-text is enabled)
* Webhook endpoint is accessible from FastAPI backend

**PostgreSQL Database:**

* `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_DB` match `DATABASE_URL`
* Database user has appropriate permissions (CONNECT, SELECT, INSERT, UPDATE, DELETE)
* Database accepts connections from backend container/host

### Runtime Validation

The application should log (without exposing values) which environment variables are loaded at startup:

```yaml
INFO: Loaded DATABASE_URL: postgresql://***:***@postgres:5432/axia_db
INFO: Loaded SECRET_KEY: *** (length: 64)
INFO: Loaded N8N_WEBHOOK_URL: http://n8n:5678/webhook/axia
```

**Sources:** Standard deployment practices

---

## Environment Variable Reference Table

### Complete Variable Matrix

This table provides a comprehensive reference of all environment variables used across the AxIA system:

| Variable | Component | Type | Sensitive | Default | Notes |
| --- | --- | --- | --- | --- | --- |
| `DATABASE_URL` | FastAPI | String | Yes | None | PostgreSQL connection URI |
| `SECRET_KEY` | FastAPI | String | Yes | None | JWT signing key (32+ chars) |
| `ALGORITHM` | FastAPI | String | No | `HS256` | JWT algorithm |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | FastAPI | Integer | No | `1440` | Token expiration (24 hours) |
| `N8N_WEBHOOK_URL` | FastAPI | String | No | None | n8n webhook endpoint |
| `POSTGRES_USER` | PostgreSQL | String | No | None | Database username |
| `POSTGRES_PASSWORD` | PostgreSQL | String | Yes | None | Database password |
| `POSTGRES_DB` | PostgreSQL | String | No | None | Database name |
| `OPENAI_API_KEY` | n8n | String | Yes | None | OpenAI API credentials |
| `ANTHROPIC_API_KEY` | n8n | String | Yes | None | Anthropic Claude API (optional) |
| `ELEVENLABS_API_KEY` | n8n | String | Yes | None | Text-to-speech API |
| `WHISPER_API_KEY` | n8n | String | Yes | None | Speech-to-text API |

**Sources:** [.gitignore L18](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L18-L18)

 [.gitignore L50](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L50-L50)

 system architecture

---

## Migration Guide from .env Files

### Step-by-Step Migration Process

For existing deployments that may have used `.env` files (now removed from the repository):

1. **Extract Variables:** Review your existing `.env` file and identify all variables
2. **Choose Deployment Method:** Select appropriate secret management for your infrastructure
3. **Configure Secrets:** Add variables to your secret management system or Docker Compose
4. **Update Deployment:** Modify deployment scripts to inject environment variables
5. **Verify Configuration:** Test that all variables are accessible at runtime
6. **Remove .env Files:** Delete any local `.env` files and confirm `.gitignore` is updated
7. **Document Secrets:** Maintain a secure inventory of which secrets exist (not their values)

### Docker Compose Example

For Docker Compose deployments, create a `.env` file (not committed to Git) in the deployment directory:

```
DATABASE_URL=postgresql://axia_user:secure_password@postgres:5432/axia_db
SECRET_KEY=generate_random_32_plus_character_string_here
N8N_WEBHOOK_URL=http://n8n:5678/webhook/axia
```

Then reference it in `docker-compose.yml`:

```yaml
services:
  backend:
    environment:
      - DATABASE_URL=${DATABASE_URL}
      - SECRET_KEY=${SECRET_KEY}
      - N8N_WEBHOOK_URL=${N8N_WEBHOOK_URL}
```

**Important:** The `.env` file used for Docker Compose deployment should remain local and never be committed to Git, as enforced by [.gitignore L18](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L18-L18)

**Sources:** [.gitignore L18](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L18-L18)

 [.gitignore L50](https://github.com/axchisan/AxIA/blob/1fe26c44/.gitignore#L50-L50)

 [..bfg-report/2025-11-27/02-50-28/deleted-files.txt L1-L3](https://github.com/axchisan/AxIA/blob/1fe26c44/..bfg-report/2025-11-27/02-50-28/deleted-files.txt#L1-L3)

---

## Related Documentation

* **[Environment Setup](/axchisan/AxIA/2.2-environment-setup)** - Development environment configuration and local setup
* **[Secure Credential Storage](/axchisan/AxIA/4.2-secure-credential-storage)** - Flutter client credential management with flutter_secure_storage
* **[Backend Deployment](/axchisan/AxIA/12.1-backend-deployment)** - Complete Docker Compose deployment guide with database setup
* **[JWT Token Flow](/axchisan/AxIA/4.1-jwt-token-flow)** - Authentication system that uses SECRET_KEY for token signing
* **[BFG Repository Cleanup](/axchisan/AxIA/11.2-bfg-repository-cleanup)** - Security remediation process that removed .env files from history
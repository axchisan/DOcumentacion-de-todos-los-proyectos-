# Getting Started

> **Relevant source files**
> * [Dockerfile](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile)
> * [app/models/carrito.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py)
> * [app/routes/__pycache__/auth.cpython-313.pyc](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/__pycache__/auth.cpython-313.pyc)
> * [app/routes/auth.py](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py)

## Purpose and Scope

This document guides developers through the initial setup, Docker deployment, database initialization, and local execution of the Casa Bella application. By the end of this guide, you will have a running instance of the application with an initialized PostgreSQL database and access to the admin dashboard.

For information about the overall system architecture and blueprint organization, see [Architecture Overview](/GroveLive/CasaBella/3-architecture-overview). For details on authentication mechanics and role-based access control, see [Authentication & Authorization](/GroveLive/CasaBella/4-authentication-and-authorization).

---

## Prerequisites

Before beginning, ensure you have the following installed on your development machine:

| Requirement | Version | Purpose |
| --- | --- | --- |
| Docker | 20.0+ | Container runtime for application deployment |
| Docker Compose | 1.29+ | Multi-container orchestration |
| Git | Any | Repository cloning |
| PostgreSQL Client | 13+ | Optional: Direct database access for debugging |

---

## Installation Overview

The following diagram illustrates the complete setup workflow from repository clone to running application:

```mermaid
flowchart TD

A["Clone Repository"]
B["Install Dependencies"]
C["Configure Environment"]
D["Build Docker Container"]
E["Initialize Database"]
F["Run Application"]
G["Access on localhost:8085"]
H["Register First User"]
I["Auto-Assigned as admin"]
C1["Set DATABASE_URL"]
C2["Set SECRET_KEY"]
C3["Set FLASK_APP=run.py"]
E1["flask db init"]
E2["flask db migrate"]
E3["flask db upgrade"]
J1["Access /auth/admin_dashboard"]
J2["Access /admin/admin_dashboard"]

A --> B
B --> C
C --> D
D --> E
E --> F
F --> G
G --> H
H --> I
C --> C1
C --> C2
C --> C3
E --> E1
E --> E2
E --> E3
I --> J1
I --> J2
```

**Sources:** [Dockerfile L1-L21](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L1-L21)

 [app/routes/auth.py L32-L60](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L32-L60)

---

## Step 1: Repository Setup

Clone the Casa Bella repository and navigate to the project directory:

```
git clone https://github.com/GroveLive/CasaBella
cd CasaBella
```

The repository structure follows Flask's application factory pattern with modular blueprints:

```markdown
CasaBella/
├── app/
│   ├── __init__.py          # Application factory
│   ├── models/              # SQLAlchemy data models
│   ├── routes/              # Flask blueprints (auth, client, admin, employee)
│   ├── templates/           # Jinja2 HTML templates
│   └── static/              # CSS, JS, images
├── migrations/              # Alembic database migrations
├── Dockerfile               # Container definition
├── requirements.txt         # Python dependencies
└── run.py                   # Application entry point
```

**Sources:** [Dockerfile L5](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L5-L5)

---

## Step 2: Environment Configuration

Create a `.env` file in the project root with the required environment variables:

```markdown
# Database Configuration
DATABASE_URL=postgresql://username:password@host:5432/casabella

# Flask Configuration
SECRET_KEY=your-secret-key-here
FLASK_APP=run.py
FLASK_ENV=development

# Application Settings
FLASK_RUN_HOST=0.0.0.0
FLASK_RUN_PORT=8085
```

**Key Configuration Parameters:**

| Variable | Description | Example |
| --- | --- | --- |
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://user:pass@db:5432/casabella` |
| `SECRET_KEY` | Flask session encryption key | Generate with `python -c "import secrets; print(secrets.token_hex(32))"` |
| `FLASK_APP` | Application entry point | `run.py` |
| `FLASK_ENV` | Environment mode | `development` or `production` |

**Sources:** [Dockerfile L21](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L21-L21)

---

## Step 3: Docker Container Build

The application uses Python 3.13 Alpine as its base image for minimal footprint. Build and run the container:

```yaml
# Build the Docker image
docker build -t casabella:latest .

# Run the container with database connection
docker run -d \
  --name casabella-app \
  -p 8085:8085 \
  --env-file .env \
  casabella:latest
```

The Dockerfile performs the following operations:

```mermaid
sequenceDiagram
  participant Dockerfile
  participant Python:3.13-alpine
  participant /app Directory
  participant Flask Application

  Dockerfile->>Python:3.13-alpine: "FROM python:3.13-alpine"
  Dockerfile->>/app Directory: "WORKDIR /app"
  Dockerfile->>/app Directory: "COPY requirements.txt"
  Python:3.13-alpine->>/app Directory: "pip install -r requirements.txt"
  Dockerfile->>/app Directory: "COPY . ."
  Dockerfile->>/app Directory: "RUN mkdir -p migrations"
  Dockerfile->>Flask Application: "EXPOSE 8085"
  Dockerfile->>Flask Application: "CMD flask run --host=0.0.0.0 --port=8085"
```

**Sources:** [Dockerfile L1-L21](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L1-L21)

---

## Step 4: Database Initialization

Casa Bella uses Flask-Migrate (Alembic) for database schema management. Initialize the database with the following commands:

### Create Migration Repository

```markdown
# Enter the container
docker exec -it casabella-app sh

# Initialize Alembic (only needed first time)
flask db init
```

This creates the `migrations/` directory structure referenced in [Dockerfile L18](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L18-L18)

### Generate and Apply Migrations

```python
# Generate migration from models
flask db migrate -m "Initial schema"

# Apply migrations to database
flask db upgrade
```

The migration system creates tables for all SQLAlchemy models, including:

| Model | Table Name | Primary Key | Description |
| --- | --- | --- | --- |
| `Usuario` | `usuarios` | `id_usuario` | User accounts with role field |
| `Carrito` | `carrito` | `id_carrito` | Shopping cart state machine |
| `DetalleCarrito` | - | - | Cart item details |
| `Producto` | - | - | Product inventory |
| `Servicio` | - | - | Bookable services |
| `Venta` | - | - | Purchase transactions |
| `Cita` | - | - | Appointment bookings |

**Sources:** [Dockerfile L18](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L18-L18)

 [app/models/carrito.py L1-L13](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L1-L13)

---

## Step 5: First-Time User Registration

The authentication system implements a special first-user rule: the first registered user automatically receives the `admin` role. This is the critical bootstrapping mechanism for the application.

### First User Registration Flow

```mermaid
stateDiagram-v2
    [*] --> CheckDatabase : "POST /auth/register"
    CheckDatabase --> CountUsers : "Usuario.query.count()"
    CountUsers --> AssignAdmin : "count == 0"
    CountUsers --> AssignCliente : "count > 0"
    AssignAdmin --> HashPassword : "rol = 'admin'"
    AssignCliente --> HashPassword : "rol = 'cliente'"
    HashPassword --> CreateUser : "generate_password_hash()"
    CreateUser --> AddToSession : "db.session.add(user)"
    AddToSession --> Commit : "db.session.commit()"
    Commit --> RedirectLogin : "redirect(url_for('auth.login'))"
    RedirectLogin --> [*] : "redirect(url_for('auth.login'))"
```

**Sources:** [app/routes/auth.py L32-L60](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L32-L60)

### Registration Code Logic

The first-user check occurs at [app/routes/auth.py L45-L46](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L45-L46)

:

```
is_first_user = Usuario.query.count() == 0
rol = 'admin' if is_first_user else 'cliente'
```

User creation with role assignment occurs at [app/routes/auth.py L49](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L49-L49)

:

```
user = Usuario(nombre=nombre, email=email, contraseña=hashed_password, rol=rol, ...)
```

### Accessing the Application

1. Navigate to `http://localhost:8085`
2. Click **Register** to access `/auth/register`
3. Complete the registration form: * **Nombre** (name) * **Email** (unique identifier) * **Password** (hashed using `werkzeug.security`) * **Teléfono** (phone number) * **Especialidad** (optional, only relevant for employee role)
4. Submit the form → The system will: * Query `Usuario.query.count()` at [app/routes/auth.py L45](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L45-L45) * Assign `rol='admin'` if this is the first user * Hash the password using `generate_password_hash()` at [app/routes/auth.py L48](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L48-L48) * Insert into the `usuarios` table via `db.session.add(user)` at [app/routes/auth.py L52](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L52-L52)

**Sources:** [app/routes/auth.py L32-L60](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L32-L60)

---

## Step 6: Authentication and Dashboard Access

After registration, log in through `/auth/login` to access role-based dashboards.

### Login Flow and Role Routing

```mermaid
flowchart TD

A["POST /auth/login"]
B["Usuario.query.filter_by(email)"]
C["check_password_hash()"]
D["Password Valid?"]
E["flash('Credenciales incorrectas')"]
F["login_user(user)"]
G["user.rol == ?"]
H["redirect('auth.admin_dashboard')"]
I["redirect('auth.empleado_dashboard')"]
J["redirect('auth.cliente_dashboard')"]
K["/auth/admin_dashboard → /admin/admin_dashboard"]
L["/dashboard/empleado"]
M["/dashboard/cliente"]

A --> B
B --> C
C --> D
D --> E
D --> F
F --> G
G --> H
G --> I
G --> J
H --> K
I --> L
J --> M
```

**Sources:** [app/routes/auth.py L9-L30](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L9-L30)

### Role-Based Dashboard Routes

The login system at [app/routes/auth.py L21-L26](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L21-L26)

 redirects users based on their `rol` field:

| Role | Condition | Redirect Target | Endpoint |
| --- | --- | --- | --- |
| `admin` | `user.rol == 'admin'` | `auth.admin_dashboard` | `/auth/admin_dashboard` → `/admin/admin_dashboard` |
| `empleado` | `user.rol == 'empleado'` | `auth.empleado_dashboard` | `/dashboard/empleado` |
| `cliente` | Default | `auth.cliente_dashboard` | `/dashboard/cliente` |

**Sources:** [app/routes/auth.py L9-L30](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L9-L30)

 [app/routes/auth.py L62-L80](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L62-L80)

---

## Step 7: Verifying the Installation

### Check Application Health

1. **Verify Container is Running:** ``` docker ps | grep casabella-app ```
2. **Check Application Logs:** ``` docker logs -f casabella-app ``` Expected output: ``` * Running on http://0.0.0.0:8085 * Debugger is active! ```
3. **Test Database Connection:** ```javascript docker exec -it casabella-app flask shell >>> from app.models.users import Usuario >>> Usuario.query.count() 1  # Should show 1 if you've registered the admin ```

### Access the Admin Dashboard

After logging in as the first user (now admin), you should have access to:

* **User Management:** Create/edit/delete users, assign roles
* **Product Management:** Add inventory, manage stock
* **Service Management:** Define bookable services
* **Appointment Coordination:** Assign employees to client appointments
* **Promotion Management:** Create time-bound discounts

For detailed information on admin capabilities, see [Admin Features](/GroveLive/CasaBella/6-admin-features).

**Sources:** [app/routes/auth.py L69-L72](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L69-L72)

---

## Database Schema Initialization

The following diagram shows how SQLAlchemy models map to PostgreSQL tables during the `flask db upgrade` process:

```css
#mermaid-hv98dnb8whq{font-family:ui-sans-serif,-apple-system,system-ui,Segoe UI,Helvetica;font-size:16px;fill:#333;}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#mermaid-hv98dnb8whq .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#mermaid-hv98dnb8whq .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#mermaid-hv98dnb8whq .error-icon{fill:#dddddd;}#mermaid-hv98dnb8whq .error-text{fill:#222222;stroke:#222222;}#mermaid-hv98dnb8whq .edge-thickness-normal{stroke-width:1px;}#mermaid-hv98dnb8whq .edge-thickness-thick{stroke-width:3.5px;}#mermaid-hv98dnb8whq .edge-pattern-solid{stroke-dasharray:0;}#mermaid-hv98dnb8whq .edge-thickness-invisible{stroke-width:0;fill:none;}#mermaid-hv98dnb8whq .edge-pattern-dashed{stroke-dasharray:3;}#mermaid-hv98dnb8whq .edge-pattern-dotted{stroke-dasharray:2;}#mermaid-hv98dnb8whq .marker{fill:#999;stroke:#999;}#mermaid-hv98dnb8whq .marker.cross{stroke:#999;}#mermaid-hv98dnb8whq svg{font-family:ui-sans-serif,-apple-system,system-ui,Segoe UI,Helvetica;font-size:16px;}#mermaid-hv98dnb8whq p{margin:0;}#mermaid-hv98dnb8whq .entityBox{fill:#ffffff;stroke:#dddddd;}#mermaid-hv98dnb8whq .relationshipLabelBox{fill:#dddddd;opacity:0.7;background-color:#dddddd;}#mermaid-hv98dnb8whq .relationshipLabelBox rect{opacity:0.5;}#mermaid-hv98dnb8whq .labelBkg{background-color:rgba(221, 221, 221, 0.5);}#mermaid-hv98dnb8whq .edgeLabel .label{fill:#dddddd;font-size:14px;}#mermaid-hv98dnb8whq .label{font-family:ui-sans-serif,-apple-system,system-ui,Segoe UI,Helvetica;color:#333;}#mermaid-hv98dnb8whq .edge-pattern-dashed{stroke-dasharray:8,8;}#mermaid-hv98dnb8whq .node rect,#mermaid-hv98dnb8whq .node circle,#mermaid-hv98dnb8whq .node ellipse,#mermaid-hv98dnb8whq .node polygon{fill:#ffffff;stroke:#dddddd;stroke-width:1px;}#mermaid-hv98dnb8whq .relationshipLine{stroke:#999;stroke-width:1;fill:none;}#mermaid-hv98dnb8whq .marker{fill:none!important;stroke:#999!important;stroke-width:1;}#mermaid-hv98dnb8whq :root{--mermaid-font-family:"trebuchet ms",verdana,arial,sans-serif;}id_usuario FKid_usuario FKid_usuario FK (cliente)id_empleado FKid_carrito FKusuariosintid_usuarioPKstringnombrestringemailstringcontrasena_hashenumrolcliente|admin|empleadostringtelefonostringespecialidadcarritointid_carritoPKintid_usuarioFKdatetimefecha_creacionenumestadoactivo|completado|abandonadoventascitasdetalle_carrito
```

**Sources:** [app/models/carrito.py L4-L13](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L4-L13)

 [app/routes/auth.py L5](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L5-L5)

---

## Troubleshooting Common Issues

### Port 8085 Already in Use

```markdown
# Find process using port 8085
lsof -i :8085

# Kill the process or change the port in Dockerfile and .env
```

### Database Connection Refused

Verify the `DATABASE_URL` format in `.env`:

```yaml
postgresql://[user]:[password]@[host]:[port]/[database]
```

Ensure the PostgreSQL server is running and accessible from the Docker container.

### Migration Errors

If `flask db upgrade` fails:

```markdown
# Reset the database (CAUTION: Destroys all data)
flask db downgrade base
flask db upgrade
```

### First User Not Receiving Admin Role

The logic at [app/routes/auth.py L45](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L45-L45)

 checks if `Usuario.query.count() == 0`. If the database already contains users, new registrations will receive `rol='cliente'`. To manually promote a user to admin:

```javascript
docker exec -it casabella-app flask shell
>>> from app.models.users import Usuario
>>> from app import db
>>> user = Usuario.query.filter_by(email='your@email.com').first()
>>> user.rol = 'admin'
>>> db.session.commit()
```

**Sources:** [app/routes/auth.py L45-L46](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L45-L46)

---

## Next Steps

Now that your development environment is running:

1. **Explore the Application Structure:** See [Architecture Overview](/GroveLive/CasaBella/3-architecture-overview) for blueprint organization and routing patterns
2. **Add Test Data:** Create products, services, and additional users through the admin dashboard
3. **Review Data Models:** See [Data Models](/GroveLive/CasaBella/3.2-data-models) for detailed entity relationships
4. **Customize Frontend:** See [UI/UX Design System](/GroveLive/CasaBella/8-uiux-design-system) for theming and component customization

For development best practices and contributing guidelines, see [Development Guide](/GroveLive/CasaBella/9-development-guide).

**Sources:** [Dockerfile L1-L21](https://github.com/GroveLive/CasaBella/blob/5f618972/Dockerfile#L1-L21)

 [app/routes/auth.py L1-L87](https://github.com/GroveLive/CasaBella/blob/5f618972/app/routes/auth.py#L1-L87)

 [app/models/carrito.py L1-L13](https://github.com/GroveLive/CasaBella/blob/5f618972/app/models/carrito.py#L1-L13)
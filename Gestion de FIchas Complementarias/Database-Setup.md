# Database Setup

> **Relevant source files**
> * [package-lock.json](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package-lock.json)
> * [package.json](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json)
> * [prisma/schema.prisma](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma)

## Purpose and Scope

This document provides technical guidance for setting up and managing the PostgreSQL database for the SENA Gestión Complementarias application using Prisma ORM. It covers database connection configuration, schema structure, migration workflows, and operational commands. For information about the overall system architecture and deployment, see [System Architecture](/axchisan/gestionComplementarias/3-system-architecture). For details on authentication data structures, see [Authentication and Authorization](/axchisan/gestionComplementarias/3.4-authentication-and-authorization).

---

## Technology Stack

The application uses the following database technologies:

| Component | Technology | Purpose |
| --- | --- | --- |
| Database Engine | PostgreSQL | Relational database management system |
| Database Provider | Neon | Serverless PostgreSQL hosting |
| ORM | Prisma 5.22.0 | Type-safe database client and schema management |
| Connection Driver | `@neondatabase/serverless` | Neon-specific PostgreSQL driver for serverless environments |
| Schema Language | Prisma Schema Language | Declarative database modeling |

**Sources:** [prisma/schema.prisma L1-L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L1-L12)

 [package.json L72-L79](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L72-L79)

---

## Database Connection Configuration

### Environment Variables

Database connectivity is configured through the `DATABASE_URL` environment variable, which must be set before running the application or database commands:

```
DATABASE_URL="postgresql://user:password@host/database?sslmode=require"
```

The Prisma datasource reads this variable to establish connections:

[prisma/schema.prisma L9-L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L9-L12)

```
datasource db {
  provider = "postgresql" 
  url      = env("DATABASE_URL")  
}
```

### Connection Flow

```mermaid
flowchart TD

EnvFile[".env file<br>DATABASE_URL"]
PrismaSchema["prisma/schema.prisma<br>datasource db"]
PrismaClient["@prisma/client<br>PrismaClient instance"]
NeonDriver["@neondatabase/serverless<br>Connection Driver"]
NeonDB["Neon PostgreSQL<br>Production Database"]
PrismaCLI["Prisma CLI Commands<br>migrate, push, seed"]

EnvFile --> PrismaSchema
PrismaSchema --> PrismaClient
PrismaClient --> NeonDriver
NeonDriver --> NeonDB
PrismaCLI --> PrismaSchema
PrismaCLI --> NeonDB
```

**Sources:** [prisma/schema.prisma L9-L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L9-L12)

 [package.json L72-L79](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L72-L79)

---

## Prisma Client Configuration

The Prisma client generator is configured with multi-platform binary targets to support both local development and Docker deployment:

[prisma/schema.prisma L4-L7](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L4-L7)

```
generator client {
  provider      = "prisma-client-js"
  binaryTargets = ["native", "linux-musl-openssl-3.0.x"]
}
```

| Binary Target | Purpose |
| --- | --- |
| `native` | Local development on host OS (macOS, Windows, Linux) |
| `linux-musl-openssl-3.0.x` | Docker Alpine Linux container runtime |

The `linux-musl-openssl-3.0.x` target is required for the Alpine-based Docker image used in production deployment.

**Sources:** [prisma/schema.prisma L4-L7](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L4-L7)

---

## Database Schema Overview

### Core Models

The schema defines 7 primary models representing the application's data structure:

```mermaid
flowchart TD

Centro["Centro<br>@map('centros')<br>Training Centers"]
User["User<br>@map('users')<br>System Users"]
Programa["Programa<br>@map('programas')<br>Training Programs"]
ObjetivoAprendizaje["ObjetivoAprendizaje<br>@map('objetivos_aprendizaje')<br>Learning Objectives"]
Competencia["Competencia<br>@map('competencias')<br>Competencies"]
ResultadoAprendizaje["ResultadoAprendizaje<br>@map('resultados_aprendizaje')<br>Learning Outcomes"]
Solicitud["Solicitud<br>@map('solicitudes')<br>Training Requests"]
HorarioDetallado["HorarioDetallado<br>@map('horarios_detallados')<br>Schedule Details"]
Notificacion["Notificacion<br>@map('notificaciones')<br>User Notifications"]

User --> Solicitud
User --> Notificacion
Programa --> Solicitud
Programa --> ObjetivoAprendizaje
Programa --> Competencia
Programa --> ResultadoAprendizaje
Solicitud --> Notificacion

subgraph Communication ["Communication"]
    Notificacion
end

subgraph subGraph2 ["Request Management"]
    Solicitud
    HorarioDetallado
    Solicitud --> HorarioDetallado
end

subgraph subGraph1 ["Program Structure"]
    ObjetivoAprendizaje
    Competencia
    ResultadoAprendizaje
end

subgraph subGraph0 ["Organizational Hierarchy"]
    Centro
    User
    Programa
    Centro --> User
    Centro --> Programa
end
```

**Sources:** [prisma/schema.prisma L14-L230](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L230)

### Enumeration Types

The schema defines 5 enumeration types for type-safe categorization:

[prisma/schema.prisma L233-L281](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L233-L281)

| Enum | Values | Purpose |
| --- | --- | --- |
| `Role` | `INSTRUCTOR`, `COORDINADOR`, `ADMIN` | User role-based access control |
| `TipoFormacion` | `COMPLEMENTARIA`, `TRANSVERSAL`, `ESPECIFICA`, `VIRTUAL` | Training program classification |
| `Modalidad` | `PRESENCIAL`, `VIRTUAL`, `MIXTA`, `DESESCOLARIZADA`, `COMBINADA` | Training delivery method |
| `EstadoSolicitud` | `BORRADOR`, `PENDIENTE`, `EN_REVISION`, `APROBADA`, `RECHAZADA`, `CANCELADA` | Solicitud lifecycle states |
| `DiaSemana` | `LUNES`, `MARTES`, `MIERCOLES`, `JUEVES`, `VIERNES`, `SABADO`, `DOMINGO` | Schedule day representation |
| `TipoNotificacion` | `NUEVA_SOLICITUD`, `SOLICITUD_APROBADA`, `SOLICITUD_RECHAZADA`, `SOLICITUD_REVISION`, `ASIGNACION_FICHA`, `RECORDATORIO`, `SISTEMA` | Notification event types |

**Sources:** [prisma/schema.prisma L233-L281](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L233-L281)

---

## Entity Relationship Model

### Centro Model

The `Centro` model represents SENA training centers and serves as the root of the organizational hierarchy:

[prisma/schema.prisma L36-L53](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L36-L53)

**Key Fields:**

* `id`: CUID primary key
* `codigo`: Unique center identifier code
* `ciudad`, `region`: Geographic location
* `isActive`: Soft delete flag

**Relations:**

* `usuarios`: One-to-many with `User`
* `programas`: One-to-many with `Programa`

### User Model

The `User` model stores system user accounts with role-based access control:

[prisma/schema.prisma L14-L34](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L34)

**Key Fields:**

* `id`: CUID primary key
* `email`: Unique user email (authentication identifier)
* `cedula`: Unique Colombian national ID number
* `password`: Bcrypt-hashed password
* `role`: Enum (`INSTRUCTOR`, `COORDINADOR`, `ADMIN`)
* `centroId`: Foreign key to `Centro`
* `isActive`: Account status flag

**Relations:**

* `centro`: Many-to-one with `Centro`
* `solicitudes`: One-to-many with `Solicitud`
* `notificaciones`: One-to-many with `Notificacion`

### Programa Model

The `Programa` model defines training programs offered by centers:

[prisma/schema.prisma L55-L77](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L55-L77)

**Key Fields:**

* `codigo`: Unique program identifier
* `tipoFormacion`: Program type enum
* `modalidad`: Delivery modality enum
* `duracionHoras`: Total training hours
* `cupoAprendices`: Maximum student capacity

**Relations:**

* `centro`: Many-to-one with `Centro`
* `objetivos`: One-to-many with `ObjetivoAprendizaje`
* `competencias`: One-to-many with `Competencia`
* `resultados`: One-to-many with `ResultadoAprendizaje`
* `solicitudes`: One-to-many with `Solicitud`

### Solicitud Model

The `Solicitud` model represents training requests submitted by instructors:

[prisma/schema.prisma L109-L195](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L109-L195)

**Key Fields:**

* `codigo`: Unique auto-generated code (format: `SOL-YYYY-NNN`)
* `estado`: Lifecycle state enum
* `instructorId`: Foreign key to requesting instructor
* `programaId`: Foreign key to requested program
* `numeroFicha`: Unique ficha number (assigned on approval)
* `modalidad`: Training modality
* `justificacion`: Academic justification text
* `objetivosPersonalizados`: JSON array of custom objectives

**Program Flags (Boolean):**

* `programaEmprendimiento`, `programaBilinguismo`
* `atencionInstituciones`, `posconflicto`
* `senaEmprendeRural`, `formacionEspecialMypimes`
* `tecnoacademiaSennova`, `campesenaConvenio8842`
* Additional special program indicators

**Validation Fields:**

* `cumpleRequisitos`: Meets requirements flag
* `autorizaUsoInfo`: Information usage authorization
* `confirmaVeracidad`: Accuracy confirmation

**Relations:**

* `instructor`: Many-to-one with `User`
* `programa`: Many-to-one with `Programa`
* `horarios`: One-to-many with `HorarioDetallado`
* `notificaciones`: One-to-many with `Notificacion`

### HorarioDetallado Model

The `HorarioDetallado` model defines granular schedule details for solicitudes:

[prisma/schema.prisma L197-L211](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L197-L211)

**Key Fields:**

* `diaSemana`: Day of week enum
* `fecha`: Optional specific date
* `horaInicio`, `horaFin`: Time strings (format: `"HH:MM"`)
* `esFlexible`: Schedule flexibility flag

**Relations:**

* `solicitud`: Many-to-one with `Solicitud` (cascade delete)

### Notificacion Model

The `Notificacion` model tracks system notifications for users:

[prisma/schema.prisma L213-L230](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L213-L230)

**Key Fields:**

* `tipo`: Notification type enum
* `titulo`, `mensaje`: Notification content
* `leida`: Read status boolean
* `solicitudId`: Optional reference to related solicitud

**Relations:**

* `usuario`: Many-to-one with `User`
* `solicitud`: Optional many-to-one with `Solicitud`

**Sources:** [prisma/schema.prisma L14-L230](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L230)

---

## Database Schema Diagram

```css
#mermaid-7r0corcls4v{font-family:ui-sans-serif,-apple-system,system-ui,Segoe UI,Helvetica;font-size:16px;fill:#333;}@keyframes edge-animation-frame{from{stroke-dashoffset:0;}}@keyframes dash{to{stroke-dashoffset:0;}}#mermaid-7r0corcls4v .edge-animation-slow{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 50s linear infinite;stroke-linecap:round;}#mermaid-7r0corcls4v .edge-animation-fast{stroke-dasharray:9,5!important;stroke-dashoffset:900;animation:dash 20s linear infinite;stroke-linecap:round;}#mermaid-7r0corcls4v .error-icon{fill:#dddddd;}#mermaid-7r0corcls4v .error-text{fill:#222222;stroke:#222222;}#mermaid-7r0corcls4v .edge-thickness-normal{stroke-width:1px;}#mermaid-7r0corcls4v .edge-thickness-thick{stroke-width:3.5px;}#mermaid-7r0corcls4v .edge-pattern-solid{stroke-dasharray:0;}#mermaid-7r0corcls4v .edge-thickness-invisible{stroke-width:0;fill:none;}#mermaid-7r0corcls4v .edge-pattern-dashed{stroke-dasharray:3;}#mermaid-7r0corcls4v .edge-pattern-dotted{stroke-dasharray:2;}#mermaid-7r0corcls4v .marker{fill:#999;stroke:#999;}#mermaid-7r0corcls4v .marker.cross{stroke:#999;}#mermaid-7r0corcls4v svg{font-family:ui-sans-serif,-apple-system,system-ui,Segoe UI,Helvetica;font-size:16px;}#mermaid-7r0corcls4v p{margin:0;}#mermaid-7r0corcls4v .entityBox{fill:#ffffff;stroke:#dddddd;}#mermaid-7r0corcls4v .relationshipLabelBox{fill:#dddddd;opacity:0.7;background-color:#dddddd;}#mermaid-7r0corcls4v .relationshipLabelBox rect{opacity:0.5;}#mermaid-7r0corcls4v .labelBkg{background-color:rgba(221, 221, 221, 0.5);}#mermaid-7r0corcls4v .edgeLabel .label{fill:#dddddd;font-size:14px;}#mermaid-7r0corcls4v .label{font-family:ui-sans-serif,-apple-system,system-ui,Segoe UI,Helvetica;color:#333;}#mermaid-7r0corcls4v .edge-pattern-dashed{stroke-dasharray:8,8;}#mermaid-7r0corcls4v .node rect,#mermaid-7r0corcls4v .node circle,#mermaid-7r0corcls4v .node ellipse,#mermaid-7r0corcls4v .node polygon{fill:#ffffff;stroke:#dddddd;stroke-width:1px;}#mermaid-7r0corcls4v .relationshipLine{stroke:#999;stroke-width:1;fill:none;}#mermaid-7r0corcls4v .marker{fill:none!important;stroke:#999!important;stroke-width:1;}#mermaid-7r0corcls4v :root{--mermaid-font-family:"trebuchet ms",verdana,arial,sans-serif;}employsofferscreatesreceivesrequested_fordefinesincludesachieveshas_scheduletriggersCentrostringidPKCUIDstringcodigoUKUnique codestringnombreCenter namestringciudadCitystringregionRegionbooleanisActiveActive statusdatetimecreatedAtdatetimeupdatedAtUserstringidPKCUIDstringemailUKAuth emailstringcedulaUKNational IDstringpasswordBcrypt hashstringnameFull nameenumroleINSTRUCTOR|COORDINADOR|ADMINstringcentroIdFKAssigned centerbooleanisActivedatetimecreatedAtdatetimeupdatedAtProgramastringidPKCUIDstringcodigoUKProgram codestringnombreProgram nameenumtipoFormacionProgram typeenummodalidadDelivery modeintduracionHorasDuration hoursintcupoAprendicesMax capacitystringcentroIdFKbooleanisActivedatetimecreatedAtdatetimeupdatedAtSolicitudstringidPKCUIDstringcodigoUKSOL-YYYY-NNNenumestadoBORRADOR|PENDIENTE|EN_REVISION|APROBADA|RECHAZADAstringinstructorIdFKstringprogramaIdFKstringnumeroFichaUKGenerated on approvalstringmunicipioMunicipalitydatetimefechaInicioCursoStart datetextjustificacionAcademic justificationjsonobjetivosPersonalizadosCustom objectives arraydatetimefechaSolicituddatetimefechaAprobacionNotificacionstringidPKCUIDenumtipoNUEVA_SOLICITUD|APROBADA|...stringtituloNotification titlestringmensajeMessage contentbooleanleidaRead statusstringusuarioIdFKstringsolicitudIdFKOptionaldatetimefechaCreadaObjetivoAprendizajeCompetenciaResultadoAprendizajeHorarioDetalladostringidPKCUIDstringsolicitudIdFKCASCADE DELETEenumdiaSemanaLUNES|MARTES|...datetimefechaOptional datestringhoraInicioHH:MM formatstringhoraFinHH:MM formatbooleanesFlexibleSchedule flexibility
```

**Sources:** [prisma/schema.prisma L14-L230](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L230)

---

## Database Operations

### Available Commands

The application provides the following database management scripts via npm:

[package.json L10-L13](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L10-L13)

| Command | Prisma Command | Purpose |
| --- | --- | --- |
| `npm run db:generate` | `prisma generate` | Generate Prisma Client from schema |
| `npm run db:push` | `prisma db push` | Push schema changes without migrations (dev) |
| `npm run db:migrate` | `prisma migrate dev` | Create and apply migrations |
| `npm run db:seed` | `prisma db seed` | Run seed script to populate initial data |

### Database Workflow

```mermaid
flowchart TD

CloneRepo["Clone Repository"]
InstallDeps["npm install"]
SetEnv["Configure DATABASE_URL<br>in .env file"]
EditSchema["Edit prisma/schema.prisma"]
GenerateClient["npm run db:generate<br>prisma generate"]
Choice["Migration Strategy"]
DevPush["npm run db:push<br>prisma db push<br>(Development)"]
DevMigrate["npm run db:migrate<br>prisma migrate dev<br>(Production-ready)"]
SeedScript["npm run db:seed<br>prisma db seed"]
SeedFile["scripts/seed.ts<br>tsx execution"]
PopulateData["Create initial Centro,<br>User, Programa records"]
AppStart["npm run dev / npm start"]
PrismaClient["PrismaClient instance<br>Auto-generated from schema"]
DatabaseQueries["Type-safe database<br>queries in API routes"]

SetEnv --> EditSchema
DevPush --> SeedScript
DevMigrate --> SeedScript
PopulateData --> AppStart

subgraph Runtime ["Runtime"]
    AppStart
    PrismaClient
    DatabaseQueries
    AppStart --> PrismaClient
    PrismaClient --> DatabaseQueries
end

subgraph subGraph2 ["Data Population"]
    SeedScript
    SeedFile
    PopulateData
    SeedScript --> SeedFile
    SeedFile --> PopulateData
end

subgraph subGraph1 ["Schema Changes"]
    EditSchema
    GenerateClient
    Choice
    DevPush
    DevMigrate
    EditSchema --> GenerateClient
    GenerateClient --> Choice
    Choice --> DevPush
    Choice --> DevMigrate
end

subgraph subGraph0 ["Initial Setup"]
    CloneRepo
    InstallDeps
    SetEnv
    CloneRepo --> InstallDeps
    InstallDeps --> SetEnv
end
```

**Sources:** [package.json L10-L16](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L10-L16)

 [prisma/schema.prisma L1-L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L1-L12)

### Migration vs Push Workflow

**`prisma db push`** (Development):

* Directly synchronizes schema to database
* No migration files created
* Faster for rapid prototyping
* Data loss possible on breaking changes
* Not recommended for production

**`prisma migrate dev`** (Production):

* Creates versioned migration SQL files in `prisma/migrations/`
* Maintains migration history
* Allows rollback and review
* Safe for production deployment
* Enables team collaboration

### Seed Configuration

The seed script configuration is defined in `package.json`:

[package.json L15-L17](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L15-L17)

```
"prisma": {
  "seed": "tsx scripts/seed.ts"
}
```

This instructs Prisma to execute `scripts/seed.ts` using the `tsx` TypeScript executor when running `prisma db seed`. The seed script should populate initial data for:

* Default `Centro` records (training centers)
* Admin/coordinator `User` accounts
* Sample `Programa` records
* Reference data for enums

**Sources:** [package.json L10-L17](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L10-L17)

---

## Index and Constraint Strategy

### Unique Constraints

The schema enforces uniqueness on critical business identifiers:

| Model | Field | Constraint Purpose |
| --- | --- | --- |
| `Centro` | `codigo` | Unique center code identifier |
| `User` | `email` | Prevents duplicate accounts (auth) |
| `User` | `cedula` | One account per national ID |
| `Programa` | `codigo` | Unique program identifier |
| `Solicitud` | `codigo` | Auto-generated unique request code |
| `Solicitud` | `numeroFicha` | Unique ficha number (post-approval) |

These `@unique` directives create database-level unique indexes.

### Primary Keys

All models use CUID (Collision-resistant Unique Identifier) primary keys:

```
id String @id @default(cuid())
```

CUIDs provide:

* URL-safe string identifiers
* Timestamp-based sortability
* Low collision probability
* No sequential enumeration (security)

### Foreign Key Cascades

The `HorarioDetallado` model implements cascade deletion:

[prisma/schema.prisma L200](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L200-L200)

```
solicitud Solicitud @relation(fields: [solicitudId], references: [id], onDelete: Cascade)
```

When a `Solicitud` is deleted, all associated `HorarioDetallado` records are automatically removed.

**Sources:** [prisma/schema.prisma L14-L211](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L211)

---

## Table Mapping

Prisma models are mapped to database tables using the `@@map` directive:

| Model Name (Prisma) | Table Name (PostgreSQL) |
| --- | --- |
| `User` | `users` |
| `Centro` | `centros` |
| `Programa` | `programas` |
| `ObjetivoAprendizaje` | `objetivos_aprendizaje` |
| `Competencia` | `competencias` |
| `ResultadoAprendizaje` | `resultados_aprendizaje` |
| `Solicitud` | `solicitudes` |
| `HorarioDetallado` | `horarios_detallados` |
| `Notificacion` | `notificaciones` |

Example:
[prisma/schema.prisma L33](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L33-L33)

```
@@map("users")
```

This allows the codebase to use PascalCase model names while maintaining snake_case database conventions.

**Sources:** [prisma/schema.prisma L14-L230](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L230)

---

## Field-Level Details

### Auto-Generated Fields

Several fields use automatic value generation:

**Timestamps:**

```
createdAt DateTime @default(now())
updatedAt DateTime @updatedAt
```

* `createdAt`: Set once on record creation
* `updatedAt`: Automatically updated on every modification

**Default Values:**

```
role Role @default(INSTRUCTOR)
isActive Boolean @default(true)
versionPrograma String @default("1")
```

**Optional vs Required:**

Fields without `?` are required at creation:

```
email String        // Required
telefono String?    // Optional (nullable)
```

### JSON Field Type

The `Solicitud.objetivosPersonalizados` field uses JSON array storage:

[prisma/schema.prisma L165](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L165-L165)

```
objetivosPersonalizados String[]
```

Prisma's `String[]` type maps to PostgreSQL's `TEXT[]` array type, allowing storage of variable-length string arrays for custom learning objectives.

**Sources:** [prisma/schema.prisma L14-L195](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L14-L195)

---

## Type Safety and Client Generation

### Generated Prisma Client

Running `npm run db:generate` generates the `@prisma/client` package with:

1. **Type-safe model types:** ```javascript import { User, Solicitud, EstadoSolicitud } from '@prisma/client' ```
2. **Query builder with autocomplete:** ```javascript const solicitudes = await prisma.solicitud.findMany({   where: { estado: 'PENDIENTE' },   include: { instructor: true, programa: true } }) ```
3. **Enum type exports:** ```javascript import { Role, Modalidad } from '@prisma/client' ```

The generated client is located in `node_modules/@prisma/client` and should be regenerated after any schema changes.

### Multi-Platform Binary Support

The `binaryTargets` configuration ensures the generated client includes query engine binaries for:

[prisma/schema.prisma L6](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L6-L6)

```
binaryTargets = ["native", "linux-musl-openssl-3.0.x"]
```

* **`native`**: Matches the development machine OS
* **`linux-musl-openssl-3.0.x`**: Alpine Linux in Docker container

This dual-target approach enables:

* Local development on any OS (macOS, Windows, Linux)
* Deployment in Alpine-based Docker containers
* No runtime binary compatibility issues

**Sources:** [prisma/schema.prisma L4-L7](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L4-L7)

 [package.json L72](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L72-L72)

---

## Connection Pooling

The `@neondatabase/serverless` driver provides connection pooling optimized for serverless environments:

[package.json L76](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L76-L76)

```
"@neondatabase/serverless": "^0.10.1"
```

Key features:

* HTTP-based connection protocol
* Automatic connection pooling
* Low latency for cold starts
* Compatible with edge runtimes
* Reduced connection overhead vs. traditional TCP

This driver is specifically designed for Neon's serverless PostgreSQL architecture and is required for optimal performance.

**Sources:** [package.json L76](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L76-L76)

 [prisma/schema.prisma L10](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L10-L10)
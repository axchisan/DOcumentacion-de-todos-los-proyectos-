# System Architecture

> **Relevant source files**
> * [Dockerfile](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile)
> * [docker-compose.yml](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml)
> * [next.config.mjs](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs)
> * [package-lock.json](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package-lock.json)
> * [package.json](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json)
> * [prisma/schema.prisma](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma)

## Purpose and Scope

This document provides a technical overview of the SENA Gestión Complementarias system architecture, describing how the application is structured, deployed, and operates. It covers the overall system design, technology stack, layer organization, and key architectural patterns.

For detailed information on specific architectural aspects:

* **Deployment configuration and containerization**: See [Deployment Architecture](/axchisan/gestionComplementarias/3.1-deployment-architecture)
* **Framework and library details**: See [Technology Stack](/axchisan/gestionComplementarias/3.2-technology-stack)
* **Database schema and entity relationships**: See [Data Model](/axchisan/gestionComplementarias/3.3-data-model)
* **Security implementation details**: See [Authentication and Authorization](/axchisan/gestionComplementarias/3.4-authentication-and-authorization)

## Architectural Overview

SENA Gestión Complementarias is a monolithic web application built with Next.js 15.2.4 that manages training request workflows for SENA (Servicio Nacional de Aprendizaje) educational institutions. The system operates as a single Docker container that handles both frontend rendering and backend API operations.

**Architecture Type**: Monolithic full-stack application
**Deployment Model**: Single-container Docker deployment
**Runtime**: Node.js 20 Alpine
**Database**: Neon PostgreSQL (serverless)
**Port Configuration**: Container port 3000 → Host port 3007

The application follows a three-tier role-based access control model with distinct workflows for INSTRUCTOR, COORDINADOR, and ADMIN users, managing the complete lifecycle of training solicitudes from draft creation through approval or rejection.

**Sources**: [docker-compose.yml L1-L24](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L1-L24)

 [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

 [package.json L1-L97](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L1-L97)

## System Layers

The application is organized into four distinct layers, each with specific responsibilities:

```

```

**Layer Descriptions**:

| Layer | Purpose | Key Technologies | Entry Points |
| --- | --- | --- | --- |
| **Client Layer** | User interface and browser interactions | HTML, CSS, JavaScript | Port 3007 (mapped to container port 3000) |
| **Presentation Layer** | React components, page routing, UI rendering | Next.js 15.2.4, React 18.3.1, Radix UI | `app/` directory structure |
| **Application Layer** | Business logic, API endpoints, middleware | Next.js API Routes, custom services | `/api/*` routes |
| **Data Layer** | Data persistence, queries, migrations | Prisma ORM 5.22.0, PostgreSQL | `PrismaClient` instance |

**Sources**: [next.config.mjs L1-L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L1-L49)

 [package.json L18-L79](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L18-L79)

 [prisma/schema.prisma L1-L282](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L1-L282)

## Application Structure

The Next.js application uses the App Router pattern with a file-system based routing structure. The following diagram maps the directory structure to functional areas:

```mermaid
flowchart TD

AppLayout["layout.tsx<br>Root layout"]
AppPage["page.tsx<br>Landing page"]
Login["login/page.tsx<br>Authentication"]
Registro["registro/page.tsx<br>User registration"]
Perfil["perfil/page.tsx<br>User profile"]
NuevaSolicitud["nueva-solicitud/page.tsx<br>Create solicitud form"]
Solicitudes["solicitudes/page.tsx<br>List solicitudes"]
SolicitudesPendientes["solicitudes-pendientes/page.tsx<br>Review queue"]
SolicitudDetail["solicitudes/[id]/page.tsx<br>Detail & actions"]
CrearUsuario["crear-usuario/page.tsx<br>Instructor creation"]
Reportes["reportes/page.tsx<br>Reports & analytics"]
API["api/"]
APIAuth["api/auth/[...action]/route.ts"]
APISolicitudes["api/solicitudes/route.ts"]
APINotificaciones["api/notificaciones/route.ts"]
LibAuth["lib/auth.ts<br>JWT functions"]
LibPrisma["lib/prisma.ts<br>PrismaClient singleton"]
LibUtils["lib/utils.ts<br>Helper functions"]
UIComponents["components/ui/*<br>Radix UI wrappers"]
CustomComponents["components/*<br>Custom components"]

APISolicitudes --> LibAuth
APISolicitudes --> LibPrisma
APIAuth --> LibPrisma
AppPage --> CustomComponents
NuevaSolicitud --> UIComponents

subgraph subGraph2 ["components/ - React Components"]
    UIComponents
    CustomComponents
end

subgraph subGraph1 ["lib/ - Shared Utilities"]
    LibAuth
    LibPrisma
    LibUtils
end

subgraph subGraph0 ["app/ - Next.js App Router"]
    AppLayout
    AppPage
    Login
    Registro
    Perfil
    NuevaSolicitud
    Solicitudes
    SolicitudesPendientes
    SolicitudDetail
    CrearUsuario
    Reportes
    API
    APIAuth
    APISolicitudes
    APINotificaciones
    AppLayout --> AppPage
    AppLayout --> Login
    AppLayout --> NuevaSolicitud
    AppLayout --> Solicitudes
    NuevaSolicitud --> APISolicitudes
    Solicitudes --> APISolicitudes
    Login --> APIAuth
end
```

**Key Directories**:

* **`app/`**: Next.js 15 App Router pages and API routes
* **`components/`**: Reusable React components
* **`components/ui/`**: Radix UI primitive wrappers (Button, Dialog, Select, etc.)
* **`lib/`**: Shared utilities and service modules
* **`prisma/`**: Database schema and migration files
* **`public/`**: Static assets

**Sources**: [next.config.mjs L1-L49](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L1-L49)

 [package.json L58](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L58-L58)

## Core Technology Stack

The system is built on the following core technologies:

### Runtime & Framework

```mermaid
flowchart TD

NodeJS["Node.js 20 Alpine<br>JavaScript runtime"]
NextJS["Next.js 15.2.4<br>Full-stack framework"]
React["React 18.3.1<br>UI library"]

NodeJS --> NextJS
NextJS --> React
```

* **Node.js 20**: Runtime environment (Alpine Linux variant for minimal image size)
* **Next.js 15.2.4**: Full-stack framework providing: * App Router for file-system based routing * Server Components for server-side rendering * API Routes for backend endpoints * Standalone output mode for Docker deployment
* **React 18.3.1**: Component-based UI library

### Data Management

```mermaid
flowchart TD

Prisma["Prisma ORM 5.22.0<br>Type-safe database client"]
NeonDriver["@neondatabase/serverless<br>PostgreSQL driver"]
NeonDB["Neon PostgreSQL<br>Serverless database"]

Prisma --> NeonDriver
NeonDriver --> NeonDB
```

* **Prisma ORM 5.22.0**: Type-safe database client with schema-first development * Generator target: `prisma-client-js` with binary targets for `native` and `linux-musl-openssl-3.0.x` * 7 data models: `User`, `Centro`, `Programa`, `Solicitud`, `HorarioDetallado`, `Notificacion`, plus 3 pedagogical models * 5 enums: `Role`, `TipoFormacion`, `Modalidad`, `EstadoSolicitud`, `DiaSemana`, `TipoNotificacion`
* **@neondatabase/serverless**: PostgreSQL driver optimized for serverless environments
* **Neon PostgreSQL**: Serverless PostgreSQL database accessed via `DATABASE_URL` environment variable

### UI & Styling

* **Radix UI**: Unstyled, accessible component primitives (40+ components imported) * Form controls: `@radix-ui/react-select`, `@radix-ui/react-checkbox`, `@radix-ui/react-radio-group` * Overlays: `@radix-ui/react-dialog`, `@radix-ui/react-popover`, `@radix-ui/react-tooltip` * Navigation: `@radix-ui/react-dropdown-menu`, `@radix-ui/react-tabs`
* **Tailwind CSS 3.4.17**: Utility-first CSS framework
* **tailwindcss-animate**: Animation utilities
* **Lucide React**: Icon library
* **class-variance-authority**: Variant-based component styling
* **tailwind-merge**: Utility for merging Tailwind classes

### Form Management & Validation

* **react-hook-form**: Form state management and validation
* **@hookform/resolvers**: Resolver integration for validation libraries
* **zod 3.23.8**: TypeScript-first schema validation

### Authentication & Security

* **jsonwebtoken 9.0.2**: JWT token generation and verification
* **bcryptjs 2.4.3**: Password hashing (10-round salt)
* Environment-based secrets: `JWT_SECRET`, `NEXTAUTH_SECRET`

### Document Export

* **jspdf 2.5.2**: PDF generation for solicitud documents
* **xlsx 0.18.5**: Excel export for solicitud data

### Optional Integrations

* **@ai-sdk/openai 0.0.66**: OpenAI API integration for AI features
* **ai 3.4.32**: AI SDK for advanced features

**Sources**: [package.json L18-L79](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L18-L79)

 [package.json L81-L95](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/package.json#L81-L95)

 [prisma/schema.prisma L4-L12](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L4-L12)

## Key Architectural Patterns

### 1. Monolithic Full-Stack Architecture

The application runs as a single Next.js process handling both client-side rendering and server-side API operations. This simplifies deployment and reduces operational complexity at the cost of scaling flexibility.

**Configuration**: [next.config.mjs L2-L3](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L2-L3)

 sets `output: 'standalone'` for self-contained deployment

### 2. File-System Based Routing

Next.js App Router maps the `app/` directory structure directly to URL routes:

* `app/login/page.tsx` → `/login`
* `app/solicitudes/[id]/page.tsx` → `/solicitudes/{id}`
* `app/api/solicitudes/route.ts` → `/api/solicitudes`

### 3. Type-Safe Data Access

Prisma generates TypeScript types from the database schema, providing compile-time type checking:

* Models: `User`, `Solicitud`, `Centro`, etc.
* Enums: `Role`, `EstadoSolicitud`, `Modalidad`, etc.
* Relations: Automatic type inference for joins

**Schema Definition**: [prisma/schema.prisma L1-L282](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L1-L282)

### 4. JWT-Based Stateless Authentication

Authentication uses JSON Web Tokens stored client-side, enabling stateless API requests:

* Token payload includes: `userId`, `role`, `centroId`
* Middleware: `withAuth()` wraps protected routes
* No server-side session storage required

### 5. Role-Based Data Filtering

API routes apply role-specific query filters:

* **INSTRUCTOR**: `where: { instructorId: user.userId }`
* **COORDINADOR**: `where: { programa: { centroId: user.centroId } }`
* **ADMIN**: No filtering (full access)

### 6. Event-Driven Notifications

Solicitud state transitions trigger notification creation:

* State change → `NotificationService.create()` → Database insert
* Users poll `/api/notificaciones` for updates
* Notification types defined in `TipoNotificacion` enum

**Sources**: [prisma/schema.prisma L213-L230](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L213-L230)

 [prisma/schema.prisma L273-L281](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/prisma/schema.prisma#L273-L281)

## Environment Configuration

The application requires the following environment variables:

| Variable | Purpose | Required | Example |
| --- | --- | --- | --- |
| `DATABASE_URL` | PostgreSQL connection string | Yes | `postgresql://user:pass@host/db` |
| `NEXTAUTH_SECRET` | NextAuth session encryption | Yes | Random 32-char string |
| `NEXTAUTH_URL` | Application base URL | Yes | `https://gestioncomplementarias.axchisan.com` |
| `JWT_SECRET` | JWT token signing key | Yes | Random 32-char string |
| `NODE_ENV` | Runtime environment | Yes | `production` |

**Configuration**: [docker-compose.yml L13-L18](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/docker-compose.yml#L13-L18)

## Build and Deployment Process

The Docker build process follows a multi-stage pattern:

1. **deps stage**: Install production dependencies only (`npm ci --only=production`)
2. **builder stage**: * Copy dependencies from deps stage * Run `npx prisma generate` to create Prisma Client * Execute `npm run build` for Next.js production build
3. **runner stage**: * Copy standalone output from builder * Set up non-root user (`nextjs:nodejs`) * Configure port 3000 exposure * Start with `node server.js`

**Build Configuration**: [Dockerfile L1-L55](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/Dockerfile#L1-L55)

## Security Headers

Next.js configuration applies security headers to all routes:

* `X-Frame-Options: DENY` - Prevents clickjacking
* `X-Content-Type-Options: nosniff` - Prevents MIME type sniffing
* `Referrer-Policy: origin-when-cross-origin` - Controls referrer information
* CORS headers allowing `gestioncomplementarias.axchisan.com`

**Configuration**: [next.config.mjs L14-L46](https://github.com/axchisan/gestionComplementarias/blob/a3d2dcb4/next.config.mjs#L14-L46)

## Summary

The SENA Gestión Complementarias system employs a modern monolithic architecture that prioritizes developer experience, type safety, and deployment simplicity. The Next.js framework provides unified frontend and backend capabilities, Prisma ensures type-safe database access, and Docker containerization enables consistent deployment across environments.

For deeper exploration of specific architectural components, consult the subsection pages referenced at the beginning of this document.
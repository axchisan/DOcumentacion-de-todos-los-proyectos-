# Audit Middleware

> **Relevant source files**
> * [client/lib/core/services/audit_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart)
> * [client/lib/presentation/screens/audit/audit_log_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart)
> * [server/app/middleware/audit_middleware.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py)
> * [server/app/routers/reports.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/reports.py)

## Purpose and Scope

The Audit Middleware is an automatic request logging system that captures all API operations for compliance tracking and system audit trails. It operates as a FastAPI middleware component that intercepts HTTP requests, extracts relevant metadata, and persists comprehensive audit logs to the database without requiring manual instrumentation in individual endpoints.

This document covers the middleware implementation, request interception logic, data filtering, and automatic action classification. For the user interface that displays audit logs, see [Audit Log Screen](/axchisan/GestionInventarioSENA/10.2-audit-log-screen). For client-side audit operations and API endpoints, see [Audit Service & APIs](/axchisan/GestionInventarioSENA/10.3-audit-service-and-apis).

**Sources:** [server/app/middleware/audit_middleware.py L1-L323](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L1-L323)

---

## Middleware Architecture

The `AuditMiddleware` class extends `BaseHTTPMiddleware` and implements the middleware lifecycle for FastAPI applications. It is registered in the application's middleware stack and intercepts every HTTP request before it reaches the route handlers.

### Middleware Lifecycle Flow

```

```

**Sources:** [server/app/middleware/audit_middleware.py L61-L91](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L61-L91)

 [server/app/middleware/audit_middleware.py L158-L218](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L158-L218)

---

## Request Filtering Logic

The middleware selectively audits requests based on configured rules to avoid performance overhead and log spam.

### Audit Decision Criteria

| Condition | Audited | Notes |
| --- | --- | --- |
| HTTP Method in `{POST, PUT, DELETE, PATCH}` | Yes | State-changing operations |
| Path starts with `/api/` | Yes | API operations only |
| Path is `/api/auth/login` | Yes | Login tracked explicitly |
| Path is `/api/audit-logs` | No | Prevents recursion |
| Path is `/api/stats` | No | High-frequency, low-value |
| Path is `/docs` or `/openapi.json` | No | Documentation endpoints |
| Path is `/` (root) | No | Health checks |

The filtering logic is implemented in the `_should_audit()` method:

```

```

**Sources:** [server/app/middleware/audit_middleware.py L22-L32](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L22-L32)

 [server/app/middleware/audit_middleware.py L93-L112](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L93-L112)

---

## Data Capture Process

The middleware captures comprehensive request and response metadata for each audited operation.

### Captured Request Data Structure

```

```

The `_capture_request_data()` method extracts and structures this information:

**Sources:** [server/app/middleware/audit_middleware.py L114-L141](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L114-L141)

### Sensitive Data Filtering

The middleware implements recursive filtering to prevent sensitive information from being stored in audit logs:

**Filtered Fields (case-insensitive):**

* `password`
* `password_hash`
* `token`
* `secret`
* `key`
* `authorization`

The `_filter_sensitive_data()` method recursively traverses dictionaries and lists, replacing sensitive values with `"[FILTERED]"`:

**Sources:** [server/app/middleware/audit_middleware.py L143-L156](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L143-L156)

---

## Action and Entity Classification

The middleware automatically determines the action type and entity type from the request path and HTTP method.

### Action Determination Logic

```

```

The `_determine_action()` method implements this logic:

**Sources:** [server/app/middleware/audit_middleware.py L260-L279](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L260-L279)

### Entity Type Extraction

The middleware maps URL paths to entity types:

| Path Pattern | Entity Type | Example |
| --- | --- | --- |
| `/inventory-checks` | `inventory_check` | Verification operations |
| `/inventory` | `inventory_item` | Equipment items |
| `/loans` | `loan` | Loan requests |
| `/users` | `user` | User management |
| `/maintenance-requests` | `maintenance_request` | Repair requests |
| `/environments` | `environment` | Location management |
| `/auth` | `authentication` | Auth operations |
| `/notifications` | `notification` | Notification events |
| `/supervisor-reviews` | `supervisor_review` | Review workflows |

**Sources:** [server/app/middleware/audit_middleware.py L281-L308](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L281-L308)

---

## Audit Log Record Structure

The middleware creates `AuditLog` database records with the following schema:

### AuditLog Database Model Fields

```

```

### new_values JSON Structure

The `new_values` field stores comprehensive request/response metadata:

```

```

**Sources:** [server/app/middleware/audit_middleware.py L187-L206](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L187-L206)

---

## User Identification

The middleware extracts user information from JWT tokens in the `Authorization` header.

### User Extraction Flow

```

```

The `_get_user_from_request()` method implements this logic:

**Sources:** [server/app/middleware/audit_middleware.py L220-L241](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L220-L241)

---

## Friendly Description Generation

The middleware generates human-readable descriptions for audit logs using predefined message templates.

### Action Message Mapping

The `ACTION_MESSAGES` dictionary maps action codes to Spanish descriptions:

| Action Code | Description |
| --- | --- |
| `LOGIN` | "Inicio de sesión" |
| `CREATE_INVENTORY_ITEM` | "Se creó un item en el inventario" |
| `UPDATE_LOAN` | "Se actualizó un préstamo" |
| `DELETE_MAINTENANCE_REQUEST` | "Se eliminó una solicitud de mantenimiento" |
| `UPDATE_INVENTORY_CHECK` | "Se actualizó una verificación de inventario" |

The `_get_friendly_description()` method enhances these with contextual details from the request body (e.g., item name, email, title):

**Sources:** [server/app/middleware/audit_middleware.py L34-L59](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L34-L59)

 [server/app/middleware/audit_middleware.py L243-L258](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L243-L258)

---

## Entity ID Extraction

The middleware attempts to extract entity identifiers from URL paths for tracking specific records.

### ID Extraction Logic

```

```

The `_extract_entity_id()` method scans path segments for UUID or numeric patterns:

**Sources:** [server/app/middleware/audit_middleware.py L309-L322](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L309-L322)

---

## Database Persistence

The middleware persists audit logs asynchronously to avoid blocking the main request flow.

### Persistence Flow

```

```

The middleware uses try-except blocks to ensure audit failures don't impact the main request:

**Sources:** [server/app/middleware/audit_middleware.py L158-L218](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L158-L218)

---

## Integration with FastAPI

The middleware is registered in the FastAPI application's middleware stack.

### Middleware Registration

The middleware should be added to the FastAPI app in the main application setup:

```

```

The middleware intercepts all requests after CORS middleware but before route handlers execute.

**Sources:** [server/app/middleware/audit_middleware.py L16-L21](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L16-L21)

---

## Performance Considerations

The middleware implements several optimizations to minimize performance impact:

### Optimization Strategies

| Strategy | Implementation | Benefit |
| --- | --- | --- |
| Selective Auditing | Only audit state-changing methods (POST, PUT, DELETE, PATCH) | Reduces log volume by ~80% |
| Endpoint Exclusion | Skip `/stats`, `/audit-logs`, documentation | Prevents recursion and spam |
| Async Persistence | Database writes don't block response | Sub-millisecond overhead |
| Silent Failures | Audit errors don't fail requests | System reliability |
| Body Size Limit | Truncate large request bodies to 500 chars | Memory efficiency |

**Sources:** [server/app/middleware/audit_middleware.py L22-L32](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L22-L32)

 [server/app/middleware/audit_middleware.py L114-L141](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L114-L141)

---

## Error Handling

The middleware implements defensive error handling at multiple levels:

### Error Handling Layers

```

```

All error handling ensures that audit failures never interrupt the normal request flow:

**Sources:** [server/app/middleware/audit_middleware.py L82-L89](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L82-L89)

 [server/app/middleware/audit_middleware.py L136-L141](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L136-L141)

 [server/app/middleware/audit_middleware.py L211-L218](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/middleware/audit_middleware.py#L211-L218)

---

## Client-Side Integration

The audit middleware is consumed by the client application through the `AuditService` class, which provides methods for querying and analyzing audit logs.

### Client Service Methods

```

```

**Sources:** [client/lib/core/services/audit_service.dart L46-L157](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/audit_service.dart#L46-L157)

---

## Audit Log Display

The captured audit logs are presented to users through the `AuditLogScreen`, which provides filtering, pagination, and detailed log inspection.

### UI Components

```

```

**Sources:** [client/lib/presentation/screens/audit/audit_log_screen.dart L1-L727](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/audit/audit_log_screen.dart#L1-L727)

---

## Reporting Integration

Audit logs are included in the comprehensive reporting system, allowing administrators to generate audit reports for compliance purposes.

### Audit Report Generation

The `_get_report_data()` function in the reports router includes special handling for audit report types:

**Sources:** [server/app/routers/reports.py L470-L502](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/reports.py#L470-L502)

The audit report includes:

* **Date and Time**: Full timestamp with timezone
* **User Information**: Name, email, role
* **Action**: Friendly Spanish description
* **Entity**: Type and ID
* **IP Address**: Client location
* **Status**: HTTP response code (success/error)
* **Duration**: Request processing time
* **Details**: HTTP method and endpoint

Reports can be exported as PDF, Excel, or CSV formats for archival and compliance documentation.

**Sources:** [server/app/routers/reports.py L316-L379](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/reports.py#L316-L379)

 [server/app/routers/reports.py L506-L571](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/reports.py#L506-L571)
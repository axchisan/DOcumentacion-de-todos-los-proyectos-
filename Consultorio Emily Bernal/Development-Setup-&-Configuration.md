# Development Setup & Configuration

> **Relevant source files**
> * [.gitignore](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/.gitignore)
> * [Admin/inicioAdmin.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php)

## Purpose and Scope

This document provides a comprehensive guide for setting up the Consultorio Emily Bernal development environment. It covers the project directory structure, external library dependencies, and version control configuration necessary to run the application locally.

For information about database schema and table structures, see [Database Architecture](/axchisan/Consultorio_Emily_Bernal/4-database-architecture). For deployment-specific configurations and production setup, those aspects are beyond the scope of this document.

---

## Prerequisites

### System Requirements

| Component | Requirement | Notes |
| --- | --- | --- |
| PHP | Version 7.0 or higher | Requires mysqli extension enabled |
| MySQL | Version 5.7 or higher | InnoDB storage engine required |
| Web Server | Apache/Nginx | mod_rewrite enabled for Apache |
| PHP Extensions | `mysqli`, `gd`, `mbstring` | Required for database, image handling, and TCPDF |
| File Permissions | Write access to `uploads/` directory | For medical image storage |

The application uses PHP's native `mysqli` extension for database connectivity [php/conexionDB.php L1-L20](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/php/conexionDB.php#L1-L20)

 The `gd` extension is required for image manipulation in TCPDF PDF generation, and `mbstring` for proper character encoding handling.

**Sources:** Admin/inicioAdmin.php, php/conexionDB.php (inferred)

---

## Project Directory Structure

### Root Directory Organization

```

```

**Directory Structure: Complete project organization showing the separation of presentation (Admin/), business logic (php/, crud/), reporting (Reportes/), storage (uploads/), and frontend assets (src/)**

### Directory Descriptions

| Directory | Purpose | Key Files |
| --- | --- | --- |
| `Admin/` | Administrative interface pages accessed by doctors | `inicioAdmin.php`, `calendar.php`, `historia_clinica.php`, `ver_historia.php`, `informe.php`, `realizar_consulta.php`, `doctores.php` |
| `php/` | Core business logic and database abstraction layer | `conexionDB.php`, `consultas.php`, `cerrar.php` |
| `crud/` | CRUD operations and AJAX endpoints | `registro_INSERT.php`, `realizar_consultasUPDATE.php`, `toggle_unavailable.php`, `delete_image.php` |
| `Reportes/` | PDF generation scripts using TCPDF | `descargar_historia.php`, `generate_informe_pdf.php` |
| `uploads/` | User-uploaded medical images (radiographs and oral photos) | `radiografias/`, `fotos_boca/` |
| `src/` | Frontend static assets (CSS, JavaScript, images) | `css/`, `js/`, `img/` |
| `_recursos_/` | Third-party libraries excluded from version control | `AnyChart/`, `DataTables/`, `jquery-confirm-v3.3.4/` |

The `Admin/` directory contains seven main interface files that implement the complete doctor workflow. Each file includes session validation [Admin/inicioAdmin.php L2-L24](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L2-L24)

 before rendering content.

The separation of `php/` and `crud/` directories reflects architectural boundaries: `php/` contains reusable functions and connection management, while `crud/` contains operation-specific scripts triggered by form submissions or AJAX requests.

**Sources:** .gitignore:1-7, Admin/inicioAdmin.php:1-165 (structure inferred)

---

## Dependency Management

### External Library Dependencies

```

```

**Dependency Graph: Shows how external libraries are utilized across the application's main interfaces**

### Dependency Reference Table

| Library | Version | Purpose | Loaded From | Used In |
| --- | --- | --- | --- | --- |
| Bootstrap | 4.x | Responsive UI framework, grid system, form styling | `src/css/lib/bootstrap/css/bootstrap.min.css``src/css/lib/bootstrap/js/bootstrap.min.js` | All Admin pages [Admin/inicioAdmin.php L36-L158](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L36-L158) |
| jQuery | 3.5.1 | DOM manipulation, AJAX requests | `src/js/jquery.js``src/js/lib/datatable/js/jquery-3.5.1.js` | All Admin pages [Admin/inicioAdmin.php L157-L160](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L157-L160) |
| DataTables | Latest | Interactive table sorting, filtering, pagination | `src/js/lib/datatable/css/jquery.dataTables.min.css``src/js/lib/datatable/js/jquery.dataTables.min.js``src/js/lib/datatable/js/dataTables.responsive.min.js` | `inicioAdmin.php`, `historia_clinica.php` [Admin/inicioAdmin.php L40-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L40-L163) |
| FullCalendar | 3.x or 4.x | Calendar interface for appointment scheduling | Loaded in `calendar.php` | `calendar.php` only |
| TCPDF | 6.x | Server-side PDF generation | Included in `Reportes/` directory | `descargar_historia.php`, `generate_informe_pdf.php` |
| FontAwesome | 5.x | Icon fonts for UI elements | `src/css/lib/fontawesome/css/all.css` | All Admin pages [Admin/inicioAdmin.php L39](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L39-L39) |
| jquery-confirm | 3.3.4 | Custom dialog boxes and confirmations | `_recursos_/jquery-confirm-v3.3.4/` | Various CRUD operations |

### Asset Loading Pattern

The application follows a consistent asset loading pattern visible in [Admin/inicioAdmin.php L31-L49](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L31-L49)

:

```
<!-- CSS Loading Order -->
1. Bootstrap CSS (framework foundation)
2. Custom admin.css (application-specific styles)
3. Custom custom_styles.css (overrides)
4. FontAwesome (icons)
5. Library-specific CSS (DataTables, etc.)

<!-- JavaScript Loading Order -->
1. jQuery (dependency foundation)
2. Bootstrap JS (requires jQuery)
3. Custom admin.js (application logic)
4. Library-specific JS (DataTables, etc.)
5. Inline scripts (page-specific initialization)
```

This loading order ensures dependencies are resolved correctly, with jQuery loaded before libraries that depend on it [Admin/inicioAdmin.php L157-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L157-L163)

**Sources:** Admin/inicioAdmin.php:31-49, Admin/inicioAdmin.php:157-163

---

## Database Configuration

### Connection Setup

The database connection is centralized in `php/conexionDB.php`, which should be configured with the following parameters:

```
Server Configuration:
- Host: localhost (or remote MySQL server)
- Username: Database user with full privileges on application schema
- Password: Secure password for database user
- Database: Schema name (e.g., "consultorio_emily_bernal")
- Character Set: utf8 or utf8mb4 (for international character support)
```

All administrative pages include this connection file at the top [Admin/inicioAdmin.php L3](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L3-L3)

:

```

```

The connection object `$link` is used throughout the application for database queries and is passed to query functions defined in `php/consultas.php` [Admin/inicioAdmin.php L4](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L4-L4)

### Query Abstraction Layer

The `php/consultas.php` file provides reusable query functions that accept the database connection as a parameter:

| Function | Purpose | Return Type |
| --- | --- | --- |
| `consultarDoctor($link, $id_doctor)` | Retrieve doctor information | Associative array |
| `consultarPaciente($link, $id_paciente)` | Retrieve patient information | Associative array |
| `MostrarCitas($link, $id_doctor)` | Get appointments for a doctor | mysqli_result object |
| `validarToken($link, $id, $tipo, $token)` | Verify session token validity | Boolean |

These functions are called throughout the administrative interface [Admin/inicioAdmin.php L26-L27](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L26-L27)

:

```

```

**Sources:** Admin/inicioAdmin.php:3-4, Admin/inicioAdmin.php:26-27

---

## Version Control Configuration

### .gitignore Configuration

The `.gitignore` file [.gitignore L1-L7](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/.gitignore#L1-L7)

 specifies which files and directories should be excluded from version control:

```

```

**Version Control Exclusions: Shows the systematic approach to excluding system files and large third-party libraries**

### Exclusion Rationale

| Excluded Item | Reason | Alternative Source |
| --- | --- | --- |
| `.DS_Store` | macOS system file, not part of application | Generated automatically by macOS |
| `node_modules/` | npm dependency directory (if Node.js used for build tools) | Restored via `npm install` |
| `_recursos_/AnyChart` | Large charting library (potentially unused in current implementation) | Download from AnyChart CDN or official site |
| `_recursos_/DataTables` | DataTables library files (also available in `src/js/lib/datatable/`) | Loaded from `src/` directory [Admin/inicioAdmin.php L40-L42](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L40-L42) |
| `_recursos_/jquery-confirm-v3.3.4` | Dialog library duplicate | Download from official source or CDN |

The configuration suggests that the `_recursos_/` directory contains experimental or alternative versions of libraries, with the active versions loaded from `src/js/lib/` and `src/css/lib/` directories.

### Missing from Version Control

Notable files that should be configured but are NOT in `.gitignore`:

1. **Database Configuration**: `php/conexionDB.php` should ideally be excluded and replaced with a template file (e.g., `conexionDB.php.example`) to prevent exposing database credentials
2. **Uploaded Files**: `uploads/radiografias/` and `uploads/fotos_boca/` contain sensitive patient medical images and should be excluded from version control
3. **Session Data**: Any temporary session storage directories

**Recommendation**: Update `.gitignore` to include:

```
php/conexionDB.php
uploads/
*.log
.env
```

**Sources:** .gitignore:1-7

---

## Initial Setup Procedure

### Step-by-Step Installation

```

```

**Installation Workflow: Shows the complete setup sequence from repository clone to first successful login**

### Configuration Checklist

| Step | Task | Verification |
| --- | --- | --- |
| 1 | Clone repository | Files present in project directory |
| 2 | Configure web server virtual host | Access `http://localhost/projectname/` shows application |
| 3 | Create MySQL database | Database visible in MySQL client |
| 4 | Import database schema | Tables `doctor`, `paciente`, `citas`, etc. exist |
| 5 | Configure `php/conexionDB.php` | Set correct host, username, password, database name |
| 6 | Create `uploads/radiografias/` directory | Directory exists with write permissions |
| 7 | Create `uploads/fotos_boca/` directory | Directory exists with write permissions |
| 8 | Verify PHP extensions | `mysqli`, `gd`, `mbstring` enabled in `php.ini` |
| 9 | Test database connection | Access `index.php` without connection errors |
| 10 | Create initial doctor account | Insert record into `doctor` table with hashed password |
| 11 | Test login | Successfully access `Admin/inicioAdmin.php` |

### Directory Permissions

Set appropriate permissions for upload directories:

```

```

The application requires write access to these directories for image upload functionality used in `informe.php`. File uploads are referenced in the database [Admin/inicioAdmin.php L129](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L129-L129)

 via the medical report viewing interface.

**Sources:** Admin/inicioAdmin.php:1-165 (setup requirements inferred)

---

## Development Environment Workflow

### Session Management Setup

All administrative pages require session initialization [Admin/inicioAdmin.php L2](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L2-L2)

:

```

```

The session validation pattern used across all admin pages [Admin/inicioAdmin.php L7-L24](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L7-L24)

 requires:

1. `$_SESSION['id_doctor']` - Doctor's database ID
2. `$_SESSION['session_token']` - Unique token preventing concurrent logins

For development, ensure `session.save_path` in `php.ini` is configured correctly, and the directory has write permissions.

### Testing Access Control

To test the authentication system:

1. Create a test doctor account in the `doctor` table
2. Access any admin page directly (e.g., `Admin/inicioAdmin.php`)
3. Verify redirect to `../index.php` without valid session
4. Login through `index.php`
5. Verify `$_SESSION['session_token']` is stored in database `doctor` table
6. Confirm access to admin pages with valid session

The token validation mechanism [Admin/inicioAdmin.php L17](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L17-L17)

 prevents concurrent logins by comparing the session token against the database.

**Sources:** Admin/inicioAdmin.php:2, Admin/inicioAdmin.php:7-24

---

## Common Development Issues

### Issue Resolution Table

| Issue | Symptom | Solution |
| --- | --- | --- |
| Database connection failure | "mysqli_connect() failed" error | Verify credentials in `php/conexionDB.php`; check MySQL service is running |
| Session not persisting | Logged out immediately after login | Check `session.save_path` permissions; verify session cookies not blocked |
| CSS/JS not loading | Unstyled pages or JavaScript errors | Verify file paths in HTML relative to current directory; check `src/` directory exists |
| DataTables not initializing | Table displays without sorting/filtering | Verify jQuery loads before DataTables [Admin/inicioAdmin.php L157-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L157-L163) <br> ; check console for JavaScript errors |
| Image upload fails | No files saved in `uploads/` | Check directory exists and has write permissions (755 or 777) |
| PDF generation errors | Blank PDFs or TCPDF errors | Verify TCPDF library is complete in `Reportes/` directory; check PHP `gd` extension enabled |
| Character encoding issues | Garbled text in PDFs or pages | Verify MySQL connection uses UTF-8; check `utf8_decode()` usage in output [Admin/inicioAdmin.php L64](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L64-L64) |
| Token validation fails unexpectedly | Session destroyed on page navigation | Verify `session_token` field exists in `doctor` table; check `validarToken()` function logic |

### Debug Mode Configuration

For development debugging, add to `php/conexionDB.php`:

```

```

This enables detailed error reporting for database queries and PHP errors during development.

**Sources:** Admin/inicioAdmin.php:7-24, Admin/inicioAdmin.php:157-163

---

## File Path Reference Map

```

```

**File Path Map: Demonstrates the relative path structure used in Admin/ pages when including PHP files and loading assets**

All pages in the `Admin/` directory use `../` prefix to access resources in the parent directory [Admin/inicioAdmin.php L3-L163](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L3-L163)

 When adding new files, maintain this consistent relative path structure.

**Sources:** Admin/inicioAdmin.php:3-4, Admin/inicioAdmin.php:35-42, Admin/inicioAdmin.php:157-163
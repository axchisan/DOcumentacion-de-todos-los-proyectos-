# Configuration and Deployment

> **Relevant source files**
> * [assets/css/style.css](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css)
> * [assets/js/script.js](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js)
> * [ddbb/DBConexion.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php)
> * [index.php](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php)

## Purpose and Scope

This document covers configuration requirements, deployment procedures, and environment setup for the Abogado application. It details hardcoded values that require modification for production deployment, web server configuration, directory structure requirements, and security considerations for moving from development to production environments.

For information about the database schema and table structure, see [Data Model and Schema](/GroveLive/abogado/6-data-model-and-schema). For security measures and concerns, see [Security Considerations](/GroveLive/abogado/7-security-considerations).

---

## Configuration Overview

The Abogado application contains several hardcoded configuration values embedded directly in source code. These values are currently set for local development and must be modified before production deployment.

### Critical Configuration Points

| Configuration Aspect | Current Value | Location | Production Action Required |
| --- | --- | --- | --- |
| Database Host | `localhost` | [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) | Change to production DB host |
| Database User | `root` | [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) | Change to restricted user |
| Database Password | Empty string `""` | [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) | Set strong password |
| Database Name | `tablaRelacional` | [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) | Verify or change name |
| Character Encoding | `utf8` | [ddbb/DBConexion.php L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L12-L12) | Confirm encoding matches DB |
| Initial Redirect | `views/home.php` | [index.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L2-L2) | Verify path correctness |

**Sources:** [ddbb/DBConexion.php L1-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L1-L17)

 [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

---

## Database Configuration

### Connection Parameters

The database connection is established through the `DBConexion` class with hardcoded credentials. The static `connection()` method creates a new `mysqli` connection on each invocation.

**Configuration Diagram: Database Connection Establishment**

```

```

**Sources:** [ddbb/DBConexion.php L5-L16](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L5-L16)

### Required Modifications for Production

To prepare `DBConexion.php` for production deployment:

1. **Database Host**: Replace `localhost` with the production database server hostname or IP address * Location: [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) * Example: `"db.production-server.com"` or `"10.0.1.50"`
2. **Database User**: Replace `root` with a dedicated application user with minimal privileges * Location: [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) * User should have only `SELECT` permissions on the `abogado` table
3. **Database Password**: Set a strong password for the database user * Location: [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) * Currently empty string poses critical security risk
4. **Database Name**: Verify `tablaRelacional` matches production database name * Location: [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7)
5. **Error Handling**: Consider suppressing error messages in production * Location: [ddbb/DBConexion.php L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L10-L10) * Current implementation echoes `mysqli_connect_error()` which exposes internal details

**Sources:** [ddbb/DBConexion.php L7-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L14)

---

## Directory Structure Requirements

### File Organization

The application requires a specific directory structure for proper operation. All file paths are relative to the application root.

**Diagram: Required Directory Structure**

```

```

### Directory Structure Table

| Directory | Purpose | Required Files | Notes |
| --- | --- | --- | --- |
| `/` | Application root | `index.php` | Entry point; must be web server document root |
| `/views/` | Presentation layer | `home.php`, `abogadosView.php` | User-facing HTML pages |
| `/controllers/` | Business logic | `abogadosCtrl.php` | Controller classes |
| `/models/` | Data access | `abogadosModel.php` | Model classes |
| `/ddbb/` | Database utilities | `DBConexion.php` | Connection management |
| `/assets/css/` | Stylesheets | `style.css` | CSS theme and components |
| `/assets/js/` | Client scripts | `script.js` | JavaScript enhancements |

**Sources:** [index.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L2-L2)

 [ddbb/DBConexion.php L1](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L1-L1)

 [assets/css/style.css L1](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L1)

 [assets/js/script.js L1](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L1-L1)

### Path Dependencies

The application uses relative paths with `require_once` statements. The path resolution depends on the web server's document root configuration:

* [index.php L2](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L2-L2)  redirects to `views/home.php` (relative to document root)
* Views include controllers using `require_once '../controllers/abogadosCtrl.php'`
* Controllers include models using `require_once '../models/abogadosModel.php'`
* Models include DBConexion using `require_once '../ddbb/DBConexion.php'`

All directories must maintain this structure for `require_once` paths to resolve correctly.

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

---

## Web Server Configuration

### System Requirements

| Requirement | Specification | Notes |
| --- | --- | --- |
| Web Server | Apache 2.4+ or Nginx 1.18+ | Must support PHP execution |
| PHP Version | 5.6+ (recommended 7.4+) | Version not specified in code |
| PHP Extensions | `mysqli` | Required for database connectivity |
| MySQL/MariaDB | 5.7+ / 10.3+ | Database server |
| Character Set | UTF-8 support | For proper text encoding |

**Sources:** [ddbb/DBConexion.php L7-L12](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L12)

### Apache Configuration

#### Virtual Host Setup

```xml
<VirtualHost *:80>
    ServerName abogado.local
    DocumentRoot /var/www/abogado
    
    <Directory /var/www/abogado>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    <Directory /var/www/abogado/assets>
        Options -Indexes
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/abogado-error.log
    CustomLog ${APACHE_LOG_DIR}/abogado-access.log combined
</VirtualHost>
```

#### Key Directives

* **DocumentRoot**: Must point to application root containing `index.php`
* **AllowOverride All**: Required if using `.htaccess` for rewrite rules
* **Options -Indexes**: Prevents directory listing of assets
* **Require all granted**: Allows public access (adjust for production)

### Nginx Configuration

#### Server Block Setup

```
server {
    listen 80;
    server_name abogado.local;
    root /var/www/abogado;
    index index.php;
    
    charset utf-8;
    
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    
    location /assets/ {
        autoindex off;
    }
    
    location ~ /\. {
        deny all;
    }
    
    access_log /var/log/nginx/abogado-access.log;
    error_log /var/log/nginx/abogado-error.log;
}
```

#### Key Directives

* **root**: Must point to application root
* **try_files**: Allows direct access to static files, falls back to PHP
* **fastcgi_pass**: PHP-FPM socket path (adjust for your system)
* **autoindex off**: Disables directory listing
* **deny all for /.**: Blocks access to hidden files

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

---

## Asset Configuration

### CSS Custom Properties

The application uses CSS custom properties (CSS variables) for theme configuration. These are defined in [assets/css/style.css L1-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L10)

 and can be modified to change the application's visual appearance.

**CSS Configuration Variables**

| Property | Default Value | Purpose |
| --- | --- | --- |
| `--primary-color` | `#1e1f26` | Main background color |
| `--secondary-color` | `#6f42c1` | Accent color (buttons, borders) |
| `--accent-color` | `#e74c3c` | Hover and emphasis color |
| `--text-color` | `#ffffff` | Primary text color |
| `--light-bg` | `#292b36` | Card and container background |
| `--border-color` | `#44475a` | Border colors |
| `--shadow` | `0 2px 10px rgba(0, 0, 0, 0.3)` | Box shadow values |
| `--transition` | `all 0.3s ease` | Animation timing |

To customize the theme, modify these values in [assets/css/style.css L1-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L10)

**Sources:** [assets/css/style.css L1-L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L10)

### JavaScript Configuration

The navigation enhancement script in [assets/js/script.js L1-L24](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L1-L24)

 contains a hardcoded delay:

* **Loader Display Duration**: 800ms delay before navigation ([assets/js/script.js L18-L20](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L18-L20) )
* Can be modified by changing the `setTimeout` value on line 18

**Sources:** [assets/js/script.js L18-L20](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L18-L20)

---

## File Permissions

### Recommended Permissions

| Path | Owner | Permissions | Purpose |
| --- | --- | --- | --- |
| `/` (all directories) | `www-data:www-data` | `755` | Read/execute for web server |
| `*.php` files | `www-data:www-data` | `644` | Read for web server |
| `/assets/css/*.css` | `www-data:www-data` | `644` | Read for web server |
| `/assets/js/*.js` | `www-data:www-data` | `644` | Read for web server |

**Commands for setting permissions (Linux):**

```

```

Replace `www-data` with your web server user (e.g., `apache`, `nginx`, `httpd`).

---

## Deployment Process

### Development to Production Migration Flow

**Diagram: Deployment Configuration Flow**

```

```

**Sources:** [ddbb/DBConexion.php L7-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L14)

### Pre-Deployment Checklist

| Step | Action | File(s) Affected | Verification |
| --- | --- | --- | --- |
| 1 | Modify database credentials | [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) | Test connection |
| 2 | Update error handling | [ddbb/DBConexion.php L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L10-L10) | Verify errors logged, not displayed |
| 3 | Set file permissions | All files | Check `ls -la` output |
| 4 | Configure web server | Apache/Nginx config | Test `http://domain` |
| 5 | Verify directory structure | All directories | Confirm all paths exist |
| 6 | Test database connectivity | [ddbb/DBConexion.php L5-L16](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L5-L16) | Access `/views/home.php` |
| 7 | Verify asset loading | [assets/css/style.css](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css) <br>  [assets/js/script.js](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js) | Inspect browser network tab |
| 8 | Test application flow | All views | Click through navigation |

### Deployment Steps

1. **Clone Repository** ``` ```
2. **Modify Database Configuration** * Edit [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) * Update all four connection parameters * Remove or suppress error echo on [ddbb/DBConexion.php L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L10-L10)
3. **Import Database Schema** ``` ``` (Assumes schema SQL file exists; see [Data Model and Schema](/GroveLive/abogado/6-data-model-and-schema))
4. **Configure Web Server** * Create virtual host configuration (Apache or Nginx) * Set document root to application directory * Enable PHP processing * Restart web server
5. **Set File Permissions** * Execute permission commands listed above * Verify web server user can read all files
6. **Test Application** * Access root URL (should redirect to `views/home.php`) * Verify lawyer listing displays * Click profile link, verify individual view loads * Check browser console for asset loading errors

**Sources:** [ddbb/DBConexion.php L1-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L1-L17)

 [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

---

## Environment-Specific Configuration

### Current State Analysis

**Diagram: Hardcoded Configuration Dependencies**

```

```

**Sources:** [ddbb/DBConexion.php L7-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L14)

### Recommended Improvements

While beyond the scope of current deployment, consider these architectural improvements for future iterations:

1. **Configuration File Separation** * Extract database credentials to separate `config.php` * Add `config.php` to `.gitignore` * Provide `config.example.php` template
2. **Environment Variables** * Use `getenv()` to read credentials from environment * Configure via web server environment directives * Eliminates hardcoded credentials in source code
3. **Multiple Environment Support** * Separate configurations for development, staging, production * Environment detection mechanism * Different error handling per environment

---

## Security Considerations for Deployment

### Critical Security Issues

The following security concerns must be addressed before production deployment:

| Issue | Location | Risk Level | Mitigation |
| --- | --- | --- | --- |
| Empty database password | [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) | **Critical** | Set strong password |
| Root database user | [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) | **Critical** | Create restricted user |
| Error message exposure | [ddbb/DBConexion.php L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L10-L10) | **High** | Log errors, don't echo |
| No authentication | All views | **High** | Add if modification features added |
| Hardcoded credentials | [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) | **Medium** | Use environment variables |

For complete security analysis, see [Security Considerations](/GroveLive/abogado/7-security-considerations).

**Sources:** [ddbb/DBConexion.php L7-L14](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L14)

---

## Troubleshooting Common Deployment Issues

### Connection Problems

| Symptom | Likely Cause | Solution |
| --- | --- | --- |
| "Failed to connect to MySQL" displayed | Database credentials incorrect | Verify [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7) <br>  matches DB |
| Blank page on access | PHP error with display_errors off | Check web server error logs |
| "require_once: No such file" | Directory structure incorrect | Verify all directories exist |
| 404 on `/` | Document root misconfigured | Point to directory containing `index.php` |
| CSS/JS not loading | Incorrect asset paths | Verify `/assets/` accessible via web |

### Path Resolution Issues

If views fail to load controllers/models:

1. Verify directory structure matches required layout
2. Check `require_once` paths use correct relative paths (`../`)
3. Ensure all referenced files exist at expected locations
4. Confirm web server has read permissions on all directories

**Sources:** [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

 [ddbb/DBConexion.php L1-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L1-L17)

---

## Production Readiness Checklist

Before deploying to production, confirm:

* Database credentials updated in [ddbb/DBConexion.php L7](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L7-L7)
* Database user has minimal required permissions (SELECT only)
* Strong password set for database user
* Error echoing removed or suppressed in [ddbb/DBConexion.php L10](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L10-L10)
* Web server configured with correct document root
* File permissions set to 755 (directories) and 644 (files)
* Database exists and contains `abogado` table with data
* PHP `mysqli` extension enabled
* Character set configured to UTF-8 in database
* All asset files accessible via `/assets/` URL path
* Application tested: listing view and individual profile view
* Browser console shows no asset loading errors
* Web server error logs reviewed for PHP warnings/errors

**Sources:** [ddbb/DBConexion.php L1-L17](https://github.com/GroveLive/abogado/blob/8bfc71d0/ddbb/DBConexion.php#L1-L17)

 [index.php L1-L5](https://github.com/GroveLive/abogado/blob/8bfc71d0/index.php#L1-L5)

 [assets/css/style.css L1](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/css/style.css#L1-L1)

 [assets/js/script.js L1](https://github.com/GroveLive/abogado/blob/8bfc71d0/assets/js/script.js#L1-L1)
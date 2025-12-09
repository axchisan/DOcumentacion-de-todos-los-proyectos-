# Profile Management

> **Relevant source files**
> * [.gitignore](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/.gitignore)
> * [src/backend/perfil/update.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/update.php)
> * [src/backend/perfil/uploads/profile_6807f3b3839528.57825759.jpg](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/uploads/profile_6807f3b3839528.57825759.jpg)
> * [src/backend/perfil/uploads/profile_6807f9a21b8730.38527839.jpg](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/uploads/profile_6807f9a21b8730.38527839.jpg)
> * [src/backend/perfil/uploads/profile_6807f9cd029340.20664564.jpg](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/uploads/profile_6807f9cd029340.20664564.jpg)
> * [src/frontend/inicio/index.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/inicio/index.php)
> * [src/frontend/panel/panel-usuario.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php)
> * [src/frontend/repositorio/repositorio.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/repositorio/repositorio.php)

## Purpose and Scope

This document describes the profile management functionality within the User Dashboard, specifically covering how users can edit their personal information, change passwords, manage notification preferences, and upload profile images. This is a subsection of the main User Dashboard (see [4](/axchisan/El-rincon-de-ADSO/4-user-dashboard-(panel-usuario)) for the overall panel architecture). For information about viewing other users' profiles, see [6.4](/axchisan/El-rincon-de-ADSO/6.4-user-profiles).

Profile management is accessed through the "Mi Perfil" tab in the user panel at [src/frontend/panel/panel-usuario.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php)

 and processed by the backend at [src/backend/perfil/update.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/update.php)

---

## System Architecture

The profile management system is organized into three main tabs within the user panel, each handling different aspects of user account management.

### Tab Structure

```

```

**Sources:** [src/frontend/panel/panel-usuario.php L350-L550](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L350-L550)

---

## Personal Data Management

The personal data tab (`datos-personales`) allows users to update their profile information including name, email, phone, profession, biography, and profile image.

### Form Fields

| Field | HTML ID | Database Column | Required | Validation |
| --- | --- | --- | --- | --- |
| Name | `nombre` | `nombre_usuario` | Yes | Text input |
| Email | `email` | `correo` | Yes | Valid email format |
| Phone | `telefono` | `telefono` | No | Tel input |
| Profession | `profesion` | `profesion` | No | Text input |
| Biography | `bio` | `bio` | No | Textarea, 4 rows |
| Profile Image | `profile-image` | `imagen` | No | File upload, image/* |

### Data Flow for Personal Information Updates

```

```

### Profile Image Preview

The system provides real-time image preview using JavaScript before uploading:

```

```

The preview updates both the profile sidebar image and the header avatar image in real-time.

**Sources:** [src/frontend/panel/panel-usuario.php L357-L425](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L357-L425)

 [src/frontend/panel/panel-usuario.php L564-L573](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L564-L573)

 [src/backend/perfil/update.php L87-L166](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/update.php#L87-L166)

---

## Password Management

The security tab (`seguridad`) provides password change functionality with comprehensive validation requirements.

### Password Requirements

The system enforces the following password security rules:

* **At least 1 uppercase letter** (A-Z)
* **At least 3 numeric digits** (0-9)
* **At least 1 special character** (@, #, $, %, etc.)

### Password Change Flow

```

```

### Password Strength Indicator

The interface provides real-time visual feedback as users type their new password:

**Checklist Display:**

* ✖ Mayúscula: Falta → ✔ Mayúscula: Cumple
* ✖ 3 números: Falta (0/3) → ✔ 3 números: Cumple
* ✖ Carácter especial: Falta → ✔ Carácter especial: Cumple

**Strength Meter:**

* 0%: Weak
* 33%: One requirement met
* 66%: Two requirements met
* 100%: All requirements met (Strong)

### Server-side Validation Logic

The backend performs comprehensive validation before accepting password changes:

```

```

**Sources:** [src/frontend/panel/panel-usuario.php L426-L468](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L426-L468)

 [src/frontend/panel/panel-usuario.php L575-L660](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L575-L660)

 [src/backend/perfil/update.php L23-L85](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/update.php#L23-L85)

---

## Notification Preferences

The notifications tab (`notificaciones`) allows users to configure their notification settings for both email and in-platform notifications.

### Configuration Options

| Category | Setting | Type |
| --- | --- | --- |
| **Email Notifications** |  |  |
|  | New resources in categories of interest | Toggle switch |
|  | Comments on user's contributions | Toggle switch |
|  | Direct messages | Toggle switch |
|  | Upcoming events and webinars | Toggle switch |
| **Platform Notifications** |  |  |
|  | Real-time notifications while using platform | Toggle switch |
|  | Notification sounds | Toggle switch |

### UI Implementation

The notification preferences use toggle switches for each setting:

```

```

The form includes action buttons:

* **Guardar configuración**: Save current preferences
* **Restaurar valores predeterminados**: Reset to defaults

**Note:** As of the current implementation, the notification preferences form does not have a backend handler. The form structure exists but requires backend implementation to persist settings.

**Sources:** [src/frontend/panel/panel-usuario.php L469-L548](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L469-L548)

---

## Backend Processing

The [src/backend/perfil/update.php](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/update.php)

 file handles all profile update requests using action-based routing.

### Request Routing

```

```

### Database Operations

The backend uses PDO prepared statements for all database operations:

**Update Personal Data:**

```

```

**Update Password:**

```

```

**Sources:** [src/backend/perfil/update.php L1-L173](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/update.php#L1-L173)

---

## Profile Image Management

Profile images are stored in the file system and referenced in the database with relative paths.

### Image Upload Process

```

```

### Storage Structure

**File System:**

```
src/backend/perfil/uploads/
├── profile_6807f3b3839528.57825759.jpg
├── profile_6807f9a21b8730.38527839.jpg
└── [other profile images]
```

**Database Storage:**

* Path stored: `"uploads/profile_6807f3b3839528.57825759.jpg"`
* Full path constructed: `"../../backend/perfil/" . $usuario['imagen']`

### Image Display

Profile images appear in multiple locations:

1. **Header avatar** (lines 114-118):

```

```

1. **Profile sidebar** (line 377):

```

```

The `?v=" . time()` query parameter ensures cache-busting when images are updated.

**Sources:** [src/backend/perfil/update.php L102-L142](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/update.php#L102-L142)

 [src/frontend/panel/panel-usuario.php L35-L379](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L35-L379)

---

## Security Considerations

### Authentication and Authorization

All profile management operations require an active user session:

```

```

**Key security measures:**

1. **Session Validation** - Every request checks for `$_SESSION['usuario_id']` (lines 11-14 in update.php)
2. **User Isolation** - Updates only affect the logged-in user's data (using session user_id)
3. **No Cache Headers** - Profile page sets cache control headers to prevent sensitive data caching (lines 4-7 in panel-usuario.php)

### Input Validation

The system implements multi-layer validation:

**Client-side (JavaScript):**

* Password matching check (lines 579-582)
* Regex validation for uppercase, numbers, special characters (lines 584-595)
* Real-time feedback during input (lines 609-640)

**Server-side (PHP):**

* Email format validation using `filter_var()` (lines 96-100)
* Password requirements using `preg_match()` (lines 56-70)
* File extension whitelist (lines 110-116)
* File size limits (5MB maximum, lines 118-122)
* Current password verification (lines 42-46)

### Password Security

**Hashing Algorithm:**

* Uses PHP's `password_hash()` with `PASSWORD_DEFAULT` algorithm (line 73)
* Uses `password_verify()` for authentication (line 42)
* Passwords are never stored in plain text
* Hashed passwords stored in `usuarios.contrasena` column

### File Upload Security

**Validation Steps:**

1. Check upload error status (`UPLOAD_ERR_OK`)
2. Validate file extension against whitelist
3. Enforce file size limits (5MB)
4. Generate unique filenames to prevent conflicts
5. Move to controlled directory (`uploads/`)

**Sources:** [src/backend/perfil/update.php L11-L122](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/update.php#L11-L122)

 [src/frontend/panel/panel-usuario.php L4-L640](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L4-L640)

---

## Error Handling and User Feedback

The system uses session-based messaging for user feedback:

### Error Messages

Errors are stored in `$_SESSION['error_message']` and displayed on the profile page:

```

```

### Success Messages

Success messages use `$_SESSION['success_message']` with the same display pattern (lines 362-367).

After displaying, messages are immediately unset to prevent showing on subsequent page loads.

### JavaScript Alerts

For immediate feedback, the backend uses JavaScript alerts:

```

```

This pattern appears for both successful password changes (line 84) and successful data updates (line 165).

**Sources:** [src/frontend/panel/panel-usuario.php L362-L373](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/frontend/panel/panel-usuario.php#L362-L373)

 [src/backend/perfil/update.php L36-L165](https://github.com/axchisan/El-rincon-de-ADSO/blob/3e310227/src/backend/perfil/update.php#L36-L165)
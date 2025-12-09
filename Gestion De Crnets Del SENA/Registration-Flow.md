# Registration Flow

> **Relevant source files**
> * [lib/models/models.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart)
> * [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart)
> * [lib/screens/registration_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart)
> * [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)
> * [macos/Flutter/GeneratedPluginRegistrant.swift](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/macos/Flutter/GeneratedPluginRegistrant.swift)

## Purpose and Scope

This document provides a detailed technical guide to the user registration process in the SENA Digital ID Card application. The registration flow implements a two-phase validation system that ensures only authorized SENA apprentices can create accounts.

This document covers:

* Two-phase ID validation and profile creation process
* Data collection and validation requirements
* Password hashing and security measures
* Database persistence and synchronization

For information about authenticating existing users, see [Login Flow](/axchisan/AppGestionCarnetsSENA/4.1-login-flow). For session creation and management after registration, see [Session Management](/axchisan/AppGestionCarnetsSENA/4.3-session-management). For security implementation details including password hashing algorithms, see [Security Implementation](/axchisan/AppGestionCarnetsSENA/4.4-security-implementation).

---

## Overview

The registration flow is implemented in [lib/screens/registration_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart)

 and orchestrates user account creation through the [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)

 The process enforces a two-phase security pattern: Phase 1 validates that the identification number exists in the SENA institutional database, and Phase 2 allows profile completion only if Phase 1 succeeds. This prevents unauthorized account creation.

**Key Characteristics**:

* **Two-phase validation**: ID validation precedes profile data collection
* **Internet required**: Both validation and registration require active connection
* **Password security**: SHA-256 hashing before storage
* **Hybrid persistence**: Immediate local Hive storage with PostgreSQL synchronization
* **Optional fields**: Blood type and profile photo are optional; all other fields required

**Sources**: [lib/screens/registration_screen.dart L1-L491](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L1-L491)

 [lib/services/database_service.dart L21-L40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L40)

---

## Registration Flow Architecture

```

```

**Diagram: Complete registration state machine showing two-phase validation and data flow**

This state machine illustrates the complete registration process. The `_isIdValid` boolean flag in `_RegistrationScreenState` controls the transition from Phase 1 to Phase 2. The UI uses `AbsorbPointer` and `AnimatedOpacity` widgets to visually disable form fields until ID validation succeeds.

**Sources**: [lib/screens/registration_screen.dart L20-L203](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L20-L203)

 [lib/screens/registration_screen.dart L79-L117](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L79-L117)

 [lib/screens/registration_screen.dart L125-L203](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L125-L203)

---

## Phase 1: Identification Validation

### Validation Process

Phase 1 validates that the user's identification number exists in the authoritative SENA database. This is implemented through the `_validateIdentification()` method in [lib/screens/registration_screen.dart L79-L117](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L79-L117)

```

```

**Diagram: Sequence diagram showing ID validation interactions**

**Sources**: [lib/screens/registration_screen.dart L79-L117](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L79-L117)

 [lib/services/database_service.dart L21-L40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L40)

 [lib/screens/registration_screen.dart L70-L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L70-L77)

### Validation Components

| Component | Type | Purpose | Location |
| --- | --- | --- | --- |
| `_identificationController` | `TextEditingController` | Captures ID input | [lib/screens/registration_screen.dart L22](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L22-L22) |
| `_isValidatingId` | `bool` | Loading state during validation | [lib/screens/registration_screen.dart L32](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L32-L32) |
| `_isIdValid` | `bool` | Flag controlling Phase 2 access | [lib/screens/registration_screen.dart L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L33-L33) |
| `validateIdentification()` | Method | Queries PostgreSQL for ID existence | [lib/services/database_service.dart L21-L40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L40) |
| `_checkInternetConnection()` | Method | Verifies connectivity to google.com | [lib/screens/registration_screen.dart L70-L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L70-L77) |

**Sources**: [lib/screens/registration_screen.dart L20-L34](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L20-L34)

### UI State Management

The form fields in Phase 2 are wrapped in an `AnimatedOpacity` widget at [lib/screens/registration_screen.dart L295-L296](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L295-L296)

 with opacity `_isIdValid ? 1.0 : 0.5`, creating a visual disabled state. An `AbsorbPointer` widget at [lib/screens/registration_screen.dart L298-L299](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L298-L299)

 with `absorbing: !_isIdValid` prevents user interaction until validation succeeds.

When validation succeeds, a success indicator is displayed at [lib/screens/registration_screen.dart L264-L294](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L264-L294)

:

* Green background container
* Check circle icon
* "Número de identificación válido" message

**Sources**: [lib/screens/registration_screen.dart L264-L294](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L264-L294)

 [lib/screens/registration_screen.dart L295-L483](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L295-L483)

---

## Phase 2: Profile Data Collection

### Required Fields

Once `_isIdValid` is true, the user completes the registration profile. The form uses a `GlobalKey<FormState>` at [lib/screens/registration_screen.dart L21](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L21-L21)

 to manage validation.

| Field | Controller | Validation | Required |
| --- | --- | --- | --- |
| Identification Number | `_identificationController` | Minimum 5 digits | Yes |
| Full Name | `_nameController` | Non-empty | Yes |
| Email | `_emailController` | Non-empty, contains '@' | Yes |
| Program | `_selectedProgram` dropdown | Must select from list | Yes |
| Ficha Number | `_fichaController` | Non-empty, numeric | Yes |
| Blood Type | `_tipoSangreController` | None | No |
| Profile Photo | `_fotoPerfilPath` | None | No |
| Password | `_passwordController` | Minimum 6 characters | Yes |
| Confirm Password | `_confirmPasswordController` | Must match password | Yes |

**Sources**: [lib/screens/registration_screen.dart L22-L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L22-L28)

 [lib/screens/registration_screen.dart L241-L457](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L241-L457)

### Program Selection

The program dropdown at [lib/screens/registration_screen.dart L331-L376](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L331-L376)

 offers 8 predefined SENA programs:

```

```

The selected value is stored in `_selectedProgram` state variable. The form submission at [lib/screens/registration_screen.dart L138-L146](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L138-L146)

 explicitly checks that a program is selected before proceeding.

**Sources**: [lib/screens/registration_screen.dart L36-L45](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L36-L45)

 [lib/screens/registration_screen.dart L331-L386](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L331-L386)

### Profile Photo Upload

Profile photo upload uses the `image_picker` package. The `_pickImage()` method at [lib/screens/registration_screen.dart L61-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L61-L68)

 opens the device gallery:

```

```

**Diagram: Profile photo selection flow**

The selected image path is stored locally. When syncing to PostgreSQL, the image is base64-encoded in [lib/services/database_service.dart L60-L62](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L60-L62)

:

```

```

**Sources**: [lib/screens/registration_screen.dart L61-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L61-L68)

 [lib/screens/registration_screen.dart L407-L425](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L407-L425)

 [lib/services/database_service.dart L60-L62](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L60-L62)

---

## Data Persistence and Synchronization

### Aprendiz Object Creation

After form validation passes, the registration handler creates an `Aprendiz` object at [lib/screens/registration_screen.dart L163-L174](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L163-L174)

:

```

```

**Diagram: Aprendiz object construction from form data**

The `Aprendiz` model is defined in [lib/models/models.dart L6-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L6-L68)

 with Hive annotations for local persistence. All fields are immutable (final).

**Sources**: [lib/screens/registration_screen.dart L163-L174](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L163-L174)

 [lib/models/models.dart L6-L68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart#L6-L68)

### Password Hashing

The `_hashPassword()` method at [lib/screens/registration_screen.dart L119-L123](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L119-L123)

 implements SHA-256 hashing:

```

```

This uses the `crypto` package imported at [lib/screens/registration_screen.dart L4](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L4-L4)

 The plain-text password never leaves the client device - only the hash is stored and transmitted.

**Sources**: [lib/screens/registration_screen.dart L4](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L4-L4)

 [lib/screens/registration_screen.dart L119-L123](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L119-L123)

 [lib/screens/registration_screen.dart L162](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L162-L162)

### Database Save Operation

The `DatabaseService.saveAprendiz()` method at [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

 executes a two-step persistence:

```

```

**Diagram: Save and sync sequence for new user registration**

The `_syncToPostgres()` method at [lib/services/database_service.dart L49-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L49-L119)

 handles the PostgreSQL synchronization:

1. Opens connection to PostgreSQL using environment variables from `.env`
2. Base64-encodes the profile photo if present
3. Executes transaction with upsert query (INSERT ... ON CONFLICT DO UPDATE)
4. Deletes existing devices (none for new users)
5. Inserts devices (empty list for new users)

**Sources**: [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

 [lib/services/database_service.dart L49-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L49-L119)

 [lib/screens/registration_screen.dart L177-L189](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L177-L189)

### PostgreSQL Schema Interaction

The registration process interacts with two PostgreSQL tables:

**aprendices table** (upsert at [lib/services/database_service.dart L65-L93](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L65-L93)

):

* `id_identificacion` (PRIMARY KEY)
* `nombre_completo`
* `programa_formacion`
* `numero_ficha`
* `tipo_sangre`
* `foto_perfil` (base64-encoded image)
* `contrasena` (SHA-256 hash)
* `email`
* `fecha_registro`

**dispositivos table** (empty insert for new users at [lib/services/database_service.dart L95-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L95-L114)

):

* `id_dispositivo`
* `id_identificacion` (FOREIGN KEY)
* `nombre_dispositivo`
* `tipo_dispositivo`
* `fecha_registro`

**Sources**: [lib/services/database_service.dart L65-L114](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L65-L114)

---

## Error Handling and User Feedback

### Error States

The registration flow handles multiple error conditions:

| Error Condition | Detection Point | User Feedback | Recovery Action |
| --- | --- | --- | --- |
| Empty ID field | Form validation | "Ingresa un número de identificación" | User re-enters ID |
| ID too short (< 5 digits) | Form validator | "El número debe tener al menos 5 dígitos" | User corrects ID |
| No internet (validation) | `_checkInternetConnection()` | Red SnackBar: "No hay conexión a Internet" | User retries after connection |
| ID not found in SENA | `validateIdentification()` returns false | Red SnackBar: "Número no registrado en el SENA" | User enters different ID |
| Missing required field | Form validators | Red text under field | User fills field |
| Invalid email format | Email validator | "Ingresa un correo válido" | User corrects email |
| No program selected | Pre-submit check | Red SnackBar: "Selecciona un programa" | User selects program |
| Password too short (< 6) | Password validator | "La contraseña debe tener al menos 6 caracteres" | User enters longer password |
| Passwords don't match | Confirm password validator | "Las contraseñas no coinciden" | User re-enters confirmation |
| No internet (registration) | `_checkInternetConnection()` | Red SnackBar: "No hay conexión a Internet" | User retries after connection |
| Database save failure | `saveAprendiz()` exception | "Error al registrar. Verifica tu conexión" | User retries |

**Sources**: [lib/screens/registration_screen.dart L79-L117](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L79-L117)

 [lib/screens/registration_screen.dart L125-L203](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L125-L203)

 [lib/screens/registration_screen.dart L241-L457](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L241-L457)

### Loading States

Two loading indicators provide feedback during async operations:

**ID Validation Loading** ([lib/screens/registration_screen.dart L32](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L32-L32)

):

* State variable: `_isValidatingId`
* Set to `true` at [lib/screens/registration_screen.dart L100-L102](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L100-L102)
* Set to `false` at [lib/screens/registration_screen.dart L106-L109](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L106-L109)
* Controls loading indicator on "Validar Identificación" button

**Registration Loading** ([lib/screens/registration_screen.dart L31](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L31-L31)

):

* State variable: `_isLoading`
* Set to `true` at [lib/screens/registration_screen.dart L158-L160](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L158-L160)
* Set to `false` at [lib/screens/registration_screen.dart L179-L181](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L179-L181)  or [lib/screens/registration_screen.dart L192-L194](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L192-L194)
* Controls loading indicator on "Registrarse" button

**Sources**: [lib/screens/registration_screen.dart L31-L32](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L31-L32)

 [lib/screens/registration_screen.dart L100-L109](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L100-L109)

 [lib/screens/registration_screen.dart L158-L194](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L158-L194)

---

## Navigation After Registration

Upon successful registration, the user is immediately logged in and navigated to the home screen at [lib/screens/registration_screen.dart L188](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L188-L188)

:

```

```

This uses `pushReplacementNamed` to:

* Replace the registration screen in the navigation stack (prevents back navigation)
* Navigate to the `/inicio` route (HomeScreen)
* Pass the newly created `Aprendiz` object as arguments

The `HomeScreen` receives this object through its route arguments and displays the user's profile. See [Home Screen](/axchisan/AppGestionCarnetsSENA/5.1-home-screen) for details on how the home screen processes this data.

The local Hive storage now contains the user's data, so future app launches will detect an active session and skip the login screen. See [Session Management](/axchisan/AppGestionCarnetsSENA/4.3-session-management) for session detection logic.

**Sources**: [lib/screens/registration_screen.dart L188](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L188-L188)

---

## Component Dependencies

```

```

**Diagram: Component dependency graph for registration flow**

**Sources**: [lib/screens/registration_screen.dart L1-L11](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L1-L11)

 [lib/screens/registration_screen.dart L20-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L20-L47)

---

## Key Code Locations

| Component | File Path | Line Numbers |
| --- | --- | --- |
| RegistrationScreen class | lib/screens/registration_screen.dart | [13-18](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/13-18) |
| State class | lib/screens/registration_screen.dart | [20-491](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/20-491) |
| ID validation method | lib/screens/registration_screen.dart | [79-117](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/79-117) |
| Registration handler | lib/screens/registration_screen.dart | [125-203](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/125-203) |
| Password hashing | lib/screens/registration_screen.dart | [119-123](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/119-123) |
| Photo picker | lib/screens/registration_screen.dart | [61-68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/61-68) |
| Form UI build | lib/screens/registration_screen.dart | [206-490](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/206-490) |
| validateIdentification | lib/services/database_service.dart | [21-40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/21-40) |
| saveAprendiz | lib/services/database_service.dart | [42-47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/42-47) |
| _syncToPostgres | lib/services/database_service.dart | [49-119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/49-119) |
| Aprendiz model | lib/models/models.dart | [6-68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/6-68) |
| Aprendiz adapter | lib/models/models.g.dart | [9-68](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/9-68) |

**Sources**: [lib/screens/registration_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart)

 [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)

 [lib/models/models.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.dart)

 [lib/models/models.g.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/models/models.g.dart)
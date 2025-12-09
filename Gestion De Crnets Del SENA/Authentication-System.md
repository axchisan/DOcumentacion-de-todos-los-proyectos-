# Authentication System

> **Relevant source files**
> * [lib/screens/login_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart)
> * [lib/screens/registration_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart)
> * [lib/screens/splash_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart)
> * [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)

## Purpose and Scope

This document provides a comprehensive overview of the authentication system in the SENA Digital ID Card application. It covers the complete authentication lifecycle including user login, account registration, session management, and security implementation.

The authentication system manages two primary user journeys:

* **Existing users**: Login via credential verification against PostgreSQL
* **New users**: Two-phase registration with ID validation followed by profile creation

For detailed information about specific subsections:

* Login process details: see [Login Flow](/axchisan/AppGestionCarnetsSENA/4.1-login-flow)
* Registration process details: see [Registration Flow](/axchisan/AppGestionCarnetsSENA/4.2-registration-flow)
* Session persistence and detection: see [Session Management](/axchisan/AppGestionCarnetsSENA/4.3-session-management)
* Password hashing and credentials: see [Security Implementation](/axchisan/AppGestionCarnetsSENA/4.4-security-implementation)

Related topics covered elsewhere:

* Database operations and synchronization: see [Data Persistence Layer](/axchisan/AppGestionCarnetsSENA/3.1-data-persistence-layer)
* Navigation after authentication: see [Screen Navigation and Routing](/axchisan/AppGestionCarnetsSENA/3.2-screen-navigation-and-routing)

---

## Architecture Overview

The authentication system is built on a three-tier architecture:

| Layer | Components | Responsibility |
| --- | --- | --- |
| **Presentation** | `LoginScreen`, `RegistrationScreen` | User input collection, form validation, UI state management |
| **Service** | `DatabaseService` | Authentication logic, credential verification, data persistence |
| **Storage** | Hive (local), PostgreSQL (remote) | Session storage, user credential verification |

The system enforces an **online-first authentication model** - initial login and registration require internet connectivity to verify credentials against PostgreSQL. Once authenticated, user data is cached in Hive for offline session detection.

Sources: [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

 [lib/screens/login_screen.dart L1-L223](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L1-L223)

 [lib/screens/registration_screen.dart L1-L491](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L1-L491)

---

## Authentication Components

### Core Service Methods

The `DatabaseService` class provides the authentication API:

```python
DatabaseService
├── validateIdentification(String id) → Future<bool>
│   └── Validates ID exists in PostgreSQL aprendices table
├── getAprendizFromPostgres(String id, String password) → Future<Aprendiz?>
│   └── Authenticates user and retrieves full profile
├── saveAprendiz(Aprendiz aprendiz) → Future<void>
│   └── Persists user to Hive and syncs to PostgreSQL
└── getAprendizFromLocal(String id) → Future<Aprendiz?>
    └── Retrieves cached session from Hive
```

**Key Implementation Details:**

* `validateIdentification`: [lib/services/database_service.dart L21-L40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L40)  - Queries PostgreSQL to verify ID exists in `aprendices` table
* `getAprendizFromPostgres`: [lib/services/database_service.dart L129-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L129-L169)  - Authenticates with hashed password, returns `Aprendiz` if valid, caches to Hive
* `saveAprendiz`: [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)  - Writes to Hive box `aprendicesBox`, triggers `_syncToPostgres`
* `getAprendizFromLocal`: [lib/services/database_service.dart L121-L127](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L121-L127)  - Reads from `aprendicesBox` Hive box

Sources: [lib/services/database_service.dart L9-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L9-L238)

---

## Authentication Flow Diagram

```

```

**Flow Summary:**

1. **Session Detection**: `SplashScreen._navigateToHome()` checks `aprendicesBox` Hive box [lib/screens/splash_screen.dart L21-L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L21-L33)
2. **Login**: User credentials hashed via SHA-256, verified against PostgreSQL, cached on success [lib/screens/login_screen.dart L49-L92](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L49-L92)
3. **Registration**: Two-phase process - ID validation first, then profile completion [lib/screens/registration_screen.dart L79-L203](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L79-L203)

Sources: [lib/screens/splash_screen.dart L1-L90](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L1-L90)

 [lib/screens/login_screen.dart L1-L223](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L1-L223)

 [lib/screens/registration_screen.dart L1-L491](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L1-L491)

---

## Component Interaction Map

```

```

**Key Interactions:**

* **LoginScreen** → `DatabaseService.getAprendizFromPostgres()` [lib/screens/login_screen.dart L69](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L69-L69)
* **RegistrationScreen** → `DatabaseService.validateIdentification()` [lib/screens/registration_screen.dart L104](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L104-L104)
* **RegistrationScreen** → `DatabaseService.saveAprendiz()` [lib/screens/registration_screen.dart L177](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L177-L177)
* **SplashScreen** → `Hive.box<Aprendiz>('aprendicesBox')` [lib/screens/splash_screen.dart L23](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L23)
* **DatabaseService** → `PostgreSQLConnection` using dotenv credentials [lib/services/database_service.dart L13-L17](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L13-L17)

Sources: [lib/screens/login_screen.dart L1-L223](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L1-L223)

 [lib/screens/registration_screen.dart L1-L491](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L1-L491)

 [lib/screens/splash_screen.dart L1-L90](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L1-L90)

 [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

---

## Security Features

### Password Hashing

Both `LoginScreen` and `RegistrationScreen` implement identical password hashing:

**Implementation:** [lib/screens/login_screen.dart L34-L38](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L34-L38)

 [lib/screens/registration_screen.dart L119-L123](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L119-L123)

```

```

* **Algorithm**: SHA-256 via `crypto` package
* **Timing**: Client-side hashing before transmission
* **Storage**: Only hashed passwords stored in PostgreSQL `aprendices.contrasena` column

### Environment-Based Credentials

Database connection credentials are never hardcoded. The `DatabaseService` loads credentials from environment variables:

| Variable | Purpose | Default Fallback |
| --- | --- | --- |
| `POSTGRES_HOST` | Database server address | `default_host` |
| `POSTGRES_PORT` | Database server port | `5432` |
| `POSTGRES_DATABASE` | Database name | `default_db` |
| `POSTGRES_USERNAME` | Database user | `default_user` |
| `POSTGRES_PASSWORD` | Database password | `default_password` |

**Implementation:** [lib/services/database_service.dart L13-L17](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L13-L17)

Sources: [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

 [lib/screens/login_screen.dart L34-L38](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L34-L38)

 [lib/screens/registration_screen.dart L119-L123](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L119-L123)

### Internet Connectivity Validation

Both authentication screens verify network availability before remote operations:

**Implementation:** [lib/screens/login_screen.dart L40-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L40-L47)

 [lib/screens/registration_screen.dart L70-L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L70-L77)

```

```

Used before:

* Login credential verification [lib/screens/login_screen.dart L52-L60](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L52-L60)
* Registration ID validation [lib/screens/registration_screen.dart L90-L98](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L90-L98)
* Registration form submission [lib/screens/registration_screen.dart L148-L156](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L148-L156)

Sources: [lib/screens/login_screen.dart L40-L60](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L40-L60)

 [lib/screens/registration_screen.dart L70-L156](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L70-L156)

---

## Authentication Data Flow

```

```

**Key Operations:**

1. **Password Processing**: Always hashed client-side before DatabaseService receives it
2. **Device Loading**: Authentication automatically loads associated devices via `_loadDevicesFromPostgres` [lib/services/database_service.dart L171-L197](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L171-L197)
3. **Cache-Then-Sync**: Successful authentication writes to Hive immediately, syncs to PostgreSQL asynchronously
4. **Navigation**: Both flows use `Navigator.pushReplacementNamed('/inicio', arguments: aprendiz)` to prevent back navigation

Sources: [lib/services/database_service.dart L21-L197](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L197)

 [lib/screens/login_screen.dart L49-L92](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L49-L92)

 [lib/screens/registration_screen.dart L79-L203](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L79-L203)

---

## Form Validation Rules

### Login Screen Validation

| Field | Rule | Error Message |
| --- | --- | --- |
| Identification | Non-empty | "Por favor ingresa tu número de identificación" |
| Identification | Minimum 5 digits | "El número de identificación debe tener al menos 5 dígitos" |
| Password | Non-empty | "Por favor ingresa tu contraseña" |

Implementation: [lib/screens/login_screen.dart L134-L156](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L134-L156)

### Registration Screen Validation

| Field | Rule | Error Message |
| --- | --- | --- |
| Identification | Non-empty | "Por favor ingresa tu número de identificación" |
| Identification | Minimum 5 digits | "El número debe tener al menos 5 dígitos" |
| Identification | Must exist in SENA database | "Número no registrado en el SENA" |
| Name | Non-empty | "Por favor ingresa tu nombre completo" |
| Email | Non-empty | "Por favor ingresa tu correo electrónico" |
| Email | Contains @ | "Ingresa un correo válido" |
| Program | Selection required | "Selecciona un programa de formación" |
| Ficha | Non-empty | "Por favor ingresa el número de ficha" |
| Password | Non-empty | "Por favor ingresa una contraseña" |
| Password | Minimum 6 characters | "La contraseña debe tener al menos 6 caracteres" |
| Confirm Password | Matches password | "Las contraseñas no coinciden" |

Implementation: [lib/screens/registration_screen.dart L241-L457](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L241-L457)

Sources: [lib/screens/login_screen.dart L129-L156](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L129-L156)

 [lib/screens/registration_screen.dart L241-L457](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L241-L457)

---

## State Management During Authentication

Both authentication screens use local state management with loading indicators:

| Screen | State Variables | Purpose |
| --- | --- | --- |
| `LoginScreen` | `_isLoading` | Disables login button, shows progress during authentication |
| `RegistrationScreen` | `_isLoading` | Disables registration button during final submission |
| `RegistrationScreen` | `_isValidatingId` | Shows progress during Phase 1 ID validation |
| `RegistrationScreen` | `_isIdValid` | Enables/disables Phase 2 form fields |

**Loading States:**

* **Login**: Set to `true` during `getAprendizFromPostgres` call [lib/screens/login_screen.dart L62-L64](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L62-L64)
* **Registration Phase 1**: Set to `true` during `validateIdentification` call [lib/screens/registration_screen.dart L100-L102](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L100-L102)
* **Registration Phase 2**: Set to `true` during `saveAprendiz` call [lib/screens/registration_screen.dart L158-L160](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L158-L160)

**Form Disabling:**

Registration form uses `AnimatedOpacity` and `AbsorbPointer` to visually and functionally disable Phase 2 until `_isIdValid == true` [lib/screens/registration_screen.dart L295-L299](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L295-L299)

Sources: [lib/screens/login_screen.dart L19-L92](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L19-L92)

 [lib/screens/registration_screen.dart L20-L203](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L20-L203)

---

## Error Handling

### Network Errors

Both screens check internet connectivity before remote operations and display user-friendly error messages via `SnackBar`:

**No Internet:**

```

```

Implementation: [lib/screens/login_screen.dart L53-L59](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L53-L59)

 [lib/screens/registration_screen.dart L90-L98](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L90-L98)

### Authentication Errors

**Invalid Login Credentials:** [lib/screens/login_screen.dart L84-L90](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L84-L90)

```

```

**Invalid Registration ID:** [lib/screens/registration_screen.dart L110-L116](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L110-L116)

```

```

### Silent Failures

The `DatabaseService` catches all exceptions and returns safe defaults:

* `validateIdentification`: Returns `false` on any error [lib/services/database_service.dart L37-L39](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L37-L39)
* `getAprendizFromPostgres`: Returns `null` on any error [lib/services/database_service.dart L166-L168](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L166-L168)
* `saveAprendiz`: Silently fails, no error propagated [lib/services/database_service.dart L46](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L46-L46)

This prevents crashes but may mask underlying issues. Error logging would be recommended for production.

Sources: [lib/services/database_service.dart L21-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L238)

 [lib/screens/login_screen.dart L49-L92](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L49-L92)

 [lib/screens/registration_screen.dart L79-L203](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L79-L203)

---

## Session Lifecycle

```

```

**Session Creation**: Occurs when `DatabaseService.saveAprendiz()` writes to Hive box [lib/services/database_service.dart L44](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L44-L44)

**Session Detection**: Occurs in `SplashScreen._navigateToHome()` which checks if `aprendicesBox` contains values [lib/screens/splash_screen.dart L23-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L24)

**Session Termination**: User logout clears the Hive box, redirecting to splash screen (implementation varies by screen)

Sources: [lib/screens/splash_screen.dart L1-L90](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L1-L90)

 [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

---

## PostgreSQL Schema Requirements

The authentication system expects the following PostgreSQL tables:

### aprendices Table

```

```

### dispositivos Table

```

```

**Query Operations:**

* **Login**: `SELECT * FROM aprendices WHERE id_identificacion = @id AND contrasena = @password` [lib/services/database_service.dart L139-L146](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L139-L146)
* **ID Validation**: `SELECT id_identificacion FROM aprendices WHERE id_identificacion = @id` [lib/services/database_service.dart L31-L34](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L31-L34)
* **Device Loading**: `SELECT * FROM dispositivos WHERE id_identificacion = @id` [lib/services/database_service.dart L181-L184](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L181-L184)
* **User Sync**: `INSERT ... ON CONFLICT (id_identificacion) DO UPDATE SET ...` [lib/services/database_service.dart L65-L93](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L65-L93)

Sources: [lib/services/database_service.dart L21-L197](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L21-L197)

---

## Integration Points

### Hive Integration

**Box Name**: `'aprendicesBox'` constant defined in `DatabaseService._hiveBoxName` [lib/services/database_service.dart L10](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L10-L10)

**Type Adapter**: `Aprendiz` model must have Hive adapter registered in `main.dart` before authentication screens can function

**Access Pattern**: Service layer accesses via `Hive.box<Aprendiz>(_hiveBoxName)` getter [lib/services/database_service.dart L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L19-L19)

### Navigation Integration

Authentication screens use named routes:

| Route | Screen | Triggered By |
| --- | --- | --- |
| `/login` | `LoginScreen` | Session not found, user clicks "Create Account" |
| `/registro` | `RegistrationScreen` | User clicks "Crear Nueva Cuenta" from login |
| `/inicio` | `HomeScreen` | Successful login or registration |
| `/splash` | `SplashScreen` | User logout |

**Data Passing**: `Aprendiz` object passed via `arguments` parameter in `Navigator.pushReplacementNamed()` [lib/screens/login_screen.dart L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L77-L77)

Sources: [lib/screens/login_screen.dart L77-L92](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L77-L92)

 [lib/screens/registration_screen.dart L188-L189](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L188-L189)

 [lib/screens/splash_screen.dart L28-L31](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L28-L31)

---

## Testing Considerations

When testing the authentication system, consider:

1. **Network Mocking**: `InternetAddress.lookup('google.com')` calls should be mocked for offline tests
2. **PostgreSQL State**: Tests require seeded database with known test accounts
3. **Hive Box State**: Clear `aprendicesBox` between test runs to ensure clean session state
4. **Password Hashing**: Test accounts must have SHA-256 hashed passwords in database
5. **Environment Variables**: Ensure `.env` is properly configured with test database credentials

**Test Scenarios:**

* Login with valid credentials
* Login with invalid credentials (wrong password)
* Login with non-existent ID
* Registration with valid SENA ID
* Registration with invalid SENA ID
* Registration with duplicate ID (already registered)
* Session detection on app relaunch
* Network failure during authentication
* Form validation edge cases

Sources: [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

 [lib/screens/login_screen.dart L1-L223](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L1-L223)

 [lib/screens/registration_screen.dart L1-L491](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L1-L491)
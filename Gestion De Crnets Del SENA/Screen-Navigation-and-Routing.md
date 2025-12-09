# Screen Navigation and Routing

> **Relevant source files**
> * [lib/main.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart)
> * [lib/screens/home_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart)
> * [lib/screens/login_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart)
> * [lib/screens/splash_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart)

## Purpose and Scope

This document describes the navigation and routing architecture of the SENA Digital ID Card application. It covers the named route structure, navigation patterns, data passing mechanisms between screens, and session-based routing logic.

For information about individual screen implementations and their UI components, see [Core Features](/axchisan/AppGestionCarnetsSENA/5-core-features). For authentication flow specifics, see [Authentication System](/axchisan/AppGestionCarnetsSENA/4-authentication-system). For the broader application architecture context, see [System Architecture](/axchisan/AppGestionCarnetsSENA/3-system-architecture).

---

## Route Definition Architecture

The application uses Flutter's built-in named route system, with all routes centrally defined in the `MaterialApp` widget. Route definitions handle both simple navigation and argument-based navigation for screens that require user data.

### Route Registry

All application routes are registered in [lib/main.dart L46-L71](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L46-L71)

 The following table summarizes the route structure:

| Route Name | Target Screen | Requires Arguments | Purpose |
| --- | --- | --- | --- |
| `/splash` | `SplashScreen` | No | Initial session detection and routing |
| `/login` | `LoginScreen` | No | User authentication entry point |
| `/registro` | `RegistrationScreen` | No | New user account creation |
| `/inicio` | `HomeScreen` | Optional (`Aprendiz`) | Main dashboard and navigation hub |
| `/carnet` | `IdCardScreen` | Optional (`Aprendiz`) | Virtual ID card display |
| `/dispositivos` | `DeviceManagementScreen` | Optional (`Aprendiz`) | Device registration and management |

**Route Definition Diagram**

```

```

**Sources:** [lib/main.dart L46-L71](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L46-L71)

### Argument Extraction Pattern

Routes that accept arguments use `ModalRoute.of(context)?.settings.arguments` to extract data. The pattern checks if the argument is of the expected type (`Aprendiz`) and provides fallback behavior when arguments are missing:

```

```

This pattern is repeated for `/carnet` [lib/main.dart L57-L63](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L57-L63)

 and `/dispositivos` [lib/main.dart L64-L70](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L64-L70)

 routes, ensuring screens can handle both explicit argument passing and fallback scenarios.

**Sources:** [lib/main.dart L50-L70](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L50-L70)

---

## Navigation Flow Architecture

The application implements a hierarchical navigation pattern with the `SplashScreen` acting as the intelligent entry router and `HomeScreen` serving as the primary navigation hub for authenticated users.

### Complete Navigation State Machine

```

```

**Sources:** [lib/screens/splash_screen.dart L21-L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L21-L33)

 [lib/main.dart L45-L71](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L45-L71)

 [lib/screens/home_screen.dart L18-L257](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L18-L257)

 [lib/screens/login_screen.dart L77-L185](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L77-L185)

---

## Navigation Methods and Patterns

The application uses two primary navigation methods with distinct purposes: `Navigator.pushNamed()` for standard forward navigation and `Navigator.pushReplacementNamed()` for navigation that should clear the back stack.

### Navigation Method Usage

| Method | Usage Pattern | Back Behavior | Use Cases |
| --- | --- | --- | --- |
| `pushNamed()` | Standard forward navigation | Returns to previous screen | Feature screens (carnet, dispositivos) accessed from home |
| `pushReplacementNamed()` | Replaces current route | Cannot return to replaced screen | Authentication transitions, logout |

### Navigation Method Implementations

**Push Named (Standard Navigation)**

Used when users should be able to navigate back to the originating screen:

* **Home to ID Card:** [lib/screens/home_screen.dart L116](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L116-L116) ``` ```
* **Home to Devices:** [lib/screens/home_screen.dart L274](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L274-L274) ``` ```
* **Login to Registration:** [lib/screens/login_screen.dart L185](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L185-L185) ``` ```

**Push Replacement Named (No-Return Navigation)**

Used for transitions where returning to the previous screen would violate application flow:

* **Splash to Login (No Session):** [lib/screens/splash_screen.dart L30](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L30-L30) ``` ```
* **Splash to Home (Active Session):** [lib/screens/splash_screen.dart L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L28-L28) ``` ```
* **Login Success:** [lib/screens/login_screen.dart L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L77-L77) ``` ```
* **Logout:** [lib/screens/home_screen.dart L20](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L20-L20) ``` ```

**Sources:** [lib/screens/home_screen.dart L20-L274](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L20-L274)

 [lib/screens/splash_screen.dart L28-L30](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L28-L30)

 [lib/screens/login_screen.dart L77-L185](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L77-L185)

---

## Data Passing Between Screens

The application uses Flutter's route arguments mechanism to pass `Aprendiz` objects between screens. This pattern ensures authenticated user data is available throughout the navigation hierarchy without relying on global state.

### Argument Passing Pattern

```

```

**Sources:** [lib/main.dart L50-L70](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L50-L70)

 [lib/screens/splash_screen.dart L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L28-L28)

 [lib/screens/login_screen.dart L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L77-L77)

 [lib/screens/home_screen.dart L116-L274](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L116-L274)

### Aprendiz Argument Flow

**Passing Arguments:**

All authenticated navigation passes the `Aprendiz` object as the route argument. Examples include:

1. **From Splash (Session Exists):** [lib/screens/splash_screen.dart L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L28-L28) * Retrieves: `box.values.first` from Hive * Passes to: `/inicio` route
2. **From Login (Authentication Success):** [lib/screens/login_screen.dart L69-L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L69-L77) * Retrieves: `_dbService.getAprendizFromPostgres(id, hashedPassword)` * Saves to Hive: `_dbService.saveAprendiz(aprendiz)` * Passes to: `/inicio` route
3. **From Home to Features:** [lib/screens/home_screen.dart L116-L274](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L116-L274) * Source: `aprendiz` constructor parameter or Hive * Passes to: `/carnet` and `/dispositivos` routes

**Receiving Arguments:**

Route handlers in [lib/main.dart L50-L70](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L50-L70)

 extract arguments and pass them as constructor parameters:

```

```

**Sources:** [lib/main.dart L50-L70](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L50-L70)

 [lib/screens/splash_screen.dart L23-L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L28)

 [lib/screens/login_screen.dart L69-L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L69-L77)

 [lib/screens/home_screen.dart L15-L274](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L15-L274)

---

## Session-Based Routing

The `SplashScreen` implements session detection logic that determines the initial navigation path based on local storage state. This pattern enables automatic login for returning users while directing new users to authentication.

### Session Detection Logic

```

```

**Sources:** [lib/screens/splash_screen.dart L16-L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L16-L33)

### Session State Persistence

The session state is managed entirely through the Hive `aprendicesBox`:

**Session Creation:**

* Occurs after successful login [lib/screens/login_screen.dart L72](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L72-L72)
* `DatabaseService.saveAprendiz()` writes to Hive
* User is then navigated to `/inicio`

**Session Detection:**

* Splash screen checks: `box.values.isNotEmpty` [lib/screens/splash_screen.dart L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L24-L24)
* If present, retrieves: `box.values.first`
* Automatically navigates to home with stored `Aprendiz`

**Session Termination:**

* Logout calls: `box.clear()` [lib/screens/home_screen.dart L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L19-L19)
* Navigates to: `/splash` which will detect no session
* Next splash check routes to `/login`

**Sources:** [lib/screens/splash_screen.dart L21-L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L21-L33)

 [lib/screens/home_screen.dart L18-L21](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L18-L21)

 [lib/screens/login_screen.dart L72](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L72-L72)

---

## Bottom Navigation Integration

The `HomeScreen` implements a bottom navigation bar that provides quick access to key features. This navigation pattern uses the same route system but with special handling for the current screen.

### Bottom Navigation Structure

The bottom navigation bar is defined in [lib/screens/home_screen.dart L220-L258](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L220-L258)

 and provides three navigation options:

| Icon | Label | Route | Behavior |
| --- | --- | --- | --- |
| `Icons.credit_card` | Carnet | `/carnet` | Navigate to ID card screen |
| `Icons.devices` | Dispositivos | `/dispositivos` | Navigate to device management |
| `Icons.close` | Cerrar | N/A | Exit application via `exit(0)` |

### Bottom Navigation Implementation

```

```

**Sources:** [lib/screens/home_screen.dart L220-L300](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L220-L300)

### Bottom Navigation Helper Method

The `_buildBottomNavItem()` helper method at [lib/screens/home_screen.dart L260-L300](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L260-L300)

 encapsulates the navigation logic:

**Parameters:**

* `BuildContext context`: Required for navigation
* `IconData icon`: Icon to display
* `String label`: Text label below icon
* `String route`: Target route name
* `Aprendiz? aprendiz`: User data to pass
* `VoidCallback? onTap`: Optional custom tap handler

**Navigation Logic:**

1. If custom `onTap` is provided, use it (for exit button)
2. If `route.isEmpty`, do nothing
3. If `aprendiz != null`, navigate with argument
4. Otherwise, show error message

**Sources:** [lib/screens/home_screen.dart L260-L300](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L260-L300)

---

## Navigation Route Table Reference

### Complete Route Parameter Reference

| Route | Arguments Type | Required | Constructor Parameter | Fallback Behavior |
| --- | --- | --- | --- | --- |
| `/splash` | None | N/A | N/A | N/A |
| `/login` | None | N/A | N/A | N/A |
| `/registro` | None | N/A | N/A | N/A |
| `/inicio` | `Aprendiz` | Optional | `HomeScreen(aprendiz: args)` | Loads from Hive in widget [lib/screens/home_screen.dart L15-L16](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L15-L16) |
| `/carnet` | `Aprendiz` | Optional | `IdCardScreen(aprendiz: args)` | Accepts `null` [lib/main.dart L62](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L62-L62) |
| `/dispositivos` | `Aprendiz` | Optional | `DeviceManagementScreen(aprendiz: args)` | Shows empty state |

**Sources:** [lib/main.dart L46-L71](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L46-L71)

 [lib/screens/home_screen.dart L8-L16](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L8-L16)

### Navigation Entry Points by Screen

| Screen | Navigates To | Method | Line Reference |
| --- | --- | --- | --- |
| `SplashScreen` | `/login` or `/inicio` | `pushReplacementNamed` | [lib/screens/splash_screen.dart L28-L30](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L28-L30) |
| `LoginScreen` | `/inicio` | `pushReplacementNamed` | [lib/screens/login_screen.dart L77](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L77-L77) |
| `LoginScreen` | `/registro` | `pushNamed` | [lib/screens/login_screen.dart L185](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L185-L185) |
| `RegistrationScreen` | `/inicio` | `pushReplacementNamed` | Referenced in architecture |
| `HomeScreen` | `/carnet` | `pushNamed` | [lib/screens/home_screen.dart L116](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L116-L116) |
| `HomeScreen` | `/dispositivos` | `pushNamed` | [lib/screens/home_screen.dart L274](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L274-L274) |
| `HomeScreen` | `/splash` (logout) | `pushReplacementNamed` | [lib/screens/home_screen.dart L20](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L20-L20) |

**Sources:** [lib/screens/splash_screen.dart L28-L30](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L28-L30)

 [lib/screens/login_screen.dart L77-L185](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L77-L185)

 [lib/screens/home_screen.dart L20-L274](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L20-L274)

---

## Key Navigation Principles

### 1. Centralized Route Definition

All routes are defined in a single location [lib/main.dart L46-L71](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L46-L71)

 within the `MaterialApp.routes` map. This centralization ensures consistency and makes route changes easier to manage.

### 2. Type-Safe Argument Passing

The pattern of checking `if (args is Aprendiz)` before passing to constructors provides runtime type safety and enables graceful fallback behavior when arguments are missing or incorrect.

### 3. Replacement for Authentication Boundaries

Authentication transitions use `pushReplacementNamed` to prevent users from navigating back to login/splash screens after authentication, maintaining security boundaries.

### 4. Hub-and-Spoke Pattern

The `HomeScreen` serves as the central navigation hub. Feature screens (`IdCardScreen`, `DeviceManagementScreen`) are accessed from home and return to home, creating a predictable navigation hierarchy.

### 5. Session-Aware Initialization

The `SplashScreen` acts as an intelligent router that determines the appropriate entry point based on session state, providing seamless re-authentication for returning users.

**Sources:** [lib/main.dart L46-L71](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L46-L71)

 [lib/screens/splash_screen.dart L21-L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L21-L33)

 [lib/screens/home_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart)
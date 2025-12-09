# AndroidManifest Configuration

> **Relevant source files**
> * [android/app/src/main/AndroidManifest.xml](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml)
> * [lib/main.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart)

## Purpose and Scope

This document provides detailed technical documentation of the Android platform configuration for the SENA Digital ID Card application, specifically focusing on the `AndroidManifest.xml` file. This manifest defines Android-specific permissions, application metadata, activity configurations, and Flutter integration settings required for the application to run on Android devices.

For information about build configuration and signing, see [Build and Signing Configuration](/axchisan/AppGestionCarnetsSENA/8.2-build-and-signing-configuration). For general system architecture, see [System Architecture](/axchisan/AppGestionCarnetsSENA/3-system-architecture).

---

## AndroidManifest Overview

The `AndroidManifest.xml` file is located at [android/app/src/main/AndroidManifest.xml L1-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L1-L47)

 and serves as the entry point for Android-specific application configuration. It declares:

* **Permissions**: Required system-level capabilities
* **Application metadata**: Application name, icon, and Flutter-specific settings
* **Activity definitions**: The main activity configuration and its launch behavior
* **Intent filters**: How the app responds to system intents
* **Queries**: Visibility declarations for other apps/intents

---

## Manifest Structure

```

```

**Sources**: [android/app/src/main/AndroidManifest.xml L1-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L1-L47)

---

## Permissions

### INTERNET Permission

```

```

Declared at [android/app/src/main/AndroidManifest.xml L2](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L2-L2)

 this permission is **required** for the application's core functionality:

| Requirement | Description |
| --- | --- |
| **PostgreSQL Connection** | DatabaseService requires internet to connect to remote PostgreSQL database |
| **User Authentication** | `getAprendizFromPostgres()` validates credentials against remote server |
| **ID Validation** | `validateIdentification()` checks if user IDs exist in SENA system |
| **Data Synchronization** | `_syncToPostgres()` uploads local changes to remote database |
| **Network Check** | LoginScreen and RegistrationScreen perform `InternetAddress.lookup()` |

Without this permission, the application would fail at runtime when attempting any network operation referenced in [DatabaseService](/axchisan/AppGestionCarnetsSENA/3.1.1-databaseservice-reference) or authentication flows ([Login Flow](/axchisan/AppGestionCarnetsSENA/4.1-login-flow), [Registration Flow](/axchisan/AppGestionCarnetsSENA/4.2-registration-flow)).

**Sources**: [android/app/src/main/AndroidManifest.xml L2](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L2-L2)

---

## Application Configuration

The `<application>` element at [android/app/src/main/AndroidManifest.xml L4-L35](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L4-L35)

 defines application-wide settings:

### Application Attributes

| Attribute | Value | Purpose |
| --- | --- | --- |
| `android:label` | `"SENA Carnet Digital"` | Application name displayed in launcher, app switcher, and settings |
| `android:name` | `"${applicationName}"` | Reference to Flutter-generated application class |
| `android:icon` | `"@mipmap/ic_launcher"` | Application icon resource reference |

**Label Usage**: The label `"SENA Carnet Digital"` matches the application title defined in [lib/main.dart L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L33-L33)

 as `title: 'SENA Carnet Digital'`, maintaining consistency between Android system UI and Flutter app UI.

**Application Name Variable**: The `${applicationName}` variable is resolved at build time by Flutter's build system. For Flutter apps, this typically references `io.flutter.app.FlutterApplication`.

**Sources**: [android/app/src/main/AndroidManifest.xml L4-L7](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L4-L7)

---

## MainActivity Configuration

The `MainActivity` is declared at [android/app/src/main/AndroidManifest.xml L8-L29](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L8-L29)

 and serves as the Android entry point that hosts the Flutter engine.

```

```

**Sources**: [android/app/src/main/AndroidManifest.xml L8-L29](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L8-L29)

 [lib/main.dart L14-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L14-L24)

### Activity Attributes

#### android:exported="true"

```

```

Declared at [android/app/src/main/AndroidManifest.xml L10](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L10-L10)

 this attribute is **required** for activities with `MAIN/LAUNCHER` intent filters on Android 12+ (API level 31+). It explicitly declares that this activity can be launched by the system and other apps.

**Impact**: Without this attribute, the application would fail to install on Android 12+ devices with an error indicating that activities with intent filters must explicitly declare exported status.

#### android:launchMode="singleTop"

```

```

Configured at [android/app/src/main/AndroidManifest.xml L11](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L11-L11)

 this setting controls activity instance behavior:

| Scenario | Behavior |
| --- | --- |
| App not running | New MainActivity instance created |
| App in background, icon tapped | Existing MainActivity brought to foreground (not recreated) |
| App in foreground, notification tapped | `onNewIntent()` called on existing instance |

**Flutter Integration**: This prevents the Flutter app from restarting when returning from background, preserving the navigation stack and in-memory state (Hive boxes, loaded Aprendiz objects).

#### android:taskAffinity=""

```

```

Set at [android/app/src/main/AndroidManifest.xml L12](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L12-L12)

 an empty task affinity ensures the activity does not group with other activities based on package name. This provides isolation in the Android task manager.

#### android:theme="@style/LaunchTheme"

```

```

Configured at [android/app/src/main/AndroidManifest.xml L13](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L13-L13)

 this theme is displayed **before** the Flutter UI initializes. The comment at [android/app/src/main/AndroidManifest.xml L17-L19](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L17-L19)

 explains that this theme remains visible during Flutter engine startup, then continues to determine the window background behind the Flutter UI.

**Visual Flow**:

1. User taps app icon
2. `LaunchTheme` displayed immediately (splash screen)
3. Flutter engine initializes ([lib/main.dart L14-L22](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L14-L22) )
4. `SplashScreen` widget renders ([lib/main.dart L45](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L45-L45) )
5. Theme transitions to `NormalTheme` (see next section)

#### android:configChanges

```

```

Declared at [android/app/src/main/AndroidManifest.xml L14](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L14-L14)

 this attribute lists configuration changes that the activity handles itself **without restarting**. This is critical for Flutter apps:

| Configuration Change | Example Trigger | Flutter Handling |
| --- | --- | --- |
| `orientation` | Device rotation | Flutter's rendering pipeline redraws UI |
| `keyboard` | Physical keyboard connected | Flutter's text input system adapts |
| `screenSize` | Multi-window mode, folding device | Flutter's layout engine recomputes constraints |
| `locale` | User changes system language | Flutter can reload localized strings |
| `fontScale` | User changes accessibility font size | Flutter respects MediaQuery.textScaleFactor |
| `uiMode` | Dark mode toggle | Flutter's theme system responds |
| `density` | External display connected | Flutter's pixel ratio calculations adjust |

**Without these declarations**, Android would destroy and recreate MainActivity on each configuration change, forcing the Dart code to re-initialize from `main()` in [lib/main.dart L14](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L14-L14)

 losing the current navigation state (e.g., if user was on [DeviceManagementScreen](/axchisan/AppGestionCarnetsSENA/5.3-device-management), they'd be reset to [SplashScreen](/axchisan/AppGestionCarnetsSENA/4.3-session-management)).

#### android:hardwareAccelerated="true"

```

```

Set at [android/app/src/main/AndroidManifest.xml L15](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L15-L15)

 this enables GPU rendering for the Flutter application. Flutter's Skia rendering engine uses OpenGL ES for drawing, making hardware acceleration essential for performance.

**Impact**: Without this, the UI would render on the CPU, causing:

* Slow frame rates on complex screens (e.g., [IdCardScreen](/axchisan/AppGestionCarnetsSENA/5.2-virtual-id-card-display) with barcode generation)
* Laggy animations during navigation transitions
* Poor scrolling performance in [DeviceManagementScreen](/axchisan/AppGestionCarnetsSENA/5.3-device-management) device list

#### android:windowSoftInputMode="adjustResize"

```

```

Configured at [android/app/src/main/AndroidManifest.xml L16](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L16-L16)

 this determines how the window behaves when the soft keyboard appears:

| Mode | Behavior |
| --- | --- |
| `adjustResize` (used) | Window resizes to fit keyboard, reducing available screen space |
| `adjustPan` (alternative) | Window pans to keep focused input visible, but doesn't resize |

**Flutter Context**: The `adjustResize` mode is critical for forms in [LoginScreen](/axchisan/AppGestionCarnetsSENA/4.1-login-flow) and [RegistrationScreen](/axchisan/AppGestionCarnetsSENA/4.2-registration-flow). When a `CustomTextField` gains focus:

1. Soft keyboard appears
2. Window resizes
3. Flutter's `Scaffold` with `SingleChildScrollView` automatically scrolls to keep the focused field visible
4. Submit buttons remain accessible

**Sources**: [android/app/src/main/AndroidManifest.xml L10-L16](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L10-L16)

---

## Flutter-Specific Metadata

### NormalTheme Metadata

```

```

Declared at [android/app/src/main/AndroidManifest.xml L21-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L21-L24)

 this specifies the theme applied **after** Flutter has initialized and rendered its first frame. This provides a smooth transition from `LaunchTheme` to the actual Flutter UI.

**Theme Transition Flow**:

```

```

**Sources**: [android/app/src/main/AndroidManifest.xml L21-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L21-L24)

 [lib/main.dart L14-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L14-L24)

### Flutter Embedding Version

```

```

Set at [android/app/src/main/AndroidManifest.xml L32-L34](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L32-L34)

 this declares the Flutter embedding API version:

| Value | Embedding Version | Features |
| --- | --- | --- |
| `1` | Legacy Android embedding | Deprecated, uses old plugin registration |
| `2` | Current Android embedding (used) | Supports plugin lifecycle, proper activity result handling |

**Impact on Plugin System**: The SENA app uses plugins that depend on embedding v2:

* `image_picker` (used in [RegistrationScreen](/axchisan/AppGestionCarnetsSENA/4.2-registration-flow) for profile photo selection)
* `path_provider` (used for Hive storage initialization)
* `flutter_dotenv` (used for environment variable loading)

These plugins use the `FlutterPlugin` interface which requires embedding v2. Using embedding v1 would cause plugin registration failures.

**Sources**: [android/app/src/main/AndroidManifest.xml L32-L34](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L32-L34)

---

## Intent Filters

### MAIN/LAUNCHER Intent Filter

```

```

Declared at [android/app/src/main/AndroidManifest.xml L25-L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L25-L28)

 this intent filter registers the activity as the application's entry point:

| Component | Purpose |
| --- | --- |
| `android.intent.action.MAIN` | Identifies this as the main entry point (no data input) |
| `android.intent.category.LAUNCHER` | Makes activity appear in the app launcher |

**Effect**: This combination causes:

1. App icon to appear in device launcher/home screen
2. Tapping icon launches `MainActivity`
3. `MainActivity` starts Flutter engine
4. Flutter engine executes [lib/main.dart L14](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L14-L14)  `main()` function
5. `SenaApp` widget tree builds, showing [SplashScreen](/axchisan/AppGestionCarnetsSENA/4.3-session-management)

Without this intent filter, the application would be installed but hidden from the launcher, making it inaccessible to users.

**Sources**: [android/app/src/main/AndroidManifest.xml L25-L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L25-L28)

---

## Queries Section

### PROCESS_TEXT Intent Query

```

```

Declared at [android/app/src/main/AndroidManifest.xml L41-L46](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L41-L46)

 this query declaration is required for Android 11+ (API level 30+) package visibility restrictions.

**Purpose**: The Flutter engine's `ProcessTextPlugin` (referenced in comment at [android/app/src/main/AndroidManifest.xml L36-L40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L36-L40)

) uses the `ACTION_PROCESS_TEXT` intent for text selection actions in `TextField` widgets. The query declaration allows the Flutter app to see and query which apps can process text.

**User-Facing Feature**: When a user long-presses selected text in any `CustomTextField` (used throughout [LoginScreen](/axchisan/AppGestionCarnetsSENA/4.1-login-flow), [RegistrationScreen](/axchisan/AppGestionCarnetsSENA/4.2-registration-flow), [DeviceManagementScreen](/axchisan/AppGestionCarnetsSENA/5.3-device-management)), Android shows a context menu with options like "Share", "Copy", "Translate". This query enables that menu to populate with available text processing apps.

**Android 11+ Requirement**: Without this declaration on Android 11+, text selection features would be limited or non-functional due to package visibility restrictions.

**Sources**: [android/app/src/main/AndroidManifest.xml L41-L46](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L41-L46)

---

## Integration with Flutter Application

The manifest configuration directly affects the Flutter application behavior defined in [lib/main.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart)

:

```

```

**Sources**: [android/app/src/main/AndroidManifest.xml L1-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L1-L47)

 [lib/main.dart L14-L75](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L14-L75)

---

## Configuration Dependencies

| Manifest Setting | Depends On | Used By |
| --- | --- | --- |
| `INTERNET` permission | PostgreSQL connection string in `.env` | [DatabaseService](/axchisan/AppGestionCarnetsSENA/3.1.1-databaseservice-reference) |
| `android:exported="true"` | Android 12+ (API 31+) requirement | System launcher |
| `android:launchMode="singleTop"` | Flutter navigation stack | [Screen Navigation](/axchisan/AppGestionCarnetsSENA/3.2-screen-navigation-and-routing) |
| `android:configChanges` | Flutter rendering pipeline | All screens |
| `android:hardwareAccelerated` | Flutter Skia engine | GPU rendering |
| `flutterEmbedding=2` | Plugin system | `image_picker`, `path_provider` |
| `PROCESS_TEXT` query | `TextField` text selection | All `CustomTextField` instances |

**Sources**: [android/app/src/main/AndroidManifest.xml L1-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L1-L47)

---

## Application Lifecycle Integration

The manifest configuration affects how the application behaves through its lifecycle:

```

```

**Key Points**:

* `launchMode="singleTop"` prevents restart when returning from background
* `configChanges` prevents restart on rotation/keyboard/theme changes
* `INTERNET` permission enables DatabaseService throughout lifecycle
* `hardwareAccelerated` ensures smooth rendering in all states

**Sources**: [android/app/src/main/AndroidManifest.xml L1-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L1-L47)

---

## Summary

The `AndroidManifest.xml` configuration establishes the Android platform foundation for the SENA Digital ID Card Flutter application:

1. **Permissions**: `INTERNET` permission enables network-dependent features (authentication, sync)
2. **Application Metadata**: Defines app branding and Flutter integration
3. **MainActivity Configuration**: Optimizes lifecycle behavior for Flutter (singleTop, configChanges, hardware acceleration)
4. **Flutter Embedding**: Version 2 embedding enables modern plugin system
5. **Intent Filters**: MAIN/LAUNCHER intent makes app accessible from launcher
6. **Queries**: PROCESS_TEXT query enables text selection features

All settings work together to ensure the Flutter application ([lib/main.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart)

) launches correctly, handles configuration changes smoothly, and provides full functionality for authentication ([Authentication System](/axchisan/AppGestionCarnetsSENA/4-authentication-system)), data persistence ([Data Persistence Layer](/axchisan/AppGestionCarnetsSENA/3.1-data-persistence-layer)), and UI features ([Core Features](/axchisan/AppGestionCarnetsSENA/5-core-features)).

**Sources**: [android/app/src/main/AndroidManifest.xml L1-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L1-L47)

 [lib/main.dart L1-L75](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/main.dart#L1-L75)
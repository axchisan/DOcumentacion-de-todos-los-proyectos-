# Android Platform Configuration

> **Relevant source files**
> * [android/app/build.gradle.kts](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts)
> * [android/app/proguard-rules.pro](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/proguard-rules.pro)
> * [android/app/src/main/AndroidManifest.xml](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml)

## Purpose and Scope

This page documents the Android-specific platform configuration for the SENA Digital ID Card application. It covers the essential Android build files, manifest settings, signing configuration, and code optimization rules required to package and deploy the application on Android devices.

For detailed manifest settings including permissions and activity configuration, see [AndroidManifest Configuration](/axchisan/AppGestionCarnetsSENA/8.1-androidmanifest-configuration). For in-depth build.gradle configuration, release signing setup, and ProGuard optimization, see [Build and Signing Configuration](/axchisan/AppGestionCarnetsSENA/8.2-build-and-signing-configuration).

For information about external dependencies and packages used across all platforms, see [Dependencies and External Packages](/axchisan/AppGestionCarnetsSENA/7-dependencies-and-external-packages).

---

## Android Platform Structure

The Android platform configuration is organized in the standard Flutter Android project structure under the `android/` directory:

| File Path | Purpose |
| --- | --- |
| `android/app/src/main/AndroidManifest.xml` | Defines application metadata, permissions, activities, and system integration |
| `android/app/build.gradle.kts` | Gradle build configuration written in Kotlin DSL, handles compilation, signing, and optimization |
| `android/app/proguard-rules.pro` | ProGuard rules for code obfuscation and shrinking in release builds |
| `android/key.properties` | Keystore credentials for release signing (not in source control) |

**Sources:** [android/app/src/main/AndroidManifest.xml L1-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L1-L47)

 [android/app/build.gradle.kts L1-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L1-L67)

 [android/app/proguard-rules.pro L1-L2](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/proguard-rules.pro#L1-L2)

---

## Android Build Configuration Overview

```

```

**Diagram: Android Build Configuration Flow**

This diagram shows how the Android build system processes configuration files to produce debug and release APKs. The `build.gradle.kts` file serves as the central configuration point, loading signing credentials from `key.properties` and applying ProGuard rules for release optimization.

**Sources:** [android/app/build.gradle.kts L1-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L1-L67)

---

## Manifest Configuration

The `AndroidManifest.xml` file defines the application's identity, permissions, and activity configuration for the Android system.

### Application Metadata

| Property | Value | Purpose |
| --- | --- | --- |
| `android:label` | `"SENA Carnet Digital"` | Application name displayed to users |
| `android:icon` | `@mipmap/ic_launcher` | Application launcher icon |
| `android:name` | `${applicationName}` | Application class name (Flutter-generated) |

**Sources:** [android/app/src/main/AndroidManifest.xml L4-L7](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L4-L7)

### Permissions

The application requires a single runtime permission:

```

```

This permission is essential for the application's core functionality:

* Authentication against the remote PostgreSQL database
* ID validation during registration
* Synchronization of local Hive data with PostgreSQL
* Image uploads during registration

**Sources:** [android/app/src/main/AndroidManifest.xml L2](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L2-L2)

### MainActivity Configuration

The `MainActivity` activity is configured with several critical attributes:

| Attribute | Value | Purpose |
| --- | --- | --- |
| `android:exported` | `true` | Allows the activity to be launched by the Android system |
| `android:launchMode` | `singleTop` | Prevents duplicate activity instances when launched while already running |
| `android:theme` | `@style/LaunchTheme` | Applies splash theme during Flutter initialization |
| `android:configChanges` | `orientation\|keyboardHidden\|keyboard\|...` | Handles configuration changes without recreating the activity |
| `android:hardwareAccelerated` | `true` | Enables GPU rendering for improved performance |
| `android:windowSoftInputMode` | `adjustResize` | Resizes window when soft keyboard appears |

**Sources:** [android/app/src/main/AndroidManifest.xml L8-L16](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L8-L16)

### Flutter Embedding

The manifest declares Flutter embedding version 2, which is the current Flutter architecture:

```

```

This configuration enables the modern Flutter engine integration with Android.

**Sources:** [android/app/src/main/AndroidManifest.xml L32-L34](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L32-L34)

### Intent Filters and Queries

```

```

**Diagram: Android Intent Configuration**

The intent filter on `MainActivity` makes it the application's entry point when launched from the Android home screen. The `<queries>` section declares intent compatibility for text processing, required by Flutter's `ProcessTextPlugin`.

**Sources:** [android/app/src/main/AndroidManifest.xml L25-L46](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L25-L46)

---

## Build and Compilation Settings

### SDK Configuration

The application uses Flutter-managed SDK versions for consistency across Flutter projects:

```

```

These values are defined in Flutter's local properties and automatically synchronized with Flutter SDK requirements.

**Sources:** [android/app/build.gradle.kts L11-L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L11-L28)

### Java/Kotlin Compilation

The build configuration targets Java 11 for both source and target compatibility:

```

```

This ensures compatibility with modern Android development practices and Flutter's requirements.

**Sources:** [android/app/build.gradle.kts L14-L21](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L14-L21)

### Application Identifiers

| Property | Value | Location in Code |
| --- | --- | --- |
| `namespace` | `com.example.sena_gestion_carnets` | [android/app/build.gradle.kts L10](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L10-L10) |
| `applicationId` | `com.example.sena_gestion_carnets` | [android/app/build.gradle.kts L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L24-L24) |

Both identifiers use the same package name to ensure proper Android package resolution.

**Sources:** [android/app/build.gradle.kts L10](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L10-L10)

 [android/app/build.gradle.kts L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L24-L24)

---

## Signing Configuration

### Keystore Properties Loading

The build system loads signing credentials from `android/key.properties` at build time:

```

```

The `key.properties` file must contain:

| Property | Description |
| --- | --- |
| `keyAlias` | Alias name for the signing key |
| `keyPassword` | Password for the signing key |
| `storeFile` | Path to the keystore file (.jks or .keystore) |
| `storePassword` | Password for the keystore file |

**Important:** The `key.properties` file is excluded from source control via `.gitignore` and must be created manually in the `android/` directory.

**Sources:** [android/app/build.gradle.kts L32-L41](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L32-L41)

### Signing Configuration Creation

```

```

The configuration throws `IllegalStateException` if any required property is missing, preventing incomplete release builds.

**Sources:** [android/app/build.gradle.kts L43-L50](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L43-L50)

---

## Build Types

```

```

**Diagram: Build Type Configuration Comparison**

### Release Build Configuration

The release build type applies aggressive optimization:

```

```

| Setting | Effect |
| --- | --- |
| `isMinifyEnabled = true` | Enables code shrinking and obfuscation via ProGuard/R8 |
| `isShrinkResources = true` | Removes unused resources from the final APK |
| `proguardFiles` | Applies optimization and keep rules |

**Sources:** [android/app/build.gradle.kts L52-L58](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L52-L58)

### Debug Build Configuration

The debug build type uses default Android signing and no optimization:

```

```

This configuration prioritizes fast build times and full debugging capabilities over APK size.

**Sources:** [android/app/build.gradle.kts L59-L61](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L59-L61)

---

## ProGuard Rules

The `proguard-rules.pro` file specifies classes that must be preserved during code optimization:

```

```

### Keep Rules Explanation

| Rule | Protected Package | Reason |
| --- | --- | --- |
| `io.flutter.plugins.dotenv.**` | flutter_dotenv plugin | Prevents obfuscation of environment variable loading code used by `DatabaseService` |
| `com.github.simonwaldherr.postgres.**` | postgres package | Preserves PostgreSQL driver classes required for database connectivity |

These rules ensure that reflection-based code in external packages continues to function correctly after ProGuard optimization. Without these rules, the application would fail to load environment variables or connect to PostgreSQL in release builds.

**Sources:** [android/app/proguard-rules.pro L1-L2](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/proguard-rules.pro#L1-L2)

---

## Android Configuration Summary

```

```

**Diagram: Complete Android Configuration Flow**

This comprehensive diagram shows how all Android configuration files interact during the build process. The manifest defines the application's runtime characteristics, the Gradle build file orchestrates compilation and optimization, ProGuard rules protect critical dependencies, and the keystore provides signing credentials for release builds.

**Sources:** [android/app/src/main/AndroidManifest.xml L1-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/src/main/AndroidManifest.xml#L1-L47)

 [android/app/build.gradle.kts L1-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L1-L67)

 [android/app/proguard-rules.pro L1-L2](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/proguard-rules.pro#L1-L2)
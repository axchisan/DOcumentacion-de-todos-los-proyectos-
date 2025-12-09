# Build and Signing Configuration

> **Relevant source files**
> * [android/app/build.gradle.kts](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts)
> * [android/app/proguard-rules.pro](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/proguard-rules.pro)
> * [pubspec.yaml](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml)

## Purpose and Scope

This document covers the Android-specific build configuration for the SENA Digital ID Card application, including Gradle build settings, release signing setup, code obfuscation with ProGuard, and application versioning.

For Android manifest permissions and activity configuration, see [AndroidManifest Configuration](/axchisan/AppGestionCarnetsSENA/8.1-androidmanifest-configuration). For Flutter package dependencies defined in `pubspec.yaml`, see [Dependencies and External Packages](/axchisan/AppGestionCarnetsSENA/7-dependencies-and-external-packages).

---

## Overview

The application uses Gradle with Kotlin DSL for Android build configuration. The build system handles compilation settings, release signing with a keystore, code optimization through ProGuard, and app icon generation. All Android-specific build files are located in the `android/` directory.

Sources: [android/app/build.gradle.kts L1-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L1-L67)

---

## Application Versioning

The application version is centrally defined in `pubspec.yaml` and propagated to the Android build system through Flutter's Gradle plugin.

| Property | Value | Location |
| --- | --- | --- |
| Version Name | `1.0.0+1` | [pubspec.yaml L4](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L4-L4) |
| Version Format | `major.minor.patch+build` | - |
| Application ID | `com.example.sena_gestion_carnets` | [android/app/build.gradle.kts L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L24-L24) |

The version string follows semantic versioning where:

* `1.0.0` = user-facing version name
* `+1` = internal build number (version code)

Flutter automatically extracts these values and makes them available as `flutter.versionCode` and `flutter.versionName` in the Gradle build script [android/app/build.gradle.kts L27-L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L27-L28)

Sources: [pubspec.yaml L4](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L4-L4)

 [android/app/build.gradle.kts L24-L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L24-L28)

---

## Build Script Structure

The Android build configuration uses Gradle Kotlin DSL (`build.gradle.kts`) with three primary plugins:

```

```

**Diagram: Gradle Build Script Component Structure**

The plugins are declared at [android/app/build.gradle.kts L3-L7](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L3-L7)

:

* `com.android.application` - Provides Android app build capabilities
* `kotlin-android` - Enables Kotlin compilation
* `dev.flutter.flutter-gradle-plugin` - Integrates Flutter SDK

Sources: [android/app/build.gradle.kts L3-L7](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L3-L7)

 [android/app/build.gradle.kts L9-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L9-L67)

---

## Compilation Settings

### SDK and NDK Configuration

| Setting | Value | Purpose |
| --- | --- | --- |
| Namespace | `com.example.sena_gestion_carnets` | Package namespace for generated R class |
| Compile SDK | `flutter.compileSdkVersion` | SDK version to compile against (from Flutter) |
| NDK Version | `29.0.13599879` | Native Development Kit version |
| Min SDK | `flutter.minSdkVersion` | Minimum Android version supported |
| Target SDK | `flutter.targetSdkVersion` | Target Android API level |

The SDK versions are provided by Flutter's Gradle plugin [android/app/build.gradle.kts L11](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L11-L11)

 rather than hardcoded, ensuring compatibility with the Flutter SDK version in use.

### Java and Kotlin Compatibility

Both Java and Kotlin are configured to target Java 11:

* **Java Compatibility**: [android/app/build.gradle.kts L14-L17](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L14-L17) * `sourceCompatibility = JavaVersion.VERSION_11` * `targetCompatibility = JavaVersion.VERSION_11`
* **Kotlin JVM Target**: [android/app/build.gradle.kts L19-L21](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L19-L21) * `jvmTarget = JavaVersion.VERSION_11.toString()`

This ensures consistent bytecode generation across both languages.

Sources: [android/app/build.gradle.kts L9-L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L9-L28)

---

## Release Signing Configuration

Release builds are signed using a keystore file whose credentials are stored in `android/key.properties`. This file is excluded from version control for security.

### Keystore Properties File Structure

The `key.properties` file (not in repository) must contain:

```
storeFile=path/to/keystore.jks
storePassword=<keystore-password>
keyAlias=<key-alias>
keyPassword=<key-password>
```

### Signing Configuration Loading

```

```

**Diagram: Release Signing Configuration Flow**

The keystore loading logic [android/app/build.gradle.kts L32-L41](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L32-L41)

 includes:

1. **File Path Resolution**: `file("../key.properties")` - loads from `android/key.properties`
2. **Existence Check**: Prints debug messages if file is missing
3. **Property Loading**: Reads properties into `Properties` object
4. **Debug Output**: Prints loaded properties and file path for troubleshooting

The signing configuration is created at [android/app/build.gradle.kts L43-L50](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L43-L50)

 with error handling:

```

```

Each property throws an `IllegalStateException` if missing, preventing builds with incomplete signing configuration.

Sources: [android/app/build.gradle.kts L32-L50](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L32-L50)

---

## Build Types Configuration

The application defines two build types with different optimization and signing strategies:

### Build Types Comparison

| Feature | Release | Debug |
| --- | --- | --- |
| Signing Config | `signingConfigs.getByName("release")` | `signingConfigs.getByName("debug")` |
| Code Minification | `isMinifyEnabled = true` | `false` (default) |
| Resource Shrinking | `isShrinkResources = true` | `false` (default) |
| ProGuard Rules | `proguard-android-optimize.txt` + `proguard-rules.pro` | None |
| APK Size | Optimized (smaller) | Larger (unoptimized) |
| Build Speed | Slower (optimization overhead) | Faster |

### Release Build Configuration

The release build type [android/app/build.gradle.kts L52-L58](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L52-L58)

 enables aggressive optimization:

* **Minification**: Removes unused code using R8/ProGuard
* **Resource Shrinking**: Removes unused resources (images, layouts, etc.)
* **ProGuard Files**: * `proguard-android-optimize.txt` - Default Android optimization rules * `proguard-rules.pro` - Custom application-specific rules

### Debug Build Configuration

The debug build type [android/app/build.gradle.kts L59-L61](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L59-L61)

 uses the default debug keystore and skips optimization for faster build times.

Sources: [android/app/build.gradle.kts L52-L62](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L52-L62)

---

## ProGuard Configuration

ProGuard rules prevent code obfuscation from breaking runtime reflection used by Flutter plugins. The custom rules are defined in `android/app/proguard-rules.pro`.

### Custom Keep Rules

| Rule | Purpose | Affected Package |
| --- | --- | --- |
| `-keep class io.flutter.plugins.dotenv.** { *; }` | Preserve flutter_dotenv plugin classes | `flutter_dotenv` |
| `-keep class com.github.simonwaldherr.postgres.** { *; }` | Preserve PostgreSQL driver classes | `postgres` |

The `**` wildcard preserves all classes and inner classes in the package, and `{ *; }` preserves all fields and methods within those classes.

### Why These Rules Are Needed

1. **flutter_dotenv**: The plugin uses reflection to load `.env` file contents at runtime [pubspec.yaml L22](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L22-L22)  Obfuscation would break environment variable access.
2. **postgres**: The PostgreSQL driver may use reflection for JDBC-style database operations. Obfuscation could break connection establishment or query execution used by `DatabaseService`.

These rules are referenced in the release build type at [android/app/build.gradle.kts L57](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L57-L57)

 via the `proguardFiles()` function.

```

```

**Diagram: ProGuard Rules Processing Pipeline**

Sources: [android/app/proguard-rules.pro L1-L2](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/proguard-rules.pro#L1-L2)

 [android/app/build.gradle.kts L57](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L57-L57)

---

## Launcher Icons Configuration

The application uses the `flutter_launcher_icons` package to generate Android launcher icons from source images. Configuration is in `pubspec.yaml` under the `flutter_icons` section.

### Icon Configuration Parameters

| Parameter | Value | Purpose |
| --- | --- | --- |
| `android` | `true` | Enable Android icon generation |
| `ios` | `true` | Enable iOS icon generation |
| `image_path` | `"assets/images/icon.png"` | Source image for standard icons |
| `adaptive_icon_background` | `"#FFFFFF"` | Background color for adaptive icon |
| `adaptive_icon_foreground` | `"assets/images/icon_foreground.png"` | Foreground layer for adaptive icon |

The configuration is at [pubspec.yaml L37-L42](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L37-L42)

### Adaptive Icon Strategy

Android 8.0+ supports adaptive icons with separate foreground and background layers:

* **Background**: Solid white color (`#FFFFFF`)
* **Foreground**: Custom image from `assets/images/icon_foreground.png`

This allows the Android system to apply different shapes (circle, square, rounded square) to the icon based on device manufacturer preferences.

### Icon Generation Process

1. Add `flutter_launcher_icons` to dependencies [pubspec.yaml L12](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L12-L12)
2. Define configuration in `flutter_icons` section
3. Run: `flutter pub run flutter_launcher_icons:main`
4. Generated icons are placed in `android/app/src/main/res/` directories

Sources: [pubspec.yaml L12](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L12-L12)

 [pubspec.yaml L37-L42](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/pubspec.yaml#L37-L42)

---

## Build Optimization Summary

The build configuration implements a multi-layered optimization strategy for release builds:

```

```

**Diagram: Release Build Optimization Pipeline**

The optimization process achieves:

* **Reduced APK size** through dead code elimination and resource shrinking
* **Improved runtime performance** through bytecode optimization
* **Code security** through obfuscation (makes reverse engineering harder)
* **Plugin compatibility** through selective keep rules

All optimization is configured in [android/app/build.gradle.kts L55-L57](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L55-L57)

 and controlled by the build type.

Sources: [android/app/build.gradle.kts L52-L58](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L52-L58)

 [android/app/proguard-rules.pro L1-L2](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/proguard-rules.pro#L1-L2)

---

## Build Commands

| Command | Build Type | Signing | Output |
| --- | --- | --- | --- |
| `flutter build apk` | Release | Requires `key.properties` | `build/app/outputs/flutter-apk/app-release.apk` |
| `flutter build apk --debug` | Debug | Auto-signed debug keystore | `build/app/outputs/flutter-apk/app-debug.apk` |
| `flutter build appbundle` | Release | Requires `key.properties` | `build/app/outputs/bundle/release/app-release.aab` |

The release builds automatically use the signing configuration defined in [android/app/build.gradle.kts L43-L50](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L43-L50)

 and apply all optimizations from [android/app/build.gradle.kts L55-L57](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L55-L57)

Sources: [android/app/build.gradle.kts L43-L58](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/android/app/build.gradle.kts#L43-L58)
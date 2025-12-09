# Offline-First Strategy

> **Relevant source files**
> * [lib/screens/home_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart)
> * [lib/screens/splash_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart)
> * [lib/services/database_service.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart)

## Purpose and Scope

This document explains how the SENA Digital ID Card application implements an offline-first architecture to enable core functionality without internet connectivity. The strategy uses Hive as a local cache with PostgreSQL as the authoritative remote data source, implementing cache-first reads and write-through synchronization patterns.

For the complete API reference of the `DatabaseService` class that implements this strategy, see [DatabaseService Reference](/axchisan/AppGestionCarnetsSENA/3.1.1-databaseservice-reference). For details on the data models stored locally, see [Data Models](/axchisan/AppGestionCarnetsSENA/3.1.2-data-models-(aprendiz-and-dispositivo)). For authentication flows that require initial internet connectivity, see [Authentication System](/axchisan/AppGestionCarnetsSENA/4-authentication-system).

---

## Architecture Overview

The application implements a **hybrid persistence architecture** where Hive serves as the primary data source for read operations, while PostgreSQL serves as the authoritative source requiring periodic synchronization. This enables the application to function completely offline after initial authentication.

### Core Principles

| Principle | Implementation | Benefit |
| --- | --- | --- |
| **Local-First Reads** | All UI screens read from `aprendicesBox` Hive box | Zero latency, works offline |
| **Write-Through Sync** | Writes go to Hive immediately, then PostgreSQL asynchronously | Optimistic UI updates, eventual consistency |
| **Session Persistence** | Authenticated user stored in Hive indefinitely | No re-login required |
| **Graceful Degradation** | Sync failures are silently caught | App continues functioning with stale data |

Sources: [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

 [High-Level System Architecture Diagram 2]

---

## Data Flow Patterns

### Offline-First Data Flow

```

```

**Diagram: Offline-First Data Flow with Write-Through Synchronization**

This diagram shows how all read operations serve data from the local Hive box with zero network dependency. Write operations update the local cache immediately for optimistic UI updates, then asynchronously attempt to sync with PostgreSQL. If synchronization fails due to network unavailability, the app continues functioning with locally cached data.

Sources: [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

 [lib/services/database_service.dart L121-L127](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L121-L127)

 [lib/screens/home_screen.dart L14-L16](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L14-L16)

 [lib/screens/splash_screen.dart L23-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L24)

---

## Read Strategy: Cache-First

The application implements a **cache-first read pattern** where all UI screens retrieve data exclusively from the local Hive box. The `getAprendizFromLocal` method provides this capability.

### Implementation

```

```

**Diagram: Cache-First Read Pattern**

| Method | Location | Behavior |
| --- | --- | --- |
| `getAprendizFromLocal` | [lib/services/database_service.dart L121-L127](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L121-L127) | Returns `Aprendiz` from Hive box synchronously |
| `_aprendicesBox.get` | [lib/services/database_service.dart L123](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L123-L123) | Direct Hive box access, no network calls |

### Usage in Screens

**HomeScreen Direct Access:**

```
final box = Hive.box<Aprendiz>('aprendicesBox');
final primerAprendiz = aprendiz ?? (box.values.isNotEmpty ? box.values.first : null);
```

[lib/screens/home_screen.dart L15-L16](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L15-L16)

**SplashScreen Session Detection:**

```
final box = Hive.box<Aprendiz>('aprendicesBox');
final aprendiz = box.values.isNotEmpty ? box.values.first : null;
```

[lib/screens/splash_screen.dart L23-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L24)

Both patterns access Hive directly without network dependency, enabling full offline operation for authenticated users.

Sources: [lib/services/database_service.dart L121-L127](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L121-L127)

 [lib/screens/home_screen.dart L15-L16](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L15-L16)

 [lib/screens/splash_screen.dart L23-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L24)

---

## Write Strategy: Write-Through with Async Sync

The application implements a **write-through pattern** where data is immediately persisted to the local Hive cache, then asynchronously synchronized to PostgreSQL. This ensures the UI updates optimistically without waiting for network operations.

### Write Flow

```

```

**Diagram: Write-Through Synchronization Pattern**

### Code Implementation

**Step 1: Immediate Local Write**

```
await _aprendicesBox.put(aprendiz.idIdentificacion, aprendiz);
```

[lib/services/database_service.dart L44](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L44-L44)

**Step 2: Asynchronous Remote Sync**

```
await _syncToPostgres(aprendiz);
```

[lib/services/database_service.dart L45](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L45-L45)

The `_syncToPostgres` method wraps all PostgreSQL operations in a try-catch block that silently handles failures:

```
try {
  final conn = PostgreSQLConnection(...);
  await conn.open();
  await conn.transaction((ctx) async { ... });
  await conn.close();
} catch (e) {}
```

[lib/services/database_service.dart L49-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L49-L119)

### Failure Handling

| Scenario | Behavior | User Impact |
| --- | --- | --- |
| **No Internet** | `_syncToPostgres` throws, caught silently | Data stays local, user continues working |
| **Database Error** | Transaction fails, caught silently | Data stays local, no error shown to user |
| **Connection Timeout** | Caught in exception handler | User unaware of sync failure |

This optimistic approach prioritizes user experience over data consistency, relying on eventual synchronization when connectivity is restored.

Sources: [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

 [lib/services/database_service.dart L49-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L49-L119)

---

## Authentication and Initial Data Population

While the app is offline-first for authenticated users, **initial authentication requires internet connectivity** to validate credentials against PostgreSQL and populate the local cache.

### Online Authentication Flow

```

```

**Diagram: Authentication Cache Population Flow**

### Cache Population Details

**Image Storage:**
The `getAprendizFromPostgres` method downloads the profile photo from PostgreSQL (stored as base64), converts it to bytes, and saves it locally using `_saveImageLocally`:

```yaml
fotoPerfilPath: (row[5] as String?) != null 
  ? await _saveImageLocally(row[5] as String) 
  : null
```

[lib/services/database_service.dart L157](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L157-L157)

The local path is stored in the `Aprendiz` object, enabling offline photo display:

```
final bytes = base64Decode(base64String);
final dir = await getApplicationDocumentsDirectory();
final file = File('${dir.path}/profile_${DateTime.now().millisecondsSinceEpoch}.png');
await file.writeAsBytes(bytes);
return file.path;
```

[lib/services/database_service.dart L199-L208](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L199-L208)

**Device List:**
Associated devices are loaded via `_loadDevicesFromPostgres` and included in the cached `Aprendiz` object:

```yaml
dispositivos: await _loadDevicesFromPostgres(id)
```

[lib/services/database_service.dart L161](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L161-L161)

Once cached, the user can log out and log back in offline by reading from Hive in `SplashScreen`.

Sources: [lib/services/database_service.dart L129-L169](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L129-L169)

 [lib/services/database_service.dart L199-L208](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L199-L208)

 [lib/services/database_service.dart L171-L197](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L171-L197)

---

## Session Persistence

The application maintains user sessions by storing the authenticated `Aprendiz` object in Hive indefinitely. The `SplashScreen` detects existing sessions on app launch.

### Session Detection Flow

```

```

**Diagram: Session Detection at App Launch**

### Implementation

**Session Check:**

```
final box = Hive.box<Aprendiz>('aprendicesBox');
final aprendiz = box.values.isNotEmpty ? box.values.first : null;

if (mounted) {
  if (aprendiz != null) {
    Navigator.pushReplacementNamed(context, '/inicio', arguments: aprendiz);
  } else {
    Navigator.pushReplacementNamed(context, '/login');
  }
}
```

[lib/screens/splash_screen.dart L23-L32](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L32)

**Session Termination (Logout):**

```
void _logout() async {
  await box.clear();
  Navigator.pushReplacementNamed(context, '/splash');
}
```

[lib/screens/home_screen.dart L18-L21](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L18-L21)

The `box.clear()` method removes all cached data, forcing re-authentication on next launch.

Sources: [lib/screens/splash_screen.dart L21-L33](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L21-L33)

 [lib/screens/home_screen.dart L18-L21](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L18-L21)

---

## Offline Capabilities Matrix

The following table summarizes which features work offline versus requiring internet connectivity:

| Feature | Offline Support | Details |
| --- | --- | --- |
| **View Home Dashboard** | ✅ Full | Reads from local Hive cache |
| **View ID Card** | ✅ Full | Profile photo and barcode generated locally |
| **View Device List** | ✅ Full | Devices stored in cached `Aprendiz.dispositivos` |
| **Add Device** | ✅ Partial | Saves locally, syncs when online |
| **Remove Device** | ✅ Partial | Updates local cache, syncs when online |
| **Initial Login** | ❌ Requires Internet | Must validate against PostgreSQL |
| **Registration** | ❌ Requires Internet | Must validate ID and save to PostgreSQL |
| **Session Detection** | ✅ Full | Reads from local Hive on app launch |
| **Logout** | ✅ Full | Clears local cache only |

### Device Management Offline Behavior

The `addDevice` method demonstrates the offline-capable write pattern:

```

```

**Diagram: Device Addition Offline Flow**

The device is immediately added to the local `Aprendiz` object and saved to Hive. The UI reflects this change instantly. Synchronization to PostgreSQL happens asynchronously and may fail silently if offline.

Sources: [lib/services/database_service.dart L211-L238](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L211-L238)

 [lib/services/database_service.dart L42-L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L42-L47)

---

## Synchronization Guarantees and Limitations

### What is Guaranteed

| Aspect | Guarantee |
| --- | --- |
| **Local Writes** | Always succeed if Hive is initialized |
| **Read Consistency** | UI always shows latest local state |
| **Immediate Updates** | User sees changes instantly |

### What is NOT Guaranteed

| Aspect | Risk |
| --- | --- |
| **Remote Sync Success** | Sync failures are silent - data may never reach PostgreSQL |
| **Cross-Device Consistency** | Different devices may have different local state |
| **Conflict Resolution** | Last-write-wins; no conflict detection |
| **Data Recovery** | If Hive data is lost, no backup exists locally |

### Sync Transaction Structure

The `_syncToPostgres` method uses a PostgreSQL transaction to ensure atomicity:

```javascript
await conn.transaction((ctx) async {
  // 1. Upsert aprendiz record
  await ctx.query('INSERT ... ON CONFLICT DO UPDATE ...');
  
  // 2. Delete all existing devices
  await ctx.query('DELETE FROM dispositivos WHERE id_identificacion = @id');
  
  // 3. Insert all current devices
  for (var dispositivo in aprendiz.dispositivos) {
    await ctx.query('INSERT ... ON CONFLICT DO UPDATE ...');
  }
});
```

[lib/services/database_service.dart L64-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L64-L115)

This transaction ensures that if synchronization succeeds, the remote database reflects exactly the local state. However, if the transaction fails midway, PostgreSQL rolls back all changes, and the local state remains unsynchronized.

Sources: [lib/services/database_service.dart L49-L119](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L49-L119)

 [lib/services/database_service.dart L64-L115](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L64-L115)

---

## Hive Box Configuration

The Hive box is initialized in `main.dart` and remains open throughout the application lifecycle.

### Initialization

| Step | Location | Purpose |
| --- | --- | --- |
| Register adapters | `Hive.registerAdapter(AprendizAdapter())` | Enable `Aprendiz` serialization |
| Initialize Hive | `await Hive.initFlutter()` | Set up local storage directory |
| Open box | `await Hive.openBox<Aprendiz>('aprendicesBox')` | Create/open persistent box |

The box name `'aprendicesBox'` is referenced consistently across the codebase:

* In `DatabaseService`: `static const String _hiveBoxName = 'aprendicesBox'` [lib/services/database_service.dart L10](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L10-L10)
* In `HomeScreen`: `Hive.box<Aprendiz>('aprendicesBox')` [lib/screens/home_screen.dart L15](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L15-L15)
* In `SplashScreen`: `Hive.box<Aprendiz>('aprendicesBox')` [lib/screens/splash_screen.dart L23](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L23)

### Storage Location

Hive stores data in the application documents directory, which persists across app launches but is cleared on app uninstall:

* Android: `/data/data/com.example.appgestioncarnetssena/app_flutter/`
* iOS: Application Documents directory

Sources: [lib/services/database_service.dart L10](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L10-L10)

 [lib/screens/home_screen.dart L15](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L15-L15)

 [lib/screens/splash_screen.dart L23](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L23-L23)

---

## Summary

The offline-first strategy enables the SENA Digital ID Card application to function completely without internet connectivity for authenticated users. Key architectural decisions include:

1. **Hive as Primary Data Source**: All reads serve from local cache with zero latency
2. **Optimistic UI Updates**: Writes update local state immediately, sync asynchronously
3. **Silent Sync Failures**: Network errors don't block user workflows
4. **Persistent Sessions**: Once authenticated, users remain logged in indefinitely
5. **Initial Online Requirement**: First login requires internet to populate cache

This approach prioritizes user experience and availability over strict data consistency, making it suitable for use cases where users need reliable access to their digital ID card regardless of network conditions.

Sources: [lib/services/database_service.dart L1-L239](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/services/database_service.dart#L1-L239)

 [lib/screens/home_screen.dart L1-L301](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/home_screen.dart#L1-L301)

 [lib/screens/splash_screen.dart L1-L90](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/splash_screen.dart#L1-L90)
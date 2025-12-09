# QR Code Scanning

> **Relevant source files**
> * [client/lib/presentation/screens/profile/profile_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/profile/profile_screen.dart)
> * [client/lib/presentation/screens/qr/qr_scan_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart)

## Purpose and Scope

This document describes the QR code scanning functionality implemented in the client application, which enables users to identify inventory items and environments by scanning QR codes, as well as link their user accounts to specific environments. The scanning screen provides a dual-mode interface supporting both entity identification and environment association workflows.

For information about QR code generation on the backend, see [QR Code Generation](/axchisan/GestionInventarioSENA/8.2-qr-code-generation). For details on environment management, see [Environment Management](/axchisan/GestionInventarioSENA/13-environment-management).

---

## Overview

The QR scan screen implements two distinct operational modes accessible through a tab-based interface:

| Mode | Purpose | QR Types Accepted | Primary Use Case |
| --- | --- | --- | --- |
| **Identify** | Retrieve detailed information about scanned entities | `item`, `environment` | Quickly view item/environment details without navigation |
| **Link Environment** | Associate user account with an environment | `environment` only | Initial user setup, environment reassignment |

The screen is implemented in [client/lib/presentation/screens/qr/qr_scan_screen.dart L1-L646](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L1-L646)

 using the `mobile_scanner` package for camera-based barcode detection.

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L1-L646](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L1-L646)

---

## Scan Modes and State Management

### Mode Enumeration

The screen defines two scanning modes via the `ScanMode` enum:

```

```

Mode switching is controlled by a `TabController` that synchronizes the active tab with the current scan mode:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L34-L56](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L34-L56)

### State Variables

The screen maintains several state variables to track scanning progress and results:

| State Variable | Type | Purpose |
| --- | --- | --- |
| `_isScanning` | `bool` | Prevents concurrent scan operations |
| `_scannedData` | `String?` | Raw QR code string data |
| `_scannedPayload` | `Map<String, dynamic>?` | Parsed API response for linked environments |
| `_identifiedItem` | `InventoryItemModel?` | Stores identified inventory item details |
| `_identifiedEnvironment` | `EnvironmentModel?` | Stores identified environment details |
| `_selectedEnvironmentId` | `String?` | Selected environment for manual linking |
| `_scanMode` | `ScanMode` | Current operational mode |

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L27-L38](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L27-L38)

---

## QR Code Payload Structure

The system expects QR codes to contain JSON payloads with a specific structure:

```

```

The `_onDetect` callback parses this payload and routes to the appropriate handler:

```

```

**Diagram: QR Detection and Routing Flow**

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L71-L116](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L71-L116)

---

## Identify Mode

### Purpose

Identify mode allows users to scan QR codes to retrieve detailed information about inventory items or environments without navigating through menus. This is useful for quick lookups during inventory checks or when verifying item locations.

### Item Identification Workflow

When an `item` type QR code is scanned:

```

```

**Diagram: Item Identification Sequence**

The `_identifyItem` method makes a GET request to retrieve full item details:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L118-L130](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L118-L130)

The identified item is displayed using `_buildItemDetails()`, which shows:

* Item name, category, and status
* Internal code, serial number, brand, model
* Quantity information (total, available, damaged, missing)
* Action buttons to view full details or scan another code

[client/lib/presentation/screens/qr/qr_scan_screen.dart L309-L388](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L309-L388)

### Environment Identification Workflow

When an `environment` type QR code is scanned in identify mode:

```

```

**Diagram: Environment Identification Sequence**

The `_identifyEnvironment` method retrieves environment metadata:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L132-L144](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L132-L144)

The displayed information includes:

* Environment name and location
* QR code identifier
* Capacity and type (warehouse vs. regular)
* Active status
* Optional description

[client/lib/presentation/screens/qr/qr_scan_screen.dart L390-L480](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L390-L480)

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L118-L144](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L118-L144)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L278-L307](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L278-L307)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L309-L480](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L309-L480)

---

## Link Environment Mode

### Purpose

Link Environment mode enables users to associate their accounts with a specific physical environment. This is typically performed during initial user onboarding or when a user is reassigned to a different location. Environment association affects:

* Environment-scoped data visibility
* Environment-specific permissions
* Inventory check assignment

### QR-Based Linking Workflow

When scanning an environment QR code in link mode:

```

```

**Diagram: QR-Based Environment Linking Flow**

The `_linkEnvironmentByQR` method posts the raw QR data to the backend for validation:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L146-L165](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L146-L165)

After successful linking:

1. The raw QR data is sent to `/api/qr/scan` endpoint
2. Backend validates the QR signature (if present)
3. User's `environment_id` is updated in the database
4. Environment details are returned and stored in `_scannedPayload`
5. `AuthProvider.checkSession()` is called to refresh user state
6. Success UI displays the linked environment information

### Manual Linking Workflow

Users can also link to an environment without scanning by selecting from a dropdown:

```

```

**Diagram: Manual Environment Linking Sequence**

The manual linking workflow is implemented in these methods:

**Environment fetching** (on screen initialization):
[client/lib/presentation/screens/qr/qr_scan_screen.dart L60-L69](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L60-L69)

**Manual linking execution**:
[client/lib/presentation/screens/qr/qr_scan_screen.dart L167-L188](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L167-L188)

The dropdown UI and link button are rendered in `_buildLinkContent()`:
[client/lib/presentation/screens/qr/qr_scan_screen.dart L605-L640](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L605-L640)

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L60-L69](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L60-L69)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L146-L188](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L146-L188)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L513-L644](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L513-L644)

---

## User Interface Architecture

### Tab-Based Navigation

The screen uses a `TabController` to manage two tabs, each corresponding to a scan mode:

```

```

**Diagram: UI Component Hierarchy**

The tab bar is defined at:
[client/lib/presentation/screens/qr/qr_scan_screen.dart L222-L240](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L222-L240)

### Camera Scanner Integration

The `MobileScanner` widget from the `mobile_scanner` package is embedded in the UI with visual styling:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L241-L257](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L241-L257)

Key integration points:

* `onDetect` callback: `_onDetect` method handles barcode captures
* `fit: BoxFit.cover`: Ensures camera feed fills the container
* Border decoration: Primary color border for visual emphasis

### Content Area Rendering

The content area below the camera displays different UI based on the current mode and state:

**Conditional rendering logic:**
[client/lib/presentation/screens/qr/qr_scan_screen.dart L258-L276](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L258-L276)

**Identify mode content branches:**

* No scan: Placeholder with instructions
* Item scanned: Detailed item card with action buttons
* Environment scanned: Detailed environment card with action buttons

**Link mode content branches:**

* No link: Dropdown selector + manual link button
* Linked: Success card with environment details + action buttons

### Information Display Components

The `_buildInfoRow` helper renders key-value pairs consistently across item and environment detail views:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L482-L511](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L482-L511)

This component:

* Uses fixed-width labels (120px) for alignment
* Supports optional color-coded values (e.g., red for missing items)
* Maintains consistent typography and spacing

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L214-L268](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L214-L268)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L270-L644](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L270-L644)

---

## State Reset and Error Handling

### Reset Mechanism

The `_resetScan()` method clears all scan-related state, allowing the user to perform another scan:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L196-L204](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L196-L204)

This method is called:

* When switching between tabs
* After an error occurs
* When the user clicks "Escanear Otro" (Scan Another) button
* When an unrecognized QR type is encountered

### Error Handling Patterns

The screen implements comprehensive error handling for various failure scenarios:

```

```

**Diagram: Error Handling Flow**

**Snackbar notification utility:**
[client/lib/presentation/screens/qr/qr_scan_screen.dart L190-L194](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L190-L194)

**Error handling in detection:**
[client/lib/presentation/screens/qr/qr_scan_screen.dart L88-L115](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L88-L115)

**API error handling examples:**
[client/lib/presentation/screens/qr/qr_scan_screen.dart L126-L129](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L126-L129)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L158-L164](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L158-L164)

### Concurrent Scan Prevention

The `_isScanning` flag prevents processing multiple QR codes simultaneously:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L71-L80](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L71-L80)

This ensures:

* Only one scan operation processes at a time
* Previously scanned data isn't overwritten during processing
* UI remains consistent during async operations

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L71-L116](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L71-L116)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L190-L204](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L190-L204)

---

## API Integration Points

The QR scan screen communicates with the backend through four primary endpoints:

| Endpoint | Method | Purpose | Response Model |
| --- | --- | --- | --- |
| `/api/inventory/{id}` | GET | Retrieve item details for identification | `InventoryItemModel` |
| `/api/environments/{id}` | GET | Retrieve environment details for identification | `EnvironmentModel` |
| `/api/qr/scan` | POST | Link user to environment via QR validation | Environment object with metadata |
| `/api/users/link-environment` | POST | Link user to environment manually | Environment object with metadata |

### ApiService Integration

All API calls are made through the `ApiService` class, which is initialized with the current `AuthProvider` for authentication:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L42-L45](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L42-L45)

**GET requests** use the `getSingle` method:

* `_apiService.getSingle('$inventoryEndpoint/$itemId')` for items
* `_apiService.getSingle('$environmentsEndpoint/$environmentId')` for environments

**POST requests** use the `post` method with JSON payloads:

* `_apiService.post('/api/qr/scan', {'qr_data': _scannedData})`
* `_apiService.post('/api/users/link-environment', {'environment_id': _selectedEnvironmentId})`

### Session Synchronization

After successful environment linking, the screen triggers a session check to update the user's cached environment association:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L155-L157](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L155-L157)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L177-L178](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L177-L178)

This ensures:

* `AuthProvider.currentUser.environmentId` is updated
* Navigation guards reflect the new environment association
* Dashboard data is scoped to the correct environment

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L32-L45](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L32-L45)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L118-L188](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L118-L188)

---

## Lifecycle Management

### Initialization

The `initState` method performs three setup tasks:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L40-L58](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L40-L58)

1. **ApiService initialization**: Creates service instance with auth provider
2. **TabController setup**: Configures 2-tab controller with listener for mode switching
3. **Environment list fetch**: Pre-loads environments for manual linking dropdown

### Cleanup

The `dispose` method releases resources to prevent memory leaks:

[client/lib/presentation/screens/qr/qr_scan_screen.dart L206-L211](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L206-L211)

* Disposes `TabController` to remove listeners
* Disposes `ApiService` to cancel pending HTTP requests

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L40-L58](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L40-L58)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L206-L211](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L206-L211)

---

## Code-to-Component Mapping

This table maps UI elements to their implementation in the codebase:

| UI Element | Method/Widget | Lines | Description |
| --- | --- | --- | --- |
| Tab Bar | `TabBar` widget | [222-240](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/222-240) | Dual-mode switcher |
| Camera View | `MobileScanner` widget | [241-257](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/241-257) | QR code detection |
| Identify Content | `_buildIdentifyContent()` | [278-307](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/278-307) | Content for identify mode |
| Link Content | `_buildLinkContent()` | [513-644](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/513-644) | Content for link mode |
| Item Details Card | `_buildItemDetails()` | [309-388](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/309-388) | Displays item information |
| Environment Details Card | `_buildEnvironmentDetails()` | [390-480](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/390-480) | Displays environment information |
| Info Row | `_buildInfoRow()` | [482-511](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/482-511) | Key-value pair display |
| Success Card | Inline in `_buildLinkContent()` | [516-551](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/516-551) | Post-link success display |
| Manual Link Form | Inline in `_buildLinkContent()` | [583-642](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/583-642) | Dropdown + button |

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L214-L644](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L214-L644)
# QR Code Generation

> **Relevant source files**
> * [client/lib/core/services/session_service.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/core/services/session_service.dart)
> * [client/lib/presentation/screens/profile/profile_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/profile/profile_screen.dart)
> * [client/lib/presentation/screens/qr/qr_code_generator_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart)
> * [client/lib/presentation/screens/qr/qr_scan_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart)
> * [server/.env](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/.env)
> * [server/.gitignore](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/.gitignore)
> * [server/app/config.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/config.py)
> * [server/app/database.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/database.py)
> * [server/docker-compose.yml](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/docker-compose.yml)

## Purpose and Scope

This document describes the QR code generation system used to create scannable codes for inventory items and environments. The system generates QR codes containing signed JSON payloads that encode entity metadata and enable quick identification and linking within the SENA Inventory Management System.

For information about scanning and processing QR codes, see [QR Code Scanning](/axchisan/GestionInventarioSENA/8.1-qr-code-scanning).

## QR Payload Structure

The system generates QR codes that encode structured JSON payloads with the following schema:

| Field | Type | Description |
| --- | --- | --- |
| `v` | integer | Payload version number |
| `type` | string | Entity type: `"item"` or `"environment"` |
| `id` | string (UUID) | Entity unique identifier |
| `code` | string | Human-readable code (internal code for items, QR code for environments) |
| `name` | string | Display name of the entity |
| `location` | string | Location (for environments only) |
| `category` | string | Category (for items only) |
| `ts` | integer | Unix timestamp of generation |
| `sig` | string | SHA-256 signature for tamper detection |

The entire JSON payload is serialized to a string and embedded directly in the QR code image. When scanned, the payload is parsed to identify the entity and retrieve additional details.

**Example Payload (Environment):**

```

```

**Example Payload (Item):**

```

```

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L99-L104](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L99-L104)

 [client/lib/presentation/screens/qr/qr_scan_screen.dart L83-L86](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L83-L86)

## QR Generation Flow

```

```

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L79-L109](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L79-L109)

 [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L129-L201](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L129-L201)

## Client-Side Generation UI

### QrCodeGeneratorScreen Widget

The `QrCodeGeneratorScreen` class provides the user interface for selecting entities and generating QR codes. It is implemented at [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L22-L600](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L22-L600)

**Key State Variables:**

| Variable | Type | Purpose |
| --- | --- | --- |
| `_selectedType` | `String` | Current entity type: `'ambiente'` or `'item'` |
| `_searchQuery` | `String` | Search filter text |
| `_selectedEnvironmentId` | `String?` | Selected environment UUID |
| `_selectedItemId` | `String?` | Selected item UUID |
| `_qrData` | `String?` | Raw JSON payload string from backend |
| `_qrPayload` | `Map<String, dynamic>?` | Parsed JSON payload for display |
| `_qrBoundaryKey` | `GlobalKey` | RepaintBoundary key for image capture |

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L29-L37](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L29-L37)

### Type Selection

Users select between generating QR codes for environments or inventory items using `ChoiceChip` widgets. Switching types resets the selection and clears any previously generated QR data.

```

```

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L307-L339](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L307-L339)

### Entity Selection and Search

The screen displays a dropdown populated with either environments or items based on the selected type. A search box filters the list in real-time by name, location (for environments), or category (for items).

**Search Implementation:**

* TextField triggers `onChanged` callback at [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L352-L361](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L352-L361)
* Calls `_fetchEnvironments()` or `_fetchItems()` with search query parameter
* Results filtered client-side via `_filteredEnvironments` and `_filteredItems` getters at [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L209-L225](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L209-L225)

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L341-L363](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L341-L363)

 [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L365-L428](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L365-L428)

### QR Preview Rendering

The QR code is rendered using the `qr_flutter` package's `QrImageView` widget, which is wrapped in a `RepaintBoundary` to enable image capture.

**Rendering Configuration:**

```sql
QrImageView(
  data: _qrData,              // Raw JSON string
  size: 240,                   // 240x240 logical pixels
  backgroundColor: Colors.white,
  version: QrVersions.auto,    // Auto-select QR version
  eyeStyle: QrEyeStyle(        // Corner square styling
    eyeShape: QrEyeShape.square,
    color: Colors.black,
  ),
  dataModuleStyle: QrDataModuleStyle(
    dataModuleShape: QrDataModuleShape.square,
    color: Colors.black,
  ),
)
```

The QR preview is displayed centered with a white background and subtle shadow at [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L456-L501](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L456-L501)

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L430-L513](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L430-L513)

## Backend API

### QR Generation Endpoint

**Endpoint:** `GET /api/qr/generate/{entity_type}/{entity_id}`

**Path Parameters:**

* `entity_type`: `"environment"` or `"item"`
* `entity_id`: UUID string of the entity

**Response Schema:**

```

```

The endpoint is invoked from the client at [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L94-L96](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L94-L96)

 using the `qrGenerateEndpoint` constant from `api_constants.dart`.

**Processing Steps:**

1. Fetch entity from database by ID
2. Extract relevant fields (name, code, location/category)
3. Build JSON payload with version, type, ID, code, name, metadata
4. Generate Unix timestamp
5. Compute SHA-256 signature of payload fields
6. Serialize payload to JSON string
7. Return as `qr_data` field

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L79-L109](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L79-L109)

## Export and Sharing Features

### Image Capture

The system captures the QR widget as a PNG image using Flutter's `RenderRepaintBoundary` API. The `_captureQrPngBytes()` method at [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L111-L127](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L111-L127)

 performs the following:

1. Retrieve `RenderRepaintBoundary` from `_qrBoundaryKey.currentContext`
2. Call `toImage(pixelRatio: 3.0)` for high-resolution capture
3. Convert to PNG bytes via `toByteData(format: ImageByteFormat.png)`
4. Return `Uint8List` of PNG data

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L111-L127](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L111-L127)

### Save to File

On non-web platforms, the PNG bytes are written to the device's application documents directory:

```

```

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L129-L147](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L129-L147)

### Share

The share functionality uses the `share_plus` package to share the PNG file via the platform's native share dialog:

1. Capture PNG bytes
2. Write to temporary directory with timestamped filename
3. Create `XFile` from path
4. Call `Share.shareXFiles([XFile(path)], text: 'Código QR generado')`

Implementation at [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L149-L166](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L149-L166)

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L149-L166](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L149-L166)

### Print

The print feature generates a PDF document containing the QR code image and entity name, then opens the platform's print dialog using the `printing` package:

**PDF Structure:**

```python
- Title: "Código QR - SENA"
- QR Image: 240x240 pt
- Entity Name: 12pt text (from _qrPayload['name'])
```

The PDF is created using the `pdf` package's `pw.Document` class and rendered via `Printing.layoutPdf()` at [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L168-L202](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L168-L202)

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L168-L202](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L168-L202)

### Actions Row UI

The three action buttons (Save, Print, Share) are displayed in a horizontal row with equal spacing:

```
+---------------+  +---------------+  +---------------+
|  [↓] Guardar  |  |  [🖨] Imprimir |  |  [↗] Compartir |
+---------------+  +---------------+  +---------------+
```

Buttons are disabled when `_qrData` is null (no QR generated yet). Save and Share are also disabled on web platforms due to file system limitations.

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L515-L554](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L515-L554)

## Security Features

### Signature Generation

Each QR payload includes a SHA-256 signature (`sig` field) to detect tampering. The signature is generated server-side by hashing key payload fields with a secret key.

**Purpose:**

* Verify QR code authenticity when scanned
* Detect modified or counterfeit QR codes
* Prevent unauthorized entity impersonation

When a QR code is scanned, the backend can recompute the signature and compare it to the embedded `sig` field. Mismatches indicate tampering.

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L575](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L575-L575)

### Timestamp Field

The `ts` field records the Unix timestamp (seconds since epoch) when the QR code was generated. This enables:

* Expiry checks for time-sensitive operations
* Audit trail of QR generation events
* Detection of replay attacks with old QR codes

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L574](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L574-L574)

## Payload Details Display

An expandable `ExpansionTile` at the bottom of the screen displays the decoded payload fields for debugging and verification purposes:

| Field Display | Source |
| --- | --- |
| Versión | `_qrPayload['v']` |
| Tipo | `_qrPayload['type']` |
| ID | `_qrPayload['id']` |
| Código | `_qrPayload['code']` |
| Ubicación/Categoría | `_qrPayload['location']` or `_qrPayload['category']` |
| Timestamp | `_qrPayload['ts']` |
| Firma (SHA-256) | `_qrPayload['sig']` |
| Raw JSON | `_qrData` (selectable monospace text) |

This allows administrators to verify QR payload contents before distribution.

**Sources:** [client/lib/presentation/screens/qr/qr_code_generator_screen.dart L557-L584](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_code_generator_screen.dart#L557-L584)

## Integration with QR Scanning

The generated QR codes are designed to be scanned by the `QRScanScreen` component (see [QR Code Scanning](/axchisan/GestionInventarioSENA/8.1-qr-code-scanning)). The scanning flow:

1. `MobileScanner` captures QR image
2. `json.decode()` parses `_qrData` string to extract payload
3. `qrPayload['type']` determines entity type
4. `qrPayload['id']` used to fetch full entity details from API
5. Results displayed to user with action buttons

**Sources:** [client/lib/presentation/screens/qr/qr_scan_screen.dart L83-L95](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/qr/qr_scan_screen.dart#L83-L95)
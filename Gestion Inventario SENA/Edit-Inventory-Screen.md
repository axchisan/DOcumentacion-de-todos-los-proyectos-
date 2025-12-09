# Edit Inventory Screen

> **Relevant source files**
> * [client/lib/presentation/screens/environment/environment_overview_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/environment_overview_screen.dart)
> * [client/lib/presentation/screens/environment/manage_schedules_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/manage_schedules_screen.dart)
> * [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart)
> * [client/lib/presentation/screens/inventory/inventory_check_screen.dart](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/inventory_check_screen.dart)
> * [server/app/routers/inventory_checks.py](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/server/app/routers/inventory_checks.py)

## Purpose and Scope

The Edit Inventory Screen provides a comprehensive form-based interface for modifying existing inventory items in the system. This screen allows authorized users (supervisors and admins) to update all aspects of an inventory item including basic information, quantities, status, additional details, and maintenance dates.

For information about creating new inventory items, see [Add Inventory Screen](#6.4) (if exists). For viewing inventory items without editing, see [Inventory Items API](/axchisan/GestionInventarioSENA/6.1-inventory-items-api) and [Environment Overview Screen](/axchisan/GestionInventarioSENA/13-environment-management).

---

## Screen Overview

The Edit Inventory Screen is implemented as a Flutter stateful widget located at [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L1-L609](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L1-L609)

 The screen receives an inventory item as input via route parameters and presents a comprehensive form for editing all item properties.

**Key Characteristics:**

* Form-based interface with validation
* Organized into logical sections with visual separators
* Real-time quantity validation with color-coded feedback
* Date picker integration for temporal fields
* Dropdown selectors with Spanish translations for categories and statuses
* Submit and cancel actions with loading state handling

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L1-L100](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L1-L100)

---

## Navigation and Access Control

### Entry Points

The Edit Inventory Screen is accessed through navigation from the Environment Overview Screen. Only users with `supervisor` or `admin` roles can trigger the edit action.

```

```

**Navigation Flow:**

1. User views inventory items in `EnvironmentOverviewScreen`
2. Taps "Editar" button on an item card (visible only to supervisor/admin roles)
3. Route pushes to `/edit-inventory-item` with item data as `extra` parameter
4. On successful update, screen pops and parent screen refreshes

**Sources:** [client/lib/presentation/screens/environment/environment_overview_screen.dart L774-L775](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/environment_overview_screen.dart#L774-L775)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L10-L17](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L10-L17)

---

## Screen Structure and Components

### Widget Hierarchy

```

```

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L182-L582](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L182-L582)

---

## Form Sections and Fields

### Section 1: Basic Information

This section contains the fundamental identification and classification fields for the inventory item.

| Field | Widget Type | Validation | Description |
| --- | --- | --- | --- |
| **Nombre** (Name) | `TextFormField` | Required | Item display name |
| **Código Interno** (Internal Code) | `TextFormField` | Required | Unique internal identifier |
| **Categoría** (Category) | `DropdownButtonFormField` | Required | Item category from predefined list |
| **Tipo de Item** (Item Type) | `DropdownButtonFormField` | Required | Either 'individual' or 'group' |
| **Estado** (Status) | `DropdownButtonFormField` | Required | Current item status |

**Category Options (with translations):**

* `computer` → Computador
* `projector` → Proyector
* `keyboard` → Teclado
* `mouse` → Ratón
* `tv` → Televisor
* `camera` → Cámara
* `microphone` → Micrófono
* `tablet` → Tableta
* `other` → Otro

**Status Options:**

* `available` → Disponible
* `in_use` → En uso
* `maintenance` → En mantenimiento
* `damaged` → Dañado
* `lost` → Perdido
* `missing` → Faltante
* `good` → Bueno

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L217-L288](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L217-L288)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L41-L61](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L41-L61)

### Section 2: Quantity Management

This critical section manages item quantities with real-time validation to ensure data consistency.

```

```

**Validation Rules:**

1. Total quantity must be ≥ 1
2. Damaged and missing quantities must be ≥ 0
3. Sum of damaged + missing cannot exceed total quantity
4. Available quantity = total - damaged - missing

**Color Coding:**

* **Green** (`AppColors.success`): All items available (damaged + missing = 0)
* **Orange** (`AppColors.warning`): Some items unavailable but valid (0 < damaged + missing ≤ total)
* **Red** (`AppColors.error`): Invalid state (damaged + missing > total)

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L315-L390](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L315-L390)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L585-L607](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L585-L607)

### Section 3: Additional Details

Optional fields providing supplementary item information.

| Field | Type | Purpose |
| --- | --- | --- |
| **Número de Serie** (Serial Number) | Text | Manufacturer serial number |
| **Marca** (Brand) | Text | Item manufacturer/brand |
| **Modelo** (Model) | Text | Model identifier |
| **URL Imagen** (Image URL) | Text | Link to item image |
| **Notas** (Notes) | Multiline Text | General notes and observations |

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L418-L544](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L418-L544)

### Section 4: Important Dates

Date fields managed through interactive date picker dialogs.

```

```

**Date Fields:**

* `_purchaseDate`: When item was acquired
* `_warrantyExpiry`: Warranty expiration date
* `_lastMaintenance`: Last maintenance performed
* `_nextMaintenance`: Scheduled next maintenance

**Date Format:** DD/MM/YYYY (Colombian format)

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L483-L521](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L483-L521)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L162-L179](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L162-L179)

---

## Data Flow and State Management

### Form Initialization

```

```

**Initialization Process:**

1. Screen receives `item` map via route parameter
2. `initState()` creates `ApiService` instance with `AuthProvider`
3. `_initializeFields()` extracts all fields from `widget.item`
4. State variables populated with existing values or defaults
5. Date strings parsed to `DateTime` objects where present

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L64-L88](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L64-L88)

### Form Submission Flow

```

```

**Validation Checks:**

1. Form field validation (`_formKey.currentState!.validate()`)
2. Quantity consistency check (damaged + missing ≤ total)
3. All validations must pass before API call

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L90-L160](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L90-L160)

---

## API Integration

### Update Endpoint

The screen interacts with the inventory update API using the `ApiService` class.

**Request Details:**

* **Method:** `PUT`
* **Endpoint:** `/api/inventory/{item_id}`
* **Headers:** Includes authorization token from `AuthProvider`
* **Body:** JSON object with all item fields

**Request Payload Structure:**

```

```

**Field Handling:**

* Required fields: `name`, `internal_code`, `category`, `quantity`, `item_type`, `status`
* Optional fields sent as `null` if empty
* Dates converted to ISO8601 format using `toIso8601String()`

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L109-L134](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L109-L134)

---

## Validation Logic

### Field-Level Validation

```

```

### Form-Level Validation

After individual field validation passes, form-level validation ensures data consistency:

**Validation Function (`_updateItem`):**

```

```

This prevents submitting inconsistent quantity data where the sum of damaged and missing items exceeds the total quantity.

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L95-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L95-L103)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L225-L375](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L225-L375)

---

## Visual Feedback and UX

### Section Headers

The screen uses color-coded section headers to organize form content:

| Section | Color | Icon |
| --- | --- | --- |
| Información Básica | Primary Blue | `Icons.info_outline` |
| Gestión de Cantidades | Warning Orange | `Icons.inventory` |
| Detalles Adicionales | Info Blue | `Icons.details` |
| Fechas Importantes | Success Green | `Icons.calendar_today` |

**Implementation:** Container widgets with colored backgrounds, borders, and icons provide visual separation between sections.

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L193-L214](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L193-L214)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L291-L312](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L291-L312)

### Loading States

The screen manages loading state during API operations:

1. **Initial Load:** Screen body shows form content immediately (no loading state needed as data comes from route params)
2. **Submission:** `_isLoading` flag triggers: * Disabled submit button * Circular progress indicator replacing button text * Prevents multiple submissions

```

```

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L105-L159](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L105-L159)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L558-L573](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L558-L573)

### Real-Time Quantity Validation Display

A dynamic validation message container updates as users modify quantity fields:

**Display Logic:**

```yaml
Available = Total - (Damaged + Missing)

If (Damaged + Missing == 0):
  Color: Green
  Message: "Todos los items están disponibles ✓"
Else If (Damaged + Missing <= Total):
  Color: Orange
  Message: "Items disponibles: {Available} de {Total}"
Else:
  Color: Red
  Message: "Error: Total de dañados/faltantes excede cantidad total"
```

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L379-L390](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L379-L390)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L585-L607](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L585-L607)

---

## Action Buttons

### Cancel Button

* **Type:** `OutlinedButton`
* **Action:** `context.pop()` - returns to previous screen without saving
* **Always enabled**

### Update Button

* **Type:** `ElevatedButton`
* **Action:** Triggers `_updateItem()` validation and submission
* **Disabled when:** `_isLoading` is true
* **Loading state:** Shows `CircularProgressIndicator` widget
* **Colors:** Primary blue background with white text

Both buttons are placed in a row with equal expansion at the bottom of the form.

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L547-L576](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L547-L576)

---

## Error Handling

### Error Categories

```

```

**Error Display Mechanisms:**

1. **Field-level errors:** Red text below invalid fields (from validator functions)
2. **Quantity validation errors:** Snackbar with error background color
3. **API errors:** Snackbar showing exception message
4. **Success feedback:** Green snackbar on successful update

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L95-L103](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L95-L103)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L144-L151](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L144-L151)

---

## Translation and Localization

The screen implements Spanish translations for all user-facing text using translation maps:

### Category Translations

Defined in `_categoryTranslations` map at [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L41-L51](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L41-L51)

### Status Translations

Defined in `_statusTranslations` map at [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L53-L61](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L53-L61)

**Usage Pattern:**

* Internal values stored/transmitted in English (e.g., `computer`, `available`)
* Display labels shown in Spanish (e.g., `Computador`, `Disponible`)
* Dropdown widgets use `entries.map()` to generate menu items with translated labels
* Selected value remains as English key for API consistency

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L41-L61](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L41-L61)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L248-L256](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L248-L256)

---

## Key Implementation Details

### State Management Approach

The screen uses Flutter's `StatefulWidget` with local state management:

* No global state providers needed beyond `AuthProvider` for API authentication
* All form values stored as private state variables (e.g., `_name`, `_quantity`)
* `setState()` called after date selection and for loading state changes
* Form controllers managed by parent `Form` widget with `GlobalKey`

### ApiService Integration

```

```

The `ApiService` instance receives the `AuthProvider` for automatic token injection in HTTP headers.

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L38-L68](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L38-L68)

### Date Handling

Date fields follow a consistent pattern:

1. State stored as nullable `DateTime?`
2. Displayed using `_formatDate()` helper (DD/MM/YYYY format)
3. Edited via `_selectDate()` helper with `showDatePicker()`
4. Transmitted as ISO8601 strings using `toIso8601String()`

**Date Range:** 2000-01-01 to 2100-12-31

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L162-L179](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L162-L179)

 [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L483-L521](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L483-L521)

---

## Related Screens and Workflows

### Upstream: Environment Overview

The Edit Inventory Screen is typically accessed from the Environment Overview Screen's inventory tab:

**Trigger Code:**

```

```

**Sources:** [client/lib/presentation/screens/environment/environment_overview_screen.dart L774-L775](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/environment/environment_overview_screen.dart#L774-L775)

### Downstream: Return Navigation

After successful update, the screen:

1. Shows success snackbar
2. Calls `context.pop()` to return to previous screen
3. Previous screen's `.then()` callback triggers data refresh

This ensures the parent screen reflects updated data immediately.

**Sources:** [client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart L135-L143](https://github.com/axchisan/GestionInventarioSENA/blob/a6b12d01/client/lib/presentation/screens/inventory/edit_inventory_item_screen.dart#L135-L143)
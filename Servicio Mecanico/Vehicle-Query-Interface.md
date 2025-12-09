# Vehicle Query Interface

> **Relevant source files**
> * [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java)

## Purpose and Scope

The Vehicle Query Interface provides read-only search and viewing capabilities for vehicles registered in the system. This module allows users to search for vehicles by license plate, view vehicle details in a tabular format, and inspect the complete service history for any selected vehicle.

For vehicle registration and modification operations, see [Vehicle CRUD Operations](/BrayanTirado/Servicio-Mec-nico/6.1-vehicle-crud-operations). For viewing service history in a service-centric context, see [Service History Viewer](/BrayanTirado/Servicio-Mec-nico/4.2-service-history-viewer).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L1-L204](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L1-L204)

---

## Overview

The `ConsultaVehiculo` class is a `JInternalFrame` that implements a three-panel interface: a search bar, a vehicle list table, and a service history text area. The interface uses `VehiculoDAO` to retrieve vehicle data and service records from the PostgreSQL database. Unlike `MarcoVehiculo`, this interface is read-only and optimized for quick lookups and historical analysis.

### Component Architecture

```

```

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L15-L30](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L15-L30)

---

## User Interface Layout

The interface is organized into three distinct sections arranged vertically:

| Section | Component | Purpose | Configuration |
| --- | --- | --- | --- |
| Search Bar | `txtBuscarId` | Enter license plate for filtering | Font: Comic Sans MS 14pt, Blue border |
| Search Bar | `btnBuscar` | Trigger search operation | Button label: "Buscar" |
| Vehicle List | `tablaVehiculos` | Display matching vehicles | 6 columns, blue border, Comic Sans MS 14pt |
| History Panel | `txtHistorial` | Show service records for selected vehicle | Read-only, white background, 5 rows |

The interface uses a light blue background (`#CCCCFF`) with blue borders (`#0066FF`) for visual consistency with the application's design language.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L62-L188](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L62-L188)

---

## Search Functionality

### Search Implementation

The search mechanism is implemented in the `buscarVehiculoPorPlaca()` method, which performs case-insensitive partial matching against vehicle license plates.

```

```

### Search Behavior

* **Empty Search Field**: If `txtBuscarId` is empty, all vehicles are displayed [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L37](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L37-L37)
* **Partial Matching**: The search uses `contains()` for substring matching, not exact match [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L37](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L37-L37)
* **Case Insensitivity**: Both search term and plate numbers are converted to uppercase [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L34](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L34-L34)
* **Client-Side Filtering**: The search retrieves all vehicles and filters in memory rather than using database queries [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L35-L39](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L35-L39)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L31-L42](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L31-L42)

---

## Service History Display

### Selection Listener Mechanism

The service history panel automatically updates when a user selects a row in `tablaVehiculos`. This is implemented using a `ListSelectionListener` attached during component initialization.

```

```

The listener checks `e.getValueIsAdjusting()` to ensure the event fires only once per selection change, avoiding duplicate processing [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L26](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L26-L26)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L25-L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L25-L29)

 [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L44-L54](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L44-L54)

### History Retrieval Process

The `mostrarHistorial()` method executes the following steps:

1. **Row Validation**: Checks if `selectedRow >= 0` to ensure a row is actually selected [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L46](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L46-L46)
2. **ID Extraction**: Retrieves the vehicle ID from column 0 of the selected row [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L47](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L47-L47)
3. **DAO Query**: Calls `vehiculoDAO.listarHistorialServicios(vehiculoId)` to fetch service records [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L48](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L48-L48)
4. **Display Update**: Clears the text area and appends each service record with a newline [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L49-L52](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L49-L52)

The service history format and content are determined by the `VehiculoDAO.listarHistorialServicios()` method implementation. Each service appears as a separate line in the `txtHistorial` text area.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L44-L54](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L44-L54)

---

## Data Flow and Dependencies

### Component Initialization

```

```

The constructor sets up two event handlers:

* **Search Button**: Lambda expression invoking `buscarVehiculoPorPlaca()` [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L24](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L24-L24)
* **Table Selection**: Lambda expression invoking `mostrarHistorial()` with value adjustment check [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L25-L29](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L25-L29)

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L21-L30](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L21-L30)

### DAO Interaction Pattern

The interface interacts with `VehiculoDAO` through two primary methods:

| DAO Method | Return Type | Purpose | Invoked By |
| --- | --- | --- | --- |
| `listarVehiculos()` | `List<Vehiculo>` | Retrieve all vehicles for filtering | `buscarVehiculoPorPlaca()` |
| `listarHistorialServicios(int vehiculoId)` | `List<String>` | Fetch service records for specific vehicle | `mostrarHistorial()` |

The DAO abstracts database access, allowing the UI component to remain decoupled from SQL implementation details. See [Database Layer](/BrayanTirado/Servicio-Mec-nico/3.2-database-layer) for DAO pattern documentation.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L16](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L16-L16)

 [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L35](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L35-L35)

 [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L48](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L48-L48)

---

## Table Model Structure

The `tablaVehiculos` component uses a `DefaultTableModel` with six columns:

```

```

Each row is populated from a `Vehiculo` object using the pattern:

```

```

The ID column (column 0) serves as the primary key for linking to service history records but is visible to the user for reference purposes.

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L106-L117](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L106-L117)

 [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L38](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L38-L38)

---

## Integration Points

### Launch Context

`ConsultaVehiculo` is instantiated from the main application menu under "Vehículos → Consulta de Vehículos". The frame is added to the `JDesktopPane` managed by `AplicacionPrincipal`. See [Main Application Window](/BrayanTirado/Servicio-Mec-nico/3.1-main-application-window) for menu configuration details.

### Read-Only Design

This interface deliberately excludes modification capabilities:

* The `txtHistorial` text area is configured as non-editable [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L121](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L121-L121)
* No update, delete, or create operations are provided
* The interface is optimized for quick lookups without the complexity of form validation

For vehicle modification operations, users must navigate to `MarcoVehiculo` through the "Vehículos → Gestión de Vehículos" menu item. See [Vehicle CRUD Operations](/BrayanTirado/Servicio-Mec-nico/6.1-vehicle-crud-operations).

**Sources:** [src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java L121](https://github.com/BrayanTirado/Servicio-Mec-nico/blob/b80161f0/src/main/java/com/adso/el_taller_de_adso/vehiculos/ConsultaVehiculo.java#L121-L121)
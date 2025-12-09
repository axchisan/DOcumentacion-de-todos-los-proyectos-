# Session-Based Flash Messaging

> **Relevant source files**
> * [Admin/doctores.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php)
> * [Admin/informe.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php)
> * [Admin/inicioAdmin.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php)
> * [Admin/realizar_consulta.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/realizar_consulta.php)

## Purpose and Scope

This document explains the session-based flash messaging system used throughout the administrative interfaces of the Consultorio Emily Bernal application. Flash messages are temporary notifications that persist across a single HTTP redirect and are automatically cleared after display. The system implements the Post-Redirect-Get (PRG) pattern to prevent duplicate form submissions while providing user feedback.

For information about the broader authentication and session management system, see [Session Management](/axchisan/Consultorio_Emily_Bernal/5.1-session-management). For details on the administrative interfaces that use flash messaging, see [Core Administrative Interfaces](/axchisan/Consultorio_Emily_Bernal/2-core-administrative-interfaces).

---

## System Overview

The flash messaging system provides a consistent mechanism for displaying success, error, and informational messages to users across all administrative pages. The system leverages PHP session variables that survive HTTP redirects but are displayed exactly once before being cleared, preventing message duplication on page refresh.

**Key Characteristics:**

* **Stateful persistence**: Messages survive HTTP redirects through session storage
* **Single-display guarantee**: Messages are cleared immediately after rendering
* **Bootstrap integration**: Uses Bootstrap alert component for consistent styling
* **Dual cleanup mechanism**: JavaScript auto-hide (5 seconds) + manual close button
* **PRG pattern enforcement**: Prevents duplicate form submissions

Sources: [Admin/doctores.php L1-L34](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L1-L34)

 [Admin/informe.php L1-L506](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L1-L506)

 [Admin/inicioAdmin.php L1-L101](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L1-L101)

---

## Session Variables

The flash messaging system uses two session variables that work together to store and style notification messages:

| Session Variable | Type | Purpose | Example Values |
| --- | --- | --- | --- |
| `$_SESSION['MensajeTexto']` | `string` | The message content displayed to the user | "Datos del paciente actualizados correctamente." |
| `$_SESSION['MensajeTipo']` | `string` | Bootstrap CSS classes for alert styling | "p-3 mb-2 bg-success text-white" |

These variables are set before any redirect operation and checked on the target page load. The separation of content and styling allows flexible message formatting while maintaining a consistent user experience.

Sources: [Admin/doctores.php L8-L9](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L8-L9)

 [Admin/informe.php L161-L162](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L161-L162)

 [Admin/inicioAdmin.php L8-L9](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L8-L9)

---

## Message Type Classification

The system uses Bootstrap 4 alert classes to visually distinguish message severity. The `$_SESSION['MensajeTipo']` variable stores the complete CSS class string:

| Message Type | CSS Class Value | Visual Style | Use Case |
| --- | --- | --- | --- |
| Success | `"p-3 mb-2 bg-success text-white"` | Green background, white text | Successful operations (create, update, delete) |
| Error | `"p-3 mb-2 bg-danger text-white"` | Red background, white text | Failed operations, validation errors, authorization failures |
| Info | `"p-3 mb-2 bg-info text-white"` | Blue background, white text | Informational messages, system notifications |
| Primary | `"p-3 mb-2 bg-primary text-white"` | Primary blue background | General notifications |

Sources: [Admin/doctores.php L9](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L9-L9)

 [Admin/informe.php L162](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L162-L162)

 [Admin/inicioAdmin.php L9](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L9-L9)

---

## Post-Redirect-Get (PRG) Pattern Implementation

The system implements the PRG pattern to prevent duplicate form submissions. When a user submits a form, the server processes the request, sets flash message variables, and redirects to a display page:

```

```

**Sources:** [Admin/doctores.php L8-L34](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L8-L34)

 [Admin/informe.php L161-L170](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L161-L170)

---

## Setting Flash Messages

Flash messages are set in processing scripts immediately before performing an HTTP redirect. The pattern is consistent across all CRUD operations:

### Success Message Pattern

```

```

**Example from informe.php (Patient Data Update):**
[Admin/informe.php L161-L170](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L161-L170)

```

```

### Error Message Pattern

```

```

**Example from informe.php (Medical Report Update Failure):**
[Admin/informe.php L383-L386](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L383-L386)

```

```

### Authorization Failure Pattern

**Example from inicioAdmin.php (Session Validation):**
[Admin/inicioAdmin.php L7-L11](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L7-L11)

```

```

**Sources:** [Admin/informe.php L161-L170](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L161-L170)

 [Admin/inicioAdmin.php L7-L11](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L7-L11)

 [Admin/doctores.php L8-L11](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L8-L11)

---

## Display Pattern Implementation

Display pages check for the presence of `$_SESSION['MensajeTexto']` and render an alert component if the variable exists. The message is immediately cleared after display to ensure single-use semantics.

### Two Display Locations

The system implements flash message display in two locations within each page:

#### 1. Early Page Display (Before HTML)

[Admin/doctores.php L20-L34](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L20-L34)

```

```

#### 2. In-Page Bootstrap Alert Display

[Admin/inicioAdmin.php L93-L101](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L93-L101)

```

```

**Note:** Setting variables to `null` versus using `unset()` achieves the same effect in this context - both clear the message and prevent redisplay.

**Sources:** [Admin/doctores.php L20-L34](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L20-L34)

 [Admin/inicioAdmin.php L93-L101](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L93-L101)

 [Admin/informe.php L497-L506](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L497-L506)

---

## Message Lifecycle State Machine

The following diagram illustrates the complete lifecycle of a flash message from creation through cleanup:

```

```

**Sources:** [Admin/doctores.php L20-L34](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L20-L34)

 [Admin/inicioAdmin.php L93-L101](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L93-L101)

---

## JavaScript Enhancement Layer

The system provides two mechanisms for dismissing alert notifications: automatic timeout and manual user interaction.

### Auto-Hide Implementation (5-Second Timeout)

[Admin/doctores.php L29-L33](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L29-L33)

```

```

This inline script targets the alert `div` with `id="mensaje"` and sets `display: none` after 5 seconds.

### Manual Close Button Handler

[Admin/inicioAdmin.php L146-L154](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L146-L154)

```

```

This event listener:

1. Selects all elements with class `.alert .delete` (close buttons)
2. Attaches click handlers that remove the parent alert element from the DOM
3. Uses `removeChild()` to completely eliminate the alert element

**Sources:** [Admin/doctores.php L29-L33](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L29-L33)

 [Admin/inicioAdmin.php L146-L154](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L146-L154)

 [Admin/informe.php L813-L819](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L813-L819)

---

## Implementation Across Administrative Pages

The flash messaging system is implemented consistently across all major administrative interfaces:

```

```

**Sources:** [Admin/inicioAdmin.php L93-L101](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L93-L101)

 [Admin/doctores.php L20-L34](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L20-L34)

 [Admin/informe.php L497-L506](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L497-L506)

---

## Common Use Cases and Message Examples

The following table documents actual flash messages used throughout the system:

| Use Case | Message Text | Type | Source Location |
| --- | --- | --- | --- |
| Session not initialized | "Error acceso al sistema: Sesión no iniciada." | Error | [inicioAdmin.php L8](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/inicioAdmin.php#L8-L8) |
| Concurrent login detected | "Tu sesión ha sido cerrada por inicio en otro dispositivo." | Error | [inicioAdmin.php L20](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/inicioAdmin.php#L20-L20) |
| Patient data updated | "Datos del paciente actualizados correctamente." | Success | [informe.php L161](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/informe.php#L161-L161) |
| Patient update failed | "Error al actualizar los datos del paciente: {error}" | Error | [informe.php L165](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/informe.php#L165-L165) |
| Medical report created | "Informe médico creado correctamente." | Success | [informe.php L414](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/informe.php#L414-L414) |
| Medical report updated | "Informe médico actualizado correctamente." | Success | [informe.php L380](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/informe.php#L380-L380) |
| Medical report failed | "Error al actualizar el informe médico: {error}" | Error | [informe.php L383](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/informe.php#L383-L383) |
| Invalid patient ID | "Error: ID de paciente no proporcionado." | Error | [informe.php L20](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/informe.php#L20-L20) |
| No appointments found | "Error: No se encontraron citas para este paciente con el doctor actual." | Error | [informe.php L59](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/informe.php#L59-L59) |

**Sources:** [Admin/inicioAdmin.php L8-L20](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L8-L20)

 [Admin/informe.php L20-L420](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L20-L420)

 [Admin/doctores.php L8-L9](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L8-L9)

---

## Security Considerations

The flash messaging system includes basic XSS protection through output escaping:

### HTML Output Escaping

[Admin/informe.php L498-L499](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L498-L499)

```

```

The `htmlspecialchars()` function converts special characters to HTML entities, preventing script injection through message content. However, not all implementations consistently use this protection:

**Inconsistent Protection:**

* [Admin/inicioAdmin.php L95](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L95-L95)  - Uses `echo $_SESSION['MensajeTexto']` without escaping
* [Admin/doctores.php L23](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L23-L23)  - Uses `echo $_SESSION['MensajeTexto']` without escaping

**Best Practice:** All message output should use `htmlspecialchars()` to prevent potential XSS vulnerabilities if user input ever flows into message text.

**Sources:** [Admin/informe.php L498-L499](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L498-L499)

 [Admin/inicioAdmin.php L95](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L95-L95)

 [Admin/doctores.php L23](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L23-L23)

---

## Architectural Benefits

The flash messaging system provides several architectural advantages:

### 1. Separation of Concerns

Processing logic (CRUD operations) is separated from display logic. Processing scripts handle business operations and set messages, while display pages focus on rendering.

### 2. Duplicate Submission Prevention

The PRG pattern prevents duplicate form submissions by converting POST requests into GET requests after processing.

### 3. Consistent User Experience

Bootstrap alert styling provides uniform visual feedback across all administrative interfaces.

### 4. Stateless HTTP Compliance

Messages survive HTTP redirects through session storage but don't persist beyond a single display, maintaining stateless HTTP principles.

### 5. Progressive Enhancement

JavaScript auto-hide and manual close buttons enhance usability but aren't required for basic functionality.

**Sources:** [Admin/inicioAdmin.php L1-L165](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L1-L165)

 [Admin/doctores.php L1-L192](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L1-L192)

 [Admin/informe.php L1-L865](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L1-L865)

---

## Code Pattern Summary

### Complete Implementation Checklist

**Setting Messages (Processing Script):**

```

```

**Displaying Messages (Display Page):**

```

```

**JavaScript Enhancement:**

```

```

**Sources:** [Admin/inicioAdmin.php L93-L154](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/inicioAdmin.php#L93-L154)

 [Admin/doctores.php L20-L189](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/doctores.php#L20-L189)
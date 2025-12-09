# User Interface Layer

> **Relevant source files**
> * [src/GUI/AdminForm.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java)
> * [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)
> * [src/Paneles.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java)

## Purpose and Scope

This document provides comprehensive documentation of the User Interface Layer in the crud3 application. It covers all Swing-based GUI components, their structure, event handling mechanisms, and interactions with the business logic layer. The UI layer consists of three main JFrame-based forms that provide user interfaces for student data management.

For information about the business logic that the UI layer invokes, see [Application Logic Layer](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/5-application-logic-layer). For details on the overall system architecture, see [Architecture](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/3-architecture).

## Overview

The User Interface Layer is built using Java Swing and consists of three independent JFrame-based forms:

| Form Class | Package | Purpose | Status |
| --- | --- | --- | --- |
| `GUI` | `GUI` | Primary data entry form for student records | Fully implemented |
| `AdminForm` | `GUI` | Administrative interface | Skeleton implementation |
| `Paneles` | (default) | Additional UI component | Skeleton implementation |

Each form is generated and managed by the NetBeans GUI Builder (Form Editor), which produces both `.java` source files and corresponding `.form` XML definition files.

**Sources:** [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)

 [src/GUI/AdminForm.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java)

 [src/Paneles.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java)

## UI Component Architecture

The following diagram shows the three main UI forms and their inheritance from the Swing framework:

```

```

**Sources:** [src/GUI/GUI.java L15](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L15-L15)

 [src/GUI/AdminForm.java L11](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java#L11-L11)

 [src/Paneles.java L10](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java#L10-L10)

## Main Data Entry Form: GUI.GUI

The `GUI` class is the primary user interface for entering and saving student records. It is the application's main entry point, as specified in the JAR manifest.

### Component Structure

The form contains a hierarchical structure of Swing components:

```

```

**Component Declarations:**

The form declares 10 private member variables for its UI components at [src/GUI/GUI.java L130-L142](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L130-L142)

:

| Variable Name | Type | Purpose |
| --- | --- | --- |
| `jPanel1` | `JPanel` | Main container panel |
| `jLabel1` through `jLabel4` | `JLabel` | Static text labels for fields |
| `txtNombre` | `JTextField` | Student name input |
| `txtApellido` | `JTextField` | Student last name input |
| `txtTelefono` | `JTextField` | Student phone input |
| `txtCorreo` | `JTextField` | Student email input |
| `btnSave1` | `JButton` | Save button with action handler |
| `btnClean` | `JButton` | Clean button to clear fields |

**Sources:** [src/GUI/GUI.java L30-L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L30-L84)

 [src/GUI/GUI.java L130-L142](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L130-L142)

### Layout Management

The `GUI` form uses `AbsoluteLayout` from the NetBeans library (`org.netbeans.lib.awtextra.AbsoluteLayout`):

* **Container:** The main content pane uses `AbsoluteLayout` [src/GUI/GUI.java L46](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L46-L46)
* **Panel:** `jPanel1` also uses `AbsoluteLayout` [src/GUI/GUI.java L48](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L48-L48)
* **Positioning:** All components are positioned using `AbsoluteConstraints` with explicit x,y coordinates and dimensions

For example, the name field is positioned at [src/GUI/GUI.java L62](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L62-L62)

:

```
jPanel1.add(txtNombre, new org.netbeans.lib.awtextra.AbsoluteConstraints(300, 40, 510, -1));
```

**Sources:** [src/GUI/GUI.java L46-L82](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L46-L82)

### Input Field Mapping

The form collects four pieces of student information:

```

```

**Sources:** [src/GUI/GUI.java L157-L161](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L157-L161)

## Event Handling and Action Listeners

The `GUI` form implements event-driven functionality through ActionListener implementations on both buttons.

### Event Flow Architecture

```

```

### ActionListener Implementations

Both buttons have ActionListener implementations defined using anonymous inner classes:

**Save Button Handler** [src/GUI/GUI.java L75-L79](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L75-L79)

:

```

```

**Clean Button Handler** [src/GUI/GUI.java L67-L71](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L67-L71)

:

```

```

The actual event handler methods are concise delegates:

* `btnSave1ActionPerformed` [src/GUI/GUI.java L87-L89](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L87-L89)  simply calls `ValidadarDatos()`
* `btnCleanActionPerformed` [src/GUI/GUI.java L91-L93](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L91-L93)  simply calls `LimpiarCampos()`

**Sources:** [src/GUI/GUI.java L67-L93](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L67-L93)

## Input Validation: ValidadarDatos Method

The `ValidadarDatos()` method at [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

 implements client-side validation and orchestrates the data persistence workflow.

### Validation Logic Flow

```

```

### Validation Steps

The method performs sequential validation of all four required fields:

1. **Name Validation** [src/GUI/GUI.java L144-L146](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L144-L146) : Checks if `txtNombre.getText().trim().isEmpty()`
2. **Last Name Validation** [src/GUI/GUI.java L147-L149](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L147-L149) : Checks if `txtApellido.getText().trim().isEmpty()`
3. **Phone Validation** [src/GUI/GUI.java L150-L152](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L150-L152) : Checks if `txtTelefono.getText().trim().isEmpty()`
4. **Email Validation** [src/GUI/GUI.java L153-L155](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L153-L155) : Checks if `txtCorreo.getText().trim().isEmpty()`

Each validation failure:

* Displays an error message via `JOptionPane.showMessageDialog()`
* Calls `requestFocus()` on the invalid field to guide the user

### Model Population and Persistence

Upon successful validation [src/GUI/GUI.java L157-L162](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L157-L162)

:

1. Creates a new `alumno` instance
2. Populates it using setter methods: * `alum.setNombre(txtNombre.getText())` * `alum.setApellido(txtApellido.getText())` * `alum.setTelefono(txtTelefono.getText())` * `alum.setCorreo(txtCorreo.getText())`
3. Invokes `alumnoDataChange.guardado(alum)` to persist the data

If persistence succeeds, the method calls `LimpiarCampos()` to reset the form [src/GUI/GUI.java L163-L165](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L163-L165)

Finally, the method always displays the service's message via `JOptionPane.showMessageDialog(null, alumnoDataChange.mensaje)` [src/GUI/GUI.java L168](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L168-L168)

**Sources:** [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

## Field Management: LimpiarCampos Method

The `LimpiarCampos()` method at [src/GUI/GUI.java L173-L178](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L173-L178)

 provides field clearing functionality:

```

```

This method is invoked in two scenarios:

1. **Automatically:** After successful data persistence [src/GUI/GUI.java L164](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L164-L164)
2. **Manually:** When the user clicks the Clean button [src/GUI/GUI.java L92](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L92-L92)

**Sources:** [src/GUI/GUI.java L173-L178](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L173-L178)

## Integration with Business Logic Layer

The `GUI` class imports and interacts with two classes from the business logic layer:

```

```

The import statements at the top of the file [src/GUI/GUI.java L7-L9](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L7-L9)

:

```

```

**Sources:** [src/GUI/GUI.java L7-L9](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L7-L9)

 [src/GUI/GUI.java L157-L162](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L157-L162)

## AdminForm Component

The `AdminForm` class at [src/GUI/AdminForm.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java)

 is a skeleton implementation for administrative functionality.

### Current State

The form currently contains:

* **Constructor:** Standard initialization calling `initComponents()` [src/GUI/AdminForm.java L16-L18](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java#L16-L18)
* **Layout:** Uses `GroupLayout` with empty content (400x300 window) [src/GUI/AdminForm.java L31-L40](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java#L31-L40)
* **Main Method:** Standard Swing boilerplate with Nimbus L&F configuration [src/GUI/AdminForm.java L48-L78](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java#L48-L78)
* **No Components:** The variables declaration section is empty [src/GUI/AdminForm.java L80-L81](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java#L80-L81)

```

```

### Look and Feel Configuration

Like all forms, `AdminForm` attempts to set the Nimbus look and feel in its main method [src/GUI/AdminForm.java L54-L69](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java#L54-L69)

 This is standard NetBeans-generated code that iterates through installed L&F options.

**Sources:** [src/GUI/AdminForm.java L11-L82](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java#L11-L82)

## Paneles Component

The `Paneles` class at [src/Paneles.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java)

 is another skeleton implementation located in the default package (no package declaration).

### Current State

Similar to `AdminForm`, this form contains minimal implementation:

* **Logger:** Declares a static logger [src/Paneles.java L12](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java#L12-L12)
* **Constructor:** Standard initialization [src/Paneles.java L17-L19](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java#L17-L19)
* **Layout:** Uses `GroupLayout` with empty content (400x300 window) [src/Paneles.java L32-L41](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java#L32-L41)
* **No Components:** Empty variables section [src/Paneles.java L71-L72](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java#L71-L72)

### Differences from AdminForm

The `Paneles` class has two notable differences:

1. **Exception Handling:** Uses Java 7+ multi-catch syntax at [src/Paneles.java L62](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java#L62-L62) : ``` ``` Instead of four separate catch blocks.
2. **Lambda Expression:** Uses a lambda in the `EventQueue.invokeLater()` call at [src/Paneles.java L68](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java#L68-L68) : ``` ``` Instead of an anonymous `Runnable` instance.

**Sources:** [src/Paneles.java L10-L73](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java#L10-L73)

## Application Entry Point

The `GUI` class serves as the application's main entry point, as specified in the JAR manifest. Its `main()` method at [src/GUI/GUI.java L98-L128](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L98-L128)

 follows the standard Swing application pattern:

```

```

The method ensures thread safety by creating and showing the GUI on the Event Dispatch Thread using `EventQueue.invokeLater()`.

**Sources:** [src/GUI/GUI.java L98-L128](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L98-L128)

## UI Component Lifecycle

All three forms follow the same initialization pattern:

```

```

The `initComponents()` method in each form is marked with a warning comment at the method definition [src/GUI/GUI.java L21-L27](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L21-L27)

:

```yaml
WARNING: Do NOT modify this code. The content of this method is always regenerated by the Form Editor.
```

This indicates that the code between the `//GEN-BEGIN:initComponents` and `//GEN-END:initComponents` markers is generated and managed by NetBeans.

**Sources:** [src/GUI/GUI.java L30-L85](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L30-L85)

 [src/GUI/AdminForm.java L26-L43](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java#L26-L43)

 [src/Paneles.java L27-L44](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java#L27-L44)

## Summary Table: UI Forms Comparison

| Aspect | GUI.GUI | AdminForm | Paneles |
| --- | --- | --- | --- |
| **Package** | `GUI` | `GUI` | (default) |
| **Purpose** | Student data entry | Administrative interface | Additional UI component |
| **Implementation Status** | Fully functional | Skeleton only | Skeleton only |
| **Layout Manager** | `AbsoluteLayout` | `GroupLayout` | `GroupLayout` |
| **Component Count** | 10 (4 fields, 4 labels, 2 buttons) | 0 | 0 |
| **Business Logic Integration** | Yes (imports `alumno`, `alumnoDataChange`) | No | No |
| **Event Handlers** | 2 (Save, Clean) | 0 | 0 |
| **Validation Logic** | `ValidadarDatos()` | None | None |
| **Field Management** | `LimpiarCampos()` | None | None |
| **Main Entry Point** | Yes (specified in manifest) | Yes (can run standalone) | Yes (can run standalone) |
| **Look & Feel** | Nimbus (if available) | Nimbus (if available) | Nimbus (if available) |

**Sources:** [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)

 [src/GUI/AdminForm.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/AdminForm.java)

 [src/Paneles.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/Paneles.java)
# Main Data Entry Form (GUI.GUI)

> **Relevant source files**
> * [build/classes/GUI/GUI$1.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI$1.class)
> * [build/classes/GUI/GUI$2.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI$2.class)
> * [build/classes/GUI/GUI$3.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI$3.class)
> * [build/classes/GUI/GUI.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.class)
> * [build/classes/GUI/GUI.form](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI.form)
> * [src/GUI/GUI.form](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.form)
> * [src/GUI/GUI.java](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java)

## Purpose and Scope

This page documents the `GUI.GUI` class, which serves as the primary user interface for entering student data into the crud3 application. It is the main entry point of the application and provides a form-based interface for capturing student information (nombre, apellido, telefono, correo) and saving it to the database.

For information about the validation logic implementation, see [Input Validation (ValidadarDatos)](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/5.3-input-validation-(validadardatos)). For details on the data persistence service invoked by this form, see [Data Change Service (alumnoDataChange)](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/5.2-data-change-service-(alumnodatachange)). For the complete event-driven architecture including other GUI components, see [Event Handling and Action Listeners](/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/4.4-event-handling-and-action-listeners).

---

## Overview

The `GUI.GUI` class is a Swing-based `JFrame` that extends `javax.swing.JFrame`. It is defined as the application's main class in the JAR manifest and provides the primary interface for the CRUD application's "Create" operation. The form is designed using NetBeans GUI Builder, with its layout and component definitions stored in [src/GUI/GUI.form](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.form)

**Key Characteristics:**

| Aspect | Details |
| --- | --- |
| **Package** | `GUI` |
| **Superclass** | `javax.swing.JFrame` |
| **Main Method** | Yes - application entry point |
| **Form Definition** | [src/GUI/GUI.form](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.form) |
| **Generated Code** | Lines 30-85 in [src/GUI/GUI.java L30-L85](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L30-L85) |

**Sources:** [src/GUI/GUI.java L15](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L15-L15)

 [src/GUI/GUI.form L3](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.form#L3-L3)

---

## Class Structure and Dependencies

```

```

**Sources:** [src/GUI/GUI.java L7-L9](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L7-L9)

 [src/GUI/GUI.java L15](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L15-L15)

---

## Component Hierarchy

The form consists of a hierarchical component structure with all UI elements contained within a single `JPanel`.

```

```

**Component Declarations:**

| Component Name | Type | Purpose | Declaration Line |
| --- | --- | --- | --- |
| `jPanel1` | `JPanel` | Main container panel | [src/GUI/GUI.java L137](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L137-L137) |
| `jLabel1` | `JLabel` | "Correo" label | [src/GUI/GUI.java L133](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L133-L133) |
| `jLabel2` | `JLabel` | "nombre" label | [src/GUI/GUI.java L134](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L134-L134) |
| `jLabel3` | `JLabel` | "apellido" label | [src/GUI/GUI.java L135](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L135-L135) |
| `jLabel4` | `JLabel` | "telefono" label | [src/GUI/GUI.java L136](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L136-L136) |
| `txtCorreo` | `JTextField` | Email input field | [src/GUI/GUI.java L139](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L139-L139) |
| `txtNombre` | `JTextField` | Name input field | [src/GUI/GUI.java L140](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L140-L140) |
| `txtApellido` | `JTextField` | Last name input field | [src/GUI/GUI.java L138](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L138-L138) |
| `txtTelefono` | `JTextField` | Phone input field | [src/GUI/GUI.java L141](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L141-L141) |
| `btnClean` | `JButton` | Clear all fields | [src/GUI/GUI.java L131](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L131-L131) |
| `btnSave1` | `JButton` | Save to database | [src/GUI/GUI.java L132](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L132-L132) |

**Sources:** [src/GUI/GUI.java L130-L142](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L130-L142)

 [src/GUI/GUI.form L28-L134](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.form#L28-L134)

---

## Layout and Positioning

The form uses `AbsoluteLayout` from the NetBeans AWT extras library (`org.netbeans.lib.awtextra.AbsoluteLayout`). Components are positioned using absolute pixel coordinates via `AbsoluteConstraints`.

```

```

**Key Layout Coordinates:**

| Component | X Position | Y Position | Width | Height |
| --- | --- | --- | --- | --- |
| Labels | 39-40 | 37-250 | 152 | 44 |
| Text Fields | 300-310 | 40-260 | 510 | default |
| Save Button | 110 | 350 | default | 80 |
| Clean Button | 620 | 350 | default | 80 |

**Sources:** [src/GUI/GUI.java L46-L82](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L46-L82)

 [src/GUI/GUI.form L24-L134](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.form#L24-L134)

---

## Component Initialization

The `initComponents()` method is automatically generated by NetBeans Form Editor and handles all component instantiation and configuration. This method is called from the constructor [src/GUI/GUI.java L20-L22](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L20-L22)

```

```

**Initialization Steps:**

1. **Component Instantiation** [src/GUI/GUI.java L33-L43](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L33-L43) : Creates new instances of all GUI components
2. **Window Configuration** [src/GUI/GUI.java L45](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L45-L45) : Sets default close operation to `EXIT_ON_CLOSE`
3. **Layout Setup** [src/GUI/GUI.java L46-L48](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L46-L48) : Configures `AbsoluteLayout` for content pane and panel
4. **Label Configuration** [src/GUI/GUI.java L50-L60](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L50-L60) : Sets text for all labels
5. **Button Configuration** [src/GUI/GUI.java L66-L80](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L66-L80) : Sets button text and attaches action listeners
6. **Component Positioning** [src/GUI/GUI.java L51-L82](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L51-L82) : Adds components to panel with absolute constraints
7. **Finalization** [src/GUI/GUI.java L84](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L84-L84) : Calls `pack()` to size the window

**Sources:** [src/GUI/GUI.java L31-L85](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L31-L85)

---

## Event Handlers and Action Listeners

The form implements two primary event handlers that respond to button clicks. These handlers are attached to the buttons via anonymous inner classes generated by NetBeans.

```

```

**Event Handler Implementations:**

### Save Button Handler

[src/GUI/GUI.java L87-L89](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L87-L89)

```

```

**Anonymous Listener Class:** `GUI$2` defined at [src/GUI/GUI.java L75-L79](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L75-L79)

### Clean Button Handler

[src/GUI/GUI.java L91-L93](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L91-L93)

```

```

**Anonymous Listener Class:** `GUI$1` defined at [src/GUI/GUI.java L67-L71](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L67-L71)

**Sources:** [src/GUI/GUI.java L67-L93](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L67-L93)

 [build/classes/GUI/GUI$1.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI$1.class)

 [build/classes/GUI/GUI$2.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI$2.class)

---

## Validation Logic (ValidadarDatos Method)

The `ValidadarDatos()` method performs client-side validation and orchestrates the data persistence workflow. It validates all fields sequentially and, if validation passes, creates an `alumno` object and invokes the persistence service.

```

```

**Validation Sequence:**

1. **Field Validation** [src/GUI/GUI.java L144-L156](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L144-L156) : Each field is checked using `getText().trim().isEmpty()` * If empty: Display error dialog and set focus to the invalid field * Validation order: txtNombre → txtApellido → txtTelefono → txtCorreo
2. **Object Creation** [src/GUI/GUI.java L157](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L157-L157) : `alumno alum = new alumno()`
3. **Data Population** [src/GUI/GUI.java L158-L161](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L158-L161) : Set all properties via setter methods
4. **Persistence** [src/GUI/GUI.java L162](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L162-L162) : `boolean guardar = alumnoDataChange.guardado(alum)`
5. **Success Handling** [src/GUI/GUI.java L163-L165](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L163-L165) : If `guardar == true`, call `LimpiarCampos()`
6. **User Feedback** [src/GUI/GUI.java L168](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L168-L168) : Display `alumnoDataChange.mensaje` in dialog
7. **Return** [src/GUI/GUI.java L169](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L169-L169) : Always returns `false`

**Sources:** [src/GUI/GUI.java L143-L171](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L143-L171)

---

## Field Management (LimpiarCampos Method)

The `LimpiarCampos()` method resets all input fields to empty strings. This method is invoked after successful data persistence or when the user explicitly clicks the Clean button.

```

```

**Method Implementation:**

[src/GUI/GUI.java L173-L178](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L173-L178)

```

```

**Invocation Contexts:**

| Context | Trigger | Source |
| --- | --- | --- |
| After successful save | `ValidadarDatos()` returns true from `guardado()` | [src/GUI/GUI.java L164](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L164-L164) |
| User clicks Clean button | `btnCleanActionPerformed()` handler | [src/GUI/GUI.java L92](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L92-L92) |

**Sources:** [src/GUI/GUI.java L173-L178](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L173-L178)

---

## Integration with Business Logic

The form integrates with the business logic layer through two primary interfaces: the `model.alumno` entity and the `services.alumnoDataChange` service.

```

```

**Data Flow Mapping:**

| GUI Component | Alumno Property | Setter Method | Line Reference |
| --- | --- | --- | --- |
| `txtNombre` | `nombre` | `setNombre(String)` | [src/GUI/GUI.java L158](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L158-L158) |
| `txtApellido` | `apellido` | `setApellido(String)` | [src/GUI/GUI.java L159](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L159-L159) |
| `txtTelefono` | `telefono` | `setTelefono(String)` | [src/GUI/GUI.java L160](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L160-L160) |
| `txtCorreo` | `correo` | `setCorreo(String)` | [src/GUI/GUI.java L161](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L161-L161) |

**Service Interaction:**

* **Static Method Call:** `alumnoDataChange.guardado(alum)` at [src/GUI/GUI.java L162](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L162-L162)
* **Return Type:** `boolean` indicating success/failure
* **Message Retrieval:** Static field `alumnoDataChange.mensaje` accessed at [src/GUI/GUI.java L168](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L168-L168)

**Sources:** [src/GUI/GUI.java L7-L9](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L7-L9)

 [src/GUI/GUI.java L157-L162](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L157-L162)

---

## Main Method and Application Entry Point

The `GUI` class contains the `main()` method, making it the application entry point as specified in the JAR manifest.

```

```

**Main Method Responsibilities:**

1. **Look and Feel Configuration** [src/GUI/GUI.java L105-L110](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L105-L110) : * Iterates through installed Look and Feel options * Sets Nimbus L&F if available * Falls back to default if Nimbus not found
2. **Exception Handling** [src/GUI/GUI.java L111-L119](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L111-L119) : * Catches four specific exceptions related to L&F configuration * Logs exceptions at SEVERE level using `java.util.logging.Logger`
3. **GUI Creation** [src/GUI/GUI.java L123-L127](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L123-L127) : * Uses `EventQueue.invokeLater()` for thread-safe GUI creation * Anonymous inner class `GUI$3` implements `Runnable` * Creates new `GUI` instance and sets visibility to `true`

**Sources:** [src/GUI/GUI.java L98-L128](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L98-L128)

 [build/classes/GUI/GUI$3.class](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/build/classes/GUI/GUI$3.class)

---

## Form Definition (GUI.form)

The `GUI.form` file is an XML-based form definition used by NetBeans GUI Builder to generate the `initComponents()` method. It specifies component properties, layout constraints, and event handler bindings.

**Form Configuration Settings:**

| Setting | Value | Description |
| --- | --- | --- |
| `defaultCloseOperation` | 3 (`EXIT_ON_CLOSE`) | Window close behavior |
| `formSizePolicy` | 1 | Form sizing policy |
| `generateCenter` | false | Center window on screen |
| Layout | `AbsoluteLayout` | Positioning strategy |
| Design Size | 860 x 480 pixels | Form dimensions |

**Event Handler Definitions:**

[src/GUI/GUI.form L112](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.form#L112-L112)

```

```

[src/GUI/GUI.form L125](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.form#L125-L125)

```

```

**Sources:** [src/GUI/GUI.form L1-L137](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.form#L1-L137)

---

## Summary

The `GUI.GUI` class provides the primary data entry interface for the crud3 application with the following key characteristics:

| Aspect | Summary |
| --- | --- |
| **Purpose** | Student data entry form |
| **Input Fields** | 4 text fields (nombre, apellido, telefono, correo) |
| **Actions** | Save (with validation), Clear fields |
| **Validation** | Client-side empty field checking |
| **Integration** | Creates `model.alumno` instances, calls `services.alumnoDataChange.guardado()` |
| **Feedback** | `JOptionPane` dialogs for validation errors and save results |
| **Entry Point** | Contains `main()` method, application launcher |

**Sources:** [src/GUI/GUI.java L1-L179](https://github.com/axchisan/Crud-MUUUy-simple-en-java-de-hace-a-os/blob/7ec3bd78/src/GUI/GUI.java#L1-L179)
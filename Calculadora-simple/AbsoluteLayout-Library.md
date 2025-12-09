# AbsoluteLayout Library

> **Relevant source files**
> * [dist/lib/AbsoluteLayout.jar](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar)
> * [src/calculadora/Calculadora.form](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form)

This document provides comprehensive technical documentation for the AbsoluteLayout library (`dist/lib/AbsoluteLayout.jar`), a NetBeans-provided layout manager that enables pixel-perfect component positioning in the SimpleCalculator application. This page covers the library's structure, core classes, integration with the calculator's GUI, and distribution strategy.

For information about how the GUI components themselves are organized and function, see [User Interface Components](/ricardo-alan/SimpleCalculator/4.2-user-interface-components). For details about the overall build process that packages this library, see [Distribution and Packaging](/ricardo-alan/SimpleCalculator/5.3-distribution-and-packaging).

---

## Overview

The AbsoluteLayout library is an external dependency from the NetBeans IDE ecosystem that provides absolute positioning capabilities for Swing components. Unlike traditional layout managers (FlowLayout, BorderLayout, GridBagLayout) that dynamically calculate component positions, AbsoluteLayout allows developers to specify exact pixel coordinates for each component.

**Key Characteristics:**

* Package: `org.netbeans.lib.awtextra`
* Distribution: Standalone JAR file (8 KB)
* Primary Classes: `AbsoluteLayout`, `AbsoluteConstraints`
* Purpose: Fixed-size, pixel-perfect UI layouts
* Origin: NetBeans GUI Builder tooling

The SimpleCalculator uses AbsoluteLayout to maintain a fixed 340×570 pixel window with components positioned at exact coordinates, ensuring consistent appearance across all platforms and preventing user resizing.

**Sources:** [dist/lib/AbsoluteLayout.jar L1-L42](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L1-L42)

---

## Library Architecture

```

```

**Diagram: AbsoluteLayout Integration with Application**

The library implements Java's `LayoutManager2` interface, allowing it to function as a standard Swing layout manager. The `AbsoluteConstraints` class encapsulates positioning data (x, y, width, height) for each component, which `AbsoluteLayout` uses to place components at runtime.

**Sources:** [dist/lib/AbsoluteLayout.jar L1-L42](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L1-L42)

 [src/calculadora/Calculadora.form L29-L31](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L29-L31)

---

## Package Structure

```

```

**Diagram: Package Hierarchy**

| Package Component | Purpose |
| --- | --- |
| `org.netbeans` | NetBeans project namespace |
| `lib` | Library/utility code |
| `awtextra` | AWT/Swing extensions |

The library follows standard Java package naming conventions, placing it within the NetBeans organizational namespace. The `awtextra` package name indicates it extends Java's Abstract Window Toolkit (AWT) and Swing functionality.

**Sources:** [dist/lib/AbsoluteLayout.jar L7-L40](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L7-L40)

---

## Core Classes

### AbsoluteLayout

The `AbsoluteLayout` class is the primary layout manager implementation. It extends or implements `LayoutManager2`, providing methods for:

* Adding components with associated constraints
* Calculating preferred/minimum/maximum sizes
* Laying out components based on their constraints
* Invalidating and revalidating layouts

**Typical Usage Pattern:**

```

```

**Sources:** [dist/lib/AbsoluteLayout.jar L18-L22](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L18-L22)

### AbsoluteConstraints

The `AbsoluteConstraints` class stores positioning data for individual components:

| Property | Type | Description |
| --- | --- | --- |
| `x` | int | Horizontal position in pixels |
| `y` | int | Vertical position in pixels |
| `width` | int | Component width (-1 for preferred) |
| `height` | int | Component height (-1 for preferred) |

When width or height is set to `-1`, the layout manager uses the component's preferred size instead of an explicit dimension.

**Sources:** [dist/lib/AbsoluteLayout.jar L14-L17](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L14-L17)

---

## Usage in SimpleCalculator

### Form Definition Structure

The SimpleCalculator's GUI is defined in [src/calculadora/Calculadora.form L1-L784](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L1-L784)

 which extensively uses AbsoluteLayout for both the main frame and nested panels.

```

```

**Diagram: Component Hierarchy with Absolute Positioning**

**Sources:** [src/calculadora/Calculadora.form L29-L46](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L29-L46)

 [src/calculadora/Calculadora.form L138-L152](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L138-L152)

### Layout Declaration in Form XML

Each container using AbsoluteLayout declares it in the form definition:

**Main Frame Layout:**

```

```

**Sources:** [src/calculadora/Calculadora.form L29-L31](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L29-L31)

**Panel Layouts:**
Similar declarations appear for both `jPanel1` [src/calculadora/Calculadora.form L45-L47](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L45-L47)

 and `jPanel2` [src/calculadora/Calculadora.form L150-L152](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L150-L152)

### Component Constraint Specifications

Each component within an AbsoluteLayout container includes a constraint specification:

**Display Panel (jPanel1):**

| Component | X | Y | Width | Height | Purpose |
| --- | --- | --- | --- | --- | --- |
| `txtOperacion` | 10 | 50 | 320 | -1 | Operation display |
| `txtResultado` | 10 | 80 | 320 | 50 | Result display |
| `btn_oscuro` | 300 | 10 | 30 | 20 | Dark mode toggle |
| `jLabel2` | 20 | 0 | 20 | 20 | Minimize button |
| `jLabel3` | 0 | 0 | 20 | 20 | Close button |

**Sources:** [src/calculadora/Calculadora.form L59-L63](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L59-L63)

 [src/calculadora/Calculadora.form L75-L79](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L75-L79)

 [src/calculadora/Calculadora.form L90-L94](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L90-L94)

**Button Panel (jPanel2) - Sample:**

| Component | X | Y | Width | Height | Button Text |
| --- | --- | --- | --- | --- | --- |
| `btn_0` | 20 | 340 | 50 | 50 | "0" |
| `btn_dot` | 100 | 340 | 50 | 50 | "." |
| `btn_igual` | 260 | 340 | 50 | 50 | "=" |
| `btn_1` | 20 | 260 | 50 | 50 | "1" |
| `btn_7` | 20 | 100 | 50 | 50 | "7" |
| `btn_c` | 20 | 20 | 50 | 50 | "C" |

The button grid uses consistent 80-pixel spacing between columns and rows, creating a regular 4×5 grid pattern.

**Sources:** [src/calculadora/Calculadora.form L775-L779](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L775-L779)

 [src/calculadora/Calculadora.form L742-L746](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L742-L746)

 [src/calculadora/Calculadora.form L181-L185](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L181-L185)

### Constraint XML Structure

The form definition uses a specific XML structure for each constraint:

```

```

The `-1` value for width or height indicates the component should use its preferred size.

**Sources:** [src/calculadora/Calculadora.form L775-L779](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L775-L779)

---

## Distribution and Deployment

```

```

**Diagram: Library Distribution Flow**

### Packaging Strategy

The AbsoluteLayout library is distributed alongside the application using the "copy libraries" approach:

1. **Source Location:** The library originates from the NetBeans IDE installation
2. **Build Process:** Ant build scripts copy it to `dist/lib/` during the distribution phase
3. **Runtime Loading:** The main JAR's manifest references the library path
4. **Class Loading:** The JVM loads classes from `dist/lib/AbsoluteLayout.jar` at runtime

This strategy ensures the application remains self-contained and doesn't require NetBeans to be installed on end-user systems.

**Sources:** [dist/lib/AbsoluteLayout.jar L1-L42](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L1-L42)

### JAR Contents

The AbsoluteLayout.jar file structure:

```
META-INF/
  MANIFEST.MF
org/
  netbeans/
    lib/
      awtextra/
        AbsoluteConstraints.class
        AbsoluteLayout.class
```

**File Sizes:**

* Total JAR: ~8 KB
* AbsoluteConstraints.class: ~1 KB
* AbsoluteLayout.class: ~2 KB

The compact size reflects the library's focused purpose: providing only absolute positioning capabilities without additional utilities.

**Sources:** [dist/lib/AbsoluteLayout.jar L1-L42](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L1-L42)

---

## Technical Implementation Details

### Why AbsoluteLayout for This Application

The SimpleCalculator makes deliberate architectural choices that necessitate AbsoluteLayout:

| Design Decision | Rationale | AbsoluteLayout Role |
| --- | --- | --- |
| Fixed window size (340×570) | Consistent calculator appearance | Precise component placement |
| Non-resizable frame | Prevents layout distortion | Static positioning requirements |
| Undecorated window | Custom title bar and controls | Exact pixel control for custom UI |
| Pixel-perfect design | Professional aesthetic | Direct coordinate specification |
| Button grid alignment | Regular 80px spacing | Explicit positioning |

The application sets `resizable=false` [src/calculadora/Calculadora.form L10](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L10-L10)

 and `undecorated=true` [src/calculadora/Calculadora.form L9](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L9-L9)

 creating an environment where dynamic layout managers would provide no benefit while adding complexity.

**Sources:** [src/calculadora/Calculadora.form L4-L11](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L4-L11)

### NetBeans GUI Builder Integration

The NetBeans GUI Builder (Matisse) generates AbsoluteLayout code when designers:

1. Place components using visual drag-and-drop
2. Specify exact coordinates in the property inspector
3. Save the form definition

The builder serializes this into the `.form` XML file with the structure:

```
DesignAbsoluteLayout (design-time representation)
  ↓ (code generation)
AbsoluteLayout (runtime class)
```

The `org.netbeans.modules.form.compat2.layouts.DesignAbsoluteLayout` class [src/calculadora/Calculadora.form L29](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L29-L29)

 is a design-time wrapper that generates code instantiating `org.netbeans.lib.awtextra.AbsoluteLayout` at runtime.

**Sources:** [src/calculadora/Calculadora.form L29-L31](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L29-L31)

 [src/calculadora/Calculadora.form L40-L42](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L40-L42)

### Constraint Resolution

When components use `-1` for dimensions, the layout manager queries the component's preferred size:

**Examples in SimpleCalculator:**

* `txtOperacion`: width=320, height=-1 [src/calculadora/Calculadora.form L61](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L61-L61) * Height calculated from font size (18pt Montserrat)
* `btn_igual`: width=-1, height=-1 with preferredSize=[50,50] [src/calculadora/Calculadora.form L168-L170](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L168-L170) * Uses explicit preferred size property

This approach balances explicit positioning with component-specific sizing needs.

**Sources:** [src/calculadora/Calculadora.form L59-L63](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L59-L63)

 [src/calculadora/Calculadora.form L166-L177](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L166-L177)

### Alternative Layout Managers

For context, here's why standard layout managers were not used:

| Layout Manager | Why Not Used |
| --- | --- |
| `FlowLayout` | No row/column control; reflows on resize |
| `BorderLayout` | Only 5 regions; insufficient for button grid |
| `GridLayout` | Equal cell sizes; inflexible for mixed components |
| `GridBagLayout` | Overly complex for fixed design; runtime calculation overhead |
| `null` (no layout) | Requires manual size management; less maintainable than AbsoluteLayout |

AbsoluteLayout provides the benefits of null layout (explicit positioning) with proper layout manager integration (automatic revalidation, size queries).

**Sources:** [src/calculadora/Calculadora.form L29-L31](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L29-L31)

---

## Runtime Behavior

### Layout Calculation

When the calculator window is displayed, AbsoluteLayout performs these operations for each container:

1. **Component Addition:** Each component is associated with its `AbsoluteConstraints`
2. **Size Resolution:** Queries component preferred sizes when dimensions are `-1`
3. **Positioning:** Sets component bounds using `setBounds(x, y, width, height)`
4. **Validation:** Ensures all components are properly positioned

Since the window is non-resizable, layout calculations occur only once during initialization.

### Performance Characteristics

AbsoluteLayout offers optimal performance for fixed layouts:

* **No recalculation on resize:** Window size is constant
* **Direct positioning:** No constraint solving or space distribution algorithms
* **Minimal overhead:** Simple coordinate-to-bounds mapping
* **Predictable behavior:** Component positions never change unexpectedly

This makes it ideal for the calculator's use case where performance is adequate and layout simplicity is prioritized.

**Sources:** [src/calculadora/Calculadora.form L4-L11](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L4-L11)

---

## Summary

The AbsoluteLayout library is a critical infrastructure component that enables the SimpleCalculator's fixed-size, pixel-perfect design. By providing explicit coordinate control through the `AbsoluteLayout` and `AbsoluteConstraints` classes, it allows the NetBeans GUI Builder to generate maintainable layout code while supporting the application's custom, non-resizable window aesthetic.

**Key Integration Points:**

* Form definition: [src/calculadora/Calculadora.form L29-L31](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L29-L31)
* Component constraints: [src/calculadora/Calculadora.form L40-L779](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L40-L779)
* Distribution: [dist/lib/AbsoluteLayout.jar L1-L42](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L1-L42)
* Build process: References in [Distribution and Packaging](/ricardo-alan/SimpleCalculator/5.3-distribution-and-packaging)

**Sources:** [dist/lib/AbsoluteLayout.jar L1-L42](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/dist/lib/AbsoluteLayout.jar#L1-L42)

 [src/calculadora/Calculadora.form L1-L784](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L1-L784)
# Application Structure

> **Relevant source files**
> * [README.md](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/README.md)
> * [src/calculadora/Calculadora.form](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form)
> * [src/calculadora/Calculadora.java](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java)

## Purpose

This page documents the structural organization of the SimpleCalculator application, focusing on the `Calculadora` class which serves as the central component of the system. It covers class responsibilities, component hierarchy, state management, and method organization. For details on UI components and layout, see [User Interface Components](/ricardo-alan/SimpleCalculator/4.2-user-interface-components). For event handling mechanisms, see [Event Handling](/ricardo-alan/SimpleCalculator/4.4-event-handling). For calculation logic, see [Calculation Engine](/ricardo-alan/SimpleCalculator/4.3-calculation-engine).

---

## Overview

The SimpleCalculator application is structured as a single monolithic class that combines UI definition, event handling, and business logic. The `calculadora.Calculadora` class extends `javax.swing.JFrame` and serves as both the application entry point and the primary UI container.

**Key Characteristics:**

| Aspect | Implementation |
| --- | --- |
| **Primary Class** | `calculadora.Calculadora` |
| **Inheritance** | Extends `javax.swing.JFrame` |
| **Window Properties** | Undecorated, non-resizable, fixed size (340x570 pixels) |
| **Layout Manager** | `org.netbeans.lib.awtextra.AbsoluteLayout` |
| **Pattern** | Monolithic structure with GUI code generation via NetBeans |

**Sources:** [src/calculadora/Calculadora.java L1-L19](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L1-L19)

 [src/calculadora/Calculadora.form L1-L27](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L1-L27)

---

## Class Structure

The `Calculadora` class contains all application logic, UI components, and state management in a single class file. The class structure follows NetBeans' code generation conventions, with clearly demarcated sections for generated and custom code.

```

```

**Sources:** [src/calculadora/Calculadora.java L11-L643](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L11-L643)

---

## Component Hierarchy

The UI is organized into two main panels that divide the application vertically. Components are positioned using absolute coordinates through the NetBeans AbsoluteLayout manager.

### Display Panel (jPanel1)

Located at the top of the window, this panel contains the operation display, result display, window controls, and theme toggle button.

```

```

**Sources:** [src/calculadora/Calculadora.form L33-L137](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L33-L137)

 [src/calculadora/Calculadora.java L58-L100](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L58-L100)

### Button Panel (jPanel2)

Located below the display panel, this panel contains all calculator buttons arranged in a 4-column grid layout.

```

```

**Button Grid Layout:**

| x=20 | x=100 | x=180 | x=260 |
| --- | --- | --- | --- |
| `btn_c` | `btn_exp` | `btn_porcentaje` | `btn_division` |
| `btn_7` | `btn_8` | `btn_9` | `btn_multi` |
| `btn_4` | `btn_5` | `btn_6` | `btn_resta` |
| `btn_1` | `btn_2` | `btn_3` | `btn_suma` |
| `btn_0` | `btn_dot` | - | `btn_igual` |

**Sources:** [src/calculadora/Calculadora.form L138-L783](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.form#L138-L783)

 [src/calculadora/Calculadora.java L103-L410](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L103-L410)

---

## State Management

The application maintains minimal state through instance variables. State is primarily managed through UI component text values and a single boolean flag for theme mode.

```

```

**State Variables:**

| Variable | Type | Purpose | Scope |
| --- | --- | --- | --- |
| `modoOscuro` | `boolean` | Tracks current theme mode | Instance field at [line 515](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/line 515) |
| `sem` | `ScriptEngineManager` | Manages JavaScript engine | Instance field at [line 13](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/line 13) |
| `se` | `ScriptEngine` | Evaluates expressions | Instance field at [line 14](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/line 14) |
| `txtOperacion` | `JLabel` | Stores current operation string | Component field |
| `txtResultado` | `JLabel` | Displays evaluation result | Component field |

**Sources:** [src/calculadora/Calculadora.java L13-L14](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L13-L14)

 [src/calculadora/Calculadora.java L515](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L515-L515)

 [src/calculadora/Calculadora.java L26-L27](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L26-L27)

---

## Core Methods and Responsibilities

The `Calculadora` class organizes its functionality into several categories of methods, each handling specific aspects of the application behavior.

### Initialization Methods

```

```

**Sources:** [src/calculadora/Calculadora.java L16-L413](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L16-L413)

### Input Handling Methods

The application processes user input through multiple event handler methods and a central aggregation method.

| Method | Lines | Purpose | Behavior |
| --- | --- | --- | --- |
| `addNumber(String)` | 611-613 | Appends input to operation display | Concatenates string to `txtOperacion` |
| `btn_0ActionPerformed()` to `btn_9ActionPerformed()` | 425-489 | Handle digit button clicks | Call `addNumber()`, then trigger evaluation |
| `btn_dotActionPerformed()` | 491-494 | Handle decimal point | Call `addNumber(".")`, then trigger evaluation |
| `btn_sumaActionPerformed()` | 507-509 | Handle addition operator | Call `addNumber("+")` |
| `btn_restaActionPerformed()` | 511-513 | Handle subtraction operator | Call `addNumber("-")` |
| `btn_multiActionPerformed()` | 446-449 | Handle multiplication operator | Call `addNumber("*")` |
| `btn_divisionActionPerformed()` | 441-444 | Handle division operator | Call `addNumber("/")` |
| `btn_porcentajeActionPerformed()` | 415-418 | Handle percentage operator | Call `addNumber("%")` |

**Real-time Evaluation Pattern:**

All digit button handlers follow the same pattern:

1. Append digit/operator to `txtOperacion` via `addNumber()`
2. Programmatically trigger `btn_igual.doClick()` for immediate evaluation

**Sources:** [src/calculadora/Calculadora.java L415-L513](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L415-L513)

 [src/calculadora/Calculadora.java L611-L613](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L611-L613)

### Evaluation and Display Methods

```

```

**Evaluation Logic at [lines 496-505](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/lines 496-505)

:**

* Uses `ScriptEngine.eval()` to evaluate JavaScript expression
* Catches exceptions silently (commented out clear operation)
* Converts result to string for display
* Updates `txtResultado` with computed value

**Sources:** [src/calculadora/Calculadora.java L496-L505](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L496-L505)

 [src/calculadora/Calculadora.java L611-L613](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L611-L613)

### Utility and Control Methods

| Method | Lines | Responsibility |
| --- | --- | --- |
| `btn_cActionPerformed()` | 420-423 | Clears both `txtOperacion` and `txtResultado` |
| `btn_expActionPerformed()` | 435-439 | Removes last character (backspace), triggers re-evaluation |
| `jLabel3MouseClicked()` | 557-559 | Closes application via `dispose()` |
| `jLabel2MouseClicked()` | 561-563 | Minimizes window via `setState(Frame.ICONIFIED)` |
| `cambiarColorBtn1(JButton)` | 597-602 | Updates button to dark theme style 1 |
| `cambiarColorBtn2(JButton)` | 604-609 | Updates button to dark theme style 2 |

**Sources:** [src/calculadora/Calculadora.java L420-L563](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L420-L563)

 [src/calculadora/Calculadora.java L597-L609](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L597-L609)

### Theme Management

The theme toggle implementation at [lines 517-555](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/lines 517-555)

 follows a replacement pattern rather than updating the existing instance:

```

```

**Dark Theme Application Process:**

1. Changes background colors of `jPanel1` (#212b41) and `jPanel2` (#2e3951)
2. Changes text foreground colors to #0db387 for `txtOperacion` and `txtResultado`
3. Calls `cambiarColorBtn1()` for operator buttons (7 calls)
4. Calls `cambiarColorBtn2()` for digit buttons (11 calls)
5. Updates theme toggle icon and equals button icon
6. Sets `modoOscuro = true`

**Light Theme Restoration:**

* Creates new `Calculadora` instance (returns to default light theme)
* Disposes current instance
* Shows new instance

**Sources:** [src/calculadora/Calculadora.java L517-L555](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L517-L555)

 [src/calculadora/Calculadora.java L597-L609](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L597-L609)

---

## Application Entry Point

The application follows standard Swing initialization patterns with Look and Feel configuration and Event Dispatch Thread invocation.

```

```

**Initialization Sequence:**

1. **Look and Feel Setup** [lines 571-587](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/lines 571-587) * Iterates through installed L&F options * Attempts to set "Nimbus" L&F if available * Catches and logs any configuration exceptions * Falls back to system default if Nimbus unavailable
2. **Frame Creation** [lines 590-594](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/lines 590-594) * Invokes on Event Dispatch Thread via `EventQueue.invokeLater()` * Creates `Calculadora` instance * Calls `setVisible(true)` to display window
3. **Constructor Execution** [lines 16-19](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/lines 16-19) * Calls `initComponents()` (NetBeans-generated UI setup) * Centers window on screen with `setLocationRelativeTo(null)`

**Sources:** [src/calculadora/Calculadora.java L565-L595](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L565-L595)

 [src/calculadora/Calculadora.java L16-L19](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L16-L19)

---

## Code Organization Conventions

The `Calculadora.java` file follows NetBeans IDE code generation conventions with specific regions marked by comments:

| Region | Marker | Lines | Content |
| --- | --- | --- | --- |
| Generated Code Block | `// <editor-fold defaultstate="collapsed" desc="Generated Code">` | 22-413 | `initComponents()` method with all UI setup |
| Event Handler Stubs | `//GEN-FIRST:event_*` / `//GEN-LAST:event_*` | Various | Individual event handler method bodies |
| Variables Declaration | `// Variables declaration - do not modify` | 615-642 | All UI component field declarations |
| Custom Code | (unmarked) | 13-21, 415-614 | Developer-written logic |

**Developer-Editable Sections:**

* Constructor body [lines 16-19](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/lines 16-19)
* Event handler implementations between `GEN-FIRST` and `GEN-LAST` markers
* Custom helper methods `addNumber()`, `cambiarColorBtn1()`, `cambiarColorBtn2()` [lines 597-613](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/lines 597-613)
* `main()` method body [lines 565-595](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/lines 565-595)

**NetBeans-Managed Sections:**

* Component instantiation and configuration
* Layout constraints and absolute positioning
* Event listener registration
* Component variable declarations

**Sources:** [src/calculadora/Calculadora.java L1-L643](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L1-L643)
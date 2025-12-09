# Theme System

> **Relevant source files**
> * [README.md](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/README.md)
> * [src/calculadora/Calculadora.java](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java)
> * [src/images/darkmode_1.png](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/images/darkmode_1.png)
> * [src/images/darkmode_2.png](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/images/darkmode_2.png)

## Purpose and Scope

This document covers the visual theme system in SimpleCalculator, which enables users to toggle between light and dark color modes. The theme system manages background colors, text colors, and button icons across the entire application interface. For information about the UI component structure and layout, see [User Interface Components](/ricardo-alan/SimpleCalculator/4.2-user-interface-components). For details about button event handling, see [Event Handling](/ricardo-alan/SimpleCalculator/4.4-event-handling).

## Overview

The SimpleCalculator application provides two visual themes: a default light mode and an optional dark mode. The theme system is implemented entirely within the `Calculadora` class and operates by dynamically modifying UI component properties and swapping icon resources when the user clicks the theme toggle button.

| Theme Aspect | Light Mode | Dark Mode |
| --- | --- | --- |
| Default State | Yes | No |
| Toggle Button Icon | `darkmode_1.png` | `darkmode_2.png` |
| Display Panel Background | `#F4FDFB` (RGB: 244, 253, 251) | `#212B41` (RGB: 33, 43, 65) |
| Button Panel Background | `#FFFFFF` (white) | `#2E3951` (RGB: 46, 57, 81) |
| Text Color | `#373E47` (RGB: 55, 62, 71) | `#0DB387` (RGB: 13, 179, 135) |
| Activation Method | Application initialization | User click on `btn_oscuro` |

**Sources:**

* [README.md L12](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/README.md#L12-L12)
* [src/calculadora/Calculadora.java L515-L555](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L515-L555)

## Theme Toggle Mechanism

The theme toggle functionality is controlled by the `btn_oscuro` button and the `modoOscuro` boolean flag. When the user clicks the theme toggle button, the application either applies dark mode styling or recreates itself to return to light mode.

### Theme Toggle Flow Diagram

```

```

**Sources:**

* [src/calculadora/Calculadora.java L515](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L515-L515)
* [src/calculadora/Calculadora.java L517-L555](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L517-L555)

### Implementation Details

The theme toggle is handled by the `btn_oscuroActionPerformed` event handler located at [src/calculadora/Calculadora.java L517-L555](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L517-L555)

 The implementation follows an asymmetric approach:

* **Light to Dark:** Applies programmatic styling changes to all components in place
* **Dark to Light:** Creates a new `Calculadora` instance and disposes the current one

This design means the default constructor initializes the application in light mode, and reverting from dark mode to light mode effectively resets the application state.

**Sources:**

* [src/calculadora/Calculadora.java L517-L555](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L517-L555)

## Color Scheme Management

The theme system manages colors for three primary UI regions: display panels, button panels, and text elements. All colors are specified as hexadecimal strings and applied using `Color.decode()`.

### Color Application Map

```

```

**Sources:**

* [src/calculadora/Calculadora.java L58](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L58-L58)
* [src/calculadora/Calculadora.java L103](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L103-L103)
* [src/calculadora/Calculadora.java L519-L522](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L519-L522)

### Text Color Transformations

When dark mode is activated, the following text color changes are applied:

| Component | Property | Code Reference | Light Mode | Dark Mode |
| --- | --- | --- | --- | --- |
| `txtOperacion` | foreground | Line 521 | `#373E47` | `#0DB387` |
| `txtResultado` | foreground | Line 522 | `#373E47` | `#0DB387` |
| Type 1 Buttons | foreground | Line 601 | `#373E47` | `#0DB387` |
| Type 2 Buttons | foreground | Line 608 | `#373E47` | `#96A8A0` |
| `btn_igual` | foreground | Line 546 | White | `#2E3951` |

**Sources:**

* [src/calculadora/Calculadora.java L521-L522](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L521-L522)
* [src/calculadora/Calculadora.java L546](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L546-L546)
* [src/calculadora/Calculadora.java L601](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L601-L601)
* [src/calculadora/Calculadora.java L608](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L608-L608)

## Icon Management

The theme system manages multiple icon sets for different button states and themes. Each button has three states (default, pressed, rollover) and potentially different icons for light and dark modes.

### Icon Resource Mapping

```

```

**Sources:**

* [src/calculadora/Calculadora.java L71](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L71-L71)
* [src/calculadora/Calculadora.java L108-L114](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L108-L114)
* [src/calculadora/Calculadora.java L542-L545](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L542-L545)
* [src/calculadora/Calculadora.java L598-L608](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L598-L608)
* [src/images/darkmode_1.png](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/images/darkmode_1.png)
* [src/images/darkmode_2.png](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/images/darkmode_2.png)

### Button Type Classification

The calculator uses three distinct button types, each with its own icon set:

| Button Type | Examples | Light Default Icon | Dark Default Icon | Purpose |
| --- | --- | --- | --- | --- |
| Type 1 | `btn_c`, `btn_exp`, `btn_porcentaje`, `btn_division`, `btn_multi`, `btn_resta`, `btn_suma` | `btn1.png` | `btn1_dark.png` | Operations and clear functions |
| Type 2 | `btn_0` through `btn_9`, `btn_dot` | `btn2.png` | `btn2_dark.png` | Numeric input and decimal point |
| Type 3 | `btn_igual` | `btn3.png` | `btn3_dark.png` | Equals/execute operation |

**Sources:**

* [src/calculadora/Calculadora.java L523-L540](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L523-L540)
* [src/calculadora/Calculadora.java L543-L545](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L543-L545)

## Button Styling Helper Methods

The theme system provides two helper methods that encapsulate the logic for updating button appearances. These methods are invoked during dark mode activation.

### Helper Method: cambiarColorBtn1

```

```

This method is defined at [src/calculadora/Calculadora.java L597-L602](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L597-L602)

 and is applied to operation buttons (`btn_multi`, `btn_suma`, `btn_resta`, `btn_exp`, `btn_division`, `btn_c`, `btn_porcentaje`).

**Sources:**

* [src/calculadora/Calculadora.java L597-L602](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L597-L602)
* [src/calculadora/Calculadora.java L523-L529](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L523-L529)

### Helper Method: cambiarColorBtn2

```

```

This method is defined at [src/calculadora/Calculadora.java L604-L609](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L604-L609)

 and is applied to numeric and decimal buttons (`btn_1` through `btn_9`, `btn_0`, `btn_dot`).

**Sources:**

* [src/calculadora/Calculadora.java L604-L609](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L604-L609)
* [src/calculadora/Calculadora.java L530-L540](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L530-L540)

## Theme State Management

The theme state is managed through a single boolean instance variable and is not persisted between application sessions.

### State Variable

| Variable | Type | Initial Value | Location | Purpose |
| --- | --- | --- | --- | --- |
| `modoOscuro` | `boolean` | `false` | Line 515 | Tracks current theme state |

When the application starts, `modoOscuro` is always `false`, initializing the application in light mode. The variable is set to `true` at [src/calculadora/Calculadora.java L547](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L547-L547)

 after dark mode styling is applied.

**Sources:**

* [src/calculadora/Calculadora.java L515](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L515-L515)
* [src/calculadora/Calculadora.java L547](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L547-L547)

## Theme Toggle Button

The theme toggle button (`btn_oscuro`) is a special UI control positioned in the display panel that shows different icons depending on the current theme state.

### Theme Button Component Mapping

```

```

The button is initialized at [src/calculadora/Calculadora.java L71-L77](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L71-L77)

 with the `darkmode_1.png` icon. When dark mode is activated, the icon changes to `darkmode_2.png` at [src/calculadora/Calculadora.java L542](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L542-L542)

**Sources:**

* [src/calculadora/Calculadora.java L71-L77](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L71-L77)
* [src/calculadora/Calculadora.java L542](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L542-L542)

## Asymmetric Theme Implementation

The theme system uses an asymmetric implementation pattern where activation and deactivation follow different code paths.

### Theme Transition Comparison

| Aspect | Light → Dark | Dark → Light |
| --- | --- | --- |
| Approach | In-place modification | Instance recreation |
| Component Changes | 24 individual property changes | All components reset |
| Method Calls | 11 helper method invocations | 1 constructor call |
| Memory Impact | Existing objects reused | Old frame disposed, new frame created |
| Code Location | Lines 519-547 | Lines 549-551 |

The recreation approach for returning to light mode at [src/calculadora/Calculadora.java L549-L551](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L549-L551)

 is simpler than reversing all individual changes but results in losing any calculation state (though the display fields would already show the results).

**Sources:**

* [src/calculadora/Calculadora.java L519-L547](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L519-L547)
* [src/calculadora/Calculadora.java L549-L551](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L549-L551)

## Complete Component Update Sequence

When dark mode is activated, the following sequence of component updates occurs:

```

```

**Sources:**

* [src/calculadora/Calculadora.java L517-L555](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L517-L555)
* [src/calculadora/Calculadora.java L523-L546](https://github.com/ricardo-alan/SimpleCalculator/blob/e9524f29/src/calculadora/Calculadora.java#L523-L546)
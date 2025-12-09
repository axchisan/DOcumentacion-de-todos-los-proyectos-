# Custom Form Widgets

> **Relevant source files**
> * [.gitignore](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/.gitignore)
> * [lib/screens/login_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart)
> * [lib/screens/registration_screen.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart)
> * [lib/widgets/custom_button.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_button.dart)
> * [lib/widgets/custom_textfield.dart](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_textfield.dart)

## Purpose and Scope

This document provides a comprehensive API reference for the custom form widgets used throughout the SENA Digital ID Card application: `CustomTextField` and `CustomButton`. These reusable components provide consistent styling, validation capabilities, and user interaction patterns across all authentication and data entry screens.

For information about the overall UI component architecture, see [UI Components Library](/axchisan/AppGestionCarnetsSENA/6-ui-components-library). For theming and color constants used by these widgets, see [Theme and Styling](/axchisan/AppGestionCarnetsSENA/6.3-theme-and-styling). For branding components like `SenaLogo`, see [Branding and Visual Components](/axchisan/AppGestionCarnetsSENA/6.2-branding-and-visual-components).

---

## CustomTextField Widget

The `CustomTextField` widget is a stateful component that wraps Flutter's `TextFormField` with standardized SENA branding, validation support, and password visibility toggling.

### Component Definition

**Sources:** [lib/widgets/custom_textfield.dart L4-L24](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_textfield.dart#L4-L24)

| Property | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `label` | `String` | Yes | - | Label text displayed above the input field |
| `hint` | `String` | Yes | - | Placeholder text shown when field is empty |
| `isPassword` | `bool` | No | `false` | Enables password obscuring and visibility toggle |
| `controller` | `TextEditingController?` | No | `null` | Controller for reading/writing field value |
| `validator` | `String? Function(String?)?` | No | `null` | Validation function returning error message or null |
| `keyboardType` | `TextInputType` | No | `TextInputType.text` | Keyboard type for mobile input |

### State Management and Password Visibility

The widget maintains internal state `_obscureText` to control password visibility toggling. When `isPassword` is `true`, a suffix icon button appears that toggles between `Icons.visibility` and `Icons.visibility_off`.

```

```

**Sources:** [lib/widgets/custom_textfield.dart L26-L28](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_textfield.dart#L26-L28)

 [lib/widgets/custom_textfield.dart L47](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_textfield.dart#L47-L47)

 [lib/widgets/custom_textfield.dart L71-L82](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_textfield.dart#L71-L82)

### Visual Styling

The widget applies consistent SENA branding through the following styling properties:

| Element | Style Property | Value |
| --- | --- | --- |
| Label | `color` | `AppColors.black` |
| Label | `fontSize` | `16` |
| Label | `fontWeight` | `FontWeight.w500` |
| Border (all states) | `color` | `AppColors.senaGreen` |
| Border | `width` | `2` |
| Border | `borderRadius` | `8` |
| Suffix Icon | `color` | `AppColors.gray` |

**Sources:** [lib/widgets/custom_textfield.dart L33-L40](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_textfield.dart#L33-L40)

 [lib/widgets/custom_textfield.dart L48-L70](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_textfield.dart#L48-L70)

### Widget Structure Diagram

```

```

**Sources:** [lib/widgets/custom_textfield.dart L29-L88](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_textfield.dart#L29-L88)

### Usage Examples from Codebase

#### Basic Text Input

**Sources:** [lib/screens/registration_screen.dart L303-L313](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L303-L313)

```

```

#### Numeric Input with Validation

**Sources:** [lib/screens/login_screen.dart L129-L143](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L129-L143)

```

```

#### Password Input with Obscuring

**Sources:** [lib/screens/login_screen.dart L145-L156](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L145-L156)

```

```

#### Email Input with Format Validation

**Sources:** [lib/screens/registration_screen.dart L315-L329](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L315-L329)

```

```

#### Optional Field (No Validation)

**Sources:** [lib/screens/registration_screen.dart L401-L405](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L401-L405)

```

```

---

## CustomButton Widget

The `CustomButton` widget is a stateless component that provides standardized button styling with support for outlined variants, loading states, and optional icons.

### Component Definition

**Sources:** [lib/widgets/custom_button.dart L4-L18](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_button.dart#L4-L18)

| Property | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `text` | `String` | Yes | - | Button label text |
| `onPressed` | `VoidCallback?` | No | `null` | Callback executed when button is tapped |
| `isOutlined` | `bool` | No | `false` | Uses outlined style instead of filled |
| `icon` | `IconData?` | No | `null` | Optional icon displayed after text |
| `isLoading` | `bool` | No | `false` | Shows loading spinner and disables interaction |

### Button Variants and Styling

The widget supports two visual variants determined by the `isOutlined` property:

#### Filled Variant (Default)

| Property | Value |
| --- | --- |
| Background Color | `AppColors.senaGreen` |
| Text Color | `AppColors.white` |
| Border | None |
| Elevation | `2` |

#### Outlined Variant

| Property | Value |
| --- | --- |
| Background Color | `AppColors.white` |
| Text Color | `AppColors.senaGreen` |
| Border | `AppColors.senaGreen`, width `2` |
| Elevation | `0` |

#### Common Properties (Both Variants)

| Property | Value |
| --- | --- |
| Width | `double.infinity` (full width) |
| Height | `50` |
| Border Radius | `8` |
| Text Font Size | `16` |
| Text Font Weight | `FontWeight.w600` |
| Icon Size | `20` |

**Sources:** [lib/widgets/custom_button.dart L22-L36](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_button.dart#L22-L36)

### Loading State Behavior

When `isLoading` is `true`:

1. The `onPressed` callback is disabled (set to `null`)
2. Text and icon are replaced with a `CircularProgressIndicator`
3. Progress indicator dimensions: `20x20` pixels
4. Progress indicator stroke width: `2`
5. Progress indicator color: `AppColors.white`

**Sources:** [lib/widgets/custom_button.dart L26](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_button.dart#L26-L26)

 [lib/widgets/custom_button.dart L38-L46](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_button.dart#L38-L46)

### Button State Diagram

```

```

**Sources:** [lib/widgets/custom_button.dart L20-L66](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_button.dart#L20-L66)

### Usage Examples from Codebase

#### Primary Action Button with Loading State

**Sources:** [lib/screens/login_screen.dart L158-L163](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L158-L163)

```

```

#### Outlined Secondary Action Button

**Sources:** [lib/screens/login_screen.dart L182-L188](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L182-L188)

```

```

#### Validation Action Button

**Sources:** [lib/screens/registration_screen.dart L257-L262](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L257-L262)

```

```

#### Button with Icon for Media Selection

**Sources:** [lib/screens/registration_screen.dart L416-L421](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L416-L421)

```

```

#### Submit Button in Form

**Sources:** [lib/screens/registration_screen.dart L459-L463](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L459-L463)

```

```

---

## Integration with Form Validation

Both widgets are designed to work seamlessly with Flutter's `Form` widget and validation system. The following diagram illustrates the typical integration pattern used across authentication screens:

```

```

**Sources:** [lib/screens/login_screen.dart L49-L92](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L49-L92)

 [lib/screens/registration_screen.dart L125-L203](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L125-L203)

### Common Validation Patterns

The codebase demonstrates several reusable validation patterns:

| Validation Type | Implementation | Example Source |
| --- | --- | --- |
| Required Field | Check `value == null \|\| value.isEmpty` | [lib/screens/login_screen.dart L135-L137](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L135-L137) |
| Minimum Length | Check `value.length < n` | [lib/screens/login_screen.dart L138-L140](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L138-L140) |
| Email Format | Check `!value.contains('@')` | [lib/screens/registration_screen.dart L324-L326](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L324-L326) |
| Password Match | Check `value != _otherController.text` | [lib/screens/registration_screen.dart L452-L454](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L452-L454) |
| Password Strength | Check `value.length < 6` | [lib/screens/registration_screen.dart L436-L438](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L436-L438) |

---

## Layout Patterns and Spacing

Both widgets follow consistent spacing patterns when used in forms:

```

```

**Sources:** [lib/screens/login_screen.dart L128-L188](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L128-L188)

 [lib/screens/registration_screen.dart L240-L463](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L240-L463)

### Standard Spacing Values

| Context | Spacing Value |
| --- | --- |
| Between text fields | `SizedBox(height: 20)` |
| Between field and primary button | `SizedBox(height: 30)` |
| Between buttons | `SizedBox(height: 20)` |
| Above form section | `SizedBox(height: 40)` |
| Internal label-to-field spacing | `SizedBox(height: 8)` (built into CustomTextField) |

**Sources:** [lib/widgets/custom_textfield.dart L41](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/widgets/custom_textfield.dart#L41-L41)

 [lib/screens/login_screen.dart L144-L157](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L144-L157)

---

## Controller Management and Disposal

Both widgets support optional `TextEditingController` instances for programmatic text access. Screens using these widgets must properly dispose of controllers to prevent memory leaks:

**Sources:** [lib/screens/login_screen.dart L21-L32](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L21-L32)

 [lib/screens/registration_screen.dart L22-L59](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/registration_screen.dart#L22-L59)

```

```

### Reading Controller Values

Controllers are typically accessed during form submission:

**Sources:** [lib/screens/login_screen.dart L66-L67](https://github.com/axchisan/AppGestionCarnetsSENA/blob/9eb64390/lib/screens/login_screen.dart#L66-L67)

```

```
# Visual Assets and Typography

> **Relevant source files**
> * [magician project1/datafiles/gothic-pixel-font.ttf](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf)
> * [magician project1/fonts/fnt_medieval/fnt_medieval.yy](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy)
> * [magician project1/mague.yyp](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp)

## Purpose and Scope

This document provides an overview of Haunted Hollow's visual asset organization with a focus on the typography system. It describes the two font resources used in the game, their configuration, and how they integrate into GameMaker's text rendering pipeline. For detailed glyph data and configuration of individual font assets, see [Medieval Font System](/axchisan/Haunted_hollow/7.1-medieval-font-system) and [Gothic Pixel Font](/axchisan/Haunted_hollow/7.2-gothic-pixel-font). For other visual assets like sprites and tilesets, see [Level Design and Environment](/axchisan/Haunted_hollow/6-level-design-and-environment).

---

## Font Resource Architecture

Haunted Hollow employs a dual-font system to achieve distinct visual aesthetics for different gameplay contexts. The project contains two font resources: a GameMaker-processed bitmap font (`fnt_medieval`) and a raw TrueType font file (`gothic-pixel-font.ttf`).

```

```

**Sources:**

* [magician L1-L143](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L1-L143)
* [magician L1-L143](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L1-L143)
* [magician L1-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L33)

---

## Font Resource Comparison

| Property | fnt_medieval | gothic-pixel-font.ttf |
| --- | --- | --- |
| **Resource Type** | GMFont (GameMaker Asset) | TrueType Font (Raw File) |
| **File Location** | `fonts/fnt_medieval/` | `datafiles/` |
| **Base Typeface** | Bradley Hand ITC | Gothic Pixel Font |
| **Font Size** | 18.0 pt | Variable (runtime) |
| **Style** | Bold, Handwritten | Regular, Pixel Art |
| **Character Set** | ASCII 32-127 + glyph 9647 | Full Unicode support |
| **Rendering Method** | Pre-rendered bitmap glyphs | Dynamic TTF rendering |
| **Anti-aliasing** | Enabled (value: 1) | N/A (pixel-perfect) |
| **Line Height** | 30 pixels | Determined by font metrics |
| **Primary Use Case** | In-game dialogue, UI text | Question room text, headers |
| **License** | Commercial (Bradley Hand ITC) | Creative Commons Attribution |

**Sources:**

* [magician L4-L142](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L4-L142)
* [magician L30-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L30-L33)

---

## GameMaker Font Configuration

The `fnt_medieval` font asset is configured through a GameMaker Font resource file (`.yy` format) that defines all rendering parameters and glyph positioning data.

```

```

**Sources:**

* [magician L1-L143](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L1-L143)

---

## Text Rendering Pipeline

GameMaker's text rendering system processes font assets through a multi-stage pipeline that converts font definitions into rendered glyphs on screen.

```

```

**Sources:**

* [magician L14-L111](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L14-L111)
* [magician L77-L78](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L77-L78)

---

## Glyph Metrics Structure

The `fnt_medieval.yy` file contains detailed positioning data for each character. Each glyph entry defines how that character should be positioned and rendered.

### Glyph Data Format

Each glyph in the `glyphs` object follows this structure:

```
"<charcode>": {
  "character": <unicode_value>,
  "h": <height_pixels>,
  "offset": <x_offset>,
  "shift": <horizontal_advance>,
  "w": <width_pixels>,
  "x": <texture_x_position>,
  "y": <texture_y_position>
}
```

### Example Glyph Entries

| Character | Code | Width | Height | Shift | Offset | Texture X | Texture Y |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Space | 32 | 6 | 30 | 6 | 0 | 2 | 2 |
| A | 65 | 18 | 30 | 17 | -1 | 211 | 130 |
| a | 97 | 9 | 30 | 12 | 1 | 54 | 2 |
| 0 | 48 | 12 | 30 | 13 | 0 | 16 | 98 |
| ! | 33 | 3 | 30 | 7 | 2 | 195 | 98 |

**Sources:**

* [magician L14-L111](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L14-L111)

---

## Font Asset Registration

Both font resources are registered in the GameMaker project manifest, making them available to game objects at runtime.

```

```

**Sources:**

* [magician L77-L87](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L77-L87)

---

## Usage Contexts

The two font systems serve different purposes within the game's visual hierarchy:

### fnt_medieval Usage

* **Dialogue System**: Primary font for `Obj_textbox` dialogue rendering
* **NPC Interactions**: Speech text for NPCs (`Obj_npc0` through `Obj_npc6`)
* **HUD Elements**: Health display (`Obj_healt_bar`), system messages
* **In-game Notifications**: Item pickups, status messages

### gothic-pixel-font.ttf Usage

* **Educational Content**: Question text in rooms `Room_question1` through `Room_question6`
* **Headers and Titles**: Section headers in quiz interfaces
* **Pixel Art Aesthetic**: Reinforces retro visual style for educational components
* **Static Text**: Pre-rendered text in sprites or background elements

**Sources:**

* [magician L121-L128](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L121-L128)
* [magician L166-L171](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L166-L171)

---

## Font Rendering Parameters

The `fnt_medieval` asset defines several rendering parameters that control how text appears on screen:

| Parameter | Value | Purpose |
| --- | --- | --- |
| `antiAlias` | 1 (enabled) | Smooths glyph edges for readability |
| `ascender` | 20 | Distance from baseline to tallest glyph |
| `ascenderOffset` | 0 | Vertical adjustment for baseline |
| `lineHeight` | 30 | Vertical spacing between lines |
| `kerningPairs` | [] (empty) | No special character pair spacing |
| `applyKerning` | 0 (disabled) | Kerning not applied |
| `pointRounding` | 0 | No coordinate rounding |
| `hinting` | 0 (disabled) | No grid-fitting hints applied |
| `interpreter` | 0 | Default glyph interpreter |

**Sources:**

* [magician L4-L125](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L4-L125)

---

## Character Coverage

The `fnt_medieval` font includes two character ranges:

1. **Standard ASCII Range (32-127)**: Contains 96 printable characters including uppercase, lowercase, numbers, and punctuation
2. **Default Character (9647)**: Unicode block character ▯ used as a fallback when an unavailable character is requested

### Character Set Breakdown

| Range | Characters | Purpose |
| --- | --- | --- |
| 32-47 | Space, !"#$%&'()*+,-./ | Punctuation and symbols |
| 48-57 | 0123456789 | Numeric digits |
| 58-64 | :;<=>?@ | Additional punctuation |
| 65-90 | ABCDEFGHIJKLMNOPQRSTUVWXYZ | Uppercase letters |
| 91-96 | []^_` | Brackets and symbols |
| 97-122 | abcdefghijklmnopqrstuvwxyz | Lowercase letters |
| 123-127 | {\|}~ | Final punctuation |
| 9647 | ▯ | Default/missing glyph |

**Sources:**

* [magician L126-L129](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L126-L129)
* [magician L15-L110](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L15-L110)

---

## Texture Atlas Organization

The pre-rendered glyph texture for `fnt_medieval` uses a packed atlas layout to efficiently store all character visuals. The atlas coordinates are specified in the glyph data, allowing GameMaker to quickly copy character regions during text rendering without re-rasterizing the font.

```

```

**Sources:**

* [magician L64](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L64-L64)  (Example: uppercase 'A' glyph data)

---

## Font Asset Properties Summary

The following properties are defined in the GMFont resource structure:

| Property | Type | fnt_medieval Value |
| --- | --- | --- |
| `$GMFont` | Resource Type Identifier | "" |
| `%Name` | Resource Name | "fnt_medieval" |
| `name` | Internal Name | "fnt_medieval" |
| `fontName` | Source Typeface | "Bradley Hand ITC" |
| `size` | Point Size | 18.0 |
| `bold` | Bold Style | true |
| `italic` | Italic Style | false |
| `canGenerateBitmap` | Regeneration Allowed | true |
| `charset` | Character Set Type | 0 (ASCII) |
| `first` | First Character | 0 |
| `last` | Last Character | 0 |
| `includeTTF` | Include TTF Data | false |
| `maintainGms1Font` | Legacy Compatibility | false |
| `regenerateBitmap` | Auto-regenerate | false |
| `usesSDF` | Signed Distance Field | false |
| `sdfSpread` | SDF Spread Radius | 8 |
| `styleName` | Font Style | "Regular" |
| `resourceType` | GameMaker Type | "GMFont" |
| `resourceVersion` | Format Version | "2.0" |

**Sources:**

* [magician L1-L143](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L1-L143)
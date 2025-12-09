# Gothic Pixel Font

> **Relevant source files**
> * [magician project1/datafiles/gothic-pixel-font.ttf](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf)

## Purpose and Scope

This document covers the Gothic Pixel TrueType font asset used in Haunted Hollow for retro-styled text rendering. This is one of two custom fonts in the project that contributes to the game's gothic aesthetic.

For information about the Bradley Hand ITC medieval font used for dialogue and UI elements, see [Medieval Font System](/axchisan/Haunted_hollow/7.1-medieval-font-system).

---

## Overview

The Gothic Pixel Font is a pixel-art style TrueType font file that provides a retro gothic aesthetic consistent with the game's haunted dungeon theme. Unlike the medieval font which is embedded as a GameMaker font resource, this font exists as a standalone TrueType file in the project's datafiles directory.

**Key Characteristics:**

* **Location:** `magician project1/datafiles/gothic-pixel-font.ttf`
* **Style:** Pixel art with gothic/modular design
* **Designer:** leonardocosta via FontStruct
* **License:** Creative Commons Attribution 3.0
* **Format:** TrueType Font (.ttf)

**Sources:** [magician L30-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L30-L33)

---

## Font Metadata

The TrueType font file contains embedded metadata that defines its identity, licensing, and technical properties:

| Property | Value |
| --- | --- |
| **Copyright** | Copyright leonardocosta 2016 |
| **Font Family** | Blox Gothic Modular |
| **Full Name** | Gothic Pixel Font |
| **Style** | Regular |
| **Version** | 1.0 |
| **PostScript Name** | Gothic-Pixel-Font |
| **Created With** | FontStruct |
| **Base Design** | Blox Gothic Modular |
| **Sample Text** | "Five big quacking zephyrs jolt my wax bed" |

**Sources:** [magician L30-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L30-L33)

---

## Licensing Information

The Gothic Pixel Font is distributed under the **Creative Commons Attribution 3.0 License**, which permits:

* ✓ Commercial use
* ✓ Modification
* ✓ Distribution
* ✓ Private use

**Requirements:**

* Attribution to the original creator (leonardocosta) must be provided

**License Details:**

* **License Type:** Creative Commons Attribution
* **License URL:** [http://creativecommons.org/licenses/by/3.0/](http://creativecommons.org/licenses/by/3.0/)
* **FontStruct URL:** [https://fontstruct.com/fontstructions/show/1344429/gothic-pixel-font-1](https://fontstruct.com/fontstructions/show/1344429/gothic-pixel-font-1)
* **Designer Profile:** [https://fontstruct.com/fontstructors/show/1090663/leonardocosta](https://fontstruct.com/fontstructors/show/1090663/leonardocosta)

**Trademark Notice:** FontStruct is a trademark of FontStruct.com

**Sources:** [magician L30-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L30-L33)

---

## Technical Specifications

### File Format Structure

The Gothic Pixel Font follows the TrueType font specification with the following technical properties:

```

```

**Font Table Overview:**

| Table | Purpose | Line Reference |
| --- | --- | --- |
| OS/2 | Operating system/2 specific metrics and character set information | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |
| cmap | Character code to glyph index mapping | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |
| glyf | Glyph outline data (vector shapes) | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |
| head | Font header with global font information | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |
| hhea | Horizontal header with line metrics | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |
| hmtx | Horizontal metrics for each glyph | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |
| kern | Kerning pair adjustments | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |
| loca | Glyph location index | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |
| maxp | Maximum profile for memory allocation | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |
| name | Naming table with font metadata | [magician L30-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L30-L33) |
| post | PostScript information | [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1) |

### Pixel Font Characteristics

As a pixel font, Gothic Pixel Font is designed for:

* **Low-resolution rendering** - Optimized for display at small sizes
* **Crisp edges** - No anti-aliasing artifacts at intended sizes
* **Retro aesthetic** - Evokes 8-bit and 16-bit era game typography
* **Modular design** - Based on the "Blox Gothic Modular" grid system

**Sources:** [magician L1-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L33)

---

## Asset Integration

### Font File Location in Project Structure

```

```

The font file is stored in the `datafiles/` directory, which is a standard GameMaker Studio 2 location for included data files that are bundled with the game but not directly compiled as GameMaker resources.

**Sources:** [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1)

---

## Usage in GameMaker Project

### Font Asset Pipeline

```

```

### Font Role in Visual Asset System

The Gothic Pixel Font integrates with Haunted Hollow's visual asset architecture:

```

```

**Font Selection Strategy:**

The project includes two font assets serving different purposes:

| Font | Type | Use Case |
| --- | --- | --- |
| `fnt_medieval` (Bradley Hand ITC) | GameMaker Font Resource | Primary UI text, dialogue boxes, NPC interactions |
| `gothic-pixel-font.ttf` | TrueType Datafile | Retro-styled text, title screens, pixel art aesthetic elements |

**Sources:** High-level architecture diagram (Diagram 5), [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L1)

---

## Comparison with Medieval Font

### Dual Font System Architecture

```

```

**Key Differences:**

| Aspect | fnt_medieval | gothic-pixel-font.ttf |
| --- | --- | --- |
| **Integration** | Compiled GameMaker font resource | External TrueType file in datafiles |
| **Font Family** | Bradley Hand ITC | Blox Gothic Modular |
| **Style** | Handwritten/calligraphic | Pixel art/modular |
| **Use Pattern** | Direct draw_text() calls | May require font_add() or similar loading |
| **Aesthetic** | Medieval fantasy theme | Retro pixel game theme |
| **Licensing** | Proprietary (system font) | Creative Commons Attribution 3.0 |

**Sources:** [Medieval Font System](/axchisan/Haunted_hollow/7.1-medieval-font-system), [magician L1-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L33)

---

## Implementation Notes

### Potential Usage Scenarios

Since the Gothic Pixel Font exists as an external TrueType file in `datafiles/`, its usage in GameMaker Studio 2 likely follows one of these patterns:

1. **Dynamic Font Loading:** ``` ```
2. **Title Screens and Menus:** * Game logo/title rendering * Main menu text * Credits screen
3. **Retro UI Elements:** * Score displays * Pixel-styled labels * Achievement notifications

### Font Not Currently Compiled

Unlike `fnt_medieval`, which appears in the GameMaker project as a compiled font resource, `gothic-pixel-font.ttf` exists only as a datafile. This suggests:

* It may be intended for future use
* It could be loaded dynamically when needed
* It might be used for specific promotional materials or external documentation
* It could serve as a backup font for platform-specific rendering scenarios

**Sources:** [magician L1-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L33)

---

## Character Set Coverage

### Supported Characters

The font includes standard Latin character coverage suitable for English text rendering:

**Included Characters:**

* **Uppercase letters:** A-Z
* **Lowercase letters:** a-z
* **Numerals:** 0-9
* **Basic punctuation:** .,;:!?"'
* **Common symbols:** ()-[]{}@#$%^&*+=/<>|~`

**Sample Rendering:**
The embedded pangram demonstrates full alphabet coverage:

> "Five big quacking zephyrs jolt my wax bed"

This sentence contains all 26 letters of the English alphabet, confirming complete character set support for basic English text.

**Sources:** [magician L32-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L32-L33)

---

## Summary

The Gothic Pixel Font represents an alternative typographic aesthetic for Haunted Hollow, providing pixel art styling that complements the game's retro dungeon-crawler elements. While stored as an external TrueType asset rather than a compiled GameMaker resource, it remains available for dynamic loading and specialized rendering scenarios.

**Key Takeaways:**

* Creative Commons licensed pixel font by leonardocosta
* Located in `datafiles/` as an external TrueType file
* Provides retro/pixel aesthetic contrasting with the medieval handwritten font
* Full English character set support
* Modular design based on Blox Gothic grid system

**Sources:** [magician L1-L33](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/datafiles/gothic-pixel-font.ttf#L1-L33)
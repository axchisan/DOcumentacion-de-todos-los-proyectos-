# Medieval Font System

> **Relevant source files**
> * [magician project1/fonts/fnt_medieval/fnt_medieval.old.png](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.old.png)
> * [magician project1/fonts/fnt_medieval/fnt_medieval.old.yy](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.old.yy)
> * [magician project1/fonts/fnt_medieval/fnt_medieval.png](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.png)
> * [magician project1/fonts/fnt_medieval/fnt_medieval.yy](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy)

## Purpose and Scope

This document describes the `fnt_medieval` font asset used throughout Haunted Hollow for rendering text in dialogue boxes, UI elements, and educational content. The font system is a GameMaker font resource that defines glyph rendering properties, character mappings, and texture coordinates for bitmap font rendering.

For information about the Gothic Pixel Font (the other font in the project), see [Gothic Pixel Font](/axchisan/Haunted_hollow/7.2-gothic-pixel-font).

---

## Font Asset Overview

The `fnt_medieval` font asset consists of two primary files that work together to render text:

| Component | File | Purpose |
| --- | --- | --- |
| Configuration | `fnt_medieval.yy` | Defines font properties, glyph metrics, and rendering settings |
| Texture Atlas | `fnt_medieval.png` | Contains pre-rendered character glyphs in a texture atlas |

The font is located at [magician project1/fonts/fnt_medieval/](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/)

 and is registered as a resource in the GameMaker project structure under the "Fonts" folder.

**Sources:**

* [magician L1-L143](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L1-L143)

---

## Font Configuration Structure

The following diagram illustrates the hierarchical structure of the font configuration defined in the `.yy` file:

```

```

**Sources:**

* [magician L1-L25](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L1-L25)
* [magician L112-L143](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L112-L143)

---

## Glyph Data Structure

Each character in the font has associated glyph metrics that define its position in the texture atlas and rendering properties. The structure follows GameMaker's standard glyph format:

| Property | Type | Description |
| --- | --- | --- |
| `character` | int | ASCII character code |
| `h` | int | Glyph height in pixels (always 30 for this font) |
| `offset` | int | Horizontal rendering offset from baseline |
| `shift` | int | Character advance width (space to next character) |
| `w` | int | Glyph width in pixels |
| `x` | int | X coordinate in texture atlas |
| `y` | int | Y coordinate in texture atlas |

### Example Glyph Definitions

```mermaid
flowchart TD

A_Data["character: 65<br>h: 30<br>offset: -1<br>shift: 17<br>w: 18<br>x: 211<br>y: 130"]
a_Data["character: 97<br>h: 30<br>offset: 1<br>shift: 12<br>w: 9<br>x: 54<br>y: 2"]
exclaim_Data["character: 33<br>h: 30<br>offset: 2<br>shift: 7<br>w: 3<br>x: 195<br>y: 98"]
Atlas["fnt_medieval.png<br>Texture Atlas"]

Atlas --> A_Data
Atlas --> a_Data
Atlas --> exclaim_Data

subgraph subGraph2 ["Character '!' (33)"]
    exclaim_Data
end

subgraph subGraph1 ["Character 'a' (97)"]
    a_Data
end

subgraph subGraph0 ["Character 'A' (65)"]
    A_Data
end
```

**Sources:**

* [magician L14-L110](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L14-L110)

---

## Rendering Properties

The font configuration defines several properties that control how glyphs are rendered at runtime:

### Typography Properties

| Property | Value | Description |
| --- | --- | --- |
| `fontName` | "Bradley Hand ITC" | System font used to generate glyphs |
| `size` | 18.0 | Font size in points |
| `bold` | true | Bold weight enabled |
| `italic` | false | Italic style disabled |
| `styleName` | "Regular" | Font style variant |

### Layout Properties

| Property | Value | Description |
| --- | --- | --- |
| `lineHeight` | 30 | Vertical spacing between lines |
| `ascender` | 20 | Height above baseline |
| `ascenderOffset` | 0 | Additional ascender adjustment |

### Rendering Flags

| Property | Value | Description |
| --- | --- | --- |
| `AntiAlias` | 1 | Anti-aliasing enabled for smooth edges |
| `hinting` | 0 | Font hinting disabled |
| `usesSDF` | false | Signed distance field rendering disabled |
| `sdfSpread` | 8 | SDF spread value (unused since SDF disabled) |
| `applyKerning` | 0 | Kerning adjustments disabled |

**Sources:**

* [magician L4-L12](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L4-L12)
* [magician L112-L142](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L112-L142)

---

## Character Coverage

The font includes two character ranges defining which glyphs are available:

```mermaid
flowchart TD

CharSet["fnt_medieval Character Set"]
ASCII["ASCII Range 32-127"]
Default["Default Character 9647"]
Printable["96 printable characters"]
Space["Space (32)"]
Punctuation["Punctuation (33-47, 58-64, 91-96, 123-126)"]
Digits["Digits 0-9 (48-57)"]
Uppercase["Uppercase A-Z (65-90)"]
Lowercase["Lowercase a-z (97-122)"]
Fallback["▯ - Renders for undefined characters"]

CharSet --> ASCII
CharSet --> Default
ASCII --> Printable
Printable --> Space
Printable --> Punctuation
Printable --> Digits
Printable --> Uppercase
Printable --> Lowercase
Default --> Fallback
```

The ranges are defined as:

* **Range 1:** Characters 32 through 127 (standard ASCII printable characters)
* **Range 2:** Character 9647 (the "▯" symbol used as the default/fallback character)

**Sources:**

* [magician L126-L129](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L126-L129)

---

## Texture Atlas Organization

The `fnt_medieval.png` file is a texture atlas containing all font glyphs packed into a single image. The packing coordinates are defined in the glyph data:

```mermaid
flowchart TD

Atlas["fnt_medieval.png<br>Texture Atlas Image"]
Region1["Top Region<br>y: 2-34<br>Contains: uppercase letters,<br>numbers, special chars"]
Region2["Middle Region<br>y: 66-98<br>Contains: lowercase letters,<br>common punctuation"]
Region3["Bottom Region<br>y: 130-162<br>Contains: remaining chars,<br>default glyph"]
Sample1["'A' at (211, 130)"]
Sample2["'a' at (54, 2)"]
Sample3["'▯' at (111, 162)"]

Atlas --> Region1
Atlas --> Region2
Atlas --> Region3
Region1 --> Sample1
Region2 --> Sample2
Region3 --> Sample3
```

Each glyph occupies a rectangular region defined by its `(x, y)` coordinates (top-left corner) and `w` (width) and `h` (height) dimensions. GameMaker's rendering engine uses these coordinates to extract the correct portion of the texture when drawing text.

**Sources:**

* [magician L1](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.png#L1-L1)
* [magician L14-L110](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L14-L110)

---

## Font Version History

The font asset has undergone a significant revision, with both old and current versions preserved in the project directory:

### Old Version (fnt_medieval.old.*)

| Property | Old Value | Description |
| --- | --- | --- |
| Font Face | "Old English Text MT" | Gothic/medieval decorative font |
| Line Height | 24 | Shorter line spacing |
| Kerning Pairs | 62 defined | Character spacing adjustments |
| Style | Bold | Heavy weight appearance |

The old version included extensive kerning pair definitions (e.g., 'A'-'T', 'F'-',', 'W'-'a') to improve visual spacing between specific character combinations.

### Current Version (fnt_medieval.*)

| Property | New Value | Description |
| --- | --- | --- |
| Font Face | "Bradley Hand ITC" | Handwritten/casual font |
| Line Height | 30 | Increased vertical spacing |
| Kerning Pairs | 0 | Kerning disabled |
| Style | Bold | Maintained bold weight |

The font face change from "Old English Text MT" to "Bradley Hand ITC" represents a shift from a formal medieval aesthetic to a more casual, readable handwritten style. This improves legibility for educational content while maintaining thematic consistency.

```mermaid
flowchart TD

OldFont["Old Version<br>Old English Text MT<br>lineHeight: 24<br>62 kerning pairs"]
NewFont["Current Version<br>Bradley Hand ITC<br>lineHeight: 30<br>0 kerning pairs"]
Reason1["Improved readability<br>for questions"]
Reason2["More casual aesthetic<br>suitable for learning"]
Reason3["Simplified rendering<br>(no kerning)"]

OldFont --> NewFont
NewFont --> Reason1
NewFont --> Reason2
NewFont --> Reason3
```

**Sources:**

* [magician L1-L189](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.old.yy#L1-L189)
* [magician L1-L143](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L1-L143)

---

## Integration with Game Systems

The `fnt_medieval` font is referenced throughout the game's text rendering systems. GameMaker objects that display text specify this font resource to control typography:

```mermaid
flowchart TD

Font["fnt_medieval<br>Font Resource"]
TextSystem["Text Rendering Systems"]
Textbox["Textbox System<br>(NPC dialogues)"]
Questions["Question Rooms<br>(Educational content)"]
UI["UI Elements<br>(Labels, buttons)"]
DrawText["draw_text()<br>draw_text_ext()"]
SetFont["draw_set_font(fnt_medieval)"]
Render["GameMaker Rendering Engine<br>Samples from fnt_medieval.png<br>using glyph coordinates"]

Font --> TextSystem
TextSystem --> Textbox
TextSystem --> Questions
TextSystem --> UI
Textbox --> DrawText
Questions --> DrawText
UI --> DrawText
DrawText --> SetFont
SetFont --> Render
```

The font is typically set using GameMaker's `draw_set_font(fnt_medieval)` function before drawing text with `draw_text()` or `draw_text_ext()`. The rendering engine then automatically uses the glyph metrics and texture atlas to render the specified string.

**Sources:**

* [magician L121-L124](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L121-L124)  (parent folder reference)
* Architecture diagrams (showing textbox and question room systems)

---

## Technical Specifications Summary

| Specification | Value |
| --- | --- |
| Resource Type | GMFont |
| Resource Version | 2.0 |
| Font Family | Bradley Hand ITC |
| Point Size | 18pt |
| Bitmap Height | 30 pixels per glyph |
| Character Count | 96 (ASCII 32-127 + default char) |
| Texture Format | PNG with alpha channel |
| Texture Group | Default |
| Anti-Aliasing | Enabled |
| SDF Rendering | Disabled (bitmap font) |
| Kerning | Disabled |

**Sources:**

* [magician L1-L143](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/fonts/fnt_medieval/fnt_medieval.yy#L1-L143)
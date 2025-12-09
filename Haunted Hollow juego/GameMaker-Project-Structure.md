# GameMaker Project Structure

> **Relevant source files**
> * [magician project1/mague.yyp](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp)

## Purpose and Scope

This document explains the organization and structure of the GameMaker Studio 2 project file `mague.yyp`, which serves as the central registry for all game assets, resources, and configuration settings in Haunted Hollow. It covers the folder hierarchy system, resource registration mechanisms, and how GameMaker organizes different asset types.

For information about version control settings for GameMaker projects, see [Version Control Configuration](/axchisan/Haunted_hollow/8.2-version-control-configuration). For details on compiling the project to an executable, see [Building and Distribution](/axchisan/Haunted_hollow/8.3-building-and-distribution).

**Sources**: [magician L1-L338](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L1-L338)

---

## Project File Overview

The `mague.yyp` file is a JSON-formatted configuration file that defines the entire GameMaker project structure. It contains metadata, folder definitions, resource registrations, and project settings.

### Core Project Metadata

| Property | Value | Description |
| --- | --- | --- |
| `$GMProject` | `""` | GameMaker project type identifier |
| `%Name` | `"mague"` | Project name |
| `defaultScriptType` | `1` | GML script format (1 = GML2) |
| `templateType` | `"game"` | Project template type |
| `isEcma` | `false` | ECMAScript mode disabled |

The project was created with GameMaker IDE version `2024.4.0.137`, as specified in the `MetaData` section [magician L82-L84](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L82-L84)

**Sources**: [magician L1-L10](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L1-L10)

 [magician L82-L84](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L82-L84)

---

## Folder Hierarchy System

GameMaker uses a virtual folder structure defined in the project file to organize resources. These folders do not necessarily map 1:1 to filesystem directories but provide logical grouping in the IDE.

### Top-Level Folder Structure

```

```

Each folder is defined as a `GMFolder` resource with properties including `%Name`, `folderPath`, and `resourceType` [magician L12-L76](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L12-L76)

**Sources**: [magician L12-L76](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L12-L76)

---

## Objects Folder Organization

The Objects folder contains the most complex hierarchical structure, reflecting the game's entity organization system.

### Objects Hierarchy Diagram

```

```

This hierarchy enables developers to locate related game objects quickly. Enemy objects are categorized by type (gray, green, red phantoms), question UI elements are nested under their respective question numbers, and player-related objects are grouped separately.

**Sources**: [magician L17-L36](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L17-L36)

---

## Scripts Folder Organization

Scripts are organized into functional categories that reflect their purpose in the codebase.

| Folder Path | Purpose | Example Scripts |
| --- | --- | --- |
| `Scripts/characters` | Character-specific behavior | NPC interaction handlers |
| `Scripts/player` | Player mechanics | `scrHandleMovement`, `scrHandleShooting`, `scrCheckPlayerDeath` |
| `Scripts/system` | System-level utilities | Game reset, initialization |
| `Scripts/textbox` | Text display system | `scrOpenTexbox`, `scrSplitText`, `scrSplitTextintoPages` |
| `Scripts/Utils` | General utilities | Helper functions |

The modular script architecture allows reusable functions to be organized by domain, preventing monolithic code files.

**Sources**: [magician L40-L45](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L40-L45)

---

## Sprites Folder Organization

Sprites are organized by entity type and animation state, creating a deep hierarchy that mirrors the game's visual asset structure.

### Sprites Hierarchy for Key Entities

```

```

Player sprites are further divided by directional movement (down, left, right, up), attack animations, and weapon states. This granular organization allows efficient sprite management during development.

**Sources**: [magician L48-L73](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L48-L73)

---

## Resource Registration System

All game resources are registered in the `resources` array, which maps resource names to their file paths. Each entry follows a consistent structure.

### Resource Entry Format

```

```

**Example Resource Entries**:

| Resource Type | Name | Path |
| --- | --- | --- |
| Font | `fnt_medieval` | `fonts/fnt_medieval/fnt_medieval.yy` |
| Object | `Obj_boss` | `objects/Obj_boss/Obj_boss.yy` |
| Object | `Obj_magician` | `objects/Obj_magician/Obj_magician.yy` |
| Room | `Room1` | `rooms/Room1/Room1.yy` |
| Script | `scrHandleShooting` | `scripts/scrHandleShooting/scrHandleShooting.yy` |
| Sound | `music1` | `sounds/music1/music1.yy` |
| Sprite | `Spr_bullet_flame` | `sprites/Spr_bullet_flame/Spr_bullet_flame.yy` |

Each resource entry at [magician L86-L317](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L86-L317)

 contains an `id` object with `name` and `path` properties pointing to the resource's `.yy` definition file.

**Sources**: [magician L86-L317](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L86-L317)

---

## Resource Type Distribution

The project contains a comprehensive set of resources across multiple categories:

### Resource Inventory

```

```

**Key Resource Categories**:

* **Objects**: 74 game objects including player (Obj_magician), enemies (Obj_boss, Obj_phantom variants), UI elements (Obj_healt_bar, Obj_textbox), and educational components (Obj_question_1 through Obj_question_6)
* **Sprites**: 120+ visual assets covering character animations, enemy sprites, UI graphics, and environmental decorations
* **Scripts**: 17 reusable functions for player control, interaction handling, and system management
* **Rooms**: 12 level definitions including main hub (Room1), question rooms (Room_question1-6), and special rooms (menuInicio, cinejefe, jefe, credits)
* **Sounds**: 14 audio assets for music, sound effects, and feedback (music1, fire, hit, screamphantom, correctanswer)

**Sources**: [magician L86-L317](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L86-L317)

---

## Room Order Configuration

The `RoomOrderNodes` array defines the sequence in which rooms appear in the GameMaker IDE and determines the default room loading order during development.

### Room Sequence

```

```

**Room Order** [magician L320-L333](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L320-L333)

:

1. `menuInicio` - Main menu
2. `Room_cinematic1` - Opening cinematic
3. `Room1` - Main hub with exploration and combat
4. `Room_question1` through `Room_question6` - Educational quiz rooms
5. `jefe` - Boss battle room
6. `credits` - End credits
7. `cinejefe` - Boss introduction cinematic

This order reflects the game's hub-and-spoke progression model where players return to Room1 between educational challenges.

**Sources**: [magician L320-L333](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L320-L333)

---

## Included Files

External files that are bundled with the game but not managed by GameMaker's resource system are registered in the `IncludedFiles` array.

### Included File: gothic-pixel-font.ttf

```

```

The TrueType font file `gothic-pixel-font.ttf` is included in the `datafiles/` directory and copied to all target platforms (`CopyToMask: -1`) [magician L77-L79](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L77-L79)

 This font provides the retro aesthetic for game text, complementing the built-in `fnt_medieval` font resource.

**Sources**: [magician L77-L79](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L77-L79)

---

## Audio and Texture Groups

GameMaker organizes runtime asset loading through group configurations.

### Audio Groups

The project defines a single audio group:

* **audiogroup_default**: Contains all sound assets with target platform value `-1` (all platforms) [magician L4-L6](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L4-L6)

### Texture Groups

The project defines a single texture group with comprehensive compression settings:

| Property | Value | Description |
| --- | --- | --- |
| Name | `Default` | Group identifier |
| `autocrop` | `true` | Removes transparent borders |
| `border` | `2` | Pixel border around textures |
| `compressFormat` | `"bz2"` | BZ2 compression algorithm |
| `loadType` | `"default"` | Standard loading behavior |
| `isScaled` | `true` | Enables texture scaling |
| `targets` | `-1` | All platforms |

This configuration optimizes texture memory usage while maintaining visual quality across all sprites [magician L335-L337](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L335-L337)

**Sources**: [magician L4-L6](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L4-L6)

 [magician L335-L337](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L335-L337)

---

## Project Configuration Structure

### Configuration Nodes

The `configs` section allows multiple build configurations (Debug, Release, etc.), though this project uses only the default configuration with no children:

```

```

This indicates a single-configuration project without platform-specific build variants [magician L7-L10](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L7-L10)

### Library Emitters

The `LibraryEmitters` array is empty, indicating the project does not use particle system libraries or external effect generators [magician L81](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L81-L81)

**Sources**: [magician L7-L10](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L7-L10)

 [magician L81](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L81-L81)

---

## Resource Path Mapping

Each resource type follows a consistent filesystem organization pattern that mirrors the virtual folder structure.

### Path Mapping Table

| Resource Type | Virtual Folder | Filesystem Path Pattern | Example |
| --- | --- | --- | --- |
| Objects | `Objects/` | `objects/{name}/{name}.yy` | `objects/Obj_boss/Obj_boss.yy` |
| Sprites | `Sprites/` | `sprites/{name}/{name}.yy` | `sprites/Spr_bullet_flame/Spr_bullet_flame.yy` |
| Scripts | `Scripts/` | `scripts/{name}/{name}.yy` | `scripts/scrHandleShooting/scrHandleShooting.yy` |
| Sounds | `sounds/` | `sounds/{name}/{name}.yy` | `sounds/music1/music1.yy` |
| Rooms | `Rooms/` | `rooms/{name}/{name}.yy` | `rooms/Room1/Room1.yy` |
| Fonts | `Fonts/` | `fonts/{name}/{name}.yy` | `fonts/fnt_medieval/fnt_medieval.yy` |
| Tile Sets | `Tile Sets/` | `tilesets/{name}/{name}.yy` | `tilesets/TileSet2/TileSet2.yy` |

Each resource is stored in a dedicated directory containing its `.yy` definition file plus associated asset files (images for sprites, audio files for sounds, etc.).

**Sources**: [magician L86-L317](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L86-L317)

---

## Resource Definition File Format

Each resource entry in `mague.yyp` points to a `.yy` file that contains the detailed configuration for that resource. These files use GameMaker's resource definition schema.

### Resource Reference Structure

```

```

For example, `Obj_boss` registration [magician L90](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L90-L90)

 points to `objects/Obj_boss/Obj_boss.yy`, which defines the boss object's events, sprite associations, physics properties, and parent object relationships.

**Sources**: [magician L86-L317](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L86-L317)

---

## Project-Level Dependencies

The project file establishes dependencies between different resource types through path references and ID linkages.

### Dependency Flow

```

```

Dependencies are resolved at compile time when GameMaker processes all `.yy` files referenced in the project. Circular dependencies are prevented by the resource type hierarchy: Objects can reference Scripts, Sprites, and Sounds, but these asset types cannot reference Objects directly.

**Sources**: [magician L1-L338](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L1-L338)
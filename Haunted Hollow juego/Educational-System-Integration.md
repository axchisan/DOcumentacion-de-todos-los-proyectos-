# Educational System Integration

> **Relevant source files**
> * [README.md](https://github.com/axchisan/Haunted_hollow/blob/96079758/README.md)
> * [magician project1/mague.yyp](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp)

## Purpose and Scope

This document describes the implementation of Haunted Hollow's educational quiz system, which integrates English comprehension questions with gameplay progression. The system uses a gating mechanism where six NPCs in the main hub trigger dedicated question rooms, and correct answers remove path obstructions to enable player advancement.

For information about general NPC interactions (non-educational), see [Game Flow and Progression](/axchisan/Haunted_hollow/3.1-game-flow-and-progression). For combat mechanics that occur alongside educational content, see [Combat Architecture](/axchisan/Haunted_hollow/3.2-combat-architecture).

---

## System Architecture Overview

The educational system consists of four primary components that work together to gate player progression:

```

```

**Sources:**

* [magician L17-L34](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L17-L34)  - Object folder structure
* [magician L121-L127](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L121-L127)  - NPC object definitions
* [magician L128-L133](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L128-L133)  - Obstruction object definitions
* [magician L166-L171](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L166-L171)  - Question room definitions
* [magician L146-L151](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L146-L151)  - Question object definitions

---

## NPC-Obstruction Gating System

Six NPC objects positioned in `Room1` (the main hub) each correspond to a unique obstruction that blocks a section of the level. This creates a mandatory educational checkpoint system.

### NPC and Obstruction Mapping

| NPC Object | Obstruction Object | Question Room | Purpose |
| --- | --- | --- | --- |
| `Obj_npc1` | `Obj_obstruction_1` | `Room_question1` | First educational gate |
| `Obj_npc2` | `Obj_obstruction_2` | `Room_question2` | Second educational gate |
| `Obj_npc3` | `Obj_obstruction_3` | `Room_question3` | Third educational gate |
| `Obj_npc4` | `Obj_obstruction_4` | `Room_question4` | Fourth educational gate |
| `Obj_npc5` | `Obj_obstruction_5` | `Room_question5` | Fifth educational gate |
| `Obj_npc6` | `Obj_obstruction_6` | `Room_question6` | Sixth educational gate |

### Component Locations

The folder structure organizes these components hierarchically:

* NPCs: `objects/` (root level)
* Obstructions: `objects/obstrucciones/`
* Question UI elements: `objects/Level/questions/buttons/`

**Sources:**

* [magician L121-L127](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L121-L127)  - NPC object references
* [magician L128-L133](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L128-L133)  - Obstruction object references
* [magician L34](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L34-L34)  - Obstructions folder
* [magician L25-L32](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L25-L32)  - Question buttons folder hierarchy

---

## Question Room Structure

Each question room follows an identical structural pattern, containing distinct UI components for presenting questions and capturing answers.

```

```

### Question Object Hierarchy

Each question room contains a parallel set of objects numbered 1-6:

**Question Display Objects:**

* `Obj_question_1` through `Obj_question_6` - Display question text using `fnt_medieval` font

**Answer Input Objects (per question):**

* `Obj_checkbox_true[N]` - Represents "true" answer selection
* `Obj_checkbox_false[N]` - Represents "false" answer selection
* `Obj_submit_button[N]` - Validates selected answer and triggers room transition

**Visual Assets:**

* `Spr_checkbox_frame` - Unselected checkbox sprite
* `Spr_checkbox_frame_selected` - Selected checkbox sprite
* `Spr_button` - Submit button sprite

**Sources:**

* [magician L146-L151](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L146-L151)  - Question object definitions
* [magician L97-L108](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L97-L108)  - Checkbox objects (false options)
* [magician L103-L108](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L103-L108)  - Checkbox objects (true options)
* [magician L152-L157](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L152-L157)  - Submit button objects
* [magician L226-L227](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L226-L227)  - Checkbox sprite assets

---

## Player Interaction Flow

The interaction system uses specialized scripts to detect when the player is near an NPC and wants to initiate a question sequence.

```

```

### Interaction Detection Scripts

**`scrPlayerCheckInteraction_question`:**

* Called from `Obj_magician` Step event when interaction key pressed
* Coordinates detection and dialogue flow specifically for question NPCs

**`scrGetNearbyObject_question`:**

* Uses `collision_circle` to detect NPCs within interaction range
* Returns the nearest NPC object instance or `noone` if none found
* Separate from general object detection to distinguish question NPCs from other interactive objects

**`scrOpenTexbox`:**

* Creates `Obj_textbox` instance to display NPC dialogue
* Prepares text content using `scrSplitText` and `scrSplitTextintoPages`
* Handles multi-page dialogue before room transition

**Sources:**

* [magician L185](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L185-L185)  - scrPlayerCheckInteraction_question definition
* [magician L178](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L178-L178)  - scrGetNearbyObject_question definition
* [magician L184](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L184-L184)  - scrOpenTexbox definition
* [magician L188-L189](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L188-L189)  - Text splitting script definitions

---

## Answer Validation and Feedback

Each submit button object contains event logic to validate the player's selected answer and provide appropriate feedback.

```

```

### Validation Implementation Pattern

Each `Obj_submit_button[N]` object follows this pattern in its Click/Mouse event:

1. **Read Checkbox State**: Check which checkbox object has its `selected` variable set to `true`
2. **Compare Against Answer Key**: Each question has a hardcoded correct answer (true or false)
3. **Execute Correct Path**: * Play `correctanswer` sound asset * Call `instance_destroy()` on corresponding `Obj_obstruction_[N]` * Call `room_goto(Room1)` to return to hub
4. **Execute Incorrect Path**: * Reset checkbox selections * Keep player in question room for retry

### Audio Feedback

The system provides audio confirmation for correct answers:

* **Sound Asset**: `correctanswer` (defined in `sounds/correctanswer/`)
* **Trigger Point**: Immediately after answer validation succeeds
* **Duration**: Plays before room transition

**Sources:**

* [magician L152-L157](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L152-L157)  - Submit button object definitions
* [magician L191](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L191-L191)  - correctanswer sound definition
* [magician L97-L108](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L97-L108)  - Checkbox state management objects

---

## Obstruction Removal Mechanics

The obstruction objects act as physical barriers in `Room1` that prevent player progression until destroyed by correct quiz answers.

### Obstruction Object Properties

Each `Obj_obstruction_[N]` object has the following characteristics:

| Property | Value | Purpose |
| --- | --- | --- |
| **Solid** | `true` | Blocks player and enemy movement |
| **Visible** | `true` | Rendered to show blocked path |
| **Persistent** | `false` | Does not carry between rooms |
| **Parent** | None | Independent objects, no inheritance |

### Destruction Trigger

Obstructions are destroyed via `instance_destroy()` called from the corresponding submit button's Click event:

```

```

### Persistence and State Management

The game does not use a global variable system to track which obstructions have been removed. Instead:

* Obstructions are destroyed permanently when their question is answered correctly
* If player dies and game restarts via `scrGamereset`, all obstructions respawn
* Progress is not saved between game sessions
* Each playthrough requires answering all six questions

**Sources:**

* [magician L128-L133](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L128-L133)  - Obstruction object definitions
* [magician L177](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L177-L177)  - scrGamereset script definition
* [magician L152-L157](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L152-L157)  - Submit button objects that trigger destruction

---

## Room Transition Flow

The educational system manages transitions between the main hub (`Room1`) and six question rooms, with conditional access to the boss area.

```

```

### Room Order Definition

The room transition order is defined in the project file's `RoomOrderNodes` array:

```
menuInicio (Menu)
  ↓
Room_cinematic1 (Intro cinematic)
  ↓
Room1 (Main hub)
  ↔ Room_question1-6 (Six question rooms)
  ↓
cinejefe (Boss cinematic)
  ↓
jefe (Boss fight)
  ↓
credits (End credits)
```

**Sources:**

* [magician L320-L333](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L320-L333)  - RoomOrderNodes definition
* [magician L161-L172](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L161-L172)  - Room resource definitions

---

## Integration with Game Progression

The educational system serves as the primary progression gate in Haunted Hollow, making quiz completion mandatory for accessing the final boss encounter.

### Progression Checkpoint Requirements

| Checkpoint | Requirement | Consequence |
| --- | --- | --- |
| **Access Room1 exploration** | Complete `Room_cinematic1` | Player can explore and encounter phantoms |
| **Access all Room1 areas** | Answer all 6 questions correctly | All `Obj_obstruction_[1-6]` removed |
| **Access boss cinematic** | All obstructions cleared | Path to `cinejefe` room unlocked |
| **Access boss fight** | Complete boss cinematic | Enter `jefe` room with `Obj_boss` |

### Educational Content Distribution

The game design distributes educational content evenly:

* **Total Questions**: 6 English comprehension questions
* **Question Format**: True/False binary choice
* **Retry Limit**: Unlimited (players can retry until correct)
* **Progressive Difficulty**: Questions do not scale in difficulty; all are equivalent checkpoints

### Interaction with Combat System

While the educational system gates progression, combat occurs independently in `Room1`:

* Players fight `Obj_phantom`, `Obj_phantom_green`, and `Obj_phantom_red` enemies in the hub
* Combat does not affect question availability or obstruction state
* Death in combat triggers `scrCheckPlayerDeath`, potentially resetting all progress via `scrGamereset`
* Educational checkpoints and combat challenges run in parallel

**Sources:**

* [README.md L1-L2](https://github.com/axchisan/Haunted_hollow/blob/96079758/README.md#L1-L2)  - Educational purpose and game overview
* [magician L174](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L174-L174)  - scrCheckPlayerDeath definition
* [magician L177](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L177-L177)  - scrGamereset definition

---

## Script Architecture for Question Interactions

The question interaction system uses specialized scripts that parallel the general interaction system but target only educational NPCs.

### Script Separation: General vs. Question

| Script | Purpose | Target Objects |
| --- | --- | --- |
| `scrPlayerCheckInteraction` | General object interaction | Chests, levers, generic NPCs |
| `scrPlayerCheckInteraction_question` | Educational NPC interaction | `Obj_npc1` through `Obj_npc6` |
| `scrGetNearbyObject` | General nearby object detection | Any interactive object |
| `scrGetNearbyObject_question` | Question NPC detection | Only question-triggering NPCs |

This separation allows the system to:

1. Distinguish between educational and non-educational interactions
2. Apply different detection ranges or behaviors
3. Trigger different dialogue/action flows based on interaction type

### Textbox System Integration

Both interaction types share the textbox rendering system:

```

```

### Font Asset Usage

The textbox system uses `fnt_medieval` for all text rendering:

* Question display in question rooms
* NPC dialogue in `Room1`
* UI elements in checkboxes and submit buttons

**Sources:**

* [magician L185-L186](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L185-L186)  - Question-specific interaction scripts
* [magician L179-L180](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L179-L180)  - General interaction scripts
* [magician L184](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L184-L184)  - scrOpenTexbox definition
* [magician L188-L189](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L188-L189)  - Text processing scripts
* [magician L87](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/mague.yyp#L87-L87)  - fnt_medieval font asset

---

## Summary

The Educational System Integration in Haunted Hollow implements a mandatory progression gate through six NPC-triggered quiz rooms. Each of the six `Obj_npc[1-6]` objects in `Room1` guards an `Obj_obstruction_[1-6]` barrier that blocks player advancement. Interaction with these NPCs via `scrPlayerCheckInteraction_question` and `scrGetNearbyObject_question` triggers transitions to `Room_question[1-6]` rooms, where players must answer English comprehension questions using checkbox UI elements. Correct answers validated by `Obj_submit_button[1-6]` objects destroy the corresponding obstruction via `instance_destroy()`, play the `correctanswer` sound, and return the player to `Room1`. Only after all six obstructions are cleared can players access the `cinejefe` boss cinematic room, making educational content completion mandatory for game progression.
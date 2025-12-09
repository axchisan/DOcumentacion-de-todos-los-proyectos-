# Home

> **Relevant source files**
> * [Game Maker- Haunted_hollow.rar](https://github.com/axchisan/Haunted_hollow/blob/96079758/Game Maker- Haunted_hollow.rar)
> * [Haunted Hollow.exe](https://github.com/axchisan/Haunted_hollow/blob/96079758/Haunted Hollow.exe)
> * [README.md](https://github.com/axchisan/Haunted_hollow/blob/96079758/README.md)

## Purpose and Scope

This wiki documents the **Haunted Hollow** codebase, an educational dungeon-crawler game developed in GameMaker Studio 2. The game transforms English language learning into an interactive experience where players navigate haunted dungeons, battle enemies, and solve English comprehension puzzles to progress.

This documentation covers:

* Core game systems and their interactions
* GameMaker object hierarchy and event handling
* Educational content integration with gameplay progression
* Asset organization and resource management

For instructions on running the game or opening the project in GameMaker, see [Getting Started](/axchisan/Haunted_hollow/2-getting-started). For detailed architectural patterns and system designs, see [Architecture Overview](/axchisan/Haunted_hollow/3-architecture-overview).

**Sources:** `README.md`, `mague.yyp`, `Game Maker- Haunted_hollow.rar`

---

## Project Overview

Haunted Hollow is structured as a GameMaker Studio 2 project where gameplay, educational content, and visual/audio assets are organized into a hub-and-spoke progression model. The player controls a wizard (`Obj_magician`) trapped in a haunted dungeon who must defeat enemies and answer English comprehension questions to unlock paths and ultimately face the boss.

### Technology Stack

| Component | Technology |
| --- | --- |
| Game Engine | GameMaker Studio 2 |
| Project File | `mague.yyp` |
| Scripting Language | GameMaker Language (GML) |
| Distribution | Compiled `.exe` + Source `.rar` |
| Fonts | `fnt_medieval`, `gothic-pixel-font` |
| Audio | `.wav` sound effects and background music |

**Sources:** `mague.yyp`, `Game Maker- Haunted_hollow.rar`, `.gitattributes`

---

## Core System Architecture

The following diagram maps the natural language system descriptions to their concrete code implementations in the GameMaker project:

```mermaid
flowchart TD

RAR["Game Maker- Haunted_hollow.rar<br>Source Archive"]
YYP["mague.yyp<br>GameMaker Project File"]
EXE["Haunted Hollow.exe<br>Compiled Executable"]
PLAYER["Obj_magician<br>Player Character"]
PSCRIPTS["Player Scripts:<br>scrHandleMovement<br>scrHandleShooting<br>scrCheckPlayerDeath"]
PBULLET["Obj_bullet_flame<br>Player Projectile"]
BOSS["Obj_boss<br>Boss Enemy"]
PHANTOM["Obj_phantom<br>Gray Phantom"]
PHANTOM_G["Obj_phantom_green<br>Green Phantom"]
PHANTOM_R["Obj_phantom_red<br>Red Phantom"]
EBULLETS["Obj_bullet_boss<br>Obj_bullet_phantom<br>Obj_bullet_phantom_green<br>Obj_bullet_phantom_red"]
NPC["Obj_npc0 - Obj_npc6<br>6 NPC Characters"]
QUESTIONS["Room_question1-6<br>Quiz Rooms"]
OBSTRUCTIONS["Obj_obstruction_1-6<br>Path Blockers"]
QSCRIPTS["scrPlayerCheckInteraction_question<br>scrGetNearbyObject_question"]
ROOM1["Room1<br>Main Hub"]
CINE1["Room_cinematic1<br>Opening Cinematic"]
BOSS_ROOM["jefe<br>Boss Fight Room"]
CREDITS["credits<br>End Credits"]
HEALTHBAR["Obj_healt_bar<br>Health Display"]
TEXTBOX["Textbox System:<br>scrOpenTextbox<br>scrSplitText<br>scrSplitTextintoPages"]
HEALTH["global.vida_actual<br>Player Health"]
BOSS_HEALTH["global.boss_current_health<br>Boss Health"]

PLAYER --> HEALTH
PBULLET --> BOSS
PBULLET --> PHANTOM
EBULLETS --> PLAYER
ROOM1 --> NPC
ROOM1 --> OBSTRUCTIONS
HEALTH --> HEALTHBAR
NPC --> TEXTBOX

subgraph subGraph6 ["Global State"]
    HEALTH
    BOSS_HEALTH
end

subgraph subGraph5 ["UI System"]
    HEALTHBAR
    TEXTBOX
end

subgraph subGraph4 ["Level System"]
    ROOM1
    CINE1
    BOSS_ROOM
    CREDITS
    CINE1 --> ROOM1
    ROOM1 --> BOSS_ROOM
end

subgraph subGraph3 ["Educational System"]
    NPC
    QUESTIONS
    OBSTRUCTIONS
    QSCRIPTS
    NPC --> QUESTIONS
    NPC --> QSCRIPTS
    QUESTIONS --> OBSTRUCTIONS
end

subgraph subGraph2 ["Enemy System"]
    BOSS
    PHANTOM
    PHANTOM_G
    PHANTOM_R
    EBULLETS
    BOSS --> PHANTOM
    BOSS --> PHANTOM_G
    BOSS --> PHANTOM_R
    BOSS --> EBULLETS
end

subgraph subGraph1 ["Player System"]
    PLAYER
    PSCRIPTS
    PBULLET
    PLAYER --> PBULLET
    PSCRIPTS --> PLAYER
end

subgraph subGraph0 ["Project Distribution"]
    RAR
    YYP
    EXE
    RAR --> YYP
    YYP --> EXE
end
```

**Sources:** `mague.yyp`, `objects/Obj_magician/`, `objects/Obj_boss/`, `objects/Obj_npc*/`, `rooms/`

---

## Game Flow and Progression

Haunted Hollow uses a structured progression system where educational content gates advancement. The game flow centers around `Room1` as the main hub:

```mermaid
stateDiagram-v2
    [*] --> menuInicio : "Launch Game"
    menuInicio --> Room_cinematic1
    Boss_Combat --> Victory : "global.boss_current_health <= 0"
    Boss_Combat --> Game_Over : "global.vida_actual <= 0"
    Game_Over --> menuInicio
    credits --> [*] : "Game Complete"
    Exploration --> Combat : "Enemy Defeated"
    Combat --> Exploration : "Enemy Defeated"
    Exploration --> NPC_Dialogue : "Interact with Obj_npcX"
    NPC_Dialogue --> Question_Room : "scrPlayerCheckInteraction_question"
```

**Sources:** `rooms/menuInicio`, `rooms/Room1`, `rooms/Room_question1-6`, `rooms/jefe`, `rooms/credits`, `objects/Obj_npc*/`, `objects/Obj_obstruction_*/`

---

## Project File Structure

The GameMaker project follows a hierarchical organization defined in `mague.yyp`:

```mermaid
flowchart TD

ROOT["mague.yyp<br>Project Root"]
SPRITES["Sprites/<br>Visual Assets"]
SOUNDS["Sounds/<br>Audio Assets"]
FONTS["Fonts/<br>fnt_medieval<br>gothic-pixel-font"]
OBJECTS["Objects/<br>Game Entities"]
SCRIPTS["Scripts/<br>Reusable Functions"]
ROOMS["Rooms/<br>Game Levels"]
TILESETS["Tile Sets/<br>Level Textures"]
PLAYER_OBJ["Obj_magician"]
ENEMY_OBJ["Obj_boss<br>Obj_phantom*"]
PROJECTILE_OBJ["Obj_bullet_flame<br>Obj_bullet_boss<br>Obj_bullet_phantom*"]
NPC_OBJ["Obj_npc0-6"]
UI_OBJ["Obj_healt_bar<br>Obj_button_play"]
ENV_OBJ["Obj_colision_block<br>Obj_obstruction_*<br>Obj_barrel_*"]
PLAYER_SCRIPTS["scrHandleMovement<br>scrHandleShooting<br>scrHandleAttackAnimation<br>scrCheckPlayerDeath"]
INTERACT_SCRIPTS["scrPlayerCheckInteraction_question<br>scrGetNearbyObject_question"]
TEXT_SCRIPTS["scrOpenTextbox<br>scrSplitText<br>scrSplitTextintoPages"]
MENU["menuInicio"]
CINE["Room_cinematic1<br>cinejefe"]
HUB["Room1"]
QUEST["Room_question1-6"]
BOSS["jefe"]
END["credits"]
MUSIC["music1"]
SFX["fire<br>hit<br>screamphantom<br>correctanswer<br>uff"]

ROOT --> SPRITES
ROOT --> SOUNDS
ROOT --> FONTS
ROOT --> OBJECTS
ROOT --> SCRIPTS
ROOT --> ROOMS
ROOT --> TILESETS
OBJECTS --> PLAYER_OBJ
OBJECTS --> ENEMY_OBJ
OBJECTS --> PROJECTILE_OBJ
OBJECTS --> NPC_OBJ
OBJECTS --> UI_OBJ
OBJECTS --> ENV_OBJ
SCRIPTS --> PLAYER_SCRIPTS
SCRIPTS --> INTERACT_SCRIPTS
SCRIPTS --> TEXT_SCRIPTS
ROOMS --> MENU
ROOMS --> CINE
ROOMS --> HUB
ROOMS --> QUEST
ROOMS --> BOSS
ROOMS --> END
SOUNDS --> MUSIC
SOUNDS --> SFX
```

**Sources:** `mague.yyp`, `mague.resource_order`, `objects/`, `scripts/`, `rooms/`, `fonts/`, `sounds/`

---

## Key Object Interactions

The following diagram shows how core game objects interact during gameplay, using their actual code entity names:

```mermaid
sequenceDiagram
  participant Obj_magician
  participant scrHandleShooting
  participant Obj_bullet_flame
  participant Obj_phantom
  participant Obj_bullet_phantom
  participant global.vida_actual
  participant Obj_healt_bar

  Obj_magician->>scrHandleShooting: Mouse Click Event
  scrHandleShooting->>Obj_bullet_flame: instance_create_layer()
  note over Obj_bullet_flame: speed = 8
  loop [Movement]
    Obj_bullet_flame->>Obj_bullet_flame: Step Event
    Obj_bullet_flame->>Obj_phantom: Collision Check
    Obj_bullet_flame->>Obj_phantom: Collision_Obj_phantom
    Obj_phantom->>Obj_phantom: healtha -= 1
    Obj_phantom->>Obj_phantom: audio_play_sound(screamphantom)
    Obj_phantom->>Obj_phantom: instance_destroy()
    Obj_bullet_flame->>Obj_bullet_flame: instance_destroy()
    note over Obj_magician,Obj_phantom: Enemy Attack Flow
    Obj_phantom->>Obj_bullet_phantom: Create at enemy position
    Obj_bullet_phantom->>Obj_magician: Collision_Obj_magician
    Obj_magician->>global.vida_actual: global.vida_actual -= 1
    Obj_magician->>Obj_healt_bar: Update Display
    global.vida_actual->>Obj_healt_bar: Obj_healt_bar.Draw Event
    Obj_magician->>Obj_magician: scrCheckPlayerDeath()
    Obj_magician->>Obj_magician: room_goto(menuInicio)
  end
```

**Sources:** `objects/Obj_magician/`, `objects/Obj_bullet_flame/`, `objects/Obj_phantom/`, `scripts/scrHandleShooting.gml`, `objects/Obj_healt_bar/`

---

## Educational System Integration

The educational content is tightly integrated with progression mechanics through a gating system:

| Component | Code Entity | Purpose |
| --- | --- | --- |
| NPCs | `Obj_npc0` - `Obj_npc6` | Trigger dialogue and quiz transitions |
| Quiz Rooms | `Room_question1` - `Room_question6` | Present English comprehension questions |
| Obstructions | `Obj_obstruction_1` - `Obj_obstruction_6` | Block paths until questions answered |
| Question Objects | `Obj_question_1` - `Obj_question_6` | Display question text and manage answers |
| Answer Checkboxes | `Obj_checkbox_false1` - `Obj_checkbox_false5` | True/False answer selection |
| Interaction Scripts | `scrPlayerCheckInteraction_question`, `scrGetNearbyObject_question` | Handle NPC-player interaction |
| Textbox System | `scrOpenTextbox`, `scrSplitText`, `scrSplitTextintoPages` | Display NPC dialogue |

When a player interacts with an NPC, the `scrPlayerCheckInteraction_question` script triggers a room transition to the corresponding `Room_questionX`. Upon answering correctly, the associated `Obj_obstruction_X` is destroyed via `instance_destroy()`, allowing further exploration of `Room1`.

**Sources:** `objects/Obj_npc*/`, `objects/Obj_obstruction_*/`, `objects/Obj_question_*/`, `rooms/Room_question*/`, `scripts/scrPlayerCheckInteraction_question.gml`

---

## Global Game State

Critical game state is managed through global variables accessible throughout the codebase:

```mermaid
flowchart TD

VIDA["global.vida_actual<br>Player Health"]
BOSS_HP["global.boss_current_health<br>Boss Health"]
PLAYER_DMG["Enemy Bullet Collisions<br>Obj_bullet_phantom.Collision_*"]
BOSS_DMG["Player Bullet Collision<br>Obj_bullet_flame.Collision_Obj_boss"]
PLAYER_INIT["Obj_magician.Create_0"]
BOSS_INIT["Obj_boss.Create_0"]
HEALTH_BAR["Obj_healt_bar.Draw_0"]
DEATH_CHECK["scrCheckPlayerDeath"]
BOSS_DEFEAT["Obj_boss.Step_0"]

PLAYER_INIT --> VIDA
BOSS_INIT --> BOSS_HP
PLAYER_DMG --> VIDA
BOSS_DMG --> BOSS_HP
VIDA --> HEALTH_BAR
VIDA --> DEATH_CHECK
BOSS_HP --> BOSS_DEFEAT

subgraph Readers ["Readers"]
    HEALTH_BAR
    DEATH_CHECK
    BOSS_DEFEAT
end

subgraph Writers ["Writers"]
    PLAYER_DMG
    BOSS_DMG
    PLAYER_INIT
    BOSS_INIT
end

subgraph subGraph0 ["Global Variables"]
    VIDA
    BOSS_HP
end
```

**Sources:** `objects/Obj_magician/Create_0.gml`, `objects/Obj_boss/Create_0.gml`, `objects/Obj_bullet_flame/Collision_Obj_boss.gml`, `objects/Obj_healt_bar/`, `scripts/scrCheckPlayerDeath.gml`

---

## Navigation

This wiki is organized into the following main sections:

* **[Getting Started](/axchisan/Haunted_hollow/2-getting-started)** - Instructions for running the game and opening the project
* **[Architecture Overview](/axchisan/Haunted_hollow/3-architecture-overview)** - Detailed system architecture and design patterns * [Game Flow and Progression](/axchisan/Haunted_hollow/3.1-game-flow-and-progression) * [Combat Architecture](/axchisan/Haunted_hollow/3.2-combat-architecture) * [Educational System Integration](/axchisan/Haunted_hollow/3.3-educational-system-integration)
* **[Boss Enemy System](/axchisan/Haunted_hollow/4-boss-enemy-system)** - Boss AI, attack patterns, and minion spawning
* **[Player Combat System](/axchisan/Haunted_hollow/5-player-combat-system)** - Player projectiles and collision handling
* **[Level Design and Environment](/axchisan/Haunted_hollow/6-level-design-and-environment)** - Environmental objects and decorations
* **[Visual Assets and Typography](/axchisan/Haunted_hollow/7-visual-assets-and-typography)** - Font systems and text rendering
* **[Project Configuration and Development](/axchisan/Haunted_hollow/8-project-configuration-and-development)** - Development setup and version control

**Sources:** Table of contents JSON structure
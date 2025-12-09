# Boss Enemy System

> **Relevant source files**
> * [magician project1/objects/Obj_boss/Create_0.gml](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml)
> * [magician project1/objects/Obj_boss/Obj_boss.yy](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Obj_boss.yy)
> * [magician project1/objects/Obj_boss/Step_0.gml](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml)

This document provides comprehensive documentation of the boss enemy implementation in Haunted Hollow. It covers the boss object structure, initialization parameters, AI behavior patterns, attack mechanics, and minion spawning system.

For information about player projectile collision with the boss, see [Player Combat System - Boss Collision Handling](/axchisan/Haunted_hollow/5.3-boss-collision-handling). For the broader combat architecture context, see [Combat Architecture](/axchisan/Haunted_hollow/3.2-combat-architecture).

## Overview

The boss enemy system implements the final challenge in Haunted Hollow, accessed after clearing all six educational obstructions. The `Obj_boss` object orchestrates complex behavior including player detection, dynamic movement patterns, projectile attacks, and autonomous minion spawning.

**Sources:**

* [magician L1-L39](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Obj_boss.yy#L1-L39)
* [magician L1-L34](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L1-L34)
* [magician L1-L93](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L1-L93)

---

## Boss Object Structure

The `Obj_boss` object is defined as a standard GameMaker object with two event handlers registered in its definition file.

```

```

**Object Configuration**

| Property | Value | Description |
| --- | --- | --- |
| `name` | `"Obj_boss"` | Object identifier in GameMaker |
| `spriteId` | `Spr_boss_down` | Default sprite (facing down) |
| `persistent` | `false` | Does not persist across rooms |
| `solid` | `false` | Does not block movement |
| `visible` | `true` | Rendered to screen |
| `physicsObject` | `false` | Uses GameMaker's built-in movement, not physics engine |
| `parentObjectId` | `null` | No object inheritance |

**Registered Events**

| Event Type | Event Number | Purpose | Script File |
| --- | --- | --- | --- |
| Create (0) | 0 | Initialize boss state | `Create_0.gml` |
| Step (3) | 0 | Execute AI each frame | `Step_0.gml` |

**Sources:**

* [magician L1-L39](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Obj_boss.yy#L1-L39)

---

## Initialization System

The boss initialization occurs in the Create event, establishing health pools, movement parameters, detection ranges, attack cooldowns, and minion spawning configuration.

```

```

### Health Initialization

The boss uses a dual-tracking health system with both global and instance-level variables:

| Variable | Scope | Initial Value | Purpose |
| --- | --- | --- | --- |
| `global.boss_max_health` | Global | 30 | Maximum health capacity |
| `global.boss_current_health` | Global | 30 | Current health (accessible to UI) |
| `max_health` | Instance | 30 | Instance-level max health |
| `healtha` | Instance | 30 | Instance-level current health |

The global variables enable the health bar UI system to read boss health without direct object references.

**Sources:**

* [magician L2-L3](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L2-L3)
* [magician L12-L13](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L12-L13)

### Movement Configuration

| Parameter | Value | Description |
| --- | --- | --- |
| `move_speed` | 6 | Movement speed when retreating from player |
| `wander_speed` | 4 | Movement speed during wander behavior |
| `wander_time` | 0 (initial) | Countdown timer for wander direction changes |
| `wander_direction` | random(0-359) | Initial random angle in degrees |

The boss uses two distinct movement speeds: a faster `move_speed` for retreating from close-range player encounters, and a slower `wander_speed` for exploratory movement.

**Sources:**

* [magician L6-L11](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L6-L11)
* [magician L26](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L26-L26)

### Range Configuration

The boss operates within two concentric detection zones:

| Range Type | Value (pixels) | Tile Equivalents | Behavior Triggered |
| --- | --- | --- | --- |
| `detect_range` | 768 | 12 tiles (12 × 64) | Player awareness; wander or retreat |
| `attack_range` | 256 | 4 tiles (4 × 64) | Retreat movement and projectile firing |

**Sources:**

* [magician L7-L8](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L7-L8)

### Attack Cooldown System

| Variable | Initial Value | Purpose |
| --- | --- | --- |
| `cooldown` | 120 frames | Time between projectile attacks (~2 seconds at 60 FPS) |
| `cooldown_timer` | 0 | Countdown counter; attacks fire when reaches 0 |

**Sources:**

* [magician L15-L16](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L15-L16)

### Minion Spawning Configuration

The boss spawns phantom minions at regular intervals with wave-based type alternation:

| Variable | Value | Description |
| --- | --- | --- |
| `ghost_spawn_cooldown` | 10 × `room_speed` | Frames between spawn attempts (~10 seconds) |
| `ghost_spawn_timer` | 10 × `room_speed` | Countdown timer for next spawn |
| `ghost_types` | `[Obj_phantom, Obj_phantom1]` | Array of spawnable minion types |
| `current_ghost_type` | 0 | Index into `ghost_types`; alternates after each spawn |
| `max_ghosts` | 4 | Maximum concurrent minions allowed |

The `ghost_types` array contains two phantom variants that alternate each spawn wave. The `current_ghost_type` index increments modulo the array length, creating an alternating pattern.

**Sources:**

* [magician L19-L20](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L19-L20)
* [magician L28-L34](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L28-L34)

### Player Reference

The `player` variable is initialized to `Obj_magician`, establishing a reference to the player object for position queries and targeting calculations.

**Sources:**

* [magician L23](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L23-L23)

---

## AI and Behavior System

The boss AI executes every frame via the Step event, implementing a state-based behavior system with player detection, dynamic movement, sprite direction handling, projectile attacks, and minion spawning.

### Behavior State Machine

```

```

**Sources:**

* [magician L1-L93](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L1-L93)

### Health Synchronization

Each frame, the boss synchronizes its instance health to the global variable:

```
global.boss_current_health = healtha;
```

This one-way synchronization allows the UI system to read current boss health without direct object references.

**Sources:**

* [magician L2](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L2-L2)

### Player Detection and Movement Logic

The boss calculates distance to the player and executes movement based on range thresholds:

```

```

**Distance Calculation:**

```

```

**Retreat Behavior (when distance < attack_range):**

```

```

This creates a "fleeing" behavior where the boss maintains distance while remaining close enough to attack.

**Sources:**

* [magician L4-L8](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L4-L8)
* [magician L11-L27](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L11-L27)

### Wander Behavior

When outside attack range (but within detect range) or completely outside detect range, the boss executes autonomous wander movement:

| Mechanism | Implementation |
| --- | --- |
| Timer Decrement | `wander_time -= 1` each frame |
| Direction Randomization | When `wander_time <= 0`, set `wander_time = random(30-90)` and `wander_direction = random(0-359)` |
| Movement Application | `x += lengthdir_x(wander_speed, wander_direction)` |
|  | `y += lengthdir_y(wander_speed, wander_direction)` |

This creates semi-random movement patterns with direction changes every 0.5-1.5 seconds (30-90 frames at 60 FPS).

**Sources:**

* [magician L19-L26](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L19-L26)
* [magician L30-L37](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L30-L37)

### Room Boundary Clamping

After all movement calculations, position is clamped to room dimensions:

```

```

**Sources:**

* [magician L41-L44](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L41-L44)

### Sprite Direction System

The boss dynamically updates its sprite based on relative player position, prioritizing the axis with greater displacement:

```

```

**Available Sprites:**

* `Spr_boss_right` - Boss facing right
* `Spr_boss_left` - Boss facing left
* `Spr_boss_down` - Boss facing down (default)
* `Spr_boss_up` - Boss facing up

**Sources:**

* [magician L47-L61](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L47-L61)

### Projectile Attack System

The boss fires `Obj_bullet_boss` projectiles when the attack cooldown reaches zero and the player is within attack range:

```

```

**Attack Logic:**

| Condition | Action |
| --- | --- |
| `cooldown_timer > 0` | Decrement `cooldown_timer` |
| `cooldown_timer == 0` AND `distance_to_player <= attack_range` | Create bullet, aim at player, reset cooldown |

**Bullet Configuration:**

* **Object:** `Obj_bullet_boss`
* **Layer:** `"Bullets"`
* **Direction:** Calculated via `point_direction(x, y, player_x, player_y)`
* **Speed:** 6 pixels/frame

**Sources:**

* [magician L64-L71](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L64-L71)

### Minion Spawning System

The boss spawns phantom minions in waves when the concurrent minion count falls below `max_ghosts`:

```

```

**Spawning Parameters:**

| Parameter | Value | Description |
| --- | --- | --- |
| Spawn Cooldown | 10 × room_speed (~10 seconds) | Time between spawn attempts |
| Ghosts per Wave | 3 (maximum) | Attempts to spawn 3 minions per wave |
| Maximum Concurrent | 4 | Hard limit on total living minions |
| Spawn Offset | 32 pixels random direction | Distance from boss position |
| Ghost Types | `[Obj_phantom, Obj_phantom1]` | Alternates between types each wave |

**Type Alternation Logic:**

```

```

This modulo operation creates an alternating pattern: 0 → 1 → 0 → 1 → ..., cycling between `Obj_phantom` and `Obj_phantom1`.

**Sources:**

* [magician L74-L92](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L74-L92)

---

## Boss-Player Combat Integration

The boss participates in the symmetrical combat system documented in [Combat Architecture](/axchisan/Haunted_hollow/3.2-combat-architecture). Key integration points:

### Damage Reception

Player projectiles (`Obj_bullet_flame`) detect collision with `Obj_boss` and decrement its `healtha` variable. When `healtha` reaches 0, the boss is destroyed and the game transitions to victory. See [Boss Collision Handling](/axchisan/Haunted_hollow/5.3-boss-collision-handling) for details.

### Damage Dealing

The boss creates `Obj_bullet_boss` projectiles that detect collision with `Obj_magician` and decrement `global.vida_actual`. This follows the same pattern as phantom enemy attacks. See [Boss Projectile System](/axchisan/Haunted_hollow/4.4-boss-projectile-system) for projectile implementation.

### Health UI Integration

The global variable `global.boss_current_health` is synchronized each frame [magician L2](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L2-L2)

 enabling `Obj_healt_bar` to display boss health without direct object coupling.

---

## Summary

The boss enemy system implements a multi-layered AI with:

* **Dual-speed movement system:** Fast retreat (6 px/frame) vs. slow wander (4 px/frame)
* **Range-based behavior states:** Detect range (768px) triggers awareness, attack range (256px) triggers retreat and firing
* **Cooldown-based attacks:** 120-frame cooldown between projectiles (~2 seconds)
* **Wave-based minion spawning:** Spawns up to 3 minions every ~10 seconds, alternating between `Obj_phantom` and `Obj_phantom1`, capped at 4 concurrent minions
* **Dynamic sprite orientation:** Four directional sprites updated based on player position
* **Global health synchronization:** Enables UI integration without tight coupling

**Core Code Entities:**

| Entity | Type | Purpose |
| --- | --- | --- |
| `Obj_boss` | GameMaker Object | Boss entity container |
| `Create_0.gml` | Event Script | Initialization logic |
| `Step_0.gml` | Event Script | Per-frame AI execution |
| `Obj_bullet_boss` | GameMaker Object | Boss projectile |
| `Obj_phantom` | GameMaker Object | Spawned minion type 1 |
| `Obj_phantom1` | GameMaker Object | Spawned minion type 2 |
| `global.boss_current_health` | Global Variable | UI-accessible health value |
| `healtha` | Instance Variable | Boss's actual health counter |

**Sources:**

* [magician L1-L34](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Create_0.gml#L1-L34)
* [magician L1-L93](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Step_0.gml#L1-L93)
* [magician L1-L39](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_boss/Obj_boss.yy#L1-L39)
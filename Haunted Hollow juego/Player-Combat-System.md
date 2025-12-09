# Player Combat System

> **Relevant source files**
> * [magician project1/objects/Obj_bullet_flame/Create_0.gml](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Create_0.gml)
> * [magician project1/objects/Obj_bullet_flame/Obj_bullet_flame.yy](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Obj_bullet_flame.yy)
> * [magician project1/objects/Obj_bullet_flame/Step_0.gml](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml)

## Purpose and Scope

This document covers the player's offensive combat capabilities in Haunted Hollow, specifically the projectile-based attack system. The player character (`Obj_magician`) fires flame bullets (`Obj_bullet_flame`) that target the mouse cursor position and interact with enemies and environmental obstacles.

For information about the player's movement and input handling, see the player object documentation. For information about enemy attack systems, see [Boss Enemy System](/axchisan/Haunted_hollow/4-boss-enemy-system). For details about the player health system and damage received, refer to the Player System documentation.

---

## System Architecture

The player combat system implements a mouse-targeted projectile attack. The player fires `Obj_bullet_flame` instances that travel in a straight line from the player's position toward the mouse cursor at the moment of firing. These projectiles detect and respond to collisions with multiple entity types.

### Combat System Component Overview

```

```

**Sources:**

* [magician L1-L47](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Obj_bullet_flame.yy#L1-L47)
* [magician L1-L5](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Create_0.gml#L1-L5)
* [magician L1-L17](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L1-L17)

---

## Projectile Object Structure

The `Obj_bullet_flame` object is defined with the following properties:

| Property | Value | Description |
| --- | --- | --- |
| Object Name | `Obj_bullet_flame` | Player projectile identifier |
| Sprite | `Spr_bullet_flame` | Visual representation of the flame bullet |
| Parent Object | `null` | No inheritance hierarchy |
| Persistent | `false` | Does not persist across room transitions |
| Physics Object | `false` | Uses standard GameMaker movement |
| Solid | `false` | Does not block other objects |
| Visible | `true` | Rendered during gameplay |

The object registers 10 distinct event handlers:

| Event Type | Event Target | Handler File |
| --- | --- | --- |
| Create (0) | - | `Create_0.gml` |
| Step (3) | - | `Step_0.gml` |
| Collision (4) | `Obj_colision_block` | Collision event |
| Collision (4) | `Obj_phantom` | Collision event |
| Collision (4) | `Obj_boss` | Collision event |
| Collision (4) | `Obj_phantom_green` | Collision event |
| Collision (4) | `Obj_phantom_green_1` | Collision event |
| Collision (4) | `Obj_phantom1` | Collision event |
| Collision (4) | `Obj_phantom_red` | Collision event |
| Collision (4) | `Obj_phantom_red_1` | Collision event |

**Sources:**

* [magician L1-L47](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Obj_bullet_flame.yy#L1-L47)

---

## Projectile Lifecycle

The following diagram illustrates the complete lifecycle of a player projectile from creation to destruction:

```

```

**Sources:**

* [magician L1-L5](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Create_0.gml#L1-L5)
* [magician L1-L17](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L1-L17)

---

## Initialization and Targeting

When `Obj_bullet_flame` is instantiated, the `Create_0.gml` event configures the projectile's movement parameters:

```
speed = 8;
direction = point_direction(x, y, mouse_x, mouse_y);
```

### Targeting Mechanism

The projectile uses GameMaker's `point_direction()` function to calculate the angle between the bullet's spawn position (`x`, `y`) and the mouse cursor's world position (`mouse_x`, `mouse_y`). This direction is locked at creation time, meaning the projectile travels in a straight line and does not track or follow the mouse cursor after firing.

### Movement Parameters

| Parameter | Value | Unit | Description |
| --- | --- | --- | --- |
| `speed` | 8 | pixels/frame | Linear velocity magnitude |
| `direction` | calculated | degrees | Angle toward mouse at creation |

The movement speed of 8 pixels per frame results in a travel speed of 480 pixels per second at 60 FPS.

**Sources:**

* [magician L1-L5](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Create_0.gml#L1-L5)

---

## Movement and Boundary Handling

Every frame, the `Step_0.gml` event executes the following logic:

### Vector-Based Movement

```

```

The projectile decomposes its `speed` and `direction` into x and y components using `lengthdir_x()` and `lengthdir_y()`, then applies these as position deltas. This standard GameMaker pattern ensures consistent diagonal movement speeds.

### Collision Detection During Movement

The step event performs manual collision checking with `Obj_colision_block` using `place_meeting()`:

```

```

This check executes **after** position updates, meaning the bullet moves into the collision block before detecting it. The projectile is immediately destroyed on wall contact.

### Out-of-Bounds Detection

```

```

Projectiles that travel beyond the room boundaries are automatically destroyed to prevent memory leaks from off-screen projectiles.

**Sources:**

* [magician L1-L17](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L1-L17)

---

## Collision Event Matrix

The `Obj_bullet_flame` object registers collision events with 9 distinct entity types. Each collision event is registered in the object definition but implemented in separate collision handler files.

### Enemy Collision Table

| Collision Target | Object Type | Event Type | Notes |
| --- | --- | --- | --- |
| `Obj_boss` | Boss enemy | Collision (4) | Primary boss entity |
| `Obj_phantom` | Gray phantom | Collision (4) | Basic phantom enemy |
| `Obj_phantom1` | Gray phantom variant | Collision (4) | Duplicate phantom type |
| `Obj_phantom_green` | Green phantom | Collision (4) | Green variant phantom |
| `Obj_phantom_green_1` | Green phantom variant | Collision (4) | Secondary green phantom |
| `Obj_phantom_red` | Red phantom | Collision (4) | Red variant phantom |
| `Obj_phantom_red_1` | Red phantom variant | Collision (4) | Secondary red phantom |

### Environmental Collision

| Collision Target | Object Type | Event Type | Notes |
| --- | --- | --- | --- |
| `Obj_colision_block` | Wall/obstacle | Collision (4) | Level geometry |

**Note:** The object definition shows separate collision events for `Obj_phantom`, `Obj_phantom1`, and multiple variants of green and red phantoms. This suggests either:

1. Multiple phantom object types exist for different spawn contexts
2. Phantom objects were duplicated during development
3. Variants represent different difficulty tiers or behaviors

The collision event registration does not specify the damage values or audio feedback in the object definition file itself—these are implemented in the individual collision event handler scripts.

**Sources:**

* [magician L6-L14](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Obj_bullet_flame.yy#L6-L14)

---

## Collision Handling Patterns

While the individual collision handler scripts are not provided in the files, the system architecture follows a consistent pattern based on the combat system design:

### Standard Collision Handler Flow

```

```

This pattern ensures:

1. Damage is applied before destruction checks
2. Both projectile and enemy can be destroyed in the same frame
3. Audio feedback plays for enemy defeats
4. Projectiles are always consumed on impact

**Sources:**

* Based on combat architecture patterns described in high-level system diagrams
* [magician L8-L14](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Obj_bullet_flame.yy#L8-L14)

---

## Integration with Player System

The `Obj_bullet_flame` projectile system integrates with the player character through the `scrHandleShooting` script (referenced in high-level architecture). The typical firing sequence:

1. Player clicks mouse button (input detection in `Obj_magician`)
2. `scrHandleShooting` script checks firing cooldown
3. If cooldown permits, script calls `instance_create_layer()` or `instance_create_depth()` to spawn `Obj_bullet_flame` at player position
4. Newly created bullet executes `Create_0.gml`, calculating direction to current mouse position
5. Bullet enters game loop, executing `Step_0.gml` each frame until collision or boundary exit

The shooting script resides in the player object's logic, not in the projectile object itself. This separation follows a producer-consumer pattern where the player produces projectiles and the projectiles independently manage their own behavior.

**Sources:**

* High-level combat system architecture diagram
* [magician L1-L5](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Create_0.gml#L1-L5)

---

## Technical Considerations

### Collision Detection Order

GameMaker executes collision events **after** the Step event completes. This means:

1. `Step_0.gml` moves the projectile and checks `place_meeting()` for walls
2. Built-in collision events fire for enemy overlaps
3. Multiple collision events can fire in a single frame if the bullet moves through overlapping enemies

The manual `place_meeting()` check in the Step event handles walls because `Obj_colision_block` may be marked as `solid`, and the collision event might not reliably fire for solid objects depending on GameMaker version and physics settings.

### Projectile Cleanup

Three distinct mechanisms destroy projectiles:

1. **Wall collision**: Detected via `place_meeting()` in Step event
2. **Boundary exit**: Checked via coordinate comparison in Step event
3. **Enemy collision**: Handled by individual collision event scripts

This redundancy ensures no projectile persists indefinitely, preventing performance degradation from accumulated off-screen instances.

### Performance Optimization

The projectile object has `persistent = false`, meaning all instances are automatically destroyed when the player transitions to a new room. This prevents projectiles from carrying over between game areas.

**Sources:**

* [magician L9-L17](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L9-L17)
* [magician L24](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Obj_bullet_flame.yy#L24-L24)

---

## Related Systems

For detailed documentation of specific collision handling logic:

* Boss collision damage calculation: See [Boss Collision Handling](/axchisan/Haunted_hollow/5.3-boss-collision-handling)
* Phantom enemy collision variations: See [Phantom Enemy Collisions](/axchisan/Haunted_hollow/5.4-phantom-enemy-collisions)
* Wall and obstacle collision: See [Environment Collision Detection](/axchisan/Haunted_hollow/5.5-environment-collision-detection)
* Projectile initialization details: See [Projectile Initialization and Movement](/axchisan/Haunted_hollow/5.2-projectile-initialization-and-movement)
* Player attack triggering: See Player System documentation
* Enemy health management: See [Boss Enemy System](/axchisan/Haunted_hollow/4-boss-enemy-system)
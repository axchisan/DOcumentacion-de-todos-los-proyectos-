# Environment Collision Detection

> **Relevant source files**
> * [magician project1/objects/Obj_bullet_flame/Collision_Obj_colision_block_1.gml](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Collision_Obj_colision_block_1.gml)
> * [magician project1/objects/Obj_bullet_flame/Step_0.gml](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml)

## Purpose and Scope

This document explains how player projectiles interact with level geometry and environmental collision blocks. When `Obj_bullet_flame` projectiles collide with `Obj_colision_block` objects or travel outside room boundaries, they are immediately destroyed to prevent bullets from passing through walls or persisting indefinitely.

This page specifically covers environment collision detection for player projectiles. For projectile collision with enemies, see [Boss Collision Handling](/axchisan/Haunted_hollow/5.3-boss-collision-handling) and [Phantom Enemy Collisions](/axchisan/Haunted_hollow/5.4-phantom-enemy-collisions). For projectile creation and movement, see [Projectile Initialization and Movement](/axchisan/Haunted_hollow/5.2-projectile-initialization-and-movement).

---

## Overview

The environment collision system ensures that player projectiles behave realistically when encountering level geometry. The system uses two parallel detection mechanisms to handle collisions with `Obj_colision_block` instances and implements boundary checking to destroy projectiles that leave the playable area.

**Sources:** [magician L1-L17](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L1-L17)

 [magician L1-L3](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Collision_Obj_colision_block_1.gml#L1-L3)

---

## Collision Detection Mechanisms

The system implements two distinct mechanisms for detecting and handling collisions between `Obj_bullet_flame` and `Obj_colision_block`:

### Collision Event Handler

GameMaker's built-in collision event system triggers automatically when two instances overlap. The `Collision_Obj_colision_block_1` event executes the moment `Obj_bullet_flame` physically touches an `Obj_colision_block` instance:

```
instance_destroy();
```

This immediate destruction prevents bullets from penetrating walls or passing through solid geometry.

**Sources:** [magician L1-L3](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Collision_Obj_colision_block_1.gml#L1-L3)

### Step-Based Detection

A secondary check occurs every frame in the Step event using `place_meeting()`:

```
if (place_meeting(x, y, Obj_colision_block)) {
    instance_destroy();
}
```

This provides redundancy and ensures collision detection even if the collision event fails to trigger due to timing issues or high-speed movement.

**Sources:** [magician L9-L12](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L9-L12)

---

## Dual Detection Architecture

```

```

**Diagram: Dual Collision Detection System**

This architecture provides fault tolerance: if high-speed movement causes the bullet to "skip over" a collision block between frames (tunneling), the step-based `place_meeting()` check catches the collision. If the collision event triggers successfully, the bullet is destroyed before the step check executes.

**Sources:** [magician L9-L12](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L9-L12)

 [magician L1-L3](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Collision_Obj_colision_block_1.gml#L1-L3)

---

## Boundary Detection

In addition to collision block detection, the system destroys projectiles that travel outside the room's playable area:

```
if (x < 0 || x > room_width || y < 0 || y > room_height) {
    instance_destroy();
}
```

This check occurs every frame in the Step event and prevents bullets from persisting indefinitely in empty space, which would waste memory and processing resources.

### Boundary Conditions

| Condition | Description | Action |
| --- | --- | --- |
| `x < 0` | Bullet exits left edge of room | Destroy instance |
| `x > room_width` | Bullet exits right edge of room | Destroy instance |
| `y < 0` | Bullet exits top edge of room | Destroy instance |
| `y > room_height` | Bullet exits bottom edge of room | Destroy instance |

**Sources:** [magician L14-L17](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L14-L17)

---

## Complete Collision Flow

```

```

**Diagram: Frame-by-Frame Collision Detection Sequence**

**Sources:** [magician L1-L17](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L1-L17)

 [magician L1-L3](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Collision_Obj_colision_block_1.gml#L1-L3)

---

## Implementation Details

### Step Event Execution Order

The Step event processes collision detection in this sequence:

1. **Movement Calculation** [lines 4-7](https://github.com/axchisan/Haunted_hollow/blob/96079758/lines 4-7) : Calculate `move_x` and `move_y` using `lengthdir_x()` and `lengthdir_y()`
2. **Position Update** [lines 6-7](https://github.com/axchisan/Haunted_hollow/blob/96079758/lines 6-7) : Apply movement to bullet position
3. **Collision Block Check** [lines 10-12](https://github.com/axchisan/Haunted_hollow/blob/96079758/lines 10-12) : Use `place_meeting()` to detect `Obj_colision_block` collision
4. **Boundary Check** [lines 15-16](https://github.com/axchisan/Haunted_hollow/blob/96079758/lines 15-16) : Verify position is within room bounds

### Why Two Detection Methods?

The dual detection system addresses a common issue in high-speed projectile systems:

**Collision Event Limitation**: GameMaker's collision events detect overlap between instances. If a bullet moves very quickly (high speed value), it may "jump over" a thin collision block between frames, never overlapping and thus never triggering the collision event.

**Step Check Solution**: The `place_meeting()` function checks if the bullet's current position overlaps any `Obj_colision_block` instances. This catches collisions even after the bullet has moved past the block.

### Performance Considerations

| Method | Performance | Reliability | Use Case |
| --- | --- | --- | --- |
| Collision Event | High (automatic) | Good for normal speeds | Primary detection |
| `place_meeting()` | Medium (per-frame check) | Excellent for all speeds | Redundancy and fast projectiles |
| Boundary Check | Low (simple arithmetic) | Perfect | Memory cleanup |

**Sources:** [magician L1-L17](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L1-L17)

 [magician L1-L3](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Collision_Obj_colision_block_1.gml#L1-L3)

---

## Code Entity Reference

### Key Objects

* **`Obj_bullet_flame`**: Player projectile object that undergoes collision detection
* **`Obj_colision_block`**: Collision geometry representing walls and solid level elements

### Key Functions

* **`instance_destroy()`**: GameMaker built-in function that removes the calling instance from the game
* **`place_meeting(x, y, object)`**: Checks if the given position overlaps any instance of the specified object type
* **`lengthdir_x(len, dir)`**: Calculates X component of movement vector
* **`lengthdir_y(len, dir)`**: Calculates Y component of movement vector

### Key Variables

* **`x`, `y`**: Bullet position coordinates
* **`speed`**: Bullet velocity magnitude
* **`direction`**: Bullet travel angle in degrees
* **`room_width`**: Width of current room in pixels
* **`room_height`**: Height of current room in pixels

**Sources:** [magician L1-L17](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Step_0.gml#L1-L17)

 [magician L1-L3](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_bullet_flame/Collision_Obj_colision_block_1.gml#L1-L3)
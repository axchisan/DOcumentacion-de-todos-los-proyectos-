# Decorative Objects

> **Relevant source files**
> * [magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy)
> * [magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy)

## Purpose and Scope

This document covers the decorative environmental objects in Haunted Hollow, specifically the barrel objects used to enhance visual atmosphere in game levels. These objects serve purely aesthetic purposes and do not affect gameplay mechanics. For collision-based environmental objects that block player movement, see page [6](/axchisan/Haunted_hollow/6-level-design-and-environment) Level Design and Environment.

## Inheritance Structure

Decorative objects in Haunted Hollow follow an object-oriented inheritance pattern. All decorative elements inherit from a common parent object `Obj_decorativo_parent`, which establishes shared baseline properties. This architecture allows for consistent behavior across all decorative objects while enabling individual variants.

```

```

**Sources:**

* [magician L12-L15](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy#L12-L15)
* [magician L12-L15](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy#L12-L15)

## Object Configuration

Both barrel objects share identical configuration properties inherited from their parent. The following table summarizes their common characteristics:

| Property | Value | Description |
| --- | --- | --- |
| `solid` | `false` | Objects do not block player or enemy movement |
| `visible` | `true` | Objects are rendered in the game scene |
| `persistent` | `false` | Objects are destroyed when leaving the room |
| `physicsObject` | `false` | Objects do not participate in physics simulation |
| `eventList` | `[]` | No custom event handlers defined |

**Sources:**

* [magician L4-L38](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy#L4-L38)
* [magician L4-L38](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy#L4-L38)

## Physics Properties

Despite having `physicsObject` set to `false`, both barrel objects retain GameMaker's default physics property definitions for potential future use:

```mermaid
flowchart TD

Barrel["Obj_barrel_*"]
AngularDamp["physicsAngularDamping: 0.1"]
Density["physicsDensity: 0.5"]
Friction["physicsFriction: 0.2"]
Group["physicsGroup: 1"]
Kinematic["physicsKinematic: false"]
LinearDamp["physicsLinearDamping: 0.1"]
Restitution["physicsRestitution: 0.1"]
Sensor["physicsSensor: false"]
Shape["physicsShape: 1"]

Barrel --> AngularDamp
Barrel --> Density
Barrel --> Friction
Barrel --> Group
Barrel --> Kinematic
Barrel --> LinearDamp
Barrel --> Restitution
Barrel --> Sensor
Barrel --> Shape
```

These properties are inactive since `physicsObject` is disabled, meaning barrels function as simple visual decorations without collision or physics interactions.

**Sources:**

* [magician L17-L28](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy#L17-L28)
* [magician L17-L28](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy#L17-L28)

## Barrel Variants

The game includes two barrel variants differentiated solely by their visual representation:

### Obj_barrel_big

The large barrel variant uses the `Spr_barrel_big` sprite for rendering. This object is defined at [magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy)

**Key Properties:**

* **Sprite:** `Spr_barrel_big` [magician L33-L36](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy#L33-L36)
* **Parent Folder:** `Level` [magician L8-L11](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy#L8-L11)
* **Parent Object:** `Obj_decorativo_parent` [magician L12-L15](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy#L12-L15)

### Obj_barrel_small

The small barrel variant uses the `Spr_barrel_small` sprite for rendering. This object is defined at [magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy)

**Key Properties:**

* **Sprite:** `Spr_barrel_small` [magician L33-L36](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy#L33-L36)
* **Parent Folder:** `Level` [magician L8-L11](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy#L8-L11)
* **Parent Object:** `Obj_decorativo_parent` [magician L12-L15](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy#L12-L15)

## Object Lifecycle

The following diagram illustrates the lifecycle of decorative barrel objects from project definition to runtime rendering:

```mermaid
sequenceDiagram
  participant mague.yyp Project
  participant Obj_barrel_*.yy Definition
  participant Obj_decorativo_parent
  participant Game Room
  participant GameMaker Renderer

  mague.yyp Project->>Obj_barrel_*.yy Definition: "Load object definition"
  Obj_barrel_*.yy Definition->>Obj_decorativo_parent: "Inherit properties"
  Game Room->>Obj_barrel_*.yy Definition: "Instance created at design time"
  note over Game Room: "Room loads during gameplay"
  Game Room->>GameMaker Renderer: "Render Spr_barrel_* sprite"
  GameMaker Renderer->>GameMaker Renderer: "Draw at object position"
  note over Game Room: "Player leaves room"
  Game Room->>Obj_barrel_*.yy Definition: "Instance destroyed (persistent=false)"
```

Since `persistent` is `false`, barrel instances are destroyed when the player transitions between rooms and must be recreated when returning.

**Sources:**

* [magician L16](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy#L16-L16)
* [magician L16](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy#L16-L16)

## Integration with Level System

Decorative objects are organized within the project's folder hierarchy under the `Level` category. This categorization groups all level-related assets, including collision blocks, obstructions, and decorative elements:

```mermaid
flowchart TD

Root["Objects Root"]
LevelFolder["Level Folder"]
DecoParent["Obj_decorativo_parent"]
BigBarrel["Obj_barrel_big"]
SmallBarrel["Obj_barrel_small"]
OtherLevel["Other Level Objects..."]

Root --> LevelFolder
LevelFolder --> DecoParent
LevelFolder --> BigBarrel
LevelFolder --> SmallBarrel
LevelFolder --> OtherLevel
BigBarrel --> DecoParent
SmallBarrel --> DecoParent
```

This organizational structure allows level designers to easily identify and place decorative objects when designing game rooms.

**Sources:**

* [magician L8-L11](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy#L8-L11)
* [magician L8-L11](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy#L8-L11)

## Technical Specifications

Both barrel objects share the following GameMaker resource metadata:

| Field | Value | Purpose |
| --- | --- | --- |
| `$GMObject` | `""` | GameMaker object type identifier |
| `resourceType` | `"GMObject"` | Resource classification |
| `resourceVersion` | `"2.0"` | GameMaker resource format version |
| `managed` | `true` | Object is managed by GameMaker runtime |
| `overriddenProperties` | `[]` | No parent properties are overridden |
| `properties` | `[]` | No custom properties defined |
| `spriteMaskId` | `null` | No collision mask sprite |

The absence of `overriddenProperties` and custom `properties` indicates that these objects rely entirely on inherited behavior from `Obj_decorativo_parent` with only sprite differentiation.

**Sources:**

* [magician L1-L37](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_big/Obj_barrel_big.yy#L1-L37)
* [magician L1-L37](https://github.com/axchisan/Haunted_hollow/blob/96079758/magician project1/objects/Obj_barrel_small/Obj_barrel_small.yy#L1-L37)
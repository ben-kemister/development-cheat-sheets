---
tool: unreal_enfine
name: Unreal Engine
tags:
 - development
 - Gaming
--- 

TODO
<!--more-->

## Terms

Blendspace - a way to blend different annimations together
Animatiom Blue Print - Allows you to use animations within a Blue Print
Socket - Allows you to attached weapson/items to a skeliton

Cast - Allows you to access variables from another class

Animation Notifies - Used to add sounds and effects (such as particle effects) to an animation
Amination Montage - Allows you to call animations from a Blue Print
    ** To play montage you need to add a Default slot to your AnimGraph
Widgets - Used to add user interface on your character
Physics Asset - A group of different capsuals around a skeletal mesh
Physical Material/Surface - an object that can be assigned to surfaces in order to differentiate how hits/collisions effects are handled 


## Handy Hints & Tips

| Task/Action | How to |
| ----- | ---- | 
| Remove a node link | Alt + click on input or output point of the line |

## Play In Editor (PIE)

Play In Editor (PIE) starts the game at the Player Start location and give you control of the player character.

### Keyboard shortcuts

| Shortcut | Description |
| ---- | ----|
| `Alt + P` | Starts PIE mode |
| `Shift + F1` | Unlocks the mouse cursor from the PIE window |

## Blueprints

### Handy Nodes

| Node name | Description | 
| --- | --- |
| Is Valid | Checks that the variable/object is empty/null |




## Collisions

### Projectile collisions

If you are having trouble getting the _Pysical Surface_ from the _Phys Mat_ of your Hit. Enabled the `Return Material on Move` checkbox of your projectile Blueprint.

Projectile_BP --> Collision --> Advanced --> Return Material on Move (Ticked)

[Source](https://forums.unrealengine.com/t/spawn-emitter-on-hit-using-surface-types/69780/9)

## Troubleshooting

### Assertion failed: WorldPartition

This is likely due to the engine unable to open the default map. 
Open the `<game_dir>/Config/DefaultEngine.ini` and set `EditorStartupMap=` to nothing.

See: [Stackoverflow](https://stackoverflow.com/questions/68628136/unreal-engine-5-assertion-failed-world-partition)


## Links

### Tutorials


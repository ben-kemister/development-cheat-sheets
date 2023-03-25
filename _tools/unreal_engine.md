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
Amimation Montage - Allows you to call animations from a Blue Print, animation montage also handles the blending of the animation with the characters current movements.
    ** To play montage you need to add a **Default slot** to your AnimGraph
    ** 
Widgets - Used to add user interface on your character
Physics Asset - A group of different capsuals around a skeletal mesh
Physical Material/Surface - an object that can be assigned to surfaces in order to differentiate how hits/collisions effects are handled 


## Map Editor

| Shortcut   | Description                                        |
|------------|----------------------------------------------------| 
| `Ctrl + D` | Duplicate the current item                         |
| `SPACE`    | Cycle through the placement/rotation/scale widgets |

## Play In Editor (PIE)

Play In Editor (PIE) starts the game at the Player Start location and give you control of the player character.

### Keyboard shortcuts

| Shortcut | Description |
| ---- | ----|
| `Alt + P` | Starts PIE mode |
| `Shift + F1` | Unlocks the mouse cursor from the PIE window |
| `P` | (In Level editor) Shows collisions |

## Blueprints

### Handy Hints & Tips

| Task/Action | How to |
| ----- | ---- | 
| Remove a node link | Alt + click on input or output point of the line |
| Split a node link | Double click on the node link |

### Handy Nodes

| Node name | Description | Link(s) |
| --- | --- | --- |
| Is Valid | Checks that the variable/object is empty/null ||
| Format Text | Allows you to format text (and Strings) | [Formatting](https://docs.unrealengine.com/4.27/en-US/ProductionPipelines/Localization/Formatting/)|

## Animation Blue Prints

Make sure you have a `Default Slot` before your output pose so that you can play animation montages, such as death and attack montages.

## Animation Montage

### Death Montage

Make sure you uncheck the `Enable Auto Blend out` for death montages to prevent the animation resetting to the default pose after the death animation has been played.

### Audio Attenuation

If your attenuation is not working correctly you may need to set the Virtualization Mode of the audio to `Play when Silent`.

Sound Wave --> Details --> Voice Management --> Virtualization Mode --> Play when Silent

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


### Complex collisions only return default physical material

This might be [due to a bug in UE](https://forums.unrealengine.com/t/physical-material-override-w-complex-collision-doesnt-work/417801) but the work around is to set the `Phys Material Override` on the Static Mesh Component in the Map/level.


## Links

### Tutorials

* [Create a Zombie First Person Shooter Game | Unreal Engine 5 Beginner Tutorial](https://www.youtube.com/watch?v=qOam3QjGE8g)
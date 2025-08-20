# Cost Layer Generation

- Input: (Optimized) Triangle Mesh
- Output: Cost Layers

Cost layers are used inside of mesh navigation to assign parts of the mesh different properties that can be considered for path planning and local planning. Such cost layers can be used:

- traversibility properties such as roughness, friction, etc.
- lethal zones: such as borders of cliffs
- prohibition areas where the robot should not drive because of certain safety regulations
- obstacle layers: recording low-latency obstacle updates which can be used to avoid obstacles either in the planners or in the controllers

Some useful cost layers are already implemented such as

- roughness
- height difference
- border

All these layers are described in [mesh navigation](https://github.com/naturerobots/mesh_navigation). In addition, [mesh navigation](https://github.com/naturerobots/mesh_navigation) provides simple interfaces to implement cost layers as plugins which is described here: [Link](/tutorials/plugins/own_cost_layer.md).

## Graph Layer System

TODO

## Layer Overview

TODO

### Obstacle Layer

This happens when you ignore obstacles:

![type:video](/media/meshnav_football1.webm)

The obstacle layer helps us there in painting points scanned on dynamic obstacles onto the ground:

![type:video](/media/meshnav_football2.webm)

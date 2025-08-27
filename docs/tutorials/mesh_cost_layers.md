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

The obstacle layer enables the planner to recognize and navigate around obstacles.
It subscribes to a topic providing obstacle points (e.g., from pre-segmented LiDAR data) and projects these points down onto the mesh surface. The vertices of the intersected faces are then marked as lethal, preventing the planner from finding paths through them.

In most setups, an inflation layer is added directly after the obstacle layer. This layer expands the lethal regions to form a safety margin or buffer zone around detected obstacles. Within this zone, the robot can still plan paths, but it is encouraged to move cautiously and maintain a safe distance from potentially dynamic or unknown objects.

To start with why obstacle avoidance is important, we start with showing what happens when you ignore it.


```bash
ros2 launch mesh_navigation_tutorials world_name:=tray
```

Then:

1. Place a ball in front of the robot using the Gazebo GUI elements top left.
2. Go the RViz window and select a goal pose behind the ball (the dynamic obstacle).

You will see the robot crashing into the ball.

![type:video](/media/meshnav_football1.webm)

Unless you want to create a soccer robot, this is usually not a wanted behavior. Especially in reality, when it's not a ball but a human being.
Fortunately, the obstacle layer can help us here.
First, we need some kind of pre-filtered scan that only contains points of the dynamic points.

Luckily, the integrated localization package [RMCL](https://github.com/uos/rmcl) already contains nodes to do that a very simplistic but fairly fast filtering of our scan points based on the mesh map.
It removes points of a LiDAR scan that are close to the map. The remaining points are classified as "unexpected" and can pretty well be used as input for the obstacle layer.

!!! Warning

    The following command requires [RMCL](https://github.com/uos/rmcl) to be installed.

Enable this pre-segmentation by calling

```bash
ros2 launch mesh_navigation_tutorials world_name:=tray obtacle_segmentation:=rmcl_seg
```

After repeating the steps 1. and 2. from above, the ball is detected as dynamic obstacle, projected onto the obstacle, and finally considered during planning.

![type:video](/media/meshnav_football2.webm)

#### Parameters

| Name | Type | Description |
|:----|:----|:------|
| `robot_height` | Float | Height of the robot (in meters) considered for projection. You can use this to let small robots drive below tables. |
| `max_obstacle_dist` | Float | Obstacles points farther away then this parameter's value are ignored. |
| `topic` | String | Input obstacle points topic of message type `sensor_msgs/PointCloud2` |

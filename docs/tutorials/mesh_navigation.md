# Mesh Navigation

Inputs:

- Mesh Geometry
- Cost Layers for Navigation
- Localization
- Target Pose

Output: Control Signals

## Parameters


```yaml
move_base_flex:
  ros__parameters:
    global_frame: 'map'
    robot_frame: 'base_footprint'
    odom_topic: 'odom'

    use_sim_time: true
    force_stop_at_goal: true
    force_stop_on_cancel: true

    planners: ['mesh_planner']
    mesh_planner:
      type: 'cvp_mesh_planner/CVPMeshPlanner'
      cost_limit: 0.99 # Vertices with costs higher than this value will be avoided. Has to be set *below* the inflation layer inscribed value to avoid obstacles in planning
      publish_vector_field: true

    planner_patience: 10.0
    planner_max_retries: 2
    project_path_onto_mesh: false

    controllers: ['mesh_controller']
    mesh_controller:
      type: 'mesh_controller/MeshController'
      ang_vel_factor: 7.0
      lin_vel_factor: 1.0

    controller_patience: 2.0
    controller_max_retries: 4
    dist_tolerance: 0.2
    angle_tolerance: 0.8
    cmd_vel_ignored_tolerance: 10.0

    mesh_map:
      # input
      # In this examples, `mesh_file` is set dynamically from the launch file. 
      # you can also set it statically:
      # mesh_file: '.../parking_garage.ply'
      mesh_part: '/'
      # storage
      # mesh_working_file: 'parking_garage.h5'
      mesh_working_part: 'mesh'

      # half-edge-mesh implementation
      # pmp (default), lvr
      hem: pmp

      # these are the layers the MeshMap will load into the layer graph
      layers:
        - border 
        - height_diff 
        - roughness 
        - static_combination 
        - static_inflation 
        - obstacle 
        - obstacle_inflation
        - final

      # this sets the layer used for planning and control
      default_layer: 'final'

      height_diff:
        type: 'mesh_layers/HeightDiffLayer'
        combination_weight: 1.0
        threshold: 0.2

      border:
        type: 'mesh_layers/BorderLayer'
        combination_weight: 1.0
        border_cost: 1.0
        threshold: 0.2

      roughness:
        type: 'mesh_layers/RoughnessLayer'
        combination_weight: 1.0
        threshold: 0.8

      static_combination:
        # This layer combines the three input layers by linearly combining vertex costs:
        # -> vertex_cost = layer1.combination_weight * layer1.vertex_cost + layer2.combination_weight * layer2.vertex_cost + ...
        # The input layers' lethal vertices are accumulated in a combined set
        type: 'mesh_layers/CombinationLayer'
        # The `inputs` parameter determines which layers are processed by this layer.
        # If a layer does not process inputs, this parameter can be omitted
        inputs: ['height_diff', 'border', 'roughness']

      static_inflation:
        type: 'mesh_layers/InflationLayer'
        inputs: ['static_combination']
        combination_weight: 1.0
        inflation_radius: 1.5 # outer ring
        inscribed_radius: 0.4 # inner ring
        lethal_value: .inf
        inscribed_value: 1.0
        repulsive_field: false
        cost_scaling_factor: 2.0

      obstacle:
        type: 'mesh_layers/ObstacleLayer'
        combination_weight: 1.0
        robot_height: 0.75
        max_obstacle_dist: 10.0
        topic: 'obstacle_points'

      obstacle_inflation:
        type: 'mesh_layers/InflationLayer'
        inputs: ['obstacle']
        combination_weight: 1.0
        inflation_radius: 1.5 # outer ring
        inscribed_radius: 0.4 # inner ring
        lethal_value: .inf
        inscribed_value: 1.0
        repulsive_field: false
        cost_scaling_factor: 2.0

      final:
        type: 'mesh_layers/MaxCombinationLayer'
        inputs: ['static_inflation', 'obstacle_inflation']

      # An edge cost equals the total costs that are collected along a 
      # triangle's edge by linearly interpolating the combined vertex costs
      # This factor defines the factor that is used for the final edge weight
      # -> edge_weight = edge_length + edge_cost_factor * edge_cost
      # This edge_weight is then passed to the global planner to search for 
      # the best path
      # Note: Set this to 0.0 if you need the shortest path
      edge_cost_factor: 5.0

      # debug function: enable this, for publishing text markers with edge costs
      publish_edge_weights_text: false

      # debug/development function: enable this to log the update times of changing layers to a csv file
      enable_layer_timer: false
```

### General Parameters (MBF)

The following parameters are related to [Move Base Flex](https://github.com/naturerobots/move_base_flex).
If you miss some information here, we refer to MBFs documentation.

```yaml
global_frame: 'map'
robot_frame: 'base_footprint'
odom_topic: 'odom'

force_stop_at_goal: true
force_stop_on_cancel: true

controller_patience: 2.0
controller_max_retries: 4
dist_tolerance: 0.2
angle_tolerance: 0.8
cmd_vel_ignored_tolerance: 10.0

planner_patience: 10.0
planner_max_retries: 2
```

The parameter `global_frame` sets the frame where the map is located in.
The parameter `robot_frame` sets the frame of the robot base.
A [localization](/tutorials/localization.md) estimates the connection between the `global_frame` and the `robot_frame`, so make sure you have one running.
You can check this by visualizing the tf-tree and searching for a path from the `global_frame` to the `robot_frame`. 

`odom_topic` points to a topic where an odometry estimation of your robot is published to.
The published messages have to be of type `nav_msgs/Odometry`.
This `odom_topic` may provide motion information in higher frequency and is internally used to extrapolate from the last map localization.
This becomes especially relevant if you have a low frequency localization in your map as it ensures short state estimation cycles for the controller.

In reality, state estimation errors are propagated to the controller, as it relies on it.
In the worst case, the localization relative to the map is completely lost, which causes the whole navigation to fail. 
For the [tutorials](https://github.com/naturerobots/mesh_navigation_tutorials), however, the localization is perfect by default as it is provided by the Gazebo simulation.
This helps to develop planning or controlling strategies since we don't inherit errors from the localization system.
This means, if our newly developed planner or controller fails, we can be sure it's our fault.
However, in order to check whether our newly developed planner or controller really works reliably, we must also take possible localization errors into account.
Therefore, we are working on including localization strategies directly in the tutorials as well.
Some of them are described here: [Link](/tutorials/localization.md).


The other parameters are related to [Move Base Flex](https://github.com/naturerobots/move_base_flex).
If you need more information about them, we refer to MBFs documentation.


### Mesh Planners and Controllers

The following parameters are describing what planners and controllers are loaded and how they are parameterized.

```yaml
# planner
planners: ['mesh_planner']
mesh_planner:
  type: 'cvp_mesh_planner/CVPMeshPlanner'
  cost_limit: 0.8
  publish_vector_field: true
# controller
controllers: ['mesh_controller']
mesh_controller:
  type: 'mesh_controller/MeshController'
  ang_vel_factor: 7.0
  lin_vel_factor: 1.0
```

In general, they are described in the exact same way you would do it for the 2d costmap navigation implementation of Move Base Flex - which is currently not existing for ROS 2, however you can orient at ROS 1 examples.
The only difference for mesh navigation is that we are only allowed to load planners and controllers that can handle to move the robot over a triangle mesh's surface.


The `planners` list contains the planners names, the `controllers` list contains the controllers names.
All names in `planners` and `controllers` point to certain key-value pairs in the parameter file.
In this example one mesh planner of type `cvp_mesh_planner/CVPMeshPlanner` is loaded and one controller of type `mesh_controller/MeshController` is used.

One core feature that of MBF is to let the user switch different planners and controllers at runtime.
For example, we might want to switch the robot's driving mode from a risky behavior to a more careful behavior for certain situations.
This can be simply achieved by renaming the existing controller and giving them different parameters as follows:

```yaml
# planner
planners: ['mesh_planner']
mesh_planner:
  type: 'cvp_mesh_planner/CVPMeshPlanner'
  cost_limit: 0.8
  publish_vector_field: true
# controller
controllers: ['mesh_controller_careful', 'mesh_controller_risky']
mesh_controller_careful:
  type: 'mesh_controller/MeshController'
  ang_vel_factor: 7.0
  lin_vel_factor: 1.0
mesh_controller_risky:
  type: 'mesh_controller/MeshController'
  ang_vel_factor: 20.0
  lin_vel_factor: 10.0
```

You can then use e.g. a behavior tree to select a pre-parameterized controller, depending on the situation.

### Mesh Map

The following parameters are used to describe general map information, the way the cost-layers are computed, and which cost-layers are considered for navigation.


```yaml
mesh_map:
  mesh_file: 'path/to/my_layered_mesh_map.h5'
  mesh_part: 'mesh' # reference to hdf5 mesh

  # list of available layers
  layers: ['border', 'height_diff', 'roughness', 'inflation']

  height_diff:
    type: 'mesh_layers/HeightDiffLayer'
    factor: 1.0
    threshold: 0.8

  border:
    type: 'mesh_layers/BorderLayer'
    factor: 1.0
    border_cost: 1.0
    threshold: 0.2

  roughness:
    type: 'mesh_layers/RoughnessLayer'
    factor: 1.0
    threshold: 0.8

  inflation:
    type: 'mesh_layers/InflationLayer'
    factor: 1.0
    inflation_radius: 0.4
    inscribed_radius: 0.2
    lethal_value: 1.0
    inscribed_value: 0.8
    repulsive_field: false
```

`mesh_file` points to the map file. In the tutorials we set this parameter dynamically in the launch file [`mesh_navigation_tutorial_launch.py`](https://github.com/naturerobots/mesh_navigation_tutorials/blob/main/mesh_navigation_tutorials/launch/mesh_navigation_tutorial_launch.py).
`mesh_part` points to the group of the HDF5 file that contains the layered mesh map.
Our `my_layered_mesh_map.h5` has only one mesh group, as you can see by inspecting the file using HDFCompass:

![HDFCompass](/media/hdfcompass1.png)

(raw is not a mesh group).

You can use this feature to store many layered meshes in one file. This can become useful, for example, for large-scale environments where you have to start to split your map into chunks.

`layers` is a list of all the used layer keys. This parameter is mainly existing because in ROS 2 it is harder to get all the keys of a yaml map.
This list also defines the order a cost-layer is computed.
This matters e.g. for the inflation layer as it inflates all the layers that a loaded before, but not after.
Below `layers` is a list of cost-layer parameter maps.
You can find an explanation on the specific parameters for each cost layer in their documentation.



## Tutorials

To quickly get a feeling what these parameters are doing and how changing them influences the robots behavior, we recommend to start the examples from the [`mesh_navigation_tutorials`](https://github.com/naturerobots/mesh_navigation_tutorials).


```console
ros2 launch mesh_navigation_tutorials mesh_navigation_tutorial_launch.py world_name:=floor_is_lava
```

You change `floor_is_lava` by any world name that is available with this repository (see all by calling launch file with `--show-args`). Those are: 

| Name | World | Default Map | Description |
|------|-------|-----|-------------|
| tray | ![tray_world](/media/tray_world.png) | ![tray_map](/media/tray_map.png)| This world is a rectangular area with a wall around the perimeter. |
| floor_is_lava | ![floor_is_lava_world](/media/floor_is_lava_world.png) | ![floor_is_lava_map](/media/floor_is_lava_map.png)| This world contains a square area with with two pits and a connecting section at a slightly higher elevation.
| parking_garage | ![parking_garage_world](/media/parking_garage_world.png) | ![parking_garage_map](/media/parking_garage_map.png)| This world represents a parking garage with multiple floors connected by ramps. |

When running a simulated world, you can save some resources by not running the gazebo GUI: Add the `start_gazebo_gui:=False` launch argument.

A RViz window opens showing a mesh map.
This map is being used for navigation.

In order to make the robot move, find the "Mesh Goal" tool at the top.
With it, you can click on any part of the mesh.
Click and hold to set a goal pose.
The MbfGoalActions rviz plugin contains a very tiny state machine that performs the following actions:
* subscribe to that goal pose
* get a path to that pose
* execute that path

For your own application you may want to integrate mesh navigation into your own deliberation (state machine / behavior tree).
The following guide explains that: [Link](/tutorials/deliberation.md).


### Dynamic Reconfigure

*Warning*: This is untested but should work. (Or should work eventually)  

Most of the parameters can be changed at run time to fine tune certain cost-layer's parameters at runtime. Call

```console
ros2 run rqt_reconfigure rqt_reconfigure
```

An rqt window will open in which you can change parameters of different parts of the mesh navigation.


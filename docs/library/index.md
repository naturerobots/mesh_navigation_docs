# Library

This section gives a more in-depth description of MeshNav seen as a library / as a plugin system which might be of interest for 3D navigation developers. MeshNav provides plugin interfaces for

- Path Planning
- Motion Control
- Mesh Layers

which means, developer can use what's already there and implement own layers/planners/controllers on top, while beeing able to keep their code in their own codebases.

## Package Structure

This **[mesh_navigation](https://github.com/naturerobots/mesh_navigation)** stack provides a navigation server for **[Move Base Flex (MBF)](https://github.com/naturerobots/move_base_flex)**. It provides a couple of configuration files and launch files to start the navigation server with the configured layer plugins for the layered mesh map, and the configured planners and controller to perform path planning and motion control in 3D (or more specifically on 2D-manifold). 

The package structure is as follows:

* `mesh_navigation` The corresponding ROS meta package.

* `mbf_mesh_core` contains the plugin interfaces derived from the abstract MBF plugin interfaces to initialize planner and controller plugins with one `mesh_map` instance. It provides the following three interfaces: 
    - MeshPlanner - `mbf_mesh_core/mesh_planner.h`
    - MeshController - `mbf_mesh_core/mesh_controller.h`
    - MeshRecovery - `mbf_mesh_core/mesh_recovery.h`

* `mbf_mesh_nav` contains the mesh navigation server which is built on top of the abstract MBF navigation server. It uses the plugin interfaces in `mbf_mesh_core` to load and initialize plugins of the types described above.

* `mesh_map` contains an implementation of a mesh map representation building on top of the generic mesh interface implemented in **[lvr2](https://github.com/uos/lvr2)**. This package provides a layered mesh map implementation. Layers can be loaded as plugins to allow a highly configurable 3D navigation stack for robots traversing on the ground in outdoor and rough terrain.

* `mesh_layers` The package provides a couple of mesh layers to compute trafficability/traversibility properties of the terrain. Furthermore, these plugins have access to the HDF5 map file and can load and store layer information. The mesh layers can be configured for the robots abilities and needs. Currently we provide the following layer plugins: 
    - HeightDiffLayer - `mesh_layers/HeightDiffLayer`
    - RoughnessLayer - `mesh_layers/RoughnessLayer`
    - SteepnessLayer - `mesh_layers/SteepnessLayer`
    - RidgeLayer - `mesh_layer/RidgeLayer`
    - ClearanceLayer - `mesh_layers/ClearanceLayer`
    - InflationLayer - `mesh_layers/InflationLayer`
    - BorderLayer - `mesh_layers/BorderLayer`
    - ObstacleLayer - `mesh_layers/ObstacleLayer`

* `dijkstra_mesh_planner` contains a mesh planner plugin providing a path planning method based on Dijkstra's algorithm. It plans by using the edges of the mesh map. The propagation start a the goal pose, thus a path from every accessed vertex to the goal pose can be computed. This leads to a sub-optimal potential field, which highly depends on the mesh structure.

* `cvp_mesh_planner` contains a Fast Marching Method (FMM) wave front path planner to take the 2D-manifold into account. This planner is able to plan over the surface, due to that it results in shorter paths than the `dijkstra_mesh_planner`, since it is not restricted to the edges or topology of the mesh. A comparison is shown below. Please refer to the paper `Continuous Shortest Path Vector Field Navigation on 3D Triangular Meshes for Mobile Robots`.
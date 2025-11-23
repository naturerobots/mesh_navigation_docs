# Mesh Planner and Controller

In classic mobile robot navigation, we like to distinguish between global path planning and local motion planning to balance long-term goals with real-time feasibility.
The global planner provides a high-level route through the environment, ensuring the robot moves efficiently toward its destination.
The local planner, in turn, adapts this route to the robot’s immediate surroundings and dynamics, handling obstacles and uncertainties as they appear.
Importantly, this concept remains the same regardless of the underlying representation: whether navigation is performed on a 2D grid map or in a 3D mesh-based environment.

## Global Planners

Mesh navigation provides several global planners designed to compute feasible paths across 3D surfaces. All implemented planners share the core principle of computing shortest paths on a triangular mesh. Depending on configuration, they can also take into account obstacle avoidance.

### Dijkstra Planner

The Dijkstra Planner implements the classic [Dijkstra's algorithm](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm). It operates on the graph representation of the triangle mesh, where mesh vertices represent graph nodes and mesh edges represent graph edges.

![Dijkstra potential field](/media/dijkstra_pot.jpg)

This planner is well-suited for applications where simplicity, robustness, and deterministic behavior are desired, but it is limited to edge-based paths rather than continuous trajectories over the surface.

### Continuous Vector Field Planner

The Continuous Vector Field Planner (CVP) extends beyond edge-based search by planning directly over the surface of the mesh instead of its connectivity graph.
CVP uses a wavefront propagation technique to generate a globally consistent vector field that encodes the continuous shortest path direction from any point on the mesh to the goal.
Therefore, can flow smoothly across triangle faces, producing shorter and more natural trajectories than the ones found by the Dijkstra planner.
Similar to Dijkstra, CVP can integrate surface costs, allowing navigation that avoids obstacles or prefers favorable terrain.
This makes CVP particularly useful for mobile robots operating on uneven or natural terrain, where edge-restricted paths would otherwise be suboptimal or unnatural.
Furthermore, CVP is less sensitive to the density of the triangles in the mesh.

![CVP potential field](/media/cvp_pot.jpg)

!!! note

    For the theoretical background and implementation details, see:

    ```bib
    @inproceedings{puetz21cvp,
        author = {Pütz, Sebastian and Wiemann, Thomas and Kleine Piening, Malte and Hertzberg, Joachim},
        title = {Continuous Shortest Path Vector Field Navigation on 3D Triangular Meshes for Mobile Robots},
        booktitle = {2021 IEEE International Conference on Robotics and Automation (ICRA)},
        year = 2021,
        url = {https://github.com/uos/mesh_navigation},
        note = {Software available at \url{https://github.com/uos/mesh_navigation}}
    }
    ```

    It is available on [IEEE Xplore](https://ieeexplore.ieee.org/document/9560981).

Config:

```yaml
mesh_planner:
  type: 'cvp_mesh_planner/CVPMeshPlanner'
  cost_limit: 0.99 # Vertices with costs higher than this value will be avoided. Has to be set *below* the inflation layer inscribed value to avoid obstacles in planning
  publish_vector_field: true
```

### Obstacle-aware planning

After the cost layers have been computed, the resulting vertex costs are transformed into edge costs inside the mesh map. In this step, the parameter of the mesh map `edge_cost_factor` determines how much the vertex costs are added (!) to the edge distance. We add the costs to the edge distances to preserve the admissible property of future heuristic search implementations: When using pre-computed distances or the air-line to the target as heuristics, the actual costs collected along the way will be always at least higher. 

Setting the `edge_cost_factor` parameter to zero will let the planners ignore all static obstacles. An example configuration that takes (static) obstacles into account while planning could be:

```yaml
mesh_map:
  # An edge cost equals the total costs that are collected along a 
  # triangle's edge by linearly interpolating the combined vertex costs
  # This factor defines the factor that is used for the final edge weight
  # -> edge_weight = edge_length + edge_cost_factor * edge_cost
  # This edge_weight is then passed to the global planner to search for 
  # the best path
  # Note: Set this to 0.0 if you need the shortest path
  edge_cost_factor: 5.0

  # [...] Other parameters of mesh_map
```


## Controller

Modern navigation on 3D surface meshes requires control strategies that can follow complex terrain while respecting the robot's kinematics.
MeshNav provides multiple controllers tailored to this task—from a simple vector-field–based controller to a full Model Predictive Path Integral (MPPI) controller.
This section introduces the available controllers, explains how they operate, and highlights when each should be used.


### Vector Field Controller (Default)

![Vector Field Controller](/media/vector_field_controller.png)

The vector field controller is the default controller used in the tutorials. It takes the vector field that was attached to the mesh by the planner and controls the robot along the field until it arrives at the desired goal.

Source Code: [mesh_controller](https://github.com/naturerobots/mesh_navigation/tree/main/mesh_controller).

??? note "No Obstacle Avoidance"

    In the current implementation (!), the vector field does not react to the new dynamic obstacle layer. Therefore, it is not possible to respond to dynamic obstacles. Implementing this capability would be a substantial improvement. Don't forget to open a PR!

### Mesh MPPI

[![MeshMPPI GIF](/media/mesh_mppi/mesh_mppi_floor_is_lava.gif)](https://github.com/uos/mesh_mppi)

MeshMPPI is an adaptation of the model predictive path integral (MPPI) control algorithm to surface meshes.
The MPPI algorithm generates control signals by simulating the trajectories resulting from a set of random samples.
This adaptation constraints the trajectory prediction to the surface defined by a triangular surface mesh.
The implementation provided in this repository implements the MeshMPPI algorithm for the ROS2-based MeshNav 3D navigation stack.

It can be used when extra attention is needed to ensure that motion planning is both kinematically and terrain feasible. It currently implements two kinematic models that can be used with either differential-drive or bicycle-drive robots. Furthermore, it reacts to the dynamic cost layers, enabling it to avoid dynamic or unmapped obstacles.

Source Code: [mesh_mppi](https://github.com/uos/mesh_mppi), developed at [Osnabrück University (UOS)](https://github.com/uos).




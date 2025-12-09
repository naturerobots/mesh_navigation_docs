# Control


In classic mobile robot navigation, we like to distinguish between global path planning and local motion planning to balance long-term goals with real-time feasibility.
The local planner (or controller), adapts a route to the robot’s immediate surroundings and dynamics, handling obstacles and uncertainties as they appear.
Modern navigation on 3D surface meshes requires control strategies that can follow complex terrain while respecting the robot's kinematics.
MeshNav provides multiple controllers tailored to this task—from a simple vector-field–based controller to a full Model Predictive Path Integral (MPPI) controller.
This section introduces the available controllers, explains how they operate, and highlights when each should be used.

## Vector Field Controller

![Vector Field Controller](/media/vector_field_controller.png)

The vector field controller is the default controller used in the tutorials. It takes the vector field that was attached to the mesh by the planner and controls the robot along the field until it arrives at the desired goal.

Source Code: [mesh_controller](https://github.com/naturerobots/mesh_navigation/tree/main/mesh_controller).

??? note "No Obstacle Avoidance"

    In the current implementation (!), the vector field does not react to the new dynamic obstacle layer. Therefore, it is not possible to respond to dynamic obstacles. Implementing this capability would be a substantial improvement. Don't forget to open a PR!

### Mesh MPPI

[![MeshMPPI GIF](/media/mesh_mppi/mesh_mppi_floor_is_lava.gif)](https://github.com/uos/mesh_mppi)

MeshMPPI is an adaptation of the model predictive path integral (MPPI) control algorithm to surface meshes.
The MPPI algorithm generates control signals by simulating the trajectories resulting from a set of random samples.
This adaptation constrains the trajectory prediction to the surface defined by a triangular mesh.
The implementation provided in this repository integrates the MeshMPPI algorithm into the ROS 2–based MeshNav 3D navigation stack.

It can be used when extra attention is needed to ensure that motion planning is both kinematically and terrain feasible. It currently implements two kinematic models that can be used with either differential-drive or bicycle-drive robots. Furthermore, it reacts to dynamic cost layers, enabling it to avoid dynamic or previously unmapped obstacles.
Additionally, it provides a mechanism to easily extend the controller with custom kinematic models.

Source Code: [mesh_mppi](https://github.com/uos/mesh_mppi), developed at [Osnabrück University (UOS)](https://github.com/uos).


# Creating MeshNav Plugins

MeshNav provides plugin interfaces that allow new cost layers to be developed by others who want to keep their code separate from the core MeshNav source. This document offers some starting points for exploring how a plugin can be implemented.

!!! note

    This guide currently only points to specific entry points in related code bases. However, it would greatly benefit from real examples. Feel free to open a PR with your improvements!

## Cost Layer


MeshNav already provides a set of layers that you can use out of the box.
If they are not sufficient for your specific task, you can create your own layer using MeshNav’s [`AbstractLayer`](https://github.com/naturerobots/mesh_navigation/blob/main/mesh_map/include/mesh_map/abstract_layer.h) plugin interface.

MeshNav itself implements all of its existing cost layers using this interface, bundled in the [`mesh_layers`](https://github.com/naturerobots/mesh_navigation/tree/main/mesh_layers) package.
Since it is a separate package, [`mesh_layers`](https://github.com/naturerobots/mesh_navigation/tree/main/mesh_layers) can be placed anywhere inside your ROS workspace’s `src` directory. As a demonstration, you can optionally move this package one level up:

```bash
mv your_ros_ws/src/mesh_navigation/mesh_layers your_ros_ws/src/mesh_layers
```

After doing this, you can still compile the ROS workspace as before.
This shows that you can also write your **own** layer (or a set of layers) for MeshNav in your own package, following the example provided by the [`mesh_layers`](https://github.com/naturerobots/mesh_navigation/tree/main/mesh_layers) package.

This allows you to keep the code within your own codebase while still contributing to the MeshNav project.

**Example Code**: [https://github.com/naturerobots/mesh_navigation/tree/main/mesh_layers](https://github.com/naturerobots/mesh_navigation/tree/main/mesh_layers)


## Planner

MeshNav’s planners are implemented through the MeshPlanner plugin interface.
If the existing planners do not cover your use case, you can implement your own planner plugin.

The default planners are located in packages named `dijkstra_mesh_planner` and `cvp_mesh_planner`. Similar as for the `mesh_layers` package in the Cost Layer section, those packages are itself only plugins of the MeshNav's plugin interface and can be seen as example to write an **own** customized planner plugin.

**Example Code**: [https://github.com/naturerobots/mesh_navigation/tree/main/dijkstra_mesh_planner](https://github.com/naturerobots/mesh_navigation/tree/main/dijkstra_mesh_planner)

## Controller

MeshNav’s controllers use the `MeshController` plugin interface from `mbf_mesh_core`.
If you want to implement a different control strategy or support custom kinematic models, you can create your own controller plugin.

The most complete example available is the MeshMPPI controller.
It is provided in a **separate repository**, which clearly demonstrates how controller plugins can live outside the main `mesh_navigation` codebase while still integrating cleanly with MeshNav.

Studying MeshMPPI is the recommended starting point, since it shows how a controller plugin can be packaged independently from the core navigation stack.

**Example Code**:
  MeshMPPI - [https://github.com/uos/mesh_mppi](https://github.com/uos/mesh_mppi)


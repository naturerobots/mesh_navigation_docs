# Tutorials

!!! Info 
    
    This documentation is under construction. It will contain explanations for both applied users and developers. 

Throughout the tutorials, code snippets are occasionally provided to demonstrate the described concepts. Before continuing, make sure you have installed the [MeshNav tutorials](https://github.com/naturerobots/mesh_navigation_tutorials).
Follow the installation instructions provided in the repository’s top-level README file.

You can test your installation by running:

```bash
ros2 launch mesh_navigation_tutorials mesh_navigation_tutorials_launch.py world_name:=tray
```

This command launches a simple world in Gazebo and opens an RViz window showing the mesh map of the environment.

| Gazebo                               | RViz                             |
| ------------------------------------ | -------------------------------- |
| ![TrayGazebo](/media/simple_envs/tray_world.png) | ![TrayRViz](/media/simple_envs/tray_map.png) |

In RViz, click **“Mesh Goal”** and draw an arrow somewhere on the map. The robot should start driving to the selected point.
If this works, the installation was successful and you can proceed with the tutorials.

---

## Applied Users

This section explains how to get started with [MeshNav](https://github.com/naturerobots/mesh_navigation). It then introduces documentation on applying these concepts to real-world scenarios through map-based localization and deliberation. Finally, we present tools for handling mesh maps, primarily by cross-referencing existing approaches and workflows for mesh mapping and editing.

### Getting Started with MeshNav

1. [Mesh Navigation](./mesh_navigation.md)
2. [Mesh Cost Layers](./mesh_cost_layers.md)
3. [Planner & Controller](./planner_and_controller.md)
4. [Virtual Worlds](./tutorial_worlds.md)

### Sim-to-Real

* [Map-based Localization](./localization.md)
* [Deliberation](./deliberation.md)

### Mesh Generation & Editing

* [Mesh Mapping](./gen_edit/mesh_mapping.md)
* [Repair Meshes](./gen_edit/repair_mesh.md)
* [Flatten Floor](./gen_edit/flatten_surface.md)
* [Align Mesh to Ground](./gen_edit/align_mesh_to_ground.md)
* [Shrink Faces](./gen_edit/shrink_faces.md)

---

## Developers

This section provides instructions on how to modify components and develop new features. Core concepts are explained in more detail to support extending the system.

### Writing Plugins

* [Custom Cost Layer](/tutorials/plugins/own_cost_layer.md)
* *More coming soon…*


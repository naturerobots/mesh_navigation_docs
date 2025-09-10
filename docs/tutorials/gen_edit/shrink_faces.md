# Explanation: Vertex Density and MeshNav

![ShrinkFaces01](/media/mesh_edit/shrink_faces_01_orig.png)

This is the mesh of the `floor_is_lava` world used for simulation in the [MeshNav tutorials](https://github.com/naturerobots/mesh_navigation_tutorials), visualized with [MeshLab](https://www.meshlab.net/).

You may notice that the triangles are very large. As shown in MeshLab's statistics, the entire, rather complex environment is represented by **just** 86 faces (connecting 52 vertices).

For many operations, this is desirable, as it greatly reduces the amount of data needed to represent a large environment:

- **Rendering**: fewer triangles need to be projected onto the camera plane,

- **Collisions**: fewer potential contact faces need to be evaluated for physics simulation.

However, some algorithms require a certain triangle density to function properly. For example, the simulation of bending materials or (most importantly for you) MeshNav's current planner implementations. MeshNav's cost layer system computes costs per vertex. For example, it may compute the maximum height difference in the immediate vicinity of a vertex:

![ShrinkFaces02](/media/mesh_edit/shrink_faces_02_orig_rviz.png)

In this visualization, you can see that each edge is assigned a cost inferred from the two vertices it connects. In this case, the edge receives a "bad" value because both vertices are in a dangerous region. Other problems that arise when using such low-resolution meshes for planning include:

- Graph search algorithms may be forced to choose dangerous paths.

- Cost inflation effects cannot be represented with sufficient resolution.

One solution is to prepare the map with faces that are small enough. Fortunately, once we have a mesh, this operation is relatively straightforward:

!!! note

    This operation will increase the number of faces. It will likely also increase the computational requirements for some navigation operations performed on the mesh.

# Shrink Faces via MeshLab

Open the mesh in MeshLab. Then go to `Filters -> Remeshing, Simplification, and Reconstruction` and select `Remeshing: Isotropic Explicit Remeshing`.

![Edit1](/media/mesh_edit/shrink_faces_03_edit.png)

Set the "Target Length" and "Max. Surface Distance" absolute values.

| 0.5   | 0.3 |
|:--:|:--:|
| MeshLab ![Edit2](/media/mesh_edit/shrink_faces_04_05.png)  | MeshLab ![Edit3](/media/mesh_edit/shrink_faces_05_03.png)   |
| MeshNav ![Edit2b](/media/mesh_edit/shrink_faces_04_05_rviz.png) |  MeshNav ![Edit3b](/media/mesh_edit/shrink_faces_05_03_rviz.png) |

In the bottom row, you can see MeshNav’s inflation cost layer visualized in RViz. The higher the resolution of the triangles, the better we can represent the lethal area within the inscribed inflation radius of 0.4 m.

# Explanation: Mesh Quality and MeshNav



# Repair Mesh via MeshLab

Open MeshLab and loading your mesh
```bash
meshlab my_mesh.ply
```

## Standard Cleanups

Sometimes the mesh contains duplicated vertices. This can indicate an unconnected region inside the mesh that should be connected. Only when it is properly connected can the MeshNav planner do its job correctly. To clean this up, MeshLab provides filters to remove duplicated vertices and faces:

```txt
Filters -> Cleaning and Repairing -> Remove Duplicated Vertices
```

```txt
Filters -> Cleaning and Repairing -> Remove Duplicated Faces
```

After applying each filter, MeshLab gives a feedback how many elements were removed.

## (Rare) Repair Non-Manifold Meshes

In some occasions non-manifoldness of the mesh leads to unexpected behavior in any algorithm that needs to traverse over the surface. Meshlab has a function that removes faces that belong to a non manifold edge.

```txt
Filters -> Cleaning and Repairing -> Repair non Manifold Edges by Removing Faces
```

Do this until MeshLab's output prints that no face is removed anymore. After that select the filter

```txt
Filters -> Cleaning and Repairing -> Repair non Manifold Vertices by splitting
```

and enter `0.5` as vertex displacement ratio to avoid creating new duplicated vertices. Repeat that until MeshLab prints it has done no changes.


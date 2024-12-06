# Cost Layer Generation

- Input: (Optimized) Triangle Mesh
- Output: Cost Layers


Cost layers are used inside of mesh navigation to assign parts of the mesh different properties that can be considered for path planning and local planning. Such cost layers can be used:

- traversibility properties such as roughness, friction, etc.
- lethal zones: such as borders of cliffs
- prohibition areas where the robot should not drive because of certain safety regulations

Some useful cost layers are already implemented such as

- roughness
- height difference
- border

All these layers are described in [mesh navigation](https://github.com/naturerobots/mesh_navigation). In addition, [mesh navigation](https://github.com/naturerobots/mesh_navigation) provides simple interfaces to implement own kinds of cost layers. This [article](./OwnCostLayer) describes the usage of this interface in greater detail.



## Initial Cost Layer Generation

After generating a mesh map, you would normally have a standard mesh format such as PLY or OBJ. However, those formats turned out to be not descriptive enough to represent the layered format that we use for navigation. So we agreed on HDF5 format. Or, in more detail, a certain structure within the HDF5 file ([Mesh-Tools Paper](https://kbs.informatik.uos.de/files/pdfs/ras2020_spuetz.pdf), Figure 7). 

Initially such a HDF5 has to be pre-generated from a standard mesh file. We use the tool `lvr2_hdf5_mesh_tool` from [lvr2](https://github.com/uos/lvr2) for that. Go the build folder of lvr2. Where this folder is located depends on the build of lvr2. If it is build inside a ROS workspace you might search for it in the respective ROS build folder. Otherwise, it is probably directly in the source folder of the lvr2 project. Once you found it, you can execute it like

```console
./lvr2_hdf5_mesh_tool -i my_mesh_map.ply -o my_layered_mesh_map.h5
```

This file converts a standard mesh format into a HDF5 file and additionally pre-computes some implemented cost layers of the mesh navigation package. The contents of the generated `my_layered_mesh_map.h5` can also be inspected by using e.g. `HDFCompass`:

```console
HDFCompass my_layered_mesh_map.h5
```

Opens the following window:

![HDFCompass](/media/hdfcompass1.png)

Browsing to `mesh -> channels` gives the following list of channels that are available at the mesh:

![HDFCompass](/media/hdfcompass2.png)


Some of these channels are related to the mesh geometry, such as `vertices` or `face_indices`.
Others are related to certain cost layers, such as `roughness` or `border`.

## Use HDF5

The next step is to integrate this pre-computed layered mesh map into mesh navigation: [Link](/tutorials/mesh_navigation.md).



## Note on Potential Changes

The procedure explained in this document will be changed in the near future to make it much simpler and more intuitive to generate and use meshes and cost layers.



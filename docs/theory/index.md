# Overview

Meshes provide a powerful representation for path planning on uneven terrain.
Unlike grid-based maps, which discretize the environment into fixed cells, meshes describe the terrain as a continuous surface composed of connected planar elements.
This allows them to capture fine-grained geometric details such as slopes, curvatures, and discontinuities that are critical for determining vehicle traversibility.
By explicitly encoding surface normals and connectivity, meshes make it possible to evaluate vehicle stability, slope constraints, and ground clearance in a natural and efficient way.

Another advantage of mesh-based representations is their adaptability: the resolution can be refined in areas of interest while remaining coarser elsewhere, balancing accuracy and efficiency.
Meshes also allow semantic or traversibility information to be directly attached to vertices, edges, or faces, enabling flexible cost modeling during planning.
Since a mesh inherently induces a graph structure, it integrates seamlessly with common planning algorithms such as Dijkstra, A*, D*, or sampling-based methods.

In short, meshes combine geometric fidelity with computational efficiency, avoiding the discretization artifacts of raster-based maps while providing a natural substrate for terrain-aware navigation.
These properties make them especially well-suited for ground vehicle path planning in complex, uneven environments, and form the theoretical foundation of the MeshNav library.

|   Pluto Robot  |  Botanical Garden Osnabrück |
|:-------------------------:|:-------------------------:|
| ![Pluto 1](/media/pluto_botanical_garden1.jpg)  |  ![Pluto 2](/media/pluto_botanical_garden2.jpg) |


<div style="position: relative; padding-bottom: 56.3%; height: 0; overflow: hidden;" >
    <iframe src="https://www.youtube.com/embed/qAUWTiqdBM4?si=Blrm6xjGsNOD6xwu?autoplay=1&mute=1" 
            title="YouTube Video" 
            frameborder="0" 
            allow="autoplay; encrypted-media" 
            allowfullscreen 
            style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
    </iframe>
</div>

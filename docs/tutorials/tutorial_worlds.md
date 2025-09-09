# MeshNav Tutorial Worlds

For the tutorials we created a selection of virtual worlds, which we categorized in realistic and simplistic environments.
Realistic worlds bring us closer to real-world conditions by modeling complex geometry, textures, and dynamics, which helps evaluate how algorithms perform in practice.
Simplistic worlds, on the other hand, keep environments minimal so that examples can run even on low-end hardware and interesting situations can be studied in isolation without unnecessary complexity.
This separation ensures both robustness testing under realistic conditions and efficient, focused experimentation when needed.

## Simple Worlds



All files are located in the following packages:

- Maps & MeshNav: [`mesh_navigation_tutorials`](https://github.com/naturerobots/mesh_navigation_tutorials/tree/main/mesh_navigation_tutorials)
- Worlds & Simulation: [`mesh_navigation_tutorials_sim`](https://github.com/naturerobots/mesh_navigation_tutorials/tree/main/mesh_navigation_tutorials_sim)


### Tray

The most simplistic environment for very simple test cases.

| RViz | Gazebo |
|:--:|:--:|
| ![tray_map](/media/simple_envs/tray_map.png) | ![tray_sim](/media/simple_envs/tray_world.png) |


### Floor Is Lava

A more sophisticated environments where the robot could fall down a bridge (into lava).

| RViz | Gazebo |
|:--:|:--:|
| ![floor_is_lava](/media/simple_envs/floor_is_lava_map.png) | ![floor_is_lava_sim](/media/simple_envs/floor_is_lava_world.png) |

### Parking Garage

A simple multi-story parking garage, demonstrating how mesh navigation enables efficient planning across different floors.

| RViz | Gazebo |
|:--:|:--:|
| ![parking_garage_map](/media/simple_envs/parking_garage_map.png) | ![parking_garage_sim](/media/simple_envs/parking_garage_world.png) |

### Download

All simple maps are automatically available after cloning the repository.


## Real World Worlds

Additionally, we provide larger maps that more closely resemble real-world scales. Because these maps also have a large file size, we use Git LFS to store them efficiently and to make downloading parts of the repository more manageable.

### Pluto Maps

Originally used to benchmark the findings in the very first publications they are now revived for ROS 2. You can find the very first version of the code here: https://github.com/uos/pluto_robot.

All files are located in the following packages:

- Maps & MeshNav: [`mesh_navigation_pluto`](https://github.com/naturerobots/mesh_navigation_tutorials/tree/main/mesh_navigation_pluto)
- Worlds & Simulation: [`mesh_navigation_pluto_sim`](https://github.com/naturerobots/mesh_navigation_tutorials/tree/main/mesh_navigation_pluto_sim)

#### Physics Campus UOS

Physics building at Campus Westerberg, Osnabrück University.

| ID | Vertices  | Triangle | Dimensions: x[m], y[m], z[m] |
|:-----|:-----:|:-----:|:-----:|
| `physics_campus_uos` | 719 080  | 1 617 772 | 166.02 * 83.61 * 26.33 |

![physics_campus_uos](/media/pluto_envs/physics_campus_uos_map.png)

#### Botanical Garden Osnabrück

| ID | Vertices  | Triangle | Dimensions: x[m], y[m], z[m] |
|:-----|:-----:|:-----:|:-----:|
| `botanical_garden_osnabrueck` | 714 760  | 1 430 188 | 39.05 * 49.25 * 6.67 |

![botanical_garden_osnabrueck](/media/pluto_envs/botanical_garden_osnabrueck_map.png) 


#### Stone Quarry Brockum

| ID | Vertices  | Triangle | Dimensions: x[m], y[m], z[m] |
|:-----|:-----:|:-----:|:-----:|
| `stone_quarry_brockum` | 992 879  | 1 904 178 | 100.58 * 100.58 * 23.94 |

![stone_quarry_brockum](/media/pluto_envs/stone_quarry_brockum_map.png) 

#### Download

While being in the source directory of this repository, download all Pluto maps by entering

```bash
git lfs pull --include="mesh_navigation_pluto*"
```

or specific ones by calling

```bash
git lfs pull --include="mesh_navigation_pluto*/**/physics_campus_uos*"
```






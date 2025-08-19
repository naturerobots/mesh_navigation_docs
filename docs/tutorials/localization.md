# Localization

While navigating a robot to a target on the map, the robot's location relative to that map has to be known constantly.
This is called localization (on a prior map).
There are again many approaches to that.
Monte Carlo Localization (MCL) is well known and has proven to be exceptionally robust for 2D navigation.
However, it is rather computationally demanding.
In 3D, it requires carefully designed software or special hardware to run online on the robot.
Therefore, implementations of 3D MCL are rarely seen right now.

However, other computationally more lightweight approaches exist.
For example, pose tracking in meshes using the MICP-L method of the [RMCL](https://github.com/uos/rmcl) project.
It assumes that the robot pose is roughly known in advance.

[![Teaser](/media/micp.gif)](http://www.youtube.com/watch?v=G-Z5K0bPFFU)


!!! hint

    The next steps in this section require RMCL to be compiled within your ROS workspace.

Some nodes of the [RMCL](https://github.com/uos/rmcl) project are directly integrated within the [tutorials](https://github.com/naturerobots/mesh_navigation_tutorials).
You can activate it's MICP-L pose tracking method  by setting the launch argument `localization` to `rmcl_micpl`.

```bash
ros2 launch mesh_navigation_tutorials mesh_navigation_tutorial_launch.py world_name:=tray localization:=rmcl_micpl
```

Whats happening internally: The localization that was previously given by the Gazebo simulation is now replaced by MICP-L, a method that continuously registers the LiDAR scans to the mesh map.
Enabling a "real" localization has several advantages:

- Easier integration into real-world systems: Gazebo-like ground truth is usually not available there unless you have a tracking system.
- Realistic problems that could effect navigation algorithms: Localization latency, localization inaccuracies, localization failures, additional computational loads.

!!! Info

    For more details about the MICP-L method we refer to the [paper](https://ieeexplore.ieee.org/document/10802360) or the [ROSCon Talk](https://vimeo.com/879000775).

## Localization in other maps

### Point Cloud Maps

You can use a point cloud (PCD) map that is accurately aligned to the mesh as alternative map representation to do localization. The PCD typically comes from:
- Sampling the mesh, using tools like [CloudCompare](https://www.danielgm.net/cc/)
- Using the PCD from a PCD SLAM approach before it was converted to a mesh

Here is a list of some software packages for localization on such prior PCD maps:

| Name | Code |
|:----:|:----:|
| mcl_3dl |  https://github.com/at-wat/mcl_3dl  |
| lidar_localization_ros2 | https://github.com/rsasaki0109/lidar_localization_ros2 |


In addition, many of the recently developed PCD SLAM packages have a localization-only mode that can be used to compute the required localization.

But beware: Using this approach for large-scale maps could cause your RAM to suffer.

### Voxel Maps

If a voxel-based representation is still available you can use one of the following map-based localization approaches:

| Name | Code |
|:----:|:----:|
| TSDF MCL | https://github.com/uos/tsdf_localization  |

## Localization using GPS

If your mesh map is geo-referenced, you can use a GPS localization.
However, GPS often lacks of accuracy at altitude / z-axis which has to be compensated.
Furthermore, if the map is slightly wrong, GPS would give wrong results *relative* to the used map; even though its signal is optimal (!).
Then, localization approaches based on matching sensor data to the map would produce more reliable results. 


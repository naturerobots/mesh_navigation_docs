# Building a Mesh Map With GLIM

This guide will teach you how to use the [GLIM](https://github.com/koide3/glim) SLAM software to build mesh maps for use with MeshNav (the mesh can of course also be used for other applications).

## Recording Data

The first step is to record data in your environment.
Since GLIM is a range-based mapping framework your robot needs to feature some sort of range sensor, for example a 3D LiDAR or an RGB-D camera.
You can reference GLIM's [Sensor-setup-guide](https://github.com/koide3/glim/wiki/Sensor-setup-guide) for more information on recommended sensors.
GLIM expects to receive your data either via ROS2 topics or to read directly from a [ROS2 bag file](https://docs.ros.org/en/rolling/Tutorials/Beginner-CLI-Tools/Recording-And-Playing-Back-Data/Recording-And-Playing-Back-Data.html).

I recommend to first record data to a bag file for better reproducibility and then run GLIM afterwards.
Use the following command to start the recording and make sure to replace the `<>` parameters with the paths to your topics.
```bash
ros2 bag record -s mcap --topics </pointcloud2/topic> </imu/topic>
```
When your are done recording your environment you can stop the recording by pressing `CTRL-C`.

Great! 🎉

Now copy the recorded bag file to the device you want to continue on.

## Environment Setup

The next step is to setup your environment with the tools necessary to continue.
First install GLIM by following their [installation instructions](https://koide3.github.io/glim/installation.html).
The easiest and fastest way is to install using the PPA method.
Then install GLIM for ROS (I recommend the CUDA version if you have a compatible system).

Next create a new colcon workspace to build the necessary [glim_scan_saver](https://github.com/JustusBraun/glim_scan_saver_ext) extension and the [slam_to_mesh](https://github.com/JustusBraun/slam_to_mesh) mesh generation tool:
```bash
mkdir -p mesh_ws/src
cd mesh_ws/src
```
and clone the source repositories:
```bash
# slam_to_mesh tool
git clone https://github.com/JustusBraun/slam_to_mesh.git

# Plugin to export undeskewed point clouds from GLIM
git clone https://github.com/JustusBraun/glim_scan_saver_ext.git
```
The `slam_to_mesh` tool uses the [LVR2](https://github.com/uos/lvr2) C++ library in the background.
If you are using Ubuntu 24 you can download a prebuild `.deb` archive from the releases page and install using apt:
```bash
apt install ./path/to/archive
```
If you are not using Ubuntu 24 (or want to build from source) you can always build from source by cloning the LVR2 repository into the workspace:
```bash
git clone https://github.com/uos/lvr2.git
```
Make sure to follow the instructions in the [LVR2 README](https://github.com/uos/lvr2/blob/main/README.md) to ensure the necessary dependencies are installed.

Now you can build your workspace:
```bash
cd ..
colcon build --cmake-args " -DCMAKE_BUILD_TYPE=Release"
```

## Running GLIM SLAM

### Configure GLIM

Before you can run GLIM with your dataset you need to adjust the configuration to reflect your setup.
First go to the root of your workspace and create a copy of GLIM's `config` directory:
```bash
cp -r /opt/ros/$ROS_DISTRO/share/glim/config/ .
```
If you build GLIM from source you can find the config folder in the root of GLIM's source tree.

Then follow the steps outlined in the [Getting Started](https://koide3.github.io/glim/quickstart.html) section of GLIM's documentation to adjust your configuration files.
The `imu_topic` and `points_topic` in the `config_ros.json` file tell GLIM which topics to read the IMU and LiDAR data from.
Set them to the topics you recorded in the first section.
You also need to add the `glim_scan_saver_ext.so` extension model to the `extension_modules` list to ensure that GLIM exports the individual pointclouds for us to use later.

<details>
<summary>Enabling the glim_scan_saver extension module</summary>
```json
"glim_ros": {
    ...
    "keep_raw_points": true // SET THIS TO TRUE IF YOU WANT FULL RESOLUTION LIDAR SCANS
    ...
    // Extension modules
    "extension_modules": [
      "libmemory_monitor.so",
      "libstandard_viewer.so",
      "librviz_viewer.so",
      "libglim_scan_saver_ext.so" // THIS IS THE IMPORTANT LINE
    ],
    ...
}
```
</details>

To fuse the IMU and pointcloud data GLIM needs to know the transformation between the IMU and range sensor coordinate systems.
The transform must be written to the `T_lidar_imu` parameter in the `config_sensors.json` file.
You should read GLIM's [documentation page on parameters](https://koide3.github.io/glim/parameters.html)
to tune the configuration to get the best mapping results with your sensor setup.

### Run the SLAM

Now that configuration is complete you can run GLIM on your dataset.
I will use the downsampled example bag file provided on GLIM's [Getting Started](https://koide3.github.io/glim/quickstart.html) page.
Run GLIM with your custom configuration, make sure to replace the `<>` parameter with the path to your bag file.
```bash
ros2 run glim_ros glim_rosbag <path/to/your/bagfile> --ros-args -p config_path:=$(realpath config/)
```
This will open a window where you can watch the mapping process:
![GLIM Window](/media/guides/mesh_mapping_glim/glim.jpg)

After GLIM is done processing your bag file you can close the window and the resulting map will be saved to `/tmp/dump`.
You can now optionally use the `offline_viewer` provided in the `glim_ros` package to manually add loop-closures and optimize the map.
When you are done editing your map, use the `File` menu to save your map to a folder of your choice.
If you do this you need to manually copy the `deskewed_scans/` directory from `/tmp/dump/` to the new map directory.

## Generating the Mesh Map

Now we can use our exported scans and the trajectory generated by GLIM to create a triangle mesh map.
For this now use the `slam_to_mesh` tool with the following command and replace the paths to the `traj_lidar.txt` file and `deskewed_scans` directory with the paths to your results:

```bash
slam_to_mesh --poses /tmp/dump/traj_lidar.txt --poses-filetype-hint tum --scans /tmp/dump/deskewed_scans/ --displacement 0.3 1.0
```

The `--displacement 0.3 1.0` flag reduces the number of scans processed by only adding a scan when the robot moved `0.3` meters or changed orientation by `1.0` radian.
This reduces the necessary computation workload and can lead to reduced artifacts in the mesh from moving objects in the point clouds.

The mesh generated from the example dataset looks as follows:
![GLIM Window](/media/guides/mesh_mapping_glim/raw_mesh.jpg)

The mesh can then be further processed with tools like MeshLab to remove artifacts and reduce the number of faces.
For more information on mesh post-processing take a look at the [Tutorials](/tutorials/index.md) page.

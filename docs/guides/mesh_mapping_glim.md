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

## Setup Environment

The next step is to setup your environment with the tools necessary to continue.
First install GLIM by following their [installation instructions](https://koide3.github.io/glim/installation.html).
The easiest and fastest way is to install using the PPA method.
Then install GLIM for ROS (I recommend the CUDA version if you have a compatible system).

Next create a new colcon worksapce to build the necessary [glim_scan_saver](https://github.com/JustusBraun/glim_scan_saver_ext) extension and the [slam_to_mesh](https://github.com/JustusBraun/slam_to_mesh) mesh generation tool.


## Running SLAM

## Generating the Mesh Map

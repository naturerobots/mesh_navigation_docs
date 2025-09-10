
# Installation

## From Source


!!! Important

    Building from source is currently the only available option. Feel free to reach out to us, if you want to support us in uploading pre-built binaries somewhere.


You need a working ROS 2 installation. We target humble at the moment. Go into a ROS 2 workspace's source directory cd $YOUR_ROS_WS/src and clone the tutorial code 

```bash
git clone git@github.com:naturerobots/mesh_navigation.git
```

### Get the mesh nav's ROS 2 dependencies:

Clone source dependencies using vcs-tool:

```bash
vcs import --input mesh_navigation/source_dependencies.yaml
```

Get packaged dependencies from within your ROS 2 workspace source directory:

```bash
rosdep install --from-paths . --ignore-src -r -y
```

### Build

Go to workspace root `cd $YOUR_ROS_WS` and run

```bash
colcon build --packages-up-to mesh_navigation
```

### Validate Build

You can validate you installation by executing the very first of the [mesh_navigation_tutorials](/tutorials/index.md).


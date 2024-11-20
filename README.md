# Zed-Zed2-Zedm-Camera-setup-with-ROS2
This is for zed camera setup and ros2 node launch.

# System Requirements
For this demo, we are using Ubuntu 22.04, ROS2 (ROS Humble), Nvidia Driver 535, Cuda 12.1 and ZED SDK 4.1.4 (pyzed-4.1) and python 3.10. 

# ZED Camera SDK Installation:
Download the Latest SDK version from the site: https://www.stereolabs.com/developers/release/.
The default download should be in your "Downloads" directory. To check, run in a terminal:
```
cd ~/Downloads
ls
```
You should see a file in the name of ZED_SDK_Ubuntu22_cuda12.1_v4.1.4.run or something similar. Change the permission of the file so that you can execute it from the terminal.
```
chmod +x ZED_SDK_Ubuntu22_cuda12.1_v4.1.4.run
```
Run the following command to install zed sdk:
```
./ZED_SDK_Ubuntu22_cuda12.1_v4.1.4.run
```
Follow the instruction & the necessary files and libraries will automatically be installed in to the system. To check installation, run the following command:
```
cd /usr/local/zed/tools
ls
```
You should see some executables. Try to run them one by one. After starting an executable, to stop it, hit ctrl+c. Now run the following command:
```

./ZED_Explorer      # To start playing, recording and loading sensor data (svo2)
ctrl+c              # To stop it
./ZED_Depth_Viewer  # To start playing and loading data (svo2) for depth and point cloud visualization
ctrl+c              # To stop it
./ZEDfu             # To start playing and loading data (svo2) for trajectory and map visualization
ctrl+c              # To stop it
```
# ZED Camera Setup
## Clone this repo:
```bash
git clone https://github.com/ArghyaChatterjee/zed-zed2-zedm-zed-x-camera-setup-in-ros2.git
cd zed-zed2-zedm-zed-x-camera-setup-in-ros2/
```

## Create a Virtualenv:
```bash
python3 -m venv zed_zed2_zedm_zed_x_camera_setup_in_ros2_venv
source venv zed_zed2_zedm_zed_x_camera_setup_in_ros2_venv/bin/activate
pip3 install --upgrade pip
pip3 install -r requirements.txt
```

## Install python api for ZED
```bash
cd scripts
python3 get_python_api.py
```

## Monitor System Performance
- To monitor system performance during the task allocated, you can open Activities Overview--->System Monitor. Alternatively, you can also install 'htop' to monitor system parameters in a unique way. In a new terminal, run:
```
sudo snap install nvtop
nvtop
```
## Monitor nvidia gpu memory usage
- To monitor your nvidia gpu memory usage during a certain gpu intensive task, install 'glances' & check by running the following command in a new terminal:
```
sudo python3 -m pip install glances[gpu]
sudo glances
```
# Setup ZED ROS2

### Prerequisites
  - [Foxy on Ubuntu 20.04](https://docs.ros.org/en/foxy/Installation/Linux-Install-Debians.html) -> Close to EOL
  - [Humble on Ubuntu 22.04](https://docs.ros.org/en/humble/Installation/Linux-Install-Debians.html)

### Build the package

The **zed_ros2_wrapper** is a [colcon](http://design.ros2.org/articles/build_tool.html) package. Set up your colcon workspace yet, please follow this short [tutorial](https://index.ros.org/doc/ros2/Tutorials/Colcon-Tutorial/).

To install the **zed_ros2_wrapper**, clone the package, and build it:

```bash
source /opt/ros/humble/setup.bash # for ubuntu 22.04 with ros humble installed
# source /opt/ros/foxy/setup.bash # for ubuntu 20.04 with ros foxy installed
mkdir -p ~/zed_ros2_ws/src/ 
cd ~/zed_ros2_ws/src/ 
git clone  --recursive https://github.com/stereolabs/zed-ros2-wrapper.git
cd zed-ros2-wrapper
git checkout tags/humble-v4.1.4
git submodule update --init --recursive
cd ../..
sudo apt update
rosdep update
rosdep install --from-paths src --ignore-src -r -y # install dependencies
colcon build --symlink-install --cmake-args=-DCMAKE_BUILD_TYPE=Release --parallel-workers $(nproc) 
echo source $(pwd)/install/local_setup.bash >> ~/.bashrc
source ~/.bashrc
```

If `rosdep` is missing you can install it with:
```
sudo apt-get install python3-rosdep python3-rosinstall-generator python3-vcstool python3-rosinstall build-essential`
```
### Start ZED ROS2 Node:

To start the ZED node, open a bash terminal and use the [CLI](https://index.ros.org/doc/ros2/Tutorials/Introspection-with-command-line-tools/) command `ros2 launch`:

```bash
ros2 launch zed_wrapper zed_camera.launch.py camera_model:=<camera_model>
```

Replace `<camera_model>` with the model of the camera that you are using: `'zed'`, `'zedm'`, `'zed2'`, `'zed2i'`, `'zedx'`, `'zedxm'`, `'virtual'`,`'zedxonegs'`,`'zedxone4k'`.

The `zed_camera.launch.py` is Python launch scripts that automatically start the ZED node using ["manual composition"](https://index.ros.org/doc/ros2/Tutorials/Composition/). The parameters for the indicated camera model are loaded from the relative "YAML files". A Robot State Publisher node is started to publish the camera static links and joints loaded from the URDF model associated with the camera model.

> :pushpin: **Note:** You can set your own configurations by modifying the parameters in the files **common_stereo.yaml**, **zed.yaml** **zedm.yaml**, **zed2.yaml**, **zed2i.yaml**, **zedx.yaml**, **zedxm.yaml**, **common_mono.yaml**, **zedxonegs.yaml**, and **zedxone4k.yaml**  available in the folder `zed_wrapper/config`.

# Record ROS2 topics in a rosbag
Now, record the ros2 topics in a rosbag.
```bash
# record all topic
ros2 bag record -a -o ros2_bag_name
# record specific topic
ros2 bag record /tf /tf_static /zed/depth/neural /zed/left/camera_info /zed/left/image_raw /zed/pointcloud /zed/pose /zed/right/camera_info /zed/right/image_raw  -o ros2_bag_name
# record mentioning compression mode
ros2 bag record -a -o ros2_bag_name --compression-mode file --compression-format zstd 
# record excluding a specific topic
ros2 bag record -a --exclude "/topic_to_exclude" -o ros2_bag_name
```

# ZED ROS2 Networking
we recommend reading this ROS 2 [tuning guide](https://www.stereolabs.com/docs/ros2/150_dds_and_network_tuning) to improve the ROS 2 experience with ZED.



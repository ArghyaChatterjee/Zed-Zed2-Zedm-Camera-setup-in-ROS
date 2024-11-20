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

#### Instruction for ZED SDK >=4.2
To install the **zed_ros2_wrapper**, clone the package, and build it:

```bash
source /opt/ros/humble/setup.bash # for ubuntu 22.04 with ros humble installed
# source /opt/ros/foxy/setup.bash # for ubuntu 20.04 with ros foxy installed
mkdir -p ~/zed_ros2_ws/src/ 
cd ~/zed_ros2_ws/src/ 
git clone  --recursive https://github.com/stereolabs/zed-ros2-wrapper.git
cd ..
sudo apt update
rosdep update
rosdep install --from-paths src --ignore-src -r -y # install dependencies
colcon build --symlink-install --cmake-args=-DCMAKE_BUILD_TYPE=Release --parallel-workers $(nproc) 
echo source $(pwd)/install/local_setup.bash >> ~/.bashrc
source ~/.bashrc
```

#### Instruction for ZED SDK <=4.1.4
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

### Set Configurations and Resolutions
You can set your own configurations by modifying the parameters in the files **common_stereo.yaml**, **zed.yaml** **zedm.yaml**, **zed2.yaml**, **zed2i.yaml**, **zedx.yaml**, **zedxm.yaml**, **common_mono.yaml**, **zedxonegs.yaml**, and **zedxone4k.yaml**  available in the folder `zed_wrapper/config`.

If you want to change the resolution of the images publishing, change 2 things.
- Change `grab_resolution: 'HD720'` to `'HD2K', 'HD1080', 'VGA', 'AUTO'` any of them inside `zed_wrapper/config/<camera_model>.yaml`
- Change `pub_resolution: "CUSTOM"` to `pub_resolution: "NATIVE"` inside `zed_wrapper/config/common.yaml`


# Record ZED ROS2 topics in a rosbag
Now, record the ros2 topics in a rosbag.
```bash
# record all topic
ros2 bag record -a -o ros2_bag_name
# record specific topic
ros2 bag record /tf /tf_static /zed/zed_node/depth/camera_info /zed/zed_node/depth_registered /zed/zed_node/left/camera_info /zed/zed_node/left/image_rect_color /zed/zed_node/point_cloud/cloud_registered /zed/zed_node/pose /zed/zed_node/pose_with_covariance /zed/zed_node/right/camera_info /zed/zed_node/right/image_rect_color /zed/zed_node/imu/data -o ros2_bag_name
# record mentioning compression mode
ros2 bag record -a -o ros2_bag_name --compression-mode file --compression-format zstd 
# record excluding a specific topic
ros2 bag record -a --exclude "/topic_to_exclude" -o ros2_bag_name
```

# Replay ZED ROS2 topics from a rosbag
If you want to replay the rosbag (at default rate, r=1):
```bash
ros2 bag play zed_ros2_topics.bag -r 1
```
# Echo ZED ROS2 topics
If you want to echo any topic:
```bash
ros2 topic echo /zed/zed_node/depth/depth_info
```
Here is the output:
```
header:
  stamp:
    sec: 0
    nanosec: 0
  frame_id: zed_left_camera_optical_frame
min_depth: 0.6266739368438721
max_depth: 7.9091668128967285

```


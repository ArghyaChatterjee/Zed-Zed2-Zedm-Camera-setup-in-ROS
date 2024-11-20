# Zed-Zed2-Zedm-Camera-setup-with-ROS2
This is for zed camera setup.
# ZED-Camera-SDK-ZED-Python-API-Installation-and-Object-Detection-Demo
It's a repository to use ZED camera with ZED SDK &amp; ZED Python API for Object detection with Tensorflow.
## ZED Camera SDK Installation:
Download the Latest SDK version from the site: https://www.stereolabs.com/developers/release/.
The default download should be in your "Downloads" directory. To check, run in a terminal:
```
cd ~/Downloads
ls
```
You should see a file in the name of ZED_SDK_Ubuntu18_cuda12.1_v4.1.4.run or something similar. Now change the permission of the file so that you can execute it from the terminal.
```
chmod +x ZED_SDK_Ubuntu18_cuda12.1_v4.1.4.run
```
Now, the file is executable. Run the following command:
```
./ZED_SDK_Ubuntu18_cuda12.1_v4.1.4.run
```
Follow the instruction & the necessary files and libraries will automatically be installed in to the system. To check installation, run the following command:
```
cd /usr/local/zed/tools
ls
```
You should see some executables. Try to run them one by one. After starting an executable, to stop it, hit ctrl+c. Now run the following command:
```

./ZED_Explorer      # To start an example
ctrl+c              # To stop it
./ZED_Depth_Viewer  # To start another example
ctrl+c              # To stop it
./ZEDfu             # To start another example
ctrl+c              # To stop it
```
## ZED Python API Installation:
- Run the following command before installing zed python api on your pc. Make sure that python3, pip & Open CV are already installed. 
```
cd ~
python3 -m pip install numpy pyopengl
```
- Now move to zed sdk directory to download zed python api. Run the following command:
```
cd /usr/local/zed
python3 get_python_api.py
```
- The system will automatically download a wheel file in the current directory. To install it, run:
```
python3 -m pip install pyzed-3.1-cp36-cp36m-linux_x86_64.whl
```
- To check successful installation, run:
```
cd ~
python3
import pyzed.sl as sl
```
The file should successfully import without showing any error. 
- To monitor system performance during the task allocated, you can open Activities Overview--->System Monitor. Alternatively, you can also install 'htop' to monitor system parameters in a unique way. In a new terminal, run:
```
sudo snap install nvtop
nvtop
```
- To monitor your nvidia gpu memory usage during a certain gpu intensive task, install 'glances' & check by running the following command in a new terminal:
```
sudo python3 -m pip install glances[gpu]
sudo glances
```
# Setup ZED ROS2:
## Installation

### Prerequisites

- [Ubuntu 20.04 (Focal Fossa)](https://releases.ubuntu.com/focal/) or [Ubuntu 22.04 (Jammy Jellyfish)](https://releases.ubuntu.com/jammy/)
- [ZED SDK](https://www.stereolabs.com/developers/release/latest/) v4.2 (for older versions support please check the [releases](https://github.com/stereolabs/zed-ros2-wrapper/releases))
- [CUDA](https://developer.nvidia.com/cuda-downloads) dependency
- ROS 2 Foxy Fitxroy or ROS 2 Humble Hawksbill: 
  - [Foxy on Ubuntu 20.04](https://docs.ros.org/en/foxy/Installation/Linux-Install-Debians.html) -> Close to EOL
  - [Humble on Ubuntu 22.04](https://docs.ros.org/en/humble/Installation/Linux-Install-Debians.html)

### Build the package

The **zed_ros2_wrapper** is a [colcon](http://design.ros2.org/articles/build_tool.html) package. Set up your colcon workspace yet, please follow this short [tutorial](https://index.ros.org/doc/ros2/Tutorials/Colcon-Tutorial/).

To install the **zed_ros2_wrapper**, clone the package, and build it:

```bash
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

If `rosdep` is missing you can install it with:
```
sudo apt-get install python3-rosdep python3-rosinstall-generator python3-vcstool python3-rosinstall build-essential`
```
# Start ZED ROS2 Node:

To start the ZED node, open a bash terminal and use the [CLI](https://index.ros.org/doc/ros2/Tutorials/Introspection-with-command-line-tools/) command `ros2 launch`:

```bash
ros2 launch zed_wrapper zed_camera.launch.py camera_model:=<camera_model>
```

Replace `<camera_model>` with the model of the camera that you are using: `'zed'`, `'zedm'`, `'zed2'`, `'zed2i'`, `'zedx'`, `'zedxm'`, `'virtual'`,`'zedxonegs'`,`'zedxone4k'`.

The `zed_camera.launch.py` is Python launch scripts that automatically start the ZED node using ["manual composition"](https://index.ros.org/doc/ros2/Tutorials/Composition/). The parameters for the indicated camera model are loaded from the relative "YAML files". A Robot State Publisher node is started to publish the camera static links and joints loaded from the URDF model associated with the camera model.

> :pushpin: **Note:** You can set your own configurations by modifying the parameters in the files **common_stereo.yaml**, **zed.yaml** **zedm.yaml**, **zed2.yaml**, **zed2i.yaml**, **zedx.yaml**, **zedxm.yaml**, **common_mono.yaml**, **zedxonegs.yaml**, and **zedxone4k.yaml**  available in the folder `zed_wrapper/config`.

# ZED ROS2 Networking
we recommend reading this ROS 2 [tuning guide](https://www.stereolabs.com/docs/ros2/150_dds_and_network_tuning) to improve the ROS 2 experience with ZED.



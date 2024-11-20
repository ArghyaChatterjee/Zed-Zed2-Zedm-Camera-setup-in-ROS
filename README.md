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
You should see a file in the name of ZED_SDK_Ubuntu18_cuda10.2_v3.1.2.run or something similar. Now change the permission of the file so that you can execute it from the terminal.
```
chmod +x ZED_SDK_Ubuntu18_cuda10.2_v3.1.2.run
```
Now, the file is executable. Run the following command:
```
./ZED_SDK_Ubuntu18_cuda10.2_v3.1.2.run
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

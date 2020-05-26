# RMP220_Barebones
Onboard PC ROS packages for the Segway RMP V3 provided by Stanley Innovation. This document roughly outlines the various steps required to setup an RMP V3 platform that has not been provided by Stanley Innovation.

**This document runs through the basic setup required to get a Segway RMP V3 system running**

**If you have purchased a Navigator or Navigator Elite package, this is already installed and setup with all sorts of extras, so no need to worry about going through this installation guide**

# We provide fully integrated systems with support
**We provide a Navigator package, a Navigator Elite package and fully integrated custom solutions with all robot setup, networking, timing, sensor/peripheral integration, calibration, tailored navigation tuning, extended functionality, and remote desktop VM. Our integrated packages come with fully setup onboard PC (for robot control) and a VM for remote monitoring and control. This tutorial is for seasoned ROS integrators that can complete that work themselves with our base RMP V3 platforms. Please contact Stanley Innovation for pricing and information on fully integrated packages and base platforms http://stanleyinnovation.com/contact-us/. Stanley Innovation is the _ONLY_ supplier of Segway RMP V3 compatible hardware! Please do not expect any of this to work if you did not purchase the system or an upgrade from Stanley Innovation, Inc.**

**If you have a Segway RMP from another source it can be upgraded. Do not try and load the V3 software without an upgrade performed by Stanley Innovation!!!!! It will render the machine inoperable, even if you try and go back to the standard RMP Release. If you do this you will likely have no option other than buying the upgrade to get the machine running again. You've been warned here and all over the place....don't do it.....**

**_Make_ _your_ _intern_ _do_ _it_, and then blame him when you tell your boss you have to upgrade your system ;)**

**If you want one-on-one engineering support for a system with onboard PC, remote desktop VM, sensors, nav, etc...please buy a Navigator package from us or contact us for an engineering support quote. Otherwise if you plan to buy your own sensors and your own computer for integration it is assumed you know what you're doing. Please use the community for support in integrating your own hardware, we will only address RMP specific questions for these customers if contacted directly. For example if you buy a barebones RMP V3 mobility platform and you need to know how to pull a faultlog, feel free to ask us; if you are trying to setup your PC and all your sensors, please rely on the community. We may help as part of the community for non-platform related questions, but there is no gaurantee**
  
## Mechanical and Electrical Integration of Sensors, Manipulators and other peripherals
* Before starting software setup make sure you have all your sensors mounted and electrically integrated
* Sensors need to be configured to work with the network and serial configurations we use by default or you need to modify them.
* For questions about electrical integration into the RMP power system see the manual and ask us if you have questions
* For mechanical 3D models please visit our website http://stanleyinnovation.com/resources/ at the bottom of the page

## Software Integration of Sensors, Manipulators and other peripherals
* We provide a subset of the available peripherals we support in the open software (see segway_v3_robot for examples)
* We can integrate pretty much anything in a custom integration system and support that
* Please use the community for software support unless you purchased a fully integrated system 
  * **If you did purchase a Navigator package or a custom robot; no need to read further unless your just interested. We did all this a quite a bit more setting up your system already.**

## Installation
The following instructions are valid for Ubuntu 18.04LTS and ROS Melodic. Before proceding please install Ubuntu 18.04LTS, ROS and any necessary ROS dependancies

### Required components
* **Segway RMP V3 platform provided by Stanley Innovation** 
  * For available platforms (http://stanleyinnovation.com/products-services/robotics/robotic-mobility-platforms/)
* **PC or VM Running Ubuntu 18.04 LTS**
  * If using onboard PC powered by RMP, make sure it runs on one of the voltages available on the Aux Power
    * Standard Aux Power includes 12VDC@150W
    * 48VDC, 24VDC, and 5VDC optional supplies available 
  * Minimum 8GB RAM (16 GB preferably)
  * Preferably a reasonably fast SSD
  * 1 Gigabit NIC 
  * Some sort of graphics for setup
  * Wireless router
* **Components for Setup**
  * Monitor
  * Keyboard (and mouse probably)
  * Internet Connection
  * Power supply for PC


### Install ROS Melodic
From a linux machine connected to the internet run the following commands

1. **Setup your ROS sources.list**
  * Setup your computer to accept software from packages.ros.org. ROS melodic ONLY supports Saucy (13.10) and Trusty (14.04) for debian packages.
  ```
  sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
  ```
2. **Set up your ROS keys**
  * Use the following command
  ```
  sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
  ```
3. **ROS Installation**
  * First, make sure your Debian package index is up-to-date:
  ```
  sudo apt-get update
  sudo apt-get install ros-melodic-desktop-full
  ``` 

4. **Initialize rosdep**
  * You must initialize rosdep
  ```
  sudo rosdep init
  rosdep update
  ``` 

5. **Environment setup**
  * Edit the local bash environment to add a few useful aliases
  ```
  gedit ~/.bashrc
  ``` 
  * Add the following lines to the end of the file each provides a few shortcuts:
  ```
  source ~/segway_ws/devel/setup.bash
  source /opt/ros/melodic/setup.bash
  alias sws='source ./devel/setup.bash'
  alias clean_backups='find ./ -name '*~' | xargs rm'
  alias clean_pyc='find ./ -name '*.pyc' | xargs rm'
  alias clean_rosbuild='rm -rf build devel install'
  ``` 

6. **Getting rosinstall**
  * rosinstall is a frequently used command-line tool in ROS that is distributed separately. It enables you to easily download many source trees for ROS packages with one command.
  ```
  sudo apt-get install python-rosinstall
  ```

### Install required packages
From a linux machine connected to the internet run the following commands

1. **Install required ROS third party packages for segway_v3_robot**
  * These are the packages that RMP V3 depends on or might be otherwise useful
  ```
  sudo apt-get -y install git xdotool wmctrl iperf chrony htop bridge-utils ecryptfs-utils ros-melodic-navigation ros-melodic-slam-karto ros-melodic-robot-localization ros-melodic-yocs-cmd-vel-mux ros-melodic-joy ros-melodic-urg-node ros-melodic-sick-tim ros-melodic-cmake-modules daemontools openssh-server libpcap0.8-dev ros-melodic-um7 ros-melodic-imu-tools ros-melodic-jsk-recognition ros-melodic-ros-controllers ros-melodic-gazebo-ros ros-melodic-gazebo-plugins ros-melodic-moveit ros-melodic-gazebo-ros-control ros-melodic-hector-gazebo-plugins ros-melodic-trac-ik ros-melodic-tf2-sensor-msgs ros-melodic-nmea-msgs ros-melodic-pointcloud-to-laserscan python-pip ros-melodic-cartographer-ros python-pymongo mongodb-server ros-melodic-move-base-msgs ros-melodic-pcl-ros ros-melodic-cv-bridge ros-melodic-map-server ros-melodic-rosbridge-suite ros-melodic-velodyne-gazebo-plugins festvox-us3 ros-melodic-cv-camera python-requests libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev ros-melodic-slam-gmapping ros-melodic-usb-cam ros-melodic-web-video-server ros-melodic-teb-local-planner ros-melodic-ar-track-alvar
  ```

2. **Create a workspace in your home directory**
  ```
  mkdir -p ~/segway_ws
  cd ~/segway_ws
  git clone https://github.com/StanleyInnovation/RMP220_Barebones.git src/
  cd src/
  catkin_init_workspace
  cd ~/segway_ws
  catkin_make
  
  ```
  
# Assuming you followed the instructions up to this point you should have successfully compiled, a little more setup and your on your way

### Setup Network
You need to set the network up for our platforms and the various ethernet enabled sensors.
This is an outline but **we also provide fully integrated packages**.

**NOTE: The ROBOT_NETWORK environment variable must match the port you use for #1 below. When you source the workspace this will be done automatically for you with the segway_network package**

1. **Setup Ubuntu to use the old style ethernet port enumeration/naming**
  * First open the grub configuration file
  ```
  sudo gedit /etc/default/grub
  ```
  * Next change the line
  ```
  GRUB_CMDLINE_LINUX=""
  ```
  * To this
  ```
  GRUB_CMDLINE_LINUX="net.ifnames=0 biosdevname=0"
  ```
  * Then run the command to update grub
  ```
  sudo update-grub
  ```
  * Finally restart your PC before moving onto the next section

2. **Set the IP of eth0 to the robot and sensor network**
  * The default IP of the network interface that talks to the platform is **10.66.171.4**
  * You can set this IP static by editing /etc/network/interfaces or using the network manager
  ```
  sudo gedit /etc/network/interfaces
  ```
  * Then add these lines assuming eth0 is connected to all the sensors using ethernet and the platform 
  ```
  auto eth0
  iface eth0 inet static
  address 10.66.171.4
  netmask 255.255.255.0
  ```
  * Then restart the interface (or restart the computer)
  ```
  sudo ifdown eth0
  sudo ifup eth0
  ```
  
### Additional steps
You should probably do the regardless, but these steps are really only required PGR Flea3 camera and onboard PC powered from RMP.

1. **Open the grub configuration**
  * Open a terminal
  ```
  sudo gedit /etc/default/grub
  ```
2. **Add this line**
  * Configures the system to not wait at startup if boot fails
  ```
  # disable getting stuck in menu after fail
  GRUB_RECORDFAIL_TIMEOUT=0
  ```
3. **Update the USB memory buffer to handle USB3**
  * Locate this line
  ```
  GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
  ```
  * Change it to this
  ```
  GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore usbfs_memory_mb=1000"
  ```
4. **Save and exit**
5. **Update GRUB**
  * From the terminal
  ```
  sudo update-grub
  sudo modprobe usbcore usbfs_memory_mb=1000
  ```
6. **Restart the computer**

### Almost Done!
**You are so close! Only a few extra steps...**

**NOTE: Power the PC from an external power supply until you have finished testing**

1. **If you have followed the instructions and set everything up correctly you should be able to launch the system manually**
  * Ensure the Disable button is not pressed
  * Power the RMP on with the silver button
    * LED Ring should pulse blue
    * The Power LED will blink green
    * The status LED should blink yellow indicating the state
  * In a new terminal
  ```
  cd ~/segway_ws
  sws
  roslaunch segway_ros segway_startup.launch
  ```
  * You should hear 2 beeps when the configuration server is initialized
  * If you are having issues communicating with the platform
    * you may have something wrong with your network configuration
    * you may have set the wrong platform in the configuration
    * you may have the wrong embedded firmware
    * the platform may not be on
4. **Save anything you might have open**
5. **Power off the RMP**
  * Press the silver power button
    * You should hear the platform play the shutdown song
    * The status LED will turn solid red
    * The blue LED ring on the power button will pulse quickly
  * The PC should enter shutdown if you have configured it to run on RMP power
  * Wait for the system to turn off.
    * All LED will turn off after 30 sec
7. **Connect the PC power input to RMP power output if you are powering the onboard PC from the RMP**

# Congratulation!!! If you got here the robot is all setup!!! Now you just need to configure a remote PC for visualization and control.....
# Don't you wish you sprung for that fully integrated system???
 
   


  
  

    
   
     
  
  
  
   


## 1. Overview
This tutorial is based on the official documentation available at [TurtleBot3](https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/#overview).
Please refer for updates and additional details.

The **TurtleBot3 Waffle Pi** is a modular, open-source mobile robot platform designed for advanced ROS research and development. As a larger-scale model in the TurtleBot3 family, it provides a stable and extensible base for complex applications such as SLAM, Navigation, and Manipulation.

---
## 2. Background

To effectively work with and understand the TurtleBot3 Waffle Pi, it's crucial to grasp some fundamental concepts and tools in robotics. These include the Robot Operating System (ROS), simulation environments like Gazebo, and core robotic capabilities such as SLAM and Navigation.

### ROS (Robot Operating System)
- **What it is:** ROS is a flexible framework for writing robot software. It's not an operating system in the traditional sense, but rather a collection of tools, libraries, and conventions that aim to simplify the task of creating complex and robust robot behaviors across a wide variety of robotic platforms.
- **Why we need it:** ROS provides a standardized way for different components of a robot (sensors, actuators, control algorithms, user interfaces) to communicate and work together. It promotes code reuse, offers a rich ecosystem of pre-built packages for common robotic tasks, and allows developers to focus on specific functionalities rather than reinventing basic communication and hardware interfaces. For the TurtleBot3, ROS is the backbone that allows its various hardware components and software modules to interact seamlessly.

### Gazebo
- **What it is:** Gazebo is a powerful 3D robotics simulator that allows you to accurately and efficiently simulate complex robot scenarios in a virtual environment. It provides robust physics engines, high-quality graphics, and convenient programmatic interfaces.
- **Why we need it:** Developing and testing robotics algorithms on physical hardware can be time-consuming, expensive, and potentially dangerous. Gazebo enables developers to test their code, design robots, and perform experiments in a safe, repeatable, and cost-effective virtual environment. This is invaluable for debugging, rapid prototyping, and exploring different scenarios for the TurtleBot3 before deploying solutions to the real robot.

### SLAM (Simultaneous Localization and Mapping)
- **What it is:** SLAM is a computational problem of constructing or updating a map of an unknown environment while simultaneously keeping track of an agent's location within it. Essentially, a robot builds a map and figures out where it is on that map at the same time.
- **Why we need it:** For a robot to operate autonomously in an unknown or dynamic environment, it needs to know both its position (localization) and the layout of its surroundings (mapping). Without SLAM, a robot would be "lost" and unable to navigate purposefully or complete tasks that require spatial awareness. The TurtleBot3 uses its Laser Distance Sensor (LDS) and Inertial measurement unit (IMU) data to perform SLAM.
### Navigation
- **What it is:** In robotics, navigation refers to the process by which a robot plans and executes paths to move from a starting point to a goal point while avoiding obstacles. It encompasses global path planning (finding a route across the entire map) and local path planning (making immediate adjustments to avoid unexpected obstacles).
- **Why we need it:** Once a robot has a map (from SLAM) and knows its location, it needs the ability to move purposefully to achieve its objectives. Navigation enables the robot to autonomously reach desired locations, perform tasks that require movement, and interact intelligently with its environment without human intervention.
### Other Important Terms

-   **IMU (Inertial Measurement Unit)**
    -   **Explanation:** A sensor (accelerometer, gyroscope, magnetometer) that measures a robot's orientation, angular velocity, and linear acceleration. It's crucial for accurate pose estimation and improving SLAM.

-   **LDS (Laser Distance Sensor)**
    -   **Explanation:** A 360° laser sensor (LDS-02 on TurtleBot3) that measures distances to surrounding objects. Essential for building maps (SLAM) and detecting obstacles for navigation.

-   **Rviz2 (ROS Visualization Tool)**
    -   **Explanation:** A 3D visualization tool for ROS 2 to view sensor data, robot models, maps, and path plans. It's vital for monitoring and debugging robot operations.

-   **Cartographer**
    -   **Explanation:** An open-source, real-time SLAM library by Google, using sensor data (like LiDAR and IMU) to build consistent maps. It provides robust mapping and localization for the TurtleBot3.

-   **Navigation2 (ROS 2 Navigation Stack)**
    -   **Explanation:** The ROS 2 framework for autonomous navigation, providing tools for global and local path planning, obstacle avoidance, and recovery behaviors. It enables the TurtleBot3 to move purposefully.

-   **OpenCR1.0** (The blue board) 
    -   **Explanation:** The TurtleBot3's main control board with a 32-bit ARM Cortex-M7 and 9-axis IMU. It handles low-level motor control and sensor communication, acting as the interface between the Raspberry Pi and robot hardware.

- **SBC (Single Board Computer)**
    - **Explanation:** A complete computer built on a single circuit board, with microprocessor(s), memory, input/output, and other features required of a functional computer. The Raspberry Pi (The green board) on the TurtleBot3 is an example of an SBC.

---
## 3. Quick Start

This section will guide you through the initial setup required to get started with your TurtleBot3 Waffle Pi.

### PC Setup
To ensure seamless communication and compatibility with the TurtleBot3 Waffle Pi, your development PC (laptop or desktop) must meet specific operating system and ROS 2 distribution requirements.

#### Operating System:
   Your PC must be running **Ubuntu 22.04 LTS Desktop**.
   - **Why it's needed:** Ubuntu 22.04 LTS is the officially supported operating system for **ROS 2 Humble Hawksbill**. This specific ROS 2 distribution is crucial because it will also be installed on the TurtleBot3's onboard computer (Raspberry Pi). For compatible communication they must have the same ROS 2 distribution.
   - **How to install:** If you don't already have Ubuntu 22.04 LTS install refer to [Ubuntu installation guide]([https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview](https://documentation.ubuntu.com/desktop/en/latest/tutorial/install-ubuntu-desktop/)) you may erase the disk or use [dual-boot option](https://documentation.ubuntu.com/desktop/en/latest/tutorial/install-ubuntu-desktop/#installing-ubuntu-alongside-another-operating-system). For the [64-bit PC (AMD64) desktop image](https://releases.ubuntu.com/22.04/ubuntu-22.04.5-desktop-amd64.iso)
    
#### ROS 2 Distribution:
You will need to install **ROS 2 Humble Hawksbill** on your Ubuntu 22.04 PC.
- **How to install:** For additional context you're encouraged to visit [The Official ROS2 Humble Documentation](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html) , we will focus on essentials.

```bash
# Set locale to ensure proper character encoding for ROS 2
sudo apt update && sudo apt install -y locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8

# Add the ROS 2 repository to your system's sources list
sudo apt install -y software-properties-common
sudo add-apt-repository universe

sudo apt update && sudo apt install curl -y
export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F\" '{print $4}')
curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo ${UBUNTU_CODENAME:-${VERSION_CODENAME}})_all.deb"
sudo dpkg -i /tmp/ros2-apt-source.deb

# Update your package list to include the new ROS 2 repository and update packages
sudo apt update && sudo apt upgrade -y

# Install the full ROS 2 Humble Desktop environment (includes ROS, Rviz2, demos, etc.)
sudo apt install -y ros-humble-desktop

# Add the ROS 2 Humble environment setup to the bash configuration file. This ensures ROS 2 commands are available in every new terminal session
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
# Refresh terminal to apply changes in .bashrc
source ~/.bashrc
```
**optional:** In the future you may also find ros2 development tools needed.``
```bash
 sudo apt install ros-dev-tools -y
```
#### Important ROS 2 Packages
```bash
#Install Gazebo
sudo apt install -y ros-humble-gazebo-*

#Install Cartographer
sudo apt install ros-humble-cartographer
sudo apt install ros-humble-cartographer-ros

#Install Navigation2
sudo apt install ros-humble-navigation2
sudo apt install ros-humble-nav2-bringup

# Ensure Gazebo simulation environment variables are loaded on startup
echo 'source /usr/share/gazebo/setup.sh' >> ~/.bashrc
# Refresh terminal to apply changes in .bashrc
source ~/.bashrc
```
####  TurtleBot3 Packages 

```bash
# Create a new workspace directory with a source folder (src) and navigate to it
mkdir -p ~/turtlebot3_ws/src && cd ~/turtlebot3_ws/src/
# Download the Dynamixel SDK (hardware drivers) specifically for the Humble distribution
git clone -b humble https://github.com/ROBOTIS-GIT/DynamixelSDK.git
# Download the custom message definitions required for TurtleBot3 communication
git clone -b humble https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git
# Download the core TurtleBot3 ROS 2 packages (Slam, Navigation, Teleop, etc.)
git clone -b humble https://github.com/ROBOTIS-GIT/turtlebot3.git

# Install colcon, the standard build tool used in ROS 2
sudo apt install python3-colcon-common-extensions -y
# Build the workspace; --symlink-install allows changes to non-compiled files to take effect without rebuilding
cd ~/turtlebot3_ws
colcon build --symlink-install

# Automatically source this workspace's setup file in every new terminal window
echo 'source ~/turtlebot3_ws/install/setup.bash' >> ~/.bashrc
# Set a unique Domain ID to prevent interference with other ROS 2 users on the same network
echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc
# Refresh terminal to apply changes in .bashrc
source ~/.bashrc
```

# ROS 2 Controller

Install the below on development machine

{% code overflow="wrap" %}

```bash
sudo apt install ros-foxy-ros2-control ros-foxy-ros2-controllers ros-foxy-gazebo-ros2-control
```

{% endcode %}

{% code overflow="wrap" %}

```
ros2 launch wolf-robot launch_sim.launch.py world:=./src/wolf-robot/worlds/construction.world

ros2 control list_hardware_interfaces

ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=diff_cont/cmd_vel_unstamped

rviz2 # To visualize robot in rviz
```

{% endcode %}

Installing ROS2 Control and ROS2 Controllers on Pi

```
sudo apt install ros-foxy-ros2-control ros-foxy-ros2-controllers
git clone https://github.com/VEEROBOT/diffdrive_arduino.git
git clone https://github.com/VEEROBOT/serial.git
```

Serial is used to connect to serial port, diff drive is used to control the hardware

```
sudo apt install ros-foxy-xacro ros-foxy-joint-state-publisher ros-foxy-joint-state-publisher-gui
```

To change between gazebo and real robot, change between ros2 control and sim\_mode in <mark style="color:blue;">`robot.urdf.xacro`</mark> file

To Launch Simulation:

{% code overflow="wrap" %}

```bash
ros2 launch wolf-robot launch_sim.launch.py world:=./src/wolf-robot/worlds/construction.world
```

{% endcode %}

To Launch Real Robot

```bash
ros2 launch wolf-robot launch_robot.launch.py
```

To Start Teleop Twist Keyboard

{% code overflow="wrap" %}

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/diff_cont/cmd_vel_unstamped
```

{% endcode %}

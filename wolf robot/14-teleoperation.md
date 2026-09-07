# Teleoperation

Install joystick package on dev machine

<pre><code>sudo apt update
<strong>sudo apt install joystick jstest-gtk evtest
</strong>sudo apt install libcanberra-gtk-module libcanberra-gtk3-module -y
</code></pre>

Once the libraries are installed, use evtest and jstest-gtk to check if all the buttons in the joystick are working. If yes, linux is detecting the joystick

To check if ROS2 is detecting the joystick, type:

{% code overflow="wrap" %}

```
ros2 run joy joy_enumerate_devices #Shows device ID. if this is not 0(zero), change it accordingly in joystick.yaml file
```

{% endcode %}

Now to connect joystick to ROS2, type

```
ros2 run joy joy_node
```

The above command creates topics for joystick. Check the topics using <mark style="color:blue;">`ros2 topic list`</mark>. To see the values of joystick, type:

```
ros2 topic echo /joy  # to see axis and button values
ros2 param list # to see details about the joystick (parameters)
```

Now that everything seems working, we will launch our joystick launch file:

{% code overflow="wrap" %}

```
ros2 launch wolf-robot joystick.launch.py
ros2 param list # to see details about the joystick (parameters)
ros2 topic echo /diff_cont/cmd_vel_unstamped
# Pressing enable buttons (6 and 7) will activate joystick. Added as a safety feature, but can be disabled in joystick.yaml
```

{% endcode %}

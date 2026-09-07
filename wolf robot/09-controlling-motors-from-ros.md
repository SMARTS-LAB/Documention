# Controlling motors from ROS

Wolf has four motors connected to quadrature encoders. Users can control motors directly from raspberry pi serial command.

### Installing necessary packages

To send serial commands from raspberry pi to micro controller board, we need to install `python3-serial package.` <mark style="color:orange;">Make sure to install the below package on the</mark> <mark style="color:blue;"><mark style="color:orange;">`@robot`<mark style="color:orange;"></mark> <mark style="color:blue;"><mark style="color:orange;"> </mark><mark style="color:blue;"><mark style="color:orange;">machine and not on the<mark style="color:orange;"></mark> <mark style="color:blue;"><mark style="color:orange;"> </mark><mark style="color:blue;"><mark style="color:orange;">`@dev`<mark style="color:orange;"></mark> <mark style="color:blue;"><mark style="color:orange;"> </mark><mark style="color:blue;"><mark style="color:orange;">machine.<mark style="color:orange;"></mark>

```
sudo apt install python3-serial
```

Run `miniterm` with baud rate of `57600` (set on the micro controller board) on `USB0`. If unsure on which usb it is connected, check by typing <mark style="color:blue;">`lsusb`</mark>. If Lidar is also connected, board can be connected to USB0 or USB1. In later part of tutorial, we will see how to hard code a particular port for different peripherals. To exit miniterm, press CTRL+]

```markup
    miniterm -e /dev/ttyUSB0 57600 #e for echo, USB0 is the port and 57600 is baud rate
```

The micro controller is configured as per the below commands. However, we are interested in only motor speed and encoder counts:

```sh
ANALOG_READ    'a'    # a <arg1> : Return analog value of arg1
GET_BAUDRATE   'b'    # b : Will return baud rate of motor controller
PIN_MODE       'c'    # Not used
DIGITAL_READ   'd'    # d <arg1> : Return digital value of arg1
READ_ENCODERS  'e'    # e : Return Encoder Values of all four encoders
READ_GYRO      'g'    # g : Return Gyro Values in x,y,z order
READ_ACCEL     'h'    # h : Return Accelerometer Values in x,y,z order
READ_IR_VALUE  'i'    # i : Return Sharp Sensor Values in fl, fr, bl, br order
MOTOR_SPEEDS   'm'    # m <arg1> <arg2> <arg3> <arg4> : Run motors (closed loop) as per arg values
MOTOR_RAW_PWM  'o'    # o <arg1> <arg2> <arg3> <arg4> : Run motors (open loop) as per arg values
PING           'p'    # Not used
RESET_ENCODERS 'r'    # r : Reset all encoder values to zero
SERVO_WRITE    's'    # Not used
SERVO_READ     't'    # Not used
UPDATE_PID     'u'    # Not used
DIGITAL_WRITE  'w'    # w <arg1> : Write a digital value to arg1 pin
ANALOG_WRITE   'x'    # x <arg1> : Write an analog value to arg1 pin
ZOMBIE_DRIVE   'z'    # z : Drive in autonomous mode <TBD>
FRONTLEFT       0
FRONTRIGHT      1
BACKLEFT        2
BACKRIGHT       3
```

Type <mark style="color:blue;">`e`</mark> and it should output encoder count. If robot has not moved, it will echo <mark style="color:blue;">`0 0 0 0`</mark>

<mark style="color:red;">To run the robot, type the</mark> <mark style="color:blue;">m</mark> <mark style="color:red;">and</mark> <mark style="color:blue;">`o`</mark> <mark style="color:red;">commands. However, make sure robot wheels are not touching the ground, or it is safe to drive as it moves the robot for 2 seconds in the said speed.</mark>

### PWM based motor control

<mark style="color:blue;">`o 127 127 127 127`</mark> # This will run the motors at 50% speed straight

<mark style="color:blue;">`o -255 -255 -255 -255`</mark> # This will run the motors at 1000% speed reverse

### Encoder based motor control

Counts per rotation of wolf motor (CPR) is 560 and PID is running at 28hz. So, to get one rotation per second, we need to send 20 ticks. The Motor driver is designed to make sure it receives signal every 2 seconds. If any signal in 2 seconds, robot stops.

<mark style="color:blue;">`m 20 20 20 20`</mark> # run the motor for 2 seconds at 1 revolutions per second

When trying encoder based motor control, the motor does not run complete two runs for 2 seconds. This is a latency issue due to Wi-Fi setup. If you set m to 25, then the wheels turn at exactly 1 revolutions per second. However, for continuous run, or navigation, it takes 20 ticks per revolution.

Reset Encoder Count

To reset the encoder count, type <mark style="color:blue;">`r`</mark> without any arguments. This resets all encoders to 0 (zero).

### GUI based motor Control

Copy `serial_motor_demo` package from downloaded folder to <mark style="color:blue;">`dev_ws/src`</mark>. Push the package to your GitHub repository and clone the same package on the <mark style="color:blue;">`@robot`</mark> machine. If you need a direct link to download on your raspberry pi, clone the below repository on your pi.

```sh
git clone https://github.com/VEEROBOT/serial_motor_demo-main.git
```

<mark style="color:blue;">`@robot`</mark>, type:

{% code overflow="wrap" %}

```sh
ros2 run serial_motor_demo driver --ros-args -p serial_port:=/dev/ttyUSB0 -p baud:=57600 -p loop_rate:=28 -p encoder_cpr:=560 # on the robot
```

{% endcode %}

<mark style="color:blue;">`@dev`</mark>, type:

```sh
ros2 run serial_motor_demo gui # on the development machine
```

Use the sliders on GUI to move motors either on raw speed mode (<mark style="color:blue;">o</mark>) or feedback mode (<mark style="color:blue;">`m`</mark>).

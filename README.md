
## Environment Setup

Follow the instructions in [development](./docs/DEVELOPMENT.md) on how to create the VM using Vagrant and setup the
development environment. Remember to install Extension Pack for USB passthrough for VirtualBox.

After the VM is created, remember to add USB device filters for the USB devices you are using with the robot in the VM
settings (e.g., webcam, servo controller).<br>
Oracle VM VirtualBox Manager -> Select the created VM -> Settings -> USB -> Check the 'Enable USB Controller' box and
choose USB 3.0 Controller -> Add filters for devices using the '+' button

## Running The Robot

We suggest downloading Dynamixel Wizard 2.0 to test the connection and functionality of the servos before trying to run
the robot. This is optional but can save some time debugging if the robot doesn't work.

Open the created VM and open a terminal. Use `rosdep install --from-paths src --ignore-src --rosdistro foxy -r -y`
to install the dependencies. In addition to this, use `sudo apt-get install python3-keras` to install the keras package
(the first command is not able to download it for some reason).

After installing the required dependencies, use `colcon build` to build the packages and `source install/setup.bash`.
The `source install/setup.bash` command has to be run every time you open a new terminal window.<br>
After building, launch the robot
```console
ros2 launch robot robot.launch.py
```
You should see some prints running in the terminal now. Open a new terminal window and start the required controllers.
```console
ros2 control load_start_controller joint_state_controller
ros2 control load_start_controller eyes_controller
```
You can check that the controllers are active with:
```console
ros2 control list_controllers
```
After the required controllers are active, you can start the face tracker and eye movement nodes (face tracker can also
be started before starting the controllers, eye movement node requires the controllers).<br>
Start the face tracker
```console
ros2 launch face_tracker face_tracker.test.launch.py
```
When the face tracker is running, you can view the camera feed with face recognition by running
```console
ros2 run rqt_image_view rqt_image_view image_face_topic
```
Finally, start the eye movement node in a new terminal window
```console
ros2 run eye_movement eye_movement_node
```
## Project structure

* src - all packages
  * src/inmoov_description - robot files (URDF, SRDF & rviz configuration)
  * src/robot - robot launch files & servo controller configurations
  * src/robot_hardware - hardware interface for ros2_controller, communicates with U2D2 via dynamixel workbench
* config - servo configuration

## Servo Table

Baud rate: 57600

**Note: Servos can be configured using dynamixel wizard**

| Servo ID | Model | Joint name                  | Description           |
| -------- | ----- | --------------------------- | --------------------- |
| 1        | XL430 | head_tilt_right_joint       | Head tilt right-side  |
| 2        | XL430 | head_tilt_vertical_joint    | Head tilt up/down     |
| 3        | XL430 | head_tilt_left_joint        | Head tilt left-side   |
| 4        | XL430 | head_pan_joint              | Head turn left/right  |
| 10       | XL320 | eyes_shift_horizontal_joint | Eyes shift left/right |
| 11       | XL320 | eyes_shift_vertical_joint   | Eyes shift up/down    |
| 12       | XL320 | head_jaw_joint              | Open/close jaw        |

**Note: head tilt range of motion is poor**

**Note: servo angle limits are not configured**

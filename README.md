# html_rosbridge_ros2

This repository contains simple HTML examples that use rosbridge + roslibjs
to call ROS 2 services and actions from the browser.

**Prerequisites**
- ROS 2 (tested on Humble)
- The `robot_interfaces` package (The custom service/action types in this project, replace them with your own types. See ROS2 Documentation to create your own package.)

1) Install rosbridge and roslib

```bash
sudo apt install ros-humble-rosbridge-suite
npm install roslib   # run this in the project directory
```

`importmap.js` will be located in the installed `roslib` folder (`node_modules/roslib/importmap.js`).

2) Launch rosbridge and (optionally) an action server

Before launching rosbridge, if you rely on custom message / service / action types
from your workspace, source your workspace install so rosbridge can find them:

```bash
source install/local_setup.bash
```

Then launch rosbridge (examples use rosbridge v2.0+ API):

```ros
ros2 launch rosbridge_server rosbridge_websocket_launch.xml \
    call_services_in_new_thread:=true \
    send_action_goals_in_new_thread:=true \
    default_call_service_timeout:=5.0

ros2 run action_tutorials_py fibonacci_action_server
```

3) Serve the HTML files and open them in a browser

From the directory that holds the `.html` files run a simple HTTP server:

```bash
python3 -m http.server 8000

# then open in browser:
http://localhost:8000/ros2_action_client.html
```

Notes and Troubleshooting
- Make sure rosbridge is version 2.0 or newer. Older versions use ROS1-style action/topic implementations and will cause errors such as:
  `No module named 'action_tutorials_interfaces.msg'` or incompatibilities when calling actions from roslibjs.
- roslibjs implements the ROS protocol differently between ROS 1 and ROS 2. The examples in this repo use the ROS 2 (rosbridge v2.0) API.

Examples of JS API usage (ROS 2 style)

Action client example:

```js
var fibonacciClient = new ROSLIB.Action({
  ros: ros,
  name: "fibonacci",
  actionType: "action_tutorials_interfaces/action/Fibonacci",
});
```
Service client example:

```js
var startEndPoseClient = new ROSLIB.Service({
  ros: ros,
  // custom service type from robot_interfaces package
  name: "/set_start_end_pose",
  serviceType: "robot_interfaces/srv/StartEndPose",
});
```

# Affordance_Learning
Affordance_Learning of robotarm（Dual arm cooperative control)
With the development of robotics technology, robotic arms have been widely applied in various fields such as industrial automation, logistics warehousing, and medical assistance. However, traditional robotic arm control relies on precise preset paths and external sensors, which are insufficient in dynamic environments or when facing complex tasks. Therefore, to enhance the autonomy and intelligence of robotic arms, control methods combining visual perception and reinforcement learning have become a research hotspot.
About affordence_learing please follow the [introduction](https://blog.csdn.net/qq_52529995?type=blog).
Visual perception gives the robotic arm the ability to understand and perceive its surrounding environment, while reinforcement learning, through trial and error and feedback mechanisms, allows the robotic arm to autonomously learn how to perform tasks such as grasping, moving, and manipulating objects. Particularly in scenarios involving tasks like wall pulling or moving, the robotic arm can use visual perception to identify the positions of walls and obstacles in real-time, and through reinforcement learning, autonomously explore suitable action strategies to optimize task completion efficiency.

This research aims to develop an intelligent robotic arm control system based on visual perception and reinforcement learning, which will output affordance features of interactions between the machine and the real world through affordance learning. In the future, it can be combined with reinforcement learning to enable the robotic arm to autonomously learn how to manipulate walls and complete precise grasping and moving tasks in complex dynamic environments.
<p align="center">
  <img src="https://github.com/user-attachments/assets/dd2aa675-f083-46be-9fcf-85f7fe11cbf9" alt="df7510d1df254d83e6180a43bde1c80_副本" style="width: 43%;">
</p>

# Setting
We are using the Airbot series for teleoperated robotic arms, and a lot of work needs to be done before completing autonomous annotation. The environment is set to PyTorch 1.11 with CUDA 11.3.Please first install PyTorch, CUDA 11.3, and Ubuntu 20.04, and complete the related operations.To install ROS, you can refer to the following [tutorial](https://www.ros.org/).
```bash
mkdir angrobotarm
cd angrobotarm
conda create --name angrobot python=3.8
conda activate angrobot
sudo apt install ./packages/airbot_play_<version>_amd64.deb
```
Run the following command to validate the installation:
```bash
airbot_read_params -v
# Example output:
# 2.8.3-4f625187
```
airbot_tools is an example tools package that depends on the basic control library package airbot_play. It contains several tools for AIRBOT Play, for quick testing and debugging. The tools include:
airbot_kbd_ctrl: An example toolkit to control AIRBOT Play using the keyboard
airbot_sync: Launch two AIRBOT Play arms in synchronization mode, making one following another.
To install airbot_tools, run the following command (replace <version> with the actual version number):
```bash
sudo apt install ./packages/airbot_tools_<version>_amd64.deb
```
Run the following command to validate the installation:
```bashairbot_kbd_ctrl -v
# Example output:
# 2.8.3-4f625187
```
# State of robot_arm
By using some parameters provided by the manufacturer, we can determine the status of the robotic arm as follows:
| State                                    | Color                                                       | Description                                                                 |
|------------------------------------------|-------------------------------------------------------------|-----------------------------------------------------------------------------|
| Self-check                               | <span style="color: #ffcc00;">Breathing</span>               | The robot is powered on and self-checking.                                   |
| Power On                                 | <span style="color: #ffffff; background-color: #000000;">Constant White</span> | The robot is powered on and prepared to be controlled.                       |
| Online (Idle)                            | <span style="color: #00ff00;">Constant Green</span>          | The robot is in online mode and can be controlled by external commands.     |
| Online (Idle)                            | <span style="color: #00ff00; font-weight: bold;">Flashing Green</span> | The robot is in online mode and is moving right now.                         |
| Manual                                   | <span style="color: #00ffff;">Constant Cyan</span>           | The robot is in manual mode and can be dragged freely with gravity compensation. |
| Manual (Recording)                       | <span style="color: #0000ff;">Constant Blue</span>           | The robot is in manual mode and can be dragged freely with gravity compensation. The trajectory is being recorded. |
| Offline (Idle)                           | <span style="color: #800080;">Constant Purple</span>        | The robot is in offline mode prepared to replay recorded trajectory. External commands will be ignored. |
| Offline (Moving to starting point)       | <span style="color: #800080; font-weight: bold;">Flowing Purple</span> | The robot is in offline mode, moving to the starting point of the recorded trajectory. External commands will be ignored. |
| Offline (Replaying)                      | <span style="color: #800080; font-weight: bold;">Breathing Purple</span> | The robot is in offline mode replaying the recorded trajectory.             |
| Error                                    | <span style="color: #ff0000;">Constant Red</span>           | The robot is in error state.                                                 |

The states of AIRBOT Play can be switched by pressing buttons on the base panels and on the end:
| Current State            | Action                             | Next State                       | Side Effect                                  | Description                                                                 |
|--------------------------|------------------------------------|----------------------------------|----------------------------------------------|-----------------------------------------------------------------------------|
| Online (Idle)             | LONG PRESS on the END BUTTON       | Manual                          | Start gravity compensation and free drive the arm | The robot starts gravity compensation and can be manually controlled. |
| Offline (Idle)            | LONG PRESS on the END BUTTON       | Manual                          | Start gravity compensation and free drive the arm | The robot starts gravity compensation and can be manually controlled. |
| Manual                    | LONG PRESS on the END BUTTON       | Online (Idle)                    | Stop gravity compensation and ready for external control | The robot stops gravity compensation and prepares for external control. |
| Online (Idle)             | DOUBLE CLICK on the END BUTTON     | Offline (Idle)                   | Stop responding to external control and prepare for trajectory replay | The robot stops responding to external control and prepares to replay a recorded trajectory. |
| Offline (Idle)            | DOUBLE CLICK on the END BUTTON     | Online (Idle)                    | Return to online mode accepting external control | The robot returns to online mode and accepts external commands. |
| Manual                    | DOUBLE CLICK on the END BUTTON     | Offline (Idle)                   | Stop gravity compensation and prepare for trajectory replay | The robot stops gravity compensation and prepares for trajectory replay. |
| Offline (Idle)            | SINGLE CLICK on the BASE BUTTON    | Offline (Moving to starting point) | Prepare to replay recorded trajectory by moving to the starting point | The robot moves to the starting point to prepare for trajectory replay. |
| Manual                    | SINGLE CLICK on the BASE BUTTON    | Manual (Recording)               | Start recording trajectory                    | The robot starts recording the trajectory while in manual mode. |
| Manual (Recording)        | SINGLE CLICK on the BASE BUTTON    | Manual                          | Stop recording trajectory                    | The robot stops recording the trajectory. |
| Manual or Manual (Recording) | SINGLE CLICK on the END BUTTON     | UNCHANGED                        | Gripper opened / closed                      | The gripper opens or closes while dragging the end effector. |

The states can also be switched by external commands

manual_mode(): Switch to manual mode

online_mode(): Switch to online mode

offline_mode(): Switch to offline mode

record_start(): Start recording trajectory

record_stop(): Stop recording trajectory

replay_start(): Start replaying recorded trajectory

# Zero Position Calibration
```bash
airbot_set_zero -m can[i] 

```
control by keyboard:
```
airbot_kbd_ctrl -m can0 -e 
airbot_kbd_ctrl -m can1 -e 
airbot_kbd_ctrl -m can2 -e 
airbot_kbd_ctrl -m can3 -e 
```
Afterward, we can collect our data and use it as input for the affordance model to train the model.

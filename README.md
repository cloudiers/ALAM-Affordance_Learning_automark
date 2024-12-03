# Affordance_Learning
Affordance_Learning of robotarm（Dual arm cooperative control)
With the development of robotics technology, robotic arms have been widely applied in various fields such as industrial automation, logistics warehousing, and medical assistance. However, traditional robotic arm control relies on precise preset paths and external sensors, which are insufficient in dynamic environments or when facing complex tasks. Therefore, to enhance the autonomy and intelligence of robotic arms, control methods combining visual perception and reinforcement learning have become a research hotspot.

Visual perception gives the robotic arm the ability to understand and perceive its surrounding environment, while reinforcement learning, through trial and error and feedback mechanisms, allows the robotic arm to autonomously learn how to perform tasks such as grasping, moving, and manipulating objects. Particularly in scenarios involving tasks like wall pulling or moving, the robotic arm can use visual perception to identify the positions of walls and obstacles in real-time, and through reinforcement learning, autonomously explore suitable action strategies to optimize task completion efficiency.

This research aims to develop an intelligent robotic arm control system based on visual perception and reinforcement learning, which will output affordance features of interactions between the machine and the real world through affordance learning. In the future, it can be combined with reinforcement learning to enable the robotic arm to autonomously learn how to manipulate walls and complete precise grasping and moving tasks in complex dynamic environments.
<p align="center">
  <img src="https://github.com/user-attachments/assets/dd2aa675-f083-46be-9fcf-85f7fe11cbf9" alt="df7510d1df254d83e6180a43bde1c80_副本" style="width: 43%;">
</p>

# Setting
We are using the Airbot series for teleoperated robotic arms, and a lot of work needs to be done before completing autonomous annotation. The environment is set to PyTorch 1.11 with CUDA 11.3.Please first install PyTorch, CUDA 11.3, and Ubuntu 20.04, and complete the related operations.To install ROS, you can refer to the following [tutorial](https://www.ros.org/).
# 创建Conda环境

在Ubuntu 20.04中创建一个文件夹并进入其中，接着创建并激活一个名为`angrobot`的Conda环境。

```bash
mkdir angrobotarm
cd angrobotarm
# 创建conda环境
conda create --name angrobot python=3.8
conda activate angrobot
```

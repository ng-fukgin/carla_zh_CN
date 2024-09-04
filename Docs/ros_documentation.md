
# ROS Bridge
完整的ROS桥接文档可以在[这里](https://carla.readthedocs.io/projects/ros-bridge/en/latest/)找到。
---
ROS桥接实现了ROS与CARLA之间的双向通信。CARLA服务器的信息被转换成ROS主题。同样地，在ROS节点之间发送的消息也被转换成在CARLA中应用的命令。
ROS桥接与ROS 1和ROS 2都兼容。
ROS桥接具有以下特点：
- 提供传感器数据，包括激光雷达（LIDAR）、语义激光雷达、摄像头（深度、分割、RGB、DVS）、全球导航卫星系统（GNSS）、雷达和惯性测量单元（IMU）。
- 提供对象数据，如变换、交通灯状态、可视化标记、碰撞和车道入侵。
- 通过转向、油门和刹车控制自动驾驶代理（AD agents）。
- 控制CARLA模拟的某些方面，如同步模式、播放和暂停模拟以及设置模拟参数。

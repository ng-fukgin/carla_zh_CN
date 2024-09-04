
以下是上述内容的中文翻译：
# Scenic
本指南概述了如何使用Scenic与CARLA生成多个多样化的场景，只需一个场景定义。本指南假设用户已经了解Scenic的语法。如果您需要了解更多关于Scenic的信息，请阅读他们的《Scenic入门指南》([点击这里](https://scenic-lang.readthedocs.io/en/latest/quickstart.html))，并查看他们的静态([点击这里](https://scenic-lang.readthedocs.io/en/latest/tutorials/tutorial.html))和动态([点击这里](https://scenic-lang.readthedocs.io/en/latest/tutorials/dynamics.html))场景创建教程。
在本指南结束时，您将了解：
- 在CARLA上运行Scenic脚本所需的最低要求。
- 如何编写简单的场景定义以生成多种场景模拟。
- 如何在CARLA上运行Scenic脚本。
- 用于配置CARLA上的Scenic模拟的参数。
---
- [Scenic](#scenic)
  - [开始之前](#开始之前)
  - [Scenic域](#scenic域)
  - [创建用于CARLA的Scenic场景](#创建用于carla的scenic场景)
    - [运行场景](#运行场景)
  - [一个pygame窗口将出现，并且场景将重复播放，每次都在脚本中设置的约束范围内生成一个独特的场景。要停止场景生成，请在终端中按 `ctrl + C`。](#一个pygame窗口将出现并且场景将重复播放每次都在脚本中设置的约束范围内生成一个独特的场景要停止场景生成请在终端中按-ctrl--c)
    - [附加参数](#附加参数)
---
## 开始之前
在使用Scenic与CARLA之前，您需要满足以下要求：
- 安装 [Python 3.8](https://www.python.org/downloads/) 或更高版本。
- 安装 [Scenic](https://scenic-lang.readthedocs.io/en/latest/quickstart.html#installation)。
---
## Scenic域
Scenic有一个通用的驾驶域，允许用户定义可以在任何驾驶模拟器上运行的场景。此外，它还有特定于每个模拟器的域。更多信息请查看[这里](https://scenic-lang.readthedocs.io/en/latest/libraries.html)关于Scenic域的信息。
每个域中特别重要的是行为和动作定义。以下链接提供了Scenic驾驶域和CARLA域的行为和动作的参考材料：
- [Scenic驾驶域中的行为](https://scenic-lang.readthedocs.io/en/latest/modules/scenic.domains.driving.behaviors.html)
- [CARLA域中的行为](https://scenic-lang.readthedocs.io/en/latest/modules/scenic.simulators.carla.behaviors.html)
- [Scenic驾驶域中的动作](https://scenic-lang.readthedocs.io/en/latest/modules/scenic.domains.driving.actions.html)
- [CARLA域中的动作](https://scenic-lang.readthedocs.io/en/latest/modules/scenic.simulators.carla.actions.html#module-scenic.simulators.carla.actions)
---
## 创建用于CARLA的Scenic场景
本节将逐步介绍如何编写一个基本Scenic脚本，其中一个领先车辆由于道路上的障碍物而突然减速。随后，一个 ego 车辆需要突然刹车以避免与领先车辆发生碰撞。完整的脚本可以在Scenic仓库中找到，其中包含涉及更复杂路网的示例。[点击这里查看完整脚本](https://github.com/BerkeleyLearnVerify/Scenic/blob/master/examples/carla/Carla_Challenge/carlaChallenge2.scenic)。
__1.__ 设置地图参数并声明场景中要使用的模型：
- `.xodr` 文件应设置为 [`map`][scenic_map] 参数的值，稍后将用于生成路网信息。
- `carla_map` 参数指的是您希望在模拟中使用的CARLA地图的名称。如果定义了这个参数，Scenic将加载地图的所有资产（建筑物、树木等），如果没有定义，则使用[OpenDRIVE独立模式](adv_opendrive.md)。
- 模型包括运行CARLA场景的所有特定工具。这应该定义在您想要在CARLA上运行的每个脚本中。
```scenic
## 设置地图和模型
param map = localPath('../../../tests/formats/opendrive/maps/CARLA/Town01.xodr')
param carla_map = 'Town01'
model scenic.simulators.carla.model
```
[scenic_map]: https://scenic-lang.readthedocs.io/en/latest/modules/scenic.domains.driving.model.html?highlight=map#module-scenic.domains.driving.model
__2.__ 定义场景中要使用的常量：
该场景涉及两辆车，领先车辆和ego车辆。我们将定义ego车辆模型、两辆车的速度、刹车的距离阈值和要施加的刹车量。
```scenic
## 常量
EGO_MODEL = "vehicle.lincoln.mkz_2017"
EGO_SPEED = 10
EGO_BRAKING_THRESHOLD = 12
LEAD_CAR_SPEED = 10
LEADCAR_BRAKING_THRESHOLD = 10
BRAKE_ACTION = 1.0
```
__3__. 定义场景行为：
在此场景中，我们将使用Scenic [行为库](https://scenic-lang.readthedocs.io/en/latest/modules/scenic.domains.driving.behaviors.html) 来指导ego车辆以预定的速度跟随车道，然后在到达另一辆车的某个距离阈值内时突然猛踩刹车。领先车辆也将以预定的速度跟随车道，并在到达任何障碍物的某个距离阈值内时猛踩刹车：
```scenic
## 定义行为
# EGO行为：跟随车道，并在通过领先车辆的阈值距离后刹车
behavior EgoBehavior(speed=10):
    try:
        do FollowLaneBehavior(speed)
    interrupt when withinDistanceToAnyCars(self, EGO_BRAKING_THRESHOLD):
        take SetBrakeAction(BRAKE_ACTION)
# 领先车辆行为：跟随车道，并在通过障碍物的阈值距离后刹车
behavior LeadingCarBehavior(speed=10):
    try: 
        do FollowLaneBehavior(speed)
    interrupt when withinDistanceToAnyObjs(self, LEADCAR_BRAKING_THRESHOLD):
        take SetBrakeAction(BRAKE_ACTION)
```
__4.__ 生成路网：
Scenic [roads库](https://scenic-lang.readthedocs.io/en/latest/modules/scenic.domains.driving.roads.html) 用于生成路网几何和交通信息。路网由 `Network` 类的一个实例表示，从脚本开头定义的 `.xodr` 文件生成。
```scenic
## 定义空间关系
# 确保 '*' 用于从 'lanes' 列表的所有元素中均匀随机选择
lane = Uniform(*network.lanes)
```
__5.__ 设置场景：
我们现在将定义车辆的起始位置和物体的放置。
- 在车道中央放置一个垃圾桶：
```scenic
obstacle = Trash on lane.centerline
```
- 将领先车辆以预定的速度沿着道路放置，在障碍物后50到30米处：
```scenic
leadCar = Car following roadDirection from obstacle for Range(-50, -30),
        with behavior LeadingCarBehavior(LEAD_CAR_SPEED)
```
- 将ego车辆以预定的速度沿着道路放置，在领先车辆后15到10米处：
```scenic
ego = Car following roadDirection from leadCar for Range(-15, -10),
        with blueprint EGO_MODEL,
        with behavior EgoBehavior(EGO_SPEED)
```
- 要求场景发生在距离交叉口80米以外的地方：
```scenic
require (distance to intersection) > 80
```
__6.__ 设置一个结束点，以便脚本知道场景何时结束：
当ego车辆的速度低于0.1米/秒，并且位于障碍物30米以内时，场景将结束。
```scenic
terminate when ego.speed < 0.1 and (distance to obstacle) < 30
```
---
### 运行场景
要运行Scenic场景：
__1.__ 启动CARLA服务器。
__2.__ 运行以下命令：
```scenic
scenic path/to/scenic/script.scenic --simulate
```
一个pygame窗口将出现，并且场景将重复播放，每次都在脚本中设置的约束范围内生成一个独特的场景。要停止场景生成，请在终端中按 `ctrl + C`。
---
### 附加参数
CARLA模型提供了几个全局参数，可以在场景中使用 `param` 语句覆盖，或者通过命令行使用 `--param` 选项覆盖。
以下是在CARLA模型中可配置的参数表：
| 名称 | 值 | 描述 |
|------|-------|-------------|
| `carla_map` | `str` | 要使用的CARLA地图的名称（例如 'Town01'）。如果设置为 `None`，CARLA将尝试使用在 [`map`][scenic_map] 参数中定义的 `.xodr` 文件在OpenDRIVE独立模式下创建世界。 |
| `timestep` | `float` | 模拟要使用的步长（Scenic中断CARLA以运行行为、检查要求等的频率）以秒为单位。默认为0.1秒。 |
| `weather` | `str` 或 `dict` | 模拟要使用的天气。可以是识别CARLA天气预设的字符串（例如 'ClearSunset'）或指定所有[天气参数](python_api.md#carla.WeatherParameters)的字典。默认为所有天气预设的均匀分布。 |
| `address` | `str` | 连接到CARLA的IP地址。默认为本地主机（127.0.0.1）。|
| `port` | `int` | 连接到CARLA的端口。默认为2000。 |
| `timeout` | `float` | 尝试连接到CARLA时的最大等待时间（秒）。默认为10。|
| `render` | `int` | 是否让CARLA创建一个窗口，从ego对象的视角显示模拟：`1` 为是，`0` 为否。默认为 `1`。 |
| `record` | `str` | 如果非空，保存CARLA记录文件的文件夹，以便重新播放模拟。默认为空。 |
<br>



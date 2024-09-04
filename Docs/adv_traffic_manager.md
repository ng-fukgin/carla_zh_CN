# 交通管理器 (Traffic Manager)
- [__什么是交通管理器？__](#什么是交通管理器)
	- [结构化设计](#结构化设计)
	- [用户自定义](#用户自定义)
- [__架构__](#架构)
	- [概述](#概述)
	- [ALSM](#alsm)
	- [车辆注册表](#车辆注册表)
	- [模拟状态](#模拟状态)
	- [控制循环](#控制循环)
	- [内存地图](#内存地图)
	- [PBVT](#pbvt)
	- [PID控制器](#pid控制器)
	- [命令数组](#命令数组)
	- [控制循环的各个阶段](#控制循环的各个阶段)
- [__使用交通管理器__](#使用交通管理器)
	- [车辆行为考虑](#车辆行为考虑)
	- [创建交通管理器](#创建交通管理器)
	- [配置自动驾驶行为](#配置自动驾驶行为)
	- [停止交通管理器](#停止交通管理器)
- [__确定性模式__](#确定性模式)
- [__混合物理模式__](#混合物理模式)
- [__运行多个交通管理器__](#运行多个交通管理器)
	- [交通管理器服务器与客户端](#交通管理器服务器与客户端)
	- [多客户端模拟](#多客户端模拟)
	- [多TM模拟](#多tm模拟)
	- [多模拟](#多模拟)
- [__同步模式__](#同步模式)
- [__大地图中的交通管理器__](#大地图中的交通管理器)
---
## 什么是交通管理器？
交通管理器（TM）是控制模拟中自动驾驶模式车辆的模块。其目标是使模拟中充满真实的城市交通条件。用户可以自定义某些行为，例如设置特定的学习环境。
### 结构化设计
TM构建在CARLA的客户端端。执行流程被划分为__阶段__，每个阶段都有独立的操作和目标。这便于开发与阶段相关的功能性和数据结构，同时提高了计算效率。每个阶段都在不同的线程上运行。与其他阶段的通信通过同步消息管理。信息单向流动。
### 用户自定义
用户可以通过设置参数来控制交通流，允许、强制或鼓励特定的行为。用户可以在线或离线更改交通行为，如他们所愿。例如，汽车可以被允许忽略速度限制或强制变道。在尝试模拟现实时，能够摆弄行为是不可或缺的。驾驶系统需要在特定的和非典型的环境中进行训练。
---
## 架构
### 概述
![架构](img/tm_2_architecture.jpg)
上面的图是TM内部架构的表示。每个组件的C++代码可以在`LibCarla/source/carla/trafficmanager`中找到。下面几节将详细解释每个组件。逻辑的简化概述如下：
__1. 存储和更新模拟的当前状态。__
- [代理生命周期与状态管理](#alsm)（ALSM）扫描世界以跟踪所有存在的车辆和行人，并清理那些不再存在的条目。所有数据都是从服务器检索的，并通过几个[阶段](#控制循环的各个阶段)传递。ALSM是唯一向服务器发出调用的组件。
- [车辆注册表](#车辆注册表)包含一个由TM控制的自动驾驶车辆数组，以及一个不由TM控制的非自动驾驶的行人和车辆列表。
- [模拟状态](#模拟状态)是所有模拟中的车辆和行人的位置、速度和额外信息的缓存存储。
__2. 计算每个自动驾驶车辆的运动。__
TM根据[模拟状态](#模拟状态)为[车辆注册表](#车辆注册表)中的所有车辆生成可行的命令。每个车辆的计算都是单独进行的。这些计算被划分为不同的[阶段](#控制循环的各个阶段)。[控制循环](#控制循环)通过在阶段之间创建__同步屏障__来确保所有计算的一致性。在当前阶段对所有车辆的计算完成之前，没有车辆会进入下一个阶段。每个车辆都会经历以下阶段：
>__2.1 - [定位阶段](#阶段-1-定位阶段)。__
>使用从[内存地图](#内存地图)收集的附近航点列表动态创建路径，内存地图是对模拟地图的简化，作为一个航点网格。路口的方向是随机选择的。每个车辆的路径由[路径缓冲区与车辆跟踪](#pbvt)（PBVT）组件存储和维护，以便在未来的阶段中轻松访问和修改。



>__2.2 - [碰撞阶段](#阶段-2-碰撞阶段)。__
>在每个车辆路径上扩展边界框，以识别和规避潜在的碰撞危险。
>__2.3 - [交通灯阶段](#阶段-3-交通灯阶段)。__
>与碰撞阶段类似，识别由于交通灯影响、停车标志和路口优先级而影响每个车辆路径的潜在危险。
>__2.4 - [运动规划器阶段](#阶段-4-运动规划器阶段)。__
>根据定义的路径计算车辆运动。一个[PID控制器](#pid控制器)确定如何到达目标航点。这然后被转换为CARLA命令，以在下一步中应用。
>__2.5 - [车辆灯光阶段](#阶段-5-车辆灯光阶段)。__
>车辆灯光根据环境因素（例如阳光、雾或雨的存在）和车辆行为（例如，如果车辆将在下一个路口左转/右转，则打开方向指示灯；如果刹车，则打开停车灯）动态开启/关闭。
__3. 在模拟中应用命令。__
在上一步骤中生成的命令被收集到[命令数组](#命令数组)中，并以批处理方式发送到CARLA服务器，以便在同一帧中应用。
以下各节将详细解释上述TM逻辑中的每个组件和阶段。
### ALSM
ALSM代表__代理生命周期与状态管理__。它是TM逻辑循环的第一步，提供了模拟当前状态的环境。
ALSM组件：
- 扫描世界以跟踪所有车辆和行人，他们的位置和速度。如果启用物理，速度是通过[Vehicle.get_velocity()](python_api.md#carla.Vehicle)检索的。否则，速度是通过随时间的位置更新历史来计算的。
- 在[模拟状态](#模拟状态)组件中存储每个车辆和行人的位置、速度和附加信息（交通灯影响、边界框等）。
- 更新[车辆注册表](#车辆注册表)中TM控制的车辆列表。
- 更新[控制循环](#控制循环)和[PBVT](#pbvt)组件中的条目以匹配车辆注册表。
__相关.cpp文件：__ `ALSM.h`, `ALSM.cpp`.
### 车辆注册表
车辆注册表跟踪模拟中的所有车辆和行人。
车辆注册表：
- 从[ALSM](#alsm)接收更新的车辆和行人列表。
- 在单独的数组中存储注册到TM的车辆，以便在[控制循环](#控制循环)期间迭代。
__相关.cpp文件：__ `MotionPlannerStage.cpp`.
### 模拟状态
模拟状态存储有关模拟中所有车辆的信息，以便在后续阶段轻松访问和修改。
模拟状态：
- 从[ALSM](#alsm)接收数据，包括当前演员位置、速度、交通灯影响、交通灯状态等。
- 将所有信息存储在缓存中，避免在[控制循环](#控制循环)期间对服务器进行后续调用。
__相关.cpp文件：__ `SimulationState.cpp`, `SimulationState.h`.
### 控制循环
控制循环管理所有自动驾驶车辆下一个命令的计算，以便它们同步执行。控制循环由五个不同的[阶段](#控制循环的各个阶段)组成；定位、碰撞、交通灯、运动规划器和车辆灯光。
控制循环：
- 从[车辆注册表](#车辆注册表)接收TM控制的车辆数组。
- 通过遍历数组，单独对每个车辆执行计算。
- 将计算划分为一系列[阶段](#控制循环的各个阶段)。
- 在阶段之间创建同步屏障，以确保一致性。所有车辆的当前阶段计算完成之前，没有车辆会进入下一个阶段，确保所有车辆在同一帧中更新。
- 协调[阶段](#控制循环的各个阶段)之间的过渡，以便所有计算同步完成。
- 当最后几个阶段（[运动规划器阶段](#阶段-4-运动规划器阶段)和[车辆灯光阶段](#阶段-5-车辆灯光阶段)）完成时，发送[命令数组](#命令数组)到服务器，以便命令计算和命令应用之间没有帧延迟。
__相关.cpp文件：__ `TrafficManagerLocal.cpp`.


### 内存地图

内存地图是[PBVT](#pbvt)中的一个辅助模块，用于[定位阶段](#stage-1-localization-stage)。

内存地图功能如下：

- 将地图转换为离散的网格状路径点。
- 将路径点包含在一个特定的数据结构中，提供更多信息以连接路径点并识别道路、交叉路口等。
- 使用一个ID来标识这些结构，用于快速定位附近区域的车辆。

__相关 .cpp 文件：__ `InMemoryMap.cpp` 和 `SimpleWaypoint.cpp`。

### PBVT

PBVT 代表 __路径缓冲和车辆追踪__（Path Buffer and Vehicle Tracking）。PBVT 是一种数据结构，包含每辆车的预期路径，并允许在[控制循环](#control-loop)中轻松访问数据。

PBVT 功能如下：

- 包含一个 deque 对象的映射，每辆车对应一个条目。
- 为每辆车包含一组路径点，描述其当前位置和近未来的路径。
- 包含[内存地图](#in-memory-map)，用于[定位阶段](#stage-1-localization-stage)将每辆车与最近的路径点和可能重叠的路径相关联。

### PID控制器

PID控制器是一个辅助模块，在[运动规划阶段](#stage-4-motion-planner-stage)进行计算。

PID控制器功能如下：

- 使用[运动规划阶段](#stage-4-motion-planner-stage)收集的信息估算达到目标值所需的油门、刹车和转向输入。
- 根据控制器的特定参数化进行调整。参数可以根据需要进行修改。想了解如何进行修改，请阅读更多关于[PID控制器](https://en.wikipedia.org/wiki/PID_controller)的内容。

__相关 .cpp 文件：__ `PIDController.cpp`。

### 命令数组

命令数组是 TM 逻辑循环的最后一步。它接收所有注册车辆的命令并应用它们。

命令数组功能如下：

- 接收来自[运动规划阶段](#stage-4-motion-planner-stage)的一系列 [carla.VehicleControl](python_api.md#carla.VehicleControl)。
- 将所有命令批量处理，并在同一帧内应用。
- 根据模拟运行的模式（异步或同步）调用 [carla.Client](../python_api/#carla.Client) 中的 __apply_batch()__ 或 __apply_batch_synch()__，将批处理命令发送到CARLA服务器。

__相关 .cpp 文件：__ `TrafficManagerLocal.cpp`。

### 控制循环的阶段

##### 阶段1- 定位阶段

定位阶段为TM控制的车辆定义近未来的路径。

定位阶段功能如下：

- 从[模拟状态](#simulation-state)中获取所有车辆的位置和速度。
- 使用[内存地图](#in-memory-map)将每辆车与一系列路径点相关联，这些路径点描述了根据其轨迹的当前位置和近未来的路径。车辆速度越快，列表越长。
- 根据规划决策更新路径，如车道变更、限速、与前车的距离等。
- 将所有车辆的路径存储在[PBVT](#pbvt)模块中。
- 比较各路径以估计可能的碰撞情况。结果传递到碰撞阶段。

__相关 .cpp 文件：__ `LocalizationStage.cpp` 和 `LocalizationUtils.cpp`。

##### 阶段2- 碰撞阶段

碰撞阶段触发碰撞风险。

碰撞阶段功能如下：

- 从[定位阶段](#stage-1-localization-stage)接收可能路径重叠的车辆对列表。
- 沿着每对车辆的路径延伸边界框（测地边界），检查它们是否实际重叠，并确定碰撞风险是否真实存在。
- 将所有可能的碰撞风险发送到[运动规划阶段](#stage-4-motion-planner-stage)，以相应地修改路径。

__相关 .cpp 文件：__ `CollisionStage.cpp`。

##### 阶段3- 交通信号阶段

交通信号阶段触发因交通管制（如交通灯、停车标志和交叉路口优先级）引发的风险。

交通信号阶段功能如下：

- 如果车辆受黄灯或红灯或停车标志的影响，则设置交通风险。
- 如果车辆处于未标识的交叉路口，则沿车辆路径延伸边界框。路径重叠的车辆按照“先进先出”顺序移动。等待时间设置为固定值。

__相关 .cpp 文件：__ `TrafficLightStage.cpp`。

##### 阶段4- 运动规划阶段

运动规划阶段生成要应用于车辆的CARLA命令。

运动规划阶段功能如下：

- 收集车辆的位置和速度（[模拟状态](#simulation-state)）、路径（[PBVT](#pbvt)）和风险（[碰撞阶段](#stage-2-collision-stage)和[交通信号阶段](#stage-3-traffic-light-stage)）。
- 对车辆应如何移动做出高级决策，例如计算防止碰撞风险所需的刹车。使用[PID控制器](#pid-controller)根据目标值估计行为。
- 将所需的运动转换为[carla.VehicleControl](python_api.md#carla.VehicleControl)以应用于车辆。
- 将生成的CARLA命令发送到[命令数组](#command-array)。

__相关 .cpp 文件：__ `MotionPlannerStage.cpp`。

##### 阶段5- 车辆灯光阶段

车辆灯光阶段根据车辆状况和周围环境激活灯光。

车辆灯光阶段功能如下：

- 检索车辆的规划路径、车辆灯光信息（例如灯光状态和要应用的计划命令）以及天气状况。
- 确定车辆灯光的新状态：
	- 如果车辆计划在下一个交叉路口左转/右转，则打开转向灯。
	- 如果应用的命令要求车辆刹车，则打开刹车灯。
	- 从日落到日出或在大雨中打开近光灯和位置灯。
	- 在浓雾条件下打开雾灯。

- 如果车辆灯光状态发生变化，则更新其状态。

__相关 .cpp 文件：__ `VehicleLightStage.cpp`。
---

以下是您提供的文档内容的中文翻译：
## 使用交通管理器
### 车辆行为考虑因素
交通管理器(TM)实现了通用行为模式，在设置车辆自动驾驶时必须考虑这些模式：
- __车辆不是目标导向的__，它们遵循动态生成的轨迹，并在接近路口时随机选择路径。它们的路径是无限的。
- __车辆的目标速度是其当前速度限制的70%__，除非设置了其他值。
- __路口优先级不遵循交通规则__。TM在路口使用自己的优先级系统。这个限制的解决方案正在进展中。在此期间，可能会出现一些问题，例如，环岛内的车辆会让试图进入的车辆。
可以通过Python API调整TM的行为。有关具体方法，请参阅Python API文档中的[TM部分](../python_api/#carla.TrafficManager)。以下是通过API可能实现的内容的一般摘要：
| 主题 | 描述 |
| ----- | ----------- |
| **一般：** | - 创建一个连接到端口的TM实例。 <br> - 检索TM连接的端口。 |
| **安全条件：** | - 设置停止车辆之间的最小距离（单个车辆或所有车辆）。这将影响最小移动距离。 <br> - 将期望速度设置为当前速度限制的百分比（单个车辆或所有车辆）。 <br> - 重置交通灯。 |
| **碰撞管理：** | - 启用/禁用车辆与特定演员之间的碰撞。 <br> - 使车辆忽略所有其他车辆。 <br> - 使车辆忽略所有行人。 <br> - 使车辆忽略所有交通灯。 |
| **车道变更：** | - 强制车道变更，忽略可能的碰撞。 <br> - 启用/禁用车辆的车道变更。 |
| **混合物理模式：** | - 启用/禁用混合物理模式。 <br> - 更改启用物理的半径。 |
### 创建交通管理器
!!! Note
	TM旨在同步模式下工作。在异步模式下使用TM可能导致意外和不理想的结果。有关同步模式的更多信息，请阅读[同步模式](#synchronous-mode)部分。
TM实例由`carla.Client`创建，传递要使用的端口。默认端口是`8000`。
创建TM实例的方法如下：
```python
tm = client.get_trafficmanager(port)
```
要为一组车辆启用自动驾驶，请检索TM实例的端口，并将`set_autopilot`设置为`True`，同时传递TM端口。如果没有提供端口，它将尝试连接到默认端口（`8000`）。如果TM不存在，它将创建一个：
```python
tm_port = tm.get_port()
for v in vehicles_list:
     v.set_autopilot(True,tm_port)
```
!!! Note 
    在多客户端情况下创建或连接TM与上述示例不同。了解更多信息，请参阅[运行多个交通管理器](#running-multiple-traffic-managers)部分。
`generate_traffic.py`脚本位于`/PythonAPI/examples`目录，提供了一个如何使用脚本参数传递的端口创建TM实例并注册每个生成的车辆的示例，通过将自动驾驶设置为`True`：
```py
traffic_manager = client.get_trafficmanager(args.tm-port)
tm_port = traffic_manager.get_port()
...
batch.append(SpawnActor(blueprint, transform).then(SetAutopilot(FutureActor, True,tm_port)))
...
traffic_manager.global_percentage_speed_difference(30.0)
```
### 配置自动驾驶行为
以下示例创建了一个TM实例，并为特定车辆配置了危险行为，使其将忽略所有交通灯，不留下与其他车辆的安全距离，并且比当前速度限制快20%：
```python
tm = client.get_trafficmanager(port)
tm_port = tm.get_port()
for v in my_vehicles:
  v.set_autopilot(True,tm_port)
danger_car = my_vehicles[0]
tm.ignore_lights_percentage(danger_car,100)
tm.distance_to_leading_vehicle(danger_car,0)
tm.vehicle_percentage_speed_difference(danger_car,-20)
``` 
以下示例将同一组车辆设置为自动驾驶，但配置了它们以适中的驾驶行为行驶。车辆的速度比当前速度限制慢80%，与其他车辆保持至少5米的距离，并且从不进行车道变更：
```python
tm = client.get_trafficmanager(port)
tm_port = tm.get_port()
for v in my_vehicles:
  v.set_autopilot(True,tm_port)
danger_car = my_vehicles[0]
tm.global_distance_to_leading_vehicle(5)
tm.global_percentage_speed_difference(80)
for v in my_vehicles: 
  tm.auto_lane_change(v,False)
``` 
#### 将车辆灯光更新委托给交通管理器
默认情况下，TM管理的车辆灯光（刹车、转向灯等）永远不会更新。可以将TM委托为自动更新给定车辆演员的车辆灯光：
```python
tm = client.get_trafficmanager(port)
for actor in my_vehicles:
  tm.update_vehicle_lights(actor, True)
```
车辆灯光管理必须在车辆级别上指定，并且在任何给定时间点，都可以有使用自动灯光管理和不使用自动灯光管理的车辆。
### 停止交通管理器
TM不是一个需要销毁的演员；当创建它的客户端停止时，它也会停止。这由API自动管理，用户不需要做任何事情。然而，在关闭TM时，用户必须销毁由它控制的车辆，否则它们将停留在地图上不动。《generate_traffic.py》脚本会自动执行此操作：
```python
client.apply_batch([carla.command.DestroyActor(x) for x in vehicles_list])
```
!!! Warning
    关闭__TM-服务器__将关闭连接到它的__TM-客户端__。要了解__TM-服务器__和__TM-客户端__之间的区别，请阅读[运行多个交通管理器](#running-multiple-traffic-managers)部分。
---
## 确定性模式
在确定性模式下，TM将在相同条件下产生相同的结果和行为。不要将确定性误认为是记录器。虽然记录器允许您存储模拟的日志以进行回放，但确定性确保了TM在不同的脚本执行中，只要条件保持不变，就会产生相同的输出。
确定性模式仅在同步模式下可用。在异步模式下，对模拟的控制较少，无法实现确定性。在启用确定性模式之前，请阅读[同步模式](#synchronous-mode)部分。
要启用确定性模式，请使用以下方法：
```py
my_tm.set_random_device_seed(seed_value)
```
`seed_value`是一个`int`类型的数字，将从该数字生成随机数。数值本身并不重要，但相同的数值将始终产生相同的输出。两个模拟，只要条件相同，并且使用相同的种子值，就会是确定性的。
为了在多次模拟运行中保持确定性，必须为每次模拟设置种子值。例如，每次重新加载世界时，都需要重新设置种子值：
```py
client.reload_world()
my_tm.set_random_device_seed(seed_value)
```
确定性模式可以在`generate_traffic.py`示例脚本中通过传递一个种子值作为参数来测试。以下示例以同步模式填充地图上的50个自动驾驶演员，并将种子值设置为任意的9：
```sh
cd PythonAPI/examples
python3 generate_traffic.py -n 50 --seed 9
```
!!! Warning
    在启用确定性模式之前，CARLA服务器和TM必须处于同步模式。有关同步模式在TM中的更多信息，请阅读[这里](#synchronous-mode)。
---
## 混合物理模式
混合模式允许用户禁用所有自动驾驶车辆的绝大多数物理计算，或者禁用自动驾驶车辆在“英雄”车辆指定半径外的物理计算。这消除了车辆物理的瓶颈。禁用物理计算的车辆将通过传送移动。基本线性加速度计算得以维持，以确保位置更新和车辆速度保持真实，并且物理计算的开启和关闭在车辆上是流畅的。
混合模式使用`Actor.set_simulate_physics()`方法来切换物理计算。默认情况下是禁用的。有两种方式可以启用它：
*   [启用`TrafficManager.set_hybrid_physics_mode(True)`](https://carla.readthedocs.io/en/latest/python_api/#carla.TrafficManager.set_hybrid_physics_mode) —— 这个方法为调用它的TM对象启用混合模式。
*   运行`generate_traffic.py`脚本时使用`--hybrid`标志 —— 这个示例脚本创建了一个TM并生成自动驾驶车辆。当传递`--hybrid`标志时，这些车辆将被设置为混合模式。
要修改混合模式的行为，请使用以下两个参数：
*   __半径__（默认= 50米）—— 半径相对于标记为“英雄”的车辆。所有在这个半径内的车辆都将启用物理；半径外的车辆将禁用物理。通过使用[`traffic_manager.set_hybrid_physics_radius(r)`](python_api.md#carla.TrafficManager.set_hybrid_physics_radius)来修改半径的大小。
*   __英雄车辆__ —— 一个标记有`role_name='hero'`的车辆，作为半径的中心。
    *   如果没有英雄车辆，所有车辆的物理将被禁用。
    *   如果有多个英雄车辆，将为它们各自考虑半径，创建不同的物理启用区域。
下面的剪辑展示了当混合模式激活时，如何开启和关闭物理。__英雄车辆__用__红色方块__标记。__物理被禁用的车辆__用__蓝色方块__标记。当进入英雄车辆的半径影响时，__物理将被启用，并且标记变为绿色__。
![Welcome to CARLA](img/tm_hybrid.gif)
---
## 运行多个交通管理器
### 交通管理器服务器和客户端
CARLA客户端通过指定要使用的端口来创建TM。如果没有指定端口，将使用默认的`8000`端口。如果进一步在同一个端口上创建TM，它们将成为__TM-客户端__，而最初的TM将成为__TM-服务器__。这些标题定义了TM在模拟中的行为。
#### TM-服务器
如果TM是第一个连接到空闲端口的，并且随后其他TM（TM-客户端）连接到它运行的同一个端口，则创建TM-服务器。__TM-服务器将控制所有TM-客户端的行为__，例如，如果TM-服务器停止，所有TM-客户端也将停止。
以下代码创建了两个TM-服务器，每个都连接到不同的、未使用的端口：
```py 
tm01 = client01.get_trafficmanager() # tm01 --> tm01 (p=8000)
```
```py
tm02 = client02.get_trafficmanager(5000) # tm02(p=5000) --> tm02 (p=5000)
```
#### TM-客户端
当TM连接到另一个TM（TM-服务器）正在使用的端口时，创建TM-客户端。TM-客户端的行为将由TM-服务器控制。
以下代码创建了两个TM-客户端，每个都连接到上面创建的TM-服务器：
```py
tm03 = client03.get_trafficmanager() # tm03 --> tm01 (p=8000). 
```
```py
tm04 = client04.get_trafficmanager(5000) # tm04(p=5000) --> tm02 (p=5000)
```
CARLA服务器会注册所有TM实例，通过存储端口和连接的客户端IP（对用户隐藏）来跟踪它们。目前没有办法检查到目前为止已经创建了哪些TM实例。始终会尝试建立连接，如果连接成功，它将创建一个新的__TM-服务器__或__TM-客户端__。
### 多客户端模拟
在多客户端模拟中，多个TM在同一个端口上被创建。第一个TM将成为TM-服务器，其余的将作为TM-客户端连接到它。TM-服务器将控制所有TM实例的行为：
```py
终端 1: ./CarlaUE4.sh -carla-rpc-port=4000
终端 2: python3 generate_traffic.py --port 4000 --tm-port 4050 # TM-服务器
终端 3: python3 generate_traffic.py --port 4000 --tm-port 4050 # TM-客户端
```
### 多TM模拟
在多TM模拟中，多个TM实例在不同的端口上被创建。每个TM实例将控制自己的行为：
```py
终端 1: ./CarlaUE4.sh -carla-rpc-port=4000
终端 2: python3 generate_traffic.py --port 4000 --tm-port 4050 # TM-服务器 A
终端 3: python3 generate_traffic.py --port 4000 --tm-port 4550 # TM-服务器 B
```
### 多模拟
多模拟是指同时运行多个CARLA服务器的情况。TM需要连接到相关的CARLA服务器端口。只要计算能力允许，TM可以同时运行多个模拟而不会出现问题：
```py
终端 1: ./CarlaUE4.sh -carla-rpc-port=4000 # 模拟 A 
终端 2: ./CarlaUE4.sh -carla-rpc-port=5000 # 模拟 B
终端 3: python3 generate_traffic.py --port 4000 --tm-port 4050 # TM-服务器 A 连接到模拟 A
终端 4: python3 generate_traffic.py --port 5000 --tm-port 5050 # TM-服务器 B 连接到模拟 B
```
多模拟的概念与TM本身无关。上述设置中，模拟A可以运行多客户端TM，而模拟B可以运行多TM或根本不运行TM。
最可能的问题是在尝试连接到一个尚未在所选模拟中运行的TM时。如果发生这种情况，将显示错误消息，并且连接将被中止，以防止不同模拟之间的干扰。
---
## 同步模式
TM旨在同步模式下工作。为了正确运行，CARLA服务器和TM都应该设置为同步模式。__在异步模式下使用TM可能导致意外和不理想的结果__，然而，如果需要异步模式，模拟应该至少以20-30 fps的速度运行。
以下脚本展示了如何将服务器和TM设置为同步模式：
```py
...
# 将模拟设置为同步模式
init_settings = world.get_settings()
settings = world.get_settings()
settings.synchronous_mode = True
# 之后，将TM设置为同步模式
my_tm.set_synchronous_mode(True)
...
# 在同一个客户端中 tick 世界
world.apply_settings(init_settings)
world.tick()
...
# 在脚本结束前始终禁用同步模式（对于世界和TM），以防止服务器在等待tick时阻塞
settings.synchronous_mode = False
my_tm.set_synchronous_mode(False)
```
`generate_traffic.py`示例脚本以同步模式启动一个TM并使用车辆和行人填充地图。它将自动将TM和CARLA服务器设置为同步模式：
```sh
cd PythonAPI/examples
python3 generate_traffic.py -n 50
```
如果需要异步模式，可以使用`--async`标志运行上述命令。
如果多个TM设置为同步模式，同步将失败。遵循以下指南以避免问题：
- 在__多客户端__情况下，只有__TM-服务器__应该设置为同步模式。
- 在__多TM__情况下，应该只设置__一个TM-服务器__为同步模式。
- __ScenarioRunner模块__会自动运行TM。ScenarioRunner中的TM将自动设置为同步模式。
!!! Warning
    在脚本管理ticks结束之前，始终禁用同步模式（对于世界和TM），以防止服务器在等待tick时阻塞。
---
## 交通管理器在大地图上的行为
为了了解TM在大地图上的工作方式，请确保首先熟悉大地图的工作原理，阅读相关文档[这里](large_map_overview.md)。
自动驾驶车辆在大地图上的行为取决于是否存在英雄车辆：
__英雄车辆不存在__
所有自动驾驶车辆将被视为休眠演员。休眠自动驾驶演员将像混合模式一样在地图上移动。车辆不会被渲染，因为没有英雄车辆来触发大地图瓦片的流式传输。
__英雄车辆存在__
自动驾驶车辆将在超过由`actor_active_distance`定义的值时变为休眠状态。要设置此值，请使用Python API：
```py
settings = world.get_settings()
# 演员将在2公里外变为休眠状态
settings.actor_active_distance = 2000
world.apply_settings(settings)
```
在TM中，休眠演员可以配置为在英雄车辆周围持续重新生成，而不是停留在地图的其他部分。此选项可以通过Python API中的`set_respawn_dormant_vehicles`方法配置。车辆将在用户定义的距离内重新生成。重新生成距离的上限和下限可以通过`set_boundaries_respawn_dormant_vehicles`方法设置。请注意，上限距离不会超过大地图的瓦片流式传输距离，最小下限距离为20米。
要在大地图上25米和700米范围内重新生成休眠车辆：
```py
my_tm.set_respawn_dormant_vehicles(True)
my_tm.set_boundaries_respawn_dormant_vehicles(25,700)
```
如果碰撞阻止休眠演员重新生成，TM将在下一个模拟步骤尝试重新生成。
如果休眠车辆没有被重新生成，其行为将取决于是否启用了混合模式。如果启用了混合模式，那么休眠演员将在地图上通过传送移动。如果混合模式未启用，那么休眠演员的物理将不会被计算，并且它们将停留在原位，直到它们不再休眠。
---
如果您对TM有任何疑问，可以在[论坛](https://github.com/carla-simulator/carla/discussions)上提问。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="Go to the CARLA forum">
CARLA论坛</a>
</p>
</div>




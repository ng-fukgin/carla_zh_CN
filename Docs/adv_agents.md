# CARLA Agents

CARLA Agent脚本允许车辆按照随机的、无尽的路线行驶，或者以最短的路线前往指定目的地。代理会遵守交通信号灯并对道路上的其他障碍物作出反应。有三种可用的代理类型。可以修改参数，如目标速度、制动距离、紧跟行为等。可以修改或使用Actor类作为基类来根据用户的需求创建自定义代理。

- [CARLA Agents](#carla-agents)
  - [代理脚本概述](#代理脚本概述)
    - [规划和控制](#规划和控制)
    - [代理行为](#代理行为)
  - [实现一个代理](#实现一个代理)
  - [行为类型](#行为类型)
  - [创建自定义行为类型](#创建自定义行为类型)
  - [创建代理](#创建代理)

---

## 代理脚本概述

CARLA Agents中的主要脚本位于`PythonAPI/carla/agents/navigation`目录下。它们分为两类：**规划和控制**和**代理行为**。

### 规划和控制

- `controller.py`：将纵向和横向PID控制器结合到一个类中，称为**VehiclePIDController**，用于从CARLA客户端对车辆进行低级控制。
- `global_route_planner.py`：从CARLA服务器获取详细的拓扑结构，构建世界地图的图形表示，为**Local Planner**提供路标和道路选项信息。
- `local_planner.py`：根据来自**VehiclePIDController**的控制输入，按照路标行驶。路标可以由**Global Route Planner**提供，也可以在动态计算中选择随机路径，类似于[Traffic Manager](adv_traffic_manager.md)。

### 代理行为

- `basic_agent.py`：包含一个实现了**Basic Agent**的代理基类，可以在地图上漫游或以最短的距离到达目标位置，避开其他车辆，对交通信号灯作出反应，但忽略停止标志。
- `behavior_agent.py`：包含一个实现了更复杂的**Behavior Agent**的类，可以以最短的距离到达目标位置，遵守交通信号灯、标志和速度限制，并紧跟其他车辆。有三种预定义的行为类型来控制代理的行为。
- `behavior_types.py`：包含条件**Behavior Agent**的行为类型的参数；谨慎型、正常型和激进型。

---

## 实现一个代理

本节将解释如何在自己的脚本中使用示例CARLA Agent类。在本节结束时，您将了解如何运行一个显示不同代理行为的示例脚本。

__1.__ 导入要使用的代理类：

```python
# 导入基本代理
from agents.navigation.basic_agent import BasicAgent

# 导入行为代理
from agents.navigation.behavior_agent import BehaviorAgent
```

__2.__任何车辆都可以转化为代理。[生成一个车辆](core_actors.md#spawning)，并将其作为参数传递给代理类以实例化：


```py
# To start a basic agent
agent = BasicAgent(vehicle)

# To start a behavior agent with an aggressive profile
agent = BehaviorAgent(vehicle, behavior='aggressive')
```

了解有关行为类型及如何配置自定义行为类型的更多信息，请查看[__行为类型__](#behavior-types)部分。


__3.__ 您可以设置代理前往的目的地。如果没有为代理设置目的地，它将在地图上无休止地漫游。要设置目的地，请为代理提供一个[位置](python_api.md#carlalocation)：


```py
destination = random.choice(spawn_points).location
agent.set_destination(destination)
```

__5.__ 在导航的每个步骤中，车辆控制和行为都会被应用。在每个步骤中，**基本代理**将应用车辆控制，并通过执行紧急停车来对其他车辆或交通信号灯做出反应。**行为代理**将根据所应用的行为类型对交通信号灯做出反应，避开行人，跟随其他车辆并在交叉口进行导航。


```py
while True:
    vehicle.apply_control(agent.run_step())
```

__6.__ 您可以检查代理是否已完成其轨迹，并在完成时执行相应的操作。以下代码片段将在车辆到达目的地后结束模拟：


```py
while True:
    if agent.done():
        print("The target has been reached, stopping the simulation")
        break

    vehicle.apply_control(agent.run_step())
```

__7.__ 当代理到达目标位置时，可以选择生成一个新的随机路径让代理继续跟随：


```py
while True:
    if agent.done():
        agent.set_destination(random.choice(spawn_points).location)
        print("The target has been reached, searching for another target")

    vehicle.apply_control(agent.run_step())
```

__Basic Agent__ 提供了几种方法来操作代理的行为或指定行驶路线：

- `set_target_speed(speed)`: 设置目标速度（以千米/小时为单位）
- `follow_speed_limits(value=True)`: 设置代理是否遵守速度限制。
- `set_destination(end_location, start_location=None)`: 代理将从指定的起始位置前往目标位置，通过最短路径进行行驶。如果未提供起始位置，则使用当前代理的位置作为起始位置。
- `set_global_plan(plan, stop_waypoint_creation=True, clean_queue=True)`: 为代理添加特定的行驶计划。计划参数应该是一个由`[carla.Waypoint, RoadOption]`组成的列表，代表代理需要遵循的路径。`stop_waypoint_creation`参数将阻止自动创建路径上的路点。`clean_queue`参数将重置代理的当前计划。
- `trace_route(start_waypoint, end_waypoint)`: 从全局路线规划器中获取两个路点之间的最短路径，并以`[carla.Waypoint, RoadOption]`的列表形式返回代理需要遵循的路径。
- `ignore_traffic_lights(active=True)`: 设置代理是否忽略或遵守交通信号灯。
- `ignore_stop_signs(active=True)`: 设置代理是否忽略或遵守停止标志。
- `ignore_vehicles(active=True)`: 设置代理是否忽略或对其他车辆做出反应。

`automatic_control.py` 脚本位于 `PythonAPI/examples` 目录中，展示了基本代理和行为代理的示例。要尝试运行该脚本，请导航到示例目录并执行以下命令：


```sh
# To run with a basic agent
python3 automatic_control.py --agent=Basic

# To run with a behavior agent
python3 automatic_control.py --agent=Behavior --behavior=aggressive
```

---

## 行为类型

行为代理的行为类型在 `behavior_types.py` 中定义。预设的三个配置文件是 __'cautious'__、__'normal'__ 和 __'aggressive'__。您可以使用这些预设配置文件，修改它们或创建自己的配置文件。以下变量可以进行调整：

- __`max_speed`__：您的车辆能够达到的最大速度，以千米/小时为单位。
- __`speed_lim_dist`__：以千米/小时为单位的值，定义了您的车辆的目标速度与当前速度限制之间的距离（例如，如果速度限制为30千米/小时，而 `speed_lim_dist` 为10千米/小时，则目标速度为20千米/小时）。
- __`speed_decrease`__：当接近前方较慢的车辆时，您的车辆以千米/小时为单位的速度减少速率。
- __`safety_time`__：碰撞时间；如果您的车辆突然刹车，与前方车辆碰撞所需的大致时间。
- __`min_proximity_threshold`__：与另一辆车或行人的最小距离（以米为单位），在达到该距离之前，您的车辆将执行回避或紧跟动作。
- __`braking_distance`__：与行人或车辆之间的距离，在该距离处，您的车辆将执行紧急停车。
- __`tailgate_counter`__：避免在上次紧跟动作之后过快地紧跟。

## 创建自定义行为类型

要创建自己的行为类型：

__1.__ 在 `behavior_types.py` 中创建行为类型的类：


```py
class ProfileName(object):
    # complete value definitions
```

__2.__ 在 `behavior_agent.py` 脚本中定义并实例化您的行为类型：


```py
# Parameters for agent behavior
if behavior == 'cautious':
    self._behavior = Cautious()

elif behavior == 'normal':
    self._behavior = Normal()

elif behavior == 'aggressive':
    self._behavior = Aggressive()

elif behavior == '<type_name>':
    self._behavior = <TypeName>()
```

---

## 创建代理

CARLA Agents 只是用户可以运行的代理类型的示例。用户可以基于基本代理 (`Basic Agent`) 构建自己的代理。可能性是无限的。每个代理都只需要两个必要的元素，即 __初始化__ 和 __运行步骤__。

以下是一个自定义代理最简布局的示例：


```py
import carla

from agents.navigation.basic_agent import BasicAgent

class CustomAgent(BasicAgent):
    def __init__(self, vehicle, target_speed=20, debug=False):
        """
        :param vehicle: actor to apply to local planner logic onto
        :param target_speed: speed (in Km/h) at which the vehicle will move
        """
        super().__init__(target_speed, debug)

    def run_step(self, debug=False):
        """
        Execute one step of navigation.
        :return: carla.VehicleControl
        """
        # Actions to take during each simulation step
        control = carla.VehicleControl()
        return control
```

请查看 `basic_agent.py` 和 `behavior_agent.py` 脚本，以了解它们的结构和函数，获取有关如何创建自己代理的更多想法。

---

您可以探索提供的代理脚本，对其进行扩展或将其用作创建自己代理的基础。如果您对代理有任何问题，欢迎在 [论坛](https://github.com/carla-simulator/carla/discussions/) 上发帖提问。








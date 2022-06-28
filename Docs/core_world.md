# 1st. 虚拟世界与客户端

客户端和虚拟世界是 CARLA 的两个基础，是操作仿真测试及其参与者的必要条件。

本教程从定义这些元素的基础和创建，到描述它们的可能性。 如果在阅读过程中出现任何疑问或问题，[CARLA forum](https://github.com/carla-simulator/carla/discussions/) 随时为您解决。 

*   [__The client__](#the-client)  
	*   [Client creation](#client-creation)  
	*   [World connection](#world-connection)  
	*   [Other client utilities](#other-client-utilities)  
*   [__The world__](#the-world)  
	*   [Actors](#actors)  
	*   [Weather](#weather)  
	*   [Lights](#lights)  
	*   [Debugging](#debugging)  
	*   [World snapshots](#world-snapshots)  
	*   [World settings](#world-settings)  

---
## The client

客户端是 CARLA 架构中的主要元素之一。 它们连接到服务器、检索信息和命令更改。 这是通过脚本完成的。 客户端识别自己，并连接到世界，然后使用模拟进行操作。

除此之外，客户还可以访问高级 CARLA 模块、功能和应用命令批处理。 本节仅介绍命令批处理。 这些对于生成大量actors之类的基本事物很有用。 其余功能更为复杂，将在各自页面的 __高级步骤__ 中进行介绍。.  

查看 Python API 参考中的 [__carla.Client__](python_api.md#carla.Client) 以了解该类的特定方法和变量. 


### Client creation

需要两件事。 标识它的 __IP__ 地址，以及用于与服务器通信的 __两个 TCP 端口__ .可选的第三个参数设置工作线程的数量。 默认情况下，这设置为全部 (`0`). Python API 参考中的 [carla.Client](python_api.md#carla.Client.__init__) 包含一个片段，显示了在运行脚本时如何将这些解析为参数. 

```py
client = carla.Client('localhost', 2000)
```
默认情况下，CARLA 使用本地主机 IP 和端口 2000 进行连接，但这些可以随意更改。 在这种情况下，第二个端口将始终为 `n+1`, 2001  

创建客户端后，设置其 __time-out__. 这限制了所有网络操作，因此这些操作不会永远阻塞客户端。 如果连接失败，将返回错误. 

```py
client.set_timeout(10.0) # seconds
```

可以连接多个客户端，因为一次运行多个脚本是很常见的。 在具有高级 CARLA 功能（例如流量管理器）的多客户端方案中工作必然会使通信更加复杂.  

!!! Note
    客户端和服务器具有不同的 `libcarla `模块。 如果版本不同，可能会出现问题。 这可以使用`get_client_version()` 和 `get_server_version()` 方法进行检查. 

### World connection

客户端可以相当轻松地连接和检索当前世界. 

```py
world = client.get_world()
```

客户端还可以获取可用地图列表来更改当前地图。 这将摧毁当前的世界并创造一个新的世界.
```py
print(client.get_available_maps())
...
world = client.load_world('Town01')
# client.reload_world() creates a new instance of the world with the same map. 
```

每个世界对象都有一个 `ID` 或情节。 每次客户端调用 `load_world()` 或 `reload_world()` 时，前一个都会被销毁。 新剧集是从头开始创建的。 在此过程中未重新启动虚幻引擎. 

### Using commands

__Commands__ 是对一些最常见的 CARLA 方法的改编，可以批量应用. 例如,  [command.SetAutopilot](python_api.md#command.SetAutopilot) 等价于 [Vehicle.set_autopilot()](python_api.md#carla.Vehicle.set_autopilot), 为车辆启用自动驾驶仪. 但是, 使用 [Client.apply_batch](python_api.md#carla.Client.apply_batch) 或 [Client.apply_batch_sync()](python_api.md#carla.Client.apply_batch_sync), 方法，可以在一个模拟步骤中应用一系列命令。 这对于通常应用于甚至数百个元素的方法变得非常有用。.  

以下示例使用批处理一次性销毁车辆列表.  

```py
client.apply_batch([carla.command.DestroyActor(x) for x in vehicles_list])
```


所有可用的命令都列在 Python API 参考的 [最新部分](python_api.md#command.ApplyAngularVelocity) 中.  

### Other client utilities

客户端对象的主要目的是获取或改变世界，并应用命令。 但是，它还提供对一些附加功能的访问.  

*   __Traffic manager.__ T该模块负责每辆设置为自动驾驶的车辆，以重建城市交通.  
*   __[Recorder](adv_recorder.md).__ 允许重新制定以前的模拟. 使用 [snapshots](core_world.md#world-snapshots) 总结每帧的模拟状态.  

---
## The world

模拟测试的主要部分。 它的实例应该由客户端检索。 它不包含世界本身的模型，它是[Map](core_map.md) 类的一部分. 相反，可以从此类访问大部分信息和常规设置.

*   模拟中的参与者和旁观者
*   蓝图库
*   地图
*   模拟设置
*   快照
*   天气和灯光管理器  

其中一些最重要的方法是_getters_，精确地检索这些元素的信息或实例。查看 [carla.World](python_api.md#carla.World) 以了解更多信息。


### Actors

世界有不同的方法与允许不同功能的参与者相关.  

*   生成actors（但不破坏它们）
*   让每个元素都在现场，或者特别找一个
*   访问蓝图库
*   访问旁观者视角
*   检索适合生成元素的随机位置。


Spawning 将在 [2nd.演员和蓝图](core_actors.md)。需要对蓝图库、属性等有一定的了解。.  

### Weather

 weather 本身不是一个类，而是一组可从世界访问的参数。 参数化包括太阳方向、云量、风、雾等等。 辅助类[carla.WeatherParameters](python_api.md#carla.WeatherParameters) i用于定义自定义天气.  
```py
weather = carla.WeatherParameters(
    cloudiness=80.0,
    precipitation=30.0,
    sun_altitude_angle=70.0)

world.set_weather(weather)

print(world.get_weather())
```

有一些天气预设可以直接应用于世界。 这些在 [carla.WeatherParameters](python_api.md#carla.WeatherParameters) 中列出，并可作为枚举访问.  

```py
world.set_weather(carla.WeatherParameters.WetCloudySunset)
```

天气也可以使用 CARLA 提供的两个脚本进行自定义. 

*   __`environment.py`__ *(in `PythonAPI/util`)* — 提供对天气和灯光参数的访问，以便实时更改这些参数.  

<details>
<summary> Optional arguments in <b>environment.py</b> </summary>

```sh
  -h, --help            show this help message and exit
  --host H              IP of the host server (default: 127.0.0.1)
  -p P, --port P        TCP port to listen to (default: 2000)
  --sun SUN             Sun position presets [sunset | day | night]
  --weather WEATHER     Weather condition presets [clear | overcast | rain]
  --altitude A, -alt A  Sun altitude [-90.0, 90.0]
  --azimuth A, -azm A   Sun azimuth [0.0, 360.0]
  --clouds C, -c C      Clouds amount [0.0, 100.0]
  --rain R, -r R        Rain amount [0.0, 100.0]
  --puddles Pd, -pd Pd  Puddles amount [0.0, 100.0]
  --wind W, -w W        Wind intensity [0.0, 100.0]
  --fog F, -f F         Fog intensity [0.0, 100.0]
  --fogdist Fd, -fd Fd  Fog Distance [0.0, inf)
  --wetness Wet, -wet Wet
                        Wetness intensity [0.0, 100.0]
```
</details><br>

*   __`dynamic_weather.py`__ *(in `PythonAPI/examples`)* — Enables a particular weather cycle prepared by developers for each CARLA map.  

<details>
<summary> Optional arguments in <b>dynamic_weather.py</b> </summary>

```sh
  -h, --help            show this help message and exit
  --host H              IP of the host server (default: 127.0.0.1)
  -p P, --port P        TCP port to listen to (default: 2000)
  -s FACTOR, --speed FACTOR
                        rate at which the weather changes (default: 1.0)
```
</details><br>

!!! Note
    天气的变化不会影响物理。 它们只是相机传感器可以捕捉到的视觉效果。. 

__当 sun_altitude_angle < 0 时，夜间模式开始__, 这被认为是日落。 这是灯光变得特别重要的时候.  

### Lights

 __路灯__在模拟进入夜间模式时自动打开。灯光由地图的开发人员放置，并可作为 [__carla.Light__](python_api.md#carla.Light) 对象访问。颜色和强度等属性可以随意更改。 [__carla.LightState__](python_api.md#carla.LightState) 类型的变量 __light_state__ 允许在一次调用中设置所有这些。
路灯使用其属性 __light_group__ 进行分类，类型为 [__carla.LightGroup__](python_api.md#carla.LightGroup)。这允许将灯分类为路灯、建筑灯......可以检索 [__carla.LightManager__](python_api.md#carla.LightManager) 的实例以在一次调用中处理灯组。

```py
# Get the light manager and lights
lmanager = world.get_lightmanager()
mylights = lmanager.get_all_lights()

# Custom a specific light
light01 = mylights[0]
light01.turn_on()
light01.set_intensity(100.0)
state01 = carla.LightState(200.0,red,carla.LightGroup.Building,True)
light01.set_light_state(state01)

# Custom a group of lights
my_lights = lmanager.get_light_group(carla.LightGroup.Building)
lmanager.turn_on(my_lights)
lmanager.set_color(my_lights,carla.Color(255,0,0))
lmanager.set_intensities(my_lights,list_of_intensities)
```

* __车灯__ 必须由用户打开/关闭。每辆车都有一组在 [__carla.VehicleLightState__](python_api.md#carla.VehicleLightState) 中列出的灯。到目前为止，并非所有车辆都集成了灯光。以下是撰写本文时可用的列表。
	* __Bikes.__ 它们都有前后位置灯。
	* __Motorcycles.__ 雅马哈和哈雷戴维森车型。
	* __Cars.__ 奥迪 TT、雪佛兰、道奇（警车）、Etron、林肯、野马、特斯拉 3S、大众 T2 以及来到 CARLA 的新模组.  

可以使用 [carla.Vehicle.get_light_state](python_api.md#carla.Vehicle.get_light_state) 和 [carla.Vehicle.set_light_state](#python_api.md#carla.Vehicle.set_light_state)方法随时检索和更新车辆的灯光. 这些使用二进制操作来自定义灯光设置.  

```py
# Turn on position lights
current_lights = carla.VehicleLightState.NONE
current_lights |= carla.VehicleLightState.Position
vehicle.set_light_state(current_lights)
```

!!! Note
    也可以使用[weather](#weather) section中描述的 `environment.py` 实时设置灯光 .  

### Debugging

世界对象有一个 [carla.DebugHelper](python_api.md#carla.DebugHelper) 对象作为公共属性。 它允许在模拟过程中绘制不同的形状。 这些用于跟踪正在发生的事件。 以下示例将在演员的位置和旋转处绘制一个红色框. 

```py
debug = world.debug
debug.draw_box(carla.BoundingBox(actor_snapshot.get_transform().location,carla.Vector3D(0.5,0.5,2)),actor_snapshot.get_transform().rotation, 0.05, carla.Color(255,0,0,0),0)
```

此示例在 [carla.DebugHelper](python_api.md#carla.DebugHelper.draw_box)  中的一个片段中进行了扩展，该片段显示了如何为世界快照中的每个角色绘制框. 

### World snapshots

包含模拟中每个角色在单个帧中的状态。 一种带有时间参考的世界静止图像。 信息来自相同的模拟步骤，即使在异步模式下也是如此  

```py
# Retrieve a snapshot of the world at current frame.
world_snapshot = world.get_snapshot()
```

 [carla.WorldSnapshot](python_api.md#carla.WorldSnapshot)  包含 [carla.Timestamp](python_api.md#carla.Timestamp) 和[carla.ActorSnapshot](python_api.md#carla.ActorSnapshot)列表. 可以使用演员的 `ID` 搜索演员快照。 快照列出了其中出现的演员的 `ID`.  

```py
timestamp = world_snapshot.timestamp # Get the time reference 

for actor_snapshot in world_snapshot: # Get the actor and the snapshot information
    actual_actor = world.get_actor(actor_snapshot.id)
    actor_snapshot.get_transform()
    actor_snapshot.get_velocity()
    actor_snapshot.get_angular_velocity()
    actor_snapshot.get_acceleration()  

actor_snapshot = world_snapshot.find(actual_actor.id) # Get an actor's snapshot
```

### World settings

世界可以访问一些用于模拟的高级配置。这些决定了渲染条件、模拟时间步长以及客户端和服务器之间的同步。它们可以从帮助类 [carla.WorldSettings](python_api.md#carla.WorldSettings) 中访问.  

目前，默认的 CARLA 以最佳图形质量、可变时间步长和异步运行。要进一步了解此问题，请查看 __Advanced steps__ 部分。 [同步​​和时间步长](adv_synchrony_timestep.md) 和 [渲染选项](adv_rendering_options.md) 上的页面可能是一个很好的起点.

---
这是对世界和客户对象的包装。下一步将仔细研究演员和蓝图，以赋予模拟生命。

继续阅读以了解更多信息。访问论坛发布在阅读过程中想到的任何疑问或建议.  

<div text-align: center>
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="CARLA forum">
CARLA forum</a>
</p>
</div>
<div class="build-buttons">
<p>
<a href="../core_actors" target="_blank" class="btn btn-neutral" title="2nd. Actors and blueprints">
2nd. Actors and blueprints</a>
</p>
</div>
</div>

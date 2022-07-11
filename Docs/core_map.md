# 3rd. Maps and navigation

在讨论了这个世界和它的actors员之后, 现在是时候把所有东西都放在适当的位置，了解地图以及actors如何导航它。.  

- [__The map__](#the-map)  
	- [Changing the map](#changing-the-map)  
	- [Landmarks](#landmarks)  
	- [Lanes](#lanes)  
	- [Junctions](#junctions)  
	- [Waypoints](#waypoints)  
	- [Environment Objects](#environment-objects)
- [__Navigation in CARLA__](#navigation-in-carla)  
	- [Navigating through waypoints](#navigating-through-waypoints)  
	- [Generating a map navigation](#generating-a-map-navigation)  
- [__CARLA maps__](#carla-maps)  
	- [Non-layered maps](#non-layered-maps)
	- [Layered maps](#layered-maps)

---
## The map

地图既包括城镇的 3D 模型，也包括其道路定义。 地图的道路定义基于 OpenDRIVE 文件，这是一种标准化、带注释的道路定义格式。 [OpenDRIVE 标准 1.4](http://www.opendrive.org/docs/OpenDRIVEFormatSpecRev1.4H.pdf) 定义道路、车道、交叉口等的方式决定了 Python API 的功能以及做出决策的原因.

Python API 作为一个高级查询系统来导航这些道路。 它不断发展以提供更广泛的工具集.

### Changing the map

__要改变地图，世界也必须改变__.模拟将从头开始重新创建。 您可以在新世界中使用相同的地图重新开始，也可以同时更改地图和世界:

- `reload_world()` creates a new instance of the world with the same map.
- `load_world()` changes the current map and creates a new world.

```py
world = client.load_world('Town01')
```

每张地图都有一个与当前加载的城市名称相匹配的name属性，例如 Town01。要获取可用地图的列表:

```py
print(client.get_available_maps())
```

### Landmarks

OpenDRIVE 文件中定义的交通标志被转换为 CARLA 作为可以从 API 查询的地标对象。 以下方法和类可用于操作和使用地标对象：

- __[`carla.Landmark`](https://carla.readthedocs.io/en/latest/python_api/#carla.Landmark)__ 对象代表 OpenDRIVE 信号。 此类的属性和方法描述了地标及其影响范围.
	- [`carla.LandmarkOrientation`](https://carla.readthedocs.io/en/latest/python_api/#carla.LandmarkOrientation) 说明地标相对于道路几何定义的方向.
	- [`carla.LandmarkType`](https://carla.readthedocs.io/en/latest/python_api/#carla.LandmarkType) 包含常见的地标类型，以便于转换为 OpenDRIVE 类型.
- __[`carla.Waypoint`](https://carla.readthedocs.io/en/latest/python_api/#carla.Waypoint)__ 可以获得位于其前方一定距离的地标。 可以指定要获取的地标类型.
- __[`carla.Map`](https://carla.readthedocs.io/en/latest/python_api/#carla.Map)__ 检索地标集。 它可以返回地图中的所有地标，或者具有共同 ID、类型或组的地标.
- __[`carla.World`](https://carla.readthedocs.io/en/latest/python_api/#carla.World)__ 充当地标与在模拟中代表它们的 carla.TrafficSign 和 carla.TrafficLight 之间的中介.

```py
my_waypoint.get_landmarks(200.0,True)
``` 

### Waypoints

A [`carla.Waypoint`](python_api.md#carla.Waypoint) 是 CARLA 世界中的 3D 定向点，对应于 OpenDRIVE 车道。 与航点相关的一切都发生在客户端； 只需与服务器通信一次即可获取包含航点信息的 [地图对象t](python_api.md#carlamap) .

每个航路点都包含一个 [`carla.Transform`](python_api.md#carla.Transform) 它说明了它在地图上的位置以及包含它的车道的方向。 变量  `road_id`,`section_id`,`lane_id` 和 `s` 对应于 OpenDRIVE 道路。 路点的`id`  由这四个值的哈希组合构成。

.

!!! Note
    __在同一条道路内距离小于2cm___ 的路点共享相同的 `id`。

航路点包含有关包含它的 __车道__ 的信息。 此信息包括车道的左右 __车道标记__ 、确定是否在路口内的布尔值、车道类型、宽度和车道变更权限。

```py
# Access lane information from a waypoint
inside_junction = waypoint.is_junction()
width = waypoint.lane_width
right_lm_color = waypoint.right_lane_marking.color
```

### Lanes

 [OpenDRIVE 标准 1.4 ](http://www.opendrive.org/docs/OpenDRIVEFormatSpecRev1.4H.pdf) 定义的车道类型作为一系列枚举值转换为 [`carla.LaneType`](python_api.md#carla.LaneType) 中的 API。


车道周围的车道标记可通过 [`carla.LaneMarking`](python_api.md#carla.LaneMarking). 访问。 车道标记由一系列变量定义：


- __color:__ [`carla.LaneMarkingColor`](python_api.md#carla.LaneMarkingColor) 是定义标记颜色的枚举值.
- __lane_change:__ [`carla.LaneChange`](python_api.md#carla.LaneChange)  说明车道是否允许左转、右转、两者都允许或不允许.
- __type:__ [`carla.LaneMarkingType`](python_api.md#carla.LaneMarkingType) 是根据 OpenDRIVE 标准定义标记类型的枚举值.
- __width:__ 定义标记的厚度.

下面的示例显示了获取有关特定航路点的车道类型、车道标记和车道变更权限的信息：

```py
# Get the lane type of the waypoint
lane_type = waypoint.lane_type

# Get the type of lane marking on the left.
left_lanemarking_type = waypoint.left_lane_marking.type()

# Get available lane changes for this waypoint.
lane_change = waypoint.lane_change
```

### Junctions

[`carla.Junction`](python_api.md#carla.Junction) 表示 OpenDRIVE 连接点。 此类包含一个带有边界框的交叉路口，以识别其中的车道或车辆.

`carla.Junction` 类包含 `get_waypoints` 方法，该方法为路口内的每条车道返回一对航路点。 每对都位于交汇点边界的起点和终点.

```py
waypoints_junc = my_junction.get_waypoints()
```

### Environment Objects

CARLA 地图上的每个对象都有一组相关变量，可在此处找到。 这些变量中包含一个[唯一ID][env_obj_id]，可用于[切换][toggle_env_obj]该对象在地图上的可见性。 您可以使用 Python API 根据[语义标签][semantic_tag][获取][fetch_env_obj]每个环境对象的 IDs：

		# Get the buildings in the world
	    world = client.get_world()
		env_objs = world.get_environment_objects(carla.CityObjectLabel.Buildings)

		# Access individual building IDs and save in a set
		building_01 = env_objs[0]
		building_02 = env_objs[1]
		objects_to_toggle = {building_01.id, building_02.id}

		# Toggle buildings off
		world.enable_environment_objects(objects_to_toggle, False)
		# Toggle buildings on
		world.enable_environment_objects(objects_to_toggle, True)

查看切换不同对象的示例：

![toggle_objects_gif](img/objects_small.gif)

[env_obj]: https://carla.readthedocs.io/en/latest/python_api/#carla.EnvironmentObject
[env_obj_id]: https://carla.readthedocs.io/en/latest/python_api/#carla.EnvironmentObject.id
[toggle_env_obj]: https://carla.readthedocs.io/en/latest/python_api/#carla.World.enable_environment_objects
[fetch_env_obj]: https://carla.readthedocs.io/en/latest/python_api/#carla.World.get_environment_objects
[semantic_tag]: https://carla.readthedocs.io/en/latest/python_api/#carla.CityObjectLabel


---
## Navigation in CARLA

CARLA 中的导航是通过 Waypoint API 管理的，它是 [`carla.Waypoint`](python_api.md#carla.Waypoint) 和 [`carla.Map`](python_api.md#carla.Map) 方法的组合.

客户端必须首先与服务器通信以检索包含航点信息的地图对象。 这只需要一次，所有后续查询都在客户端执行.

### Navigating through waypoints

Waypoint API 公开了一些方法，这些方法允许路点相互连接并沿着道路构建路径供车辆导航：

- `next(d)` 在 __车道的方向上__ 创建一个近似距离 `d`的航路点列表。 该列表包含每个可能偏差的一个航路点。
- `previous(d)` __在车道的相反方向上__ 创建一个近似距离 `d`的航路点列表。 该列表包含每个可能偏差的一个航路点.
- `next_until_lane_end(d)` 和`previous_until_lane_start(d)`  返回相距  `d` 的航路点列表。 这些列表分别从当前航路点到其车道的终点和起点。
-  `get_right_lane()` and `get_left_lane()` 返回相邻车道中的等效航路点（如果存在）。 可以通过在其右/左车道上找到下一个航路点并移动到该航路点来进行变道操作.

```py
# Find next waypoint 2 meters ahead.
waypoint = waypoint.next(2.0)
```

### Generating map navigation

客户端需要向服务器发出请求以获取 `.xodr` 映射文件并将其解析为 [`carla.Map`](python_api.md#carla.Map) 对象。 这只需要执行一次.

To get the map object:

```py
map = world.get_map()
```
地图对象包含用于创建车辆的 __推荐生成点__。 您可以使用以下方法获取这些生成点的列表，每个生成点都包含一个 [`carla.Transform`](python_api.md#carlatransform)。 请记住，生成点可能已经被占用，导致由于碰撞而无法创建车辆.

```py
spawn_points = world.get_map().get_spawn_points()
```
您可以通过 __[获取](python_api.md#carla.Map.get_waypoint) 最接近__ 特定位置或地图 OpenDRIVE 定义中的特定 `road_id`、`lane_id` 和 `s` 值的航点开始使用航点


```py
# Nearest waypoint in the center of a Driving or Sidewalk lane.
waypoint01 = map.get_waypoint(vehicle.get_location(),project_to_road=True, lane_type=(carla.LaneType.Driving | carla.LaneType.Sidewalk))

#Nearest waypoint but specifying OpenDRIVE parameters. 
waypoint02 = map.get_waypoint_xodr(road_id,lane_id,s)
```

T下面的例子展示了如何 __生成航点集合__ 来可视化城市车道。 这将在地图上为每条道路和车道创建航点。 它们都将相距约 2 米:

```py
waypoint_list = map.generate_waypoints(2.0)
```

要 __生成道路拓扑的最小图__，请使用以下示例。 这将返回航点对（元组）的列表。 每对中的第一个元素与第二个元素连接，并且都定义了地图中每个车道的起点和终点。 有关此方法的更多信息，请参见 [PythonAPI](python_api.md#carla.Map.get_topology).

```py
waypoint_tuple_list = map.get_topology()
```

下面的示例 以 [`carla.GeoLocation`](python_api.md#carla.GeoLocation) 的形式 __将 `carla.Transform` 转换为地理纬度和经度坐标__：

```py
my_geolocation = map.transform_to_geolocation(vehicle.transform)
```

使用以下示例将 OpenDRIVE 格式的 __保存道路信息__ 到磁盘
```py
info_map = map.to_opendrive()
```

---
## CARLA maps

CARLA 生态系统中有八个城镇，每个城镇都有两种地图，非分层地图和分层地图。 [Layers][layer_api] 指的是地图中的分组对象，包括以下内容:

- NONE(无)
- Buildings(建筑物)
- Decals(贴花)
- Foliage(叶子)
- Ground(地面)
- ParkedVehicles(停放的车辆)
- Particles(粒子)
- Props(道具)
- StreetLights(路灯)
- Walls(墙壁)
- All(全部)

[layer_api]: https://carla.readthedocs.io/en/latest/python_api/#carlamaplayer

### Non-layered maps

非分层地图如下表所示（单击城镇名称可查看布局的俯视图）。 所有图层始终存在，并且无法在这些地图中打开或关闭。 在 CARLA 0.9.11 之前，这些是唯一可用的地图类型。

!!! Note
  用户可以在CARLA中[自定义地图]（tuto_A_map_customization.md）甚至[创建新地图]（tuto_M_custom_map_overview.md）。

| Town       | Summary |
| -----------| ------  |
| **[Town01](img/Town01.jpg)** | 由“T形路口”组成的基本城镇布局.|
| **[Town02](img/Town02.jpg)** | 类似于 **Town01**，但更小.|
| **[Town03](img/Town03.jpg)** | Town03 最复杂的城镇，有 5 车道的交叉路口、环形交叉路口、凹凸不平、隧道等。|
| **[Town04](img/Town04.jpg)** | 一条高速公路和一个小镇的无限循环.|
| **[Town05](img/Town05.jpg)** | 有十字路口和桥梁的方格城镇。 每个方向有多个车道。 用于执行车道变更.  |
| **[Town06](img/Town06.jpg)** | 有许多高速公路入口和出口的长高速公路，还有一个 [**Michigan left**](<https://en.wikipedia.org/wiki/Michigan_left>). |
| **[Town07](img/Town07.jpg)** |乡村环境，道路狭窄，谷仓，几乎没有红绿灯. |
| **[Town10](img/Town10.jpg)** | 乡村环境，道路狭窄，谷仓，几乎没有红绿灯.|

### Layered maps

分层地图的布局与非分层地图的布局相同，但可以关闭和打开地图的图层。 有一个不能关闭的最小布局，由道路、人行道、交通信号灯和交通标志组成。 分层地图可以通过后缀 `_Opt` 来标识，例如 Town01_Opt。 使用这些地图，可以通过 Python API [load][load_layer]和[unload][unload_layer]图层


		# Load layered map for Town 01 with minimum layout plus buildings and parked vehicles
		world = client.load_world('Town01_Opt', carla.MapLayer.Buildings | carla.MapLayer.ParkedVehicles)

		# Toggle all buildings off
		world.unload_map_layer(carla.MapLayer.Buildings)

		# Toggle all buildings on	
		world.load_map_layer(carla.MapLayer.Buildings)

[load_layer]: https://carla-docs.readthedocs.io/zh_CN/latest/python_api/#carla.World.load_map_layer
[unload_layer]: https://carla-docs.readthedocs.io/zh_CN/latest/python_api/#carla.World.unload_map_layer

查看按顺序加载和卸载所有层的示例:

![map-layers](img/sublevels.gif)


---
这是关于 CARLA 中的地图和导航的包装。 下一步将仔细研究传感器类型以及它们检索的数据  

继续阅读以了解更多信息或访问论坛发布在阅读过程中想到的任何疑问或建议. 
<div text-align: center>
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="CARLA forum">
CARLA forum</a>
</p>
</div>
<div class="build-buttons">
<p>
<a href="../core_sensors" target="_blank" class="btn btn-neutral" title="4th. Sensors and data">
4th. Sensors and data</a>
</p>
</div>
</div>

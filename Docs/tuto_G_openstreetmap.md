# 使用OpenStreetMap生成地图
在本指南中，您将学习：
- 如何从OpenStreetMaps导出地图。
- 可以在CARLA中使用不同格式的地图以及每种格式的限制。
- 如何将原生`.osm`格式转换为`.xodr`。
- 如何在`.xodr`文件中包含交通灯信息。
- 如何在CARLA模拟中运行最终地图。
[OpenStreetMap](https://www.openstreetmap.org)是一个由成千上万的贡献者开发的世界开放数据地图，根据[Open Data Commons Open Database License](https://opendatacommons.org/licenses/odbl/)许可。地图的部分可以导出为XML格式的`.osm`文件。CARLA可以将此文件转换为OpenDRIVE格式，并使用[OpenDRIVE独立模式](#adv_opendrive.md)将其导入。
- [__从OpenStreetMap导出地图__](#从openstreetmap导出地图)
- [__在CARLA中使用OpenStreetMaps__](#在carla中使用openstreetmaps)
- [__将OpenStreetMap格式转换为OpenDRIVE格式__](#将openstreetmap格式转换为opendrive格式)
    - [Linux](#linux)
    - [Windows](#windows)
    - [生成交通灯](#生成交通灯)
- [__导入CARLA__](#导入carla)
---
## 从OpenStreetMap导出地图
本节介绍如何从Open Street Map导出您所需的地图信息：
__1.__ 访问[Open Street Map网站](https://www.openstreetmap.org)。您将看到地图视图和窗口右侧的面板，您可以在其中配置不同的地图图层、查询不同的特征、切换图例等。
__2.__ 搜索您想要的位置并放大到特定区域。
![openstreetmap_view](img/tuto_g_osm_web.jpg)
!!! 提示
    如果您想使用大区域地图，例如巴黎，您可以考虑使用CARLA的[__大型地图__功能](large_map_overview.md)。
__3.__ 在窗口左上角点击“导出”以打开“导出”面板。
__4.__ 在“导出”面板中点击“手动选择不同的区域”。
__5.__ 通过在视图中拖动方形区域的角来选择一个自定义区域。
__6.__ 在“导出”面板中点击“导出”按钮，并将选定区域的地图信息保存为`.osm`文件。
![openstreetmap_area](img/tuto_g_osm_area.jpg)
---
## 在CARLA中使用OpenStreetMaps
Open Street Map数据可以通过三种不同的方法在CARLA中使用。您使用的 方法将取决于数据是否处于原始`.osm`格式，或者您是否使用下面几节中解释的转换方法将文件转换为`.xodr`。保留文件为`.osm`是最受限制的方法，因为它不允许自定义设置。
__对于`.xodr`格式的选项：__
- 在您自己的脚本中生成地图。__此方法允许参数化。__
- 将文件作为参数传递给CARLA的`config.py`。__此方法不允许参数化。__
__对于`.osm`格式的选项：__
- 将文件作为参数传递给CARLA的`config.py`。__此方法不允许参数化。__
以下各节将详细说明上面列出的选项。
---
## 将OpenStreetMap格式转换为OpenDRIVE格式
本节演示如何使用Python API将我们在上一节导出的`.osm`文件转换为`.xodr`格式，以便在CARLA中使用。
[carla.Osm2OdrSettings](python_api.md#carla.Osm2OdrSettings)类用于配置转换设置，如偏移值、交通灯生成、原点坐标等。可配置参数的完整列表可以在Python API [文档](python_api.md#carla.Osm2OdrSettings)中找到。[carla.Osm2Odr](python_api.md#carla.Osm2Odr)类使用这些设置来解析`.osm`数据并将其输出为`.xodr`格式。
在Windows上，`.osm`文件必须编码为`UTF-8`。这在Linux上是不必要的。以下是根据您的操作系统执行文件转换的示例代码片段：
##### Linux
```py
# 读取.osm数据
f = open("path/to/osm/file", 'r')
osm_data = f.read()
f.close()
# 定义所需的设置。在这种情况下，使用默认值。
settings = carla.Osm2OdrSettings()

# 设置要导出到OpenDRIVE的OSM道路类型
settings.set_osm_way_types(["motorway", "motorway_link", "trunk", "trunk_link", "primary", "primary_link", "secondary", "secondary_link", "tertiary", "tertiary_link", "unclassified", "residential"])
# 将转换为.xodr
xodr_data = carla.Osm2Odr.convert(osm_data, settings)
# 保存opendrive文件
f = open("path/to/output/file", 'w')
f.write(xodr_data)
f.close()
```
##### Windows
```py
import io
# 读取.osm数据
f = io.open("test", mode="r", encoding="utf-8")
osm_data = f.read()
f.close()
# 定义所需的设置。在这种情况下，使用默认值。
settings = carla.Osm2OdrSettings()
# 设置要导出到OpenDRIVE的OSM道路类型
settings.set_osm_way_types(["motorway", "motorway_link", "trunk", "trunk_link", "primary", "primary_link", "secondary", "secondary_link", "tertiary", "tertiary_link", "unclassified", "residential"])
# 将转换为.xodr
xodr_data = carla.Osm2Odr.convert(osm_data, settings)
# 保存opendrive文件
f = open("path/to/output/file", 'w')
f.write(xodr_data)
f.close()
```
---
### 生成交通灯
Open Street Map数据可以定义哪些路口受交通灯控制。要使用此交通灯数据在CARLA中，您需要在将`.osm`文件转换为`.xodr`格式之前在OSM地图设置中启用它：
```py
# 定义所需的设置。在这种情况下，使用默认值。
settings = carla.Osm2OdrSettings()
# 从OSM数据生成交通灯
settings.generate_traffic_lights = True
# 转换为.xodr
xodr_data = carla.Osm2Odr.convert(osm_data, settings)
```
交通灯数据质量可能会因提取数据的地区而异。某些交通灯信息可能完全缺失。为了在这些限制内工作，您可以使用Python API配置所有路口都受交通灯控制：
```py
settings.all_junctions_with_traffic_lights = True
```
您还可以排除某些道路，例如，从生成交通灯中排除：
```
settings.set_traffic_light_excluded_way_types(["motorway_link"])
```
---
## 导入CARLA
本节解释了如何使用[OpenDRIVE独立模式](adv_opendrive.md)将您的Open Street Map信息导入CARLA。
有三种可用的选项：
[__A)__](#a-使用您自己的脚本) 使用转换后的`.xodr`文件在您自己的自定义Python脚本中生成新地图。__此方法允许参数化。__  
[__B)__](#b-将xodr传递给configpy) 将转换后的`.xodr`文件作为参数传递给CARLA的`config.py`脚本。__此方法不允许参数化。__  
[__C)__](#c-将osm传递给configpy) 将原始`.osm`文件作为参数传递给CARLA的`config.py`脚本。__此方法不允许参数化。__  
###### A) 使用您自己的脚本
生成新地图并阻塞模拟，直到它准备就绪，通过调用[`client.generate_opendrive_world()`](python_api.md#carla.Client.generate_opendrive_world)。使用[carla.OpendriveGenerationParameters](python_api.md#carla.OpendriveGenerationParameters)类配置网格生成。以下是一个示例：
```py
vertex_distance = 2.0  # 以米为单位
max_road_length = 500.0 # 以米为单位
wall_```
wall_height = 0.0      # 以米为单位
extra_width = 0.6      # 以米为单位
world = client.generate_opendrive_world(
    xodr_xml, carla.OpendriveGenerationParameters(
        vertex_distance=vertex_distance,
        max_road_length=max_road_length,
        wall_height=wall_height,
        additional_width=extra_width,
        smooth_junctions=True,
        enable_mesh_visibility=True))
```
!!! Note
    `wall_height = 0.0`强烈推荐使用。OpenStreetMap定义了双向行驶的道路为不同的道路。如果生成墙壁，这将导致墙壁重叠和意外碰撞。
###### B) 将`.xodr`传递给`config.py`
启动CARLA服务器后，在另一个终端中运行以下命令以加载您的Open Street Map：
```sh
cd PythonAPI/util
python3 config.py -x=/path/to/xodr/file
```
[默认参数](python_api.md#carla.OpendriveGenerationParameters)将被使用。
###### C) 将`.osm`传递给`config.py`
启动CARLA服务器后，在另一个终端中运行以下命令以加载您的Open Street Map：
```sh
cd PythonAPI/util
python3 config.py --osm-path=/path/to/OSM/file
```
[默认参数](python_api.md#carla.OpendriveGenerationParameters)将被使用。
无论使用哪种方法，地图都将被导入CARLA，结果应类似于以下图像：
![opendrive_meshissue](img/tuto_g_osm_carla.jpg)
<div style="text-align: right"><i>使用OpenStreetMap生成的CARLA地图的结果。</i></div>
<br>
!!! Warning
    生成的道路在地图的边界突然结束。这将导致交通管理器在车辆无法找到下一个路点时崩溃。为了避免这种情况，交通管理器的OSM模式默认设置为__True__ ([`set_osm_mode()`](python_api.md#carlatrafficmanager))。这将显示警告并在必要时销毁车辆。
---
关于此主题的任何问题和疑虑都可以在CARLA论坛上提出。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="Go to the CARLA forum">
CARLA论坛</a>
</p>
</div>


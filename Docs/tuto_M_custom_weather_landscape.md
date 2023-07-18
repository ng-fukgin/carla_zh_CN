# 自定义地图：天气和景观

CARLA提供了几个蓝图，用于帮助您创建地图的默认天气设置，并在景观中添加串行网格，如街灯、电线等。

本指南将解释这些蓝图的位置以及如何使用和配置它们。

- [自定义地图：天气和景观](#自定义地图天气和景观)
	- [天气自定义](#天气自定义)
		- [BP\_Sky](#bp_sky)
		- [BP\_Weather](#bp_weather)
	- [添加串行网格](#添加串行网格)
		- [BP\_RepSpline](#bp_repspline)
		- [BP\_Spline](#bp_spline)
		- [BP\_Wall](#bp_wall)
		- [BP\_SplinePoweLine](#bp_splinepoweline)
	- [下一步](#下一步)

!!! 重要提示
    本教程仅适用于使用源代码构建并具有访问 Unreal Editor 的用户。

---

## 天气自定义

本部分将解释如何在设置地图的默认天气之前尝试不同的天气参数，并在满意设置后如何配置地图的默认天气参数。

### BP_Sky

`BP_Sky` 蓝图是为地图提供光线和天气效果的必需组件。在决定地图的默认天气参数之前，您还可以使用它来测试不同的天气配置。

很可能在您的地图中已经加载了 `BP_Sky` 蓝图。如果没有加载，您可以将其从 `Content/Carla/Blueprints/Weather` 拖动到场景中进行添加。

要尝试不同的天气参数，请转到 `BP_Sky` actor 的 _Details_ 面板，并在 _Parameters_ 部分调整值。

!!! 重要提示
    如果场景中加载了多个 `BP_Sky` 蓝图，则天气将重复并产生不良效果，例如出现两个太阳。

### BP_Weather

地图的默认天气由 `BP_Weather` 蓝图定义。该蓝图允许您设置与 Python API 中可用的参数相同的参数。这些参数在[此处](https://carla.readthedocs.io/en/latest/python_api/#carlaweatherparameters)有描述。

要设置地图的默认天气：

__1. 打开 `BP_Weather` 蓝图。__

在 _Content Browser_ 中导航到 `Content/Carla/Blueprints/Weather`，双击打开 `BP_Weather`。

__2. 添加您的城镇。__

在 `BP_Weather` 窗口的 _Details_ 面板中，转到 _Weather_ 部分，并将您的城镇添加到 _Default Weathers_ 数组中。

__3. 配置默认天气参数。__

对于每个天气参数，设置您想要的值。完成后，点击 _Compile_，然后点击 _Save_ 并关闭窗口。

>>>>>![bp_weather_pic](../img/map_customization/BP_Weather.jpg)<br>
<div style="text-align: right">
<i>包含每个CARLA地图默认天气参数的数组。打开了Town01。</i></div>
<br>

---

## 添加串行网格

有四个蓝图可用于添加沿贝塞尔曲线对齐的道具，例如墙壁、电线、街灯等。这些蓝图使用一系列分布在贝塞尔曲线上的网格。每个蓝图的初始化方式相同：

__1. 初始化系列。__

将蓝图拖动到场景中。您将在贝塞尔曲线的起点处看到一个元素，两个节点标记开始和结束。

__2. 定义路径。__

选择元素的方向箭头，并按住 __Alt__ 键拖动元素到您希望的方向。这将创建一个新元素，用于定义曲线。当您拖动时，新的网格将出现在曲线的每个节点上，或者每次按住 `Alt` 键拖动时，取决于蓝图。

__3. 自定义模式。__

以下部分将介绍每个蓝图可用的不同自定义参数。

### BP_RepSpline

`BP_RepSpline` 蓝图位于 `Carla/Blueprints/LevelDesign`。它用于沿由贝塞尔曲线定义的路径添加 __单独的__ 元素。

可以通过以下值来自定义序列化：

- _Distance between_ — 设置元素之间的距离。
- _Offset rotation_ — 为不同的轴设置固定旋转。
- _Random rotation_ — 为不同的轴设置随机旋转范围。
- _Offset translation_ — 设置沿不同轴的随机位置范围。
- _Max Number of Meshes_ — 设置在曲线节点之间放置的元素的最大数量。
- _World aligned ZY_ — 如果选择，元素将垂直对齐于世界坐标系。
- _EndPoint_ — 如果选择，将在曲线的结束节点添加一个元素。
- _Collision enabled_ — 设置启用网格碰撞的类型。

![bp_repspline_pic](../img/map_customization/BP_Repspline.jpg)
<div style="text-align: right"><i>BP_RepSpline 示例。</i></div>

### BP_Spline

`BP_Spline` 蓝图位于 `Carla/Blueprints/LevelDesign`。它添加沿贝塞尔曲线严格遵循路径的 __连接__ 元素。网格将被弯曲以适应创建的路径。

可以使用以下值来自定义蓝图：

- _Gap distance_ — 在元素之间添加间隔。

![bp_spline_pic](../img/map_customization/BP_Spline.jpg)
<div style="text-align: right"><i>BP_Spline 示例。</i></div>

### BP_Wall

`BP_Wall` 蓝图位于 `Carla/Blueprints/LevelDesign`。它沿贝塞尔曲线添加 __连接__ 元素。网格不会被弯曲以适应曲线，但会尊重节点。

- _Distance between_ — 设置元素之间的距离。
- _Vertically aligned_ — 如果选择，元素将垂直对齐于世界坐标系。
- _Scale offset_ — 缩放网格的长度，以使元素之间的连接更圆滑。

![bp_wall_pic](../img/map_customization/BP_Wall.jpg)
<div style="text-align: right"><i>BP_Wall 示例。</i></div>

### BP_SplinePoweLine

__BP_SplinePoweLine__ 蓝图位于 `Carla/Static/Pole/PoweLine`。它沿贝塞尔曲线添加 __电力杆__，并通过电线将它们连接起来。

为了增加多样性，您可以向蓝图提供一个电力线网格数组以填充路径。操作步骤如下：

1. 在 _Content Browser_ 中双击 __BP_SplinePoweLine__ 蓝图。
2. 在 _Details_ 面板中，转到 _Default_ 部分。
3. 展开 _Array Meshes_，根据需要进行添加或更改。
4. 点击 _Compile_，然后保存并关闭窗口。

![bp_splinepowerline_pic](../img/map_customization/BP_Splinepowerline.jpg)
<div style="text-align: right"><i>BP_SplinePowerLine 示例。</i></div>

要更改电力线的线张力：

1. 在编辑器场景中选择蓝图 actor，并转到 _Details_ 面板。
2. 转到 _Default_ 部分。
3. 在 _Tension_ 中调整值。`0` 表示线条将保持直线。

要增加电线的数量：

1. 在 _Content Browser_ 中，双击其中一个杆网格。
2. 转到 _Socket Manager_ 面板。
3. 通过点击 _Create Socket_ 来配置现有的 socket 或添加新的 socket。Socket 是代表电力线连接点的空网格。电线是从杆之间的 socket 到 socket 之间创建的。

![bp_powerline_socket_pic](../img/map_customization/BP_Splinepowerline_Sockets.jpg)
<div style="text-align: right"><i>BP_SplinePowerLine 的 socket 可视化。</i></div>
<br>

!!! 重要提示
    杆之间的 socket 数量及其名称应保持一致。否则，可能会出现可视化问题。

---

## 下一步

继续使用以下工具和指南自定义您的地图：

- [在地图中实现子级别。](tuto_M_custom_layers.md)
- [添加和配置交通灯和标志。](tuto_M_custom_add_tl.md)
- [使用程序化建筑工具添加建筑物。](tuto_M_custom_buildings.md)
- [使用道路绘制工具自定义道路。](tuto_M_custom_road_painter.md)
- [使用串行网格自定义景观。](tuto_M_custom_weather_landscape.md#add-serial-meshes)

完成自定义后，您可以[生成行人导航信息](tuto_M_generate_pedestrian_navigation.md)。

---

如果您对此过程有任何问题，可以在[论坛](https://github.com/carla-simulator/carla/discussions)中提问。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA 论坛</a>
</p>
</div>

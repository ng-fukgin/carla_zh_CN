# 添加一个新地图

CARLA的用户可以创建自定义地图并使用它们来进行仿真。 在CARLA中导入自定义地图有多种方法。 要使用的方法取决于你使用的是CARLA的打包版本还是从源代码构建的版本。 本节概述了开始该过程所需的内容、导入时可开启的选项、可用的定制工具和行人导航工具。

- [__概述__](#overview)
- [__生成__](#generation)
- [__导入__](#importation)
- [__定制__](#customization)
- [__生成行人导航__](#generate-pedestrian-navigation)

---

## overview概述

在Carla中使用自定义地图有以下4个过程：

1. 生成
2. 导入
3. 定制
4. 生成行人导航

请继续阅读以了解每个过程的详细内容

---

## generation生成

CARLA需要`.fbx`格式的地图几何信息和`.xodr`格式的OpenDRIVE信息。 当前推荐使用RoadRunner软件生成这些文件。

__[此处](tuto_M_generate_map.md) 将指导你如何使用RoadRunner生成地图信息。__

---

## importation导入

有几种方法可以将你的地图导入CARLA。

如果你使用的是CARLA的打包版本，你需要使用Docker导入地图。 此方法仅在Linux下可用，并且你将不能在Unreal Editor中编辑你的地图。 __更多详情请查看[此处](tuto_M_add_map_package.md)。__

如果你使用的是CARLA的源代码构建版本，则可以使用以下三种方法来导入你的地图：

1. 使用`make import`命令自动导入（推荐）。__更多详情请查看[此处](tuto_M_add_map_source.md)。__
2. 使用RoadRunner插件，__更多详情请查看[此处](tuto_M_add_map_alternative.md#roadrunner-plugin-import)。__
3. 手动导入地图到Unreal Engine中。__更多详情请查看[此处](tuto_M_add_map_alternative.md#manual-import)。__

以下视频介绍了一些可用于将地图导入CARLA的方法：

<iframe width="560" height="315" src="https://www.youtube.com/embed/mHiUUZ4xC9o" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br>

---

## customization定制

除了将准备的好的静态网格添加到景观外，CARLA还提供了多种工具和指南来帮助你自定义地图：

- __添加子级别:__ 子级别可以使得多人在同一张地图上协同工作. 它们还允许你使用Python API切换地图的图层, 就像CARLA的分层地图一样。 __更多详情请查看[此处](tuto_M_custom_layers.md)。__
- __设置默认天气:__ 尝试不同的天气预设，并找到你认为合适的天气。将其设置为地图的默认天气。 __更多详情请查看[此处](tuto_M_custom_weather_landscape.md#weather-customization)。__
- __填充景观__：使用蓝图填充具有重复网格的景观，例如路灯、电源线和墙壁。__更多详情请查看[此处](tuto_M_custom_weather_landscape.md#add-serial-meshes)。__
- __绘制道路__：使用混合不同纹理的主材质绘制道路。添加贴花和网格，例如落叶、裂缝或沙井。__更多详情请查看[此处](tuto_M_custom_road_painter.md)。__
- __添加程序性建筑__：使用程序性建筑蓝图添加具有自定义大小、楼层数量和可变网格组合的建筑物。__更多详情请查看[此处](tuto_M_custom_buildings.md)。__
- __添加交通灯和标志__：添加交通灯和标志并配置其影响范围。在路口分组红绿灯。__更多详情请查看[此处](tuto_M_custom_add_tl.md)。__

---

## generate-pedestrian-navigation生成行人导航

你需要使用CARLA提供的工具生成行人导航信息，并且需要在定制完地图之后，以免在行人导航的路径上存在障碍物。 __更多详情请查看[此处](tuto_M_generate_pedestrian_navigation.md).__

---

如果您对上述内容有任何疑问，请随时在[论坛](https://github.com/carla-simulator/carla/discussions)中反馈。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>

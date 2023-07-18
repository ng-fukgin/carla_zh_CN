# 自定义地图：程序化建筑物

- [自定义地图：程序化建筑物](#自定义地图程序化建筑物)
	- [程序化建筑物](#程序化建筑物)
		- [建筑物结构](#建筑物结构)
		- [结构修改](#结构修改)
	- [下一步](#下一步)

---

## 程序化建筑物

程序化建筑工具允许您创建由不同层组成的矩形建筑物。每个层都使用可配置的网格数组或单个蓝图构建。如果使用网格数组，则每个网格将在层中随机重复以提供多样性。网格只会创建一次，每次重复都将是该网格的实例。这提高了地图的性能。

### 建筑物结构

开始创建建筑物：

1. 在Unreal Engine编辑器的_Content Browser_中，导航到 `Content/Carla/Blueprints/LevelDesign`。
2. 将 `BP_Procedural_Building` 拖动到场景中。

在 _Details_ 面板中，您将看到所有可用的选项以自定义建筑物。每次在此处进行更改时，建筑物将从场景视图中消失，因为关键网格会更新。点击 _Create Building_ 查看新结果，或者启用 _Create automatically_ 以避免重复此步骤。

关键网格是建筑物的结构组成部分。它们分为四个类别：

- __Base:__ 底层。
- __Body:__ 中间层。
- __Top:__ 最高层。
- __Roof:__ 覆盖顶层的屋顶。

除了 __Roof__ 外的每个部分都有一个网格来填充楼层的中心位置，以及一个放置在楼层两侧的 __Corner__ 网格。下图表示全局结构。

![bp_procedural_building_visual](../img/map_customization/BP_Procedural_Building_Visual.jpg)
<div style="text-align: right"><i>建筑结构可视化。</i></div>

__Base参数__ 设置了尺寸。

- __Num Floors:__ 建筑物的楼层数。__Body__ 网格的重复次数。
- __Length X和Length Y:__ 建筑物的长度和宽度。每侧建筑物的中心网格的重复次数。

![bp_procedural_building_full](../img/map_customization/BP_Procedural_Building_Full.jpg)
<div style="text-align: right"><i>BP_Procedural_Building的示例。</i></div>

### 结构修改

还有一些其他选项可用于修改建筑物的总体结构。

- __Disable corners:__ 如果选择了此选项，将不使用任何角落网格。
- __Use full blocks:__ 如果选择了此选项，建筑物的结构将每层仅使用一个网格。每层中不会出现角落或重复。
- __Doors:__ 出现在底层中心网格前方的网格。可以设置门的数量和位置。`0` 是初始位置，`1` 是下一个底层重复，依此类推。
- __Walls:__ 替代建筑物的一侧或多侧的网格。例如，可以使用一个平面网格来绘制建筑物的一侧。

![bp_procedural_building_extras](../img/map_customization/BP_Procedural_Building_Extras.jpg)
<div style="text-align: right"><i>左侧是没有角落和一个门的建筑物。<br>右侧是应用于建筑物一侧的墙壁。墙壁是没有防火梯的纹理。</i></div>

---

## 下一步

使用以下工具和指南继续自定义地图：

- [在地图中实现子级别。](tuto_M_custom_layers.md)
- [添加和配置交通灯和路标。](tuto_M_custom_add_tl.md)
- [使用道路绘制工具自定义道路。](tuto_M_custom_road_painter.md)
- [自定义天气](tuto_M_custom_weather_landscape.md#weather-customization)
- [使用序列网格自定义景观。](tuto_M_custom_weather_landscape.md#add-serial-meshes)

完成自定义后，您可以[生成行人导航信息](tuto_M_generate_pedestrian_navigation.md)。

---

如果您对此过程有任何疑问，可以在[论坛](https://github.com/carla-simulator/carla/discussions)上提问。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>

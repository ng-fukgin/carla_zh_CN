# 自定义地图：道路绘制工具

本指南将解释什么是道路绘制工具，如何使用它通过组合不同的纹理来自定义道路的外观，如何添加贴花和网格，并如何根据道路纹理更新车道标记的外观。

- [自定义地图：道路绘制工具](#自定义地图道路绘制工具)
  - [什么是道路绘制工具？](#什么是道路绘制工具)
  - [开始之前](#开始之前)
  - [建立道路绘制工具、主材质和渲染目标](#建立道路绘制工具主材质和渲染目标)
  - [准备主材质](#准备主材质)
    - [绘制道路](#绘制道路)
  - [更新车道标记的外观](#更新车道标记的外观)
  - [下一步](#下一步)

---

## 什么是道路绘制工具？

道路绘制工具是一种蓝图，使用 OpenDRIVE 信息来快速绘制道路。它采用主材质并将其应用于道路的渲染目标，用作画布。主材质由一组材质组成，可以使用画刷进行混合，并作为蒙版应用。无需应用光度技术或考虑几何体的 UV。

---

## 开始之前

道路绘制工具使用 OpenDRIVE 信息来绘制道路。确保您的 `.xodr` 文件与地图具有相同的名称，以确保正确工作。

---

## 建立道路绘制工具、主材质和渲染目标

__1. 创建 `RoadPainter` actor。__

1. 在 _Content Browser_ 中导航到 `Content/Carla/Blueprints/LevelDesign`。
2. 将 `RoadPainter` 拖动到场景中。

__2. 创建渲染目标。__

1. 在 _Content Browser_ 中导航到 `Content/Carla/Blueprints/LevelDesign/RoadPainterAssets`。
2. 右键单击 `RenderTarget` 文件，选择 `Duplicate`。
3. 重命名为 `Tutorial_RenderTarget`。

__3. 创建主材质实例。__

1. 在 _Content Browser_ 中导航到 `Game/Carla/Static/GenericMaterials/RoadPainterMaterials`。
2. 右键单击 `M_RoadMaster`，选择 _Create Material Instance_。
3. 重命名为 `Tutorial_RoadMaster`。

__4. 重新校准 _地图尺寸（Cm）_，使其等于实际地图的大小。__

1. 选择场景中的 `RoadPainter` actor。
2. 转到 _Details_ 面板，点击 _Z-Size_ 按钮。您将看到 _地图尺寸（Cm）_ 的值发生变化。

>>>>>![map size](../img/map_size.png)

__5. 在 `RoadPainter` 和 `Tutorial_RoadMaster` 之间同步地图尺寸。__

1. 在 _Content Browser_ 中打开 `Tutorial_RoadMaster`。
2. 复制前面步骤中的 _地图尺寸（Cm）_ 的值，然后粘贴到 `Tutorial_RoadMaster` 窗口的 _Global Scalar Parameter Values -> Map units (CM)_ 中。
3. 点击 _保存_。

>>>>>>![img](../img/map_size_sync.png)

__6. 创建道路绘制工具与主材质之间的通信链接。__

`Tutorial_RenderTarget` 将作为道路绘制工具和 `Tutorial_RoadMaster` 之间的通信链接。

1. 在 `Tutorial_RoadMaster` 窗口中，将 `Tutorial_RenderTarget` 应用于 _Global Texture Parameter Values -> Texture Mask_。
2. 保存并关闭。
3. 在主编辑器窗口中，选择道路绘制工具 actor，转到 _Details_ 面板，并将 `Tutorial_RenderTarget` 应用于 _Paint -> Render Target_。

---

## 准备主材质

您创建的 `Tutorial_RoadMaster` 材质保存了基础材质、额外的材质信息和通过 `Tutorial_RenderTarget` 应用的参数。您可以配置一个基础材质和最多三个额外的材质。

>>![master materials](../img/master_material.png)

要配置材质，双击打开 `Tutorial_RoadMaster` 文件。在弹出的窗口中，可以选择并调整每个材质的以下值，以满足您的需求：

- 亮度
- 色调
- 饱和度
- AO 强度
- 法线贴图强度
- 粗糙度对比度
- 粗糙度强度

您可以通过选择以下值并在搜索框中搜索纹理来更改每个材质的纹理：

- 漫反射
- 法线
- ORMH

您可以在 `Game/Carla/Static/GenericMaterials/Asphalt/Textures` 中探索一些可用的 CARLA 纹理。

---

### 绘制道路

__1. 建立道路绘制工具与道路之间的链接。__

1. 在主编辑器窗口中，使用 _World Outliner_ 搜索框搜索 `Road_Road`。
2. 按下 `Ctrl + A` 选择所有道路。
3. 在 _Details_ 面板中，转到 _Materials_ 部分，并将 `Tutorial_RoadMaster` 应用于 _Element 0_、_Element 1_、_Element 2_ 和 _Element 3_。

__2. 选择要自定义的材质。__

我们向 `Tutorial_RoadMaster` 添加的每个材质将分别应用于道路，并可以使用 _Brush_ 工具进行调整。要应用和自定义材质：

1. 选择道路绘制工具 actor。
2. 在 _Details_ 面板中，从 _Mask Color_ 下拉菜单中选择要使用的材质。

>>>>>>![choose material](../img/choose_material.png)

__3. 设置画刷和模板参数。__

在 `GenericMaterials/RoadStencil/Alphas` 中有各种模板可供选择。模板用于根据需要绘制道路，并可以使用以下值进行调整：

- _Stencil size_ — 画刷的大小。
- _Brush strength_ — 轮廓的粗糙程度。
- _Spacebeween Brushes_ — 笔触之间的距离。
- _Max Jitter_ — 笔触之间的大小变化。
- _Stencil_ — 要使用的笔刷。
- _Rotation_ — 应用于笔刷的旋转。

>>>>>>![materials_roadpaint_brushes](../img/material_customization/Materials_Brush.jpg)
<div style="text-align: right"><i>画刷面板。</i></div>
<br>
![materials_roadpaint_typesofbrushes](../img/material_customization/Materials_Road_Typesofbrushes.jpg)
<div style="text-align: right"><i>不同类型的画刷。</i></div>
<br>
__4. 将每个材质应用于道路的相应部分。__

通过 _Details_ 面板上的 _Default_ 部分的按钮选择要应用所选材质的位置：

* _Paint all roads_ — 绘制所有道路。
* _Paint by actor_ — 绘制特定选定 actor。
* _Paint over circle_ — 使用圆形模式进行绘制，有助于提供变化。
* _Paint over square_ — 使用方形模式进行绘制，有助于提供变化。

该部分还包含擦除应用的更改的选项。

* _Clear all_ — 清除所有绘制的材质。
* _Clear materials_ — 删除当前激活的材质。
* _Clear material by actor_ — 删除最接近所选 actor 的材质。

>>>>>>![materials_roadpaint_brushes](../img/material_customization/Materials_RoadPainter_Default.jpg)
<div style="text-align: right"><i>不同的绘制和擦除选项。</i></div>
<br>
__5. 添加贴花和网格。__

您可以在 `Content/Carla/Static/Decals` 和 `Content/Carla/Static` 中探索可用的贴花和网格，并通过扩展和添加到 _Decals Spawn_ 和 _Meshes Spawn_ 数组中将它们添加到道路绘制工具中。对于每个元素，您可以配置以下参数：

- _Number of Decals/Meshes_ - 要绘制的每种贴花或网格的数量。
- _Decal/Mesh Scale_ — 贴花/网格的缩放比例。
- _Fixed Decal/Mesh Offset_ — 与车道中心的偏差。
- _Random Offset_ — 距离车道中心的最大偏差。
- _Decal/Mesh Random Yaw_ — 最大随机偏航角度。
- _Decal/Mesh Min Scale_ — 应用于贴花/网格的最小随机缩放比例。
- _Decal/Mesh Max Scale_ — 应用于贴花/网格的最大随机缩放比例。

>>>>>>![materials_](../img/decals_meshes.png)
<div style="text-align: right"><i>贴花和网格面板。</i></div>
<br>

配置完贴花和网格后，通过点击 `Spawn decals` 和 `Spawn meshes` 来生成它们。

!!! Note
    确保网格和贴花没有启用可能干扰道路上车辆的碰撞，并将任何边界框缩小到道路的水平。

__7. 尝试以获得您期望的外观。__ 

尝试使用不同的材质、纹理、设置、贴花和网格来获得您期望的外观。下面是一些示例图片，展示了在绘制每种材质时道路外观的变化过程。

![materials_roadpaint_mat00](../img/material_customization/Materials_Road_MaterialBase.jpg)
<div style="text-align: right"><i>基础道路材质的示例。</i></div>
<br>
![materials_roadpaint_mat01](../img/material_customization/Materials_Road_Material1.jpg)
<div style="text-align: right"><i>应用材质 1 后的示例。</i></div>
<br>
![materials_roadpaint_mat02](../img/material_customization/Materials_Road_Material2.jpg)
<div style="text-align: right"><i>应用材质 2 后的示例。</i></div>
<br>
![materials_roadpaint_mat03](../img/material_customization/Materials_Road_Material3.jpg)
<div style="text-align: right"><i>应用材质 3 后的示例。</i></div>
<br>
![materials_roadpaint_mat03](../img/material_customization/Materials_Road_Decals.jpg)
<div style="text-align: right"><i>应用贴花后的示例。</i></div>
<br>
![materials_roadpaint_mat03](../img/material_customization/Materials_Road_Meshes.jpg)
<div style="text-align: right"><i>应用网格后的示例。</i></div>
<br>

---

## 更新车道标记的外观

在绘制完道路后，可以根据以下步骤更新车道标记的外观：

__1. 复制主材质。__

1. 在 _Content Browser_ 中导航到 `Game/Carla/Static/GenericMaterials/RoadPainterMaterials`。
2. 右键单击 `Tutorial_RoadMaster`，选择 _Create Material Instance_。
3. 重命名为 `Tutorial_LaneMarkings`。

__2. 配置车道标记材质。__

1. 在 _Content Browser_ 中双击打开 `Tutorial_LaneMarkings`。
2. 在 _Details_ 面板中，转到 _Global Static Switch Parameter Values_ 部分，勾选 _LaneMark_ 左右两侧的复选框。
3. 转到 _Texture_ 部分，勾选 _LaneColor_ 和 _Uv Size_ 的复选框。
4. 在 _LaneColor_ 中选择您喜欢的车道标记颜色。
5. _保存_ 并关闭。

__3. 选择道路标记的网格。__

将材质拖放到要着色的车道标记上。如果需要不同颜色的车道标记，重复整个过程。

---

## 下一步

继续使用以下工具和指南自定义您的地图：

- [在地图中实施子层级。](tuto_M_custom_layers.md)
- [添加和配置交通信号灯和标志。](tuto_M_custom_add_tl.md)
- [使用程序化建筑工具添加建筑物。](tuto_M_custom_buildings.md)
- [自定义天气。](tuto_M_custom_weather_landscape.md#weather-customization)
- [使用串行网格自定义景观。](tuto_M_custom_weather_landscape.md#add-serial-meshes)

完成自定义后，您可以[生成行人导航信息](tuto_M_generate_pedestrian_navigation.md)。

---

如果对该过程有任何问题，请在[论坛](https://github.com/carla-simulator/carla/discussions)中提问。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="进入 CARLA 论坛">
CARLA 论坛</a>
</p>
</div>

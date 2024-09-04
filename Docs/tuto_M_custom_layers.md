# 自定义地图：分层地图

在自定义地图中使用级别，可以让多个人同时在同一张地图上工作。它还允许您在模拟期间使用Python API加载和卸载地图的层，就像[分层CARLA地图](core_map.md#layered-maps)一样。

本指南将解释如何添加新的级别，如何向级别添加资源，并如何配置级别是否始终加载。

- [__添加新的级别__](#添加新的级别)
- [__向级别添加资源__](#向级别添加资源)
- [__配置级别加载选项__](#配置级别加载选项)
- [__下一步__](#下一步)

---

## 添加新的级别

所有新的级别都将嵌套在父级别中，即 _Persistent Level_。要创建新的级别：

__1. 打开级别面板。__

1. 在Unreal Engine编辑器中，打开菜单栏的 _Window_。
2. 单击 _Levels_。

__2. 创建一个新的级别。__

1. 在 _Levels_ 面板中，单击 _Levels_，然后选择 _Create New..._。
2. 选择 _Empty Level_。
3. 将级别保存在 `Content/Carla/Maps/Sublevels/<map_name>/` 目录下。为了与CARLA Python API集成，使用命名约定 `<map_name>_<layer_name>`，例如 `TutorialMap_Buildings`。可用的层列表，请参阅[这里](core_map.md#carla-maps)。

>>>>>>![创建新级别](../img/new_level.png)

---

## 向级别添加资源

__1. 选择要向其添加资源的级别__。

在 _Levels_ 面板中，双击要向其添加资源的级别。确保通过切换锁定图标将级别解锁。

__2. 选择要添加的资源。__

1. 选择要添加到级别的所有资源。
2. 右键单击，然后选择 _Level_。
3. 单击 _Move Selection to Current Level_。

__3. 保存级别。__

如果级别有未保存的更改，您将在 _Levels_ 面板中的级别旁边看到一个铅笔图标。点击它来保存更改。

![移动资源](../img/move_assets.png)

---

## 配置级别加载选项

级别可以配置为可切换或始终加载。要配置级别的选项：

1. 右键单击 _Levels_ 面板中的级别，然后选择 _Change Streaming Method_。
2. 选择所需的设置：
    1. _Always Loaded_：级别 __将无法__ 通过Python API切换。
    2. _Blueprint_：级别 __将能够__ 通过Python API切换。级别名称旁边会出现蓝点。

无论设置如何，您仍然可以通过按眼睛图标来在编辑器中切换级别。

>>>>>>![级别](../img/levels.png)

---

## 下一步

使用以下工具和指南继续自定义地图：

- [添加和配置交通灯和路标。](tuto_M_custom_add_tl.md)
- [使用程序化建筑工具添加建筑物。](tuto_M_custom_buildings.md)
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

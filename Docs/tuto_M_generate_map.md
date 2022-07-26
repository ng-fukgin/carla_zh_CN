# 在RoadRunner中生成地图

RoadRunner是CARLA官方推荐的地图导入软件。本节概述了RoadRunner是什么、构建地图时要考虑的事项以及如何导入自定义地图到CARLA中。

- [__RoadRunner介绍__](#introduction-to-roadrunner)
- [__在开始之前__](#before-you-start)
- [__在RoadRunner中创建地图__](#roadrunner_1)
- [__在RoadRunner中导出地图__](#roadrunner_2)
- [__下一步__](#next-steps)
---
## introduction-to-RoadRunner介绍

RoadRunner是一款可以让用户设计3D场景以用于自动驾驶系统的仿真和测试的交互式编辑软件。它可用于创建道路布局，附带OpenDrive和几何信息。在[此处][rr_home]了解有关RoadRunner的更多信息。

RoadRunner是MATLAB Campus-Wide Licenses的一部分，因此许多大学可以提供无限制的学术性使用。在[这里][rr_eligibility]查看你的大学是否可以使用。有关许可性问题，请联系automated-driving@mathworks.com。这里有一个[试用版][rr_trial_version]可用。

参加CARLA排行榜的每个人都可以获得RoadRunner的许可证。点击[这里][rr_leaderboard]了解更多信息。

[rr_home]: https://www.mathworks.com/products/roadrunner.html
[rr_trial_version]: https://www.mathworks.com/products/roadrunner.html
[rr_eligibility]: https://www.mathworks.com/academia/tah-support-program/eligibility.html
[rr_leaderboard]: https://www.mathworks.com/academia/student-competitions/carla-autonomous-driving-challenge.html

---
## before-you-start在开始之前

你需要安装RoadRunner。你可以在Mathworks的网站上找到[安装向导][rr_docs]

[rr_docs]: https://www.mathworks.com/help/roadrunner/ug/install-and-activate-roadrunner.html

---

## 在RoadRunner中创建地图

如何在RoadRunner中构建地图超出了本节的范围，你可以在[RoadRunner文档][rr_tutorials]中查看视频教程。

__请记住，带有大量道具的地图会显著减慢导入过程。__ 这是因为虚幻引擎需要将每个网格转换为虚幻资源。 如果你打算将地图导入到CARLA的源代码构建版本中，我们强烈建议你仅在RoadRunner中创建道路布局，并在地图导入虚幻引擎之前保留自定义的内容。 CARLA提供了多种工具，你可以在虚幻引擎编辑器中使用这些工具来简化定制过程。

---

## 在RoadRunner中导出地图

[rr_tutorials]: https://www.mathworks.com/support/search.html?fq=asset_type_name:video%20category:roadrunner/index&page=1&s_tid=CRUX_topnav

以下是从RoadRunner导出自定义地图的基本指南。 你可以在[MathWorks的文档][exportlink]中找到有关如何导出地图到CARLA的更多详细信息。

在RoadRunner中制作好地图后，你就可以将其导出。 请注意，__道路布局在导出后无法修改__。 在导出之前，请确保：

[exportlink]: https://www.mathworks.com/help/roadrunner/ug/Exporting-to-CARLA.html

- 地图以(0,0)为中心，以确保地图可以在虚幻引擎中正确显示。
- 地图正确定义。
- 地图验证无误，密切关注道路连接处和道路几何形状。

>>>>![CheckGeometry](../img/check_geometry.jpg)

地图准备好后，点击`OpenDRIVE Preview Tool`按钮以预览OpenDRIVE道路网格并做最后的检查。

>>>>![checkopen](../img/check_open.jpg)

!!! note
    _OpenDrive Preview Tool_ 可以更轻松地检查地图的完整性。 如果路口有问题，点击`Maneuver Tool`和`Rebuild Maneuver Roads`。

当你准备好导出时：

__1.__ 导出场景时选择CARLA选项：

  - 在主工具栏中，选择`File` -> `Export` -> `CARLA (.fbx, .xodr, .rrdata.xml)`

__2.__ 在弹出的窗口中：
  - 选中以下选项：
     - _Split by Segmentation_：按语义分割划分网格。
     - _Power of Two Texture Dimensions_：用于提高性能。
     - _Embed Textures_：确保纹理嵌入到网格中。
     - _Export to Tiles_：选择瓦片的大小（只有一块时不选中此选项）。
  - 不选中以下选项:
    - _Export Individual Tiles_: 仅生成包含所有地图块的`.fbx`文件

>>>>![roadrunner_export](../img/roadrunner_export.png)

__3.__ 选择要导出文件的目录，然后单击导出。 这将生成`<mapName>.fbx`和`<mapName>.xodr`文件。
  
!!! Warning
    请确保`.xodr`和`.fbx`文件名相同

---

## 下一步

现在已准备好将地图导入CARLA，下一步将取决于你的CARLA安装版本：

* __对于从源代码构建的CARLA用户__，请参照[此指南](tuto_M_add_map_source.md)。
* __对于CARLA的打包（二进制）版本的用户__，请参照[此指南](tuto_M_add_map_package.md)。

---

如果您对上述内容有任何疑问，请随时在[论坛](https://github.com/carla-simulator/carla/discussions)中反馈。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>

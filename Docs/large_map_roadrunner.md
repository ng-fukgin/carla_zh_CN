# 在RoadRunner中创建大型地图
RoadRunner是创建用于导入CARLA的大型地图的推荐软件。本指南概述了RoadRunner是什么，构建大型地图时需要考虑的事项，以及如何导出准备导入CARLA的自定义大型地图。
- [__RoadRunner简介__](#introduction-to-roadrunner)
- [__开始之前__](#before-you-start)
- [__在RoadRunner中构建大型地图__](#build-a-large-map-in-roadrunner)
- [__在RoadRunner中导出大型地图__](#export-a-large-map-in-roadrunner)
- [__下一步__](#next-steps)
---
## RoadRunner简介
RoadRunner是一个交互式编辑器，允许您设计用于模拟和测试自动驾驶系统的3D场景。它可以用来创建道路布局和相关OpenDRIVE和几何信息。有关RoadRunner的更多信息，请[点击这里](https://www.mathworks.com/products/roadrunner.html)。
RoadRunner是MATLAB校园范围许可证的一部分，因此许多大学可以提供无限制的学术访问。[检查](https://www.mathworks.com/academia/tah-support-program/eligibility.html)您的大学是否有访问权限。如果您有任何关于访问权限的问题或困难，请联系[automated-driving@mathworks.com](mailto:automated-driving@mathworks.com)。还有一个[试用版本](https://www.mathworks.com/products/roadrunner.html)可供使用。
RoadRunner的许可证也提供给参与CARLA排行榜的每个人。点击[这里](https://www.mathworks.com/academia/student-competitions/carla-autonomous-driving-challenge.html)获取更多信息。
[rr_home]: https://www.mathworks.com/products/roadrunner.html
[rr_trial_version]: https://www.mathworks.com/products/roadrunner.html
[rr_eligibility]: https://www.mathworks.com/academia/tah-support-program/eligibility.html
[rr_leaderboard]: https://www.mathworks.com/academia/student-competitions/carla-autonomous-driving-challenge.html
---
## 开始之前
您需要安装RoadRunner。您可以遵循Mathworks网站上的[安装指南](https://www.mathworks.com/help/roadrunner/ug/install-and-activate-roadrunner.html)。
[rr_docs]: https://www.mathworks.com/help/roadrunner/ug/install-and-activate-roadrunner.html
---
## 在RoadRunner中构建大型地图
在RoadRunner中构建大型地图的详细信息超出了本指南的范围，但是，RoadRunner文档中有视频教程可用。
如果您正在构建具有高度差的大型地图，建议的最大地图尺寸为20km x 20km。大于此的地图在导出时可能会导致RoadRunner崩溃。
[rr_tutorials]: https://www.mathworks.com/support/search.html?fq=asset_type_name:video%20category:roadrunner/index&page=1&s_tid=CRUX_topnav
---
## 在RoadRunner中导出大型地图
以下是导出自定义大型地图的基本指南。
[exportlink]: https://www.mathworks.com/help/roadrunner/ug/Exporting-to-CARLA.html
在RoadRunner中制作地图后，您可以导出它。在导出之前，请确保：
- 地图以(0,0)为中心，以确保在Unreal Engine中正确可视化地图。
- 地图定义正确。
- 地图验证正确，特别注意连接和几何形状。
>>>>![CheckGeometry](../img/check_geometry.jpg)
一旦地图准备就绪，点击`OpenDRIVE预览工具`按钮来可视化OpenDRIVE道路网络，并最后一次检查所有内容。
>>>>![checkopen](../img/check_open.jpg)
!!! note
    _OpenDrive预览工具_有助于测试地图的完整性。如果任何交叉口有错误，请点击`操作工具`，然后点击`重建操作道路`。一旦地图准备就绪，点击`OpenDRIVE预览工具`按钮来可视化OpenDRIVE道路网络，并最后一次检查所有内容。
确保地图选择完整，以便导出。通过点击[世界设置工具](https://www.mathworks.com/help/roadrunner/ref/worldsettingstool.html)，拖动蓝色边界框的边缘以包含您希望导出的完整区域。准备就绪后，点击_应用世界更改_。
![world_bounds_settings](img/rr_world_settings.png)
准备导出时：
__1.__ 导出`.fbx`文件：
  - 在主工具栏中，选择`文件` -> `导出` -> `Firebox (.fbx)`
__2.__ 在弹出的窗口中：
  - 检查以下选项：
    - _按分割导出_：根据语义分割将网格分割，改善行人导航。
    - _2的幂次纹理尺寸_：提高性能。
    - _嵌入纹理_：确保纹理嵌入网格中。
    - _导出到图块_：选择图块的大小。CARLA可以使用的最大大小为2000 x 2000。
    - _导出单个图块_：生成用于在CARLA中流式传输大型地图所需的单个图块。
>>>>>>![export_large_map_fbx](../img/large_map_export_fbx.png)
__3.__ 导出`.xodr`文件：
  - 在主工具栏中，选择`文件` -> `导出` -> `OpendDRIVE (.xodr)`
!!! Warning
    请确保`.xodr`和`.fbx`文件具有相同的名称。
---
## 下一步
您现在可以准备将地图导入CARLA。有关更多详细信息，请参阅[导入大型地图](large_map_import.md)指南。
---
如果您对过程有任何疑问，可以在[论坛](https://github.com/carla-simulator/carla/discussions)中提问。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>

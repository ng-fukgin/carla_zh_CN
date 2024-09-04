# 生成详细碰撞器
本教程解释了如何为车辆创建更准确的碰撞边界（相对于原始物体的形状）。这些可以作为与碰撞检测兼容的物理碰撞器使用，或者作为基于射线的传感器（如LIDAR）使用的二级碰撞器，以获取更准确的数据。新的碰撞器可以集成到CARLA中，以便整个社区都能从中受益。有关如何为内容仓库做出贡献的更多信息，请点击[这里](cont_contribution_guidelines.md)。
创建新碰撞器有两种方法，但它们并不完全等价。
*   __射线碰撞器__ — 这种方法需要一些基本的3D建模技能。为车辆添加一个二级碰撞器，以便基于射线的传感器（如LIDAR）可以获取更精确的数据。
*   __物理碰撞器__ — 这种方法遵循由贡献者[Yan Kaganovsky / yankagan](https://github.com/yankagan)创建的[教程](https://bitbucket.org/yankagan/carla-content/wiki/Home)，用于创建不需要手动建模的网格。然后，这个网格可以作为车辆的主要碰撞器，用于物理和传感器检测（除非添加了二级碰撞器）。
---
*   [__射线碰撞器__](#射线碰撞器)
    *   [1-导出车辆FBX](#1-导出车辆FBX)
    *   [2-生成低密度网格](#2-生成低密度网格)
    *   [3-将网格导入UE](#3-将网格导入UE)
    *   [4-将网格作为碰撞器添加](#4-将网格作为碰撞器添加)
---
- [生成详细碰撞器](#生成详细碰撞器)
	- [射线碰撞器](#射线碰撞器)
		- [1-导出车辆FBX](#1-导出车辆fbx)
		- [2-生成低密度网格](#2-生成低密度网格)
		- [3-将网格导入UE](#3-将网格导入ue)
		- [4-将网格作为碰撞器添加](#4-将网格作为碰撞器添加)
	- [物理碰撞器](#物理碰撞器)
		- [0-先决条件](#0-先决条件)
		- [1-在Unreal编辑器中为车轮定义自定义碰撞](#1-在unreal编辑器中为车轮定义自定义碰撞)
		- [2-导出车辆为FBX](#2-导出车辆为fbx)
		- [3到4-导入到Blender并创建自定义边界](#3到4-导入到blender并创建自定义边界)
		- [5-从Blender导出到FBX](#5-从blender导出到fbx)
		- [6到8-导入碰撞器并定义物理](#6到8-导入碰撞器并定义物理)
	- [](#)
---
## 射线碰撞器
### 1-导出车辆FBX
首先，需要原始车辆的网格作为参考。为了学习目的，本教程导出了CARLA车辆的网格。
1.1 在UE中打开CARLA，转到 `Content/Carla/Static/Vehicles/4Wheeled/<model_of_vehicle>`。
1.2 在 `SM_<model_of_vehicle>` 上按 `右键` 以将车辆网格导出为FBX。
### 2-生成低密度网格
2.1 打开一个3D建模软件，使用原始网格作为参考，创建一个低密度网格，保持与原始网格的一致性。
![manual_meshgen](img/tuto_D_colliders_mesh.jpg)
2.2 将新网格保存为FBX。将网格命名为 `sm_sc_<model_of_vehicle>.fbx`，例如 `sm_sc_audiTT.fbx`。
!!! Note
    对于轮子和其他元素（如车顶、挡泥板等），新网格应与几何形状非常准确。放置简单的立方体是不行的。
### 3-将网格导入UE
3.1 在UE中打开CARLA，转到 `Content/Carla/Static/Vehicles/4Wheeled/<model_of_vehicle>`。
3.2 按 `右键` 导入新的网格 `SM_sc_<model_of_vehicle>.fbx`。
### 4-将网格作为碰撞器添加
4.1 转到 `Content/Carla/Blueprints/Vehicles/<model_of_vehicle>` 并打开名为 `BP_<model_of_vehicle>` 的车辆蓝图。
4.2 选择 `CustomCollision` 元素，并在 `Static mesh` 属性中添加 `SM_```
`SM_sc_<model_of_vehicle>.fbx`。
![manual_customcollision](img/tuto_D_colliders_final.jpg)
4.3 在工具栏上方按下 `Compile` 并保存更改。
!!! Note
    对于摩托车和自行车等车辆，请使用相同的组件 `CustomCollision` 更改车辆本身的碰撞器网格。
---
## 物理碰撞器
!!! Important
    本教程基于[yankagan](https://github.com/yankagan)的[贡献](https://bitbucket.org/yankagan/carla-content/wiki/Home)! 贡献者还想感谢[Francisco E](https://www.youtube.com/watch?v=SEH4f0HrCDM)的[如何将自定义碰撞器导入UE的教程](https://www.youtube.com/watch?v=SEH4f0HrCDM)。
[此视频](https://www.youtube.com/watch?v=CXK2M2cNQ4Y)展示了遵循本教程后所取得的成果。
### 0-先决条件
*   __从源码构建CARLA__ 在[Linux](build_linux.md)或[Windows](build_windows.md)上。
*   __Blender 2.80或更高版本__ 从[官方网站](https://www.blender.org/download/)免费获取（开源3D建模软件）。
*   __Blender的VHACD插件__ 按照[此处](https://github.com/andyp123/blender_vhacd)的说明使用。此插件自动使用一系列凸包来近似所选对象。
!!! Note
    对于Blender的新手，这个[系列](https://www.youtube.com/watch?v=ppASl6yaguU)和[Udemy课程](https://www.udemy.com/course/blender-3d-from-zero-to-hero/?pmtag=MRY1010)可能是一个很好的介绍。
### 1-在Unreal编辑器中为车轮定义自定义碰撞
__步骤1.__ *(在UE)* — 为车轮添加碰撞边界。这些步骤在以下视频中详细描述。
[![auto_step01](img/tuto_D_colliders_01.jpg)](https://www.youtube.com/watch?v=bECnsTw6ehI)
### 2-导出车辆为FBX
__步骤2.__ *(在UE)* — 将车辆的骨架网格导出为FBX文件。
__2.1__ 转到 `Content/Carla/Static/Vehicles/4Wheeled/<model_of_vehicle>`。
__2.2__ 在 `SM_<model_of_vehicle>` 上按 `右键` 以将车辆网格导出为FBX。
### 3到4-导入到Blender并创建自定义边界
__步骤3.__ *(在Blender)* — 将FBX文件导入Blender。
__步骤4.__ *(在Blender)* — 使用VHACD工具添加凸包网格以形成新的碰撞边界（UE为了计算效率的要求）。这是最难的步骤。如果选择整个汽车，VHACD创建的碰撞边界将不精确且混乱。它将包含尖锐的边缘，这将使车辆在道路上行驶时出现问题。重要的是，车轮周围要有平滑的边界。使用汽车主体的凸包分解，镜子仍然不会看起来很正确。对于计算机视觉，车辆的细节很重要。因此，这些步骤被分为两部分。
__4.1__ 使用VHACD工具切出车轮的底部部分、侧视镜和汽车车身的顶部部分，以创建第一个边界。使用VHACD工具切出汽车底部的上半部分以创建第二个边界（汽车车身的顶部部分）。
[![auto_step03](img/tuto_D_colliders_03.jpg)](https://www.youtube.com/watch?v=oROkK3OCuOA)
__4.2__ 使用VHACD工具为侧视镜创建单独的边界。
[![auto_step04](img/tuto_D_colliders_04.jpg)](https://www.youtube.com/watch?v=L3upzdC602s)
!!! Warning
    非常小心地命名对象。每个边界都应该以 `UCX_` 开头，其余名称必须与原始网格的名称完全相同。
### 5-从Blender导出到FBX
__步骤5.__ *(在Blender)* — 将自定义碰撞边界导出为FBX文件。
__5.1__ 只选择原始车辆和所有新添加的碰撞对象。
__5.2__ 在导出菜单中，选择“所选对象”并仅选择“网格”。
[![auto_step05](img/tuto_D_colliders_05.jpg)](https://youtu.be/aJPyskYjzWo)
### 6到8-导入碰撞器并定义物理
__步骤6.__ *(在UE)* — 将新的FBX导入CARLA作为Unreal资产文件（静态网格）。
__步骤7.__ *(在UE)* — 将自定义碰撞器导入到特定车辆的物理资产中，以便用于计算。
__步骤8.__ *(在UE)* — 创建连接不同关节的约束并定义所有部件的物理。
[![auto_step0608](img/tuto_D_colliders_0608.jpg)](https://www.youtube.com/watch?v=aqFNwAyj2CA)
---
这就是关于如何在CARLA中更改车辆默认碰撞器的全部内容。
打开CARLA并玩一会儿。如果有任何疑问，请随时在论坛上提出。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="Go to the CARLA forum">
CARLA论坛</a>
</p>
</div>
<p style="font-size: 20px">接下来？</p>
<div class="build-buttons">
<p>
<a href="../tuto_E_create_road_markings" target="_blank" class="btn btn-neutral" title="Learn how to create custom road markings for CARLA.">
创建自定义道路标记</a>
</p>
<p>
<a href="../tuto_G_control_vehicle_physics" target="_blank" class="btn btn-neutral" title="Learn how to set runtime changes on a vehicle physics.">
控制车辆物理</a>
</p>
</div>



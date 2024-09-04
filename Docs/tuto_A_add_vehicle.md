# 添加新车辆

本教程详细介绍如何将新车辆添加到CARLA中。教程分为两部分，一部分用于四轮车辆，另一部分用于两轮车辆。教程概述了在建模车辆时必须满足的基本要求，以确保其在CARLA中正常运行，并在将车辆导入到Unreal Engine后进行配置。

*   [__添加四轮车辆__](#add-a-4-wheeled-vehicle)
	*   [绑定和建模车辆](#bind-and-model-the-vehicle)
	*   [导入和配置车辆](#import-and-configure-the-vehicle)
*   [__添加两轮车辆__](#add-a-2-wheeled-vehicle)

!!! Important
    本教程仅适用于从源代码构建并具有Unreal Engine编辑器访问权限的用户。

---
## 添加四轮车辆

添加到CARLA的车辆需要使用一个[__通用基础骨架__](https://carla-assets.s3.eu-west-3.amazonaws.com/fbx/VehicleSkeleton.rar)。点击链接将下载名为`VehicleSkeleton.rar`的文件夹，其中包含两种不同格式的基础骨架文件（ASCII和二进制格式的`.fbx`文件）。根据你的3D建模软件的需求选择合适的格式。

__可以更改骨架骨骼的位置，但任何其他操作（如旋转、新增骨骼或更改当前层次结构）都将导致错误。__

---

### 绑定和建模车辆

此部分介绍在车辆建模阶段的最低要求，以确保其能在CARLA中成功使用。过程涉及将骨架正确绑定到车辆的基础和车轮上，创建物理资产和光线传感器网格，并导出为正确的格式。

__1. 导入基础骨架。__

将基础骨架导入你选择的3D建模软件。常用编辑器包括Maya和Blender。

__2. 绑定骨骼。__

根据以下命名规范，将骨骼绑定到车辆网格的相应部分。确保将轮子的骨骼居中放置在网格中。

*   __左前轮：__ `Wheel_Front_Left`
*   __右前轮：__ `Wheel_Front_Right`
*   __左后轮：__ `Wheel_Rear_Left`
*   __右后轮：__ `Wheel_Rear_Right`
*   __其他网格部分：__ `VehicleBase`

!!! Warning
    不要对骨骼名称或层次结构进行任何更改，也不要添加新的骨骼。

__3.  建模车辆。__

车辆的多边形数量应在50,000至100,000之间。我们建议根据实际车辆的尺寸和比例进行建模。

建议将车辆分为以下几种材质：

>1. __车身：__ 车辆的金属部分。此材质将更改为Unreal Engine材质。可以添加徽标和细节，但要使其可见，必须使用Unreal Engine编辑器中的Alpha通道将其涂上不同颜色。
- __外部玻璃：__ 允许从外部看到内部的玻璃层。
- __内部玻璃：__ 允许从内部看到外部的玻璃层。
- __灯光：__ 前灯、指示灯等。
- __外部灯光玻璃：__ 允许从外部看到灯光内部的玻璃层。
- __内部灯光玻璃：__ 允许从内部看到灯光外部的玻璃层。
- __车牌：__ 29x12厘米的矩形平面。你可以使用CARLA提供的`.fbx`文件以获得最佳效果，请点击[此处](https://carla-assets.s3.eu-west-3.amazonaws.com/fbx/LicensePlate.rar)下载。纹理将在Unreal Engine中自动分配。
- __内部：__ 任何不属于以上部分的细节都可以放入_内部_。

材质命名格式应为`M_CarPart_CarName`，例如`M_Bodywork_Mustang`。

纹理命名格式应为`T_CarPart_CarName`，例如`T_Bodywork_Mustang`。纹理的尺寸应为2048x2048。

Unreal Engine会自动创建LOD，但你也可以在3D编辑器中手动创建。三角形数量如下：

- __LOD 0：__ 100,000 tris
- __LOD 1：__ 80,000 tris
- __LOD 2：__ 60,000 tris
- __LOD 3：__ 30,000 tris


__4. 创建物理资产网格。__

物理资产网格是一个附加网格，允许Unreal Engine计算车辆的物理效果。它应该尽可能简单，具有较少的多边形，并应覆盖除车轮外的整个车辆。请参阅下面的图像以获取示例。

>>![物理资产网格](../img/physical_asset_mesh.png)

物理资产网格应作为单独的`.fbx`文件导出。最终文件应满足以下要求：

- 拥有一个基础网格。此基础网格应为物理资产网格的副本，名称应与原始车辆相同。
- 物理资产网格必须命名为`UCX_<vehicle_name>_<number_of_mesh>`，__否则Unreal Engine将无法识别它__。
- 网格不得超出原始模型的边界。
- 网格应与原始模型的位置相同。

>>![基础网格](../img/base_mesh.png)

将最终网格导出为名为`SMC_<vehicle_name>.fbx`的`.fbx`文件。

__5. 创建光线传感器网格。__

光线传感器网格设置车辆的形状，传感器（雷达、激光雷达和语义激光雷达）将检测到此形状。此网格的几何形状应比物理资产网格稍微更精确，以增加传感器仿真的真实性，但不能像车辆网格那样详细，以保持性能。

创建光线传感器网格时请考虑以下几点：

- 网格应覆盖车辆的所有部分，包括车轮、后视镜和格栅。
- 车轮应为不超过16圈的圆柱体。
- 如果需要，可以将多个网格组合在一起。
- 网格不得超出原始模型的边界。
- 网格应与原始模型的位置相同。

>>![碰撞网格](../img/collision_mesh.png)

将最终网格导出为名为`SM_sc_<vehicle_name>.fbx`的`.fbx`文件。

__5. 导出车辆网格。__

选择所有主车辆网格和骨架基础，导出为`.fbx`格式。

---

### 导入和配置车辆

此部分详细介绍了如何将车辆导入Unreal Engine以在CARLA中使用的过程。请在Unreal Engine编辑器中执行这些步骤。

__1. 创建车辆文件夹。__

在`Content/Carla/Static/Vehicles/4Wheeled`中创建一个名为`<vehicle_name>`的新文件夹。

__2. 导入`.fbx`文件。__

在新车辆文件夹中，通过右键单击**_内容浏览器_**并选择**_Import into Game/Carla/Static/Vehicles/4Wheeled/<vehicle_name\>_**导入你的主车辆骨架`.fbx`文件。

在弹出的对话框中：

- 将**_导入内容类型_**设置为`Geometry and Skinning Weights`。
- 将**_法线导入方法_**设置为`Import Normals`。
- 可选地，将**_材质导入方法_**设置为`Do not create materials`。取消选中**_导入纹理_**以避免Unreal Engine创建默认材质。

骨架网格将会出现，并伴随两个新文件`<vehicle_name>_PhysicsAssets`和`<vehicle_name>_Skeleton`。

将其余的`.fbx`文件从主车辆骨架`.fbx`文件中单独导入。

__3. 设置物理资产网格。__

>1. 从**_内容浏览器_**中打开`<vehicle_name>_PhysicsAssets`。
- 右键单击**_骨架树_**面板中的`Vehicle_Base`网格，然后选择**_从静态网格复制碰撞_**。
- 搜索并选择你的`SMC_<vehicle_name>`文件。你应该能在视口中看到物理资产网格的轮廓。
- 从`Vehicle_Base`中删除默认胶囊形状。
- 选择所有车轮：
    - 转到**_工具_**面板，将**_原始类型_**更改为`Sphere`。
    - 转到**_详情_**面板，将**_物理类型_**更改为`Kinematic`。
    - 将**_线性阻尼_**设置为`0`。这将消除车轮的额外摩擦。
- 为所有网格启用**_生成命中事件仿真_**。
- 单击“重新生成骨骼”。
- 调整轮胎球体以适应轮胎的大小。
- 保存并关闭窗口。
>![碰撞网格](../img/collision_mesh_vehicle.png)
__4. 创建动画蓝图。__
>1. 在“内容浏览器”中，右键单击您的车辆文件夹内部，然后选择“动画->动画蓝图”。
- 在“父类”中搜索并选择`VehicleAnimInstance`。
- 在“目标骨架”中搜索并选择`<vehicle_name>_Skeleton`。
- 单击“确定”并将蓝图重命名为`AnimBP_<vehicle_name>`。
__5. 配置动画蓝图。__
为了简化配置动画蓝图的过程，我们将从CARLA的本地车辆复制一个：
>1. 前往`Content/Carla/Static/Vehicle`并选择任何CARLA车辆文件夹。打开其动画蓝图。
- 在“我的蓝图”面板中，双击“动画图”。您将看到图表出现在视口中。
- 单击并拖动以选择“网格空间参考姿态”，“轮子处理器”和“组件到本地”组件。右键单击并选择“复制”。
- 返回到您自己的车辆动画蓝图，并将复制的 content 粘贴到图表区域。
- 从“组件到本地”组件中的站立人物单击并拖动到“输出姿态”中的人物，以将组件连接在一起。
- 在左上角单击“编译”。您现在应该会看到一条脉冲线流经整个序列。
- 保存并关闭窗口。
>>![add_vehicle_step_04](img/add_vehicle_step_04.jpg)
__6. 准备车辆和车轮蓝图。__
>1. 在“内容浏览器”中，转到`Content/Carla/Blueprints/Vehicles`并创建一个新文件夹`<vehicle_name>`。
- 在文件夹内部，右键单击并转到“蓝图类”。在弹出的窗口中打开“所有类”部分。
- 搜索`BaseVehiclePawn`并单击“选择”。
- 将文件重命名为`BP_<vehicle_name>`。
- 前往`Carla/Blueprints/Vehicles`中的任何本地CARLA车辆文件夹。从“内容浏览器”中，将四个车轮蓝图复制到您自己的车辆蓝图文件夹中。重命名文件，将旧车辆名称替换为您的车辆名称。
>>![复制车轮蓝图](../img/copy_wheel_blueprint.png)
__7. 配置车轮蓝图。__
>1. 在您的车辆蓝图文件夹中，打开所有四个车轮蓝图。
- 在“类默认值”面板中，将“碰撞网格”设置为`Wheel_Shape`。__省略此步骤将导致车辆轮胎沉入地面__。
- 根据您的车辆规格调整轮形状半径、宽度、质量和阻尼率。
- 将“轮胎配置”设置为`CommonTireConfig`
- 在前轮上根据您的喜好设置“转向角”(默认为`70`)。取消勾选“受手刹影响”。
- 在后轮上设置“转向角”为`0`。勾选“受手刹影响”。
- 设置悬挂值时，可以使用这里的值作为参考。[点击此处](tuto_D_customize_vehicle_suspension.md)。
- 编译并保存。
>>![轮形状](../img/wheel_shape.png)
__8. 配置车辆蓝图。__
>1. 从“内容浏览器”中，打开您的`BP_<vehicle_name>`。
- 在“组件”面板中，选择“网格(车辆网格)(继承)”。
- 在“详细信息”面板中，转到“骨骼网格”并搜索并选择您的车辆基础骨架文件(位于`Carla/Static/Vehicles/4Wheeled/<vehicle_name>`文件夹中)。
- 在“详细信息”面板中转到“动画类”。搜索并选择您的`AnimBP_<vehicle_name>`文件。
- 在“组件”面板中，选择“自定义碰撞(继承)”。
- 在“详细信息”面板中选择“静态网格”，并搜索您的`SM_sc_<vehicle_name>`射线投射网格。
- 在“组件”面板中，选择“车辆移动(运动组件)(继承)”。
- 在“详细信息”面板中搜索`wheel`。您将找到每个轮子的设置。对于每个轮子，单击“轮子类”并搜索对应正确轮位置的`BP_<vehicle_name>_<wheel_name>`文件。
>>>>![车轮蓝图](../img/wheel_blueprint.png)
如果您车辆的额外网格(门、灯等)与基础网格分开：
>1. 将它们拖到“组件”面板中的“网格(车辆网格)(继承)”层次结构中。
- 在层次结构中选择额外网格，并在“详细信息”面板中搜索“碰撞”。 
- 将“碰撞预设”设置为`NoCollision`。
- 选择任何灯光网格。在“详细信息”面板中搜索“标签”，并添加标签`emissive`。
点击“保存”并“编译”。
__9. 将车辆添加到蓝图库__。
>1. 在`Content/Carla/Blueprint/Vehicle`中，打开`VehicleFactory`文件。
- 在“生成定义”选项卡中，双击“车辆”。
- 在“详细信息”面板中，展开“默认值”部分，并向车辆数组中添加一个新元素。
- 填写车辆的“制造商”和“型号”。
- 在“类别”值中填写您的`BP_<vehicle_name>`文件。
- 可选地，为车辆提供一组推荐的颜色。
- 编译并保存。
>![车辆工厂](../img/vehicle_factory.png)
__10. 测试车辆__。
启动CARLA，在`PythonAPI/examples`打开一个终端，并运行以下命令：
```sh
python3 manual_control.py --filter <model_name> # 在步骤9中定义的制造商或型号
```
!!! Note
    即使您在制造商和型号中使用了大写字母，当传递给过滤器时，也需要将它们转换为小写。
---
## 添加两轮车辆
添加两轮车辆与添加四轮车辆类似，但由于动画的复杂性，您需要设置额外的骨骼来指导驾驶员的动画。[这里](https://carla-assets.s3.eu-west-3.amazonaws.com/fbx/BikeSkeleton.rar)是两轮车辆参考骨架的链接。
与四轮车辆一样，将模型朝向正“x”方向，每个骨骼轴朝向正x方向，z轴朝上。
```yaml
骨骼设置：
  - Bike_Rig:                   # 网格的起源点。将其放在场景的0点
    - BikeBody:                 # 模型的身体中心。
      - Pedals:                 # 如果车辆是自行车，请将踏板器绑定到这个骨骼，它会随着自行车的加速而旋转。
        - RightPedal:           # 设置驾驶员脚的位置，如果车辆是自行车，则与踏板器一起旋转。
        - LeftPedal:            # ^
      - RearWheel:              # 车辆的后轮
      - Handler:                # 与前轮一起旋转，将车辆处理器绑定到它。
        - HandlerMidBone:       # 位于前轮骨骼上方，以使处理器与前轮对齐
        - HandlerRight:         # 设置驾驶员手的位置，不需要绑定到任何东西。
        - HandlerLeft:          # ^
      - Frontwheel:             # 车辆的前轮
      - RightHelperRotator:     # 这四个额外的骨骼是为了使用额外的不可见轮子使自行车稳定的过时系统
        - RightHelperWheel:      # ^
      - LeftHelperRotator:      # ^
        - LeftHelperWheel:      # ^
      - Seat:                   # 设置驾驶员髋骨的位置。不需要绑定到任何东西，但要小心放置。
```
__1.__ 将fbx作为骨骼网格导入到`Content/Carla/Static/Vehicles/2Wheeled`自己的文件夹中。导入时选择“General2WheeledVehicleSkeleton”作为骨架。应该会自动创建并链接一个物理资产。
__2.__ 调整物理资产。删除自动创建的资产，并尝试将盒子添加到`BikeBody`骨骼以尽可能匹配形状，确保启用了生成命中事件。
  为每个轮子添加一个球体，并将它们的“物理类型”设置为“运动学”。
__3.__ 创建文件夹`Content/Blueprints/Vehicles/<vehicle-model>`
__4.__ 在该文件夹内创建两个派生自“VehicleWheel”类的蓝图类。将它们命名为`<vehicle-model>_FrontWheel`和`<vehicle-model>_RearWheel`。将它们的“形状半径”精确设置为与网格轮子半径匹配（小心，是半径不是直径）。将它们的“轮胎配置”设置为“CommonTireConfig”。在前轮上取消勾选“受手刹影响”，在后轮上将“转向角”设置为零。
__5.__ 在同一文件夹中创建一个派生自`Base2WheeledVehicle`的蓝图类，命名为`<vehicle-model>`。打开它进行编辑，选择组件“网格”，设置“骨骼网格”和“动画类”到对应的类。然后选择`VehicleBounds`组件并将大小设置为覆盖从上方看到的车辆区域。
__6.__ 选择组件“VehicleMovement”，在“车辆设置”下展开“轮子设置”，设置每个轮子。
*   __0:__ 轮子类=`<vehicle-model>_FrontWheel`，骨骼名称=`FrontWheel`
*   __1:__ 轮子类=`<vehicle-model>_FrontWheel`，骨骼名称=`FrontWheel`
*   __2:__ 轮子类=`<vehicle-model>_RearWheel`，骨骼名称=`RearWheel`
*   __3:__ 轮子类=`<vehicle-model>_RearWheel`，骨骼名称=`RearWheel`
(您会注意到我们实际上是在每个骨骼上放置了两个轮子。Unreal提供的车辆类不支持轮子数量与4不同的车辆，所以我们必须让它相信车辆有4个轮子)
__7.__ 找到变量“is bike”并勾选它，如果您的模型是自行车。这将激活踏板器的旋转。如果是摩托车，则保持未标记。
__8.__ 找到变量“back Rotation”并根据需要设置它。选择组件“SkeletalMesh”(驾驶员)，并将其沿x轴移动，直到它位于座位位置。
__9.__ 测试它，转到`CarlaGameMode`蓝图并更改“Default Pawn Class”为新建的自行车蓝图。
__10.__ (可选)如果您想要为两轮车辆添加AI代理，请执行以下操作：
>1. 在`Content/Carla/Blueprints/Vehicles/<vehicle-model>`中创建一个新蓝图，派生自`VehicleAIController`。
- 在“事件图”中，找到并双击“Possess”函数。
- 在“细节”面板中，选择“Vehicle”变量并将其设置为您的`<vehicle-model>`蓝图。
- 保存并编译蓝图。
现在，当您在CARLA中启动模拟时，AI代理应该能够控制您的两轮车辆。您可以通过以下命令来测试它：
```sh
python3 dynamic_weather.py --vehicle <vehicle-model> --ai
```
请确保将`<vehicle-model>`替换为您的车辆蓝图的名称。此命令将启动模拟，并创建一个AI代理来控制您的车辆。


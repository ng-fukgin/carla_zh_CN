# 导入地图的其他方法

本指南介绍了将地图导入 CARLA 的其他方法。与 [软件包](tuto_M_add_map_package.md) 和 [源码](tuto_M_add_map_source.md) 导入指南中描述的过程相比，这些方法涉及更多手动步骤。首先我们将介绍 RoadRunner 插件，然后是手动导入方法。

- [__使用 RoadRunner 插件导入__](#roadrunner-plugin-import)
- [__手动导入__](#manual-import)

---

## 使用 RoadRunner 插件导入

MathWorks 的 RoadRunner 软件为虚幻引擎提供了插件，以帮助简化将地图导入 CARLA 的过程。 

#### 插件安装

__1.__ 插件可从 [MathWorks 网站](https://www.mathworks.com/help/roadrunner/ug/Downloading-Plugins.html) 下载。MathWorks 还有一个 [完整教程](https://www.mathworks.com/help/roadrunner/ug/Exporting-to-CARLA.html)，与本教程类似，介绍如何使用插件将地图导入 CARLA。

__2.__ 解压下载文件夹的内容，并将文件夹 `RoadRunnerImporter`、`RoadRunnerCarlaIntegration` 和 `RoadRunnerMaterials` 移动到 `<carla>/Unreal/CarlaUE4/Plugins/`。

__3.__ 按照以下说明重新构建插件：  

*   __在 Windows 上__  
	* 右键单击 `<carla>/Unreal/CarlaUE4` 中的 `.uproject` 文件，并选择 `Generate Visual Studio project files`。  
	* 在 CARLA 的根文件夹中运行命令：

```sh
make launch
```

*   __在 Linux 上__  
	* 运行以下命令：  
```sh
UE4_ROOT/GenerateProjectFiles.sh -project="carla/Unreal/CarlaUE4/CarlaUE4.uproject" -game -engine
```

__4.__ 在虚幻引擎窗口中，确保通过 `Edit > Plugins` 选中了这两个插件的复选框。 

![rr_ue_plugins](../img/rr-ue4_plugins.jpg)

### 导入地图

__1.__ 使用 `Import` 按钮将 `<mapName>.fbx` 文件导入到 `/Content/Carla/Maps` 下的一个新文件夹中。  

![ue_import](../img/ue_import_mapname.jpg)

__2.__ 将 `Scene > Hierarchy Type` 设置为 _Create One Blueprint Asset_（默认已选中）。  
__3.__ 将 `Static Meshes > Normal Import Method` 设置为 _Import Normals_。  

![ue_import_options](../img/ue_import_options.jpg)

__4.__ 将 `<mapName>.xodr` 文件复制到虚幻引擎 Content Browser 窗口中的同一文件夹中。

!!! 注意
    如果您使用的是 0.9.10 之前的 CARLA 版本，则此步骤不同。请通过窗口右下角的面板更改文档版本并遵循旧说明。

__5.__ 打开 RoadRunner 导入生成的蓝图，在 `组件` 面板中选择 `Open Drive Actor`。在 `属性` 面板中，选中 `针对此地图创建网络` 的框，然后在 Content Browser 中点击您想要用于该地图的 `.xodr` 文件。 

![ue_odactor](../img/ue_odactor.jpg)

---

## 手动导入

此方法描述了手动导入地图和手动设置其所有组件的过程。

#### 地图摄入

__1.__ 在 `Unreal/CarlaUE4/Content/Carla/Maps` 下创建新文件夹，并将其命名为 `<mapName>`。 

__2.__ 将 `<mapName>.fbx` 和 `<mapName>.xodr` 移动到该文件夹。 

__3.__ 打开虚幻引擎编辑器并导入 `<mapName>.fbx`。在弹出的 `FBX 导入选项` 窗口中执行以下操作： 
- 确保 `Mesh > Combine Meshes` 已选中。 
- 将 `Scene > Hierarchy Type` 设置为 _Create One Blueprint Asset_。 
- 将 `Static Meshes > Normal Import Method` 设置为 _Import Normals_。 

__4.__ 导入后，将生成的蓝图拖动到世界中。

#### OpenDRIVE 设置

__1.__ 在 Content Browser 中右键点击并选择 `Blueprint Class`。
__2.__ 在 `All Classes` 搜索栏中输入 `OpenDriveActor`。 
__3.__ 为蓝图命名并将其拖动到场景中。 
__4.__ 在 `组件` 窗口中选择 `OpenDriveActor`。
__5.__ 在 `属性` 窗口中，确保 `Add Spawners` 已选中，然后在 `Open Drive Data` 下点击 `File` 并选择您的 `<mapName>.xodr` 文件。 

!!! 重要
    如果您使用的是 CARLA 的软件包版本，请确保您的地图位于 `Content/Carla/Maps`。

---

如果您对上述内容有任何疑问，请随时在[论坛](https://github.com/carla-simulator/carla/discussions)中反馈。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往 CARLA 论坛">
CARLA 论坛</a>
</p>
</div>

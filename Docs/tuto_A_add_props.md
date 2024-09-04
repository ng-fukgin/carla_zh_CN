# 在CARLA中添加新道具
道具是场景中的资产，除了地图和车辆之外。这包括路灯、建筑物、树木等。模拟器可以随时以简单的过程摄入新道具。这对于在地图上创建自定义环境非常有用。
* [__准备包__](#prepare-the-package)  
	*   [创建文件夹结构](#create-the-folder-structure)  
	*   [创建JSON描述](#create-the-json-description)  
*   [__在CARLA包中摄入__](#ingestion-in-a-carla-package)  
*   [__从源代码构建中摄入__](#ingestion-in-a-build-from-source)  
---
## 准备包
### 创建文件夹结构
__1. 在`carla/Import`内部创建一个文件夹.__ 文件夹的名称不重要。
__2. 创建子文件夹.__ 应该有一个通用的子文件夹用于所有道具，并在其中创建与要导入的道具数量相同的子文件夹。
__3. 将每个道具的文件移动到相应的子文件夹.__ 道具子文件夹将包含`.fbx`网格，以及（可选）它所需的纹理。
例如，一个包含两个单独包的`Import`文件夹应该具有以下结构。
```sh
Import
│
├── Package01
│   ├── Package01.json
│   └── Props
│       ├── Prop01
│       │   ├── Prop01_Diff.png
│       │   ├── Prop01_Norm.png
│       │   ├── Prop01_Spec.png
│       │   └── Prop01.fbx
│       └── Prop02
│           └── Prop02.fbx
└── Package02
    ├── Packag02.json
    └── Props
        └── Prop03
            └── Prop03.fbx
```
### 创建JSON描述
在包的根文件夹中创建一个`.json`文件。将文件命名为包的名称。请注意，这将是分发名称。文件的内容将描述每个道具的JSON数组和地图，并提供它们的基本信息。
__地图__不包括在本教程中，因此此定义将为空。有关[添加新地图](tuto_M_custom_map_overview.md)的特定教程。
__道具__需要以下参数。
*   道具的__名称__。这必须与`.fbx`相同。
*   `.fbx`的__源路径__。
*   道具大小的估计。可能的值列在这里。
	*   `tiny`  
	*   `small`  
	*   `medium`  
	*   `big`  
	*   `huge`  
*   语义分割的__标签值__。如果标签拼写错误，它将被读作`Unlabeled`。
	*   `Bridge`
	*   `Building`
	*   `Dynamic`
	*   `Fence`
	*   `Ground`
	*   `GuardRail`
	*   `Other`
	*   `Pedestrian`
	*   `Pole`
	*   `RailTrack`
	*   `Road`
	*   `RoadLine`
	*   `SideWalk`
	*   `Sky`
	*   `Static`
	*   `Terrain`
	*   `TrafficLight`
	*   `TrafficSign`
	*   `Unlabeled`
	*   `Vegetation`
	*   `Vehicles`
	*   `Wall`
	*   `Water`
最后，`.json`应类似于以下内容。
```json
{
  "maps": [
  ],
  "props": [
    {
      "name": "MyProp01",
      "size": "medium",
      "source": "./Props/Prop01/Prop01.fbx",
      "tag": "SemanticSegmentationTag01"
    },
    {
      "name": "MyProp02",
      "size": "small",
      "source": "./Props/Prop02/Prop02.fbx",
      "tag": "SemanticSegmentationTag02"
    }
  ]
}
```
!!! Warning
    Packages with the same name will produce an error.  
---
## 在CARLA包中摄入
这是用于摄入道具到CARLA包（如CARLA 0.9.8）的方法。
将创建一个Unreal Engine的Docker镜像。它作为一个黑盒，自动将包导入CARLA镜像，并生成一个分发包。Docker镜像的构建需要4小时和400GB的空间。然而，这只在第一次需要。
__1. 构建Unreal Engine的Docker镜像.__ 按照[这些说明](https://github.com/carla-simulator/carla/tree/master/Util/Docker)构建镜像。
__2. 运行脚本来烹饪道具.__ 在`~/carla/Util/Docker`文件夹中有一个脚本，它连接到之前创建的Docker镜像，并自动进行摄入。它只需要输入和输出文件的路径，以及要摄入的包的名称。
```sh
python3 docker_tools.py --input ~/path_to_package --output ~/path_for_output_assets  --package=Package01
```
__3. 定位包__。Docker应该已经在输出路径生成了`Package01.tar.gz`包。这是资产的独立包。
__4. 将包导入CARLA.__  
*   __在Windows上__在`WindowsNoEditor`文件夹中提取包。
*   __在Linux上__将包移动到`Import`文件夹，并运行脚本以导入它。
```sh
cd Util
./ImportAssets.sh
```
!!! Note
    Linux上有一个替代方法。将包移动到`Import`文件夹，并运行`Util/ImportAssets.sh`脚本来提取包。
---
## 从源代码构建中摄入
这是将道具导入CARLA源代码构建的方法。
JSON文件将被读取以在Unreal Engine的`Content`内部放置道具。此外，它将在包的`Config`文件夹内创建一个`Package1.Package.json`文件。这将用于在蓝图库中定义道具，并在Python API中公开它们。如果包被导出为[独立包](tuto_A_create_standalone.md)，它也将被使用。
当一切准备就绪时，运行命令。
```sh
make import
```
!!! Warning
    请确保包在CARLA的`Import`文件夹内。
---
这就是关于将新道具添加到CARLA的所有信息。如果您有任何疑问，请在论坛上提问。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>


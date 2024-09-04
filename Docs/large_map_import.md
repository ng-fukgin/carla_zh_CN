# 导入/打包大型地图
在RoadRunner中生成的大型地图可以导入到CARLA的源代码构建中，并打包用于分发和在CARLA独立包中使用。这个过程与标准地图的导入非常相似，只是增加了对图块的具体命名规则和批量导入。
- [__文件和文件夹__](#files-and-folders)
- [__创建JSON描述（可选）__](#create-the-json-description-optional)
- [__进行导入__](#making-the-import)
- [__打包大型地图__](#package-a-large-map)
---
## 文件和文件夹
所有要导入的文件都应该放在CARLA根目录的`Import`文件夹中。这些文件应包括：
- 地图的网格，以多个`.fbx`文件表示地图的不同图块。
- OpenDRIVE定义，一个`.xodr`文件。
!!! Warning
    您不能同时导入大型地图和标准地图。
地图图块的命名规则非常重要。每个地图图块应根据以下约定命名：
```
<mapName>_Tile_<x-coordinate>_<y-coordinate>.fbx
```
请注意，更正的__y坐标__指的是在y轴上更低的图块。例如，`Map01_Tile_0_1`将位于`Map01_Tile_0_0`的正下方。
>>>>>>>>![map_tiles](../img/map_tiles.png)
一个包含由四个图块组成的大型地图的包的`Import`文件夹结构应如下所示：
```sh
Import
│
└── Package01
  ├── Package01.json
  ├── Map01_Tile_0_0.fbx
  ├── Map01_Tile_0_1.fbx
  ├── Map01_Tile_1_0.fbx
  ├── Map01_Tile_1_1.fbx
  └── Map01.xodr
```
!!! Note
    `package.json`文件不是严格必需的。如果没有创建`package.json`文件，自动导入过程将创建一个。有关如何构建自己的`package.json`的更多信息，请参见下一节。
---
## 创建JSON描述（可选）
`.json`描述在导入过程中自动创建，但也可以选择手动创建。现有的`.json`描述将覆盖导入过程中作为参数传递的任何值。
`.json`文件应在包的根文件夹中创建。文件名将是包分发名称。文件内容描述了每个地图和道具的基本信息的JSON数组。
__地图__需要以下参数：
- __name:__ 地图的名称。这必须与`.fbx`和`.xodr`文件相同。
- __xodr:__ `.xodr`文件的路径。
- __use_carla_materials:__ 如果为__True__，地图将使用CARLA材质。否则，它将使用RoadRunner材质。
- __tile_size:__ 图块的大小。默认值是2000（2kmx2km）。
- __tiles:__ 构成整个地图的`.fbx`图块文件列表。
__Props__不包含在本教程中。有关如何添加新道具，请参见[这个](tuto_A_add_props.md)教程。
结果`.json`文件应如下所示：
```json
{
  "maps": [
      {
        "name": "Map01",
        "xodr": "./Map01.xodr",
        "use_carla_materials": true,
        "tile_size": 2000,
        "tiles": [ 
        "./Map01_Tile_0_0.fbx",
        "./Map01_Tile_0_1.fbx",
        "./Map01_Tile_1_0.fbx",
        "./Map01_Tile_1_1.fbx"
        ]
      }
  ],
  "props": []
}
```
</details>
<br>
---
## 进行导入
将所有文件放置在`Import`文件夹后，在CARLA根目录下运行以下命令：
```sh
make import
```
根据您的系统，Unreal Engine可能消耗太多内存而无法一次性导入所有文件。您可以选择通过运行以下命令以MB为单位分批导入文件：
```sh
make import ARGS="--batch-size=200"
```
`make import`命令还有两个标志：
- `--package=<package_name>`指定包的名称。默认情况下，这设置为`map_package`。两个包不能有相同的名称，因此使用默认值会导致后续导入时出现错误。__强烈建议更改包的名称__。使用此标志，请运行以下命令：
```sh
make import  ARGS="--package=<package_name>"
```
- `--no-carla-materials`指定您不希望使用默认的CARLA材质（如道路纹理等）。您将使用RoadRunner材质代替。这个标志__只有在您不提供自己的[`.json`文件](tuto_M_manual_map_package.md)时才需要__。任何`.json文件中的值都将覆盖此标志。使用此标志，请运行以下命令：
```sh
make import ARGS="--no-carla-materials"
```
所有文件都将被导入并准备好在Unreal编辑器中使用。地图包将创建在`Unreal/CarlaUE4/Content`中。将创建一个基础地图图块，`<mapName>`，作为所有图块的流式传输级别。基础图块将包含天空、天气和大型地图演员，并准备好用于模拟。
!!! Note
    目前不推荐在Unreal编辑器中使用为标准地图提供的定制工具，例如道路绘制器、程序化建筑等。
---
## 打包大型地图
要打包您的大型地图以便在CARLA独立包中使用，请运行以下命令：
```sh
make package ARGS="--packages=<mapPackage>"
```
这将创建一个压缩在`.tar.gz`文件中的独立包。文件将保存在Linux上的`Dist`文件夹和Windows上的`/Build/UE4Carla/`中。然后，它们可以被分发并打包以在独立的CARLA包中使用。
---
如果您对大型地图导入和打包过程有任何疑问，可以在[论坛](https://github.com/carla-simulator/carla/discussions)中提问。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>


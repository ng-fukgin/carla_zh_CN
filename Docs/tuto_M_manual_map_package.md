# 手动地图包准备

一个地图包遵循特定的文件夹结构，并且必须包含一个描述该结构的 `.json` 文件。我们的自动地图导入过程会自动创建这个 `.json` 文件，但您也可以选择自行准备。包含自己的 `.json` 文件将覆盖 `make import` 命令中传递的任何参数。

- [手动地图包准备](#手动地图包准备)
  - [标准地图](#标准地图)
    - [创建标准地图的文件夹结构](#创建标准地图的文件夹结构)
    - [创建标准地图的 JSON 描述](#创建标准地图的-json-描述)

---

## 标准地图
### 创建标准地图的文件夹结构

1. 在 `carla/Import` 目录下创建一个文件夹。文件夹的名称不重要。
2. 为每个要导入的地图创建不同的子文件夹。
3. 将每个地图的文件移动到相应的子文件夹中。子文件夹应包含以下元素：
    - 地图的网格文件（`.fbx` 文件）。
    - 地图的 OpenDRIVE 定义文件（`.xodr` 文件）。
    - 可选的资产所需的纹理文件。

例如，一个包含两个地图的 `Import` 文件夹的结构应如下所示：

```plaintext
Import
│
└── Package01
    ├── Package01.json
    ├── Map01
    │   ├── Asphalt1_Diff.jpg
    │   ├── Asphalt1_Norm.jpg
    │   ├── Asphalt1_Spec.jpg
    │   ├── Grass1_Diff.jpg
    │   ├── Grass1_Norm.jpg
    │   ├── Grass1_Spec.jpg
    │   ├── LaneMarking1_Diff.jpg
    │   ├── LaneMarking1_Norm.jpg
    │   ├── LaneMarking1_Spec.jpg
    │   ├── Map01.fbx
    │   └── Map01.xodr
    └── Map02
        └── Map02.fbx
```
### 创建标准地图的 JSON 描述

在地图包的根文件夹中创建一个 `.json` 文件，并以地图包的名称命名该文件。请注意，这将是分发名称。文件的内容将描述一个包含每个地图和资产的基本信息的 JSON 数组。

每个地图需要提供以下参数：
- 地图的名称（`name`），必须与 `.fbx` 和 `.xodr` 文件的名称相同。
- 地图的网格文件路径（`source`）。
- 是否使用 CARLA 材质（`use_carla_materials`）。如果设置为 `True`，地图将使用 CARLA 材质；否则，将使用 RoadRunner 材质。
- 地图的 OpenDRIVE 定义文件路径（`xodr`）。

资产部分不在本教程的范围内，因此 `props` 字段将保留为空。有关如何添加新资产的教程，请参阅其他教程。

生成的 `.json` 文件应如下所示：

``` json
{
  "maps": [
    {
      "name": "Map01",
      "source": "./Map01/Map01.fbx",
      "use_carla_materials": true,
      "xodr": "./Map01/Map01.xodr"
    },
    {
      "name": "Map02",
      "source": "./Map02/Map02.fbx",
      "use_carla_materials": false,
      "xodr": "./Map02/Map02.xodr"
    }
  ],
  "props": []
}
```

如果您对该过程有任何问题，请在 [论坛](https://github.com/carla-simulator/carla/discussions) 中提问。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往 CARLA 论坛">
CARLA 论坛</a>
</p>
</div>

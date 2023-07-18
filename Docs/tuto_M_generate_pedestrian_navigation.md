# 生成行人导航

为了使行人能够在地图上导航，您需要生成一个行人导航文件。本指南详细介绍了要使用的网格以及如何生成该文件。

- [生成行人导航](#生成行人导航)
  - [开始之前](#开始之前)
  - [行人可导航网格](#行人可导航网格)
  - [可选的行人导航选项](#可选的行人导航选项)
  - [生成行人导航](#生成行人导航-1)

---

## 开始之前

在生成行人导航之前，应完成地图定制（添加建筑物、绘制道路、添加景观特征等），以避免干扰或碰撞，并导致需要再次生成行人导航。

---

## 行人可导航网格

行人只能在特定的网格上导航。您需要按照下表中的命名规则为要包含在行人导航中的网格命名：

| 类型 | 包含名称 | 描述 |
|------|------------|-------------|
| 地面 | `Road_Sidewalk` 或 `Roads_Sidewalk` | 行人可以自由走动在这些网格上。 |
| 人行横道 | `Road_Crosswalk` 或 `Roads_Crosswalk` | 如果没有地面可用，行人可以选择走这些网格上的人行横道。 |
| 草地 | `Road_Grass` 或 `Roads_Grass` | 除非您指定一定比例的行人可以走在草地上，否则行人不会走在这个网格上。 |
| 道路 | `Road_Road` 或 `Roads_Road` <br> `Road_Curb` 或 `Roads_Curb` <br> `Road_Gutter` 或 `Roads_Gutter` <br> `Road_Marking` 或 `Roads_Marking` | 行人只会通过这些网格穿越道路。 |

---

## 可选的行人导航选项

下面的步骤不是生成行人导航所必需的，但允许您在一定程度上自定义行人活动。

- __生成新的人行横道__。

如果人行横道在 `.xodr` 文件中已经定义，请避免执行此操作，否则会导致重复定义：

1. 创建一个平面网格，它稍微超出了您想要连接的两个人行道。
2. 将该网格放置在地面上方，并禁用其物理和渲染。
3. 将网格的名称更改为 `Road_Crosswalk` 或 `Roads_Crosswalk`。

![ue_crosswalks](../img/ue_crosswalks.jpg)

---

## 生成行人导航

__1.__ 为了防止地图过大而无法导出，请选择 __BP_Sky 对象__ 并给它添加标签 `NoExport`。如果有其他特别大的网格不参与行人导航，请将 `NoExport` 标签也添加到它们上。

![ue_skybox_no_export](../img/ue_noexport.png)

__2.__ 仔细检查网格名称。网格名称应以下面列出的任何适当格式之一开头，以便被识别为行人可以行走的区域。默认情况下，行人可以在人行道、人行横道和草地上行走（对其他网格的影响较小）：

* 人行道 = `Road_Sidewalk` 或 `Roads_Sidewalk`
* 人行横道 = `Road_Crosswalk` 或 `Roads_Crosswalk`
* 草地 = `Road_Grass` 或 `Roads_Grass`

![ue_meshes](../img/ue_meshes.jpg)

__3.__ 按下 `ctrl + A` 选择所有内容，并通过选择 `File` -> `Carla Exporter` 导出地图。将创建一个 `<mapName>.obj` 文件，保存在 `Unreal/CarlaUE4/Saved` 中。

__4.__ 将 `<mapName>.obj` 和 `<mapName>.xodr` 移动到 `Util/DockerUtils/dist`。

__5.__ 运行以下命令生成导航文件：

* __Windows__
```sh
build.bat <mapName> # <mapName> 不包含扩展名

```
*   __Linux__
```sh
./build.sh <mapName> # <mapName> has no extension
```

__6.__ 一个 `<mapName>.bin` 文件将被创建。该文件包含了地图上行人导航的信息。将此文件移动到包含地图的包的 `Nav` 文件夹中。

__7.__ 通过启动仿真并在 `PythonAPI/examples` 中运行示例脚本 `generate_traffic.py` 来测试行人导航。

---

如果您对该过程有任何问题，请在 [论坛](https://github.com/carla-simulator/carla/discussions) 中提问。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往 CARLA 论坛">
CARLA 论坛</a>
</p>
</div>

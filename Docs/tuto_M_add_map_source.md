# 将地图导入从源码构建的 CARLA

本节介绍将地图导入到 __从源码构建的 CARLA__ 中的过程。如果您使用的是 CARLA 的软件包（二进制）版本，请遵循 [此处][package_ingest] 的指南。

导入过程涉及通过将相关地图文件编译到软件包中来导入它们。然后可以在虚幻引擎编辑器中打开此软件包，在生成行人导航文件并最终将其添加到软件包之前进行自定义。 

[package_ingest]: tuto_M_add_map_package.md

- [__在开始之前__](#before-you-begin)
- [__地图导入__](#map-ingestion)
- [__下一步__](#next-steps)

---

## 在开始之前

- 确保您使用的是从源码构建的 CARLA 版本。如果您使用的是软件包（二进制）版本的 CARLA，请遵循教程 [这里][import_map_package]。
- 您应该至少有两个文件，`<mapName>.xodr` 和 `<mapName>.fbx`，这些文件是从 RoadRunner 等地图编辑器 [生成][rr_generate_map] 的。
- 这些文件应该具有相同的 `<mapName>` 值，以便被识别为同一地图。
- 您可以将多个地图导入到同一个软件包中。每个地图应具有唯一的名称。

[import_map_package]: tuto_M_add_map_package.md
[rr_generate_map]: tuto_M_generate_map.md

---
## 地图导入

__1.__ 将要导入的地图文件放在 CARLA 根目录下的 `Import` 文件夹中。

__2.__ 运行以下命令以导入文件：

```sh
make import
```

__请注意，可以设置两个可选参数标志__：

- `--package=<package_name>` 指定软件包的名称。默认情况下，它被设置为 `map_package`。两个软件包不能具有相同的名称，因此使用默认值会导致后续导入时出现错误。__强烈建议更改软件包的名称__。通过运行以下命令使用此标志：

```sh
make import  ARGS="--package=<package_name>"
```

- `--no-carla-materials` 指定您不想使用默认的 CARLA 材质（道路纹理等）。您将使用 RoadRunner 材质。此标志 __仅在您没有__ 提供自己的 [`.json` 文件](tuto_M_manual_map_package.md) 时才需要。`.json` 文件中的任何值都将覆盖此标志。通过运行以下命令使用此标志：

```sh
make import  ARGS="--no-carla-materials"
```

将在 `Unreal/CarlaUE4/Content` 中创建一个以您的地图包命名的文件夹。它将包含配置文件、OpenDRIVE 信息、静态资产信息和导航信息。

---

## 下一步

现在您已经导入了地图。您可以打开虚幻引擎编辑器，在生成行人导航之前对地图进行任何所需的自定义并进行打包。我们建议按照以下顺序进行：

__1. 自定义地图。__ 您可以为地图添加子图层，添加路灯和交通信号灯，甚至使用程序化建筑物填充景观。
- [添加子图层](tuto_M_custom_layers.md)
- [添加交通灯和标志](tuto_M_custom_add_tl.md)
- [使用程序化建筑物填充景观](tuto_M_custom_buildings.md)
- [自定义天气和景观](tuto_M_custom_weather_landscape.md)

__2. 生成行人导航。__ 
- [生成行人导航](tuto_M_generate_pedestrian_navigation.md)

__3. 打包地图。__
- [打包用于分发的地图](tuto_A_create_standalone.md)

---

如果您对上述内容有任何疑问，请随时在[论坛](https://github.com/carla-simulator/carla/discussions)中反馈。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往 CARLA 论坛">
CARLA 论坛</a>
</p>
</div>

# 将地图摄入CARLA包版本

本节介绍将地图导入到 __CARLA的包（二进制）版本__ 中。 如果你使用的是CARLA的源代码构建版本，请参照[此指南][source_ingest]。

此过程仅适用于Linux系统。 导入过程涉及运行虚幻引擎的Docker映像以导入相关文件，然后将它们导出为可以在CARLA中使用的独立包。 构建Docker映像大约需要4小时和600-700GB硬盘空间。 这仅在第一次构建映像时需要。

- [__在开始之前__](#before-you-begin)
- [__在CARLA包版本中摄入地图__](#map-ingestion-in-a-carla-package)


---

## 在开始之前

- 你需要满足以下系统要求：
     - Ubuntu 16.04及以上的系统以及64位版本的[Docker](https://docs.docker.com/engine/install/)
     - 至少8GB内存
     - 至少700GB可用磁盘空间用于构建Docker映像
     - [Git](https://git-scm.com/downloads) 版本控制
- 确保你使用的是CARLA的包（二进制）版本。如果你使用的是CARLA的源代码构建版本，请参照[此指南][source_ingest]。
- 你应该至少有两个文件，`<mapName>.xodr` 和 `<mapName>.fbx`，它们是从地图编辑器（例如RoadRunner）[生成][rr_generate_map]的。
- 这些文件应该具有相同的 `<mapName>` 文件名，以便识别为相同的地图。

[source_ingest]: tuto_M_add_map_source.md
[import_map_package]: tuto_M_add_map_package.md
[rr_generate_map]: tuto_M_generate_map.md


---
## 在CARLA包版本中摄入地图

__1.__ CARLA在Github中提供了所有用于构建虚幻引擎Docker映像以及使用Docker映像编译CARLA的工具。使用以下命令克隆git仓库：

```sh
    git clone https://github.com/carla-simulator/carla
```

__2.__ 按照[说明](https://github.com/carla-simulator/carla/tree/master/Util/Docker)构建虚幻引擎的Docker映像。

__3.__ 创建一个`input_folder`文件夹用于放置你需要导入的文件。 Docker将自动创建一个描述文件夹结构的`.json`文件。 更改`input_folder`的权限以确保能够成功创建：

```sh
    # 在input_folder的上一层文件夹执行以下命令
    chmod 777 input_folder
```

> !!! Note
    如果地图包是[手动准备](tuto_M_manual_map_package.md)好的，并且已经包含一个`.json`文件，那么`3`步骤不是必需的。

__4.__ 创建一个`output_folder`文件夹。 这是Docker映像在完成地图烘焙(cooked the map)之后输出文件的位置。

__5.__ 切换到`~/carla/Util/Docker`目录。这是地图摄入脚本所在的位置。该脚本需要 `input_folder` 和 `output_folder`的路径以及要摄入的地图包的名称。如果提供了`.json`文件，则该文件的文件名是地图包的名称，如果没有提供`.json`，则地图包的名称必须是`map_package`：

```sh
    python3 docker_tools.py --input ~/path_to_input_folder --output ~/path_to_output_folder --packages map_package
```

> !!! Warning
    如果没有提供`--packages map_package`参数, Docker映像将构建一个CARLA包版本。 

__6.__ `output_folder`文件夹中将生成`<map_package>.tar.gz`的地图包文件。这是可用于导入CARLA的独立包。 将地图包移动到CARLA根目录中的`Import`文件夹（你将在CARLA的包/二进制版本中使用此地图），然后从CARLA根目录运行以下脚本来导入它：

```sh
        ./ImportAssets.sh
```

__7.__ 要使用新地图，请运行CARLA，然后使用`config.py`文件更换地图：

```sh
    cd PythonAPI/util
    python3 config.py --map <mapName>
```
<br>

---

你的地图现在应该可以在CARLA中运行。 如果你对以上过程有任何疑问，可以在[论坛](https://github.com/carla-simulator/carla/discussions)中提出，或者你可以尝试运行我们的一些[示例脚本](https://github.com/carla-simulator/carla/tree/master/PythonAPI/examples)在你的新地图上进行测试。

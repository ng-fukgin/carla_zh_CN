
# 创建语义标签
学习如何定义用于语义分割的自定义标签。这些标签还可以添加到 [carla.CityObjectLabel](python_api.md#carla.CityObjectLabel) 中，以过滤 [carla.World](python_api.md#carla.World) 获取的边界框。
- [创建语义标签](#创建语义标签)
	- [创建一个新的语义标签](#创建一个新的语义标签)
		- [1. 创建标签ID](#1-创建标签id)
		- [2. 为资产创建UE文件夹](#2-为资产创建ue文件夹)
		- [3. 在UE和代码标签之间建立双向对应关系](#3-在ue和代码标签之间建立双向对应关系)
		- [4. 定义颜色代码](#4-定义颜色代码)
		- [5. 添加标记网格](#5-添加标记网格)
	- [新的语义标签已准备好使用。只有存储在标签UE文件夹中的网格才会被标记为相应标签。移动或导入相应的网格到新文件夹，以便正确标记。](#新的语义标签已准备好使用只有存储在标签ue文件夹中的网格才会被标记为相应标签移动或导入相应的网格到新文件夹以便正确标记)
	- [将标签添加到 carla.CityObjectLabel](#将标签添加到-carlacityobjectlabel)
	- [](#)
---
## 创建一个新的语义标签
### 1. 创建标签ID
__打开 `LibCarla/source/carla/rpc` 中的 `ObjectLabel.h` 文件。__ 在枚举的末尾添加您的标签，使用与其他标签相同的格式。
![object_label_h](img/tuto_D_create_semantic_tags/01_objectlabel_tag.jpg)
!!! Note
    标签不需要按顺序排列。但是，按顺序列出它们是一种好习惯。
### 2. 为资产创建UE文件夹
__打开Unreal Engine编辑器__，转到 `Carla/Static`。创建一个与您的标签同名的文件夹。
![ue_folder](img/tuto_D_create_semantic_tags/02_ue_folder.jpg)
!!! Note
    UE文件夹和标签不必同名。但是，这样做是一种好习惯。
### 3. 在UE和代码标签之间建立双向对应关系
__3.1. 打开 `Unreal/CarlaUE4/Plugins/Carla/Source/Carla/Game` 中的 `Tagger.cpp` 文件。__ 转到 __`GetLabelByFolderName`__ 在列表末尾添加您的标签。这里比较的字符串是 [__2.__](#2-为资产创建UE文件夹) 中使用的UE文件夹的名称，因此在这里使用完全相同的名称。
![tagger_cpp](img/tuto_D_create_semantic_tags/03_tagger_cpp.jpg)
__3.2. 在同一个 `Tagger.cpp` 文件中转到 `GetTagAsString`。__ 在switch语句的末尾添加新标签。
![tagger_cpp_02](img/tuto_D_create_semantic_tags/04_tagger_cpp_02.jpg)
### 4. 定义颜色代码
__打开 `LibCarla/source/carla/image` 中的 `CityScapesPalette.h` 文件。__ 在数组的末尾添加您新标签的颜色代码。
![city_scapes_palette_h](img/tuto_D_create_semantic_tags/05_city_scapes_palette_h.jpg)
!!! Warning
    数组中的位置必须与标签ID相对应，在这种情况下是 `23u`。
### 5. 添加标记网格
新的语义标签已准备好使用。只有存储在标签UE文件夹中的网格才会被标记为相应标签。移动或导入相应的网格到新文件夹，以便正确标记。
---
## 将标签添加到 [carla.CityObjectLabel](python_api.md#carla.CityObjectLabel)
这一步与语义分割没有直接关系。然而，这些标签可以用于在 [carla.World](python_api.md#carla.World) 中过滤边界框查询。为此，必须将标签添加到 PythonAPI 中的 [carla.CityObjectLabel](python_api.md#carla.CityObjectLabel) 枚举。
__打开 `carla/PythonAPI/carla/source/libcarla` 中的 `World.cpp` 文件，__ 在枚举的末尾添加新标签。
![city_object_label](img/tuto_D_create_semantic_tags/06_city_object_label.jpg)
---
阅读 **[常见问题解答](build_faq.md)** 页面或在 [CARLA论坛](https://github.com/carla-simulator/carla/discussions) 上发帖，以解决任何问题、疑问或建议。
<p style="font-size: 20px">接下来是什么？</p>
<div class="build-buttons">
<p>
<a href="../ref_sensors" target="_blank" class="btn btn-neutral" title="了解CARLA中传感器的所有信息">
传感器参考</a>
</p>
<p>
<a href="../tuto_A_add_props" target="_blank" class="btn btn-neutral" title="了解如何将您的自定义内容导入CARLA">
添加新道具</a>
</p>
</div>

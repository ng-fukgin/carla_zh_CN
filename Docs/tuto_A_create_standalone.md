# 创建资产分发包
在CARLA中，使用独立的包来管理资产是一种常见做法。将它们分开可以减少构建的大小。这些资产包可以随时轻松地导入到CARLA包中。它们还有助于以有组织的方式轻松分发资产。
- [从UE4编辑器导出包](#从ue4编辑器导出包)
- [使用Docker导出包](#使用docker导出包)
- [将资产导入CARLA包](#将资产导入carla包)
---
## 从源代码构建的CARLA中导出包
一旦资产被导入到Unreal中，用户可以为它们生成一个__独立包__。这将用于将内容分发到CARLA包，如0.9.8。
要导出包，只需运行以下命令。
```sh
make package ARGS="--packages=Package1,Package2"
```
这将为列表中列出的每个包创建一个压缩在`.tar.gz`文件中的独立包。在Linux上，文件将保存在`Dist`文件夹中，在Windows上，将保存在`/Build/UE4Carla/`。
---
## 使用Docker导出包
Unreal Engine和CARLA可以在Docker镜像中构建，然后使用该镜像创建包或导出资产以在包中使用。
要创建Docker镜像，请遵循[这里](build_docker_unreal.md)的教程。
当您准备好镜像后：
1. 导航到`Util/Docker`。
2. 通过运行以下任一命令来创建CARLA包或准备在包中使用的资产：
```sh
# 创建独立包
./docker_tools.py --output /output/path
# 烹饪资产以供CARLA包使用
./docker_tools.py --input /assets/to/import/path --output /output/path --packages PkgeName1,PkgeName2
```
---
## 将资产导入CARLA包
独立包包含在`.tar.gz`文件中。提取此文件的方式取决于平台。
*   在__Windows__上，将压缩文件解压到CARLA的主根文件夹中。
*   在__Linux__上，将压缩文件移动到`Import`文件夹并运行以下脚本。
```sh
cd Import
./ImportAssets.sh
```
!!! Note
    独立包不能直接导入到CARLA构建中。请遵循教程来导入[道具](tuto_A_add_props.md)、[地图](tuto_M_custom_map_overview.md)或[车辆](tuto_A_add_vehicle.md)。
---
这就是如何在CARLA中创建和使用独立包的方法。如果遇到任何意外问题，请随时在论坛上发帖。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>

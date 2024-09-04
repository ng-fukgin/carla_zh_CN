# Chrono集成
本指南概述了Chrono是什么，如何在CARLA中使用它，以及集成中涉及的局限性。
- [__Chrono项目__](#project-chrono)
- [__在CARLA中使用Chrono__](#using-chrono-on-carla)
    - [配置服务器](#configuring-the-server)
    - [启用Chrono物理](#enabling-chrono-physics)
- [__局限性__](#limitations)
---
## Chrono项目
[Chrono项目](https://projectchrono.org/)是一个开源的多物理模拟引擎，它使用基于模板的方法提供高度真实的车辆动力学。在CARLA中的集成允许用户在导航地图时利用Chrono模板来模拟车辆动力学。
---
## 在CARLA中使用Chrono
要使用Chrono集成，您必须首先在启动时配置带有标签的服务器，然后使用PythonAPI在生成的车辆上启用它。详情请继续阅读。
### 配置服务器
只有当CARLA服务器编译时带有Chrono标签，Chrono才能工作。
__在CARLA的源代码构建版本中__，运行以下命令来启动服务器：
```sh
make launch ARGS="--chrono"
```
---
### 启用Chrono物理
Chrono物理是通过[Actor](python_api.md#carlaactor)类中的`enable_chrono_physics`方法启用的。除了子步骤和子步骤时间增量值外，它还需要三个模板文件和一个用于定位这些文件的基本路径：
- __`base_path`:__ 包含模板文件的目录路径。这是必要的，以确保模板文件中引用的辅助文件有一个共同的基路径来搜索。
- __`vehicle_json`:__ 相对于`base_path`的车辆模板文件路径。
- __`tire_json`:__ 相对于`base_path`的轮胎模板文件路径。
- __`powertrain_json`:__ 相对于`base_path`的动力系统模板文件路径。
!!! Important
    请仔细检查您的路径。不正确或缺失的路径可能导致Unreal Engine崩溃。
在`Build/chrono-install/share/chrono/data/vehicle`中有各种不同车辆的示例模板文件。阅读Project Chrono的[文档](https://api.projectchrono.org/manual_vehicle.html)了解更多关于他们的车辆示例以及如何创建模板。
以下是如何启用Chrono物理的示例：
```python
    # 生成您的车辆
    vehicle = world.spawn_actor(bp, spawn_point)
    # 设置基本路径
    base_path = "/path/to/carla/Build/chrono-install/share/chrono/data/vehicle/"
    # 设置模板文件
    vehicle_json = "sedan/vehicle/Sedan_Vehicle.json"
    powertrain_json = "sedan/powertrain/Sedan_SimpleMapPowertrain.json"
    tire_json = "sedan/tire/Sedan_TMeasyTire.json"
    # 启用Chrono物理
    vehicle.enable_chrono_physics(5000, 0.002, vehicle_json, powertrain_json, tire_json, base_path)
```
您可以使用`PythonAPI/examples`中的示例脚本`manual_control_chrono.py`来尝试Chrono物理集成。运行脚本后，按`Ctrl + o`启用Chrono。
---
### 局限性
此集成不支持碰撞。__当发生碰撞时，车辆将恢复为CARLA默认物理。__

# RSS
CARLA在客户端库中集成了[C++ Library for Responsibility Sensitive Safety](https://github.com/intel/ad-rss-lib)，这项功能允许用户在不实现任何东西的情况下调查RSS的行为。CARLA将负责提供输入，并将输出应用到AD系统上。
*   [__概述__](#overview)
*   [__编译__](#compilation)
	*   [依赖项](#dependencies)
	*   [构建](#build)
*	[__当前状态__](#current-state)
	*   [RssSensor](#rsssensor)
	*   [RssRestrictor](#rssrestrictor)
!!! Important
    这项功能仍在开发中。目前，它只适用于Linux构建。
---
## 概述
RSS库实现了一个用于安全保证的数学模型。它接收传感器信息，并为车辆的控制提供限制。概括来说，RSS模块使用传感器数据来定义__情境__。一个情境描述了自我车辆与环境元素的状态。对于每个情境，都会进行安全检查，并计算适当的响应。总体响应是所有情境组合的结果。有关库的特定信息，请阅读[文档](https://intel.github.io/ad-rss-lib/)，特别是[背景部分](https://intel.github.io/ad-rss-lib/ad_rss/Overview/)。
在CARLA中，这是通过以下两个元素实现的。
*	__RssSensor__ 负责情境分析和使用 *ad-rss-lib* 生成响应。
*	__RssRestrictor__ 通过限制车辆的命令来应用响应。
以下图像概述了__RSS__集成到CARLA架构中。
![Interate RSS into CARLA](img/rss_carla_integration_architecture.png)
__1. 服务器。__
- 向客户端发送相机图像。<small>（仅当客户端需要可视化时）。</small>
- 向RssSensor提供世界数据。
- 向RssRestrictor发送车辆的物理模型。<small>（仅当默认值被覆盖时）。</small>
__2. 客户端。__
- 向 *RssSensor* 提供一些要考虑的[参数](https://intel.github.io/ad-rss-lib/ad_rss/Appendix-ParameterDiscussion/)。
- 向 *RssResrictor* 发送一个初始的 [carla.VehicleControl](python_api.md#carla.VehicleControl)。
__3. RssSensor。__
- 使用 *ad-rss-lib* 提取情境，进行安全检查，并生成响应。
- 向 *RssRestrictor* 发送一个包含适当响应和要应用的加速度限制的响应。
__4. RssRestrictor__
- 如果客户端要求，将响应应用于 [carla.VehicleControl](python_api.md#carla.VehicleControl)，并返回结果。
[![CARLA中的RSS传感器](img/rss_carla_integration.png)](https://www.youtube.com/watch?v=UxKPXPT2T8Q)
<div style="text-align: right"><i>RssSensor结果的可视化。</i></div>
---
## 编译
RSS集成需要单独于CARLA的其他部分进行构建。__ad-rss-lib__ 带有一个LGPL-2.1开源许可证，这会产生冲突。它必须静态链接到 *libCarla*。
提醒一下，目前这项功能只适用于Linux构建。
### 依赖项
构建RSS及其依赖项需要额外的先决条件。请查看[官方文档](https://intel.github.io/ad-rss-lib/BUILDING)以了解更多信息。
Ubuntu (>= 16.04) 提供的依赖项。
```sh
sudo apt-get install libgtest-dev libpython-dev libpugixml-dev libtbb-dev
```
依赖项是使用 [colcon](https://colcon.readthedocs.io/en/released/user/installation.html) 构建的，因此必须安装它。
```sh
pip3 install --user -U colcon-common-extensions
```
Python绑定还有一些额外的依赖项。
```sh
sudo apt-get install castxml
pip3 install --user pygccxml pyplusplus
```
### 构建
完成这些步骤后，可以构建完整的依赖项集和RSS组件。
*	编译LibCarla以与RSS一起工作。
```sh
make LibCarla.client.rss
```
*	编译PythonAPI以包含RSS功能。
```sh
make PythonAPI.rss
```
*	作为替代方案，可以直接构建一个包。
```sh
make package.rss
```
---
## 当前状态
### RssSensor
[__carla.RssSensor__](python_api.md#carla.RssSensor) 完全支持 [ad-rss-lib v4.2.0 功能集](https://intel.github.io/ad-rss-lib/RELEASE_NOTES_AND_DISCLAIMERS)，包括路口、[保持在路上](https://intel.github.io/ad-rss-lib/ad_rss_map_integration/HandleRoadBoundaries/) 的支持以及 [非结构化星座（例如与行人）](https://intel.github.io/ad-rss-lib/ad_rss/UnstructuredConstellations/)。
到目前为止，服务器为传感器提供周围环境的地面真实数据，包括其他交通参与者的状态和交通灯。
### RssRestrictor
当客户端调用时，[__carla.RssRestrictor__](python_api.md#carla.RssRestrictor) 将修改车辆控制器，以最佳方式达到给定的响应所要求的加速度或减速度。
由于 [carla.VehicleControl](python_api.md#carla.VehicleControl) 对象的结构，所应用的限制有一定的局限性。这些控制器包括 `油门`、`刹车` 和 `转向` 值。然而，由于车辆物理和简单的控制选项，这些可能无法满足。限制在横向方向上简单地通过朝平行车道方向的反向转向来干预。如果RSS请求减速，将激活刹车。这取决于车辆质量和平由 [carla.Vehicle](python_api.md#carla.Vehicle) 提供的刹车扭矩。
!!! Note
    在自动车辆控制器中，可能可以调整计划轨迹以适应限制。可以使用快速控制循环（>1KHz）来确保遵循这些限制。
---
这就是关于CARLA中RSS传感器的基本信息。有关特定属性和参数的更多信息，请参阅[传感器参考](ref_sensors.md#rss-sensor)。
打开CARLA并尝试一段时间。如果有任何疑问，请随时在论坛上发布。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>


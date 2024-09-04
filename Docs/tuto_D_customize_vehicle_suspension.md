# 自定义车辆悬挂
本教程涵盖了CARLA车辆悬挂系统的基础知识，以及这些悬挂系统如何为不同的车辆实现。使用这些信息可以在Unreal Engine中访问车辆的悬挂参数化，并随意进行自定义。
*   [__悬挂系统基础知识__](#悬挂系统基础知识)
*   [__悬挂组__](#悬挂组)
    *   [Coupe](#coupe)
    *   [Off-road](#off-road)
    *   [Truck](#truck)
    *   [Urban](#urban)
    *   [Van](#van)
---
## 悬挂系统基础知识
车辆的悬挂系统由车辆的车轮定义。每个车轮都有一个带有一些参数化的独立蓝图，其中包括悬挂系统。
这些蓝图可以在 `Content/Carla/Blueprints/Vehicles/<vehicle_name>` 中找到。它们的命名方式如下：`BP_<vehicle_name>_<F/R><R/L>W`。
*   `F` 或 `R` 分别用于前轮或后轮。
*   `R` 或 `L` 分别用于右轮或左轮。
![tuto_suspension_blueprints](img/tuto_suspension_blueprints.jpg)
<div style="text-align: right"><i>在这个例子中，Audi A2的前左轮蓝图被命名为 <code>BP_AudiA2_FLW</code>。</i></div>
`shape_radius` 用于车轮在道路上休息，既不悬空也不陷入地面。
在蓝图内部，有一个关于车轮悬挂的参数化部分。以下是Unreal Engine中描述的定义。
*   `Suspension Force Offset` — 悬挂力作用的垂直偏移（沿Z轴）。
*   `Suspension Max Raise` — 车轮可以高于休息位置的距离。
*   `Suspension Max Drop` — 车轮可以低于休息位置的距离。
*   `Suspension Natural Frequency` — 悬挂的振荡频率。标准汽车的价值在 `5` 到 `10` 之间。
*   `Suspension Damping Ratio` — 从弹簧中消散能量的速率。标准汽车的价值在 `0.8` 到 `1.2` 之间。值 `<1` 更迟缓，值 `>1` 更敏感。
*   `Sweep Type` — 车轮悬挂是否考虑简单、复杂或两者。
![tuto_suspension_parameterization](img/tuto_suspension_parameterization.jpg)
<div style="text-align: right"><i>车轮蓝图中的悬挂面板。</i></div>
!!! Note
    默认情况下，CARLA中一个车辆的所有车轮都具有相同的参数化。以下解释将按车辆而不是按车轮进行。
---
## 悬挂组
根据它们的悬挂系统，CARLA中的车辆可以分为五组。一个组中的所有车辆都有相同的参数化，因为它们预计在路上会有类似的行为。车辆的悬挂可以随意修改，不受这五个组的限制。然而，了解这些组并观察模拟中的它们的行为对于定义自定义悬挂非常有用。
这五个组是：*Coupe*, *Off-road*, *Truck*, *Urban*, 和 *Van*。更仔细地观察，这些组的参数化遵循特定的模式。
| 硬悬挂 | Coupe | Urban | Van | Off-road | Truck | 软悬挂 |
| --- | --- | --- | --- | --- | --- | --- |
<br>
当从软悬挂移动到硬悬挂时，参数化中存在一些明显的趋势。
*   `Suspension Max Raise` 和 `Suspension Max Drop` 的 __减少__ — 硬悬挂车辆旨在在没有颠簸的平坦道路上行驶。为了空气动力学，底盘不应该大幅移动，而应始终靠近地面。
*   `Suspension Damping Ratio` 的 __增加__ — 硬悬挂车辆的减震器吸收弹跳的能力更强。
### Coupe
具有最硬悬挂的车辆。
| 参数化 | 车辆 |
| -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `Suspension Force Offset` — `0.0`<br>`Suspension Max Raise` — `7.5`<br>`Suspension Max Drop` — `7.5`<br>`Suspension Natural Frequency` — `9.5`<br>`Suspension Damping Ratio` — `1.0`<br>`Sweep Type` — `SimpleAndComplex`<br> | `vehicle.audi.tt`<br>`vehicle.lincoln.mkz2017`<br>`vehicle.mercedes-benz.coupe`<br>`vehicle.seat.leon`<br>`vehicle.tesla.model3`<br> |
### Off-road
具有软悬挂的车辆。
| 参数化 | 车辆 |
| ----------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `Suspension Force Offset` — `0.0`<br>`Suspension Max Raise` — `15.0`<br>```
`Suspension Max Drop` — `15.0`<br>`Suspension Natural Frequency` — `7.0`<br>`Suspension Damping Ratio` — `0.5`<br>`Sweep Type` — `SimpleAndComplex`<br> | `vehicle.audi.etron`<br>`vehicle.jeep.wrangler_rubicon`<br>`vehicle.nissan.patrol`<br>`vehicle.tesla.cybertruck`<br> |
### Truck
具有最软悬挂的车辆。
| 参数化 | 车辆 |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Suspension Force Offset` — `0.0`<br>`Suspension Max Raise` — `17.0`<br>`Suspension Max Drop` — `17.0`<br>`Suspension Natural Frequency` — `6.0`<br>`Suspension Damping Ratio` — `0.4`<br>`Sweep Type` — `SimpleAndComplex`<br> | `vehicle.carlamotors.carlacola`<br> |
### Urban
具有软悬挂的车辆。
| 参数化 | 车辆 |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Suspension Force Offset` — `0.0`<br>`Suspension Max Raise` — `8.0`<br>`Suspension Max Drop` — `8.0`<br>`Suspension Natural Frequency` — `9.0`<br>`Suspension Damping Ratio` — `0.8`<br>`Sweep Type` — `SimpleAndComplex`<br> | `vehicle.audi.a2`<br>`vehicle.bmw.grandtourer`<br>`vehicle.chevrolet.impala`<br>`vehicle.citroen.c3`<br>`vehicle.dodge_charger.police`<br>`vehicle.mini.cooperst`<br>`vehicle.mustang.mustang`<br>`vehicle.nissan.micra`<br>`vehicle.toyota.prius`<br> |
### Van
具有中等悬挂的车辆。
| 参数化 | 车辆 |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Suspension Force Offset` — `0.0`<br>`Suspension Max Raise` — `9.0`<br>`Suspension Max Drop` — `9.0`<br>`Suspension Natural Frequency` — `8.0`<br>`Suspension Damping Ratio` — `0.8`<br>`Sweep Type` — `SimpleAndComplex`<br> | `vehicle.volkswagen.t2`<br> |
---
使用论坛发布关于此主题的任何疑问、问题或建议。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>
以下是此后的建议阅读。
<div class="build-buttons">
<p>
<a href="../tuto_G_control_vehicle_physics" target="_blank" class="btn btn-neutral" title="设置车辆物理的运行时变化。">
控制车辆物理</a>
</p>
<p>
<a href="../tuto_G_add_friction_triggers" target="_blank" class="btn btn-neutral" title="定义车轮的动态盒触发器。">
添加摩擦触发器</a>
</p>
<p>
<a href="../tuto_D_generate_colliders" target="_blank" class="btn btn-neutral" title="为车辆创建详细的碰撞器">
生成详细碰撞器</a>
</p>
</div>


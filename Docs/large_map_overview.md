# 大型地图概览
- [__大型地图概览__](#large-maps-overview)
- [__图块流式传输__](#tile-streaming)
- [__休眠演员__](#dormant-actors)
---
## 大型地图概览
CARLA中的大型地图功能允许用户在广阔的规模上进行模拟。在CARLA中，大型地图被划分为不超过2kmx2km的方形图块。图块根据它们与 ego 车辆的接近程度（流式传输距离）被加载到服务器中或从服务器中卸载。地图上的其他演员也根据它们与ego车辆的流式传输距离来管理。
---
## 图块流式传输
ego车辆是加载和卸载地图图块的关键。图块根据ego车辆流式传输距离的值被加载到服务器中或从服务器中卸载。例如，位于流式传输距离之外的图块将不会在模拟中渲染，而位于流式传输距离之内的图块将被渲染。随着主角车辆移动，渲染的图块将发生变化。
要将车辆设置为ego，请使用如下所示的[`set_attribute`](python_api.md#carla.ActorBlueprint.set_attribute)方法：
```py
blueprint.set_attribute('role_name', 'hero')
world.spawn_actor(blueprint, spawn_point)
```
使用下面的代码片段来设置流式传输距离，以便在ego车辆周围2km范围内加载图块：
```py
settings = world.get_settings()
settings.tile_stream_distance = 2000
world.apply_settings(settings)
```
您也可以使用`config.py`来设置流式传输距离：
```sh
cd PythonAPI/util
python3 config.py --tile-stream-distance 2000
```
!!! Note
    大型地图目前只支持同时存在一个ego车辆。
---
## 休眠演员
大型地图功能向CARLA引入了休眠演员的概念。休眠演员仅在大型地图的上下文中存在。休眠演员是指模拟中位于ego车辆__演员活动距离__之外的的非ego车辆演员，例如，远离ego车辆的车辆。演员活动距离可以等于或小于流式传输距离。
如果一个演员发现自己位于ego车辆的演员活动距离之外，它将变为休眠状态。演员仍然存在，但不会渲染。不会计算物理（除非通过交通管理器以混合模式运行），尽管仍然可以设置[位置](python_api.md#carla.Actor.set_location)和[变换](python_api.md#carla.Actor.set_transform)。一旦休眠演员再次进入ego车辆的演员活动距离范围内，它将唤醒，其渲染和物理将恢复正常。
由交通管理器控制的演员在大型地图中操作时具有可以配置的不同行为。更多信息请阅读[交通管理器文档](adv_traffic_manager.md#traffic-manager-in-large-maps)了解这是如何工作的。
演员将在[`world.tick()`](python_api.md#carla.World.tick)时变为休眠状态或唤醒。
要将演员活动距离设置为ego车辆周围2km范围内：
```py
settings = world.get_settings()
settings.actor_active_distance = 2000
world.apply_settings(settings)
```
您也可以使用`config.py`来设置演员活动距离：
```sh
cd PythonAPI/util
python3 config.py --actor-active-distance 2000
```
要检查一个演员是否处于休眠状态，您可以使用Python API：
```py
actor.is_dormant
```
---
如果您对大型地图有任何疑问，可以在[论坛](https://github.com/carla-simulator/carla/discussions)中提问。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>

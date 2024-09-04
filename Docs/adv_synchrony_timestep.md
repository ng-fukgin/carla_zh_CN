# 同步和时间步
本部分讨论CARLA中的两个基本概念。它们的配置定义了仿真中时间是如何流逝的，以及服务器如何使仿真向前推进。
- [同步和时间步](#同步和时间步)
	- [仿真时间步长](#仿真时间步长)
		- [可变时间步长](#可变时间步长)
		- [固定时间步长](#固定时间步长)
		- [记录仿真时的提示](#记录仿真时的提示)
		- [物理细分](#物理细分)
	- [客户端-服务器同步性](#客户端-服务器同步性)
		- [设置同步模式](#设置同步模式)
		- [使用同步模式](#使用同步模式)
	- [可能的配置](#可能的配置)
	- [物理确定性](#物理确定性)
	- [运行这些步骤将确保每次仿真运行都产生相同的结果。](#运行这些步骤将确保每次仿真运行都产生相同的结果)
---
## 仿真时间步长
真实时间和仿真时间之间存在差异。仿真世界有自己的时钟和时间，由服务器控制。计算两个仿真步骤需要一些真实时间。然而，这两个仿真时刻之间的时间跨度，即时间步长，是可以配置的。
例如，服务器可能需要几毫秒来计算两个仿真步骤。但是，这两个仿真时刻之间的时间跨度可以配置为始终为1秒。
时间步长可以是固定的或可变的，取决于用户偏好。
!!! Note
    时间步长和同步性是交织的概念。确保阅读两个部分以全面理解CARLA是如何工作的。
### 可变时间步长
CARLA的默认模式。仿真时间步长将根据服务器计算这些步骤所需的时间而变化。
```py
settings = world.get_settings()
settings.fixed_delta_seconds = None # 设置可变时间步长
world.apply_settings(settings)
```
`PythonAPI/util/config.py`使用一个参数设置时间步长。零等于可变时间步长。
```sh
cd PythonAPI/util && python3 config.py --delta-seconds 0
``` 
### 固定时间步长
步骤之间的时间跨度保持不变。如果设置为0.5秒，则每秒会有两个帧。在每一步使用相同的时间增量是收集仿真数据的最佳方式。物理和传感器数据将与仿真中的一个容易理解的时刻相对应。此外，如果服务器足够快，它将能够在较少的真实时间内模拟更长的时间段。
可以在世界设置中设置固定的时间步长。要以0.05秒的固定时间步长运行仿真，请应用以下设置。在这种情况下，模拟器将需要20步（1/0.05）来重现仿真世界的1秒。
```py
settings = world.get_settings()
settings.fixed_delta_seconds = 0.05
world.apply_settings(settings)
```
这也可以通过提供的脚本`PythonAPI/util/config.py`设置。
```sh
cd PythonAPI/util && python3 config.py --delta-seconds 0.05
``` 
### 记录仿真时的提示
CARLA有一个[记录器功能](adv_recorder.md)，允许仿真被记录并重新执行。然而，为了精确，需要考虑一些事情。
* 使用__固定时间步长__时，重新执行将很容易。服务器可以设置为使用原始仿真中使用的时间步长。
* 使用__可变时间步长__时，情况稍微复杂一些。
	* 如果__服务器以可变时间步长运行__，时间步长将与原始的一个不同，因为逻辑周期每次都不同。然后，将使用记录的数据进行插值。
	* 如果__服务器被迫以完全相同的时间步长重现__，步骤将是相同的，但它们之间的真实时间不同。时间步长应逐一传递。这些原始时间步长是原始仿真尽可能快地运行的结果。由于代表这些的时间大部分不同，仿真很可能会以奇怪的时间波动重新执行。
	* 还有由可变时间步长引入的__浮点数算术误差__。仿真以与真实时间相等的时间步长运行。真实时间是连续变量，在仿真中以`float`值表示，具有小数限制。每一步截断的时间累积，并阻止仿真精确地重复已经发生的事情。
### 物理细分
为了精确，物理必须在非常低的时间步长内计算。当选择仿真时间步长时，我们通常在每个帧上执行多个计算，例如传感器渲染，这可能会成为一个问题。由于这个限制仅发生在物理仿真中，我们可以将物理计算应用于子步。这是默认启用，并且设置最多10个物理细分步，最大物理细分时间为0.01。
这些选项可以通过API在世界设置中更改。
```py
settings = world.get_settings()
settings.substepping = True
settings.max_substep_delta_time = 0.01
settings.max_substeps = 10
world.apply_settings(settings)
```
请注意，如果启用了同步模式并且设置了固定的时间步长，细分选项需要与固定时间步长的值一致。需要满足的条件是：
```py
fixed_delta_seconds <= max_substep_delta_time * max_substeps
```
为了获得最佳的物理仿真，细分时间步长至少应低于`0.01666`，理想情况下应低于`0.01`。
为了演示物理细分的效果，考虑以下图形。第一个图形显示了在不同固定仿真时间步长下的速度随时间变化。物理细分时间在所有仿真中都是默认值`0.01`。我们可以看到，速度仅由仿真时间步长的差异决定。
>>>>>![固定物理细分时间](../img/physics_convergence_fixed_pdt.png)
第二个图形显示了在固定仿真时间步长为`0.04`下的速度随时间变化。我们可以看到，一旦物理细分时间超过`0.01`，速度的恒定性开始出现偏差，随着物理细分时间的增加，偏差变得更加严重。
>>>>>![变化的物理细分时间](../img/physics_convergence_fixed_dt.png)
我们再次通过显示在固定仿真时间步长下物理细分时间对z轴加速度测量的相同差异的影响来演示这一点，只有当物理细分时间为`0.01`或更小时，才会发生收敛。
>>>>>![物理收敛z加速度](../img/physics_convergence_z_acceleration.png)
---
## 客户端-服务器同步性
CARLA建立在客户端-服务器架构之上。服务器运行仿真。客户端检索信息，并请求对世界的更改。本节讨论客户端与服务器之间的通信。
默认情况下，CARLA以__异步模式__运行。服务器以尽可能快的速度运行仿真，不等待客户端。在__同步模式__下，服务器在接收客户端的“准备就绪”消息后等待更新到下一个仿真步骤。
!!! Note
    在多客户端架构中，只有一个客户端应该进行滴答。服务器对收到的每个滴答做出反应，好像它来自同一个客户端。多个客户端滴答会使服务器和客户端之间产生不一致。
### 设置同步模式
在同步模式和异步模式之间切换只是布尔状态的问题。
```py
settings = world.get_settings()
settings.synchronous_mode = True # 启用同步模式
world.apply_settings(settings)
```
!!! Warning
    如果启用了同步模式，并且有运行的Traffic Manager，也必须将其设置为同步模式。阅读[此](adv_traffic_manager.md#同步模式)以了解如何操作。
要禁用同步模式，只需将变量设置为false或使用脚本`PythonAPI/util/config.py`。
```sh
cd PythonAPI/util && python3 config.py --no-sync # 禁用同步模式
``` 
同步模式无法通过脚本启用，只能禁用。启用同步模式会使服务器等待客户端的滴答。使用此脚本，用户无法在需要时发送滴答。
### 使用同步模式
同步模式对于慢速客户端应用程序和需要不同元素（如传感器）之间同步性时变得特别重要。如果客户端太慢，服务器不等待，信息将溢出。客户端将无法管理所有信息，并且会丢失或混淆。类似地，对于许多传感器和异步性，将无法知道所有传感器是否使用仿真中的相同时刻的数据。
以下代码片段扩展了前面的一个。客户端创建了一个相机传感器，将当前步骤的图像数据存储在队列中，并在从队列中获取它后对服务器进行滴答。有关多个传感器的更复杂示例，请参阅[此处][syncmodelink]。
```py
settings = world.get_settings()
settings.synchronous_mode = True
world.apply_settings(settings)
camera = world.spawn_actor(blueprint, transform)
image_queue = queue.Queue()
camera.listen(image_queue.put)
while True:
    world.tick()
    image = image_queue.get()
```
[syncmodelink]: https://github.com/carla-simulator/carla/blob/master/PythonAPI/examples/synchronous_mode.py
!!! Important
    来自GPU的传感器（主要是相机）的数据显示通常会有几帧的延迟。在这种情况下，同步性至关重要。
世界具有异步方法，使客户端等待服务器滴答，或在收到滴答时执行某些操作。
```py
# 等待下一个滴答并检索滴答的快照。
world_snapshot = world.wait_for_tick()
# 注册一个回调函数，每当收到新的快照时都会被调用。
world.on_tick(lambda world_snapshot: do_something(world_snapshot))
```
---
## 可能的配置
时间步长和同步性的配置导致不同的设置。这里是对可能性的简要总结。
|                                                                        | **固定时间步长**                                                    | **可变时间步长**                                                 |
| ---------------------------------------------------------------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| **同步模式**                                                           | 客户端对仿真及其信息有完全的控制。                                 | 存在不可靠的仿真风险。                                              |
| **异步模式**                                                           | 信息具有良好的时间参考。服务器尽可能快地运行。                     | 难以复现的仿真。                                                     |
<br>
* __同步模式 + 可变时间步长__。这种状态几乎肯定是不受欢迎的。当时间步长大于0.1秒时，物理无法正确运行。如果服务器必须等待客户端计算步骤，这很可能会发生。仿真时间和物理将不会同步。仿真将不可靠。  
* __异步模式 + 可变时间步长__。这是CARLA的默认状态。客户端和服务器是异步的。仿真时间根据真实时间流动。重新执行仿真需要考虑浮点数算术误差和可能的服务器之间的时间步长差异。  
* __异步模式 + 固定时间步长__。服务器将尽可能快地运行。检索的信息将与仿真中的确切时刻相关。这种配置使仿真能够在较少的真实时间内模拟更长的时间段，如果服务器足够快的话。  
* __同步模式 + 固定时间步长__。客户端将控制仿真。时间步长将是固定的。服务器在客户端发送滴答之前不会计算下一个步骤。当需要同步性和精确性时，这是最好的模式。特别是在处理慢速客户端或不同元素检索信息时。
!!! Warning
    在同步模式下，始终使用固定的时间步长。如果服务器必须等待用户，并且它使用可变的时间步长，时间步长将会太大。物理将不可靠。这个问题在时间步长限制部分有更好的解释。
---
## 物理确定性
CARLA在特定情况下支持物理和碰撞确定性：
- __同步模式和固定的时间步长必须启用：__ 确定性要求客户端与服务器完美同步，以确保命令正确应用并产生准确可重复的结果。必须通过设置`fixed_delta_seconds`来强制执行恒定的时间步长。如果没有设置，时间步长将在每一步根据仿真性能自动计算。
- __在加载或重新加载世界之前必须启用同步模式：__ 如果世界不是从一开始就处于同步模式，可能会出现不同的时间戳。这可能会在物理仿真和对象的生命周期（如交通灯）中产生小的差异。
- __每次新重复之前必须重新加载世界：__ 每次你想重复仿真时，都要重新加载世界。
- __命令应以批处理方式而不是逐个发出：__ 尽管不常见，但在繁忙的仿真或过载的服务器上，逐个发出的命令可能会丢失。如果命令以[`apply_batch_sync`](python_api.md/#carla.Client.apply_batch_sync)命令批量发出，命令将被执行或返回失败响应。
以下是一个执行上述步骤的示例：
```py
client = carla.Client(HOST, PORT) # 连接到服务器
client.set_timeout(10.0)
world = client.get_world()
# 加载所需地图
client.load_world("Town10HD_Opt")
# 设置同步模式设置
new_settings = world.get_settings()
new_settings.synchronous_mode = True
new_settings.fixed_delta_seconds = 0.05
world.apply_settings(new_settings) 
client.reload_world(False) # 重新加载地图，保持世界设置
# 设置交通管理器
traffic_manager = client.get_trafficmanager(TM_PORT)
traffic_manager.set_synchronous_mode(True)
traffic_manager.set_random_device_seed(SEED) # 定义TM种子以实现确定性
# 生成你的车辆、行人等
# 仿真循环
while True:
    # 你的代码
    world.tick()
```
对于播放功能的一个特定示例：
```py
client = carla.Client(HOST, PORT) # 连接到服务器
client.set_timeout(10.0)
world = client.get_world()
# 加载所需地图
client.load_world("Town10HD_Opt")
# 设置同步模式设置
new_settings = world.get_settings()
new_settings.synchronous_mode = True
new_settings.fixed_delta_seconds = 0.05
world.apply_settings(new_settings) 
client.replay_file(FILE_TO_PLAY, 0, 0, 0, False)
world.tick() # 服务器需要处理replay_file命令的滴答
# 仿真循环
while True:
    # 你的代码
    world.tick()
```
运行这些步骤将确保每次仿真运行都产生相同的结果。
---
这就是关于CARLA中仿真时间和客户端-服务器同步性的所有内容。
打开CARLA并玩一会儿。任何建议或疑问都欢迎在论坛上提出。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>



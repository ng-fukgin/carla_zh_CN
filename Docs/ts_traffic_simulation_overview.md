# CARLA中的交通模拟
交通模拟对于自动驾驶堆栈的准确和高效训练与测试至关重要。CARLA提供了多种不同的选项来模拟交通和特定的交通场景。本节概述了可用的选项，以帮助您决定哪一种最适合您的使用场景。
- [CARLA中的交通模拟](#carla中的交通模拟)
  - [场景运行器和OpenScenario](#场景运行器和openscenario)
  - [](#)
  - [交通管理器](#交通管理器)
  - [](#-1)
  - [Scenic](#scenic)
  - [](#-2)
  - [SUMO](#sumo)
  - [](#-3)
---
## 场景运行器和OpenScenario
场景运行器提供了[预定义的交通场景](https://carla-scenariorunner.readthedocs.io/en/latest/list_of_scenarios/)，并允许用户使用Python或[OpenSCENARIO 1.0标准](https://releases.asam.net/OpenSCENARIO/1.0.0/ASAM_OpenSCENARIO_BS-1-2_User-Guide_V1-0-0.html#_foreword)来[定义自己的场景](https://carla-scenariorunner.readthedocs.io/en/latest/creating_new_scenario/)。
OpenSCENARIO的主要用途是描述涉及多辆车辆的复杂操作。用户可以查看Scenario Runner支持哪些OpenSCENARIO功能[这里](https://carla-scenariorunner.readthedocs.io/en/latest/openscenario_support/)。这些功能包括操作、动作、条件、故事和故事板。
场景运行器需要从主CARLA包中[单独安装](https://github.com/carla-simulator/scenario_runner)。
__适用于：__
- 创建复杂的交通场景和路线，以准备在[CARLA排行榜](https://leaderboard.carla.org/)上进行评估的自动驾驶代理。
- 定义可以针对场景模拟的记录运行的定制[指标](https://carla-scenariorunner.readthedocs.io/en/latest/metrics_module/)，从而无需重复运行模拟。
<div class="build-buttons">
<p>
<a href="https://carla-scenariorunner.readthedocs.io" target="_blank" class="btn btn-neutral" title="前往场景运行器">
前往场景运行器</a>
</p>
</div>
---
## 交通管理器
交通管理器是CARLA中的一个模块，它从客户端控制模拟中的某些车辆。车辆通过[`carla.Vehicle.set_autopilot`](https://carla.readthedocs.io/en/latest/python_api/#carla.Vehicle.set_autopilot)方法或[`command.SetAutopilot`](https://carla.readthedocs.io/en/latest/python_api/#commandsetautopilot)类注册到交通管理器。每辆车的控制通过一系列[不同的阶段](adv_traffic_manager.md#stages)进行管理，每个阶段在不同的线程上运行。
__适用于：__
- 用真实的城市交通条件填充模拟。
- [自定义交通行为](adv_traffic_manager.md#general-considerations)，以设置特定的学习情境。
- 在提高计算效率的同时，开发与阶段相关的功能和数据结构。
<div class="build-buttons">
<p>
<a href="https://carla.readthedocs.io/en/latest/adv_traffic_manager/" target="_blank" class="btn btn-neutral" title="前往交通管理器">
前往交通管理器</a>
</p>
</div>
---
## Scenic
[Scenic](https://scenic-lang.readthedocs.io)是一种特定领域的概率编程语言，用于模拟像机器人和自动驾驶汽车这样的网络物理系统的环境。Scenic提供了一个[专用领域](https://scenic-lang.readthedocs.io/en/latest/modules/scenic.simulators.carla.html)来促进在CARLA模拟器上执行Scenic脚本。
Scenic场景定义易于阅读和构建。创建简单场景的教程提供[这里](tuto_G_scenic.md)。
__适用于：__
- 使用单一场景定义生成多个、多样化的场景。
- 定义动态代理的概率策略，以响应世界状态随时间采取行动。
<div class="build-buttons">
<p>
<a href="https://carla.readthedocs.io/en/latest/tuto_G_scenic/" target="_blank" class="btn btn-neutral" title="前往Scenic教程">
前往Scenic教程</a>
</p>
</div>
---
## SUMO
[SUMO](https://sumo.dlr.de/docs/SUMO_at_a_Glance.html)是一个开源的、微观的、多模式的交通模拟。在SUMO中，每辆车都被明确地建模，拥有自己的路线，并独立地在网络中移动。模拟默认是确定性的，但有多种选项可以引入随机性。
CARLA提供了与SUMO的联合模拟功能，允许在两个模拟器之间分配任务。车辆可以通过SUMO在CARLA中生成，并由SUMO管理，就像交通管理器所做的那样。
__适用于：__
- 在一个包中利用CARLA和SUMO的功能。
<div class="build-buttons">
<p>
<a href="https://carla.readthedocs.io/en/latest/adv_sumo/" target="_blank" class="btn btn-neutral" title="前往SUMO联合模拟">
前往SUMO联合模拟</a>
</p>
</div>
---
如果您对在CARLA中模拟交通的不同选项有任何疑问，请随时在论坛或[Discord](https://discord.gg/8kqACuC)上发帖。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
前往CARLA论坛</a>
</p>
</div>



# SUMO协同仿真

CARLA已开发与SUMO的协同仿真功能。这使得可以随意分配任务，并利用每个仿真的功能为用户服务。

*   [__前提条件__](#requisites)  
*   [__运行自定义协同仿真__](#run-a-custom-co-simulation)  
	*   [创建CARLA vtypes](#create-carla-vtypes)  
	*   [创建SUMO网格](#create-the-sumo-net)  
	*   [运行同步](#run-the-synchronization)  
*   [__生成由SUMO控制的NPCs__](#spawn-npcs-controlled-by-sumo)  

---
## 先决条件
首先，需要[安装SUMO](https://sumo.dlr.de/docs/Installing.html)来运行联合仿真。推荐从源代码构建而不是简单安装，因为有一些新功能和修复将改进联合仿真。
安装完成后，设置SUMO环境变量。
```sh
echo "export SUMO_HOME=/usr/share/sumo" >> ~/.bashrc && source ~/.bashrc
```
SUMO已准备好运行联合仿真。在`Co-Simulation/Sumo/examples`中有一些针对__Town01__、__Town04__和__Town05__的示例。这些`.sumocfg`文件描述了仿真的配置（例如，网络、路线、车辆类型等）。使用其中一个来测试联合仿真。脚本有不同的选项，详细内容见[下文](#run-the-synchronization)。现在，让我们运行一个针对__Town04__的简单示例。
运行带有__Town04__的CARLA仿真。
```sh
cd ~/carla
./CarlaUE4.sh
cd PythonAPI/util
python3 config.py --map Town04
```
然后，运行SUMO联合仿真示例。
```sh
cd ~/carla/Co-Simulation/Sumo
python3 run_synchronization.py examples/Town04.sumocfg  --sumo-gui
```
!!! Important
    
## 运行自定义联合仿真
### 创建CARLA车辆类型
使用脚本`Co-Simulation/Sumo/util/create_sumo_vtypes.py`，用户可以根据CARLA蓝图库创建SUMO的*vtypes*，即CARLA蓝图的对等物。
*   __`--carla-host`__ *(默认: 127.0.0.1)* — CARLA主机服务器的IP。
*   __`--carla-port`__ *(默认: 2000)* — TCP监听端口。
*   __`--output-file`__ *(默认: carlavtypes.rou.xml)* — 包含*vtypes*的生成文件。
此脚本使用`data/vtypes.json`中存储的信息来创建SUMO的*vtypes*。可以通过编辑该文件来修改这些信息。
!!! Warning
    执行脚本时必须运行CARLA仿真。
### 创建SUMO网络
推荐使用脚本`Co-Simulation/Sumo/util/netconvert_carla.py`创建与CARLA同步的SUMO网络。这将使用SUMO提供的[netconvert](https://sumo.dlr.de/docs/NETCONVERT.html)工具。为了运行脚本，需要一些参数。
*   __`xodr_file`__ — OpenDRIVE文件`.xodr`。
*   __`--output'`__ *(默认:`net.net.xml`)* — 输出文件`.net.xml`。
*   __`--guess-tls`__ *(默认:false)* — SUMO可以为特定道路的特定车道设置交通灯，但CARLA不能。如果设置为__True__，SUMO不会区分特定车道的交通灯，这些将与CARLA同步。
脚本的输出将是一个`.net.xml`文件，可以使用__[NETEDIT](https://sumo.dlr.de/docs/NETEDIT.html)__编辑它。使用它来编辑路线、添加需求，并最终准备一个可以保存为`.sumocfg`的仿真。
提供的示例在这个过程中可能有所帮助。看看`Co-Simulation/Sumo/examples`。对于每个`example.sumocfg`，都有几个相关文件同名。它们共同构成了一个联合仿真示例。
### 运行同步
一旦仿真准备好并保存为`.sumocfg`，就可以运行了。有一些可选参数可以更改联合仿真的设置。
*   __`sumo_cfg_file`__ — SUMO配置文件。
*   __`--carla-host`__ *(默认: 127.0.0.1)* — CARLA主机服务器的IP。
*   __`--carla-port`__ *(默认: 2000)* — TCP监听端口。
*   __`--sumo-host`__ *(默认: 127.0.0.1)* — SUMO主机服务器的IP。
*   __`--sumo-port`__ *(默认: 8813)* — TCP监听端口。
*   __`--sumo-gui`__ — 打开一个窗口以可视化SUMO的GUI版本。
*   __`--step-length`__ *(默认: 0.05s)* — 为仿真时间步长设置固定增量秒。
*   __`--sync-vehicle-lights`__ *(默认: False)* — 同步车辆灯光。
*   __`--sync-vehicle-color`__ *(默认: False)* — 同步车辆颜色。
*   __`--sync-vehicle-all`__ *(默认: False)* — 同步所有车辆属性。
*   __`--tls-manager`__ *(默认: none)* — 选择哪个仿真器应该管理交通灯。另一个将相应地更新它们。选项有`carla`、`sumo`和`none`。如果选择`none`，交通灯将不同步。每个车辆只会遵守在其仿真器中生成的交通灯。
```sh
python3 run_synchronization.py <SUMOCFG FILE> --tls-manager carla --sumo-gui
```
!!! Warning
    要停止联合仿真，请按`Ctrl+C`在运行脚本的终端中。
## 由SUMO控制的生成NPC
与SUMO的联合仿真提供了一个附加功能。车辆可以通过SUMO在CARLA中生成，并由后者作为交通管理器来管理。
脚本`spawn_npc_sumo.py`几乎与已知的`generate_traffic.py`相同。此脚本会根据CARLA中激活的城镇自动在临时文件夹中生成SUMO网络。脚本将创建随机路线并让车辆四处行驶。
由于脚本运行同步仿真，并在其中生成车辆，因此参数与`run_synchronization.py`和`generate_traffic.py`中的相同。
*   __`--host`__ *(默认: 127.0.0.1)* — 主机服务器的IP。
*   __`--port`__ *(默认: 2000)* — TCP监听端口。
*   __`-n,--number-of-vehicles`__ *(默认: 10)* — 生成的车辆数量。
*   __`--safe`__ — 避免生成易发生事故的车辆。
*   __`--filterv`__ *(默认: "vehicle.*")* — 过滤生成的车辆蓝图。
*   __`--sumo-gui`__ — 打开一个窗口以可视化SUMO。
*   __`--step-length`__ *(默认: 0.05s)* — 为仿真时间步长设置固定增量秒。
*   __`--sync-vehicle-lights`__ *(默认: False)* — 同步车辆灯光状态。
*   __`--sync-vehicle-color`__ *(默认: False)* — 同步车辆颜色。
*   __`--sync-vehicle-all`__ *(默认: False)* — 同步所有车辆属性。
*   __`--tls-manager`__ *(默认: none)* — 选择哪个仿真器将改变交通灯的状态。另一个将相应地更新它们。如果`none`，交通灯将不同步。
```sh
# 生成10辆车，将由SUMO而不是交通管理器来管理。
# CARLA负责交通灯。
# 打开一个窗口以可视化SUMO。
python3 spawn_sumo_npc.py -n 10 --tls-manager carla --sumo-gui
```
---
这就是目前关于CARLA与SUMO联合仿真的全部内容。
打开CARLA玩一会儿。如果有任何疑问，请随时在论坛上发帖。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>


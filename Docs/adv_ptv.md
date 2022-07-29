# PTV-Vissim 联合仿真

CARLA 开发了与 PTV-Vissim 的协同仿真功能. 这允许随意分配任务，并利用每个模拟的功能来支持用户.  

*   [__先决条件__](#requisites)  
*   [__运行联合仿真__](#run-the-co-simulation)  
	*   [创建一个新网络](#create-a-new-network)  

---
## Requisites

为了运行协同仿真，有两件事是必要的.  

* 为 [__PTV-Vissim 模拟器__ ](https://www.ptvgroup.com/en/solutions/products/ptv-vissim/) 购买许可证. 有必要获得驾驶模拟器界面插件.
* 在 PTV-Vissim 安装文件夹中，查找一个 `DrivingSimulatorProxy.dll`. 将它移动到`C:\Windows\System32`

---
## Run a co-simulation

与此功能相关的所有内容都可以在“Co-Simulation/PTV-Vissim”中找到. CARLA 提供了一些示例，其中包含 __Town01__ 和 __Town03__ 的网络.

要运行协同仿真，请使用脚本 `PTV-Vissim/run_synchronization.py`. 这有一个包含 PTV-Vissim 网络的强制性参数，以及一些其他可选参数. 

* `vissim_network`— vissim 网络文件。 这可以是一个示例或一个自行创建的 PTV-Vissim 网络。
* `--carla-host` *（默认值：127.0.0.1）* — carla 主机服务器的 IP。
* `--carla-port` *（默认值：2000）* 要监听的 TCP 端口。
* `--vissim-version` *（默认值：2020）* — PTV-Vissim 版本。
* `--step-length` *(default: 0.05s)* — 为模拟时间步设置固定增量秒。
* `--simulator-vehicles` *（默认值：1）* — 将在 CARLA 中生成并传递给 PTV-Vissim 的车辆数量。

```sh
python3 run_synchronization.py examples/Town03/Town03.inpx
```
!!! Warning
    要停止联合仿真，请在运行脚本的终端中按 `Ctrl+C`.  

两个仿真将同步运行。 一个模拟器中发生的动作或事件将传播到另一个模拟器。 到目前为止，该功能仅包括车辆移动和生成。 由于 PTV-Vissim 类型，产卵是有限的。
* 如果车辆在 CARLA 中生成，并且 PTV-Vissim 中的 *Vehicle Type* 设置为`car`，它将生成汽车。 不管它是否在 CARLA 中作为摩托车。 在提供的示例中，车辆类型设置为`car`。
* 如果载具在 PTV-Vissim 中生成，CARLA 将使用相同类型的载具。 尺寸和特性将是相似的，但不完全相同 

### Create a new network 

为了使新的 PTV-Vissim 网络能够与 CARLA 一起运行，需要进行一些设置.  

* __激活驾驶模拟器__。 进入`Base Data/Network settings/Driving simulator`并启用该选项。
* __指定车辆和行人类型__。 这些类型将在 PTV-Vissim 中用于与 CARLA 中的生成同步。 默认情况下是空的。
* __将网络导出为`.inpx`__。 创建网络，将其导出，然后使用 `run_synchronization.py` 运行联合仿真
  

![ptv_types](img/ptv_types.jpg)
<div style="text-align: right"><i>任何在 CARLA 中生成的载具都将在 PTV-Vissim 中使用这些类型生成.</i></div>

!!! Warning
    如果行人和车辆类型为空，则 PTV-Vissim 将崩溃. 

---

到目前为止，这就是 PTV-Vissim 与 CARLA 的联合仿真的全部内容。

打开 CARLA 并随便搞一段时间后， 如果有任何疑问，请随时在论坛中发布这些内容。 

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="Go to the CARLA forum">
CARLA forum</a>
</p>
</div>


# 性能基准测试
我们提供了一个基准测试脚本，使用户能够轻松在自己的环境中分析CARLA的性能。该脚本可以配置为运行多种场景，这些场景结合了不同的地图、传感器和天气条件。它报告了在请求的场景下的平均和标准偏差FPS。
在本节中，我们将详细介绍运行基准测试的要求，在哪里可以找到脚本，可用的标志以自定义要运行的场景，以及如何运行命令的示例。
我们还包含了一个单独的基准测试结果，该测试测量了在特定环境中使用不同数量的车辆组合、启用物理和/或启用交通管理器时CARLA的性能。结果与使用的CARLA版本和测试环境一起呈现。
- [__基准测试脚本__](#基准测试脚本)
    - [__开始之前__](#开始之前)
    - [__概要__](#概要)
        - [__标志__](#标志)
- [__CARLA性能报告__](#carla性能报告)
---
## 基准测试脚本
基准测试脚本位于`PythonAPI/util`目录下。它有几个标志可用于自定义要测试的场景，以下是详细说明。
### 开始之前
在运行基准测试脚本之前，需要安装一些依赖项：
```python
python -m pip install -U py-cpuinfo==5.0.0
python -m pip install psutil
python -m pip install python-tr
python -m pip install gpuinfo
python -m pip install GPUtil
```
### 概要
`python3` [`performance_benchmark.py`](https://github.com/carla-simulator/carla/blob/master/PythonAPI/util/performance_benchmark.py) [`[--host HOST]`](#-host-ip_address) [`[--port PORT]`](#-port-port) [`[--file FILE]`](#-file-filenamemd) [`[--tm]`](#-tm)
[`[--ticks TICKS]`](#-ticks) [`[--sync]`](#-sync) [`[--async]`](#-async))
[`[--fixed_dt FIXED_DT]`](#-fixed_dt) [`[--render_mode]`](#-render_mode))
[`[--no_render_mode]`](#-no_render_mode) [`[--show_scenarios]`](#-show_scenarios))
[`[--sensors SENSORS [SENSORS ...]]`](#-sensors-integer))
[`[--maps MAPS [MAPS ...]]`](#-maps-townname))
[`[--weather WEATHER [WEATHER ...]]`](#-weather-integer)

#### 标志
###### `--host`: IP地址
>> __默认__: 本地主机。
>> 配置服务器的主机。
###### `--port`: 端口
>> __默认__: 2000
>> 配置TCP端口监听。
###### `--file`: 文件名.md
>> __默认__: benchmark.md 
>> 将结果以markdown表格格式写入文件。
###### `--tm`
>> 切换到交通管理器基准测试
###### `--ticks`
>> __默认__: 100
>> 为每个场景设置要使用的刻度数。
###### `--sync`
>> __默认模式。__
>> 以同步模式运行基准测试。 
###### `--async`
>> 以异步模式运行基准测试。
###### `--fixed_dt`
>> __默认__: 0.05
>> 如果您想在同步模式下设置时间步长增量，请使用此选项。
###### `--render_mode`
>> 在渲染模式下运行基准测试。
###### `--no_render_mode`
>> __默认模式。__
>> 在非渲染模式下运行基准测试。
###### `--show_scenarios`
>> 当只使用此标志运行脚本时，您将看到所有可用场景参数的列表。
>> 当与其他标志结合使用时，您将看到将要运行的场景预览，而不会实际执行它们。
###### `--sensors`: 整数
>> __默认__: 所有
>> 基准测试中要使用的传感器。在LIDAR和RGB相机之间选择：
>> * __`0`__: cam-300x200
>> * __`1`__: cam-800x600
>> * __`2`__: cam-1900x1080
>> * __`3`__: cam-300x200 cam-300x200 (两个相机)
>> * __`4`__: LIDAR: 100k
>> * __`5`__: LIDAR: 500k
>> * __`6`__: LIDAR: 1M
###### `--maps`: 城镇名称
>> __默认__: 所有地图
>> 所有[CARLA maps][carla_maps]，包括分层和子分层地图，均可使用。
[carla_maps]: https://carla.readthedocs.io/en/latest/core_map/#carla-maps
###### `--weather`: 整数
>> __默认__: 所有天气条件
>> 改变天气条件：
>> * __`0`__: 清澈正午
>> * __`1`__: 阴天正午
>> * __`2`__: 软雨日落
## 如何运行基准测试
1. 启动CARLA：
        # Linux:
        ./CarlaUE4.sh
        # Windows:
        CarlaUE4.exe
        # Source:
        make launch
2. 在另一个终端中，导航到`PythonAPI/util`目录以找到`performance_benchmark.py`脚本：
>> * 显示所有可能的场景而不运行它们：
```shell
python3 performance_benchmark.py --show_scenarios
```
>> * 在应用配置后显示将运行的场景预览，而不实际执行它们：
```shell
python3 performance_benchmark.py --sensors 2 5 --maps Town03 Town05 --weather 0 1 --show_scenarios`
```
>> * 为这些场景执行性能基准测试：
```shell
python3 performance_benchmark.py --sensors 2 5 --maps Town03 Town05 --weather 0 1
```
>> * 在异步模式和渲染模式下执行基准测试：
```shell
python3 performance_benchmark.py --async --render_mode
```
---
## CARLA性能报告
以下表格详细说明了在运行CARLA时，随着车辆数量的增加以及启用和/或禁用物理和交通管理器的不同组合对平均FPS的影响。
* CARLA版本：开发分支，日期为2021年1月29日（提交198fa38c9b1317c114ac15dff130766253c02832）
* 环境规格：Intel(R) Xeon(R) CPU E5-1620 v3 @ 3.50GHz / 32 GB / NVIDIA GeForce GTX 1080 Ti
|车辆数量|物理：关闭 交通管理器：关闭|物理：开启 交通管理器：关闭|物理：关闭 交通管理器：开启|物理：开启 交通管理器：开启|
|--------|--------------------------|-----------------------|----------------------|---------------------|
|0       |1220                      |1102                   |702                   |729                  |
|1       |805                       |579                    |564                   |422                  |
|10      |473                       |223                    |119                   |98                   |
|50      |179                       |64                     |37                    |26                   |
|100     |92                        |34                     |22                    |15                   |
|150     |62                        |21                     |17                    |10                   |
|200     |47                        |15                     |14                    |7                    |
|250     |37                        |11                     |12                    |6                    |
---
如果您对性能基准测试有任何疑问，请随时在论坛上发帖。
<div class="build-buttons">
<!-- 最新发布按钮 -->
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="转到最新的CARLA发布">
CARLA论坛</a>
</p>
</div>


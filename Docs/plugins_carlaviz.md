# carlaviz

carlaviz 插件用于在网页浏览器中可视化仿真。该插件会创建一个基本场景的窗口，实时更新演员，并可以获取传感器数据，还可以在场景中绘制额外的文本、线条和折线。

*   [__通用信息__](#general-information)  
	*   [支持](#support)  
*   [__获取 carlaviz__](#get-carlaviz)  
	*   [前提条件](#prerequisites)  
	*   [下载插件](#download-the-plugin)  
*   [__工具__](#utilities)  

---
## 通用信息

*   __贡献者__ — Minjun Xu，也被称为 [wx9698](https://github.com/wx9698)。  
*   __许可证__ — [MIT](https://en.wikipedia.org/wiki/MIT_License)。  

### 支持

*   __Linux__ — 支持 CARLA 0.9.6、0.9.7、0.9.8、0.9.9、0.9.10。  
*   __Windows__ — 支持 CARLA 0.9.9、0.9.10。  
*   __源码构建__ — 最新更新版本。  

---
## 获取 carlaviz

### 前提条件

*   __Docker__ — 请访问文档并[安装 Docker](https://docs.docker.com/get-docker/)。  
*   __操作系统__ — 任何能够运行 CARLA 的操作系统都应该可以工作。  
*   __Websocket-client__ — 运行命令 `pip3 install websocket_client` 安装。若系统中尚未安装 pip，请参考[安装指南](https://pip.pypa.io/en/stable/installing/)。  

### 下载插件

打开终端，并根据要运行的 CARLA 版本拉取 carlaviz 的 Docker 镜像。

```bash
# 只拉取与使用的 CARLA 版本匹配的镜像
docker pull mjxu96/carlaviz:0.9.6
docker pull mjxu96/carlaviz:0.9.7
docker pull mjxu96/carlaviz:0.9.8
docker pull mjxu96/carlaviz:0.9.9
docker pull mjxu96/carlaviz:0.9.10

# 如果是在源码构建的 CARLA 上工作，请拉取这个镜像
docker pull mjxu96/carlaviz:latest
```

!!! Important
    目前在 Windows 系统上仅支持 0.9.9 和 0.9.10 版本。

CARLA 0.9.9 及之前的版本为单流模式。对于更高版本，传感器实现了多流模式。

* __在单流模式中__，一个传感器只能被一个客户端监听。当一个传感器已被其他客户端监听时，例如运行 `manual_control.py` 时，carlaviz 被迫复制该传感器以获取数据，性能可能会受到影响。

* __在多流模式中__，一个传感器可以被多个客户端监听。carlaviz 无需复制传感器，性能不会受到影响。

!!! Note
    在 Linux 上，用户也可以按照[这里](https://github.com/carla-simulator/carlaviz/blob/master/docs/build.md)的说明构建 carlaviz，但使用 Docker 镜像会更简单。

---
## 运行 carlaviz

__1. 运行 CARLA。__

*   __a) 在 CARLA 包中__ — 进入 CARLA 文件夹，启动仿真 `CarlaUE4.exe` (Windows) 或 `./CarlaUE4.sh` (Linux)。

*   __b) 在源码构建中__ — 进入 CARLA 文件夹，使用 `make launch` 运行 UE 编辑器并按下 `Play`。

__2. 运行 carlaviz。__ 在另一个终端中，根据已下载的 Docker 镜像运行以下命令。

将 `<name_of_Docker_image>` 替换为之前下载的镜像名称，例如 `mjxu96/carlaviz:latest` 或 `mjxu96/carlaviz:0.9.10`。

```sh
# 在 Linux 系统上
docker run -it --network="host" -e CARLAVIZ_HOST_IP=localhost -e CARLA_SERVER_IP=localhost -e CARLA_SERVER_PORT=2000 <name_of_Docker_image>

# 在 Windows/MacOS 系统上
docker run -it -e CARLAVIZ_HOST_IP=localhost -e CARLA_SERVER_IP=host.docker.internal -e CARLA_SERVER_PORT=2000 -p 8080-8081:8080-8081 -p 8089:8089 <name_of_Docker_image>
```

如果所有设置正确，carlaviz 会显示类似于以下的成功信息。

![carlaviz_run](img/plugins_carlaviz_run.jpg)

!!! Warning
    请记得编辑上述命令以匹配所使用的 Docker 镜像。

__3. 打开本地主机__ 打开浏览器并访问 `http://127.0.0.1:8080/`。carlaviz 默认运行在端口 `8080`。输出应类似于以下内容。

![carlaviz_empty](img/plugins_carlaviz_empty.jpg)

---
## 工具

插件启动后，可以用来可视化仿真、仿真中的演员以及传感器获取的数据。插件在右侧显示一个可视化窗口，其中场景实时更新，左侧为显示项目列表的侧边栏。一些项目会出现在可视化窗口中，其他（主要是传感器和游戏数据）则会显示在项目列表的上方。  
以下是可视化的可用选项列表。可能会显示其他元素，例如：

*   __视图模式__ — 更改可视化窗口中的视角。
	*   `俯视` — 从空中俯视。
	*   `透视` — 自由视角。
	*   `驾驶员` — 第一人称视角。

*   __/vehicle__ — 显示自车属性。包括可视化窗口中的速度计和加速计，以及 IMU、GNSS 和碰撞检测传感器获取的数据。
	*   `/velocity` — 自车的速度。
	*   `/acceleration` — 自车的加速度。
*   __/drawing__ — 在可视化窗口中显示使用 [CarlaPainter](https://github.com/wx9698/carlaviz/blob/master/examples/carla_painter.py) 绘制的额外元素。
	*   `/texts` — 文本元素。
	*   `/points` — 点元素。
	*   `/polylines` — 折线元素。
*   __/objects__ — 在可视化窗口中显示演员。
	*   `/walkers` — 更新行人。
	*   `/vehicles` — 更新车辆。
*   __/game__ — 显示游戏数据。
	*   `/time` — 当前仿真时间和帧数。
*   __/lidar__ — LIDAR 传感器数据。
	*   `/points` — LIDAR 传感器检测到的点云。
*   __/radar__ — RADAR 传感器数据。
	*   `/points` — RADAR 传感器检测到的点云。
*   __/traffic__ — 地标数据。
	*   `/traffic_light` — 在可视化窗口中显示地图的交通信号灯。
	*   `/stop_sign` — 在可视化窗口中显示地图的停车标志。

尝试生成一些演员。这些演员将自动在可视化窗口中更新。

```sh
cd PythonAPI/examples
# 在同步模式仿真中生成演员
python3 generate_traffic.py -n 10 -w 5
```

![carlaviz_full](img/plugins_carlaviz_full.jpg)

生成一个具有手动控制的自车并移动，查看插件如何更新传感器数据。

```sh
cd PythonAPI/examples
python3 manual_control.py
```

![carlaviz_data](img/plugins_carlaviz_data.jpg)

贡献者 ([wx9698](https://github.com/wx9698)) 创建了一个额外的类，[CarlaPainter](https://github.com/wx9698/carlaviz/blob/master/examples/carla_painter.py)，允许用户绘制显示在可视化窗口中的元素。这些元素包括文本、点和折线。按照[这个示例](https://github.com/carla-simulator/carlaviz/blob/master/examples/example.py)生成一个带有 LIDAR 的自车，并绘制 LIDAR 数据、车辆的轨迹和速度。

![carlaviz_demo](img/plugins_carlaviz_demo.jpg)

---

以上就是关于 carlaviz 插件的所有信息。如果有任何疑问，请随时在论坛中提出。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="Go to the CARLA forum">
CARLA forum</a>
</p>
</div>
# 快速入门软件包安装

本指南介绍如何下载和安装 CARLA 的打包版本。 该软件包包括 CARLA 服务器和两个用于客户端库的选项。 还有其他可以下载并导入到package中的资源素材。 需要禁用虚幻引擎编辑器的高级自定义和开发选项，但可以通过使用 [Windows](build_windows.md) 或 [Linux](build_linux.md) 的 CARLA 构建版本来访问这些选项。

* __[开始安装前的准备](#_1)__  
* __[CARLA 安装](#carla)__  
	* [A. Debian CARLA 安装](#a-debian-carla)  
	* [B. Package 安装](#b-package)  
* __[导入其他资源素材](#_2)__  
* __[安装客户端库](#_3)__
    * [0.9.12 之前的 CARLA 版本](#0912-carla)
    * [CARLA 0.9.12+](#carla-0912)
* __[运行CARLA](#carla_1)__  
	* [命令行选项](#_4)  
* __[更新CARLA](#carla_2)__    
* __[Follow-up](#follow-up)__ 
---
## 开始安装前的准备

安装 CARLA 前应满足以下要求：

* __系统要求.__ CARLA 是为 Windows 和 Linux 系统构建的.
* __足够的 GPU.__ CARLA 旨在进行逼真的模拟，因此服务器至少需要 6 GB GPU，尽管我们建议使用 8 GB。 强烈建议使用专用 GPU 进行机器学习. 
* __磁盘空间.__ CARLA 将使用大约 20 GB 的空间e.
* __Python.__ [Python](https://www.python.org/downloads/) 是 CARLA 中的主要脚本语言。 CARLA 在 Linux 上支持 Python 2.7 和 Python 3，在 Windows 上支持 Python 3；.
* __Pip.__ CARLA 客户端库的某些安装方法需要 pip 或 pip3（取决于您的 Python 版本）20.3 或更高版本. 检查您的 __pip__ 版本：

>>      # For Python 3
>>      pip3 -V

>>      # For Python 2
>>      pip -V

>如果需要升级:

>>      # For Python 3
>>      pip3 install --upgrade pip

>>      # For Python 2
>>      pip install --upgrade pip

* __两个 TCP 端口和良好的互联网连接.__  默认为 2000 和 2001。 确保这些端口未被防火墙或任何其他应用程序阻止. 
* __其他需求.__  CARLA 需要一些 Python 依赖项. 根据您的操作系统安装依赖项：

### Windows

```sh
pip3 install --user pygame numpy
```

### Linux

```sh
pip install --user pygame numpy &&
pip3 install --user pygame numpy
```

---
## CARLA 安装

有两种方法可以将 CARLA 作为package下载和安装 :

__A)__ [Debian CARLA 安装.](#a-debian-carla)

__B)__ [ 从 GitHub 下载.](#b-package) 

### A. Debian CARLA 安装

Debian 软件包可用于 Ubuntu 18.04 和 Ubuntu 20.04，但 __官方支持的平台是 Ubuntu 18.04__.

__1.__ 在系统中设置 Debian 存储库:
```sh
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1AF1527DE64CB8D9
    sudo add-apt-repository "deb [arch=amd64] http://dist.carla.org/carla $(lsb_release -sc) main"
```

__2.__ 安装 CARLA 并检查 在`/opt/` 文件夹中的安装:
```sh
    sudo apt-get update # Update the Debian package index
    sudo apt-get install carla-simulator # Install the latest CARLA version, or update the current installation
    cd /opt/carla-simulator # Open the folder where CARLA is installed
```

此存储库包含 CARLA 0.9.10 和更高版本。 要安装特定版本，请将版本标签添加到安装命令:  
```sh
    apt-cache madison carla-simulator # List the available versions of Carla
    sudo apt-get install carla-simulator=0.9.10-1 # In this case, "0.9.10" refers to a CARLA version, and "1" to the Debian revision
```

!!! 重要
    要安装 0.9.10 之前的 CARLA 版本，请使用窗口右下角的面板更改为文档的先前版本，并按照旧说明进行操作.  

### B. Package 安装

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/blob/master/Docs/download.md" target="_blank" class="btn btn-neutral" title="Go to the latest CARLA release">
<span class="icon icon-github"></span> CARLA repository</a>
</p>
</div>
此存储库包含不同版本的 CARLA。 您将找到选项来下载包含所有最新修复和功能的 __当前版本__ 、 __以前的版本__ 以及包含所有开发修复和功能的 __夜间__ 构建（夜间构建是 CARLA 最不稳定的版本）。 该包是一个名为 __CARLA_version.number__ 的压缩文件。 下载并解压发布文件。 它包含模拟器的预编译版本、Python API 模块和一些用作示例的脚本。



---
## 导入其他资源素材

每个发布的版本都有它额外的资源和地图包。这些额外的资源包包含 __Town06__, __Town07__ 和 __Town10__ 的地图。这些单独存储以减少构建的大小，因此只能在安装主包后导入。


__1.__ [下载](https://github.com/ng-fukgin/carla_zh_CN/blob/master/Docs/download.md) 合适您版本的Carla版本

__2.__ 解压包 :

- __在 Linux 上__:

    - 将包移动到 Import 文件夹并运行以下脚本以提取内容:  

```sh
        cd path/to/carla/root

        ./ImportAssets.sh
```

- __在 Windows 上__:

    - 直接提取根文件夹中的内容. 

---

## 安装客户端库

### 0.9.12 之前的 CARLA 版本

以前版本的 CARLA 不需要安装 Python 库,它们自带一个开箱即用的 `.egg` 文件。__CARLA 版本 0.9.12+ 显着改变了这种行为； 有几个选项可用于安装客户端库。__ 如果您使用的是 0.9.12 之前的 CARLA 版本，请选择该版本以查看相关文档。 否则，请阅读下文，了解 CARLA 0.9.12+ 中的可用选项。


### CARLA 0.9.12+

有几个选项可用于安装和使用 CARLA 客户端库 :

- __.egg__ file
- __.whl__ file
- __下载Python安装包__
在决定使用哪种方法之前，请阅读以下有关每种方法的要求和限制的更多信息。 请注意，混合使用不同的方法可能会导致不兼容，因此请尽可能使用虚拟环境，或者在使用新的库之前[载以前安装的库](build_faq.md#how-do-i-uninstall-the-carla-client-library)


>__A. .egg files__


>>CARLA 提供 `.egg` 文件在 `PythonAPI/carla/dist/` 用于不同的Python 版本。 `PythonAPI/examples` 中的每个示例脚本都包含一个自动查找此文件的[代码片段](build_system.md#versions-prior-to-0912)。 在 Linux 中，您可能需要将此文件添加到您的 PYTHONPATH. 在[此处](build_faq.md#importerror-no-module-named-carla).阅读有关CARLA `.egg`文件的更多信息.

>>__如果您之前使用 pip 安装了客户端库，则这将优先于 .`.egg` 文件__. 您需要先[卸载](build_faq.md#how-do-i-uninstall-the-carla-client-library)以前的库 .

>__B. .whl files__

>>CARLA 为不同的 Python 版本提供了 `.whl` 文件。 您需要安装 `.whl` 文件。 `.whl` 文件位于 `PythonAPI/carla/dist/` 中。 每个支持的 Python 版本都有一个文件，由文件名指示（例如，carla-0.9.12-__cp36__-cp36m-manylinux_2_27_x86_64.whl 表示 Python 3.6）.

>>__建议在虚拟环境中安装 CARLA 客户端库，以避免在使用多个版本时发生冲突.__

>>从 PyPi 安装客户端库，请运行以下命令,选择适合您所需 Python 版本的文件。您将需要 __pip/pip3__ 20.3版本 或更高版本。有关如何检查版本和升级 __pip/pip3__ 的信息，请参阅[开始安装前的准备](#_1)部分 :

>>      # Python 3
>>      pip3 install <wheel-file-name>.whl 

>>      # Python 2
>>      pip install <wheel-file-name>.whl

>>如果您之前安装了客户端库，则应先[卸载](build_faq.md#how-do-i-uninstall-the-carla-client-library)旧的，然后再安装新的 .

>__C. 下载Python安装包__

>>TCARLA客户端可以从 [PyPi](https://pypi.org/project/carla/)下载. 该库与 Python 版本 2.7、3.6、3.7 和 3.8 兼容。 要安装它，您需要 __pip/pip3__ 版本 20.3 或更高版本。有关如何检查版本和升级__pip/pip3__的信息，请参阅[开始安装前的准备](#_1)部分 . 

>>__ 建议在虚拟环境中安装 CARLA 客户端库，以避免在使用多个版本时发生冲突.__

>>要从 PyPi 安装客户端库，请运行以下命令：:

>>      # Python 3
>>      pip3 install carla

>>      # Python 2
>>      pip install carla

>>PyPi 下载仅适用于 CARLA 包（即，不适用于从源代码构建的版本）。 由于 PyPi 下载 __仅包含客户端库__ ，因此它在您将与远程 CARLA 服务器通信且不需要下载完整 CARLA 包的情况下最有用.

---
## 运行CARLA

启动 CARLA 服务器的方法取决于您使用的安装方法和您的操作系统:
	
- Debian 安装n:

```sh
    cd /opt/carla-simulator/bin/

    ./CarlaUE4.sh
```

- Linux 安装:

```sh
    cd path/to/carla/root

    ./CarlaUE4.sh
```

- Windows 安装:

```sh
    cd path/to/carla/root

    CarlaUE4.exe
```

将弹出一个包含城市视图的窗口。 这是 _spectator view_。 请使用鼠标和 WASD 键来在视图中游览，按住鼠标右键来控制方向。 

这是服务器模拟器，它现在正在运行并等待客户端连接并与世界交互。 您可以尝试一些示例脚本来为城市注入生命并驾驶汽车：

```sh
        # Terminal A 
        cd PythonAPI\examples

        python3 -m pip install -r requirements.txt # Support for Python2 is provided in the CARLA release packages

        python3 generate_traffic.py  

        # Terminal B
        cd PythonAPI\examples

        python3 manual_control.py 
```

#### 命令行选项

启动 CARLA 时有一些配置选项可用，它们可以按如下方式使用：

```sh
    ./CarlaUE4.sh -carla-rpc-port=3000
```

* `-carla-rpc-port=N` 监听端口 `N` 的客户端连接。流式传输端口默认设置为 `N+1`。  
* `-carla-streaming-port=N` 指定传感器数据流的端口。使用 0 获取随机未使用的端口。第二个端口将自动设置为`N+1`.  
* `-quality-level={Low,Epic}` 更改图形质量级别。在[渲染选项](adv_rendering_options.md)中了解更多信息.  
* __[虚幻引擎 4 命令行参数列表][ue4clilink].__ 幻引擎提供了很多选项，但并非所有这些都在 CARLA 中可用.  

[ue4clilink]: https://docs.unrealengine.com/en-US/Programming/Basics/CommandLineArguments

脚本[`PythonAPI/util/config.py`]( https://github.com/ng-fukgin/carla_zh_CN/blob/master/PythonAPI/util/config.py) 提供了更多配置选项，应该在服务器启动时运行：

[config]: https://github.com/ng-fukgin/carla_zh_CN/blob/master/PythonAPI/util/config.py

```sh
    ./config.py --no-rendering      # Disable rendering
    ./config.py --map Town05        # Change map
    ./config.py --weather ClearNoon # Change weather

    ./config.py --help # Check all the available configuration options
```

---
## 更新CARLA

没有办法更新 CARLA 的打包版本。 发布新版本时，存储库会更新，您需要删除以前的版本并安装新版本.

 如果您使用 pip/pip3 安装客户端库，则应通过运行以下命令将其卸载：
```sh
# Python 3
pip3 uninstall carla

# Python 2
pip uninstall carla
```

---
## Follow-up

至此，您应该已经启动并运行了 CARLA 的打包版本。 如果您在安装过程中遇到任何困难，请随时在 [CARLA forum](https://github.com/carla-simulator/carla/discussions/) 或[Discord](https://discord.gg/8kqACuC) Discord频道发帖.

下一步是更多地了解 CARLA 的核心概念。阅读__第一步__部分开始学习。您还可以在Python API 参考中找到有关 [Python API](python_api.md) 类和方法的所有信息。


<div class="build-buttons">
<p>
<a href="../core_concepts" target="_blank" class="btn btn-neutral" title="Go to first steps">
Go to: First steps</a>
</p>
</div>

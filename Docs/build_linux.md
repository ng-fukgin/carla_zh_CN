# Linux系统下构建CARLA

本指南详细介绍了如何在 Linux 上从源代码构建 CARLA。 有两个部分。 第一部分详细介绍了系统要求和所需软件的安装，第二部分详细介绍了如何实际构建和运行 CARLA.  

构建过程很长（4 小时或更长时间）并且涉及多种软件。 强烈建议在开始之前通读本指南. 

如果您遇到错误或困难，请查看 **[常见问题解答](build_faq.md)** 页面，该页面为最常见的并发症提供解决方案。或者，使用[CARLA 论坛](https://github.com/carla-simulator/carla/discussions) 发布您可能有的任何疑问。


- [__Part One: 构建CARLA的准备条件](#part-one-prerequisites)
    - [系统要求](#system-requirements)
    - [软件要求](#software-requirements)
        - [Unreal Engine](#unreal-engine)
- [__Part Two: 构建 CARLA_](#part-two-build-carla)
    - [克隆 CARLA 存储库](#clone-the-carla-repository)
    - [获取资产](#get-assets)
    - [设置虚幻引擎环境变量](#set-unreal-engine-environment-variable)
    - [构建 CARLA](#build-carla)
    - [其他 make 命令](#other-make-commands)

---
## Part One: 构建CARLA的准备条件

### 系统要求

* __Ubuntu 18.04.__ CARLA 为 16.04 之前的 Ubuntu 版本提供支持。 **然而**，虚幻引擎需要适当的编译器才能正常工作。 下面分别列出了 Ubuntu 18.04 和以前版本的依赖项。 确保安装与您的系统相对应的那些.
* __130 GB 磁盘空间.__ Carla 将占用大约 31 GB，虚幻引擎将占用大约 91 GB，因此有大约 130 GB 的可用空间来解决这两个问题以及额外的次要软件安装. 
* __足够的 GPU.__  CARLA 旨在进行逼真的模拟，因此服务器至少需要 6 GB GPU，但建议使用 8 GB。 强烈建议使用专用 GPU 进行机器学习. 
* __T两个 TCP 端口和良好的互联网连接.__ 默认为 2000 和 2001. 确保这些端口未被防火墙或任何其他应用程序阻止. 

..warning::
   __如果您是从 CARLA 0.9.12 升级到 0.9.13__：您必须先将 UE4 引擎的 CARLA fork 升级到最新版本。有关升级 UE4 的详细信息，请参阅 [Unreal Engine虚幻引擎](#unreal-engine) 部分



### 软件要求

CARLA 需要许多不同类型的软件才能运行。 有些是在 CARLA 构建过程本身期间构建的，例如 *Boost.Python*。 其他是应该在开始构建之前安装的二进制文件（*cmake*、*clang*、不同版本的 *Python* 等）。 要安装这些要求，请运行以下命令：

```sh
sudo apt-get update &&
sudo apt-get install wget software-properties-common &&
sudo add-apt-repository ppa:ubuntu-toolchain-r/test &&
wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key|sudo apt-key add - &&
sudo apt-add-repository "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-8 main" &&
sudo apt-get update
```

!!! Warning
    以下命令取决于您的 Ubuntu 版本。确保做出相应的选择. 

__Ubuntu 18.04__.

```sh
sudo apt-get install build-essential clang-8 lld-8 g++-7 cmake ninja-build libvulkan1 python python-pip python-dev python3-dev python3-pip libpng-dev libtiff5-dev libjpeg-dev tzdata sed curl unzip autoconf libtool rsync libxml2-dev git
```
__Previous Ubuntu__ versions.

```sh
sudo apt-get install build-essential clang-8 lld-8 g++-7 cmake ninja-build libvulkan1 python python-pip python-dev python3-dev python3-pip libpng16-dev libtiff5-dev libjpeg-dev tzdata sed curl unzip autoconf libtool rsync libxml2-dev git
```

__All Ubuntu systems__.

为避免虚幻引擎和 CARLA 依赖项之间的兼容性问题，请使用相同的编译器版本和 C++ 运行时库来编译所有内容。 CARLA 团队使用 clang-8 和 LLVM 的 libc++。 更改默认 clang 版本以编译 Unreal Engine 和 CARLA 依赖项.

```sh
sudo update-alternatives --install /usr/bin/clang++ clang++ /usr/lib/llvm-8/bin/clang++ 180 &&
sudo update-alternatives --install /usr/bin/clang clang /usr/lib/llvm-8/bin/clang 180
```

从CARLA 0.9.12版本开始, 用户可以选择安装 CARLA Python API 通过`pip` 或 `pip3`的版本需要20.3以上。要检查您是否有合适的版本，请运行以下命令:

```sh
# For Python 3
pip3 -V

# For Python 2
pip -V
```

如果需要升级:

```sh
# For Python 3
pip3 install --upgrade pip

# For Python 2
pip install --upgrade pip
```

You must install the following Python dependencies:

```sh
pip install --user setuptools &&
pip3 install --user -Iv setuptools==47.3.1 &&
pip install --user distro &&
pip3 install --user distro &&
pip install --user wheel &&
pip3 install --user wheel auditwheel
```

---

## Unreal Engine虚幻引擎

从 0.9.12 版开始，CARLA 使用了 Unreal Engine 4.26 的修改分支。此分叉包含特定于 CARLA 的补丁。

请注意，要下载虚幻引擎的这个分支，__您需要有一个与虚幻引擎帐户相关联的 GitHub 帐户__ 。如果您没有此设置，请在继续之前按照[本指南](https://www.unrealengine.com/en-US/ue4-on-github)进行操作。


__1.__ 将 CARLA 的 Unreal Engine 4.26 fork 的内容克隆到您的本地计算机:

```sh
    git clone --depth 1 -b carla https://github.com/CarlaUnreal/UnrealEngine.git ~/UnrealEngine_4.26
```
__2.__ 导航到克隆存储库的目录:
```sh
    cd ~/UnrealEngine_4.26
```

__3.__ 进行构建。 这可能需要一两个小时，具体取决于您的系统. 
```sh
    ./Setup.sh && ./GenerateProjectFiles.sh && make
```

__4.__ 打开编辑器检查虚幻引擎是否已正确安装.
```sh
    cd ~/UnrealEngine_4.26/Engine/Binaries/Linux && ./UE4Editor
```

---

## Part Two: 构建CARLA 

!!! Note
    使用 `sudo apt-get install aria2`下载 aria2  将会加速运行以下指令.

### 克隆 CARLA 存储库

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla" target="_blank" class="btn btn-neutral" title="Go to the CARLA repository">
<span class="icon icon-github"></span> CARLA repository</a>
</p>
</div>

上面的按钮会将您带到项目的官方存储库。 从那里下载并在本地解压缩或使用以下命令克隆它:

```sh
        git clone https://github.com/carla-simulator/carla
```

!!! Note
    `master` 分支 CARLA 的当前版本以及最新的修复和功能。以前的 CARLA 版本标有版本名称。 使用命令 `git branch`检查 git 中的当前分支. 


### 获取资产

您需要下载 __最新__ 的资产才能使用当前版本的 CARLA。 我们提供了一个脚本来自动化这个过程。 要使用该脚本，请在 CARLA 根文件夹中运行以下命令:

```sh
        ./Update.sh
```

资产将被下载并提取到适当的位置.

!!! Important
    要下载当前正在开发的资产，请访问[更新 CARLA ](build_update.md#get-development-assets)并阅读 __获取开发资产__.

要下载 __特定版本 CARLA__ 的资产:

1.  从根 CARLA 目录，导航到 `\Util\ContentVersions.txt` 本文档包含指向所有 CARLA 版本的资产的链接。
2.  提取 `Unreal\CarlaUE4\Content\Carla`中的资产. 如果路径不存在，则创建它。
3.  使用类似于以下的命令提取文件：

```sh
        tar -xvzf <assets_file_name>.tar.gz.tar -C C:\path\to\carla\Unreal\CarlaUE4\Content\Carla
```

### 设置虚幻引擎环境变量

为了让 CARLA 找到正确的虚幻引擎安装，我们需要设置 CARLA 环境变量.

仅为此会话设置变量:

```sh
    export UE4_ROOT=~/UnrealEngine_4.26
```

要设置变量以使其在会话中持续存在:

__1.__ 打开 `~/.bashrc` 或 `./profile`.  
```sh
    gedit ~/.bashrc

    # or 

    gedit ~/.profile
```

__2.__ 将以下行添加到文件的底部: 

```sh
    export UE4_ROOT=~/UnrealEngine_4.26 
```

__3.__ 保存文件并重置终端.  


### 构建 CARLA

本节概述了构建 CARLA 的命令。 **所有命令都应在根 CARLA 文件夹中运行。**

CARLA 的构建过程分为两部分，编译客户端和编译服务器.

!!! Warning
    确保运行 `make PythonAPI` 来准备客户端和 `make launch` 来准备服务端。
    或者，运行`make LibCarla` 来准备要在任何地方导入的 CARLA 库。

__1.__ __编译 Python API 客户端__:

Python API 客户端授予对模拟的控制权。 首次构建 CARLA 以及执行任何更新后都需要编译 Python API 客户端。 客户端编译完成后，您将能够运行脚本与模拟器进行交互.

以下命令编译 Python API 客户端:

```sh
    make PythonAPI
```

或者，要为特定版本的 Python 编译 PythonAPI，请在CARLA 根目录中运行以下命令.

```sh
    # Delete versions as required
    make PythonAPI ARGS="--python-version=2.7, 3.6, 3.7, 3.8"
```

CARLA 客户端库将以两种不同的、互斥的形式构建。 这使用户可以自由选择他们喜欢以哪种形式运行 CARLA 客户端代码。 这两种形式包括  `.egg`  文件和  `.whl`  文件。 选择以下选项之 __一__ 以使用客户端库:

__A. `.egg` file__
>.egg 文件不需要安装。 CARLA 的所有示例脚本在导入 CARLA 时都会自动[查找此文件](build_system.md#versions-prior-to-0912)

>如果您之前安装了 CARLA  `.whl` ，则  `.whl` 将优先于  `.egg` 文件.

__B. `.whl` file__

>`.whl` 文件应该使用 `pip` 或 `pip3`来进行安装:

```sh
# Python 3
pip3 install <path/to/wheel>.whl

# Python 2
pip install <path/to/wheel>.whl
```

>此 `.whl` 文件无法分发，因为它是专门为您的操作系统构建的

!!! Warning
    使用不同方法安装 CARLA 客户端库并在系统上使用不同版本的 CARLA 可能会出现问题。 建议在安装 .whl 时使用虚拟环境，并在安装新的客户端库之前[卸载](build_faq.md#how-do-i-uninstall-the-carla-client-library)任何以前安装的客户端库。
   

__2.__ __编译服务器__:

以下命令编译并启动虚幻引擎。 每次要启动服务器或使用虚幻引擎编辑器时运行此命令:

```sh
    make launch
```

该项目可能会要求在第一次构建其他实例，例如 `UE4Editor-Carla.dll`。 同意以打开项目。 在第一次启动期间，编辑器可能会显示有关着色器和网格距离场的警告。 这些需要一些时间才能加载，直到那时地图才能正确显示.


__3.__ __开始模拟__:

按 **Play** 开始服务器模拟。 可以使用 **WASD** 键移动相机，并通过在移动鼠标的同时单击场景来旋转相机.  

使用 `PythonAPI\examples` 中的示例脚本测试模拟器。 随着模拟器的运行，为每个脚本打开一个新终端并运行以下命令以在城镇中产生一些生命并创建一个天气周期：

```sh
        # Terminal A 
        cd PythonAPI/examples
        python3 -m pip install -r requirements.txt
        python3 generate_traffic.py  

        # Terminal B
        cd PythonAPI/examples
        python3 dynamic_weather.py 
```

!!! Important
    如果模拟以很低的FPS速率运行，请转到Unreal Engine Editor中的`Edit -> Editor preferences -> Performance`，并禁用“`Use less CPU when in background`。
 


### 其他 make 命令

还有更多您可能会觉得有用的 `make` 命令。 在下表中找到它们：:  

| Command | Description |
| ------- | ------- |
| `make help`                                                           | Prints all available commands.                                        |
| `make launch`                                                         | Launches CARLA server in Editor window.                               |
| `make PythonAPI`                                                      | Builds the CARLA client.                                              |
| `make LibCarla`                                                       | Prepares the CARLA library to be imported anywhere.                   |
| `make package`                                                        | Builds CARLA and creates a packaged version for distribution.         |
| `make clean`                                                          | Deletes all the binaries and temporals generated by the build system. |
| `make rebuild`                                                        | `make clean` and `make launch` both in one command.                   |

---

阅读 **[F.A.Q.](build_faq.md)** 页面或在[CARLA forum](https://github.com/carla-simulator/carla/discussions)上发布有关本指南的任何问题。

接下来，学习如何更新CARLA构建或在模拟中迈出第一步，并学习一些核心概念  

<div class="build-buttons">

<p>
<a href="../build_update" target="_blank" class="btn btn-neutral" title="Learn how to update the build">
Update CARLA</a>
</p>

<p>
<a href="../core_concepts" target="_blank" class="btn btn-neutral" title="Learn about CARLA core concepts">
First steps</a>
</p>

</div>

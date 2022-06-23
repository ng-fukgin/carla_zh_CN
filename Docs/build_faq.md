# F.A.Q.

此处列出了有关 CARLA 安装和构建的一些最常见问题。更多内容可以在项目的[GitHub issues](https://github.com/carla-simulator/carla/issues?utf8=%E2%9C%93&q=label%3Aquestion+) 中找到。如果您没有在找到您的疑问，请查看论坛并随时在那里提问


<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="Go to the CARLA forum">
CARLA forum</a>
</p>
</div>

---

## 系统要求

* [构建 CARLA 的预期磁盘空间.](#carla_1)
* [运行 CARLA 的推荐硬件.](#carla_2)

---

## Linux 构建

* [从 GitHub 下载时没有出现"CarlaUE4.sh"脚本.](#github-carlaue4sh)
* ["make launch"在 Linux 上不起作用.](#make-launch-linux)
* [克隆虚幻引擎存储库显示错误.](#_4)
* [运行脚本时:AttributeError: module 'carla' has no attribute 'Client' ](#attributeerror-module-carla-has-no-attribute-client)
* [无法运行示例脚本或“RuntimeError: rpc::rpc_error during call in function version”。](#runtimeerror-rpcrpc_error-during-call-in-function-version)

---

## Windows 构建

* [从 GitHub 下载时没有出现"CarlaUE4.exe".](#github-carlaue4exe)
* [CarlaUE4 无法编译,尝试手动从源代码重建它。](#carlaue4)
* [即使正确安装了 CMake，也会显示 CMake 错误.](#cmake-cmake)
* [Error C2440, C2672: compiler version.](#error-c2440-c2672-compiler-version)
* ["make launch" 在 Windows 上不起作用.](#make-launch-windows)
* [Make 缺少 libintl3.dll 或/和 libiconv2.dll.](#make-libintl3dll-libiconv2dll)
* [模块丢失或使用不同的引擎版本构建.](#_5)
* [尽管输出消息成功，`PythonAPI/carla`中没有`dist`文件夹](#pythonapicarladist)

---

## 运行 Carla

* [在虚幻编辑器中运行服务器时 FPS 速率低.](#fps)
* [无法运行脚本.](#_6)
* [在虚幻编辑器中运行时连接到模拟器.](#_7)
* [无法运行 CARLA 二进制文件和源代码构建.](#carla_4)
* [ImportError: DLL load failed: The specified module could not be found.](#importerror-dll-load-failed-the-specified-module-could-not-be-found)
* [ImportError: DLL load failed while importing libcarla: %1 is not a valid Win32 app.](#importerror-dll-load-failed-while-importing-libcarla-1-is-not-a-valid-win32-app)
* [ImportError: No module named 'carla'](#importerror-no-module-named-carla)

---

## 其他

* [Fatal error: 'version.h' has been modified since the precompiled header.](#fatal-error-versionh-has-been-modified-since-the-precompiled-header)
* [创建 CARLA 的二进制版本.](#carla_5)
* [我可以在 Linux 机器上打包 CARLA for Windows 吗？反之亦然？](#linux-carla-for-windows)
* [如何卸载 CARLA 客户端库？](#carla_6)

---

<!-- ======================================================================= -->
## 系统要求
<!-- ======================================================================= -->

###### 构建 CARLA 的预期磁盘空间.

> 建议至少有 170GB 可用空间。构建 CARLA 需要大约 35GB 的磁盘空间，加上虚幻引擎大约需要 95-135GB。.

<!-- ======================================================================= -->

###### 运行 CARLA 的推荐硬件.

> CARLA 是一款性能要求很高的软件。它至少需要一个 6GB 的 GPU，或者更好的是，一个能够运行虚幻引擎的专用 GPU。.  
>
> 看看[虚幻引擎推荐的硬件](https://www.ue4community.wiki/recommended-hardware-x1p9qyg0).

---

## Linux 构建
<!-- ======================================================================= -->
###### 从 GitHub 下载时没有出现"CarlaUE4.sh"脚本.

> CARLA 的源版本中没有CarlaUE4.sh脚本。按照[构建说明](build_linux.md)从源代码构建 CARLA。 
> 
> 要使用 CARLA 运行CarlaUE4.sh，请按照[快速启动安装](start_quickstart.md).

<!-- ======================================================================= -->

###### "make launch"在 Linux 上不起作用.

> 在构建安装期间可以拖出许多不同的问题，并且会像这样表现出来。以下是最可能的原因列表：:  
> 
> * __运行虚幻引擎 4.26.__ 构建虚幻引擎时可能出现故障。尝试自行运行 UE 编辑器并检查它是否为 4.26 版本.  
> * __下载资产.__ 没有可视内容，服务器将无法运行。此步骤是强制性的。.  
> * __UE4_ROOT 未定义.__ 未设置环境变量。请记住通过将其添加到 `~/.bashrc` 或 `~/.profile` 使其在会话范围内持久化。否则，需要为每个shell设置它。运行`export UE4_ROOT=<path_to_unreal_4-26>`来设置变量。.  
> * __检查依赖关系.__ 确保一切都已正确安装。也许其中一个命令被跳过、不成功或依赖项不适合系统.
> * __删除 CARLA 并再次克隆它.__ 以防万一出了什么问题。删除 CARLA 并克隆或重新下载。  
> * __满足系统要求.__ Ubuntu 版本应为 16.04 或更高版本。CARLA 需要大约 170GB 的磁盘空间和一个专用的 GPU（或至少一个 6GB 的 GPU）才能运行.
> 
> 可能会出现系统显示与 CARLA 冲突的其他特定原因。请在[forum](https://github.com/carla-simulator/carla/discussions/) 上发布这些内容，以便团队了解更多关于它们的信息。
<!-- ======================================================================= -->

###### 克隆虚幻引擎存储库显示错误.

> __1. 虚幻引擎账号是否激活？__ UE 存储库是私有的。为了克隆它，创建 [UE](https://www.unrealengine.com/en-US/)帐户，激活它（检查验证邮件），并[链接您的 GitHub帐户](https://www.unrealengine.com/en-US/blog/updated-authentication-process-for-connecting-epic-github-accounts) .  
> 
> __2. git 是否正确安装？__ 有时错误会显示与https协议不兼容。可以通过卸载并重新安装 git 轻松解决。打开终端并运行以下命令:
>
>      sudo apt-get remove git #Uninstall git
>      sudo apt install git-all #install git
> 

<!-- ======================================================================= -->

###### 运行脚本时:AttributeError: module 'carla' has no attribute 'Client'.

> 运行以下命令.
> 
> 
>       pip3 install -Iv setuptools==47.3.1
>  
>
> 并再次构建 PythonAPIn. 
> 
>
>      make PythonAPI
>
>
> 尝试构建文档以测试一切是否正常运行。应该显示一条成功的消息.
>
>      make PythonAPI.docs

<!-- ======================================================================= -->

###### 无法运行示例脚本或“RuntimeError: rpc::rpc_error during call in function version”.

> ![faq_rpc_error](img/faq_rpc_error.jpg)
>
> 如果运行脚本返回与此类似的输出，则.eggPythonAPI 中的文件存在问题。. 

!!! Important
    如果您使用的是 0.9.12+，有几种方法可以使用/安装客户端库。如果您对客户端库（`.whl`或 PyPi 下载）使用较新的方法之一，则本节中的信息与您无关。
> 
> 首先，打开 `<root_carla>/PythonAPI/carla/dist`. 您正在使用的相应 CARLA 和 Python 版本应该有一个`.egg`文件（类似于`carla-0.X.X-pyX.X-linux-x86_64.egg`）。确保文件与您使用的 Python 版本匹配。要检查您的 Python 版本，请使用以下命令.  
> 
> 
>       python3 --version
>       # or for Python 2
>       python --version
> 
>
> 如果文件丢失或您认为它可能已损坏，请再次尝试重建.  
>
>      make clean
>      make PythonAPI
>      make launch
>
>  
> 现在再次尝试其中一个示例脚本. 
>
>      cd PythonAPI/examples
>      python3 dynamic_weather.py
>
> 如果错误仍然存在，则问题可能与您的 PythonPATH 有关。这些脚本会自动查找`.egg`与构建关联的文件，因此您的 PythonPATH 中可能有另一个`.egg`文件干扰了该过程。使用以下命令显示 PythonPATH 的内容。.  
>
>
>      echo $PYTHONPATH
>
> 在输出中查找类似于 `PythonAPI/carla/dist` 的路径中的 `.egg` 文件的其他实例，并删除这些。它们可能属于 CARLA 装置的其他实例。例如，如果您还通过 *apt-get* 安装了 CARLA，则可以使用以下命令将其删除，PythonPATH 也会被清除。  
>
>      sudo apt-get purge carla-simulator
>
> 最终可以选择使用 `~/.bashrc` 将构建的 `.egg` 文件添加到 PythonPATH。这不是推荐的方式。最好有一个明确的 PythonPATH 并简单地将路径添加到脚本中必要的“.egg”文件。
>
> 首先，打开`~/.bashrc`.
>
>      gedit ~/.bashrc
>
> 
> 将以下行添加到`~/.bashrc`. 这些存储了构建`.egg`文件的路径，以便 Python 可以自动找到它。保存文件，并重置终端以使更改生效。
>
> ```
> export PYTHONPATH=$PYTHONPATH:"${CARLA_ROOT}/PythonAPI/carla/dist/$(ls ${CARLA_ROOT}/PythonAPI/carla/dist | grep py3.)"
> export PYTHONPATH=$PYTHONPATH:${CARLA_ROOT}/PythonAPI/carla
> ```
> 
> 清理 PythonPATH 或添加构建`.egg`文件的路径后，所有示例脚本都应该可以正常工作.  

---

## Windows 构建
<!-- ======================================================================= -->

###### 从 GitHub 下载时没有出现"CarlaUE4.exe".

> CARLA 的源版本中没有“CarlaUE4.exe”可执行文件。按照 [构建说明](build_windows.md) 从源代码构建 CARLA。按照[快速启动说明](start_quickstart.md)直接获取`CarlaUE4.exe`.  

<!-- ======================================================================= -->

###### CarlaUE4 无法编译,尝试手动从源代码重建它.

> 尝试构建 CARLA 时出现问题。使用 Visual Studio 重建以发现发生了什么
>
> __1.__ 转到 `carla/Unreal/CarlaUE4` 并右键单击 `CarlaUE4.uproject`.  
> __2.__ 单击 __Generate Visual Studio project files__.  
> __3.__ 打开使用 Visual Studio 2019 生成的文件.  
> __4.__ 使用 Visual Studio编译项目。快捷键是 F7。构建将失败，但发现的问题将在下面显示.
>
> 不同的问题可能会导致此特定错误消息。用户[@tamakoji](https://github.com/tamakoji) 解决了源代码未正确克隆且无法设置 CARLA 版本（从 gi​​t 下载为 .zip 时）的经常性案例.  
>
> *   __检查 `Build/CMakeLists.txt.in`.__ 如果显示 `set(CARLA_VERSION )` 如下:  
>
> __1.__ 转到 `Setup.bat` 第 198 行.  
>
> __2.__ 更新 from: 
> 
> ```
> for /f %%i in ('git describe --tags --dirty --always') do set carla_version=%%i
> ```
> 
> to:
> 
> ```
> for /f %%i in ('git describe --tags --dirty --always') do set carla_version="0.9.9"
> ```

<!-- ======================================================================= -->

###### 即使正确安装了 CMake，也会显示 CMake 错误.

> 尝试使用make命令构建服务器或客户端时会出现此问题。即使 CMake 已安装、更新并添加到环境路径。Visual Studio 版本之间可能存在冲突。.  
>
> 只留下 VS2019 并完全删除其余部分.  

<!-- ======================================================================= -->

###### Error C2440, C2672: compiler version.

>  构建未使用 Visual Studio 2019 编译器.其他 Visual Studio 或 Microsoft 编译器版本冲突。卸载并重新构建.  
>
> Visual Studio 不擅长卸载自己。要从计算机中彻底清除 Visual Studio，请转到“Program Files (x86)\Microsoft Visual Studio\Installer\resources\app\layout”并运行“.\InstallCleanup.exe -full”。这可能需要管理员权限.  
>
> 
要保留其他 Visual Studio 版本，请通过添加以下行来编辑 ```%appdata%\Unreal Engine\UnrealBuildTool\BuildConfiguration.xml```：
>
> ```
>  <VCProjectFileGenerator>
>    <Version>VisualStudio2019</Version>
> </VCProjectFileGenerator>
> ```
> 
> ``` 
>  <WindowsPlatform>
>    <Compiler>VisualStudio2019</Compiler>
> </WindowsPlatform>
> ```  

<!-- ======================================================================= -->

###### "make launch" 在 Windows 上不起作用.

> 在构建安装期间可以拖出许多不同的问题，并像这样表现出来。以下是最可能的原因列表：: 
> 
> * __重新启动计算机.__ 在 Windows 构建期间发生了很多事情。重新启动并确保所有内容都已正确更新.  
> * __运行 Unreal Engine 4.26.__ 构建虚幻引擎时可能出现故障。运行编辑器并检查是否正在使用 4.26 版本.  
> * __下载资产.__ 没有可视内容，服务器将无法运行。此步骤是强制性的.  
> * __Visual Studio 2019.__ 如果安装或最近卸载了其他版本的 Visual Studio，则可能会出现冲突。要从计算机中彻底清除 Visual Studio，请转到“Program Files (x86)\Microsoft Visual Studio\Installer\resources\app\layout”并运行“.\InstallCleanup.exe -full”.  
> * __删除 CARLA 并再次克隆它.__ 以防万一出了什么问题。删除 CARLA 并克隆或重新下载.  
> * __满足系统要求.__ CCARLA 需要大约 170GB 的磁盘空间和一个专用的 GPU（或至少一个 6GB 的 GPU）才能运行.  
>
>可能会出现系统显示与 CARLA 冲突的其他特定原因。请在[forum](https://github.com/carla-simulator/carla/discussions/) 上发布这些内容，以便团队了解更多关于它们的信息.

<!-- ======================================================================= -->

###### Make 缺少 libintl3.dll 或/和 libiconv2.dll.
 
> 下载[依赖项](http://gnuwin32.sourceforge.net/downlinks/make-dep-zip.php) 并将`bin`内容提取到`make`安装路径中.   

<!-- ======================================================================= -->

###### 模块丢失或使用不同的引擎版本构建.

> 单击 __Accept__以重建它们. 

<!-- ======================================================================= -->

###### 尽管输出消息成功，PythonAPI/carla中没有dist文件夹.

>在 Windows 中，`make PythonAPI`命令可以返回一条消息，表明 `Python API` 已成功安装，而实际上并未成功安装。如果看到此输出后`PythonAPI/carla`目录中没有创建`dist`文件夹，请查看上方的命令输出。很可能发生了错误，需要在纠正错误并运行`make clea`后重试构建.

---

## 运行 Carla
<!-- ======================================================================= -->

###### 在虚幻编辑器中运行服务器时 FPS 速率低..

> UE4 编辑器在失焦时进入低性能模式.  
>
> 转到`Edit/Editor Preferences/Performance`编辑器首选项，然后禁用"Use Less CPU When in Background" option.

<!-- ======================================================================= -->

###### 无法运行脚本..

> 有些脚本有要求。这些列在名为 __Requirements.txt__ 的文件中，与脚本本身位于相同的路径中。请务必检查这些以运行脚本。它们中的大多数都可以使用简单的`pip`命令进行安装。.  
>
> 有时在 Windows 上，仅使用 `> script_name.py` 无法运行脚本。尝试添加`> python3 script_name.py`，并确保在正确的目录中。

<!-- ======================================================================= -->

###### 在虚幻编辑器中运行时连接到模拟器..

> 单击 __Play__  并等待场景加载完毕。此时，Python 客户端可以像使用独立模拟器一样连接到模拟器。

<!-- ======================================================================= -->

###### 无法运行 CARLA 二进制文件和源代码构建.

> NVIDIA 驱动程序可能已过时。确保不是这种情况。如果问题仍未解决，请查看[forum](https://github.com/carla-simulator/carla/discussions/) 并发布具体问题. 

<!-- ======================================================================= -->

###### ImportError: DLL load failed: The specified module could not be found.

> 所需的库之一尚未正确安装。作为一种解决方法，转到 `carla\Build\zlib-source\build`，然后将名为 `zlib.dll` 的文件复制到脚本目录中. 

<!-- ======================================================================= -->

###### ImportError: DLL load failed while importing libcarla: %1 is not a valid Win32 app.

> 尝试运行脚本时，32 位 Python 版本会产生冲突。卸载它，只留下所需的 Python3 x64 

<!-- ======================================================================= -->

###### ImportError: No module named 'carla'

> 发生此错误是因为 Python 找不到 CARLA 库。 CARLA 库包含在 `.egg` 文件中，位于 `PythonAPI/carla/dist` 目录中，所有示例脚本都将在此目录中查找它。 `.egg` 文件遵循 `carla-<carla-version>-py<python-version>-<operating-system>.egg` 的命名法.
>

!!! Important
    CARLA 仅在 0.9.12 之前的版本中将 `.egg` 文件用于客户端库。如果您使用的是 0.9.12+，有几种方法可以使用/安装客户端库。如果您使用客户端库的较新方法之一（`.whl` 或 PyPi 下载），则本节中的信息与您无关。

    在 [__Quickstart tutorial__](start_quickstart.md#carla-0912) 中阅读有关使用/安装客户端库的新方法的更多信息。.

>如果你使用的是 CARLA 的打包版本，会有几个.egg文件，对应不同版本的 Python，具体取决于 CARLA 的版本。确保您使用这些 Python 版本之一运行脚本。要检查默认 Python 版本，请在命令行中键入以下内容:
>
>
>       python3 --version
>       # or
>       python --version 
>

>如果您从源代码构建 Python，则该.egg文件将根据系统上的默认 Python 版本构建。在 Linux 中，这将是返回的默认 Python 版本:


>       /usr/bin/env python3 --version
>       # or if you specify ARGS="--python-version=2"
>       /usr/bin/env python2 --version


>在 Windows 中，它将是默认的 Python 版本:

>       py -3 --version

>确保使用与 `.egg` 文件对应的 Python 版本运行脚本.
>在 Linux 中，您可能还需要将 Python 路径指向 CARLA `.egg`的路径。为此，请运行以下命令:

>       export PYTHONPATH=$PYTHONPATH:<path/to/carla/>/PythonAPI/carla/dist/<your_egg_file>
>       # Check if CARLA can now be found
>       python3 -c 'import carla;print("Success")'

>请注意，虚拟环境或其他 Python 环境（如 Conda）可能会使 CARLA 的安装复杂化。确保您已相应地设置了 Python 默认值和路径.

---

## 其他
<!-- ======================================================================= -->

###### Fatal error: 'version.h' has been modified since the precompiled header.

> 由于 Linux 更新，这种情况不时发生。Makefile 中有一个针对此问题的特殊目标。这需要很长时间，但解决了这个问题：:
>
>      make hard-clean
>      make CarlaUE4Editor

<!-- ======================================================================= -->

###### 创建 CARLA 的二进制版本..

> 在 Linux 中，在项目文件夹中运行“make package”。该包将包括项目和 Python API 模块。
>
>或者，可以在虚幻编辑器中编译 CARLA 的二进制版本。打开 CarlaUE4 项目，进入菜单 `File/Package Project`，然后选择一个平台。可能还要等一下。

<!-- ======================================================================= -->

###### 我可以在 Linux 机器上打包 CARLA for Windows 吗？反之亦然?

>尽管此功能可用于Unreal Engine，但在 CARLA 中不可用。我们有许多不支持交叉编译的依赖项。

<!-- ======================================================================= -->
###### 如何卸载 CARLA 客户端库？

>如果您使用pip/pip3安装了客户端库，则应通过运行以下命令将其卸载：

```sh
# Python 3
pip3 uninstall carla

# Python 2
pip uninstall carla
```

---

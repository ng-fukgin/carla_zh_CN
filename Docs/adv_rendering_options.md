
# 渲染选项
本指南详细介绍了CARLA中可用的不同渲染选项，包括质量级别、无渲染模式和离屏模式。此外，还解释了版本0.9.12的CARLA在这些方面与之前版本的区别。
- [__图形质量__](#graphics-quality)  
	- [Vulkan图形API](#vulkan-graphics-api)  
	- [质量级别](#quality-levels)
- [__无渲染模式__](#no-rendering-mode)
- [__离屏模式__](#off-screen-mode)  
	- [离屏与无渲染](#off-screen-vs-no-rendering)
	- [设置离屏模式（版本0.9.12+）](#setting-off-screen-mode-version-0912)
	- [设置离屏模式（版本0.9.12之前的版本）](#setting-off-screen-mode-versions-prior-to-0912)
!!! Important
    以下命令选项在CARLA打包发行版中并不等效。请阅读[命令行选项](start_quickstart.md#command-line-options)部分了解更多信息。
---
## 图形质量
### Vulkan图形API
从版本0.9.12开始，CARLA运行在Unreal Engine 4.26上，该版本仅支持Vulkan图形API。之前版本的CARLA可以配置为使用OpenGL。如果您使用的是CARLA的早期版本，请在屏幕右下角选择相应版本的文档以获取更多信息。
### 质量级别
CARLA有两个不同的图形质量级别。__史诗__ 是默认级别，也是最详细的。__低__ 级别禁用所有后处理和阴影，并将绘制距离设置为50米，而不是无限。
在 __低__ 模式下，模拟运行速度显著加快。这在存在技术限制、精度非必需或训练代理在涉及简单数据或仅近距离元素的情况下非常有帮助。
下面的图片比较了这两种模式。在Windows和Linux上使用的标志是相同的。在构建版本中没有等效选项，但UE编辑器有自己的质量设置。前往 `设置/引擎可扩展性设置` 以获得所需质量的更多定制。
#### 史诗模式
`./CarlaUE4.sh -quality-level=Epic`
![史诗模式截图](img/rendering_quality_epic.jpg)
*史诗模式截图*
#### 低模式
`./CarlaUE4.sh -quality-level=Low`
![低模式截图](img/rendering_quality_low.jpg)
*低模式截图*
<br>
!!! Important
    导致史诗模式显示异常白色的 issue 已修复。如果问题仍然存在，请删除 `GameUserSettings.ini`。它保存了之前的设置，并在下一次运行时重新生成。__Ubuntu路径：__ `~/.config/Epic/CarlaUE4/Saved/Config/LinuxNoEditor/` __Windows路径：__ `<Package folder>\WindowsNoEditor\CarlaUE4\Saved\Config\WindowsNoEditor\`
---
## 无渲染模式
此模式禁用渲染。Unreal Engine将跳过与图形有关的所有内容。此模式防止了渲染开销。它极大地促进了在高频率下的交通模拟和道路行为。要启用或禁用无渲染模式，请更改世界设置，或使用 `/PythonAPI/util/config.py` 中提供的脚本。
以下是如何通过脚本启用然后禁用它的示例：
```py
settings = world.get_settings()
settings.no_rendering_mode = True
world.apply_settings(settings)
...
settings.no_rendering_mode = False
world.apply_settings(settings)
```
通过命令行禁用和启用渲染，运行以下命令：
```sh
cd PythonAPI/util && python3 config.py --no-rendering
```
```sh
cd PythonAPI/util && python3 config.py --rendering
```
脚本 `PythonAPI/examples/no_rendering_mode.py` 将启用无渲染模式，并使用 __Pygame__ 创建一个使用简单图形的空中视图：
```sh
cd PythonAPI/examples && python3 no_rendering_mode.py
```
!!! Warning
    在无渲染模式下，摄像头和GPU传感器将返回空数据。不会使用GPU。Unreal Engine不绘制任何场景。
---
## 离屏模式
从版本0.9.12开始，CARLA运行在Unreal Engine 4.26上，该版本引入了对离屏渲染的支持。在CARLA的早期版本中，离屏渲染取决于您使用的图形API。
### 离屏与无渲染
了解 __无渲染模式__ 和 __离屏模式__ 之间的区别非常重要：
- __无渲染模式：__ Unreal Engine不渲染任何内容。不计算图形。基于GPU的传感器返回空数据。
- __离屏模式：__ Unreal Engine像往常一样工作，渲染被计算，但没有可用的显示。基于GPU的传感器返回数据。
### 设置离屏模式（版本0.9.12+）
要以离屏模式启动CARLA，请运行以下命令：
```sh
./CarlaUE4.sh -RenderOffScreen
```
### 设置离屏模式（版本0.9.12之前的版本）
使用OpenGL或Vulkan进行离屏模式有所不同。
__使用OpenGL__，您可以在Linux中以离屏模式运行以下命令：
```sh
# Linux
DISPLAY= ./CarlaUE4.sh -opengl
```
__Vulkan__ 需要额外的步骤，因为它需要使用X11网络协议与显示X服务器通信才能正常工作。以下步骤将指导您如何在没有显示的Ubuntu 18.04机器上设置CARLA以使用Vulkan。
__1. 获取最新的NVIDIA驱动程序：__
```sh
wget http://download.nvidia.com/XFree86/Linux-x86_64/450.57/NVIDIA-Linux-x86_64-450.57.run
```
__2. 安装驱动程序：__
```sh
sudo /bin/bash NVIDIA-Linux-x86_64-450.57.run --accept-license --no-questions --ui=none
```
__3. 安装与xserver相关的依赖项：__
```sh
sudo apt-get install -y xserver-xorg mesa-utils libvulkan1
```
__4. 配置xserver：__
```sh
sudo nvidia-xconfig --preserve-busid -a --virtual=1280x1024
```
__5. 设置SDL_VIDEODRIVER变量。__
```sh
ENV SDL_VIDEODRIVER=x11
```
__6. 运行xserver：__
```sh
sudo X :0 &
```
__7. 运行CARLA：__
```sh
DISPLAY=:0.GPU ./CarlaUE4.sh -vulkan
```
CARLA提供了一个Dockerfile，执行上述所有步骤，[请点击此处](https://github.com/carla-simulator/carla/blob/0.9.12/Util/Docker/Release.Dockerfile)。
---
与此主题相关的任何问题或疑问都可以在CARLA论坛上发布。
<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往CARLA论坛">
CARLA论坛</a>
</p>
</div>


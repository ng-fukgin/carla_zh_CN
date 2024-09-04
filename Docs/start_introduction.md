# CARLA

![Welcome to CARLA](img/welcome.png)

!!! Important
    本文档指向的是最新版本的 CARLA(0.9.13,目前是最新版本)。有关以前版本的文档，请在您看到此按钮的右下角选择所需的版本： ![docs_version_panel](img/docs_version_panel.jpg)




CARLA 是一个开源的自动驾驶模拟器。 它是从底层开始构建的，用作模块化和灵活的 API，以解决涉及自动驾驶问题的一系列任务。 CARLA 的主要目标之一是帮助实现自动驾驶研发的简易化，成为用户可以轻松访问和定制的工具。 为此，模拟器必须满足一般驾驶问题中不同用例的要求（例如学习驾驶策略、训练感知算法等）。 CARLA 基于虚幻引擎运行模拟，并使用 OpenDRIVE 标准（目前为 1.4）来定义道路和城市环境。 通过使用 Python 和 C++ 处理的 API 授予对模拟的控制权，该 API 会随着项目的不断发展而不断增长。

为了使开发、培训和验证驾驶系统的过程顺利进行，CARLA 发展成为一个由开源社区围绕主平台构建的项目生态系统（就如同Carla中文站一样成为开源交流的平台）。 在这种情况下，了解CARLA是如何工作的一些事情是很重要的，这样才能充分理解它的能力。

The simulator
-------------

CARLA 模拟器由一个可扩展的客户端-服务器架构组成。 服务器负责与模拟本身相关的所有事情：传感器渲染、物理计算、世界状态及其参与者的更新等等。 由于它旨在获得真实的结果，因此最适合使用专用 GPU 运行服务器，尤其是在处理机器学习时。 客户端由一组客户端模块组成，这些模块控制场景中演员的逻辑并设置世界条件。 这是通过利用 CARLA API（在 Python 或 C++ 中）实现的，这是一个在服务器和客户端之间进行调解的层，它不断发展以提供新的功能。

![CARLA Modules](img/carla_modules.png)

这总结了模拟器的基本结构。 了解 CARLA 远不止于此，因为其中存在许多不同的功能和元素。 下面列出了其中一些，以便于大家了解 CARLA 可以实现的功能。

**Traffic manager** 一种内置系统，除了用于学习的车辆外，还可以控制车辆。它充当 CARLA 提供的指挥器，以逼真的行为再现类似城市的环境。

**Sensors** 车辆依靠它们来分发周围环境的信息。在 CARLA 中，它们是附加在车辆上的特定类型的参与者，可以检索和存储它们接收到的数据以简化流程。目前，该项目支持不同类型的这些，从摄像头到雷达、激光雷达等等。

**Recorder** 此功能用于为虚拟世界中的每个要素逐步重演模拟。它允许访问世界任何地方的时间轴中的任何时刻，是一个很棒的追踪工具。

**ROS bridge and Autoware implementation** CARLA 项目打结并致力于将模拟器集成到其他学习环境中。

**Open Assets** CARLA 通过对天气条件的控制和一个包含大量参与者的蓝图库，为城市环境提供了不同的地图。但是，这些元素可以定制，并且可以按照简单的指南生成新元素。

**Scenario runner**为了简化车辆的学习过程，CARLA 提供了一系列描述不同情况的路线进行迭代。这些也为 CARLA 挑战赛奠定了基础，每个人都可以测试他们的解决方案并进入排行榜。

The project
-----------

CARLA 快速而稳定地发展，扩大了提供的解决方案范围，并为自动驾驶的不同方法开辟了道路。 它这样做的同时永远不会忘记其开源性质。 该项目是透明的，就像一个白盒子，任何人都可以访问工具和开发社区。 在简易化中，CARLA 发现了它的价值。 谈论 CARLA 如何成长意味着谈论一个开发者社区，他们一起深入研究自动驾驶的彻底问题。 每个人都可以自由地与 CARLA 一起探索，找到自己的解决方案，然后与社区的其他人分享他们的成就。 

这份文档将是一路走来的伴侣。 下一页包含为渴望安装 CARLA 版本的用户提供的 __[快速入门](start_quickstart.md)__ 说明。 还有适用于 Linux 和 Windows 的构建指南。 这将使 CARLA 从存储库中生成，并允许深入了解其功能。

欢迎来到CARLA.  

<div class="build-buttons">
<p>
<a href="../build_linux" target="_blank" class="btn btn-neutral" title="Go to the latest CARLA release">
<b>Linux</b> build</a>
</p>
<p>
<a href="../build_windows" target="_blank" class="btn btn-neutral" title="Go to the latest CARLA release">
<b>Windows</b> build</a>
</p>
</div>

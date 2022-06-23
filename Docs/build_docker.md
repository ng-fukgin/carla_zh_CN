# CARLA in Docker

用户可以拉取基于 CARLA 版本的镜像在 Docker 容器中运行。 这对于以下用户很有用:

- 想要在不需要安装所有依赖项的情况下运行 CARLA
- 运行多个 CARLA 服务器并执行 GPU 映射
- 运行不带显示器的 CARLA 服务器

本教程解释了运行 CARLA 图像的要求以及如何使用 OpenGL 和 Vulkan 图形 APIs 运行图像.

- [__在你开始之前__](#before-you-begin)
- [__在容器中运行CARLA__](#running-carla-in-a-container)
- [__离屏模式__](#off-screen-mode)

---
## Before you begin

你需要安装以下环境:

- __Docker:__ 按照安装说明进行操作 [点击查看教程](https://docs.docker.com/engine/install/).
- __NVIDIA Container Toolkit:__ NVIDIA Container Toolkit 是一个库和工具集，可将 NVIDIA 图形设备赋予给 Linux 容器。 它专为在 Linux 主机系统上或在 Linux 的 Windows 子系统Version 2下的 Linux 发行版中运行的 Linux 容器而设计。 查看安装说明安装 `nvidia-docker2` 安装包[点击查看安装说明](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#installation-guide)..

!!! note
   Docker需要sudo以完成运行，根据[这个教程](https://docs.docker.com/install/linux/linux-postinstall/)来向docker sudo群组添加用户.

---
## 在容器中运行CARLA

__1. 拉取 CARLA 镜像.__

您可以提取最新的 CARLA 镜像或特定的发布版本。最新镜像是指[最新的打包版本](https://github.com/carla-simulator/carla/releases)。 要拉取映像，请运行以下命令之一:

```sh
# Pull the latest image
docker pull carlasim/carla:latest

# Pull a specific version
docker pull carlasim/carla:0.9.12
```

__2. 运行CARLA容器.__

不同版本的 CARLA 支持不同的图形 API，这会影响 Docker 映像可以运行的条件:

- 0.9.12 supports only Vulkan
- 0.9.7+ supports both Vulkan and OpenGL.


__CARLA 0.9.12__

使用显示器运行 CARLA:

```
sudo docker run --privileged --gpus all --net=host -e DISPLAY=$DISPLAY carlasim/carla:0.9.12 /bin/bash ./CarlaUE4.sh
```

在无屏幕模式下运行 CARLA:

```
sudo docker run --privileged --gpus all --net=host -v /tmp/.X11-unix:/tmp/.X11-unix:rw carlasim/carla:0.9.12 /bin/bash ./CarlaUE4.sh -RenderOffScreen
```

__CARLA 0.9.7 to 0.9.11__

用Vulkan运行CARLA:

```sh
sudo docker run --privileged --gpus all --net=host -e DISPLAY=$DISPLAY -e SDL_VIDEODRIVER=x11 -v /tmp/.X11-unix:/tmp/.X11-unix:rw carlasim/carla:0.9.11 /bin/bash ./CarlaUE4.sh -vulkan <-additonal-carla-flags>
```

!!! Note
   只要您的机器有显示器，此命令将允许您使用 Vulkan 运行 CARLA 映像。 有关在屏幕外模式下使用 Vulkan 运行的信息，请参阅[渲染文档](https://carla-docs.readthedocs.io/zh_CN/latest/adv_rendering_options/#off-screen-mode).

用OpenGL运行CARLA:

```sh
docker run -e DISPLAY=$DISPLAY --net=host --gpus all --runtime=nvidia carlasim/carla:<version> /bin/bash CarlaUE4.sh -opengl <-additonal-carla-flags>
```

__3. (选用) 配置 Docker 标志.__

上面的命令使用了一些 Docker 标志，可以根据你的需要进行配置:

- __Networking:__ The [`--net=host`](https://docs.docker.com/engine/reference/run/#network-settings) 参数将允许容器共享主机的整个网络。如果您希望将主机上的[特定端口](https://docs.docker.com/engine/reference/run/#expose-incoming-ports)映射到容器端口，请使用标志 `-p <host-ports>:<container-ports>`.
- __GPUs:__ 您可以选择将所有 GPU 与 `--gpus all` 结合使用, 或将特定 GPU 与 `--gpus '"device=<gpu_01>,<gpu_02>"'` 结合使用。 [点击获取更多信息](https://docs.docker.com/config/containers/resource_constraints/#gpu).

---

## 离屏模式

如果您在没有显示器的机器上运行 CARLA，则 OpenGL 不需要配置，但是您需要执行一些额外的步骤才能使用 CARLA 0.9.12 之前的 Vulkan 执行相同操作。 有关信息，请参阅[渲染文档](https://carla-docs.readthedocs.io/zh_CN/latest/adv_rendering_options/#off-screen-mode).

---

Any issues or doubts related with this topic can be posted in the CARLA forum.

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="Go to the CARLA forum">
CARLA forum</a>
</p>
</div>

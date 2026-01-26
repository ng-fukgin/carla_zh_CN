# 在 Docker 中构建虚幻引擎和 CARLA

本指南介绍了如何使用 Docker 从头开始构建虚幻引擎和 CARLA。生成的镜像随后可用于创建 CARLA 软件包或准备用于 CARLA 软件包的资产。此过程不应与用于在多个服务器上或在没有显示器的情况下运行 CARLA 的预构建 CARLA Docker 镜像相混淆。该镜像的文档可以在[这里](build_docker.md)找到。

- [__在开始之前__](#before-you-begin)
    - [__系统要求__](#system-requirements)
    - [__软件要求__](#software-requirements)
- [__构建镜像__](#building-the-images)
- [__下一步：软件包__](#next-steps-packages)

---

## 在开始之前

##### 系统要求

您需要满足以下系统要求：

- 64 位版本的 Docker 且操作系统为 Ubuntu 16.04+
- 至少 8GB 内存
- 初始容器构建过程至少需要 600GB 可用磁盘空间

##### 软件要求

__Docker:__ 

按照[这里](https://docs.docker.com/engine/install/)的安装说明安装 Docker。

__Python__: 

您需要安装 Python 3.6 或更高版本并正确设置系统路径。有关安装说明和 Python 文档，请查看[这里](https://www.python.org/downloads/)。

__虚幻引擎 GitHub 访问权限__:

从 0.9.12 版本开始，CARLA 使用虚幻引擎 4.26 的修改分支。该分支包含特定于 CARLA 的补丁。这将在 Docker 构建过程中被下载。对于此下载，__您需要有一个链接到虚幻引擎账户的 GitHub 账户__。如果您尚未设置，请在继续之前遵循[此指南](https://www.unrealengine.com/en-US/ue4-on-github)。您需要在构建过程中登录您的账户。

__CARLA:__

构建用于 CARLA 的虚幻引擎和 CARLA 本身所需的 Dockerfile 和工具位于 CARLA 源码仓库的 `Util/Docker` 目录中。

如果您还没有它，请使用以下命令下载仓库：

```sh
git clone https://github.com/carla-simulator/carla
```

---

## 构建镜像

以下步骤各需要很长时间。

__1. 构建 CARLA 先决条件镜像。__

以下命令将使用 `Prerequisites.Dockerfile` 构建一个名为 `carla-prerequisites` 的镜像。在此构建中，我们将安装编译器和所需工具，下载虚幻引擎 4.26 分支并进行编译。您需要提供登录详细信息作为构建参数，以便虚幻引擎下载成功：

```sh
docker build --build-arg EPIC_USER=<GitHubUserName> --build-arg EPIC_PASS=<GitHubPassword> -t carla-prerequisites -f Prerequisites.Dockerfile .
```

__2. 构建最终的 CARLA 镜像。__

以下命令将使用上一步创建的镜像，基于 CARLA 仓库的当前 master 分支（最新版本）构建最终的 CARLA 镜像：

```sh
docker build -t carla -f Carla.Dockerfile .
```

如果您想构建 CARLA 仓库的特定分支或标签，请运行以下命令：

```sh
docker build -t carla -f Carla.Dockerfile . --build-arg GIT_BRANCH=<branch_or_tag_name>
```

---

## 下一步：软件包

本指南中创建的 CARLA 镜像用于创建独立的 CARLA 软件包，或对地图或网格等资产进行打包，以便它们可以在 CARLA 软件包中使用。这是通过使用 `Util/Docker` 中的 `docker_tools.py` 脚本实现的。该脚本使用 [`docker-py`](https://github.com/docker/docker-py) 与 Docker 镜像进行交互。

`docker_tools.py` 脚本可用于：

- __创建 CARLA 软件包__：查看教程[这里](tuto_A_create_standalone.md#export-a-package-using-docker)
- __烘焙用于 CARLA 软件包的资产：__ 查看教程[这里](tuto_A_add_props.md#ingestion-in-a-carla-package)
- __准备地图以便在 CARLA 软件包中使用：__ 查看教程[这里](tuto_M_add_map_package.md)

---

与此主题相关的任何问题或疑虑都可以在 CARLA 论坛中发布。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="前往 CARLA 论坛">
CARLA 论坛</a>
</p>
</div>

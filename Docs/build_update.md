# Update CARLA

*   [__更新命令摘要__](#_1)  
*   [__获取最新的二进制版本__](#_2)  
*   [__更新 Linux 和 Windows 版本__](#linux-windows)  
	*   [清理构建](#_3)  
	*   [Pull from origin](#pull-from-origin)  
	*   [下载资产素材](#_4)  
	*   [登录服务器](#_5)  
*   [__获取开发资产__](#_6)  

要发布问题、疑问或建议，请随时登录 CARLA 官方论坛.

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="Go to the latest CARLA release">
CARLA forum</a>
</p>
</div>

---
## 更新命令摘要

<details>
<summary> Show command lines to update CARLA</summary>

```sh
# Update a CARLA packaged release. 
#   1. Delete the current one. 
#   2. Follow the Quick start installation to get the one desired. 


# Update Linux build. 
git checkout master
make clean
git pull origin master
./Update.sh


# Update Windows build. 
git checkout master
make clean
git pull origin master
#   Erase the content in `Unreal\CarlaUE4\Content\Carla`.
#   Go to `\Util\ContentVersions.txt`.
#   Download the latest content.
#   Extract the new content in `Unreal\CarlaUE4\Content\Carla`. 


# Get development assets. 
#   Delete the `/Carla` folder containing previous assets. 
#   Go to the main carla folder.
git clone https://bitbucket.org/carla-simulator/carla-content Unreal/CarlaUE4/Content/Carla

```
</details>

---
## 获取最新的二进制版本 

二进制版本是预先打包的，因此与特定版本的 CARLA 相关联。要获取最新版本，请删除以前的版本并按照 [快速开始安装](start_quickstart.md) 获取所需的版本。

版本列在 CARLA 存储库的 __Development__ 中。还有一个高度实验性的 __Nightly build__ 包含最新的 CARLA 状态.  


<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/blob/master/Docs/download.md" target="_blank" class="btn btn-neutral" title="Go to the list of CARLA releases">
<span class="icon icon-github"></span> Get releases</a>
</p>

<p>
<a href="http://carla-releases.s3.amazonaws.com/Linux/Dev/CARLA_Latest.tar.gz" target="_blank" class="btn btn-neutral" title="Go to the nightly CARLA build">
<span class="icon fa-cloud-download"></span> Get the nightly build</a>
</p>
</div>

---
## 更新 Linux 和 Windows 版本


确保在更新前位于本地`master`分支。 然后，将更改合并或变基到其他分支并解决可能的冲突. 

```sh 
git checkout master
```

### Clean the build

转到主 CARLA 文件夹并删除先前构建生成的二进制文件和临时文件.
```sh 
make clean
```

### Pull from origin

Get the current version from `master` in the CARLA repository. 
```sh
git pull origin master
```

### 下载资产素材

__Linux.__
```sh
./Update.sh
```

__Windows.__  

__1.__ 在 `Unreal\CarlaUE4\Content\Carla`中抹去之前的版本  .  
__2.__ 访问 `\Util\ContentVersions.txt`.  
__3.__ 为 `latest`下载内容.  
__4.__ 从 `Unreal\CarlaUE4\Content\Carla`中提取新内容.  

!!! Note
       为了配合 CARLA 团队正在开发的内容，请前往下方 __获取开发资产__ . 

### 登录服务器

在spectator view 中运行服务器以确保一切正常.

```sh
make launch
```

---
## 获取开发资产

CARLA 团队使用仍在开发中的资产。这些模型和地图有一个 [公共 git 存储库][contentrepolink] CARLA 团队定期推送最新更新。资产未完成，仅推荐开发者使用.  

为了处理这个存储库，建议安装 [git-lfs][gitlfslink]。 存储库会定期修改，并且 git-lfs 可以更快地处理大型二进制文件.

要克隆存储库，请转到主 CARLA 目录并运行以下命令, __转到主 CARLA 目录__ and run the following command.  

```sh
git clone https://bitbucket.org/carla-simulator/carla-content Unreal/CarlaUE4/Content/Carla
```

!!! Warning
   在克隆存储库之前删除包含资产的 `/Carla` 文件夹。 否则会显示错误。 

[contentrepolink]: https://bitbucket.org/carla-simulator/carla-content
[gitlfslink]: https://github.com/git-lfs/git-lfs/wiki/Installation

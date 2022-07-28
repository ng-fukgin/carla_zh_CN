# Contributing to CARLA

CARLA 团队很高兴接受任何愿意合作的人的贡献。根据贡献者的能力，有不同的方式可以为项目做出贡献。团队将尽可能多地工作，以便将贡献成功集成到 CARLA 中。

加入我们，不要犹豫！!  

*   [__报告bugs__](#report-bugs)  
*   [__请求功能__](#request-features)  
*   [__贡献代码__](#code-contributions)  
	*   [学习 Unreal Engine](#learn-about-unreal-engine)  
	*   [开始之前](#before-getting-started)  
	*   [编码标准](#coding-standard)  
	*   [提交](#submission)  
	*   [清单](#checklist)  
*   [__艺术贡献__](#art-contributions)  
*   [__文档贡献__](#docs-contributions)  

---
## Report bugs

可以在 GitHub的[问题部分][issueslink]报告问题。在报告新错误之前，请务必进行一些检查。

__1.检查bugs是否有冷报告过.__ 在 GitHub 上的同一问题部分中查找它。

__2. 阅读文档.__ 确保问题是bug，而不是对 CARLA 的不熟悉操作。阅读[文档][docslink]中与问题相关的页面并查看[常见问题解答][faqlink]页面。
Issues can be reported in the [issue section] on GitHub. Before reporting a new bug, make sure to do some checkups.  


[issueslink]: https://github.com/carla-simulator/carla/issues
[docslink]: http://carla.readthedocs.io
[faqlink]: build_faq.md

---
## Request features

新功能的想法也是一种很好的贡献方式。 任何可以改善用户体验的建议都可以点击 [这里][frlink]提交到中 GitHub相应的 部分。

[frlink]: https://github.com/carla-simulator/carla/issues?q=is%3Aissue+is%3Aopen+label%3A%22feature+request%22+sort%3Acomments-desc

---
## Code contributions

在开始动手编码之前，请查看 [issue board][issueboard] 以检查团队已经在做什么，以避免任务重叠。 如有疑问或讨论如何进行编码，请联系我们（或发送电子邮件至 <carla.simulator@gmail.com>）.  

为了开始工作，[请fork CARLA 存储库](https://docs.github.com/en/enterprise/2.13/user/articles/fork-a-repo)，并在您的计算机中克隆所述 fork。 请 [保持你的 fork ](https://docs.github.com/en/enterprise/2.13/user/articles/syncing-a-fork) 与原始存储库同步。

[issueboard]: https://github.com/carla-simulator/carla/issues

### Learn about Unreal Engine

可以在 Unreal 的 [C++ Programming Tutorials][ue4tutorials] 中找到使用 UE4 进行 C++ 编程的基本介绍。你可可以去网上找其他资源，但是其中一些不是免费的。 [Udemy 的 Unreal C++ 课程][ue4course] 的知识非常完整，而且课程的价格非常实惠。

[ue4tutorials]: https://docs.unrealengine.com/latest/INT/Programming/Tutorials/
[ue4course]: https://www.udemy.com/unrealcourse/

### Before getting started

查看 [CARLA Design](index.md)<!-- @todo --> 文档，了解组成 CARLA 的不同模块。 选择最合适的一个
保留新功能。 如果在此过程中出现任何疑问，请随时联系 [Discord 服务器](https://discord.com/invite/8kqACuC) 中的团队。

### Coding standard

提交新代码时遵循当前的[编码标准](cont_coding_standard.md)。

### Submission

贡献和新功能不会直接合并到“master”分支，而是合并到名为“dev”的中间分支。 这个 [Gitflow](https://nvie.com/posts/a-successful-git-branching-model/) 分支模型使维护稳定的 master 分支变得更加容易。 此模型需要特定的贡献工作流程。 

*   始终保持您的 `dev` 分支更新为最新的更改。
*   在名为 `username/name_of_the_contribution` 的 `dev` 中开发子分支的贡献
*   一旦贡献准备就绪，从你的分支提交一个拉取请求到 `dev`。 填写描述时尽量做到描述性强。 请注意，在合并之前需要通过一些检查新代码。 检查由持续集成系统自动运行。 如果检查成功，将出现一个绿色的勾号。 如果是红色标记，请相应地更正代码。

一旦贡献被合并到 `dev` 中，它就可以与其他新功能一起测试。 到下一次发布时，`dev` 分支将合并到 `master` 中，并且贡献将可用并公布。

### Checklist  

*   [ ] 您的分支与 `dev` 分支是最新的，并已通过最新更改进行测试.  
*   [ ] 如有必要，扩展自述文件/文档.  
*   [ ] 代码编译正确.  
*   [ ] 所有通过“make check”的测试.  

---
## Art contributions

艺术贡献包括在 CARLA 中使用的车辆、步行者、地图或任何其他类型的资产。 它们存储在 BitBucket 存储库中，该存储库有一些帐户空间限制。 出于上述原因，贡献者将不得不与 CARLA 团队取得联系，并要求他们在内容存储库上为贡献创建一个分支。  

__1. 创建一个 BitBucket 帐户.__ 访问 [Bitbucket 页面](https://bitbucket.org).  

__2. 联系美术团队以获取对内容存储库的访问权限.__ 加入 [Discord 服务器](https://discord.com/invite/8kqACuC)。 转到 __Contributors__ 频道并请求访问内容存储库.  

__3. 每个贡献者都会创建一个分支.__ 该分支将被命名为 `contributors/contributor_name`。 该用户所做的所有贡献都应在相应的分支中进行.  

__4. Build CARLA.__ 为了做出贡献，CARLA 构建是必要的。 按照说明在 [Linux](https://carla.readthedocs.io/en/latest/build_linux/) 或 [Windows](https://carla.readthedocs.io/en/latest/build_windows/) 中构建

__5. 下载内容存储库.__ 按照[此处](https://carla.readthedocs.io/en/latest/build_update/#get-development-assets)中的说明更新内容.

__6. 更新分支以与 master 同步.__ 应始终使用 master 中的最新更改更新分支.  

__7.上传贡献.__ 进行相应的更改并将分支推送到源.  

__8. 等待美术组检查.__ 上传贡献后，团队将检查所有内容是否已准备好与 master 合并.  


---
## Docs contributions

如果缺少某些文档、文档表述含糊不清或不准确，则可以像报告任何其他错误一样报告（阅读上一节关于[如何报告错误](#report-bugs)）。 用户也可以通过编写文档来做出贡献.  

该文档由 [Markdown](https://www.markdownguide.org/) 和 HTML 标记混合编写，并带有一些额外的 CSS 代码，用于诸如表格或 [城镇滑块](https://carla.readthedocs.io/en/latest/core_map/#carla-maps) 等功能 。 按照下面的步骤开始编写文档。

!!! Important
    要提交文档贡献，请按照上面 [代码贡献](#submission) 中解释的相同工作流程进行操作。 总而言之，贡献是在“dev”的子分支中进行的，并合并到该分支.  

__1. 从源代码构建 CARLA.__ 按照文档中的步骤在 [Linux](build_linux.md) 或 [Windows](build_windows.md) 上构建.  


__2. 安装 [MkDocs](http://www.mkdocs.org/).__  MkDocs 是一个静态网站生成器，用于构建文档. 

```sh
sudo pip install mkdocs
```

__3. 可视化文档.__ 在 CARLA 主文件夹中，运行以下命令并单击终端中出现的链接 (http://127.0.0.1:8000) 以打开文档的本地可视化。

```sh
mkdocs serve
```
__4. 创建一个 git 分支.__ 确保在创建新分支时位于 `dev` 分支中（更新为最新更改）. 

```sh
git checkout -b <contributor_name>/<branch_name>
```

__5. 编写文档.__ 按照 [documentation standard](cont_doc_standard.md) 页面中的指南编辑文件.  

__6. 提交更改.__ 在 GitHub 存储库中创建一个拉取请求，并添加一位建议的审阅者。 填写拉取请求描述时，尽量做到描述性强.  

__7. 等待审核.__ 团队将检查一切是否已准备好合并或是否需要任何更改。

!!! Warning
   必须使用 `dev` 分支中的最新更新更新本地存储库.  
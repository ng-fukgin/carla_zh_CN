# 如何发布新版本
> _本文档适用于希望发布新版本的开发者。_
1. **确保内容是最新的。**<br>
   请参阅[升级内容](tuto_D_contribute_assets.md)。
2. **在必要时增加CARLA版本。**<br>
   在以下文件中增加版本：_DefaultGame.ini_, _Carla.uplugin_, _setup.py_, _ContentVersions.txt_. 使用grep查找当前版本，确保不遗漏任何引用。
3. **清理CHANGELOG.md。**<br>
   确保更改日志是最新的，如有必要，重新措辞和重组；考虑哪些项目对用户更重要。
4. **提交更改并添加新标签。**<br>
   一旦所有更改都已提交，使用`git tag -a X.X.X`添加新标签（将`X.X.X`替换为最新版本）。将此版本的更改日志作为标签消息添加。
5. **标记内容仓库。**<br>
   在_ContentVersions.txt_中与当前提交相同的提交处添加一个类似的标签。
6. **推送更改。**<br>
   将所有更改推送到两个仓库，如果需要，请使用`git push --tags`推送标签。如果需要，创建一个Pull Request。
7. **编辑GitHub发行版。**<br>
   访问[GitHub发行版](https://github.com/carla-simulator/carla/releases)并创建一个新的发行版，基于新创建的标签。等待Jenkins完成发布带有最新版本的构建，并将下载链接添加到新创建的发行版中。

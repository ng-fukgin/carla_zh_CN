# 如何升级内容

我们的内容位于一个单独的 [Git LFS 存储库][contentrepolink]。 作为我们构建系统的一部分，我们会生成并上传一个包，其中包含此内容的最新版本，并带有当前日期和提交标记。 我们会定期升级 [CARLA 存储库][carlarepolink]，并提供指向最新版本内容包的链接。 本文档包含将此链接更新至最新版本所需的手动步骤。

1. **复制要链接的内容包的标签。**<br>
    这个标签可以通过查看最新的 [Jenkins build][jenkinslink] 的 artifacts 部分中生成的包名称来找到，例如，`20190617_086f97f.tar.gz`。

2. **将标签粘贴到 ContentVersions.txt 中。**<br>
    [编辑 ContentVersions.txt][cvlink] 通过将标签粘贴到文件末尾，例如 `Latest: 20190617_086f97f`（没有 `.tar.gz` 部分）。

3. **打开一个拉取请求。**<br>
    提交更改并打开一个新的 Pull Request。

[contentrepolink]: https://bitbucket.org/carla-simulator/carla-content
[carlarepolink]: https://github.com/carla-simulator/carla
[jenkinslink]: http://35.181.165.160:8080/blue/organizations/jenkins/carla-content/activity
[cvlink]: https://github.com/carla-simulator/carla/edit/master/Util/ContentVersions.txt

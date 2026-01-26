# C++ 参考 
我们使用 Doxygen 来生成 C++ 代码的文档：

[Libcarla/Source](http://carla.org/Doxygen/html/dir_b9166249188ce33115fd7d5eed1849f2.html)<br>
[Unreal/CarlaUE4/Source](http://carla.org/Doxygen/html/dir_733e9da672a36443d0957f83d26e7dbf.html)<br>
[Unreal/CarlaUE4/Carla/Plugins](http://carla.org/Doxygen/html/dir_8fc34afb5f07a67966c78bf5319f94ae.html)

生成的文档可以通过此链接访问： **<http://carla.org/Doxygen/html/index.html>**

!!! note
    文档更新由 GitHub 自动完成。

### 创建 Doxygen 文档

!!! important
    生成文档需要 [Doxygen](http://www.doxygen.nl/index.html)，绘图工具包需要 [Graphviz](https://www.graphviz.org/)。

1- 使用以下命令安装 Doxygen 和 Graphviz：

```sh
# linux
> sudo apt-get install doxygen graphviz
```

2- 安装完成后，进入 _Doxyfile_ 文件所在的项目根目录，并运行以下命令：

```sh
> doxygen
```

它将开始构建文档网页。  
生成的网页可以在 Doxygen/html/ 中找到。

3- 在浏览器中打开 _index.html_。现在你就拥有了本地的 C++ 文档！

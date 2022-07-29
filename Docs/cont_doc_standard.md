#文档标准

本文档将作为一些规则的指南和示例，这些规则需要遵循才能对文档做出贡献

*   [__文档结构__](#docs-structure)  
*   [__规则__](#rules)  
*   [__例外__](#exceptions)  

---
## Docs structure

我们使用 Markdown 和 HTML 标签的组合来自定义文档以及 [`extra.css`](https://github.com/carla -simulator/carla/tree/master/Docs/extra.css) 文件.
要更新 Python API 文档，而不是直接修改 Markdown，您需要在 [`carla/PythonAPI/docs/`][fileslink] 中编辑相应的 YAML 文件并运行 [`doc_gen.py`][scriptlink] 或 `make PythonAPI .docs`.

这将在 `carla/Docs/` 中重新生成相应的 Markdown 文件，然后可以将其输入 `mkdocs`.

[fileslink]: https://github.com/carla-simulator/carla/tree/master/PythonAPI/docs
[scriptlink]: https://github.com/carla-simulator/carla/blob/master/PythonAPI/docs/doc_gen.py

---
## Rules

* 在章节之间和文档末尾始终留空行。
* 写作不应超过 100 列，HTML 相关内容、markdown 表、代码片段和引用链接除外。
* 如果内联链接超出限制，请使用引用的 `[name][reference_link]` 标记符号 `[reference_link]: https://` 而不是 `[name](https://)`。
* 使用 `<br>` 进行内联跳转，而不是在行尾留下两个空格。
* 在新页面的开头使用 `<h1>Title</h1>` 来制作标题或 `<hx>Heading<hx>` 来在导航栏制作**不会显示**的标题 。
* 使用 `-----` 为标题或层次结构加下划线来制作标题并将它们显示在导航栏中。

---
## Exceptions

 * 通过 Python API 参考等 Python 脚本生成的文档

简便的Markdown [cheatsheet] [cheatlink]。

[cheatlink]: https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

# 编码标准

*   [__一般的__](#general)  
*   [__Python__](#python)  
*   [__C++__](#c++)  

---
## General

  * 使用空格，而不是制表符(tab).
  * 避免添加尾随空格，因为它会在差异对比中产生噪音.

---
## Python

* 注释不超过 80 列，代码不超过 120 列.
* 所有代码必须与 Python 2.7 和 3.7 兼容.
* [Pylint][pylintlink] 不应给出任何错误或警告（很少有例外适用于外部类，如 `numpy` 和 `pygame`，请参阅我们的 `.pylintrc`）.
* Python 代码遵循 [PEP8 风格指南][pep8link]（尽可能使用 `autopep8`）.
  
[pylintlink]: https://www.pylint.org/
[pep8link]: https://www.python.org/dev/peps/pep-0008/

---
## C++

* 注释不应超过 80 列，如果代码更清晰，代码在极少数情况下可能会超出此限制.
* 编译不应给出任何错误或警告（`clang++-8 -Wall -Wextra -std=C++14 -Wno-missing-braces`）.
* 禁止使用 `throw`，改用 `carla::throw_exception`.
* Unreal C++ 代码（CarlaUE4 和 Carla 插件）遵循[Unreal Engine's Coding Standard][ue4link]，除此之外，使用空格而不是制表符(tab).
* LibCarla 使用 [Google's style guide][googlelink] 的变体.
 * 如果在服务器端使用代码，则使用 `try-catch` 块应由`#ifndef LIBCARLA_NO_EXCEPTIONS` 包围.

[ue4link]: https://docs.unrealengine.com/latest/INT/Programming/Development/CodingStandard/
[googlelink]: https://google.github.io/styleguide/cppguide.html

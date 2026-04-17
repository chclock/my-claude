---
paths:
  - "**/*.py"
  - "**/*.pyi"
---
# Python 编码风格

> 本文件通过 Python 特定内容扩展了 [common/coding-style.md](../common/coding-style.md)。

## 标准

- 遵循 **PEP 8** 约定
- 所有函数签名使用 **类型注解**

## 不可变性

首选不可变数据结构：

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class User:
    name: str
    email: str

from typing import NamedTuple

class Point(NamedTuple):
    x: float
    y: float
```

## 格式化

- 使用 **black** 进行代码格式化
- 使用 **isort** 进行导入排序
- 使用 **ruff** 进行 linting

## 参考

参见 skill：`python-patterns`，获取全面的 Python 惯用写法和解耦。

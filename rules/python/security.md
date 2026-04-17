---
paths:
  - "**/*.py"
  - "**/*.pyi"
---
# Python 安全

> 本文件通过 Python 特定内容扩展了 [common/security.md](../common/security.md)。

## 密钥管理

```python
import os
from dotenv import load_dotenv

load_dotenv()

api_key = os.environ["OPENAI_API_KEY"]  # 如果缺失则引发 KeyError
```

## 安全扫描

- 使用 **bandit** 进行静态安全分析：
  ```bash
  bandit -r src/
  ```

## 参考

有关 Django 特定的安全指南（如果适用），请参见 skill：`django-security`。

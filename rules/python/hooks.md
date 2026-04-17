---
paths:
  - "**/*.py"
  - "**/*.pyi"
---
# Python Hooks

> 本文件通过 Python 特定内容扩展了 [common/hooks.md](../common/hooks.md)。

## PostToolUse Hooks

在 `~/.claude/settings.json` 中配置：

- **black/ruff**：编辑 `.py` 文件后自动格式化
- **mypy/pyright**：编辑 `.py` 文件后运行类型检查

## 警告

- 警告编辑文件中的 `print()` 语句（改用 `logging` 模块）

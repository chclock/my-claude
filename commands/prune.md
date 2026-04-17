---
name: prune
description: Delete pending instincts older than 30 days that were never promoted
command: true
---

# 清理待处理的 Instincts

删除已过期（超过 30 天）的待处理 instincts，这些 instinct 是自动生成的，但从未被审查或提升。

## 实现方式

使用插件根路径运行 instinct CLI：

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" prune
```

或者如果未设置 `CLAUDE_PLUGIN_ROOT`（手动安装）：

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py prune
```

## 使用方法

```
/prune                    # 删除超过 30 天的 instincts
/prune --max-age 60      # 自定义时间阈值（天）
/prune --dry-run         # 预览而不删除
```

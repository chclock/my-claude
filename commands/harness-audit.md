# Harness 审计命令

运行确定性的仓库 harness 审计并返回优先级评分卡。

## 用法

`/harness-audit [scope] [--format text|json] [--root path]`

- `scope`（可选）：`repo`（默认）、`hooks`、`skills`、`commands`、`agents`
- `--format`：输出样式（默认为 `text`，自动化使用 `json`）
- `--root`：审计指定路径而非当前工作目录

## 确定性引擎

始终运行：

```bash
node scripts/harness-audit.js <scope> --format <text|json> [--root <path>]
```

此脚本是评分和检查的权威来源。不要创建额外的维度或临时分数。

评分标准版本：`2026-03-30`。

该脚本计算 7 个固定类别（每个标准化为 `0-10`）：

1. 工具覆盖率
2. 上下文效率
3. 质量门禁
4. 内存持久化
5. 评估覆盖率
6. 安全护栏
7. 成本效率

分数源自明确的文件/规则检查，对于同一提交是可重现的。该脚本默认审计当前工作目录，并自动检测目标是 ECC 仓库本身还是使用 ECC 的消费项目。

## 输出契约

返回：

1. `overall_score` 占 `max_score` 的比例（`repo` 为 70；范围较小的审计更小）
2. 类别分数和具体发现
3. 失败检查及确切文件路径
4. 确定性输出的前 3 个操作（`top_actions`）
5. 建议接下来应用的 ECC 技能

## 检查清单

- 直接使用脚本输出；不要手动重新评分。
- 如果请求 `--format json`，原样返回脚本 JSON。
- 如果请求文本，总结失败的检查和主要操作。
- 包含 `checks[]` 和 `top_actions[]` 中的确切文件路径。

## 示例结果

```text
Harness 审计 (repo): 66/70
- 工具覆盖率: 10/10 (10/10 分)
- 上下文效率: 9/10 (9/10 分)
- 质量门禁: 10/10 (10/10 分)

前 3 个操作：
1) [安全护栏] 在 hooks/hooks.json 中添加提示/工具预检安全护栏。(hooks/hooks.json)
2) [工具覆盖率] 同步 commands/harness-audit.md 和 .opencode/commands/harness-audit.md。(.opencode/commands/harness-audit.md)
3) [评估覆盖率] 增加 scripts/hooks/lib 的自动化测试覆盖率。(tests/)
```

## 参数

$ARGUMENTS：
- `repo|hooks|skills|commands|agents`（可选的作用域）
- `--format text|json`（可选的输出格式）

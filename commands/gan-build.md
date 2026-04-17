从 $ARGUMENTS 解析以下内容：
1. `brief` — 用户关于要构建内容的一行描述
2. `--max-iterations N` — （可选，默认 15）最大生成器-评估器循环次数
3. `--pass-threshold N` — （可选，默认 7.0）通过的加权分数
4. `--skip-planner` — （可选）跳过规划器，假设 spec.md 已存在
5. `--eval-mode MODE` — （可选，默认 "playwright"）其中之一：playwright、screenshot、code-only

## GAN 风格 Harness 构建

此命令协调受 Anthropic 2026 年 3 月 harness 设计论文启发的三代理构建循环。

### 阶段 0: 设置
1. 在项目根目录创建 `gan-harness/` 目录
2. 创建子目录：`gan-harness/feedback/`、`gan-harness/screenshots/`
3. 如果尚未初始化 git，则初始化
4. 记录开始时间和配置

### 阶段 1: 规划（Planner Agent）
除非设置了 `--skip-planner`：
1. 通过 Task 工具启动 `gan-planner` agent，并提供用户的简要说明
2. 等待它生成 `gan-harness/spec.md` 和 `gan-harness/eval-rubric.md`
3. 向用户显示规范摘要
4. 进入阶段 2

### 阶段 2: 生成器-评估器循环
```
iteration = 1
while iteration <= max_iterations:

    # 生成
    通过 Task 工具启动 gan-generator agent：
    - 阅读 spec.md
    - 如果 iteration > 1：阅读 feedback/feedback-{iteration-1}.md
    - 构建/改进应用程序
    - 确保开发服务器正在运行
    - 提交更改

    # 等待生成器完成

    # 评估
    通过 Task 工具启动 gan-evaluator agent：
    - 阅读 eval-rubric.md 和 spec.md
    - 测试实时应用程序（模式：playwright/screenshot/code-only）
    - 根据评分标准评分
    - 将反馈写入 feedback/feedback-{iteration}.md

    # 等待评估器完成

    # 检查分数
    阅读 feedback/feedback-{iteration}.md
    提取加权总分

    if score >= pass_threshold:
        记录 "在第 {iteration} 次迭代时通过，分数 {score}"
        Break

    if iteration >= 3 且分数在过去 2 次迭代中没有改善：
        记录 "检测到平台期 — 提前停止"
        Break

    iteration += 1
```

### 阶段 3: 摘要
1. 阅读所有反馈文件
2. 显示最终分数和迭代历史
3. 显示分数进展：`迭代 1: 4.2 → 迭代 2: 5.8 → ... → 迭代 N: 7.5`
4. 列出最终评估中的任何剩余问题
5. 报告总时间和估算成本

### 输出

```markdown
## GAN Harness 构建报告

**Brief:** [original prompt]
**Result:** PASS/FAIL
**Iterations:** N / max
**Final Score:** X.X / 10

### 分数进展
| Iter | Design | Originality | Craft | Functionality | Total |
|------|--------|-------------|-------|---------------|-------|
| 1 | ... | ... | ... | ... | X.X |
| 2 | ... | ... | ... | ... | X.X |
| N | ... | ... | ... | ... | X.X |

### 剩余问题
- [最终评估中的任何问题]

### 创建的文件
- gan-harness/spec.md
- gan-harness/eval-rubric.md
- gan-harness/feedback/feedback-001.md 到 feedback-NNN.md
- gan-harness/generator-state.md
- gan-harness/build-report.md
```

将完整报告写入 `gan-harness/build-report.md`。

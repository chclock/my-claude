# 文件翻译情况说明

本文档列出项目中所有 Markdown 文件的翻译状态和文件作用。

---

## 目录结构

```
my-claude/
├── CLAUDE.MD              # 根目录配置文件
├── TRANSLATION_STATUS.md  # 本文件
├── commands/              # slash 命令定义
├── agents/                # Agent 智能体定义
├── rules/                 # 代码规则集
├── skills/                # 技能文档
└── examples/             # 示例项目配置
```

---

## commands/ — 命令文件

| 文件                 | 翻译状态  | 文件作用                                |
| -------------------- | --------- | --------------------------------------- |
| build-fix.md         | ✅ 已翻译 | 构建修复命令，逐步修复构建和类型错误    |
| aside.md             | ✅ 已翻译 | 侧边栏命令，临时问答不中断任务          |
| hookify.md           | ✅ 已翻译 | 创建 hook 规则防止不良行为              |
| hookify-list.md      | ✅ 已翻译 | 列出所有配置的 hookify 规则             |
| hookify-configure.md | ✅ 已翻译 | 启用/禁用 hookify 规则                  |
| hookify-help.md      | ✅ 已翻译 | hookify 系统帮助                        |
| checkpoint.md        | ✅ 已翻译 | 创建或验证工作流检查点                  |
| plan.md              | ✅ 已翻译 | 需求重述、风险评估、创建实施计划        |
| code-review.md       | ✅ 已翻译 | 本地更改或 GitHub PR 的代码审查         |
| review-pr.md         | ✅ 已翻译 | 使用专业智能体进行全面的 PR 审查        |
| feature-dev.md       | ✅ 已翻译 | 结构化功能开发工作流                    |
| e2e.md               | ✅ 已翻译 | E2E 测试命令（旧版兼容桥接）            |
| eval.md              | ✅ 已翻译 | Eval 命令（旧版兼容桥接）               |
| docs.md              | ✅ 已翻译 | 文档查找命令（遗留兼容层）              |
| evolve.md            | ✅ 已翻译 | 分析 instincts 并建议或生成进化结构     |
| agent-sort.md        | ✅ 已翻译 | Agent Sort（旧版兼容桥接）              |
| claw.md              | ✅ 已翻译 | Claw 命令（旧版兼容桥接）               |
| devfleet.md          | ✅ 已翻译 | DevFleet（旧版兼容桥接）                |
| context-budget.md    | ✅ 已翻译 | Context Budget 优化器（遗留兼容层）     |
| prompt-optimize.md   | ✅ 已翻译 | Prompt Optimize（传统兼容层）           |
| tdd.md               | ✅ 已翻译 | TDD 命令（旧版兼容桥接）                |
| verify.md            | ✅ 已翻译 | 验证命令（旧版兼容桥接）                |
| rules-distill.md     | ✅ 已翻译 | Rules Distill（旧版兼容入口）           |
| orchestrate.md       | ✅ 已翻译 | Orchestrate 命令（传统兼容层）          |
| multi-workflow.md    | ✅ 已翻译 | 多模型协作开发工作流                    |
| multi-plan.md        | ✅ 已翻译 | 多模型协作规划                          |
| multi-execute.md     | ✅ 已翻译 | 多模型协作执行                          |
| multi-backend.md     | ✅ 已翻译 | 后端主导开发工作流                      |
| multi-frontend.md    | ✅ 已翻译 | 前端主导开发工作流                      |
| model-route.md       | ✅ 已翻译 | 根据复杂度和预算推荐最佳模型层级        |
| loop-start.md        | ✅ 已翻译 | 启动托管自主循环模式                    |
| loop-status.md       | ✅ 已翻译 | 检查活动循环状态、进度和失败信号        |
| save-session.md      | ✅ 已翻译 | 保存当前会话状态到文件                  |
| resume-session.md    | ✅ 已翻译 | 加载最近的会话文件并恢复工作            |
| sessions.md          | ✅ 已翻译 | 管理 Claude Code 会话历史               |
| learn.md             | ✅ 已翻译 | 分析当前会话并提取可重用的模式          |
| learn-eval.md        | ✅ 已翻译 | 提取、评估然后保存模式                  |
| gan-build.md         | ✅ 已翻译 | GAN 构建命令                            |
| gan-design.md        | ✅ 已翻译 | GAN 设计命令                            |
| jira.md              | ✅ 已翻译 | 检索 Jira ticket、分析需求、更新状态    |
| gradle-build.md      | ✅ 已翻译 | Gradle 构建修复（Android/KMP）          |
| harness-audit.md     | ✅ 已翻译 | 运行仓库 harness 审计并返回优先级评分卡 |
| instinct-export.md   | ✅ 已翻译 | 导出 instincts 到文件                   |
| instinct-import.md   | ✅ 已翻译 | 从文件或 URL 导入 instincts             |
| instinct-status.md   | ✅ 已翻译 | 显示已学习的 instincts                  |
| python-review.md     | ✅ 已翻译 | Python 代码审查                         |
| go-build.md          | ✅ 已翻译 | Go 构建修复                             |
| go-review.md         | ✅ 已翻译 | Go 代码审查                             |
| go-test.md           | ✅ 已翻译 | Go TDD 命令                             |
| rust-build.md        | ✅ 已翻译 | Rust 构建修复                           |
| rust-review.md       | ✅ 已翻译 | Rust 代码审查                           |
| rust-test.md         | ✅ 已翻译 | Rust TDD 命令                           |
| cpp-build.md         | ✅ 已翻译 | C++ 构建与修复                          |
| cpp-review.md        | ✅ 已翻译 | C++ 代码审查                            |
| cpp-test.md          | ✅ 已翻译 | C++ TDD 命令                            |
| kotlin-build.md      | ✅ 已翻译 | Kotlin 构建修复                         |
| kotlin-review.md     | ✅ 已翻译 | Kotlin 代码审查                         |
| kotlin-test.md       | ✅ 已翻译 | Kotlin TDD 命令                         |
| flutter-build.md     | ✅ 已翻译 | Flutter 构建与修复                      |
| flutter-review.md    | ✅ 已翻译 | Flutter 代码审查                        |
| flutter-test.md      | ✅ 已翻译 | Flutter 测试命令                        |
| pm2.md               | ✅ 已翻译 | PM2 初始化，自动生成 PM2 服务命令       |
| setup-pm.md          | ✅ 已翻译 | PM 设置命令，配置首选的包管理器         |
| projects.md          | ✅ 已翻译 | 列出已知项目及其 instinct 统计          |
| promote.md           | ✅ 已翻译 | 将项目作用域 instincts 提升到全局       |
| prune.md             | ✅ 已翻译 | 清理超过 30 天的 pending instincts      |
| skill-create.md      | ✅ 已翻译 | 分析 git 历史提取编码模式生成 SKILL.md  |
| skill-health.md      | ✅ 已翻译 | 显示技能组合健康仪表板                  |
| update-codemaps.md   | ✅ 已翻译 | 分析代码库结构生成架构文档              |
| update-docs.md       | ✅ 已翻译 | 同步文档与代码库                        |
| test-coverage.md     | ✅ 已翻译 | 分析测试覆盖率，生成缺失测试            |
| refactor-clean.md    | ✅ 已翻译 | 安全识别并删除死代码                    |
| quality-gate.md      | ✅ 已翻译 | 按需运行 ECC 质量管道                   |
| prp-plan.md          | ✅ 已翻译 | 创建全面的功能实施计划                  |
| prp-implement.md     | ✅ 已翻译 | 执行实施计划并验证                      |
| prp-commit.md        | ✅ 已翻译 | 自然语言描述提交更改                    |
| prp-pr.md            | ✅ 已翻译 | 从当前分支创建 GitHub PR                |
| prp-prd.md           | ✅ 已翻译 | 交互式 PRD 生成器                       |

**commands/ 小计：75 个文件，全部已翻译**

---

## agents/ — Agent 智能体定义

| 文件                    | 翻译状态  | 文件作用                                   |
| ----------------------- | --------- | ------------------------------------------ |
| architect.md            | ✅ 已翻译 | 软件架构专家，系统设计、可扩展性、技术决策 |
| planner.md              | ✅ 已翻译 | 实施规划智能体                             |
| tdd-guide.md            | ✅ 已翻译 | 测试驱动开发指南                           |
| python-reviewer.md      | ✅ 已翻译 | Python 代码审查专家                        |
| csharp-reviewer.md      | ✅ 已翻译 | C# 代码审查专家                            |
| build-error-resolver.md | ✅ 已翻译 | 构建错误解决智能体                         |
| pr-test-analyzer.md     | ✅ 已翻译 | PR 测试分析智能体                          |
| chief-of-staff.md       | ✅ 已翻译 | 首席运营官智能体                           |
| doc-updater.md          | ✅ 已翻译 | 文档和代码地图专家                         |
| e2e-runner.md           | ✅ 已翻译 | 端到端测试运行器                           |
| refactor-cleaner.md     | ✅ 已翻译 | 死代码清理和重构专家                       |
| rust-reviewer.md        | ✅ 已翻译 | Rust 代码审查专家                          |
| security-reviewer.md    | ✅ 已翻译 | 安全漏洞检测专家                           |

**agents/ 小计：13 个文件，全部已翻译**

---

## rules/ — 代码规则集

### rules/common/ — 通用规则

| 文件                    | 翻译状态  | 文件作用                            |
| ----------------------- | --------- | ----------------------------------- |
| agents.md               | ✅ 已翻译 | Agent 编排指南                      |
| code-review.md          | ✅ 已翻译 | 代码审查指南                        |
| coding-style.md         | ✅ 已翻译 | 编码风格规范（不可变性、KISS、DRY） |
| development-workflow.md | ✅ 已翻译 | 开发工作流                          |
| git-workflow.md         | ✅ 已翻译 | Git 工作流规范                      |
| hooks.md                | ✅ 已翻译 | Hooks 系统规范                      |
| patterns.md             | ✅ 已翻译 | 设计模式指南                        |
| performance.md          | ✅ 已翻译 | 性能优化规范                        |
| security.md             | ✅ 已翻译 | 安全规范                            |
| testing.md              | ✅ 已翻译 | 测试规范                            |

**rules/common/ 小计：10 个文件，全部已翻译**

### rules/csharp/ — C# 特定规则

| 文件            | 翻译状态  | 文件作用      |
| --------------- | --------- | ------------- |
| coding-style.md | ✅ 已翻译 | C# 编码风格   |
| hooks.md        | ✅ 已翻译 | C# Hooks 配置 |
| patterns.md     | ✅ 已翻译 | C# 设计模式   |
| security.md     | ✅ 已翻译 | C# 安全规范   |
| testing.md      | ✅ 已翻译 | C# 测试规范   |

**rules/csharp/ 小计：5 个文件，全部已翻译**

### rules/python/ — Python 特定规则

| 文件            | 翻译状态  | 文件作用          |
| --------------- | --------- | ----------------- |
| coding-style.md | ✅ 已翻译 | Python 编码风格   |
| hooks.md        | ✅ 已翻译 | Python Hooks 配置 |
| patterns.md     | ✅ 已翻译 | Python 设计模式   |
| security.md     | ✅ 已翻译 | Python 安全规范   |
| testing.md      | ✅ 已翻译 | Python 测试规范   |

**rules/python/ 小计：5 个文件，全部已翻译**

### rules/web/ — Web 前端规则

| 文件              | 翻译状态  | 文件作用       |
| ----------------- | --------- | -------------- |
| coding-style.md   | ✅ 已翻译 | Web 编码风格   |
| hooks.md          | ✅ 已翻译 | Web Hooks 配置 |
| patterns.md       | ✅ 已翻译 | Web 设计模式   |
| security.md       | ✅ 已翻译 | Web 安全规范   |
| testing.md        | ✅ 已翻译 | Web 测试规范   |
| performance.md    | ✅ 已翻译 | Web 性能规范   |
| design-quality.md | ✅ 已翻译 | 设计质量规范   |

**rules/web/ 小计：7 个文件，全部已翻译**

**rules/ 总计：27 个文件，全部已翻译**

---

## skills/ — 技能文档

| 文件                     | 翻译状态  | 文件作用                                 |
| ------------------------ | --------- | ---------------------------------------- |
| python-patterns/SKILL.md | ✅ 已翻译 | Python 模式技能                          |
| python-testing/SKILL.md  | ✅ 已翻译 | Python 测试策略（pytest、TDD、fixtures） |
| seo/SKILL.md             | ✅ 已翻译 | SEO 技能                                 |

**skills/ 小计：3 个文件，全部已翻译**

---

## examples/ — 示例项目配置

| 文件                 | 翻译状态  | 文件作用                  |
| -------------------- | --------- | ------------------------- |
| CLAUDE.md            | ✅ 已翻译 | 示例项目级 CLAUDE.md 文件 |
| user-CLAUDE.md       | ✅ 已翻译 | 示例用户级 CLAUDE.md 文件 |
| django-api-CLAUDE.md | ✅ 已翻译 | Django REST API 项目示例  |

**examples/ 小计：3 个文件，全部已翻译**

---

## 总体统计

| 目录      | 已翻译  | 英文  | 总计    | 翻译率   |
| --------- | ------- | ----- | ------- | -------- |
| commands/ | 75      | 0     | 75      | **100%** |
| agents/   | 13      | 0     | 13      | **100%** |
| rules/    | 27      | 0     | 27      | **100%** |
| skills/   | 3       | 0     | 3       | **100%** |
| examples/ | 3       | 0     | 3       | **100%** |
| **总计**  | **121** | **0** | **121** | **100%** |

---

## 翻译规范说明

根据项目 `CLAUDE.MD` 中的翻译规范：

1. **Frontmatter 元数据不翻译**：保留 `name`、`description`、`origin` 等元数据不变
2. **正文内容翻译**：将标题、段落、列表等翻译成中文
3. **代码块保留**：代码示例中的注释可保留英文或适当翻译
4. **中文标点**：使用中文标点符号（，。：；？！""）
5. **标题翻译**：文件标题翻译为中文（如 "Build and Fix" → "构建修复"）

---

**翻译工作已全部完成！**

---

_最后更新：2026/04/17_

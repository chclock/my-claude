# Claude Code 命令、规范与技能使用指南

本文档说明项目中所有 Claude Code 命令（Commands）、规范（Rules）和技能（Skills）的触发条件和使用场景。

---

## 目录

1. [Slash 命令触发指南](#1-slash-命令触发指南)
2. [规范（Rules）自动应用机制](#2-规范rules自动应用机制)
3. [技能（Skills）激活条件](#3-技能skills激活条件)
4. [命令组合使用场景](#4-命令组合使用场景)
5. [快速参考表](#5-快速参考表)

---

## 1. Slash 命令触发指南

### 1.1 规划与分析类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/plan` | 开始新功能、重大重构、需求不清晰时 | 架构决策、多组件改动、复杂业务逻辑 |
| `/prp-plan` | 需要生成正式 PRD 或详细实施计划时 | 产品功能规划、技术方案设计 |
| `/multi-plan` | 需要多模型协作规划时 | 复杂系统、多团队并行开发 |
| `/model-route` | 选择困难、不确定用哪个模型时 | 根据复杂度/预算选择最佳模型层级 |

### 1.2 开发执行类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/build-fix` | 构建失败、类型错误时 | 编译错误、TS/ESLint 错误 |
| `/go-build` | Go 项目构建失败时 | Go 编译错误、go mod 问题 |
| `/rust-build` | Rust 项目构建失败时 | Cargo 错误、 borrow checker 问题 |
| `/kotlin-build` | Kotlin/Gradle 构建失败时 | Android/KMP 构建问题 |
| `/flutter-build` | Flutter 构建失败时 | Dart/Flutter 编译错误 |
| `/cpp-build` | C++ 构建失败时 | 编译错误、链接问题 |
| `/gradle-build` | Gradle 构建失败时 | Android/Java Gradle 问题 |
| `/tdd` | 遵循测试驱动开发时 | 先写测试、再写实现 |
| `/multi-execute` | 需要多模型并行执行时 | 大型任务分解、并行开发 |
| `/multi-backend` | 后端主导的多模型开发时 | API + 数据库 + 服务端逻辑 |
| `/multi-frontend` | 前端主导的多模型开发时 | React/Vue + 组件 + 样式 |

### 1.3 代码审查类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/code-review` | 代码审查、PR 检查时 | 本地改动审查、非正式 review |
| `/review-pr` | GitHub PR 需要全面审查时 | 正式 PR、 多视角审查 |
| `/python-review` | Python 代码审查时 | FastAPI/Django/Flask 项目 |
| `/go-review` | Go 代码审查时 | Go 项目最佳实践检查 |
| `/rust-review` | Rust 代码审查时 | Rust 安全性/性能审查 |
| `/cpp-review` | C++ 代码审查时 | C++ 性能和安全性 |
| `/kotlin-review` | Kotlin 代码审查时 | Android/Kotlin 检查 |
| `/flutter-review` | Flutter 代码审查时 | Dart/Flutter 最佳实践 |
| `/simplify` | 代码需要简化重构时 | 减少复杂度、提升可读性 |

### 1.4 测试相关类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/test-coverage` | 需要提升测试覆盖率时 | 达到 80%+ 覆盖率目标 |
| `/go-test` | Go TDD 开发时 | 测试先行开发模式 |
| `/rust-test` | Rust TDD 开发时 | Rust 测试模式 |
| `/cpp-test` | C++ TDD 开发时 | C++ 单元测试 |
| `/kotlin-test` | Kotlin TDD 开发时 | Kotlin 测试驱动开发 |
| `/flutter-test` | Flutter 测试时 | Widget/集成测试 |

### 1.5 Hooks 与规则类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/hookify` | 需要防止不良行为时 | 重复错误、模式纠正 |
| `/hookify-list` | 查看所有 hookify 规则时 | 检查现有规则 |
| `/hookify-configure` | 启用/禁用规则时 | 管理 hookify 规则 |
| `/hookify-help` | 需要 hookify 帮助时 | 了解 hookify 系统 |
| `/rules-distill` | 提取代码模式为规则时 | 从现有代码生成规则 |

### 1.6 学习与记忆类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/instinct-status` | 查看已学到的 instincts 时 | 检查项目/全局学习成果 |
| `/instinct-import` | 导入外部 instincts 时 | 分享的 pattern、团队规则 |
| `/instinct-export` | 导出 instincts 共享时 | 保存学习成果 |
| `/learn` | 从当前会话提取模式时 | 总结工作经验 |
| `/learn-eval` | 评估并保存模式时 | 验证模式有效性 |
| `/skill-create` | 从 git 历史生成技能时 | 分析团队编码习惯 |
| `/skill-health` | 检查技能健康状态时 | 查看技能组合仪表板 |
| `/evolve` | 将 instincts 进化为技能时 | 模式聚类、Agent 生成 |

### 1.7 项目管理类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/projects` | 列出所有项目及统计时 | 查看已知项目 |
| `/promote` | 将项目本能提升为全局时 | 共享团队-wide 规则 |
| `/prune` | 清理过期 instincts 时 | 清理 30 天+ pending |
| `/sessions` | 管理会话历史时 | 保存/恢复会话 |
| `/save-session` | 需要保存当前工作时 | 暂停、工作备份 |
| `/resume-session` | 恢复之前会话时 | 继续之前的工作 |
| `/checkpoint` | 创建工作流检查点时 | 重要节点标记 |
| `/loop-start` | 启动自主循环模式时 | 自动化任务循环 |
| `/loop-status` | 检查循环状态时 | 监控活动循环 |
| `/santa-loop` | 自主循环变体 | 实验性循环模式 |

### 1.8 文档与架构类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/update-codemaps` | 更新架构文档时 | 代码库结构分析 |
| `/update-docs` | 同步文档与代码时 | 文档保持最新 |
| `/refactor-clean` | 清理死代码时 | 删除未使用代码 |
| `/quality-gate` | 运行质量检查时 | ECC 质量管道 |
| `/orchestrate` | 多组件编排时 | 复杂工作流协调 |
| `/multi-workflow` | 多模型协作工作流时 | 团队协作开发 |
| `/jira` | 集成 Jira 时 | Ticket 检索、状态更新 |

### 1.9 部署与工具类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/pm2` | 初始化 PM2 服务时 | PM2 服务配置 |
| `/setup-pm` | 设置包管理器时 | npm/yarn/pnpm 选择 |
| `/harness-audit` | 运行仓库审计时 | 安全审计、评分卡 |
| `/verify` | 验证时 | 遗留兼容层 |

### 1.10 PRP 相关类（产品需求流程）

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/prp-plan` | 创建功能实施计划时 | 完整规划流程 |
| `/prp-implement` | 执行实施计划时 | 计划执行与验证 |
| `/prp-pr` | 创建 GitHub PR 时 | 从分支创建 PR |
| `/prp-commit` | 提交更改时 | 自然语言提交 |
| `/prp-prd` | 生成 PRD 时 | 产品需求文档 |

### 1.11 GAN 相关类

| 命令 | 触发条件 | 使用场景 |
|------|----------|----------|
| `/gan-build` | GAN 构建命令时 | 生成式开发流程 |
| `/gan-design` | GAN 设计命令时 | 设计阶段 |

---

## 2. 规范（Rules）自动应用机制

### 2.1 规则目录结构

```
rules/
├── common/          # 通用规则（所有语言）
├── csharp/          # C# 特定规则
├── python/          # Python 特定规则
└── web/             # Web 前端特定规则
```

### 2.2 通用规则（common/）

| 规则文件 | 自动触发条件 |
|----------|-------------|
| `coding-style.md` | 任何代码编写时（不可变性、KISS、DRY） |
| `code-review.md` | 使用 `/code-review` 时 |
| `hooks.md` | 配置 hooks 时 |
| `patterns.md` | 需要设计模式建议时 |
| `performance.md` | 性能相关代码时 |
| `security.md` | 安全敏感操作时 |
| `testing.md` | 编写测试时 |
| `agents.md` | Agent 编排时 |
| `development-workflow.md` | 开发流程相关时 |
| `git-workflow.md` | Git 操作时 |

### 2.3 语言特定规则

#### Python 规则（rules/python/）

| 规则 | 触发条件 |
|------|----------|
| `coding-style.md` | Python 文件编辑时 |
| `hooks.md` | Python 项目 hooks 配置 |
| `patterns.md` | Python 设计模式建议 |
| `security.md` | Python 安全相关 |
| `testing.md` | Python 测试编写 |

#### C# 规则（rules/csharp/）

| 规则 | 触发条件 |
|------|----------|
| `coding-style.md` | C# 文件编辑时 |
| `hooks.md` | C# 项目 hooks 配置 |
| `patterns.md` | C# 设计模式 |
| `security.md` | C# 安全相关 |
| `testing.md` | C# 测试编写 |

#### Web 规则（rules/web/）

| 规则 | 触发条件 |
|------|----------|
| `coding-style.md` | HTML/CSS/JS/TS 文件编辑时 |
| `hooks.md` | Web 项目 hooks 配置 |
| `patterns.md` | Web 设计模式 |
| `security.md` | Web 安全（XSS、CORS 等） |
| `testing.md` | Web 测试（Vitest、Jest） |
| `performance.md` | Web 性能优化 |
| `design-quality.md` | UI/UX 设计质量 |

### 2.4 规则如何生效

1. **CLAUDE.md 引用**：项目根目录的 `CLAUDE.md` 指定加载哪些规则
2. **自动加载**：当 Claude Code 启动时，自动加载对应语言的规则
3. **上下文感知**：根据当前工作的文件类型自动应用相关规则

---

## 3. 技能（Skills）激活条件

### 3.1 技能目录

```
skills/
├── python-patterns/SKILL.md    # Python 模式
├── python-testing/SKILL.md     # Python 测试
└── seo/SKILL.md                # SEO 技能
```

### 3.2 技能激活条件

| 技能 | 激活条件 |
|------|----------|
| `python-patterns` | Python 代码编写、重构、模式建议时 |
| `python-testing` | - 编写 Python 测试时<br>- 使用 TDD 方法时<br>- 设置测试基础设施时<br>- 审查测试覆盖率时 |
| `seo` | 前端项目需要 SEO 优化时 |

### 3.3 Python Testing 技能详解

激活时机会：
- **TDD 流程**：`/tdd` 或明确的测试先行需求
- **测试覆盖**：`/test-coverage` 分析后
- **测试审查**：审查现有测试套件时
- **Fixture 问题**：遇到测试设置/拆解问题时
- **Mocking 需求**：需要模拟外部依赖时

### 3.4 自定义技能创建

使用 `/skill-create` 从项目 git 历史生成自定义技能：
- 分析提交模式和编码约定
- 生成针对项目的特定技能
- 自动识别项目特有的工作流程

---

## 4. 命令组合使用场景

### 4.1 新功能开发流程

```
/plan                    # 1. 规划功能
  → /tdd                 # 2. TDD 开发
    → /build-fix         # 3. 修复构建错误（如有）
      → /test-coverage   # 4. 检查覆盖率
        → /code-review   # 5. 代码审查
          → /prp-pr      # 6. 创建 PR
```

### 4.2 PR 审查流程

```
/review-pr [PR编号]      # 完整 PR 审查
  → /code-review         # 本地代码审查
    → /simplify          # 代码简化（如需要）
```

### 4.3 死代码清理流程

```
/refactor-clean          # 识别并清理死代码
  → /test-coverage       # 验证清理后覆盖率
    → /update-docs       # 更新文档
```

### 4.4 Hook 创建流程

```
/hookify [问题描述]       # 创建 hook 规则
  → /hookify-list        # 确认规则已创建
    → /hookify-configure # 调整规则（如需要）
```

### 4.5 学习与进化流程

```
/learn                   # 从当前会话提取模式
  → /learn-eval          # 评估模式
    → /instinct-status   # 查看 instincts
      → /evolve          # 进化为技能
        → /skill-create  # 生成技能文件
```

### 4.6 多模型协作流程

```
/multi-plan              # 多模型规划
  → /multi-execute       # 并行执行
    → /multi-backend     # 后端主导开发
      → /multi-frontend  # 前端主导开发
        → /build-fix     # 修复构建问题
```

### 4.7 项目初始化流程

```
/projects                # 列出项目
  → /skill-create        # 创建项目技能
    → /update-codemaps   # 生成架构文档
      → /setup-pm        # 设置包管理器
        → /pm2           # 配置 PM2（如需要）
```

---

## 5. 快速参考表

### 5.1 按任务类型快速查找

| 任务 | 推荐命令 |
|------|----------|
| **规划** | `/plan`, `/prp-plan`, `/multi-plan` |
| **构建修复** | `/build-fix`, `/go-build`, `/rust-build`, `/gradle-build` |
| **代码审查** | `/code-review`, `/review-pr` |
| **测试** | `/tdd`, `/test-coverage`, `/*-test` (go/rust/cpp/kotlin/flutter) |
| **死代码清理** | `/refactor-clean` |
| **覆盖率提升** | `/test-coverage` |
| **Hook 管理** | `/hookify`, `/hookify-list`, `/hookify-configure` |
| **学习进化** | `/learn`, `/instinct-status`, `/skill-create` |
| **文档更新** | `/update-codemaps`, `/update-docs` |
| **会话管理** | `/save-session`, `/resume-session`, `/sessions` |
| **循环模式** | `/loop-start`, `/loop-status` |
| **质量检查** | `/quality-gate`, `/harness-audit` |

### 5.2 按编程语言快速查找

| 语言 | 构建 | 审查 | 测试 |
|------|------|------|------|
| Go | `/go-build` | `/go-review` | `/go-test` |
| Rust | `/rust-build` | `/rust-review` | `/rust-test` |
| Python | — | `/python-review` | *(技能)* |
| C++ | `/cpp-build` | `/cpp-review` | `/cpp-test` |
| Kotlin | `/kotlin-build` | `/kotlin-review` | `/kotlin-test` |
| Flutter | `/flutter-build` | `/flutter-review` | `/flutter-test` |
| Web/通用 | `/build-fix` | `/code-review` | `/test-coverage` |

### 5.3 触发规则自动检查

| 触发源 | 自动应用的规则 |
|--------|----------------|
| 文件类型 `.py` | `rules/python/*` |
| 文件类型 `.cs` | `rules/csharp/*` |
| 文件类型 `.ts/.js/.jsx/.tsx` | `rules/web/*` |
| 任何文件 | `rules/common/coding-style.md` |
| Git 操作 | `rules/common/git-workflow.md` |
| 测试文件 | `rules/common/testing.md` |
| 安全敏感代码 | `rules/common/security.md` |

---

## 6. Agent 智能体详解

### 6.1 Agent 目录

```
agents/
├── architect.md           # 架构专家
├── planner.md             # 规划专家
├── tdd-guide.md           # TDD 指南
├── python-reviewer.md     # Python 审查
├── csharp-reviewer.md     # C# 审查
├── rust-reviewer.md       # Rust 审查
├── security-reviewer.md   # 安全审查
├── code-reviewer.md       # 通用代码审查
├── build-error-resolver.md # 构建错误解决
├── pr-test-analyzer.md    # PR 测试分析
├── doc-updater.md         # 文档更新
├── e2e-runner.md          # E2E 测试运行
├── refactor-cleaner.md    # 重构清理
└── chief-of-staff.md      # 首席运营官
```

### 6.2 Agent 触发条件

| Agent | 触发条件 | 使用场景 |
|-------|----------|----------|
| `architect` | 系统设计、架构决策时 | 复杂系统设计、技术选型 |
| `planner` | `/plan` 命令调用时 | 实施规划、需求分解 |
| `tdd-guide` | `/tdd` 或 TDD 开发时 | 测试驱动开发指导 |
| `python-reviewer` | Python 代码审查时 | FastAPI/Django/Flask |
| `csharp-reviewer` | C# 代码审查时 | .NET/C# 项目 |
| `rust-reviewer` | Rust 代码审查时 | 所有权、安全、生命周期 |
| `security-reviewer` | 安全敏感代码、安全审计时 | OWASP Top 10、漏洞检测 |
| `code-reviewer` | 通用代码审查 | `/code-review` 命令 |
| `build-error-resolver` | 构建失败时 | 编译错误解决 |
| `pr-test-analyzer` | PR 审查时 | 测试分析、覆盖率 |
| `doc-updater` | 文档/代码地图更新时 | `/update-codemaps`、`/update-docs` |
| `e2e-runner` | E2E 测试执行时 | 端到端测试 |
| `refactor-cleaner` | 死代码清理时 | `/refactor-clean` |
| `chief-of-staff` | 项目管理、编排时 | 复杂工作流协调 |

### 6.3 Agent 与命令的关系

| 命令 | 调用的 Agent |
|------|-------------|
| `/plan` | `planner` |
| `/review-pr` | `code-reviewer`, `pr-test-analyzer`, `security-reviewer` 等多个 |
| `/code-review` | `code-reviewer` + 语言特定审查者 |
| `/refactor-clean` | `refactor-cleaner` |
| `/update-codemaps` | `doc-updater` |
| `/update-docs` | `doc-updater` |
| `/build-fix` | `build-error-resolver` |
| `/tdd` | `tdd-guide` |

---

## 附录：文件位置

- **Commands**: `commands/*.md`
- **Rules**: `rules/**/*.md`
- **Skills**: `skills/*/SKILL.md`
- **Agents**: `agents/*.md`

---

_最后更新：2026/04/17_

---

## 更新日志

### 2026/04/17
- 补充 agents/ 目录 5 个未记录的 Agent（doc-updater、e2e-runner、refactor-cleaner、rust-reviewer、security-reviewer）
- 添加 Agent 触发条件和与命令的对应关系

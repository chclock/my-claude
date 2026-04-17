---
description: Review Flutter/Dart code for idiomatic patterns, widget best practices, state management, performance, accessibility, and security. Invokes the flutter-reviewer agent.
---

# Flutter 代码审查

此命令调用 **flutter-reviewer** agent 来审查 Flutter/Dart 代码变更。

## 此命令的作用

1. **收集上下文**：审查 `git diff --staged` 和 `git diff`
2. **检查项目**：查看 `pubspec.yaml`、`analysis_options.yaml`、状态管理方案
3. **安全预扫描**：检查硬编码密钥和关键安全问题
4. **全面审查**：应用完整的审查清单
5. **报告发现**：按严重程度分组输出问题及修复指导

## 前置条件

运行 `/flutter-review` 之前，请确保：
1. **构建通过** — 先运行 `/flutter-build`；在损坏的代码上进行审查是不完整的
2. **测试通过** — 运行 `/flutter-test` 确认无回归
3. **无合并冲突** — 解决所有冲突，使 diff 仅反映有意图的变更
4. **`flutter analyze` 干净** — 修复分析器警告后再进行审查

## 使用场景

在以下情况下使用 `/flutter-review`：
- 提交带有 Flutter/Dart 变更的 PR 之前（构建和测试通过后）
- 实现新功能后尽早发现问题
- 审查他人的 Flutter 代码
- 审核 widget、状态管理组件或服务类
- 生产环境发布前

## 审查领域

| 领域 | 严重程度 |
|------|----------|
| 硬编码密钥、明文 HTTP | 严重 |
| 架构违规、状态管理反模式 | 严重 |
| Widget 重建问题、资源泄漏 | 高 |
| 缺少 `dispose()`、`BuildContext` 在 await 后使用 | 高 |
| Dart 空安全、缺少错误/加载状态 | 高 |
| Const 传播、widget 组合 | 高 |
| 性能：`build()` 中的耗时操作 | 高 |
| 无障碍、语义标签 | 中 |
| 状态转换缺少测试 | 高 |
| 硬编码字符串（l10n） | 中 |
| Pub 依赖卫生 | 低 |

## 示例会话

````text
用户：/flutter-review

Agent：
# Flutter 代码审查

## 上下文

变更文件：
- lib/features/auth/presentation/login_page.dart
- lib/features/auth/data/auth_repository_impl.dart

状态管理：Riverpod（从 pubspec.yaml 检测到）
架构：feature-first

## 安全预扫描

✓ 未检测到硬编码密钥
✓ 无明文 HTTP 调用

## 审查发现

[高] 在异步间隙后使用 BuildContext 且未检查 mounted
文件：lib/features/auth/presentation/login_page.dart:67
问题：`await auth.login(...)` 后调用 `context.go('/home')`，但没有 `mounted` 检查。
修复：在 await 后的任何导航前添加 `if (!context.mounted) return;`（Flutter 3.7+）。

[高] AsyncValue 错误状态未处理
文件：lib/features/auth/presentation/login_page.dart:42
问题：`ref.watch(authProvider)` 在 loading/data 之间切换，但没有 `error` 分支。
修复：在 switch 表达式或 `when()` 调用中添加错误处理，以显示面向用户的错误消息。

[中] 硬编码字符串未本地化
文件：lib/features/auth/presentation/login_page.dart:89
问题：`Text('Login')` — 用户可见字符串未使用本地化系统。
修复：使用项目的 l10n 访问器：`Text(context.l10n.loginButton)`。

## 审查摘要

| 严重程度 | 数量 | 状态 |
|----------|-------|--------|
| 严重 | 0     | 通过   |
| 高     | 2     | 阻止   |
| 中     | 1     | 提示   |
| 低     | 0     | 备注   |

判定：阻止 — 高优先级问题必须在合并前修复。
````

## 审批标准

- **批准**：无严重或高优先级问题
- **阻止**：任何严重或高优先级问题必须在合并前修复

## 相关命令

- `/flutter-build` — 先修复构建错误
- `/flutter-test` — 审查前运行测试
- `/code-review` — 通用代码审查（语言无关）

## 相关资源

- Agent：`agents/flutter-reviewer.md`
- Skill：`skills/flutter-dart-code-review/`
- 规则：`rules/dart/`

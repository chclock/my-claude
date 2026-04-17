---
description: Comprehensive Rust code review for ownership, lifetimes, error handling, unsafe usage, and idiomatic patterns. Invokes the rust-reviewer agent.
---

# Rust 代码审查

此命令调用 **rust-reviewer** agent 进行全面的 Rust 特定代码审查。

## 此命令的作用

1. **验证自动检查**：运行 `cargo check`、`cargo clippy -- -D warnings`、`cargo fmt --check` 和 `cargo test` — 如有任何失败则停止
2. **识别 Rust 变更**：通过 `git diff HEAD~1`（或 PR 的 `git diff main...HEAD`）查找修改的 `.rs` 文件
3. **运行安全审计**：如可用则执行 `cargo audit`
4. **安全扫描**：检查不安全使用、命令注入、硬编码密钥
5. **所有权审查**：分析不必要的克隆、生命周期问题、借用模式
6. **生成报告**：按严重程度对问题进行分类

## 使用场景

在以下情况下使用 `/rust-review`：
- 编写或修改 Rust 代码之后
- 提交 Rust 变更之前
- 审查包含 Rust 代码的 pull requests
- 加入新的 Rust 代码库时
- 学习惯用 Rust 模式时

## 审查分类

### 严重（必须修复）
- 生产代码路径中未检查的 `unwrap()`/`expect()`
- `unsafe` 没有 `// SAFETY:` 注释记录不变量
- 通过查询中的字符串插值进行的 SQL 注入
- 通过 `std::process::Command` 中未验证输入进行的命令注入
- 硬编码凭证
- 通过原始指针的使用后释放

### 高（应该修复）
- 不必要的 `.clone()` 以满足借用检查器
- `String` 参数在 `&str` 或 `impl AsRef<str>` 足够的情况下
- 异步上下文中的阻塞（`std::thread::sleep`、`std::fs`）
- 共享类型上缺少 `Send`/`Sync` 边界
- 在业务关键枚举上的通配符 `_ =>` 匹配
- 大型函数（>50 行）

### 中（考虑修复）
- 热路径中的不必要分配
- 在已知大小时缺少 `with_capacity`
- 无正当理由地抑制 clippy 警告
- 公共 API 没有 `///` 文档
- 考虑在非 `must_use` 返回类型上使用 `#[must_use]`，当忽略值可能是 bug 时

## 自动运行的检查

```bash
# 构建门（审查前必须通过）
cargo check

# Lints 和建议
cargo clippy -- -D warnings

# 格式化
cargo fmt --check

# 测试
cargo test

# 安全审计（如有）
if command -v cargo-audit >/dev/null; then cargo audit; else echo "cargo-audit not installed"; fi
```

## 使用示例

````text
用户: /rust-review

Agent:
# Rust 代码审查报告

## 审查的文件
- src/service/user.rs (已修改)
- src/handler/api.rs (已修改)

## 静态分析结果
- 构建: 成功
- Clippy: 无警告
- 格式化: 通过
- 测试: 全部通过

## 发现的问题

[严重] 生产路径中未检查的 unwrap
文件: src/service/user.rs:28
问题: 在数据库查询结果上使用 `.unwrap()`
```rust
let user = db.find_by_id(id).unwrap();  // 缺失用户时 panic
```
修复: 用上下文传播错误
```rust
let user = db.find_by_id(id)
    .context("failed to fetch user")?;
```

[高] 不必要的克隆
文件: src/handler/api.rs:45
问题: 克隆 String 以满足借用检查器
```rust
let name = user.name.clone();
process(&user, &name);
```
修复: 重构以避免克隆
```rust
let result = process_name(&user.name);
use_user(&user, result);
```

## 总结
- 严重: 1
- 高: 1
- 中: 0

建议: 阻止合并，直至严重问题修复
````

## 审批标准

| 状态 | 条件 |
|--------|-----------|
| 批准 | 无严重或高级问题 |
| 警告 | 只有中级问题（谨慎合并）|
| 阻止 | 发现严重或高级问题 |

## 与其他命令的集成

- 先使用 `/rust-test` 确保测试通过
- 如果出现构建错误，使用 `/rust-build`
- 提交前使用 `/rust-review`
- 使用 `/code-review` 处理非 Rust 特定的问题

## 相关

- Agent: `agents/rust-reviewer.md`
- 技能: `skills/rust-patterns/`、`skills/rust-testing/`

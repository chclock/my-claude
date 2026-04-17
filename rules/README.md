# 规则
## 结构

规则分为**通用**层和**语言特定**目录：

```
rules/
├── common/          # 语言无关的原则（始终安装）
│   ├── coding-style.md
│   ├── git-workflow.md
│   ├── testing.md
│   ├── performance.md
│   ├── patterns.md
│   ├── hooks.md
│   ├── agents.md
│   └── security.md
├── typescript/      # TypeScript/JavaScript 特定
├── python/          # Python 特定
├── golang/          # Go 特定
├── web/             # Web 和前端特定
├── swift/           # Swift 特定
└── php/             # PHP 特定
```

- **common/** 包含通用原则——没有语言特定的代码示例。
- **语言目录**通过框架特定的模式、工具和代码示例扩展通用规则。每个文件都引用其对应的通用规则。

## 安装

### 方式一：安装脚本（推荐）

```bash
# 安装通用规则 + 一个或多个语言特定的规则集
./install.sh typescript
./install.sh python
./install.sh golang
./install.sh web
./install.sh swift
./install.sh php

# 一次安装多种语言
./install.sh typescript python
```

### 方式二：手动安装

> **重要：** 复制整个目录——不要使用 `/*` 扁平化。
> 通用和语言特定的目录包含相同名称的文件。
> 将它们扁平化到一个目录会导致语言特定文件覆盖通用规则，
> 并破坏语言特定文件使用的相对 `../common/` 引用。

```bash
# 安装通用规则（所有项目都需要）
cp -r rules/common ~/.claude/rules/common

# 根据项目的技术栈安装语言特定的规则
cp -r rules/typescript ~/.claude/rules/typescript
cp -r rules/python ~/.claude/rules/python
cp -r rules/golang ~/.claude/rules/golang
cp -r rules/web ~/.claude/rules/web
cp -r rules/swift ~/.claude/rules/swift
cp -r rules/php ~/.claude/rules/php

# 注意！！请根据实际项目需求进行配置；此处的配置仅供参考。
```

## 规则 vs 技能

- **规则**定义适用于广泛的标准、约定和检查清单（例如，"80% 测试覆盖率"、"禁止硬编码密钥"）。
- **技能**（`skills/` 目录）针对特定任务提供深入、可操作的参考材料（例如 `python-patterns`、`golang-testing`）。

语言特定的规则文件在适当的地方引用相关技能。规则告诉你*做什么*；技能告诉你*怎么做*。

## 添加新语言

要添加对新语言的支持（例如 `rust/`）：

1. 创建 `rules/rust/` 目录
2. 添加扩展通用规则的文件：
   - `coding-style.md` — 格式化工具、习惯用法、错误处理模式
   - `testing.md` — 测试框架、覆盖率工具、测试组织
   - `patterns.md` — 语言特定的设计模式
   - `hooks.md` — 用于格式化程序、linters、类型检查器的 PostToolUse hooks
   - `security.md` — 密钥管理、安全扫描工具
3. 每个文件应以以下内容开头：
   ```
   > This file extends [common/xxx.md](../common/xxx.md) with <Language> specific content.
   ```
4. 引用现有技能（如果有），或在 `skills/` 下创建新技能。

对于非语言领域（如 `web/`），当有足够的可重用的领域特定指导来证明独立规则集的合理性时，请遵循相同的分层模式。

## 规则优先级

当语言特定规则与通用规则冲突时，**语言特定规则优先**（具体覆盖一般）。这遵循标准的分层配置模式（类似于 CSS 特异性或 `.gitignore` 优先级）。

- `rules/common/` 定义适用于所有项目的通用默认值。
- `rules/golang/`、`rules/python/`、`rules/swift/`、`rules/php/`、`rules/typescript/` 等在语言习惯不同的地方覆盖这些默认值。

### 示例

`common/coding-style.md` 建议将不可变性作为默认原则。特定语言的 `golang/coding-style.md` 可以覆盖这一点：

> 惯用的 Go 使用指针接收器进行结构体变更——参见 [common/coding-style.md](../common/coding-style.md) 了解一般原则，但在 Go 中惯用的变更是首选。

### 带有覆盖说明的通用规则

`rules/common/` 中可能被语言特定文件覆盖的规则标有：

> **语言注意**：此规则可能被语言特定规则覆盖，适用于这种模式不是惯用的语言。

# 示例项目 CLAUDE.md

这是一个示例项目级 CLAUDE.md 文件。将其放置在项目根目录。

## 项目概述

【项目简介 - 做什么的，技术栈】

## 关键规则

### 1. 代码组织

- 多个小文件优于少个大文件
- 高内聚，低耦合
- 每个文件通常 200-400 行，最多 800 行
- 按功能/领域组织，而非按类型

### 2. 代码风格

- 代码、注释或文档中不使用表情符号
- 始终保持不可变性 - 不要修改对象或数组
- 生产代码中不使用 console.log
- 使用 try/catch 进行正确的错误处理
- 使用 Zod 或类似工具进行输入验证

### 3. 测试

- TDD：先写测试
- 至少 80% 覆盖率
- 工具类写单元测试
- API 写集成测试
- 关键流程写端到端测试

### 4. 安全

- 不硬编码密钥
- 敏感数据使用环境变量
- 验证所有用户输入
- 仅使用参数化查询
- 启用 CSRF 保护

## 文件结构

```
src/
|-- app/              # Next.js 应用路由
|-- components/       # 可复用的 UI 组件
|-- hooks/            # 自定义 React hooks
|-- lib/              # 工具库
|-- types/            # TypeScript 类型定义
```

## 关键模式

### API 响应格式

```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
}
```

### 错误处理

```typescript
try {
  const result = await operation()
  return { success: true, data: result }
} catch (error) {
  console.error('操作失败：', error)
  return { success: false, error: '用户友好的消息' }
}
```

## 环境变量

```bash
# 必需
DATABASE_URL=
API_KEY=

# 可选
DEBUG=false
```

## 可用命令

- `/tdd` - 测试驱动开发工作流
- `/plan` - 创建实现计划
- `/code-review` - 审查代码质量
- `/build-fix` - 修复构建错误

## Git 工作流

- 约定式提交：`feat:`、`fix:`、`refactor:`、`docs:`、`test:`
- 不要直接提交到 main
- PR 需要审查
- 合并前所有测试必须通过

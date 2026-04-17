> 本文件通过 Web 特定模式扩展了 [common/patterns.md](../common/patterns.md)。

# Web 模式

## 组件组合

### 复合组件

当相关 UI 共享状态和交互语义时使用复合组件：

```tsx
<Tabs defaultValue="overview">
  <Tabs.List>
    <Tabs.Trigger value="overview">概览</Tabs.Trigger>
    <Tabs.Trigger value="settings">设置</Tabs.Trigger>
  </Tabs.List>
  <Tabs.Content value="overview">...</Tabs.Content>
  <Tabs.Content value="settings">...</Tabs.Content>
</Tabs>
```

- 父组件拥有状态
- 子组件通过 context 消费
- 相比 prop drilling，优先使用此模式处理复杂组件

### Render Props / Slots

- 当行为共享但标记必须变化时使用 render props 或 slot 模式
- 将键盘处理、ARIA 和焦点逻辑保持在 headless 层

### 容器/展示器分离

- 容器组件负责数据加载和副作用
- 展示组件接收 props 并渲染 UI
- 展示组件应保持纯净

## 状态管理

分别处理这些关注点：

| 关注点 | 工具 |
|---------|---------|
| 服务器状态 | TanStack Query、SWR、tRPC |
| 客户端状态 | Zustand、Jotai、signals |
| URL 状态 | search params、route segments |
| 表单状态 | React Hook Form 或同类工具 |

- 不要将服务器状态复制到客户端存储
- 使用派生值而非存储冗余计算状态

## URL 作为状态

在 URL 中持久化可共享状态：
- 过滤器
- 排序顺序
- 分页
- 活动标签
- 搜索查询

## 数据获取

### Stale-While-Revalidate

- 立即返回缓存数据
- 后台重新验证
- 优先使用现有库而非手动实现

### 乐观更新

- 快照当前状态
- 应用乐观更新
- 失败时回滚
- 回滚时发出可见错误反馈

### 并行加载

- 并行获取独立数据
- 避免父子请求瀑布
- 在合理时预取可能的下一个路由或状态

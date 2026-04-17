> 本文件通过 Web 特定的测试内容扩展了 [common/testing.md](../common/testing.md)。

# Web 测试规则

## 优先级顺序

### 1. 视觉回归

- 在关键断点截图：320、768、1024、1440
- 测试 hero 区域、滚动叙事区域和有意义的交互状态
- 对视觉密集型工作使用 Playwright 截图
- 如果同时存在两个主题，同时测试两者

### 2. 可访问性

- 运行自动化可访问性检查
- 测试键盘导航
- 验证减少动画行为
- 验证颜色对比度

### 3. 性能

- 针对有意义的页面运行 Lighthouse 或同类工具
- 保持来自 [performance.md](performance.md) 的 CWV 目标

### 4. 跨浏览器

- 最低要求：Chrome、Firefox、Safari
- 测试滚动、动画和后备行为

### 5. 响应式

- 测试 320、375、768、1024、1440、1920
- 验证无溢出
- 验证触摸交互

## E2E 形状

```ts
import { test, expect } from '@playwright/test';

test('landing hero 加载', async ({ page }) => {
  await page.goto('/');
  await expect(page.locator('h1')).toBeVisible();
});
```

- 避免基于超时的不稳定断言
- 首选确定性等待

## 单元测试

- 测试工具函数、数据转换和自定义 hooks
- 对于高度视觉化的组件，视觉回归通常比脆弱的标记断言更有价值
- 视觉回归补充覆盖率目标；不替代它们

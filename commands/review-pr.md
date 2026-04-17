---
description: 使用专业智能体进行全面的 PR 审查
---

运行全面的多视角拉取请求审查。

## 用法

`/review-pr [PR编号或URL] [--focus=comments|tests|errors|types|code|simplify]`

如果未指定 PR，则审查当前分支的 PR。如果未指定焦点，则运行完整的审查流程。

## 步骤

1. 识别 PR：
   - 使用 `gh pr view` 获取 PR 详情、变更文件和差异
2. 查找项目指南：
   - 查找 `CLAUDE.md`、lint 配置、TypeScript 配置、仓库规范
3. 运行专业审查智能体：
   - `code-reviewer`
   - `comment-analyzer`
   - `pr-test-analyzer`
   - `silent-failure-hunter`
   - `type-design-analyzer`
   - `code-simplifier`
4. 汇总结果：
   - 去重重叠的发现
   - 按严重程度排序
5. 按严重程度分组报告发现

## 置信度规则

仅报告置信度 >= 80% 的问题：

- 严重：bug、安全问题、数据丢失
- 重要：缺少测试、质量问题、样式违规
- 建议：仅在明确请求时提供建议

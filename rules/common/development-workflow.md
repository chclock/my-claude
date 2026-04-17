# 开发工作流

> 本文件通过 git 操作之前的完整功能开发流程扩展了 [common/git-workflow.md](./git-workflow.md)。

功能实施工作流描述了开发流程：研究、规划、TDD、代码审查，然后提交到 git。

## 功能实施工作流

0. **研究与复用**（任何新实现前的强制步骤）
   - **首先 GitHub 代码搜索：** 运行 `gh search repos` 和 `gh search code` 在编写任何新内容之前查找现有实现、模板和模式。
   - **其次查看库文档：** 使用 Context7 或主要供应商文档确认 API 行为、包的用法和版本特定细节后再实现。
   - **当前两者不足时使用 Exa：** 在 GitHub 搜索和主要文档之后，使用 Exa 进行更广泛的网络研究或发现。
   - **检查包注册表：** 在编写工具代码之前搜索 npm、PyPI、crates.io 等注册表。优先使用久经考验的库而非自己实现的方案。
   - **搜索可适应的实现：** 寻找解决 80% 以上问题的开源项目，可以 fork、移植或包装。
   - 当满足需求时，优先采用或移植成熟方案而非编写全新代码。

1. **先规划**
   - 使用 **planner** agent 创建实施计划
   - 编码前生成规划文档：PRD、架构、system_design、tech_doc、task_list
   - 识别依赖和风险
   - 分解为阶段

2. **TDD 方法**
   - 使用 **tdd-guide** agent
   - 先写测试（RED）
   - 实现以通过测试（GREEN）
   - 重构（IMPROVE）
   - 验证 80%+ 覆盖率

3. **代码审查**
   - 编写代码后立即使用 **code-reviewer** agent
   - 解决 CRITICAL 和 HIGH 问题
   - 尽可能修复 MEDIUM 问题

4. **提交与推送**
   - 详细的提交消息
   - 遵循 conventional commits 格式
   - 参见 [git-workflow.md](./git-workflow.md) 了解提交消息格式和 PR 流程

5. **预审查检查**
   - 验证所有自动化检查（CI/CD）通过
   - 解决任何合并冲突
   - 确保分支与目标分支同步
   - 仅在这些检查通过后请求审查

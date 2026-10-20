---
title: "练习 2 - GitHub Copilot 云智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 上一课：自定义说明][previous-lesson] |
|:--|

几乎所有组织都需要处理技术债务，例如未解决的安全问题、需要更新的旧代码，或因缺少时间而长期搁置的功能请求。GitHub Copilot 云智能体可以自主执行更新代码和添加功能等任务。任务完成后，它会创建草稿拉取请求 (PR)，供开发人员审查。这样可以将繁琐任务委派出去，加快开发流程，让开发人员专注于更重要的工作。

你将探索 Copilot 云智能体的以下用法：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 了解清晰限定议题范围的重要性。
- 将议题分配给 Copilot。

## 场景

Tailspin Toys 想要解决一些技术债务。最初受雇创建网站的承包商没有妥善编写文档，事实上，代码几乎完全缺少文档。团队首先希望为应用程序中所有导出的函数添加 TypeScript 文档注释 (TSDoc)。

此外，设计团队希望改进游戏发现体验。他们希望在每款游戏的详情页中展示相关游戏，即相同类别的其他游戏，方便用户继续浏览。他们暂时不需要完善的实现，只希望能用于用户体验 (UX) 验收测试。目前这项工作被其他优先级更高的议题阻塞。

这类任务很容易被延后，适合分配给 Copilot 云智能体。云智能体可以异步工作，让开发人员继续处理其他任务，之后再回来审查 Copilot 的工作并确认结果符合预期。

## 了解 GitHub Copilot 云智能体

[GitHub Copilot 云智能体][copilot-agents]可以像人类开发人员一样在后台执行任务。你可以通过多种方式分配工作，例如[将 GitHub 议题分配给 Copilot][assign-issue]。分配后，Copilot 会创建草稿拉取请求以跟踪进度、设置环境并开始处理任务。你可以在会话进行中或完成后查看详情。待解决方案准备好供你审查时，Copilot 会在拉取请求中通知你。

## 明确限定说明和任务范围的重要性

GitHub Copilot 并非万能，使用它仍需要思考。看似简单的操作也可能包含复杂细节，因此请留意如何为 Copilot 云智能体限定任务范围，并参阅[云智能体最佳实践][cloud-agent-best-practices]。将其视为人工智能 (AI) 结对编程伙伴，分阶段开展工作、学习、试验并逐步调整。引入生成式 AI 并不会改变软件开发的基本原则。

## 此存储库中的自定义说明

前面的练习介绍了自定义说明及其引导 Copilot 的方式。如果已经完成自定义说明练习，就已了解 `.github/copilot-instructions.md` 的作用；如果还没有，现在可以快速查看。在将工作分配给 Copilot 云智能体前，只读查看存储库中已有的说明文件，以便之后观察其效果。

在 GitHub 网页界面中打开以下文件，或在已启动的 Codespace 中查看：

- `.github/copilot-instructions.md`：查看 **Code standards** 部分，重点关注 TypeScript 约定和 TSDoc 文档注释要求。
- `.github/instructions/unit-tests.instructions.md`：注意 `applyTo` frontmatter，它将这些说明限定为 `**/*.test.ts` 文件。

下一节将把 `Code lacks documentation` 议题分配给云智能体。请留意生成的拉取请求是否包含 TSDoc 文档注释、TypeScript 约定和注释头；这些内容来自说明文件。后续练习还会再次审查这些 PR。

## 设置 Copilot 云智能体的开发环境

无论由谁编写代码，通常都需要特定环境并运行一些设置脚本，确保一切准备就绪。将任务分配给 Copilot 时也一样，因为它会以类似软件工程师的方式工作。

云智能体使用 [GitHub Actions][github-actions]作为工作环境。你可以创建[专用设置工作流][setup-workflow]来自定义环境，该工作流配置在 `.github/workflows/copilot-setup-steps.yml` 中，并在 Copilot 开始工作前运行，以提供所需的开发工具和依赖项。此设置已预先配置，用于简化练习流程。它为单体 Astro 应用准备 Node.js、项目依赖项、端到端测试所需的浏览器二进制文件，以及已迁移并填充数据的 SQLite 数据库：

```yaml
name: "Copilot Setup Steps"

# 允许你从存储库的“Actions”选项卡测试设置步骤
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # 将权限设置为这些步骤所需的最低级别。Copilot 会使用自己的令牌执行操作。
    permissions:
      # 如果设置步骤需要克隆存储库（例如安装依赖项），则需要 `contents: read` 权限。如果设置步骤不克隆存储库，Copilot 会在这些步骤完成后自动克隆。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # 前端 / 应用设置 - Node.js（整个应用现在使用 Astro + Drizzle/libSQL）
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # 迁移并填充本地 SQLite 数据库，以便构建和测试使用其中的数据。
      - name: Set up the database
        run: npm run db:setup
```

此文件与其他 GitHub 工作流文件类似，但有几个要点：

- 它包含一个名为 `copilot-setup-steps` 的作业。Copilot 开始处理拉取请求前，GitHub Actions 会先执行此作业。
- `workflow_dispatch` 触发器允许你从存储库的 **Actions** 选项卡手动运行工作流。可先确认工作流运行成功，不必等待 Copilot 启动。

## 添加文档

大多数项目要么文档过时，要么缺少文档。这样的技术债务往往得不到处理，会降低工作效率，也让代码库维护和新成员上手更加困难。Copilot 擅长生成文档，因此很适合将此议题分配给 Copilot 云智能体。它会在后台生成所需文档，你将在后续练习中审查结果。

1. 在新的浏览器选项卡中打开 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue**。
5. 将 **Title** 设为 `Code lacks documentation`。
6. 将 **Description** 设为：

   ```plaintext
   我们组织要求在适当情况下为函数和方法添加 TSDoc 文档注释。遗憾的是，近期更新没有遵循这一标准。需要更新现有代码，在文档注释有助于说明行为时添加注释。
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。
10. 选择 **Pull Requests** 选项卡。
11. 打开新生成的拉取请求 (PR)，其标题可能类似于 `[WIP]: Code lacks documentation`。如果列表中没有新 PR，请稍等片刻并刷新浏览器窗口。
12. 几分钟后，Copilot 应该会创建待办事项列表。

> [!NOTE]
> Copilot 生成待办事项列表可能需要几分钟，因为它正在创建环境（运行前面介绍的工作流）、分析项目并确定解决问题的最佳方法。

13. 审查列表和它将要完成的任务。
14. 沿拉取请求时间线向下滚动，查看 Copilot 已开始处理议题的更新。
15. 选择 **View session** 按钮。

   ![Copilot 会话视图](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 可能需要刷新窗口才能看到更新后的状态指示器。

16. 查看实时会话，了解 Copilot 如何解决问题，包括探索代码和理解当前状态、暂停思考并确定合适计划，以及编写代码。

此过程可能需要几分钟。Copilot 云智能体的主要目标之一是异步执行任务，让开发人员处理其他工作。我们将同时分配另一个任务，然后转而编写代码，为应用添加功能。

## 在游戏详情页添加相关游戏区域

GitHub Copilot 云智能体的一大优势是可以拆分工作：你处理一项任务，它处理另一项任务。为设计团队添加相关游戏区域可能不需要很长时间，但这段时间也可以用于其他工作。现在将任务分配给 Copilot 云智能体。

1. 返回 github.com 上的存储库。
2. 选择 **Issues** 选项卡。
3. 选择 **New issue** 打开新议题对话框。
4. 选择 **Blank issue** 使用空白模板。
5. 将 **Title** 设为 `Show related games on the game details page`。
6. 将 **Description** 设为：

   ```markdown
   我们希望通过在每款游戏的详情页展示相关游戏，帮助用户发现更多游戏。设计团队希望探索用户体验并进行验收测试。需求如下：

   - 在 `src/lib/` 中添加数据访问辅助函数，返回与指定游戏类别相同的其他游戏，但不包含该游戏本身
   - 在游戏详情页展示使用此辅助函数的“Related games”区域
   - 处理游戏没有相关游戏的情况
   - 为新辅助函数创建单元测试
   - 创建 PR 前确保所有测试通过
   ```

7. 选择 **Create** 创建议题。
8. 在右侧选择 **Assign to Copilot**，打开分配对话框。

   ![将 Copilot 分配给议题](../../../_images/shared-assign-copilot.png)

9. 选择 **Assign**。

随后，议题中的第一条评论会出现一组 👀，表示 Copilot 已开始处理任务。

![Copilot 使用眼睛表情符号表示正在处理议题](../../../_images/ex4-issue-eyes-emoji.png)

Copilot 正在处理第二项请求。Copilot 云智能体的工作方式类似软件工程师，因此无需持续监控，完成后再审查即可。现在转而创建并使用自定义智能体。

## 总结和后续步骤

本课探索了 [GitHub Copilot 云智能体][copilot-agents]。你可以将议题分配给云智能体，由它异步处理。Copilot 可用于处理技术债务、创建新功能，或协助将代码从一个框架迁移到另一个框架。

你探索了以下概念：

- 自定义代码生成环境。
- 确保安全地执行操作。
- 了解清晰限定议题范围的重要性。
- 将议题分配给 Copilot。

云智能体正在后台工作，现在可以转而创建和使用自定义智能体。[Copilot 云智能体也可使用模型上下文协议 (MCP) 服务器][cloud-agent-mcp]，并且可以访问自定义说明，前面的练习已介绍过这些内容。

## 资源

- [关于 Copilot 云智能体][copilot-agents]
- [将 GitHub 议题分配给 Copilot][assign-issue]
- [Copilot 云智能体设置工作流最佳实践][cloud-agent-best-practices]

| [下一课：自定义智能体 →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions

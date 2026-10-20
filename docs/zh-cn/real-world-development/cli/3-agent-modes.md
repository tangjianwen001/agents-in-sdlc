---
title: "Lesson 3 - 智能体模式：Plan 和 Autopilot"
description: "使用 Plan 确定方案，使用 Autopilot 根据议题实现筛选，并在 Interactive 模式中审查结果。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

我们先为项目添加了一项小功能，但较大的更改需要更完善的流程。GitHub Copilot CLI 可以融入组织现有的工作方式，帮助团队以正确方式构建正确功能。本课开启一系列课程，将按照常见的智能体驱动开发流程完成工作：从议题出发生成新功能，验证代码和功能行为，最后成功合并到项目中。

> [!NOTE]
> 后续功能工作将继续使用同一段对话和同一分支。通常，不同类型的文件会分别放在不同分支或拉取请求 (PR) 中；本研讨会为聚焦核心概念而简化了流程。

在本课中，你将：

- 根据 GitHub 议题开始新的 Copilot 对话。
- 在 Plan 模式中定义需求。
- 使用 Autopilot 模式实现新功能。
- 审查代码。
- 在转发的浏览器中手动验证功能。

继续完成此功能时，你还将更新存储库说明、自定义现有的 `quality-checks` 技能、添加 MCP 验证、创建质量保证 (QA) 智能体，并创建功能 PR。

## 场景

Tailspin Toys 的目录不断扩大，访客需要按类别和发布商筛选游戏。待办事项中的议题描述了该功能，但组合多个类别等细节需要在编码前达成一致。你将使用 Plan 模式确定这些决策，然后授权 Autopilot 在明确范围内实现功能。

## 背景

在开发流程中引入人工智能 (AI) 编码智能体，不会改变软件开发的基本原则，反而会让这些原则更加重要。大多数开发人员的流程大致如下：

1. 从已创建的议题开始，了解需要完成的工作。
2. 规划要构建的内容。
3. 编写并审查代码。
4. 运行测试验证代码。
5. 手动验证新功能。
6. 创建 PR。
7. 代码通过审查且持续集成 (CI) 成功后，合并代码。

> [!NOTE]
> 具体流程因团队和组织而异，但大多与上述流程类似。

遵循标准流程，可以确保 AI 生成的代码符合需求，并像手写代码一样经过验证。

## 对话模式

**对话模式**控制智能体的自主程度。按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 可在模式间切换：

- **Interactive**：你和智能体协作。智能体提出更改建议，并等待你的输入后再继续。
- **Plan**：智能体先制定计划，并且无法编辑项目文件。
- **Autopilot**：智能体自主工作，包括编写代码、运行测试并反复迭代，直到认为任务完成。

先使用 Plan 模式审查计划，然后再使用 Autopilot 实施计划。

## 从议题开始

开始筛选功能前，返回 Codespace，确认存储库和终端已准备就绪。

1. 返回 Codespace。如果它已停止，请先重新启动。
2. 确认游戏评分 PR 已合并。
3. 如果终端尚未打开，请按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）。
4. 更新 `main`，然后为筛选工作创建分支：

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. 启动 Copilot CLI：

   ```bash
   copilot --yolo
   ```

6. 按两次 <kbd>Tab</kbd> 打开 **Issues** 选项卡。
7. 按 <kbd>A</kbd> 显示所有议题。
8. 使用箭头键选中标题为 **Allow users to filter games by category and publisher** 的议题。
9. 按 <kbd>C</kbd> 将议题添加到提示中，并返回 **Session** 选项卡。

请注意，提示现在以 `#7`（或类似编号）开头。`#` 可将 GitHub 上的议题或 PR 添加到上下文中。

## 规划筛选功能

在交给 Copilot 执行前，规划功能或任务的实现方式有助于明确方向。复杂任务尤其值得花时间规划。现在切换到 Plan 模式，请 Copilot 制定计划。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 切换到 Plan 模式。确认提示下方的模式指示器显示 **Plan**。
2. 在上一步添加的议题引用之后输入：

   ```plaintext
   为实现此功能制定计划。
   ```

Copilot 会探索项目并确定合适的实现方式，以此制定计划。

3. Copilot 可能会询问筛选行为的细节。根据自己的偏好回答；这些问题没有唯一正确答案。
4. 计划完成后，按 <kbd>Control</kbd>+<kbd>E</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>E</kbd>（Windows/Linux）展开计划。
5. 滚动查看并审查计划。
6. 如果计划中的内容与你的决定不符，请让 Copilot 修改。

## 批准 Autopilot

计划编写并审查完毕后，就可以开始实现。使用 Autopilot 让 Copilot 执行计划。

Autopilot 会持续迭代，直到它认为问题已解决。

1. 选择 **Accept plan and build on autopilot (recommended)**，或选择你所安装版本中名称相近的选项。
2. 确认提示下方的模式指示器显示 **Autopilot**。
3. 观察 Copilot 如何根据既定计划迭代、生成代码并运行测试。

> [!NOTE]
> 批准后可能会立即开始实现，因此请先审查计划。如果 Copilot 报告缺少依赖项或端口冲突，请先解决设置问题，再将检查视为已完成。

## 审查并验证实现

生成的代码与其他代码一样，合并前需要审查。检查代码并运行网站，确认结果符合预期。

1. 按 <kbd>Shift</kbd>+<kbd>Tab</kbd> 切换到 Interactive 模式。确认模式指示器不再显示 **Plan** 或 **Autopilot**。
2. 输入 `/diff` 并检查筛选实现和测试。
3. 对照议题和规划时确定的决策，检查实现结果。
4. 审查完代码后，按 <kbd>Esc</kbd> 退出差异视图。
5. 查看项目检查的输出；如果有失败，请让 Copilot 修复。

## 探索新功能

代码看起来不错，但实际运行情况如何？像之前一样启动应用，并通过 Codespaces 转发的端口打开网站。

1. 请 Copilot 启动应用：

   ```plaintext
   启动应用，让我在浏览器中试用筛选功能。告诉我 URL，并让服务器继续运行。
   ```

2. 当 Codespaces 提示端口 `4321` 已可用时，选择 **Open in Browser**。
3. 尝试按类别、发布商筛选，以及组合使用在计划中达成一致的筛选条件。
4. 确认重置和无结果状态符合议题要求及你的决定。
5. 返回 Codespace，请 Copilot 停止它启动的开发服务器。

## 总结和后续步骤

你已使用不同的对话模式构建并审查功能。在本课中，你：

- 根据 GitHub 议题开始新的 Copilot 对话。
- 在 Plan 模式中定义需求。
- 使用 Autopilot 模式实现新功能。
- 审查代码。
- 在转发的浏览器中手动验证功能。

接下来通过[使用自定义说明][next-lesson]深入了解如何让代码遵循文档化的实践。

## 资源

- [GitHub Copilot CLI 中的 Autopilot][autopilot]
- [Copilot CLI 命令参考][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/

---
title: "Lesson 10 - 总结与后续步骤"
description: "回顾 Copilot CLI 工作流、两个拉取请求、可复用的自定义设置和更多资源。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

你已在 Tailspin Toys 的连续工作流中使用 GitHub Copilot CLI。你已经：

- 准备 Codespace、安装 Copilot CLI、探索项目并找到已创建的筛选议题。
- 添加游戏评分，在转发的浏览器中审查结果，并手动合并第一个拉取请求 (PR)。
- 从筛选议题开始，在 Plan 模式中确定方案，在 Autopilot 模式中实现，并在 Interactive 模式中审查。
- 使用自定义说明引导智能体，然后自定义现有的 `quality-checks` 技能，并用它运行单元测试、lint 和类型检查。
- 添加 Playwright Model Context Protocol (MCP) 服务器，并在真实浏览器中探索筛选功能。
- 创建并选择质量保证 (QA) 自定义智能体，以检查需求、覆盖范围、技能结果和浏览器证据。
- 审查完整的筛选更改，并授权 Agent Merge 处理筛选 PR。
- 探索用于管理上下文、模型、共享和可选云端委派的斜杠命令。

## 已交付的内容

本研讨会包含两个 PR 阶段，每项工作都从更新后的 `main` 创建独立分支：

1. **游戏评分：**在游戏卡片上显示现有的 `starRating` 和明确的未评分状态。
2. **筛选和质量工作流：**实现筛选、更新说明并将其应用于功能、自定义 `quality-checks` 报告、创建 QA 配置，并包含相关测试。

从规划筛选功能到创建其 PR，你始终使用同一段对话和同一分支。将相关工作合并到一个 PR 中可以简化研讨会流程。

## 不同类型的验证

你通过多种方式检查代码：运行自动化测试、亲自在浏览器中检查，以及通过 MCP 让 Copilot 在浏览器中探索。`quality-checks` 技能运行了单元测试、lint 和类型检查，并按新格式报告结果。QA 在 PR 创建前综合检查了这些结果、需求和测试覆盖范围。

新增测试应弥补实际缺口；如果 QA 检查后无需添加测试，也可能是正确结果。授权合并前，请审查代码和证据；更改后应更新受影响的验证证据。

## 最佳实践

提供给 Copilot 的上下文和工具会影响它的工作。在本研讨会中，你更新了说明、自定义了技能、创建了 QA 配置并配置了 MCP 服务器。可以在不同会话中复用这些自定义设置，并根据团队需要进行调整。说明规定标准，技能描述可重复执行的任务，自定义智能体定义专门角色，MCP 服务器连接外部工具。请审查实际更改和工具结果，而不只是查看智能体的总结。

根据任务选择合适的**模式和模型**。使用 **Plan** 在开始构建前考虑方案，使用 **Interactive** 参与聚焦的更改，使用 **Autopilot** 处理范围明确的任务。日常编辑可选择速度更快的模型，复杂工作则可选择能力更强的模型。

上下文和基础设施同样重要。清楚说明要构建的*内容*、*原因*和*方式*，会显著影响输出结果。

## 继续探索

你已完成核心工作流。还可以了解以下 CLI 功能：

- `/review`：请代码审查智能体分析更改。
- `/rubber-duck`：讨论问题并获取不同视角。
- `/fleet`：协调多个独立子任务并行执行。
- `/worktree`：隔离单独任务。
- `/delegate`：将任务发送给 Copilot 云智能体。

## 后续步骤

提升任何工具使用能力的最好方式就是持续使用。可以将它用于生产代码、个人项目，或那个想了很多年却一直没开始构建的小应用。与团队分享经验，也向团队学习。并一如既往地继续探索文档。

如果想从终端扩展 Tailspin Toys，可以继续学习可选的 [Foundry Backer Concierge 系列][foundry]。若想比较其他环境，请探索 [VS Code 研讨会][vscode]、[GitHub Copilot app 研讨会][app]或 [Copilot 云智能体研讨会][cloud]。

## 资源

- [关于 GitHub Copilot CLI][about-cli]
- [Copilot CLI 命令参考][cli-reference]
- [自定义 Copilot CLI][customize-cli]
- [使用 Copilot CLI 管理拉取请求][manage-prs]

[previous-lesson]: ../9-cli-power-tools/
[foundry]: ../8-foundry-agent/
[vscode]: ../../vscode/
[app]: ../../app/
[cloud]: ../../cloud/
[about-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[customize-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot
[manage-prs]: https://docs.github.com/copilot/how-tos/copilot-cli/use-copilot-cli/manage-pull-requests

---
title: "Lesson 7 - 创建并使用质量保证 (QA) 智能体"
description: "创建 QA 自定义智能体，将议题需求、quality-checks 技能和 Playwright MCP 整合起来。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

你已使用 `quality-checks` 技能运行自动化检查，并通过 Playwright 模型上下文协议 (MCP) 在浏览器中观察筛选体验。现在将这些能力整合到一个自定义智能体中，并为其定义清晰的 QA 流程。

在本课中，你将：

- 了解自定义智能体如何使用说明、技能和 MCP 工具。
- 创建并检查可复用的质量保证配置。
- 选择 QA 智能体，并根据筛选议题审查其发现。

## 场景

Tailspin Toys 希望在创建拉取请求 (PR) 前，统一检查需求、代码质量、自动化检查、测试覆盖范围和浏览器行为。自定义智能体可以协调 QA 流程并生成可复用报告。

## 什么是自定义智能体？

自定义智能体是通过 Markdown 配置文件定义的 Copilot 专用版本。配置文件描述智能体的用途、说明和可用工具。在本研讨会中，你将在 `.github/agents/qa.agent.md` 中定义 QA 角色，并在 Copilot CLI 中选择它。

已使用的自定义功能各有用途：存储库说明描述团队标准；`quality-checks` 技能封装可重复执行的检查；Playwright MCP 提供浏览器工具；QA 配置则告诉 Copilot 如何使用这些能力评估需求并报告发现。QA 配置不会取代其他功能，也不需要开启另一段对话。

## 创建 QA 配置

创建功能 PR 前，请 Copilot 创建可复用的 QA 配置。配置将说明 QA 要执行的检查及其必须遵循的边界。

1. 返回 Codespace，确认筛选对话处于 Interactive 模式。
2. 请默认智能体创建新的自定义智能体：

   ```plaintext
   在 .github/agents/qa.agent.md 中创建名为 QA 的自定义智能体。它应根据议题和达成一致的需求检查功能，遵循存储库说明，运行 quality-checks 技能，使用 Playwright MCP 验证行为，并在测试覆盖不足时添加测试。

   要求它使用通过、失败或受阻来报告每项需求，并提供支持证据。更改实现代码前必须先询问；不得提交更改或创建拉取请求。

   现在只创建配置文件，供我审查。
   ```

## 检查配置

使用新智能体前，请检查其配置，确认 Copilot 准确记录了预期 QA 流程和边界。这样可避免只想验证功能时，智能体配置不完整或范围过宽而意外修改功能。

1. 输入 `/diff` 并打开 `.github/agents/qa.agent.md`。
2. 阅读 frontmatter。`description` 是必需项；`name` 是可选项，但添加后可以作为清晰的显示名称。
3. 阅读配置说明，确认 QA 会从需求开始、遵循存储库说明、运行 `quality-checks` 技能并使用 Playwright MCP。
4. 确认 QA 会提供支持证据、修改实现代码前会先询问，并且不会提交更改或创建 PR。
5. 如果配置缺少任何职责或边界，请让默认智能体先修改，再继续。

## 根据议题运行 QA

Copilot CLI 会在对话开始时加载项目智能体。创建配置后，恢复同一段筛选对话并选择 QA，使其能够使用已有的议题和规划决策。

1. 使用以下提示启用智能体：

   ```plaintext
   /agent QA
   ```

> [!NOTE]
> 刚创建的智能体可能尚未显示在可用列表中，但仍可通过上述命令启用。

2. 使用以下提示，请 QA 智能体审查功能：

   ```plaintext
   根据议题和计划中达成一致的决策审查筛选功能。它是否已准备好创建 PR？
   ```

3. 等待 QA 智能体完成工作。
4. 阅读它生成的报告。

## 总结和后续步骤

你已向工作流添加可复用的专用角色，并审查了它的工作。在本课中，你：

- 了解了自定义智能体如何使用说明、技能和 MCP 工具。
- 创建并检查了可复用的质量保证配置。
- 选择 QA 智能体，并根据筛选议题审查其发现。

现在实现、技能更新、QA 配置、测试和验证报告均已准备就绪。接下来将它们[整合到功能 PR 中，并使用 Agent Merge][next-lesson]。

## 资源

- [为 Copilot CLI 创建自定义智能体][create-agents]
- [自定义智能体配置][agent-config]

[previous-lesson]: ../6-mcp-playwright/
[next-lesson]: ../8-create-pull-request/
[create-agents]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli
[agent-config]: https://docs.github.com/copilot/reference/custom-agents-configuration

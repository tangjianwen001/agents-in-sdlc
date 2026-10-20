---
title: "Lesson 6 - 使用 Playwright 模型上下文协议 (MCP) 验证功能"
description: "在 Copilot CLI 中检查或配置 Playwright MCP，并在浏览器中探索筛选功能。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

正如前面所述，编写代码不只是实现功能。还需要处理数据、外部服务，并让 Copilot 使用其他自动化工具。这正是 MCP 服务器发挥作用的地方。MCP 服务器可以扩展 Copilot CLI 内置的能力，提供更多工具和服务。

在本课中，你将：

- 了解 MCP 及 Copilot CLI 如何使用它。
- 如果尚未配置 Playwright MCP 服务器，则添加该服务器。
- 请智能体驱动浏览器并探索筛选功能。

## 场景

单元测试和端到端测试都很重要，但要验证用户界面 (UI) 更新，必须实际与界面交互。让 Copilot 像用户一样使用网站，可以进一步自动化验证过程，并提升对更改符合预期的信心。

## 什么是 MCP？

[MCP][mcp-blog-post] 为人工智能 (AI) 智能体与外部工具和服务的通信提供了一种方式。AI 智能体可通过 MCP 实时访问最新信息，并代表你执行操作。

这些工具和资源通过 MCP 服务器访问。MCP 服务器充当智能体与外部工具和服务之间的桥梁，每个服务器都提供一组智能体可访问的工具和资源。

一些常用的 MCP 服务器包括：

- **[GitHub MCP Server][github-mcp]**：提供用于管理 GitHub 存储库、议题和拉取请求的应用程序编程接口 (API)。
- **[Playwright MCP Server][playwright-mcp-server]**：使用 Playwright 提供浏览器自动化功能。

还有许多其他 MCP 服务器。GitHub 提供 [MCP 注册表][mcp-registry]，帮助开发者发现生态系统中的服务器并参与贡献。

> [!CAUTION]
> 请像对待项目中的其他依赖项一样对待 MCP 服务器。使用前请审查其源代码、验证发布者，并考虑安全影响。

## 添加 Playwright MCP 服务器

如果尚未配置 Playwright MCP 服务器，请添加它，以便 Copilot 像用户一样与网站交互。

> [!NOTE]
> 如果 Copilot CLI 已配置 Playwright MCP，请跳过这些设置步骤并继续下一节。

1. 返回 Codespace。
2. 在 Copilot CLI 中输入以下命令，打开添加 MCP 服务器的对话框：

   ```plaintext
   /mcp add
   ```

3. 在名称处输入 `playwright`，然后按 <kbd>Tab</kbd>。
4. 按 <kbd>Enter</kbd> 确认服务器类型为 **STDIO**，然后按 <kbd>Tab</kbd>。
5. 在 **Command** 对话框中粘贴：

   ```plaintext
   npx -y @playwright/mcp@latest --headless --no-sandbox
   ```

6. 按 <kbd>Control</kbd>+<kbd>S</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>S</kbd>（Windows/Linux）保存新的 MCP 服务器。
7. 按 <kbd>Esc</kbd> 退出 MCP 对话框。

## 让 Copilot 使用 Playwright 探索功能

之前你已手动确认功能行为符合预期。现在使用 Copilot 中的 Playwright MCP 服务器进行同样的检查。

1. 使用以下提示，要求 Copilot 使用 Playwright MCP 服务器验证功能：

   ```plaintext
   启动应用，并使用 Playwright MCP 对照议题和计划检查筛选功能。不要进行更改，告诉我哪些行为正常、哪些不正常。完成后停止你启动的服务器。
   ```

> [!NOTE]
> 通常无需明确要求 Copilot 使用 MCP 服务器，它一般会自行判断。不过，指出应使用的工具可以让结果更一致，也能节省一些令牌。

2. 观察 Copilot 列出浏览器中的操作步骤并验证功能。
3. 阅读报告，确认各项行为符合预期。

Copilot 会启动服务器、使用 Playwright 与网站交互、停止服务器，并提供报告。

## 总结和后续步骤

你已使用 Playwright MCP 服务器，通过 Copilot CLI 在真实浏览器中探索功能。回顾一下，你：

- 了解了 MCP 及 Copilot CLI 如何使用它。
- 如果此前未配置，则添加了 Playwright MCP 服务器。
- 请智能体驱动浏览器并探索筛选功能。

接下来[创建质量保证 (QA) 自定义智能体][next-lesson]，将技能和浏览器工具整合到专用角色中。

## 资源

- [MCP 是什么，为什么受到关注？][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [向 Copilot CLI 添加 MCP 服务器][add-mcp]

[previous-lesson]: ../5-agent-skills/
[next-lesson]: ../7-qa-agent/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp
[github-mcp]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[add-mcp]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers

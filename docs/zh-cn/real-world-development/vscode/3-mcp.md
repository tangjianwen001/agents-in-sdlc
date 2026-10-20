---
title: "练习 3 - 使用 Playwright 模型上下文协议 (MCP) 服务器测试功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：智能体模式][previous-lesson] · [下一课：自定义智能体 →][next-lesson]

你刚刚使用智能体模式构建了筛选功能。创建拉取请求 (PR) 前，需要在浏览器中确认功能确实可用。无需手动操作应用，你可以连接 **Playwright MCP 服务器**，让 Copilot 驱动真实浏览器测试功能；随后发布分支并创建拉取请求。

在本练习中，你将：

- 了解模型上下文协议 (MCP) 及 MCP 服务器如何为 Copilot 扩展工具。
- 在工作区中添加 Playwright MCP 服务器。
- 请 Copilot 使用它在浏览器中手动测试筛选功能。
- 发布分支并为筛选工作创建拉取请求。

## 什么是模型上下文协议 (MCP)？

智能体模式连接编辑器之外的资源后会更强大。模型上下文协议 (MCP) 是 Copilot 实现此功能的方式，它是智能体与外部工具和服务通信的标准。

![智能体模式内部工作原理示意图，展示它如何与上下文、大型语言模型 (LLM) 和工具交互，包括 MCP 服务器和 VS Code 扩展提供的工具](../../../_images/ex1-mcp-diagram.png)

[MCP](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/)为人工智能 (AI) 智能体与外部工具和服务的通信提供了一种方式。AI 智能体可通过 MCP 实时获取最新信息（使用资源）并代表你执行操作（使用工具）。

这些工具和资源通过 MCP 服务器访问。MCP 服务器充当 AI 智能体与外部工具和服务之间的桥梁，负责管理通信。外部工具可以是现有应用程序编程接口 (API)，也可以是 npm 包等本地工具。每个 MCP 服务器都提供一组智能体可访问的工具和资源。

一些常用的 MCP 服务器包括：

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**：提供用于管理 GitHub 存储库的 API，允许 AI 智能体创建和更新存储库，以及管理议题和拉取请求。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**：使用 Playwright 提供浏览器自动化功能，允许 AI 智能体访问网页、填写表单和选择按钮。

还有许多其他 MCP 服务器提供不同的工具和资源。GitHub 提供 [MCP 注册表](https://github.com/mcp)，帮助开发者发现生态系统中的服务器并参与贡献。

> [!CAUTION]
> 请像对待项目中的其他依赖项一样对待 MCP 服务器。使用前请仔细审查其源代码、验证发布者并考虑安全影响。只使用可信任的服务器，并谨慎授予它访问敏感资源或执行敏感操作的权限。

## 查看 MCP 配置

`.vscode/mcp.json` 文件用于配置此 VS Code 工作区可用的 MCP 服务器。

1. 在 Codespace 中打开 `.vscode/mcp.json`。
2. 应能看到已配置的 `github` 服务器：

   ```json
   {
     "servers": {
       "github": {
         "type": "http",
         "url": "https://api.githubcopilot.com/mcp/"
       }
     }
   }
   ```

`github` 条目来自项目模板，因此上一练习中 Copilot 才能读取议题待办事项。它使用[远程 GitHub MCP 服务器][remote-github-mcp-server]，无需在本地安装；VS Code 通过超文本传输协议 (HTTP) 连接，并在 Copilot 首次使用其工具时要求你通过 GitHub 身份验证。

## 添加 Playwright MCP 服务器

现在添加第二个服务器。[Playwright MCP 服务器][playwright-mcp-server]为 Copilot 提供可控制的浏览器，正好可以用于测试功能。

1. 在 `.vscode/mcp.json` 中添加 `playwright` 条目，与 `github` 并列。文件应如下所示：

   ```json
   {
     "servers": {
       "github": {
         "type": "http",
         "url": "https://api.githubcopilot.com/mcp/"
       },
       "playwright": {
         "command": "npx",
         "args": ["@playwright/mcp@latest", "--headless"]
       }
     }
   }
   ```

2. 保存文件。

与远程 `github` 服务器不同，`playwright` 是**本地**服务器：VS Code 会在你的计算机上运行 `npx @playwright/mcp@latest` 启动服务器。`--headless` 标志表示 Playwright 在没有可见窗口的情况下运行浏览器；Codespace 没有桌面环境，因此必须使用无头模式。

> [!NOTE]
> Tailspin Toys 项目已在端到端测试中使用 Playwright，因此通常已安装浏览器。如果 Copilot 随后报告缺少浏览器，请让它运行 `npx playwright install chromium`，然后重试。

## 启动并信任服务器

VS Code 会**按需**启动 MCP 服务器：Copilot 首次需要服务器工具时，VS Code 会启动它，并要求你确认是否信任。

1. 在 `.vscode/mcp.json` 中，`playwright` 条目上方会出现 **Start** 操作。选择它以立即启动服务器，确认配置正确。
2. 如果 VS Code 询问是否信任服务器，请先审查配置，然后选择信任，以便启动服务器。

手动启动是可选操作。如果跳过，VS Code 会在下一步中 Copilot 首次需要该工具时启动服务器，并提示你信任。

## 测试筛选功能

[Playwright MCP 服务器][playwright-mcp-server]为 Copilot 提供可驱动的真实浏览器。智能体可以打开网页、导航、应用筛选并读取结果，而无需你手动操作应用；随后它会总结观察结果。无需离开对话，即可快速确认功能行为是否符合预期。

Playwright MCP 服务器会根据页面的[无障碍访问树][playwright-mcp-server]（而非截图）操作。这意味着智能体基于结构化且带标签的元素（按钮、链接、列表项等）进行推理，就像辅助技术一样。因此，快速功能检查也能初步检查无障碍访问情况。

连接服务器并启动应用后，请 Copilot 测试刚完成的筛选功能：

```text
使用 Playwright MCP 服务器，在 http://localhost:4321 打开正在运行的应用，并验证新添加的游戏筛选功能：

1. 打开游戏页面，并记录游戏数量。
2. 应用类别筛选，确认列表只显示该类别的游戏。
3. 清除筛选，然后应用发布商筛选，确认列表已更新为该发布商的游戏。
4. 同时应用类别和发布商筛选，确认结果同时符合两个条件。

报告每一步观察到的结果，并指出任何不符合预期的行为。
```

Copilot 会通过 Playwright MCP 服务器启动浏览器，逐步执行测试并报告结果。请对照议题中的验收条件检查报告；如果发现异常，请追问或让 Copilot 修复代码，再创建拉取请求。

> [!NOTE]
> 测试时，应用必须运行在 `http://localhost:4321`。如果已停止开发服务器，请先重新启动，再发送提示。Copilot 首次使用 Playwright MCP 服务器时可能需要下载浏览器；如果它报告缺少浏览器，请让它运行 `npx playwright install chromium`，然后重试。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## 发布分支并创建拉取请求

确认功能正常后，可以创建拉取请求 (PR)，让团队审查。首先发布 `filtering-vscode` 分支。

1. 打开 Codespace 中的 **Source Control** 面板，审查 Copilot 所做的更改。
2. 选择 **+** 图标暂存更改。
3. 使用 **Sparkle** 按钮生成提交消息。

   ![显示 Copilot 更改的 Source Control 面板截图](../../../_images/ex3-source-control-changes.png)

4. 选择 **Publish**，将分支推送到存储库。

## 创建拉取请求

可以通过 github.com 或 GitHub 命令行界面 (CLI) 等多种方式创建拉取请求。你已经在使用 GitHub Copilot，可以让它帮你查找相关议题并创建关联 PR。

1. 打开 Copilot Chat 面板，选择 **New Chat** 开始新会话。
2. 确认在智能体下拉列表中选择 **Agent**，以便 Copilot 使用 GitHub 工具。
3. 请 Copilot 创建 PR：

   ```plaintext
   查找存储库中与按类别和发布商筛选相关的议题。为当前分支创建新的拉取请求，并关联正确的议题。
   ```

4. 根据需要选择 **Continue**，允许 Copilot 收集信息并执行操作。首次使用 GitHub 工具时，可能会提示通过 GitHub 身份验证；按提示授权即可。
5. 查看 Copilot 如何搜索议题、找到正确议题并创建 PR。
6. 选择 Copilot 生成的链接，审查拉取请求，但**暂时不要合并**。

## 总结和后续步骤

在本练习中，你：

- 了解了模型上下文协议 (MCP) 及 MCP 服务器如何为 Copilot 扩展工具。
- 在工作区中添加了 Playwright MCP 服务器。
- 在发布前使用它在浏览器中手动测试筛选功能。
- 发布分支并为筛选工作创建了拉取请求。

你使用 MCP 服务器测试了功能，但 MCP 只是扩展 Copilot 的一种方式。接下来[创建自定义智能体][next-lesson]，简化无障碍访问审查等专门任务。

## 资源

- [MCP 是什么，为什么受到关注？][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP 注册表][mcp-registry]
- [VS Code 中的 MCP 服务器][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration

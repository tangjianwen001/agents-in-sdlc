---
title: "练习 4 - 监控和管理智能体"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

前几项练习中，你让 Copilot 云智能体处理了三个不同任务，目标是改善用户体验并添加功能。虽然云智能体以异步、自主方式运行，但监控任务仍然很重要。

你可以使用多种工具管理分配给云智能体的任务，包括 GitHub.com 上的[智能体页面][agents-page]。在这个任务控制中心，你可以查看所有带有未关闭拉取请求 (PR) 的智能体任务、了解已执行的操作，并引导正在进行的会话。

在本课中，你将：

- 使用智能体页面监控云智能体任务。
- 引导正在运行的会话，请求添加其他功能。

## 场景

将高对比度模式任务分配给智能体后，团队意识到也适合同时添加浅色模式。既然已在更新网站样式并添加切换功能，就可以一并实现浅色模式。你将引导智能体，确保它同时添加浅色模式和高对比度模式。

## 查看 Copilot 云智能体任务

查看分配给 Copilot 云智能体的所有任务的当前状态。

1. 打开 [Copilot 智能体页面](https://github.com/copilot/agents)。
2. 查看主窗格和左侧窗格中的任务列表。你应能看到已分配给 Copilot 的任务，例如：
   - 更新代码库文档。
   - 生成修改游戏的 API。
   - 为网站添加高对比度模式。
3. 选择一个正在运行的任务，查看 Copilot 执行的操作，例如：
   - 从存储库检出代码。
   - 为 Copilot 创建环境。
   - 设置 MCP 服务器。
   - 执行完成分配任务所需的其他步骤。

> [!NOTE]
> 具体显示的步骤会因 Copilot 的工作状态和采用的方式而异。

4. 同时查看右侧显示的拉取请求 (PR) 窗格，可在其中查看 PR 和更改文件，以便进一步监控。

## 引导云智能体

查看当前任务后，请 Copilot 在实现高对比度模式时也添加浅色模式切换。

1. 选择与添加高对比度模式相关的会话。标题具体内容会因 Copilot 使用的名称和当前工作状态而异。

   ![任务控制中心中的无障碍访问会话](../../../_images/ex6-accessibility-session.png)

2. 等待几分钟，直到会话显示设置已完成并开始执行任务。此时会出现类似下方示例的消息。
3. 在 **Steer active session while Copilot is working** 对话框中输入以下提示：

   ```plaintext
   在实现高对比度模式的同时，也添加浅色模式。浅色模式也应有一个开关，供用户选择所需的显示模式。
   ```

   ![智能体页面上的云智能体任务截图，其中高亮显示“Steer active session while Copilot is working”对话框](../../../_images/ex6-steer-cloud-agent-task.png)

4. 按 <kbd>Enter</kbd> 发送提示。
5. 注意 Copilot 如何确认提示，并将其纳入工作流程。

## 让 Copilot 继续工作

与之前一样，Copilot 会开始处理更新后的任务。它会先完成收到消息时正在执行的步骤，然后将新请求纳入工作流程。

这项工作可能需要几分钟，可以借此时间回顾目前学习和探索的内容。

## 总结和后续步骤

本课探索了 Copilot 智能体页面，它是监控和引导 GitHub Copilot 云智能体任务的中心位置。通过这个任务控制中心，你可以跟踪所有活动任务和已完成任务、审查正在执行的工作，并重定向正在运行的任务以调整范围或提供额外指导。

你了解了以下内容：

- 使用任务控制中心和智能体页面监控云智能体任务。
- 重定向正在运行的会话，请求添加其他功能。

Copilot 正在完成无障碍访问功能。接下来[迭代改进 Copilot 创建的拉取请求][next-lesson]。

## 资源

- [Copilot 智能体页面][agents-page]
- [关于自定义智能体][custom-agents]

---

| [← 上一课：自定义智能体][previous-lesson] | [下一课：迭代改进 Copilot 的工作 →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents

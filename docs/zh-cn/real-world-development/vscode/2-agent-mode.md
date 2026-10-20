---
title: "练习 2 - 使用 Copilot 智能体模式添加功能"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

即使是最简单的应用更新，通常也需要修改多个文件并运行测试。作为开发人员，常见流程包括查找相关文件、进行更改、运行测试、调试、发现遗漏文件，再次修改……这样的工作会不断重复。

Copilot 智能体模式可以帮助处理这类任务。

Copilot 智能体模式可在集成开发环境 (IDE) 中自主执行工作。它会先探索现有项目结构，进行必要更新，运行测试等任务，并自动修复发现的问题。接下来使用智能体模式为网站添加新功能。

> [!NOTE]
> 智能体模式和云智能体名称相似，但提供不同的体验。智能体模式在 IDE 中执行任务，支持快速反馈和交互。云智能体则像团队成员一样异步工作，通过议题和拉取请求与你协作。

在本练习中，你将了解：

- Copilot 智能体模式如何探索项目、查找相关文件并协调多处更改。
- GitHub Copilot 智能体模式如何跨用户界面 (UI) 和数据层实现新功能。
- 合并前如何审查 Copilot 智能体模式生成的更改和测试。

## 场景

游戏列表不断增长，需要允许用户按类别和发布商筛选。上一练习中已添加发布商辅助函数，现在使用 Copilot 智能体模式完成其余数据层、UI 和测试工作。

## 运行 Tailspin Toys 网站

更改代码前，先探索 Tailspin Toys 网站，了解当前功能。

该网站是一个以开发人员为主题的桌游众筹平台，支持列出游戏并显示详情。它是一个 Astro 应用，在构建时将页面渲染为静态超文本标记语言 (HTML)。页面通过 Drizzle 对象关系映射 (ORM) 在 frontmatter 中直接查询本地 SQLite 数据库；没有独立后端应用程序编程接口 (API) 或客户端用户界面 (UI) 框架。可复用的数据访问辅助函数位于 `src/lib/`，交互功能通过使用标准文档对象模型 (DOM) API 的小型 Astro `<script>` 实现。

### 启动网站

项目提供了 `npm run dev` 任务，用于启动单个 Astro 开发服务器。按以下步骤在 GitHub Codespace 中运行：

1. 返回 Codespace。上一练习后应已切回 `main`。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux），在 Codespace 中打开新终端。
3. 为筛选功能创建并切换到新分支：

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 运行以下命令启动网站：

   ```bash
   npm run dev
   ```

   Astro 开发服务器就绪后，应显示类似以下内容的横幅，其中包含 URL：

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> 如果出现提示，询问是否打开 `http://localhost:4321` 的浏览器窗口，请选择 **x** 关闭。

5. 按住 <kbd>Command</kbd>（Mac）或 <kbd>Ctrl</kbd>（Windows/Linux），然后选择终端中的 Astro 服务器地址 `http://localhost:4321`，打开网站。

> [!NOTE]
> 在 Codespace 中选择终端里的 localhost URL 链接时，系统会将其重定向到 `https://<your-codespace-name>-4321.app.github.dev/`。这是通往 Codespace 的私有隧道，网站服务器正在该环境中运行。

### 探索网站

网站运行后，可以探索其功能：

- **主页**：显示桌游列表及其标题、图片和简介。
- **游戏详情页**：选择游戏后会打开详情页，其中包含游戏标题、简介、发布商和类别等信息。

## 使用 Copilot 探索待办事项

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始全新对话。模式和模型选择因练习而异；相关步骤会在需要时说明。

网站的初始实现已可运行，但还需要添加功能。先查看待办事项。根据模板设置项目时，系统已自动创建一组议题；请 GitHub Copilot 展示这些内容。

1. 在 Chat 视图的智能体下拉列表中选择 **Agent**。**Agent** 智能体会自主规划并跨文件实现更改、运行终端命令和调用工具。

   ![Chat 视图中的智能体选择器截图](../../../_images/shared-chat-mode-selector.png)

2. 从可用模型列表中选择 **Claude Sonnet 4.5**。

> [!CAUTION]
> 本研讨会作者不偏好特定模型。编写本研讨会时使用了 Claude Sonnet 4.5，因此将其列入说明，希望代码建议相对一致，从而提供良好体验。但大型语言模型 (LLM) 具有概率性，实际建议可能与示例不同，这是正常且预期的情况。

> [!NOTE]
> 由于 LLM 具有概率性，Copilot 可能会使用不同的 MCP 命令，但仍应能完成任务。

3. 向 Copilot 发送以下提示，询问议题待办列表：

   ```plaintext
   请显示我 GitHub 存储库中的待办事项，并根据对用户的价值帮助我确定优先级。
   ```

4. 选择 **Continue**，运行列出所有议题的命令。
5. 审查生成的议题列表。

Copilot 会根据它认为对用户最有价值的内容为议题排序。

## 查看说明文件

让智能体生成代码前，先查看将为 Copilot 提供上下文的说明文件。项目的[用户界面 (UI) 说明文件][ui-instructions]介绍了如何为网站添加功能。

1. 在 Codespace 中打开 `.github/instructions/ui.instructions.md`。
2. 查看整体指导，了解如何添加功能，包括：
   - 架构概述。
   - 组件设计、可测试性和无障碍访问原则。
   - 指向不同文件类型专用说明的链接，包括 Astro、Tailwind CSS 和 Drizzle。

> [!TIP]
> 说明文件可以引用其他说明文件和项目中的文件。路径相对于说明文件所在位置。这种方式便于复用、拆分较复杂的说明，并提供示例和模板。

## 实现筛选功能

完成筛选功能至少需要更新应用的三个部分：

- 在数据层 (`src/lib/`) 中添加或完善其余筛选逻辑。
- 添加或更新筛选行为测试。
- 更新游戏列表页，添加筛选 UI。

此外，合并前需要运行并通过测试。Copilot 智能体模式可以执行这些任务。

1. 继续当前 Copilot 对话，或选择 **New Chat** 开始新对话。
2. 选择 **Add Context**、**Instructions** 和 **ui** 说明文件。

   ![选择 UI 说明文件的示例截图](../../../_images/ex3-select-instructions-file.png)

3. 确认已在 Chat 视图的智能体下拉列表中选择 **Agent**。

   ![Chat 视图中的智能体选择器截图](../../../_images/shared-chat-mode-selector.png)

4. 确认当前仍选择 **Claude Sonnet 4.5** 模型。
5. 使用以下提示，请 Copilot 根据待办列表中的相关议题实现功能：

   ```plaintext
   请根据待办事项中相关 GitHub 议题的要求，更新网站，添加按发布商和类别筛选的功能。上一练习已创建发布商辅助函数，请保留并在需要时完善现有工作，同时完成其余数据层、UI 和测试。完成前确保所有测试通过。服务器已经在运行，无需再次启动。
   ```

6. 观察 Copilot 如何探索项目并查找与功能相关的文件，包括数据层辅助函数、UI 和测试。随后它会修改文件并运行测试。

   ![Copilot 正在探索项目文件的截图](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot 会执行多项任务，例如探索项目、修改文件和运行测试。根据任务和代码库的复杂程度，这可能需要几分钟。处理过程中，代码编辑器中可能会出现 **Keep** 和 **Undo** 按钮。Copilot 完成后，每项更改都会显示 **Keep** 或 **Undo**，因此无需在它工作时选择这些按钮。

7. Copilot 提示时，选择 **Continue** 运行测试。

   ![Copilot Chat 面板中询问是否允许运行测试的对话框截图](../../../_images/ex3-agent-mode-run-tests.png)

8. 过程中可能会暂停，甚至出现测试失败。这是正常的；Copilot 会在生成代码和运行测试之间迭代，直到完成任务且不再检测到错误。

   ![Copilot 智能体模式完整 Chat 会话截图](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 检查生成的代码，查找潜在问题。

> [!CAUTION]
> 请务必审查 Copilot 或其他 AI 工具生成的代码。

10. 返回正在运行网站的浏览器，探索新功能。
11. 确认功能正常并审查代码后，在 Copilot Chat 窗口中选择 **Keep**。

## 总结和后续步骤

本练习探索了如何使用 GitHub Copilot 智能体模式为 Tailspin Toys 网站添加功能。你了解了：

- GitHub Copilot 智能体模式如何跨 UI 和数据层实现新功能。
- Copilot 智能体模式如何探索项目、查找相关文件并协调多处更改。
- 合并前如何审查 Copilot 智能体模式生成的更改和测试。

接下来使用 [Playwright MCP 服务器测试功能][next-lesson]，并为此功能创建拉取请求。

### 附加探索练习——实现分页

游戏列表扩大后需要支持分页。运用本练习所学，请 Copilot 更新网站，实现分页。编写提示和审查实现时，可以考虑：

- 遵循现有最佳实践，包括使用已有说明文件。
- 确定分页方式，例如让用户选择每页数量，或将数量固定。
- 提示中提供 Copilot 实现所需的指导。
- 视情况通过补充上下文和要求更改，与 GitHub Copilot 迭代。这是使用 Copilot 的正常流程。

## 资源

- [Copilot 的 ask、edit 和 agent 模式：功能及适用场景][choose-mode]
- [VS Code 中的智能体模式][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode

---
title: "练习 1 - 自定义说明（VS Code）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：智能体模式 →][next-lesson]

使用生成式人工智能 (AI) 时，上下文至关重要。如果任务需要以特定方式完成，或者 Copilot 需要了解背景信息，就应确保它能访问相关内容。可以通过多种方式向 Copilot 提供特定上下文，其中一种重要方式是使用[说明文件][instruction-files]，指导代码生成。

在本练习中，你将：

- 查看项目专属上下文、编码指南和文档标准如何通过存储库自定义说明及按路径指定的说明文件传递给 Copilot。
- 使用当前说明生成筛选功能的数据切片（发布商辅助函数）。
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准。
- 再次发送相同提示，观察生成代码如何采用新标准。
- 将说明更新和筛选功能数据切片提交并合并到 `main`，以便 Copilot 在下一练习中使用更新后的指导。

> [!CAUTION]
> 生成的代码可能不完全符合你指定的某些标准。Copilot 具有概率性。本练习的目标是观察更新说明后行为的*趋势变化*，而不是要求输出与示例逐字相同。

## 说明文件

### 场景

Tailspin Toys 制定了以下开发实践要求：

- 数据层必须有单元测试。
- 用户界面 (UI) 应采用深色模式，并具有现代风格。
- 使用 TypeScript 文档注释 (TSDoc) 为代码添加说明。
- 在每个文件开头添加注释块，说明文件用途。

通过说明文件确保 Copilot 遵循这些实践完成任务。

### 自定义说明

自定义说明可向 Copilot 提供上下文和偏好，帮助它理解代码风格和要求。你可以指定编码约定、库，甚至注释习惯，从而引导 Copilot 生成更相关的建议和代码片段。说明既可以应用于整个存储库，也可以针对特定文件类型提供任务级上下文。

说明文件有两种：

- `.github/copilot-instructions.md`：针对存储库中**每个**请求发送给 Copilot 的单个说明文件。应包含适用于大多数请求的项目级信息，例如技术栈、项目概况、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：为特定任务或文件类型提供指南，例如 TypeScript、Astro、UI 组件或单元测试。

> [!NOTE]
> 在 IDE 中，说明文件仅用于 Copilot Chat 代码生成，不用于代码补全或下一步编辑建议。
>
> Copilot Chat、Copilot CLI 和 Copilot 云智能体在生成代码时都会使用存储库级说明文件和带有 `applyTo` frontmatter 的 `*.instructions.md` 文件。
>
> Copilot 还[支持其他标准的说明文件][custom-instructions-support]，包括 AGENTS.md 和 CLAUDE.md。

### 管理说明文件的最佳实践

完整介绍如何创建说明文件超出了本研讨会范围。示例项目展示了一种具有代表性的做法：

- 将 `copilot-instructions.md` 限定为项目级指导，例如项目简介、结构和全局编码标准。
- 使用 `*.instructions.md` 文件提供特定文件类型（单元测试、Astro 组件、数据层）或任务的说明。
- 使用自然语言，保持指导清晰，并提供代码示例。

说明文件没有唯一正确的写法，AI 的使用方式也一样。通过实践找到适合项目的方式。

> [!TIP]
> 每个使用 GitHub Copilot 的项目都应有完善的说明文件。查看项目中的说明时，可以找到适用于 [UI 更新][ui-instructions]和 [Astro][astro-instructions]等任务的文件。
>
> Copilot 也可以帮助生成说明文件。不同使用界面的方式有所不同，例如 VS Code 中的 **Configure Chat → Generate Agent Instructions**，或 Copilot CLI 中的 `/init`。相应课程会在需要时介绍这些操作。
>
> 如需模板或起点，请查看 [awesome-copilot][awesome-copilot]，其中包含说明文件、自定义智能体和其他资源。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## 查看项目中的自定义说明文件

先阅读存储库附带的说明文件：一个核心 `copilot-instructions.md`，以及一组用于不同任务的 `*.instructions.md` 文件。可在编辑器或 GitHub 网页界面中打开它们。

1. 打开 `.github/copilot-instructions.md`。
2. 查看项目简介，以及 **Agent notes**、**Code standards**、**Scripts** 和 **Repository Structure** 等部分。在 **Code standards** 下查看嵌套的 **GitHub Actions Workflows** 指南。这些内容适用于 Copilot 的各类交互。
3. 打开 `.github/instructions` 文件夹并浏览其中的文件。这里包含 Astro、Drizzle 数据层、测试等方面的说明。
4. 打开 `.github/instructions/unit-tests.instructions.md`。注意顶部的 `applyTo` 字段，它设置相对于存储库根目录的 glob，用来确定说明适用的文件。例如，符合 `**/*.test.ts` 的 TypeScript 测试文件会匹配。
5. 查看项目中创建单元测试的专用说明。
6. 打开 `.github/instructions/drizzle.instructions.md` 并滚动到文件底部。查看它指向其他说明文件（例如 `unit-tests.instructions.md`）和项目示例的链接。这种做法可将较大的说明集拆分成可复用的小文件，并为 Copilot 提供代码示例。这里的路径相对于说明文件所在位置，而非存储库根目录。

> [!NOTE]
> `copilot-instructions.md` 中的 **Code formatting requirements** 部分记录了项目编码标准，但尚未要求在代码中添加文档。接下来将添加 TSDoc 文档注释和文件注释头的规则。

## 为更改创建分支

遵循最佳实践，为本练习的更改创建分支。

1. 返回上一练习中的 Codespace。
2. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
3. 创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions
   ```

## 更新说明前先使用 Copilot Chat

> [!TIP]
> **打开 Copilot Chat**
>
> 开始以下练习前，返回 Codespace，打开 Copilot Chat 面板并选择 **New Chat**，开始全新对话。模式和模型选择因练习而异；相关步骤会在需要时说明。

为观察自定义说明的影响，先在现有说明文件下发送提示。之后更新说明，再次发送相同提示并比较结果。

1. 关闭之前练习中打开的编辑器选项卡，让 Copilot 只获取所需上下文。
2. 打开 `src/lib/publishers.ts`，让 Copilot 知道辅助函数应放在哪里。
3. 在 Chat 视图的智能体下拉列表中选择 **Agent**，让 Copilot 能应用文件更改。

   ![Chat 视图中的智能体选择器截图](../../../_images/shared-chat-mode-selector.png)

4. 发送以下提示：

   ```plaintext
   创建或更新 src/lib/publishers.ts，添加数据访问辅助函数，返回所有发布商的名称和 ID。应用文件更改。
   ```

5. Copilot 会探索项目并应用代码更新，通常会同时修改辅助函数及其测试。
6. 注意建议的辅助函数是类型化函数，以 `db` 客户端作为第一个参数，并返回类型化的发布商数组。这来自 `.github/instructions/drizzle.instructions.md` 中适用于 `src/lib/*.ts` 的数据层约定。
7. 注意建议的代码**缺少** TSDoc 文档注释和文件级注释头。

> [!CAUTION]
> Copilot 具有概率性，即使没有说明，也可能添加文档注释。如果出现这种情况也没问题；本练习的重点仍是观察更新说明后行为是否更一致。

## 添加新的存储库标准

`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。请更新存储库编码标准，改善代码建议。

1. 重新打开 `.github/copilot-instructions.md`。
2. 找到 **Code formatting requirements** 部分（大约在第 27 行）。注意其中记录了项目编码标准，但尚无代码文档规则，这也是生成的辅助函数没有文档注释的原因。
3. 在现有标准下方添加以下 Markdown，要求 Copilot 添加文件注释头和 TSDoc 文档注释：

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. 保存 `copilot-instructions.md`。

> [!TIP]
> 说明文件可以放在存储库级 `.github/copilot-instructions.md` 中，提供全局指导，也可以放在 `*.instructions.md` 文件中，针对特定语言、文件类型或任务提供指导。项目级文件适合存放刚添加的文档注释标准等全局要求。

## 重新发送提示并观察变化

说明更新后，在新对话中重新发送相同提示，以便 Copilot 根据新标准生成代码。

1. 返回 Copilot Chat 并选择 **New Chat**，清除对话缓冲区。
2. 再次打开 `src/lib/publishers.ts`，让 Copilot 聚焦正确文件。
3. 发送**相同的提示**：

   ```plaintext
   创建或更新 src/lib/publishers.ts，添加数据访问辅助函数，返回所有发布商的名称和 ID。应用文件更改。
   ```

4. 注意建议的文件现在会以类似以下注释块开头：

   ```typescript
   /**
    * Tailspin Toys 众筹平台的发布商数据访问辅助函数。
    * 提供从数据库中获取发布商信息的函数。
    */
   ```

5. 注意建议的函数现在包含类似以下 TSDoc 注释：

   ```typescript
   /**
    * 返回所有发布商的 ID 和名称列表。
    *
    * @param db - Drizzle 数据库客户端。
    * @returns 解析为发布商对象数组的 Promise。
    */
   ```

你刚刚引导 Copilot 遵循新的项目标准，并将其应用于真实代码；下一练习会在此基础上继续完成工作。

## 提交说明并推送分支

说明文件与存储库中的其他资源一样，都由源代码管理进行维护。现在提交并推送分支。

1. 按 <kbd>Control</kbd>+<kbd>`</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>`</kbd>（Windows/Linux）打开新终端。
2. 在终端中运行以下命令，确认说明更新和辅助函数更改已存在：

   ```bash
   git status
   ```

3. 在终端中暂存并提交说明更新，以及 Copilot 为辅助函数基础所更改的所有文件：

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # 如果 git status 显示还有其他支持性更改（例如测试），也请暂存这些文件。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 将分支推送到存储库：

   ```bash
   git push -u origin custom-instructions
   ```

## 创建并合并拉取请求

分支推送完成后，创建拉取请求并关联待办事项中的文档标准议题。Copilot 可以使用已连接到项目的 GitHub 工具，查找议题、创建用于关闭该议题的 PR，然后合并。

> [!NOTE]
> 这是第一次让 Copilot 代表你在 GitHub 上操作。项目模板已在工作区中配置 GitHub 工具，无需设置。Copilot 首次使用工具时，VS Code 可能会提示登录 GitHub；按提示授权即可。后续练习会介绍这种连接（模型上下文协议）。

1. 在 Codespace 中打开 Copilot Chat。
2. 按 <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd>（Mac）或 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>（Windows/Linux）打开 Copilot Chat 视图，并确认在智能体选择器中选择了 **Agent**。
3. 请 Copilot 查找与更新说明文件相关的议题，并从当前分支创建 PR；说明 PR 既包含说明更新，也包含新的发布商辅助函数基础：

   ```plaintext
   查找与更新说明文件相关的议题。从当前分支创建 PR，说明该 PR 会关闭此议题，并注明我们还为后续筛选工作添加了发布商辅助函数基础。
   ```

4. Copilot 会查找议题并创建 PR。
5. 如果 Copilot 提示 **Allow** 它代表你执行 GitHub 操作，请先检查操作，再按需选择 **Allow**。
6. 创建 PR 后，请 Copilot 合并 PR 并将本地分支切回 `main`：

   ```plaintext
   将 PR 合并到 main。然后在本地切回 main 并拉取最新代码，以保持同步。
   ```

7. 如果 Copilot 提示 **Allow** 它代表你执行 GitHub 操作和 shell 命令，请先检查命令，再按需选择 **Allow**。

你已在 GitHub Copilot 的帮助下创建并合并拉取请求。

## 总结和后续步骤

你已了解 Copilot 如何从项目说明文件获取上下文，并使用 VS Code 中的 Copilot Chat：

- 查看存储库级说明和按路径指定的说明文件如何向 Copilot 提供项目上下文。
- 在**现有**说明下发送代码生成提示，观察 Copilot 的输出。
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准。
- 再次发送相同提示，观察建议代码如何采用新标准。
- 将说明更新和辅助函数基础提交并合并到 `main`，供下一练习使用。

接下来在[智能体模式][next-lesson]中应用这些说明，让 Copilot 跨代码库添加新功能。

## 资源

- [GitHub Copilot 自定义说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更佳 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [GitHub Copilot 个人自定义说明][personal-instructions]
- [Awesome Copilot：说明文件及其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/

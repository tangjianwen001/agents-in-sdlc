---
title: "练习 1 - 自定义说明（云智能体）"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 上一课：先决条件][previous-lesson] · [下一课：Copilot 云智能体 →][next-lesson]

使用生成式人工智能 (AI) 时，上下文至关重要。如果任务需要以特定方式完成，或者 Copilot 需要了解背景信息，就应确保它能访问相关内容。[说明文件][instruction-files]可以提供指导，让 Copilot 理解不仅要做*什么*，还要*如何*完成。

在本练习中，你将：

- 查看项目专属上下文、编码指南和文档标准如何通过存储库自定义说明及按路径指定的说明文件传递给 Copilot。
- 在 `.github/copilot-instructions.md` 中添加新的存储库级标准。

> [!NOTE]
> 与 VS Code 和 CLI 开发环境不同，本环境不会运行更改前后的对比提示。Copilot 云智能体会异步处理 GitHub 议题，因此难以实时并排展示影响。稍后审查云智能体创建的拉取请求时，可以看到说明文件的效果。

## 说明文件

### 场景

Tailspin Toys 制定了以下开发实践要求：

- 数据层必须有单元测试。
- UI 应采用深色模式，并具有现代风格。
- 使用 TypeScript 文档注释 (TSDoc) 为代码添加说明。
- 在每个文件开头添加注释块，说明文件用途。

通过说明文件确保 Copilot 遵循这些实践完成任务。

### 自定义说明

自定义说明可向 Copilot 提供上下文和偏好，帮助它理解代码风格和要求。你可以指定编码约定、库，甚至注释习惯，从而引导 Copilot 生成更相关的建议和代码片段。说明既可以应用于整个存储库，也可以针对特定文件类型提供任务级上下文。

说明文件有两种：

- `.github/copilot-instructions.md`：针对存储库中**每个**请求发送给 Copilot 的单个说明文件。应包含适用于大多数请求的项目级信息，例如技术栈、项目概况、最佳实践和全局指导。
- `.github/instructions/*.instructions.md`：为特定任务或文件类型提供指南，例如 TypeScript、Astro、UI 组件或单元测试。

> [!NOTE]
> 在集成开发环境 (IDE) 中，说明文件仅用于 Copilot Chat 代码生成，不用于代码补全或下一步编辑建议。
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

## 添加新的存储库标准

接下来只需编辑一次：添加项目级规则，要求使用 TSDoc 文档注释和文件级注释头。云智能体稍后处理本开发环境中的议题时会读取这些规则。

编辑前先创建分支。云智能体会读取目标议题分支中的说明，因此之后仍需将更改合并到 `main`。

1. 在 Codespace 终端中创建并切换到新分支：

   ```bash
   git checkout -b custom-instructions-cloud
   ```

`.github/copilot-instructions.md` 用于向 Copilot 提供项目级信息。请将编码标准补充完整。

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

## 提交、推送并合并说明更新

云智能体会读取议题目标分支中的说明。下一练习中，Copilot 会从 `main` 分支创建分支，因此说明更改必须先合并到 `main`，云智能体才能读取。

1. 暂存并提交更改：

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. 推送分支：

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. 在 github.com 上创建从 `custom-instructions-cloud` 到 `main` 的拉取请求并合并。下一练习中，云智能体处理议题时就会读取这些说明。

> [!TIP]
> 如果本研讨会直接在 `main` 上操作更方便，可以跳过分支步骤并直接提交到 `main`。创建分支是为了模拟实际项目中的处理方式。

## 总结和后续步骤

你已了解 Copilot 如何从项目说明文件获取上下文，并在 `.github/copilot-instructions.md` 中添加新的存储库级标准。之后可以在云智能体创建的拉取请求中看到这些标准的效果。

接下来[将第一个议题分配给 Copilot 云智能体][next-lesson]。

## 资源

- [GitHub Copilot 自定义说明文件][instruction-files]
- [创建自定义说明的最佳实践][instructions-best-practices]
- [编写更佳 Copilot 自定义说明的 5 个技巧][copilot-instructions-five-tips]
- [Awesome Copilot：说明文件及其他资源集合][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/

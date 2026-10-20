---
title: "Lesson 12 - 使用 GitHub Pages 部署网站"
description: "使用 GitHub Copilot app 配置 Pages 工作流、审查拉取请求并验证 Space Quiz 已上线。"
authors:
  - jamesmontemagno
lastUpdated: 2026-10-12
---

使用 GitHub Pages 部署 Space Quiz，让它拥有公开访问地址。Copilot 可以准备工作流并引导部署，但仍需审查更改并确认网站已上线。

在本课中，你将：

- 请 Copilot 配置 GitHub Pages 部署。
- 在合并前审查工作流和拉取请求。
- 跟进部署并验证已发布的网站。

## 场景

Space Quiz 已存放在 GitHub 存储库中，但其他人还没有方便的体验方式。使用 GitHub Pages 发布这个单文件网站，无需添加服务器或运行时依赖即可通过公共 URL 访问。

## 请 Copilot 部署网站

在 `space-quiz` 的 Copilot app 会话中发送以下提示：

```plaintext
为此存储库启用 GitHub Pages。创建一个 GitHub Actions 工作流，在需要时构建网站并将其部署到 GitHub Pages。如果存储库中尚无 README，请添加一个文件，介绍应用以及如何在本地运行。为这些更改创建拉取请求，并在所需检查通过后合并。持续监控部署，直到成功，然后报告网站的最终在线 URL。如果你没有权限启用 Pages 或合并，请明确告知我需要批准或执行哪些操作；网站上线前不要报告成功。
```

![三阶段示意图：已合并的 space-quiz 拉取请求；成功运行的 GitHub Actions“Deploy website”工作流，包含“Build website”“Upload artifact”和“Deploy to Pages”步骤；以及位于 yourname.github.io/space-quiz/ 的在线 Space Quiz。](../../../_images/first-steps-app-pages-deploy.svg)

> [!WARNING]
> 此提示要求智能体创建并合并拉取请求，以及更改存储库的 Pages 设置。继续操作前，请审查差异、部署权限和 GitHub 的审批提示。

## 审查工作流并验证部署

合并前先检查智能体提出的工作流和部署更改；部署完成后，再确认公开网站可以访问。

1. 确认只有在存储库尚无 README 时才会添加 README。
2. 审查拉取请求中的更改，包括工作流的构建和部署步骤。
3. 仅在所需的拉取请求检查通过后合并。
4. 跟进 GitHub Actions 部署运行，直到成功。
5. 打开报告中的 URL，确认 Space Quiz 可以加载。

如果智能体无法启用 Pages 或合并拉取请求，请按它提供的具体说明操作。只有部署成功且在线网站可以加载后，任务才算完成。

## 总结和后续步骤

Space Quiz 现已作为在线网站发布；你也已亲自验证部署，而非仅依赖智能体的报告。接下来继续学习[第 13 课：审查与后续步骤][next-lesson]。

[next-lesson]: ../13-review/

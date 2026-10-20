---
slug: ja-jp/real-world-development/vscode
title: "Visual Studio Code"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

VS Code の **[GitHub Copilot Chat](https://code.visualstudio.com/docs/copilot/chat/copilot-chat)** を使うと、普段使っているコードエディターで GitHub Copilot を利用できます。Visual Studio Code と GitHub Codespaces でエージェントモードの Copilot Chat を使い、Model Context Protocol (MCP) で外部ツールに接続し、カスタムエージェントを活用します。統合開発環境 (IDE) を離れずに、ファイル、ターミナル、問題全体を Copilot に確認させながら作業できます。

まずカスタム命令を追加して Copilot の応答を確認し、次にエージェントモードでユーザー インターフェイス (UI)、データ層、テストにまたがるフィルタリング機能を実装します。その後、Playwright MCP サーバーを接続し、プルリクエストの作成前に Copilot にブラウザーを操作させて機能をテストします。最後に、アクセシビリティ作業用のカスタムエージェントを確認して使い、エディターを離れずに Copilot の変更を監視、調整、反復します。

## 演習

| 演習 | トピック | 説明 |
|----------|-------|-------------|
| [0. 前提条件][ex0] | セットアップ | リポジトリと Codespace を作成します |
| [1. カスタム命令][ex1] | コンテキスト | VS Code でカスタム命令を追加して確認します |
| [2. エージェントモード][ex2] | コード生成 | エージェントモードでフィルタリング機能を構築します |
| [3. Playwright MCP][ex3] | 外部ツール | Playwright MCP サーバーでブラウザー上の機能をテストします |
| [4. カスタムエージェント][ex4] | 専門エージェント | カスタムエージェントを確認して使用します |
| [5. エージェントの管理][ex5] | 監視 | エージェント セッションを監視して指示します |
| [6. 反復して改善する][ex6] | レビュー | Copilot の作業をローカルでレビューし、次の手順を選びます |
| [オプション: Foundry を組み込む][foundry-toolkit] | AI エージェント | モデルを準備し、エージェントをデプロイして、VS Code と Foundry Toolkit でサイトに接続します |

## 前提条件

このワークショップを始める前に、次のものを用意してください。

- [ ] **Copilot Student、Pro、Pro+、Business、Enterprise** のいずれかが有効な GitHub アカウント
- [ ] GitHub Codespaces を利用できること

> [!TIP]
> 有料プランがなくても、認証済みの学生は [GitHub Education][callout-student-plan-education] を通じて GitHub Copilot を無料で利用できます。**Copilot Student** プランには、このワークショップで使うエージェント、MCP、コードレビュー、Copilot CLI の機能が含まれています。

[callout-student-plan-education]: https://github.com/education/students

## 始める

**[演習 0: 前提条件から始める →][ex0]**

[ex0]: 0-prerequisites/
[ex1]: 1-custom-instructions/
[ex2]: 2-agent-mode/
[ex3]: 3-mcp/
[ex4]: 4-custom-agents/
[ex5]: 5-managing-agents/
[ex6]: 6-iterating/
[foundry-toolkit]: 7-foundry-toolkit/

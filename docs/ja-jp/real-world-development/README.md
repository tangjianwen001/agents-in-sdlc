---
slug: ja-jp/real-world-development
title: "実践的な開発"
authors:
  - geektrainer
lastUpdated: 2026-09-16
---

現実的なシナリオ、完成したアプリケーション、共有のバックログを使い、ソフトウェア開発ライフサイクル全体で GitHub Copilot を体験します。変更を計画し、コードを作成して動作をテストし、プルリクエストをレビューし、エージェントを使って作業を進めます。

## シナリオ

ボードゲームのクラウドファンディングを行う架空の企業 Tailspin Toys に、新しい開発者として参加します。チームのバックログは GitHub の Issue として登録済みです。フィルタリングやページネーションなどの機能開発に加え、アクセシビリティやコーディング標準などの品質改善にも取り組みます。

## 作業環境を選ぶ

GitHub Copilot は、さまざまな作業環境で利用できます。自分に合った環境を選んでください。各ワークショップでは、それぞれの環境をセットアップして、共通の Tailspin Toys シナリオに取り組みます。

### [GitHub Copilot CLI][cli]

ターミナルでエージェント型アシスタントを使います。Plan モードと Autopilot モード、指示、スキル、カスタムエージェント、Playwright の Model Context Protocol (MCP) サーバー、Agent Merge、実用的なスラッシュ コマンドを扱います。

### [GitHub Copilot app][app]

デスクトップ アプリでエージェント セッションを並行して実行し、セッションモードを切り替え、Canvas で共同作業し、GitHub の Issue とプルリクエストを管理します。

### [GitHub Copilot cloud agent][cloud]

GitHub の Issue を非同期エージェントに割り当て、作業を案内し、進捗を監視して、エージェントが作成したプルリクエストをレビューします。

### [Visual Studio Code][vscode]

Visual Studio Code と GitHub Codespaces で GitHub Copilot を使います。エディターを離れずに、Copilot Chat のエージェントモード、MCP サーバー、カスタムエージェントを利用します。

[cli]: cli/
[app]: app/
[cloud]: cloud/
[vscode]: vscode/

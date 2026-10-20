---
title: "演習 4 - エージェントの監視と管理"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

ここまでの演習で、ユーザー体験の改善や機能追加に関する 3 つのタスクを Copilot cloud agent に依頼しました。cloud agent は非同期かつ自律的に動作しますが、タスクの進捗を監視することも重要です。

GitHub.com の[エージェント ページ][agents-page]など、cloud agent に割り当てたタスクを管理する方法はいくつかあります。この Mission Control では、オープン中のプルリクエスト (PR) があるエージェント タスクを確認できます。実行内容を調べ、進行中のセッションに指示を追加することもできます。

このレッスンでは、次の作業を行います。

- エージェント ページで cloud agent のタスクを監視します。
- 実行中のセッションに追加機能を依頼します。

## シナリオ

ハイコントラスト モードの作成をエージェントに依頼した後、チームはライト モードも追加することにしました。サイトのスタイル更新と切り替え機能を実装中なので、ライト モードも同じ作業に含めるのが適切です。ハイコントラストに加えてライト モードも実装されるよう、エージェントに指示します。

## Copilot cloud agent のタスクを確認する

Copilot cloud agent に割り当てたすべてのタスクの状態を確認します。

1. [Copilot agents ページ](https://github.com/copilot/agents)を開きます。
2. メイン ペインと左ペインにあるタスク一覧を確認します。割り当てた次のようなタスクが表示されます。
   - コードベースのドキュメントを更新する。
   - ゲームを変更する API を生成する。
   - Web サイトにハイコントラスト モードを追加する。
3. 実行中のタスクを選択し、Copilot が行った作業を確認します。次のような操作が表示される場合があります。
   - リポジトリからコードをチェックアウトする。
   - Copilot が作業する環境を作成する。
   - MCP サーバーをセットアップする。
   - 割り当てられたタスクを完了するための各種手順を実行する。

> [!NOTE]
> 表示される手順は、Copilot の作業状態や採用した方法によって異なります。

4. 右側に表示される PR ペインを確認します。ここから PR と変更ファイルを確認し、作業をさらに監視できます。

## cloud agent に追加の指示を出す

進行中のタスクを確認したので、エージェントがハイコントラスト モードを実装している間に、ライト モードの切り替えも追加するよう依頼します。

1. ハイコントラスト モードの追加に関するセッションを選択します。タイトルは、Copilot が付けた名前や作業状況によって異なります。

   ![Mission Control のアクセシビリティ セッション](../../../_images/ex6-accessibility-session.png)

2. セットアップが完了し、実装を開始したことを示すメッセージが表示されるまで、セッションを数分間確認します。
3. **Steer active session while Copilot is working** ダイアログに、次のプロンプトを入力します。

   ```plaintext
   While we are working on a high contrast mode, let's also add a light mode. There should be a switch for this mode as well where users can select their desired display mode.
   ```

   ![エージェント ページで、cloud agent のタスクに追加指示を送るダイアログを示すスクリーンショット](../../../_images/ex6-steer-cloud-agent-task.png)

4. <kbd>Enter</kbd> を押してプロンプトを送信します。
5. Copilot がプロンプトを受け取り、作業計画に追加したことを確認します。

## Copilot の作業を待つ

Copilot は更新されたタスクに取り組みます。メッセージを受け取った時点で作業していた手順が完了した後、新しい依頼を作業に加えます。

これまでと同様に完了まで数分かかる場合があります。その間に、ここまでに学んだことを振り返ってください。

## まとめと次のステップ

Copilot cloud agent のタスクを監視、指示する中心的な場所である Copilot agents ページを確認しました。Mission Control では、進行中および完了済みのタスクを追跡し、作業内容をレビューして、実行中のタスクの範囲を変更したり追加の指示を出したりできます。

このレッスンでは、次の作業を行いました。

- Mission Control とエージェント ページで cloud agent のタスクを監視しました。
- 実行中のセッションに追加機能を依頼しました。

Copilot がアクセシビリティ機能を実装している間に、[Copilot が作成したプルリクエストの改善に進みます][next-lesson]。

## リソース

- [Copilot agents ページ][agents-page]
- [カスタムエージェントについて][custom-agents]

---

| [← 前のレッスン: カスタムエージェント][previous-lesson] | [次のレッスン: Copilot の作業を反復して改善する →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents

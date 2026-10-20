---
title: "演習 2 - GitHub Copilot cloud agent"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: カスタム命令][previous-lesson] |
|:--|

多くの組織が、未解決のセキュリティ問題、更新が必要なレガシー コード、実装する時間がなくバックログに残った機能要求などの技術的負債を抱えています。GitHub Copilot cloud agent は、コードの更新や機能の追加といったタスクを自律的に実行できます。作業が完了すると、人間の開発者がレビューできるドラフトのプルリクエスト (PR) を作成します。手間のかかる作業を委任し、開発を速めることで、開発者はより大きな課題に集中できます。

この演習では、Copilot cloud agent を使って次の内容を確認します。

- コード生成用の環境をカスタマイズする。
- 操作を安全に実行する。
- 範囲を明確にした Issue の重要性を理解する。
- Issue を Copilot に割り当てる。

## シナリオ

Tailspin Toys には解決したい技術的負債があります。サイトの初期バージョンを作成した開発者がドキュメントを十分に整備していません。最初の作業として、アプリケーション内のエクスポートされた関数すべてに TSDoc ドキュメント コメントを追加します。

また、デザイン チームはゲームを見つけやすくしたいと考えています。各ゲームの詳細ページに、同じカテゴリに属する他のゲームを表示し、ユーザーが引き続き閲覧できるようにします。現時点では、ユーザー受け入れテストに使える簡易的な実装を求めており、洗練された仕上がりは必要ありません。この機能はブロッカーですが、より優先度の高い Issue もあります。

どちらも優先度が下がりやすい作業で、Copilot cloud agent に割り当てるのに適しています。cloud agent が非同期に作業している間、開発者は別の作業に取り組み、後から提案内容をレビューできます。

## GitHub Copilot cloud agent の概要

[GitHub Copilot cloud agent](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) は、人間の開発者と同じようにバックグラウンドでタスクを実行できます。[GitHub Issue を Copilot に割り当てる][assign-issue]など、複数の方法で作業を開始できます。割り当てると、Copilot は進捗を追跡するドラフト PR を作成し、環境をセットアップしてタスクに着手します。実行中または完了後にセッションの内容を確認できます。解決案のレビュー準備ができると、PR で通知されます。

## 範囲を明確にした指示の重要性

GitHub Copilot に魔法のような機能はなく、考える手順を省略できるわけではありません。一見単純な作業でも、詳しく確認すると複雑な場合があります。[Copilot cloud agent に依頼するタスクの範囲を慎重に決めてください][cloud-agent-best-practices]。AI ペア プログラマーとして、段階を踏み、学び、試し、必要に応じて調整します。生成 AI を導入しても、ソフトウェア開発の基本は変わりません。

## このリポジトリのカスタム命令

前の演習では、カスタム命令と Copilot の指針としての使い方を確認しました。すでにカスタム命令の演習に取り組んだ場合は、`.github/copilot-instructions.md` が実際に使われる様子を確認しています。まだの場合も、ここで簡単に確認できます。Issue を cloud agent に割り当てる前に、リポジトリに用意されている指示ファイルを読み取り専用で確認して、後でその影響を調べられるようにします。

Issue を cloud agent に割り当てる前に、リポジトリの GitHub Web UI または起動中の Codespace で指示ファイルを読み取り専用で確認します。

1. `.github/copilot-instructions.md` を開きます。**Code standards** セクションを確認し、特に TypeScript 規約と TSDoc ドキュメント コメントの要件に注目します。
2. `.github/instructions/unit-tests.instructions.md` を開きます。`applyTo` frontmatter が `**/*.test.ts` ファイルを対象にしていることを確認します。

次のセクションで `Code lacks documentation` という Issue を cloud agent に割り当てるときは、作成された PR に TSDoc コメント、TypeScript 規約、コメント ヘッダーが含まれていることを確認します。これらは指示ファイルによるものです。後の演習で PR をレビューするときに、改めて説明します。

## Copilot cloud agent の開発環境をセットアップする

コードを作成する際は、通常、必要なツールや依存関係を用意するため、特定の環境とセットアップ スクリプトが必要です。Copilot にタスクを割り当てる場合も同様で、cloud agent はソフトウェア エンジニアと同様の作業を行います。

Cloud agent は作業環境として [GitHub Actions][github-actions] を使います。`.github/workflows/copilot-setup-steps.yml` に[セットアップ ワークフロー][setup-workflow]を作成すると、作業開始前に実行される環境をカスタマイズできます。これにより、必要な開発ツールと依存関係を利用できるようになります。ワークショップの流れを簡潔にするため、この設定はあらかじめ用意されています。単一の Astro アプリに必要な Node.js、プロジェクトの依存関係、エンドツーエンド テスト用のブラウザー、マイグレーションおよびシード済みの SQLite データベースを準備します。

```yaml
name: "Copilot Setup Steps"

# リポジトリの Actions タブからセットアップ手順をテストできます。
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # セットアップ手順に必要な最小限の権限を設定します。Copilot は操作用の独自トークンを取得します。
    permissions:
      # 依存関係のインストールなど、セットアップ手順でリポジトリをクローンする場合は `contents: read` 権限が必要です。手順でクローンしない場合は、完了後に Copilot が自動的にクローンします。
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # フロントエンドとアプリのセットアップ — Node.js (アプリは Astro + Drizzle/libSQL です)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # ローカルの SQLite データベースをマイグレーションし、シード データを設定します。
      - name: Set up the database
        run: npm run db:setup
```

ほかの GitHub ワークフローと同じような構成ですが、次の点を確認してください。

- `copilot-setup-steps` という単一のジョブがあり、Copilot が PR に着手する前に GitHub Actions で実行されます。
- `workflow_dispatch` トリガーを使うと、Copilot の開始を待たずにリポジトリの **Actions** タブからワークフローを手動実行できます。設定のテストに便利です。

## ドキュメントを追加する

ドキュメントの重要性は知られていますが、多くのプロジェクトでは情報が古かったり、ドキュメントが不足していたりします。この種の技術的負債は後回しになり、作業効率を下げ、コードベースの保守や新しい開発者の参加を難しくします。Copilot はドキュメント作成に役立つため、この Issue を cloud agent に割り当てます。エージェントがバックグラウンドでドキュメントを生成し、後の演習でその内容をレビューします。

1. 新しいブラウザー タブで github.com のリポジトリを開きます。
2. **Issues** タブを選択します。
3. **New issue** を選択して新しい Issue ダイアログを開きます。
4. **Blank issue** を選択します。
5. **Title** に `Code lacks documentation` と入力します。
6. **Description** に次の内容を入力します。

   ```plaintext
   Our organization has a requirement that functions and methods include TSDoc doc comments where helpful. Unfortunately, recent updates haven't followed this standard. We need to update the existing code to ensure doc comments are included where they clarify behavior.
   ```

7. **Create** を選択して Issue を作成します。
8. 右側にある **Assign to Copilot** を選択し、割り当てダイアログを開きます。

   ![Copilot を Issue に割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。
10. **Pull Requests** タブを選択します。
11. 新しく生成された PR を開きます。タイトルは `[WIP]: Code lacks documentation` のようになります。一覧に表示されない場合は、少し待ってからブラウザーを更新します。
12. 数分後に Copilot が作成した ToDo リストを確認します。

> [!NOTE]
> Copilot が ToDo リストを PR に表示するまで数分かかる場合があります。Copilot は環境を作成して先ほどのワークフローを実行し、プロジェクトを分析して、問題に取り組む方法を決めています。

13. リストと、Copilot が実行するタスクを確認します。
14. PR のタイムラインを下にスクロールし、Copilot が Issue の作業を開始したことを示す更新を確認します。
15. **View session** ボタンを選択します。

   ![Copilot のセッション ビュー](../../../_images/ex4-view-session.png)

> [!CAUTION]
> 更新後のインジケーターを表示するために、ページの再読み込みが必要な場合があります。

16. ライブ セッションをスクロールし、Copilot が問題を解決する様子を確認します。コードの調査、状態の把握、計画を検討するための一時停止、コードの作成などが含まれます。

作業には数分かかる場合があります。Copilot cloud agent の主な目的は、タスクを非同期に実行し、開発者が別の作業に取り組めるようにすることです。この機能を活用するため、もう 1 つのタスクを cloud agent に割り当ててから、アプリに機能を追加する作業に取り組みます。

## ゲーム詳細ページに関連ゲームを追加する

GitHub Copilot cloud agent の利点の 1 つは、作業を分担できることです。デザイン チームから依頼された関連ゲームの表示は時間がかからない場合でも、別の作業に時間を使えます。cloud agent にこの機能を割り当てます。

1. github.com のリポジトリに戻ります。
2. **Issues** タブを選択します。
3. **New issue** を選択します。
4. **Blank issue** を選び、空のテンプレートを使います。
5. **Title** に `Show related games on the game details page` と入力します。
6. **Description** に次の内容を入力します。

   ```markdown
   We want to help people discover more games by showing related games on each game's details page. The design team wants to explore the UX and do some acceptance testing. Our requirements are:

   - Add a data-access helper in `src/lib/` that returns other games in the same category as a given game, excluding that game
   - Show a "Related games" section on the game details page that uses the helper
   - Handle the case where a game has no related games
   - There should be unit tests created for the new helper
   - Before creating the PR, ensure all tests pass
   ```

7. **Create** を選択して Issue を作成します。
8. 右側の **Assign to Copilot** を選択し、割り当てダイアログを開きます。

   ![Copilot を Issue に割り当てる画面](../../../_images/shared-assign-copilot.png)

9. **Assign** を選択します。

まもなく、Issue の最初のコメントに 👀 が表示されます。Copilot が作業を開始した合図です。

![目の絵文字で Issue に取り組み中であることを示す Copilot](../../../_images/ex4-issue-eyes-emoji.png)

Copilot は 2 つ目の依頼にも取り組み始めました。Copilot cloud agent はソフトウェア エンジニアと同様に作業するため、常に監視する必要はありません。完了後にレビューできます。次はカスタムエージェントを作成して使います。

## まとめと次のステップ

[GitHub Copilot cloud agent][copilot-agents]について確認しました。cloud agent に Issue を割り当てると、非同期に作業を実行できます。技術的負債への対応、新機能の作成、別のフレームワークへのコード移行などに利用できます。

この演習では、次の作業を行いました。

- コード生成用の環境をカスタマイズしました。
- 操作を安全に実行する方法を確認しました。
- 範囲を明確にした Issue の重要性を学びました。
- Issue を Copilot に割り当てました。

cloud agent がバックグラウンドで作業している間に、[カスタムエージェントの作成と使用][next-lesson]に進みます。[Copilot cloud agent は MCP サーバーも利用できます][cloud-agent-mcp]。前の演習で確認したカスタム命令も使えます。

## リソース

- [Copilot cloud agent について][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップ ワークフローのベストプラクティス][cloud-agent-best-practices]

| [次のレッスン: カスタムエージェント →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions

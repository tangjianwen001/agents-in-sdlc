---
title: "演習 3 - Model Context Protocol (MCP) の Playwright サーバーで機能をテストする"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: エージェントモード][previous-lesson] · [次のレッスン: カスタムエージェント →][next-lesson]

エージェントモードでフィルタリング機能を構築しました。プルリクエストを作成する前に、ブラウザーで実際に動作することを確認します。アプリを手動で操作する代わりに、**Playwright MCP サーバー**を接続し、Copilot にブラウザーを操作させて機能をテストします。その後、ブランチを公開して PR を作成します。

この演習では、次の作業を行います。

- Model Context Protocol (MCP) と、MCP サーバーが Copilot に新しいツールを追加する方法を学びます。
- Playwright MCP サーバーをワークスペースに追加します。
- Copilot にサーバーを使わせ、ブラウザーでフィルタリング機能を手動テストします。
- ブランチを公開してフィルタリングの PR を作成します。

## Model Context Protocol (MCP) とは

エージェントモードは、エディターの外部に接続すると、さらに多くのことを実行できます。Copilot が外部ツールやサービスとやり取りするための標準が Model Context Protocol (MCP) です。

![エージェントモード内部の動作と、MCP サーバーおよび VS Code 拡張機能が提供するツールを含むコンテキスト、言語モデル、ツールとの連携を示す図](../../../_images/ex1-mcp-diagram.png)

[Model Context Protocol (MCP)](https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/) は、AI エージェントが外部ツールやサービスと通信するための仕組みです。MCP を使うと、AI エージェントは外部ツールやサービスとリアルタイムに通信できます。リソースを使って最新情報にアクセスしたり、ツールを使って代わりに操作を実行したりできます。

これらのツールとリソースは、AI エージェントと外部ツールやサービスの間をつなぐ MCP サーバーを通じて利用します。MCP サーバーは、既存のアプリケーション プログラミング インターフェイス (API) や npm パッケージなどのローカル ツールとの通信を管理します。各 MCP サーバーは、AI エージェントが使えるツールとリソースを提供します。

よく使われる MCP サーバーには、次のものがあります。

- **[GitHub MCP Server](https://github.com/github/github-mcp-server)**: GitHub リポジトリを管理する API を利用できます。AI エージェントがリポジトリを作成、更新し、Issue やプルリクエストを管理できます。
- **[Playwright MCP Server](https://github.com/microsoft/playwright-mcp)**: Playwright を使ったブラウザー自動化機能を利用できます。AI エージェントが Web ページを移動し、フォームに入力し、ボタンを選択できます。

ほかにも多くの MCP サーバーがあります。GitHub は、エコシステム内の検索と貢献を促す [MCP レジストリ](https://github.com/mcp)をホストしています。

> [!CAUTION]
> セキュリティ面では、MCP サーバーをプロジェクト内のほかの依存関係と同様に扱ってください。使う前にソースコードを慎重に確認し、公開元を検証して、セキュリティへの影響を検討します。信頼できる MCP サーバーのみを使用し、機密リソースや操作へのアクセスを許可する際は注意してください。

## MCP の構成を確認する

`.vscode/mcp.json` ファイルで、この VS Code ワークスペースで使用できる MCP サーバーを設定します。

1. Codespace で `.vscode/mcp.json` を開きます。
2. `github` サーバーがすでに構成されていることを確認します。

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

この `github` エントリはプロジェクト テンプレートに含まれています。そのため、前の演習で Copilot が Issue のバックログを参照できました。[リモート GitHub MCP サーバー][remote-github-mcp-server]を使うため、ローカルにインストールする必要はありません。VS Code は HTTP で接続し、Copilot が初めてツールを使うときに GitHub で認証します。

## Playwright MCP サーバーを追加する

次に、2 つ目のサーバーを追加します。[Playwright MCP サーバー][playwright-mcp-server]を使うと Copilot がブラウザーを操作できます。機能のテストに利用します。

1. `.vscode/mcp.json` の `github` と並べて `playwright` エントリを追加し、ファイルを次のようにします。

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

2. ファイルを保存します。

リモートの `github` サーバーとは異なり、`playwright` は**ローカル** サーバーです。VS Code は `npx @playwright/mcp@latest` を実行して起動します。`--headless` フラグを指定すると、Playwright は画面を表示せずにブラウザーを実行します。Codespace にはブラウザーを表示するデスクトップがないため、この設定が必要です。

> [!NOTE]
> Tailspin Toys はエンドツーエンド テストで Playwright を使っているため、必要なブラウザーは通常インストール済みです。ブラウザーが見つからないと Copilot から報告された場合は、`npx playwright install chromium` を実行するよう依頼して、もう一度試します。

## サーバーを起動して信頼する

VS Code は、Copilot がサーバーのツールを初めて必要としたときに MCP サーバーを起動します。その際、サーバーを信頼するか確認されます。

1. `.vscode/mcp.json` で `playwright` エントリの上に **Start** アクションが表示されます。これを選択し、今すぐサーバーを起動して構成が正しいことを確認します。
2. VS Code からサーバーを信頼するか確認されたら、構成を確認して、サーバーの起動を許可します。

手動での起動は任意です。省略した場合、次の手順で Copilot がツールを初めて使う際に VS Code がサーバーを起動し、信頼を確認します。

## フィルタリング機能をテストする

[Playwright MCP サーバー][playwright-mcp-server]を使うと、Copilot が実際のブラウザーを操作できます。開発者がアプリを手動で確認する代わりに、エージェントがページを開き、移動してフィルターを適用し、結果を読み取って報告します。会話を離れず、機能が想定どおりに動作することをすばやく確認できます。

Playwright MCP サーバーはスクリーンショットではなく、ページの[アクセシビリティ ツリー][playwright-mcp-server]を使って操作します。つまり、エージェントは支援技術と同じように、ボタン、リンク、リスト項目などの構造化されラベル付けされた要素を認識します。機能の簡単な確認を、アクセシビリティの基本的な確認にも活用できます。

サーバーを接続し、アプリが実行中であることを確認してから、次のプロンプトで Copilot にフィルタリング機能を試させます。

```text
Using the Playwright MCP server, open a browser to the running app at http://localhost:4321 and verify the new game filtering feature:

1. Go to the games page and note how many games are listed.
2. Apply a category filter and confirm the list updates to only show games in that category.
3. Clear it, then apply a publisher filter and confirm the list updates to that publisher.
4. Combine a category and a publisher filter and confirm the results respect both.

Report what you observe at each step, and call out anything that does not behave as expected.
```

Copilot は Playwright MCP サーバー経由でブラウザーを起動し、手順を実行して結果を報告します。報告内容を Issue の受け入れ条件と照らし合わせます。想定外の動作があれば、プルリクエストを作成する前に質問したり、コードを修正するよう依頼したりします。

> [!NOTE]
> このテストでは、アプリが `http://localhost:4321` で実行中である必要があります。開発サーバーを停止している場合は、プロンプトを送る前に再起動します。Copilot が Playwright MCP サーバーを初めて使うときに、ブラウザーをダウンロードする場合があります。ブラウザーが見つからないと報告された場合は、`npx playwright install chromium` を実行するよう依頼して再試行します。

[playwright-mcp-server]: https://github.com/microsoft/playwright-mcp

## ブランチを公開してプルリクエストを作成する

機能が動作することを確認したら、チームがレビューできるよう PR を作成します。まず `filtering-vscode` ブランチを公開します。

1. Codespace の **Source Control** パネルに移動し、Copilot が行った変更を確認します。
2. **+** アイコンを選択して変更をステージします。
3. **Sparkle** ボタンを使ってコミット メッセージを生成します。

   ![変更を示す Source Control パネルのスクリーンショット](../../../_images/ex3-source-control-changes.png)

4. **Publish** を選択してブランチをリポジトリにプッシュします。

## プルリクエストを作成する

github.com や GitHub コマンドライン インターフェイス (CLI) など、PR を作成する方法はいくつかあります。GitHub Copilot を使って、関連 Issue を探し、Issue と関連付けた PR を作成します。

1. Copilot Chat パネルに移動し、**New Chat** を選択して新しいセッションを開始します。
2. エージェント選択メニューで **Agent** が選ばれていることを確認し、Copilot が GitHub ツールを使えるようにします。
3. 次のプロンプトで PR の作成を依頼します。

   ```plaintext
   Find the issue in the repo related to filtering by category and publisher. Create a new pull request for the current branch, and associate it with the correct issue.
   ```

4. 必要に応じて **Continue** を選択し、Copilot が情報を収集して操作を実行できるようにします。GitHub ツールを初めて使うときに GitHub の認証を求められた場合は、画面の指示に従って許可します。
5. Copilot が Issue を検索し、該当する項目を見つけて PR を作成する様子を確認します。
6. Copilot が生成したリンクを選択して PR をレビューします。**まだマージしないでください。**

## まとめと次のステップ

この演習では、次の作業を行いました。

- Model Context Protocol (MCP) と、MCP サーバーが Copilot に新しいツールを追加する方法を学びました。
- Playwright MCP サーバーをワークスペースに追加しました。
- ブラウザーで機能を手動テストするようサーバーを使いました。
- ブランチを公開してフィルタリングの PR を作成しました。

MCP サーバーを使って機能を**テスト**しましたが、MCP は Copilot を拡張する方法の 1 つにすぎません。次は、アクセシビリティ レビューなどの目的を絞った作業を効率化する[カスタムエージェントを作成します][next-lesson]。

## リソース

- [MCP とは何か、なぜ注目されているのか][mcp-blog-post]
- [Microsoft Playwright MCP Server][playwright-mcp-server]
- [GitHub MCP Server][github-mcp-server]
- [GitHub MCP Registry][mcp-registry]
- [VS Code の MCP サーバー][vscode-mcp-config]

[previous-lesson]: ../2-agent-mode/
[next-lesson]: ../4-custom-agents/
[mcp-blog-post]: https://github.blog/ai-and-ml/llms/what-the-heck-is-mcp-and-why-is-everyone-talking-about-it/
[github-mcp-server]: https://github.com/github/github-mcp-server
[mcp-registry]: https://github.com/mcp
[remote-github-mcp-server]: https://github.blog/changelog/2025-06-12-remote-github-mcp-server-is-now-available-in-public-preview/
[vscode-mcp-config]: https://code.visualstudio.com/docs/agents/reference/mcp-configuration

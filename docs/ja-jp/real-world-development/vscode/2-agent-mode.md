---
title: "演習 2 - Copilot エージェントモードで機能を追加する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

アプリケーションの小さな変更でも、通常は複数のファイルの更新やテストの実行が必要です。開発者は必要なファイルを探し、変更を加え、テストを実行して、問題をデバッグし、編集し忘れたファイルを見つけて、さらに更新を加えます。こうした作業が繰り返されます。

このような作業には Copilot のエージェントモードが役立ちます。

Copilot のエージェントモードは、統合開発環境 (IDE) でより自律的に作業します。開発者と同様にプロジェクトの構成を調べ、必要な変更を行い、テストなどのタスクを実行し、見つかった問題を自動で修正します。エージェントモードを使って、サイトに新機能を追加する方法を確認します。

> [!NOTE]
> 名前は似ていますが、エージェントモードと cloud agent は異なる用途向けです。エージェントモードは IDE 内で作業するため、すばやくフィードバックを得て対話できます。Cloud agent はチームの一員のように非同期で作業し、Issue とプルリクエストを通じて開発者とやり取りします。

この演習では、次の方法を学びます。

- Copilot エージェントモードでプロジェクトを調査し、関連ファイルを特定して、連携した変更を行う。
- GitHub Copilot エージェントモードで UI とデータ層にまたがる機能を実装する。
- コードベースにマージする前に、Copilot エージェントモードが生成した変更とテストをレビューする。

## シナリオ

ゲーム一覧が増えてきたため、ユーザーがカテゴリとパブリッシャーで絞り込めるようにします。前の演習ではパブリッシャー ヘルパーを追加しました。ここでは Copilot エージェントモードを使って、残りのデータ層、UI、テスト作業を完了します。

## Tailspin Toys の Web サイトを実行する

変更を加える前に、Tailspin Toys の Web サイトを確認し、現在の機能を把握します。

この Web サイトは、開発者をテーマにしたボードゲームのクラウドファンディング プラットフォームです。ゲームの一覧と詳細を表示できます。Astro アプリは 1 つで、ビルド時に各ページを静的な HyperText Markup Language (HTML) として生成します。ページは frontmatter で Drizzle オブジェクト リレーショナル マッピング (ORM) を通じてローカル SQLite データベースに直接クエリします。別のバックエンド アプリケーション プログラミング インターフェイス (API) やクライアント側 UI フレームワークはありません。再利用可能なデータアクセス ヘルパーは `src/lib/` にあり、インタラクティブな UI は標準のドキュメント オブジェクト モデル (DOM) API を使う小さな Astro `<script>` で追加します。

### Web サイトを起動する

サイトを簡単に実行できるよう、単一の Astro 開発サーバーを起動する `npm run dev` タスクが用意されています。GitHub Codespace で実行します。

1. Codespace に戻ります。前の演習後、`main` ブランチに戻っているはずです。
2. <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押し、Codespace 内に新しいターミナルを開きます。
3. フィルタリング作業用のブランチを作成し、切り替えます。

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. 次のコマンドで Web サイトを起動します。

   ```bash
   npm run dev
   ```

   Astro 開発サーバーの準備ができると、次のような URL を示すバナーが表示されます。

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> `http://localhost:4321` をブラウザーで開くダイアログが表示された場合は、**x** を選択して閉じます。

5. <kbd>Command</kbd> (Mac) または <kbd>Ctrl</kbd> (Windows/Linux) を押しながら、ターミナルに表示されている Astro サーバーのアドレス `http://localhost:4321` を選択して、Web サイトを開きます。

> [!NOTE]
> Codespace のターミナルで localhost URL を選択すると、`https://<your-codespace-name>-4321.app.github.dev/` に自動的に転送されます。これは Codespace 内で実行中の Web サーバーに接続するためのプライベート トンネルです。

### Web サイトを確認する

Web サイトを起動したら、機能を確認します。主な機能は次のとおりです。

- **ホームページ**: ボードゲームのタイトル、画像、説明を一覧表示します。
- **ゲーム詳細ページ**: ゲームを選択すると、タイトル、説明、パブリッシャー、カテゴリなどの詳細を表示します。

## Copilot でバックログを確認する

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を開始してください。モードとモデルの選択は演習によって異なるため、必要な箇所で説明します。

Web サイトの初期実装は動作しますが、機能を追加します。プロジェクト テンプレートから作成したときに、Issue のバックログも自動的に用意されています。GitHub Copilot に項目を表示させます。

1. Chat ビューのエージェント選択メニューで **Agent** を選びます。**Agent** は自律的に計画を立てて複数ファイルを変更し、ターミナル コマンドを実行し、ツールを呼び出します。

   ![Chat ビューのエージェント選択メニューを示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

2. 利用可能なモデルから **Claude Sonnet 4.5** を選択します。

> [!CAUTION]
> このワークショップの作成者が特定のモデルを推奨しているわけではありません。ワークショップでは Claude Sonnet 4.5 を使ったため、体験の一貫性を保ちやすいよう手順に記載しています。ただし、大規模言語モデル (LLM) は確率的に動作するため、提案内容が記載例と異なる場合があります。これは正常な動作です。

> [!NOTE]
> LLM の確率的な性質により、Copilot が別の MCP コマンドを使う場合がありますが、タスクは実行できます。

3. 次のプロンプトを Copilot に送信し、Issue のバックログを表示するよう依頼します。

   ```plaintext
   Please show me the backlog of items from my GitHub repository. Help me prioritize them based on those which will be most useful to the user.
   ```

4. **Continue** を選択して、すべての Issue を一覧表示するコマンドを実行します。
5. 生成された Issue の一覧を確認します。

Copilot がユーザーにとって役立つと思われる項目を優先順位付けします。

## 指示ファイルを確認する

コード生成をエージェントに依頼する前に、作業のコンテキストを伝える指示ファイルを確認します。[ユーザー インターフェイス (UI) の指示ファイル](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md)には、Web サイトの機能追加に関する指針が記載されています。

1. Codespace で `.github/instructions/ui.instructions.md` を開きます。
2. 機能を追加する方法についての全体的な指針を確認します。次の内容が含まれます。
   - アーキテクチャの概要。
   - コンポーネントの設計、テスト容易性、アクセシビリティの原則。
   - Astro、Tailwind CSS、Drizzle など、ファイルの種類別の指示へのリンク。

> [!TIP]
> 指示ファイルから、ほかの指示ファイルやプロジェクト内のファイルを参照できます。パスは指示ファイルを基準とします。指示を再利用したり、複雑な指示を小さなファイルに分けたり、Copilot に例やテンプレートを示したりできます。

## フィルタリング機能を実装する

フィルタリングを完成させるには、少なくとも次の 3 つの変更が必要です。

- データ層 (`src/lib/`) に残りのフィルタリング ロジックを追加または調整する。
- フィルタリングの動作を確認するテストを追加または更新する。
- ゲーム一覧ページにフィルタリング UI を追加する。

さらに、コードベースにマージする前にテストを実行し、成功させる必要があります。Copilot エージェントモードにこれらの作業を依頼します。

1. 現在の Copilot 会話を続けるか、**New Chat** を選択して新しい会話を開始します。
2. **Add Context**、**Instructions**、**ui** の順に選択して、指示ファイルを追加します。

   ![UI の指示ファイルを選択する例を示すスクリーンショット](../../../_images/ex3-select-instructions-file.png)

3. Chat ビューのエージェント選択メニューで **Agent** が選ばれていることを確認します。

   ![Chat ビューのエージェント選択メニューを示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

4. モデルとして **Claude Sonnet 4.5** が選択されていることを確認します。
5. バックログの関連 Issue に基づいて機能を実装するよう、次のプロンプトを送信します。

   ```plaintext
   Please update the site to include filtering by publisher and category based on the requirements from the related GitHub issue in the backlog. A publishers helper already exists from the previous exercise, so preserve and refine that work as needed while completing the remaining data-layer, UI, and tests. Ensure all tests are passing before completion. The server is already running, so you do not need to start it up.
   ```

6. Copilot がプロジェクトを調べ、機能に関係するデータ層、UI、テストのファイルを見つけて変更し、テストを実行する様子を確認します。

   ![Copilot がプロジェクト ファイルを調べる様子を示すスクリーンショット](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot はプロジェクトを調べ、ファイルを更新してテストを実行します。タスクやコードベースの複雑さによって数分かかる場合があります。作業中にコード エディターに **Keep** と **Undo** が表示される場合があります。完了時にはすべての変更に **Keep** または **Undo** が表示されるため、作業中に選択する必要はありません。

7. Copilot から求められたら、**Continue** を選択してテストを実行します。

   ![Copilot Chat でテスト実行の確認を求めるダイアログを示すスクリーンショット](../../../_images/ex3-agent-mode-run-tests.png)

8. 作業中に一時停止したり、テストが失敗したりすることがあります。Copilot はコード生成とテストを繰り返し、エラーを検出しなくなるまで対応します。

   ![Copilot エージェントモードの完了した Chat セッションを示すスクリーンショット](../../../_images/ex3-agent-mode-proposed-changes.png)

9. 生成されたコードに問題がないか確認します。

> [!CAUTION]
> Copilot などの AI ツールが生成したコードは必ずレビューしてください。

10. Web サイトを起動しているブラウザーに戻り、新しい機能を試します。
11. 動作とコードの確認が終わったら、Copilot Chat ウィンドウで **Keep** を選択します。

## まとめと次のステップ

GitHub Copilot エージェントモードで Tailspin Toys の Web サイトに機能を追加する方法を確認しました。この演習では、次の方法を学びました。

- GitHub Copilot エージェントモードで UI とデータ層にまたがる機能を実装する。
- Copilot エージェントモードでプロジェクトを調査し、関連ファイルを特定して、連携した変更を行う。
- コードベースにマージする前に、Copilot エージェントモードが生成した変更とテストをレビューする。

次は [Playwright MCP サーバーで機能をテストし][next-lesson]、プルリクエストを作成します。

### 追加の演習 — ページネーションを実装する

ゲーム一覧が増えるにつれて、ページネーションが必要になります。この演習で学んだことを活かし、Copilot にページネーションの実装を依頼します。次の点を検討してください。

- 既存の指示ファイルを使うなど、現在のベストプラクティスに従う。
- ユーザーがページ サイズを選べるようにするか、固定するかなど、ページネーションの仕様を決める。
- 希望する実装に必要な指針をプロンプトに含める。
- Copilot に追加の変更を依頼し、コンテキストを与えて反復する。Copilot を使う際の通常の作業方法です。

## リソース

- [Copilot の Ask、Edit、Agent モード: 機能と使い分け][choose-mode]
- [VS Code のエージェントモード][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode

---
title: "レッスン 1 - GitHub Copilot CLI をインストールする"
description: "Codespace に Copilot CLI をインストールして認証し、基本操作を確認して、あらかじめ用意されたフィルタリングの Issue を見つけます。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

[GitHub Copilot CLI][about-copilot-cli] は、ターミナルで動作するエージェント型コーディング アシスタントです。コードベースを調べ、コードを生成し、コマンドを実行し、外部ツールを利用できます。ターミナルを離れずにタスクを委任し、変更を依頼しながら作業を続けられます。最初に、使い慣れたツールを使って Copilot CLI をインストールします。

このレッスンでは、次の作業を行います。

- npm を使って GitHub Copilot CLI をインストールします。
- GitHub アカウントで認証します。
- ワークショップのリポジトリを信頼して、Copilot との会話を試します。
- 組み込みの GitHub MCP サーバーを使ってフィルタリングの Issue を見つけます。

## シナリオ

チームでは、増え続けるバックログに取り組むため、AI エージェントを使い始めています。Copilot CLI を使うと、多くの開発者が日常的に作業するターミナルでその機能を利用できます。このレッスンではインストールと認証を済ませ、ワークショップの残りで使えるようにします。

## Copilot CLI をインストールする

Copilot CLI は [npm][install-cli]、WinGet、Homebrew でインストールできます。GitHub Codespaces には Node.js があらかじめインストールされているため、この演習では npm を使います。

1. Codespace に戻り、ターミナルを開きます。
2. Node.js がインストールされ、必要なバージョンを満たしていることを確認します。

   ```bash
   node --version
   ```

   バージョン 24 以上が表示されます。

3. Copilot CLI をグローバルにインストールします。

   ```bash
   npm install -g @github/copilot
   ```

4. インストールを確認します。

   ```bash
   copilot --version
   ```

   バージョン番号が表示されます。

## GitHub で認証する

初めて起動すると、Copilot CLI は GitHub アカウントでの認証を求めます。

1. Copilot CLI を起動します。

   ```bash
   copilot
   ```

2. 認証を求められたら、device code の手順に従って認証し、Copilot CLI に許可を与えます。
3. Copilot CLI に次のプロンプトが表示されます。

   ```plaintext
   Copilot can read files in this folder and, with your permission, edit them or run code and shell commands. It will remember your permissions for the rest of this session.

   Do you trust the files in this folder?
   ```

4. 表示されたパスが Tailspin Toys リポジトリであることを確認し、**Yes, and remember this folder for future sessions** を選択します。

> [!NOTE]
> Codespace では、GitHub セッションを通じてすでに認証されている場合があります。Copilot CLI が認証を求めずに起動した場合は、そのまま進めてください。

## 基本操作を確認する

通常のシェル プロンプトで入力したコマンドは、Codespace で実行されます。Copilot CLI の起動後は、自然言語の入力がエージェントに送信され、スラッシュ コマンドで会話を操作します。

1. `/model` を実行し、矢印キーで **Auto** を選び、<kbd>Enter</kbd> を押して確定します。
2. `/help` を実行し、インストール済みバージョンで利用できるコマンドを確認してから、<kbd>Esc</kbd> を押してヘルプ画面を閉じます。
3. Copilot に簡単な質問を送り、動作を確認します。

   ```plaintext
   What are the key files in this project?
   ```

4. 回答を読み、Copilot がリポジトリを調べてから答えていることを確認します。
5. `/mcp list` を実行し、組み込みの GitHub Model Context Protocol (MCP) サーバーが利用可能であることを確認します。
6. 次のプロンプトでフィルタリングの Issue を探します。

   ```plaintext
   Using GitHub MCP, find the issue in this repository titled "Allow users to filter games by category and publisher." Give me its URL and a short summary. Don't change anything.
   ```

7. URL を開いて Issue を読みます。最初の変更を行った後に、この Issue を使います。

> [!TIP]
> 通常の Copilot CLI セッションは、ターミナルで現在チェックアウトされているブランチを使い、自動では worktree を作成しません。各変更の前に機能ブランチを作成します。

## ワークショップ用のショートカットを使う

通常、Copilot CLI は許可されている範囲外のツールを使う前に確認を求めます。このワークショップでは Codespace 内の承認プロンプトを省略し、演習に集中できるよう、ユーザーが承認したショートカット `--yolo` を付けて再起動します。

> [!CAUTION]
> `--yolo` を指定すると、完全な自動権限 (`--allow-all-tools`、`--allow-all-paths`、`--allow-all-urls`) が有効になります。Codespace や VM などの分離された環境でのみ使用し、日常的な開発の既定値として alias に設定しないでください。詳しくは[ツールの使用を許可または禁止する方法][allow-all-warning]を確認してください。

このワークショップでは `--enable-all-github-mcp-tools` も指定します。後のレッスンで Issue やプルリクエストを操作するため、GitHub MCP の読み取りおよび書き込みツールが有効になります。Codespace によってローカル コンピューターへのアクセスは制限されますが、認証済みの GitHub リソースには引き続きアクセスできます。公開やマージの前に変更をレビューしてください。

1. `/exit` で Copilot CLI を終了します。
2. リポジトリのルートから、次のコマンドで再起動します。

   ```bash
   copilot --yolo --enable-all-github-mcp-tools
   ```

3. プロジェクトについてもう 1 つ質問して会話が動作することを確認し、`/exit` で終了します。

Copilot は会話を自動保存します。後で指示を変更したりエージェントを追加したりした後、`copilot --resume` を使って同じ機能の会話とブランチに戻ります。

## まとめと次のステップ

GitHub Copilot CLI をインストールして認証しました。このレッスンでは、次の作業を行いました。

- npm を使って GitHub Copilot CLI をインストールしました。
- GitHub アカウントで認証しました。
- ワークショップのリポジトリを信頼して、Copilot との会話を試しました。
- 組み込みの GitHub MCP サーバーを使ってフィルタリングの Issue を見つけました。

Copilot CLI の準備ができました。次は[最初の変更][next-lesson]に取り組み、ゲームカードに星評価を表示します。

## リソース

- [GitHub Copilot CLI をインストールする][install-cli]
- [GitHub Copilot CLI について][about-copilot-cli]
- [Copilot CLI コマンド リファレンス][cli-reference]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-add-star-rating/
[install-cli]: https://docs.github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli
[about-copilot-cli]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[allow-all-warning]: https://docs.github.com/copilot/concepts/agents/about-copilot-cli#allowing-and-denying-tool-use

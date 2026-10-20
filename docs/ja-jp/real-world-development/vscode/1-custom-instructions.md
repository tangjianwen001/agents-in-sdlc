---
title: "演習 1 - カスタム命令 (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: エージェントモード →][next-lesson]

生成 AI を使う際には、コンテキストが重要です。タスクを特定の方法で実行する必要がある場合や、Copilot が知っておくべき背景情報がある場合は、その情報を利用できるようにします。Copilot に特定のコンテキストを伝える方法はいくつかあります。コード生成の指針を示す[指示ファイル][instruction-files]がその 1 つです。

この演習では、次の作業を行います。

- リポジトリのカスタム命令とパス別の指示ファイルで、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準が Copilot に伝わる方法を確認します。
- 現在の指示を使い、フィルタリング用のデータ スライスの最初の部分 (パブリッシャー ヘルパー) を生成します。
- `.github/copilot-instructions.md` にリポジトリ全体の新しい標準を追加します。
- 同じプロンプトを再実行し、生成コードが新しい標準を採用する様子を確認します。
- 指示の更新とフィルタリングの一部を `main` にコミットし、次の演習で Copilot が新しい指針を使えるようにします。

> [!CAUTION]
> 生成されたコードが、設定した標準の一部に沿わない場合があります。Copilot は確率的に動作します。この演習では、出力の文字列を完全一致させるのではなく、指示を更新した後に動作が改善する傾向を確認します。

## 指示ファイル

### シナリオ

Tailspin Toys には、開発プラクティスに関するガイドラインと要件があります。

- データ層には常に単体テストが必要です。
- UI はダーク モードにし、現代的なデザインにします。
- TSDoc ドキュメント コメントでコードを説明します。
- 各ファイルの先頭に、そのファイルの役割を説明するコメント ブロックを追加します。

指示ファイルを使い、Copilot がこれらのプラクティスに沿って作業できるようにします。

### カスタム命令

カスタム命令は、コーディング スタイルや要件を Copilot に伝えるコンテキストや設定です。コーディング規約、ライブラリ、コメントの形式などを指定できます。リポジトリ全体、または特定のファイルを対象に設定できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリに対する**すべての**要求で Copilot に送信されます。技術スタック、構築するものの概要、ベストプラクティスなど、ほとんどのチャットや CLI 要求に関係するプロジェクト情報を記述します。
- `.github/instructions/*.instructions.md` は、特定のタスクやファイルの種類に対応します。TypeScript や Astro などの言語、UI コンポーネントや単体テストなどのタスクに関する指針を設定できます。

> [!NOTE]
> IDE では、指示ファイルは Copilot Chat のコード生成にのみ使われ、コード補完や次の編集候補には使われません。
>
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成時にリポジトリ レベルの指示と `applyTo` frontmatter を持つ `*.instructions.md` ファイルの両方を使います。
>
> Copilot は AGENTS.md や CLAUDE.md など、ほかの標準形式の指示ファイルにも対応しています。詳細は[カスタム命令のサポート][custom-instructions-support]を確認してください。

### 指示ファイルを管理するためのベストプラクティス

指示ファイルの作成方法をすべて説明することは、このワークショップの範囲外です。サンプル プロジェクトのファイルを例として、主な考え方を確認します。

- `copilot-instructions.md` には、構築中のもの、プロジェクト構成、全体的なコーディング標準など、プロジェクト レベルの指針を記述します。
- `*.instructions.md` は、単体テスト、Astro コンポーネント、データ層などのファイルの種類、または特定のタスクを対象にします。
- 自然な言葉で、明確な指針とコードの良い例、悪い例を記述します。

指示ファイルにも AI の使い方にも、唯一の正解はありません。試しながらプロジェクトに適した方法を見つけます。

> [!TIP]
> GitHub Copilot を使うプロジェクトには、充実した指示ファイルを用意することをお勧めします。このプロジェクトでは [UI の更新][ui-instructions]や [Astro][astro-instructions]など、さまざまなタスク向けの指示を確認できます。
>
> Copilot に指示ファイルを生成させることもできます。利用する環境によって方法は異なります。たとえば VS Code では **Configure Chat → Generate Agent Instructions**、Copilot CLI では `/init` を使います。必要な場合は、その環境のレッスンで説明します。
>
> テンプレートや出発点を探す場合は、指示ファイル、カスタムエージェント、ほかのリソースをまとめた [awesome-copilot][awesome-copilot]を確認してください。

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## プロジェクトのカスタム命令ファイルを確認する

スターター プロジェクトに含まれる指示ファイルを確認し、変更前の動作を把握します。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの概要と **Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** 内の **GitHub Actions Workflows** の指針も確認します。これらは Copilot とのすべてのやり取りに適用されます。
3. `.github/instructions` フォルダーを開き、Astro ファイル、Drizzle データ層、テストなどの指示を確認します。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドは、リポジトリのルートを基準に適用対象を決める glob です。たとえば `**/*.test.ts` に一致する TypeScript テスト ファイルに適用されます。
5. このプロジェクトで単体テストを作成するときの指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、他の指示ファイル (`unit-tests.instructions.md` など) やプロジェクト ファイルへのリンクを確認します。指示を再利用可能な小さなファイルに分け、コード生成時に参考にする例を Copilot に示せます。ここにあるパスは指示ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはコーディング標準が記載されていますが、コード内ドキュメントについてはまだ定められていません。次の手順で TSDoc コメントとファイルのコメント ヘッダーを追加します。

## 変更用のブランチを作成する

変更を加える前に、ブランチを作成します。

1. 前の演習で使った Codespace に戻ります。
2. <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
3. 次のコマンドでブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions
   ```

## 命令を更新する前に Copilot Chat を使う

> [!TIP]
> **Copilot Chat を開く**
>
> 以下の演習を始める前に、Codespace に戻り、Copilot Chat パネルを開いて **New Chat** を選択し、新しい会話を開始します。モードとモデルの選択は演習によって異なるため、必要な箇所で説明します。

カスタム命令の影響を確認するため、まず現在の指示ファイルを使ってプロンプトを送ります。後で指示ファイルを更新し、同じプロンプトを再送信して違いを確認します。

1. 前の演習で使ったエディター タブを閉じ、Copilot が必要なコンテキストだけを取得するようにします。
2. `src/lib/publishers.ts` を開き、ヘルパーの配置場所を Copilot に示します。
3. Chat ビューのエージェント選択メニューで **Agent** を選び、ファイルを変更できるようにします。

   ![Chat ビューのエージェント選択メニューを示すスクリーンショット。](../../../_images/shared-chat-mode-selector.png)

4. 次のプロンプトを送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

5. Copilot がプロジェクトを調べ、ヘルパー ファイルとテストなどのコードを更新する様子を確認します。
6. 提案されたヘルパーが `db` クライアントを最初の引数に取り、型付きのパブリッシャー配列を返す関数になっていることを確認します。これは `src/lib/*.ts` に適用される `.github/instructions/drizzle.instructions.md` のデータ層の規約によるものです。
7. 提案されたコードには TSDoc ドキュメント コメントとファイル レベルのコメント ヘッダーが**ない**ことを確認します。

> [!CAUTION]
> Copilot は確率的に動作するため、指示がなくてもドキュメント コメントを追加する場合があります。その場合も問題ありません。指示を更新した後に動作が一貫することが、この演習の目的です。

## リポジトリ全体の標準を追加する

`.github/copilot-instructions.md` は、プロジェクト全体の情報を Copilot に伝えるファイルです。コード提案を改善するため、コーディング標準を追加します。

1. `.github/copilot-instructions.md` を再度開きます。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。コード内ドキュメントの規則はまだなく、そのため生成されたヘルパーにコメントがなかったことを確認します。
3. 既存の標準の直後に、次の行を追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 指示ファイルは、リポジトリ全体 (`.github/copilot-instructions.md`) または特定の言語、ファイルの種類、タスク (`*.instructions.md`) に対して作成できます。今回追加したドキュメント コメントの規則など、プロジェクト全体の標準にはリポジトリ レベルのファイルを使います。

## プロンプトを再実行して変化を確認する

指示を更新した後、同じプロンプトを再度送り、提案内容を比較します。

1. Copilot Chat に戻り、**New Chat** を選択して会話を新しくします。
2. `src/lib/publishers.ts` を再度開き、Copilot が対象ファイルを認識できるようにします。
3. 先ほどと**同じプロンプト**を送信します。

   ```plaintext
   Create or update src/lib/publishers.ts with a data-access helper that returns a list of all publishers with the name and id for each. Apply the file changes.
   ```

4. 提案ファイルの先頭に、次のようなコメント ブロックがあることを確認します。

   ```typescript
   /**
    * Publisher data-access helpers for the Tailspin Toys Crowd Funding platform.
    * Provides functions to retrieve publisher information from the database.
    */
   ```

5. 提案された関数に、次のような TSDoc コメントが含まれていることを確認します。

   ```typescript
   /**
    * Returns a list of all publishers with their id and name.
    *
    * @param db - The Drizzle database client.
    * @returns A promise that resolves to an array of publisher objects.
    */
   ```

Copilot に新しいプロジェクト標準を適用させ、次の演習で使うコードに反映しました。

## 指示とブランチをコミットしてプッシュする

指示ファイルもリポジトリ内のほかのファイルと同じように、ソース管理で管理します。指示の変更とヘルパーの基盤をコミットして、ブランチをプッシュします。

1. <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押して、新しいターミナルを開きます。
2. 次のコマンドで指示の更新とヘルパーの変更を確認します。

   ```bash
   git status
   ```

3. 指示の更新と、Copilot がヘルパーの基盤として変更したすべてのファイルをステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # git status にテストなどの追加変更が表示された場合は、それらのファイルも追加します。
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. 次のコマンドでリポジトリにブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions
   ```

## プルリクエストを作成してマージする

ブランチをプッシュしたら、バックログにあるドキュメント標準の Issue に関連付けた PR を作成します。プロジェクトに接続済みの GitHub ツールを使って、Copilot に Issue の検索、PR の作成、マージを依頼します。

> [!NOTE]
> この演習では、Copilot が初めて GitHub 上の操作を行います。プロジェクト テンプレートでは GitHub ツールがワークスペースに接続済みなので、セットアップは不要です。初回使用時に VS Code から GitHub へのサインインを求められる場合があります。画面の指示に従って許可してください。この接続で使われる Model Context Protocol については、後の演習で説明します。

1. Codespace 内で Copilot Chat を開きます。
2. <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) を押して Copilot Chat ビューを開き、エージェント選択メニューで **Agent** が選ばれていることを確認します。
3. 指示ファイル更新の Issue を検索し、現在のブランチから PR を作成するよう依頼します。PR の説明には、指示の更新と今後のフィルタリング作業用に追加したパブリッシャー ヘルパーの基盤の両方を含めます。

   ```plaintext
   Find the issue related to updating the instructions file. Create a new PR from the current branch, highlight that the PR closes that issue, and include that we also added the publishers helper foundation for the upcoming filtering work.
   ```

4. Copilot が Issue を探し、PR を作成する様子を確認します。
5. GitHub 操作の **Allow** を求められたら、コマンドを確認して適切な操作を許可します。
6. PR が作成されたら、次のプロンプトで PR のマージを依頼し、ローカルのブランチを `main` に戻して最新のコードを取得します。

   ```plaintext
   Merge the PR into main. Then return to main locally, and pull the latest code so we are up to date.
   ```

7. GitHub 操作やシェル コマンドの **Allow** を求められたら、コマンドを確認して適切な操作を許可します。

これで GitHub Copilot の支援を受けて PR を作成し、マージできました。

## まとめと次のステップ

このプロジェクトで Copilot が指示ファイルからコンテキストを取得する方法を確認し、VS Code の Copilot Chat で次の作業を行いました。

- 現在の指示を使ったコード生成プロンプトを送り、Copilot の提案を確認しました。
- `.github/copilot-instructions.md` にリポジトリ全体の標準を追加しました。
- 同じプロンプトを再送信し、提案コードが新しい標準に沿うことを確認しました。
- 指示の更新とヘルパーの基盤を `main` にコミットし、次の演習で使えるようにしました。

次は、Copilot がコードベース全体に機能を追加する[エージェントモード][next-lesson]で、指示を活用します。

## リソース

- [GitHub Copilot のカスタマイズ用指示ファイル][instruction-files]
- [カスタム命令を作成するためのベストプラクティス][instructions-best-practices]
- [Copilot のカスタム命令を改善する 5 つのヒント][copilot-instructions-five-tips]
- [GitHub Copilot の個人用カスタム命令][personal-instructions]
- [Awesome Copilot — 指示ファイルなどのコレクション][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/

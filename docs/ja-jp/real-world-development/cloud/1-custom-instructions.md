---
title: "演習 1 - カスタム命令 (Cloud agent)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← 前のレッスン: 前提条件][previous-lesson] · [次のレッスン: Copilot cloud agent →][next-lesson]

生成 AI を使う際には、コンテキストが重要です。タスクを特定の方法で実行する必要がある場合や、Copilot が知っておくべき背景情報がある場合は、その情報を利用できるようにします。[指示ファイル][instruction-files]を使うと、Copilot に何を実装するかだけでなく、どのように実装するかも伝えられます。

この演習では、次の作業を行います。

- リポジトリのカスタム命令とパス別の指示ファイルを使って、プロジェクト固有のコンテキスト、コーディング ガイドライン、ドキュメント標準が Copilot に伝わる方法を確認します。
- `.github/copilot-instructions.md` にリポジトリ全体の新しい標準を追加します。

> [!NOTE]
> VS Code や CLI の操作環境と異なり、ここでは命令の効果を確認するための変更前後のプロンプトを実行しません。Copilot cloud agent は GitHub Issue に対して非同期に動作するため、リアルタイムで並べて比較しにくいからです。指示ファイルの影響は、後の演習で cloud agent が作成した PR をレビューするときに確認できます。

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
> Copilot Chat、Copilot CLI、Copilot cloud agent は、コード生成時にリポジトリ レベルの指示と、`applyTo` frontmatter を持つ `*.instructions.md` ファイルの両方を使います。
>
> Copilot は AGENTS.md や CLAUDE.md など、ほかの標準形式の指示ファイルにも対応しています。詳細は[カスタム命令のサポート][custom-instructions-support]を確認してください。

### 指示ファイルを管理するためのベストプラクティス

指示ファイルの作成方法をすべて説明することは、このワークショップの範囲外です。サンプル プロジェクトのファイルを例として、主な考え方を確認します。

- `copilot-instructions.md` には、構築中のもの、プロジェクト構成、全体的なコーディング標準などのプロジェクト レベルの指針を記述します。
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

このリポジトリの指示ファイルを開いて、プロジェクトの構成と Copilot への指針を確認します。

1. `.github/copilot-instructions.md` を開きます。
2. プロジェクトの概要と **Agent notes**、**Code standards**、**Scripts**、**Repository Structure** などのセクションを確認します。**Code standards** 内にある **GitHub Actions Workflows** の指針も確認します。これらは Copilot とのすべてのやり取りに適用されます。
3. `.github/instructions` フォルダーを開き、Astro ファイル、Drizzle データ層、テストなどの指示を確認します。
4. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドは、リポジトリのルートを基準に適用対象を決める glob です。たとえば `**/*.test.ts` に一致する TypeScript テスト ファイルに適用されます。
5. このプロジェクトで単体テストを作成するときの指示を確認します。
6. `.github/instructions/drizzle.instructions.md` を開き、末尾にある他の指示ファイル (`unit-tests.instructions.md` など) やプロジェクト ファイルへのリンクを確認します。パスはリポジトリのルートではなく、指示ファイルからの相対パスです。

> [!NOTE]
> `copilot-instructions.md` の **Code formatting requirements** セクションにはコーディング標準が記載されていますが、コード内ドキュメントについてはまだ定められていません。次の手順で TSDoc コメントとファイルのコメント ヘッダーを追加します。

## リポジトリ全体の標準を追加する

この演習で編集するのは、プロジェクト全体のドキュメント標準を記述するファイルです。後の演習で Issue を割り当てるとき、cloud agent がこの標準を読み取ります。

編集する前にブランチを作成します。cloud agent は Issue の対象ブランチから指示を読み込みますが、ここでは変更を個別にコミットします。

1. Codespace のターミナルで、次のコマンドを実行してブランチを作成し、切り替えます。

   ```bash
   git checkout -b custom-instructions-cloud
   ```

`.github/copilot-instructions.md` にプロジェクト レベルの情報を記述し、コード提案のための標準を追加します。

1. `.github/copilot-instructions.md` を開きます。
2. 27 行目付近にある **Code formatting requirements** セクションを探します。現在はプロジェクトのコーディング標準が記載されていますが、コード内ドキュメントの規則はありません。
3. 既存の標準の直後に、次の Markdown 行を追加します。

   ```markdown
   - Every exported function should have a TSDoc comment describing its purpose, parameters, and return value.
   - Before imports or any code, add a comment block to the file that explains its purpose.
   ```

4. `copilot-instructions.md` を保存します。

> [!TIP]
> 指示ファイルは、リポジトリ全体 (`.github/copilot-instructions.md`) または特定の言語、ファイルの種類、タスク (`*.instructions.md`) に対して作成できます。今回追加したドキュメント コメントの規則など、プロジェクト全体の標準にはリポジトリ レベルのファイルを使います。

## 指示をコミットし、プッシュしてマージする

Cloud agent は、Issue の対象ブランチにある指示ファイルを読み取ります。次の演習で Issue を割り当てると、Copilot は `main` からブランチを作成します。そのため、指示の変更を `main` に反映しておきます。

1. 変更をステージしてコミットします。

   ```bash
   git add .github/copilot-instructions.md
   git commit -m "Add doc comment and file-header standards to copilot instructions"
   ```

2. ブランチをプッシュします。

   ```bash
   git push -u origin custom-instructions-cloud
   ```

3. github.com で `custom-instructions-cloud` から `main` へのプルリクエストを開き、マージします。次の演習で Issue を割り当てるとき、cloud agent がこれらの指示を読み取ります。

> [!TIP]
> このワークショップで `main` に直接作業する場合は、ブランチを作成せず、変更を `main` にコミットしてもかまいません。ここでは実際のプロジェクトに近い手順にするため、ブランチを使います。

## まとめと次のステップ

このプロジェクトで Copilot が指示ファイルからコンテキストを取得する方法を確認し、`.github/copilot-instructions.md` にリポジトリ全体の標準を追加しました。この標準が、この操作環境で cloud agent が作成する PR に反映されます。

次は、[最初の Issue を Copilot cloud agent に割り当てます][next-lesson]。

## リソース

- [GitHub Copilot のカスタマイズ用指示ファイル][instruction-files]
- [カスタム命令を作成するためのベストプラクティス][instructions-best-practices]
- [Copilot のカスタム命令を改善する 5 つのヒント][copilot-instructions-five-tips]
- [Awesome Copilot — 指示ファイルなどのコレクション][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-cloud-agent/
[instruction-files]: https://docs.github.com/copilot/customizing-copilot/about-customizing-github-copilot-chat-responses
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/

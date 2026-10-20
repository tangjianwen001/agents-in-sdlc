---
title: "レッスン 4 - カスタム命令で Copilot を導く"
description: "リポジトリの指示を確認し、ドキュメント作成の標準を追加して、フィルタリングのコードに適用します。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

生成 AI を使うときは、コンテキストが重要です。タスクを特定の方法で実行する必要がある場合、その指針を Copilot が参照できるようにします。[指示ファイル][instruction-files]には、求めるコードだけでなく、コードの構成方法も記述できます。フィルタリングを実装したので、Copilot が使った指示を確認し、ドキュメント作成の標準を追加して、コードに適用します。

このレッスンでは、次の作業を行います。

- リポジトリの指示とパス別の指示ファイルがエージェントにどう適用されるかを確認します。
- 指示ファイルを更新して、コーディング標準が守られるようにします。
- 指示ファイルがコードに与える影響を確認します。

## シナリオ

Tailspin Toys には、開発に関するガイドラインと要件があります。たとえば、次のとおりです。

- コメントには、コードを言い換えるのではなく、意図や自明でない判断を記述します。
- `db/` と `src/lib/` のエクスポートされた関数には、存在する場合は注入可能な `db` 引数も含め、目的、パラメーター、戻り値を TypeScript Documentation (TSDoc) または JavaScript Documentation (JSDoc) で記述します。
- 再利用可能な Astro コンポーネントには `Props` の契約を記述し、関連コードの変更時にはコメントも最新の状態に保ちます。
- 既存のフォーマットと lint の指針を維持します。

指示ファイルを使って、Copilot がこれらのプラクティスに沿って作業できるようにします。

## 指示ファイル

カスタム命令を使うと、コーディング スタイルや要件を Copilot に伝えるコンテキストや設定を指定できます。コーディング規約、ライブラリ、コメントの形式などを指定できるため、より適切な提案やコード スニペットを得やすくなります。リポジトリ全体、または特定の種類のファイルを対象に命令を作成できます。

指示ファイルには次の 2 種類があります。

- `.github/copilot-instructions.md` は、リポジトリに対する**すべての**要求で Copilot に送信される単一の指示ファイルです。ほとんどの要求に関係するプロジェクト レベルの情報を記述します。
- `.github/instructions/*.instructions.md` は、特定の言語、ファイルの種類、タスクに関する指針を記述します。

> [!NOTE]
> その他の指示形式とサポート状況は、環境によって異なります。特定の形式を前提にする前に、[カスタム命令のサポート リファレンス][custom-instructions-support]を確認してください。

## プロジェクトのカスタム命令ファイルを確認する

スターター プロジェクトには、いくつかの指示ファイルが用意されています。変更の影響を確認できるよう、まず既存の内容を調べます。

1. Codespace に戻ります。
2. Codespaces エディターで、ターミナルではなく `.github/copilot-instructions.md` を開きます。
3. プロジェクトの簡単な説明とコーディング ガイダンスを確認します。これらの指示は、このリポジトリで Copilot と行うすべてのやり取りに適用されます。
4. `.github/instructions` フォルダーを開いて、ファイルを確認します。Astro ファイル、Drizzle データ層、テストなどの指示があります。
5. `.github/instructions/unit-tests.instructions.md` を開きます。先頭の `applyTo` フィールドで、指示の適用対象となるファイルを決める glob が指定されています。
6. `.github/instructions/drizzle.instructions.md` を開き、ほかの指示ファイルや既存のプロジェクト ファイルへのリンクを確認します。大きな指示を再利用可能な小さなファイルに分け、Copilot に参考となる例を示せます。

## チームのガイダンスに合わせて指示ファイルを更新する

既存のファイルはよい出発点ですが、生成する TypeScript に TSDoc コメントを追加するための標準がまだありません。コアとなる `copilot-instructions.md` ファイルを更新します。

1. `.github/copilot-instructions.md` で、**Code formatting guidance** という見出しのセクションを探します。35 行目付近にあります。
2. 最後の箇条書きとして、次の行を追加します。

   ```markdown
   - All new TypeScript should contain TSDocs comments for documentation purposes.
   ```

   ファイルは自動的に保存されます。

## 更新したガイダンスを使う

Copilot CLI は会話の開始時にリポジトリの指示を読み込みます。機能のコンテキストを保ちながら新しいガイダンスを読み込めるよう、指示ファイルを保存した後、フィルタリングの会話を終了して再開します。

1. フィルタリングの会話がまだ開いている場合は、`/exit` を入力して終了します。Copilot CLI は会話を自動的に保存します。
2. リポジトリのルートに移動し、`game-filters-cli` ブランチにいることを確認してから、保存した会話を再開します。

   ```bash
   copilot --resume
   ```

   選択を求められたら、フィルタリングの会話を選び、`game-filters-cli` ブランチがチェックアウトされていることを確認します。

3. 次のプロンプトで、更新したガイダンスを適用するよう Copilot に依頼します。

   ```plaintext
   We just updated our instructions and code guidance. Can you please update the code you generated to match that guidance?
   ```

4. `/diff` を入力し、変更された TypeScript ファイルを確認します。新しい TSDoc コメントが追加され、コードを正確に説明していることを確認します。

## まとめと次のステップ

Copilot CLI が指示ファイルからコンテキストを取得する方法を確認し、新しい標準を機能に適用しました。具体的には、次の作業を行いました。

- リポジトリの指示とパス別の指示ファイルがエージェントにどう適用されるかを確認しました。
- 指示ファイルを更新して、コーディング標準が守られるようにしました。
- 指示ファイルがコードに与える影響を確認しました。

次は、再利用可能な quality-checks スキルを[カスタマイズして実行し][next-lesson]、lint とテストを一貫して実行できるようにします。

## リソース

- [Copilot CLI 用のカスタム命令を追加する][instruction-files]
- [カスタム命令のサポート][custom-instructions-support]
- [カスタム命令を作成するためのベスト プラクティス][instructions-best-practices]
- [Awesome Copilot — 指示ファイルなどのコレクション][awesome-copilot]

[instruction-files]: https://docs.github.com/copilot/how-tos/copilot-cli/customize-copilot/add-custom-instructions
[instructions-best-practices]: https://docs.github.com/copilot/concepts/prompting/response-customization#writing-effective-custom-instructions
[awesome-copilot]: https://awesome-copilot.github.com/
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support
[previous-lesson]: ../3-agent-modes/
[next-lesson]: ../5-agent-skills/

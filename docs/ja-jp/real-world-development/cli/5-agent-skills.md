---
title: "レッスン 5 - quality-checks スキルをカスタマイズして使う"
description: "既存の quality-checks スキルを確認し、レポート形式をカスタマイズして、フィルタリング機能を検証します。"
authors:
  - geektrainer
lastUpdated: 2026-09-29
---

コードを書くこと以外にも、開発にはさまざまな作業があります。これまでにコードを手動で検証し、指示ファイルを使って標準に沿うようにしました。テストや lint、ほかの継続的インテグレーション (CI) 作業はどうでしょうか。

このような作業には**エージェント スキル**が適しています。スキルを使うと、Copilot に操作の実行方法を伝えられます。

このレッスンでは、次の作業を行います。

- 既存の `quality-checks` スキルを確認します。
- 結果の形式をカスタマイズします。
- スキルを再読み込みして実行します。

## シナリオ

Tailspin Toys では、単体テスト、lint、型チェックに `quality-checks` スキルを使っています。チームは、結果が読みやすくなるようレポートを改善したいと考えています。

## 指示、スクリプト、リソース

エージェント スキルには、再利用できるタスクの指示、実行可能なスクリプト、補足リソースがまとめられており、エージェントは必要に応じて読み込みます。基本的には、スキル名のフォルダーと、その中にある `SKILL.md` で構成されます。Markdown ファイルには `name` と `description` の frontmatter、スキルの概要、呼び出すタイミングの指針が記載されています。スキルには、実行時に使うスクリプトやほかのリソースを含むサブフォルダーを追加することもできます。

> [!NOTE]
> スキルに追加のフォルダーやファイルは必須ではありません。Tailspin Toys の `quality-checks` スキルには `SKILL.md` のみが含まれ、既存のプロジェクト コマンドを使います。

スキルは、チームで共有、再利用できるリポジトリ内の `.github/skills` フォルダー、またはユーザーの `~/.copilot/skills` フォルダーに配置できます。

## スキルを確認する

Tailspin Toys チームが単体テスト、lint、型チェックのために作成した `quality-checks` スキルを確認します。

1. Codespace に戻ります。Codespaces エディターで `.github/skills/quality-checks/SKILL.md` を開きます。
2. 先頭にある `name` と `description` を読みます。説明によって、Copilot はスキルを呼び出すタイミングを判断します。
3. 指示を読み、テストと lint の手順がどう示されているかを確認します。
4. まだ **Results output formatting** セクションがないことを確認します。

## 変更前にスキルを実行する

Copilot に 3 種類のチェックを実行させ、変更前のレポートを確認します。

1. Interactive モードのフィルタリング会話に戻ります。
2. 次のプロンプトを送信します。

   ```plaintext
   Run the quality-checks skill for unit tests, lint, and type checks.
   ```

3. 最後に表示されたレポートを確認します。

## レポートをカスタマイズする

実行した内容、成功したかどうか、ツールが出力した情報が分かるようにレポートを改善します。

1. `.github/skills/quality-checks/SKILL.md` を開きます。
2. ファイルの末尾に、次のセクションを追加します。

   ```markdown
   ## Results output formatting

   Upon completion, report each command that ran and whether it passed, failed, or was blocked. Include test counts, durations, errors, warnings, and other metrics only when the tool reports them. Identify the next action for any failure or blocker, and never describe a skipped or incomplete check as passed.
   ```

3. ファイルを保存します。変更は自動的に保存されます。

## 更新したスキルを実行する

スキルの編集を反映して実行し、最初のレポートと比較します。Copilot CLI は会話を再起動せずに編集済みスキルを再読み込みできます。

1. 次のコマンドを実行します。

   ```plaintext
   /skills reload
   ```

2. 先ほどと同じプロンプトを送信します。

   ```plaintext
   Run the quality-checks skill for unit tests, lint, and type checks.
   ```

3. 最後のレポートを確認し、最初の結果と比較します。

## まとめと次のステップ

既存のエージェント スキルをカスタマイズして実行しました。このレッスンでは、次の作業を行いました。

- 単体テスト、lint、型チェックを行う `quality-checks` スキルを確認しました。
- 結果の形式をカスタマイズしました。
- スキルを再読み込みして実行しました。

この変更はフィルタリングの機能 PR に含まれます。次は [Playwright MCP サーバー][next-lesson]を使って、Copilot にサイトを直接操作させます。

## スキルの追加例

コミュニティの次の例は参考資料であり、追加の作業ではありません。

- [Agent Skills 仕様][skill-spec]
- [貢献ワークフロー: `make-repo-contribution`][contribution-example]
- [要件ドキュメント: `prd`][prd-example]
- [図と同梱のエクスポート スクリプト: `drawio`][drawio-example]
- [ブラウザー テスト: `webapp-testing`][browser-example]

[previous-lesson]: ../4-custom-instructions/
[next-lesson]: ../6-mcp-playwright/
[skill-spec]: https://agentskills.io/specification
[contribution-example]: https://github.com/github/awesome-copilot/tree/main/skills/make-repo-contribution
[prd-example]: https://github.com/github/awesome-copilot/tree/main/skills/prd
[drawio-example]: https://github.com/github/awesome-copilot/tree/main/skills/drawio
[browser-example]: https://github.com/github/awesome-copilot/tree/main/skills/webapp-testing

---
title: "レッスン 3 - エージェントモード: Plan と Autopilot"
description: "Plan で方針を決め、Autopilot で Issue からフィルタリングを実装し、Interactive モードで結果をレビューします。"
authors:
  - geektrainer
lastUpdated: 2026-09-18
---

最初に、小さな機能をプロジェクトに追加しました。より大きな変更には、さらに確実なプロセスが必要です。GitHub Copilot CLI は組織の既存の開発フローに沿って動作するため、適切な方法で適切なものを構築できます。このレッスンから、一般的なエージェント主導の開発プロセスに取り組みます。Issue から機能を実装し、コードと動作を検証して、最終的にプロジェクトにマージします。

> [!NOTE]
> 機能開発を進める間は、同じ会話とブランチを使います。通常はファイルの種類ごとにブランチや PR を分けますが、ここでは主な概念に集中できるよう手順を簡略化します。

このレッスンでは、次の作業を行います。

- GitHub Issue から Copilot の新しい会話を開始します。
- Plan モードで要件を定義します。
- Autopilot モードで新機能を実装します。
- コードをレビューします。
- 転送したブラウザーで機能を手動検証します。

この機能開発を進める中で、リポジトリの指示を更新し、既存の `quality-checks` スキルをカスタマイズし、MCP 検証を追加して QA エージェントを作成し、機能の PR を作成します。

## シナリオ

Tailspin Toys のカタログが拡大し、訪問者はカテゴリやパブリッシャーでゲームを絞り込む必要があります。バックログの Issue には機能が記述されていますが、複数カテゴリの組み合わせなど、コーディング前に合意が必要な詳細があります。Plan モードで決定事項を整理し、範囲を限定した実装を Autopilot に任せます。

## 背景

AI コーディング エージェントを開発フローに加えても、基本的な手順は変わりません。むしろ、これまで以上に重要です。多くの開発者は、次のような手順で作業します。

1. 作業内容を記述した Issue から始めます。
2. 実装内容を計画します。
3. コードを作成してレビューします。
4. テストを実行してコードを検証します。
5. 新しい機能を手動で検証します。
6. プルリクエスト (PR) を作成します。
7. コードレビューと継続的インテグレーションが成功した後、コードをマージします。

> [!NOTE]
> 詳細はチームや組織によって異なりますが、多くの場合、上記の手順を基にしたフローで進めます。

この標準的な方法に従えば、AI が生成したコードが要件を満たし、手書きのコードと同じ検証プロセスを経ていることを確認できます。

## 会話モード

**会話モード**は、エージェントにどの程度の自律性を与えるかを制御します。<kbd>Shift</kbd>+<kbd>Tab</kbd> を押すとモードを切り替えられます。

- **Interactive**: 開発者とエージェントが協力して作業します。エージェントは変更を提案し、入力を待ってから次に進みます。
- **Plan**: エージェントが先に計画を作成します。プロジェクト ファイルの編集はできません。
- **Autopilot**: エージェントが自律的にコードを書き、テストを実行し、タスクが完了するまで反復します。

まず Plan モードで計画を確認し、その後 Autopilot で実装します。

## Issue から開始する

フィルタリングに着手する前に、Codespace に戻り、リポジトリとターミナルの準備ができていることを確認します。

1. Codespace に戻ります。停止している場合は、再起動してから続けます。
2. 星評価の PR がマージ済みであることを確認します。
3. ターミナルを開いていない場合は、<kbd>Control</kbd>+<kbd>\`</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux) を押します。
4. `main` を更新し、フィルタリング用ブランチを作成します。

   ```bash
   git checkout main
   git pull --ff-only
   git checkout -b game-filters-cli
   ```

5. Copilot CLI を起動します。

   ```bash
   copilot --yolo
   ```

6. <kbd>Tab</kbd> を 2 回押して **Issues** タブを開きます。
7. <kbd>A</kbd> を押してすべての Issue を表示します。
8. 矢印キーで **Allow users to filter games by category and publisher** というタイトルの Issue を選択します。
9. <kbd>C</kbd> を押して Issue をプロンプトに追加し、**Session** タブに戻ります。

プロンプトの先頭に `#7` (または同様の番号) が表示されます。`#` を使うと、GitHub の Issue やプルリクエスト (PR) をコンテキストに追加できます。

## フィルタリング機能を計画する

計画を立てると、Copilot に作業を任せる前に、機能の実装方法やタスクの進め方を決められます。複雑な作業では、時間をかけて計画することをお勧めします。Plan モードに切り替えて Copilot に計画を作成させます。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Plan モードに切り替えます。プロンプトの下にあるモード表示が **Plan** になったことを確認します。
2. 先ほど追加した Issue の後に、次のプロンプトを入力します。

   ```plaintext
   Create a plan for implementing this feature.
   ```

Copilot はプロジェクトを調べ、適切な実装方法を検討して計画を作成します。

3. フィルタリングの仕様について質問された場合は、希望する動作に基づいて回答します。正解は 1 つではありません。
4. 計画が完成したら、<kbd>Control</kbd>+<kbd>E</kbd> (Mac) または <kbd>Ctrl</kbd>+<kbd>E</kbd> (Windows/Linux) を押して計画を展開します。
5. 計画全体を確認します。
6. 決定事項と合わない箇所があれば、Copilot に修正を依頼します。

## Autopilot を承認する

計画の作成とレビューが完了したら、実装に進みます。Autopilot モードで Copilot に作業を任せます。

Autopilot では、Copilot がタスクを完了したと判断するまで、問題への対応を反復します。

1. **Accept plan and build on autopilot (recommended)** または、インストール済みのバージョンで同様のラベルが付いた選択肢を選びます。
2. プロンプトの下にあるモード表示が **Autopilot** になったことを確認します。
3. Copilot が計画に沿って作業を反復し、コードを生成してテストを実行する様子を確認します。

> [!NOTE]
> 承認すると、すぐに実装が始まる場合があります。先に計画を確認してください。依存関係の不足やポートの競合が報告された場合は、チェック完了とみなす前に環境の問題を解決してください。

## 実装をレビューして検証する

コードが生成されたら、ほかのコードと同様にマージ前にレビューします。コードを確認し、サイトを実行して動作も確認します。

1. <kbd>Shift</kbd>+<kbd>Tab</kbd> を押して Interactive モードに切り替えます。モード表示が **Plan** または **Autopilot** ではないことを確認します。
2. `/diff` を実行し、フィルタリングの実装とテストを確認します。
3. 結果を Issue および計画時に決定した内容と照らし合わせます。
4. レビューが終わったら、<kbd>Esc</kbd> を押して差分ビューを閉じます。
5. プロジェクトのチェック結果を確認し、失敗があれば Copilot に解決を依頼します。

## 新機能を試す

コードが正しく見えても、実際に動作するとは限りません。前と同様にアプリを起動し、Codespaces が転送したポートからサイトを開きます。

1. Copilot にアプリの起動を依頼します。

   ```plaintext
   Start the app so I can try the filtering feature in my browser. Tell me the URL and leave the server running.
   ```

2. Codespaces にポート `4321` が使用可能と表示されたら、**Open in Browser** を選択します。
3. カテゴリでの絞り込み、パブリッシャーでの絞り込み、計画時に合意した組み合わせを試します。
4. リセット時と結果が空の場合の動作が、Issue と決定事項に合っていることを確認します。
5. Codespace に戻り、起動した開発サーバーの停止を Copilot に依頼します。

## まとめと次のステップ

会話モードを使い分けて、機能の構築とレビューを行いました。このレッスンでは、次の作業を行いました。

- GitHub Issue から Copilot の新しい会話を開始しました。
- Plan モードで要件を定義しました。
- Autopilot モードで新機能を実装しました。
- コードをレビューしました。
- 転送したブラウザーで機能を手動検証しました。

次は、コードが文書化されたプラクティスに沿うよう、[カスタム命令を使います][next-lesson]。

## リソース

- [GitHub Copilot CLI の Autopilot][autopilot]
- [Copilot CLI コマンド リファレンス][cli-reference]

[autopilot]: https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot
[cli-reference]: https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference
[previous-lesson]: ../2-add-star-rating/
[next-lesson]: ../4-custom-instructions/

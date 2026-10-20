---
title: "演習 5: GitHub Copilot の作業を反復して改善する"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

## 作業をレビューする

このワークショップでは、ユーザー体験の改善と機能追加に関する複数のタスクを GitHub Copilot と進めてきました。コードへのドキュメント追加、デザイン チームが改善を検討するための関連ゲーム機能、ハイコントラストとライト モードの切り替えを含むアクセシビリティ機能を依頼しました。コードの変更を確認し、必要に応じて Copilot にフィードバックを送って改善します。

### シナリオ

生成 AI を導入しても、ソフトウェア設計と DevOps の基本は変わりません。生成されたコードは必ずレビューし、通常の DevOps プロセスに沿って作業します。チームのレビューを開始する前に、ドキュメント、関連ゲーム機能、アクセシビリティ機能に関する GitHub Copilot の提案を確認します。

## GitHub Copilot cloud agent のセキュリティ

Copilot cloud agent は監督なしで非同期にタスクを実行するため、安全性を保つための制約が設けられています。

- Copilot はリポジトリへの読み取りアクセスを持ち、書き込みはコード作業用のブランチに限られます。
- Cloud agent は GitHub Actions 内で、作業用の独立した一時環境を作成して実行します。
- GitHub Actions ワークフローを実行するには、人間の承認が必要です。
- [外部リソースへのアクセスは既定で制限されています](https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)。MCP サーバーへのアクセスも含まれます。

## 生成されたドキュメントをレビューする

最初に、GitHub Copilot cloud agent が作成したコード ドキュメントの PR を確認します。GitHub.com の通常の PR インターフェイスを使います。

> [!NOTE]
> PR に、GitHub Copilot がファイアウォールでブロックされているという警告が表示される場合があります。Copilot は MCP サーバーなどの外部リソースへのアクセスが既定で制限されるため、これは**想定どおり**です。必要であれば、[Copilot cloud agent のファイアウォールをカスタマイズまたは無効化][agent-firewall]できます。

1. github.com のリポジトリに戻ります。
2. **Pull Requests** を選択して、PR の一覧を開きます。
3. **Add missing documentation** などのタイトルが付いたプルリクエストを開きます。

> [!NOTE]
> Copilot がまだ作業中の場合、PR のタイトルに **[WIP]** が付いています。作業が完了するまで待ちます。数分かかる場合があるので、その間に休憩したり、ここまでに学んだことを振り返ったりできます。

4. PR の準備ができたら、**Files changed** タブを選択し、変更内容をレビューします。

   ![Files changed タブ](../../../_images/shared-pr-files-changed.png)

5. 新しく更新されたコードを確認します。TSDoc ドキュメント コメントなどが追加されています。変更内容は状況によって異なります。レビュー時は、Exercise 2 の冒頭で確認したカスタム指示ファイルに基づく TSDoc コメント、TypeScript 規約、コメント ヘッダーに注目します。
6. 更新内容をレビューして問題がなければ、**Conversation** タブに戻り、下へスクロールします。
7. ワークフローの承認待ちを示すインジケーターがあることを確認します。
8. 承認待ちのワークフローがある場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行するボタン](../../../_images/shared-approve-workflows.png)

9. PR のチェック欄にワークフローが登録されます。問題がなければ、単一の Astro アプリに対するプロジェクトのチェックが成功します。完了まで数分かかる場合があります。

## GitHub Copilot に変更を依頼する

Copilot との PR 作業は一方向ではありません。チームのほかのメンバーと同様に、PR またはコードのインライン コメントで Copilot にメンションできます。Copilot はコメントを確認し、対応する新しいセッションを開始します。結果は確率的に変化するため、依頼文を一律には指定できません。たとえば、次の更新を依頼できます。

- 各コード ファイルの先頭に、役割を簡単に説明するコメント ヘッダーを追加する。
- TypeScript と Astro ファイルに TSDoc ドキュメント コメントを追加する。
- Astro アプリの構成を説明する README を作成する。

1. 生成されたドキュメントへの変更を依頼するコメントを追加し、ユーザーと同じように **@copilot** をメンションします。上の例またはコードベースに必要な別の変更を依頼します。
2. **View Session** を選択して Copilot の作業を確認します。更新を行うために Copilot が新しいセッションを開始します。
3. **Back to pull request** を選択すると、PR に戻れます。

   ![Back to pull request ボタン](../../../_images/ex7-back-to-pr.png)

4. Copilot が変更を完了すると、PR に新しいコミットが表示されます。
5. **Files changed** タブを選択し、変更内容をレビューします。

満足できるまで反復して改善できます。問題がなければ、ドラフト PR をレビュー可能な状態に変更し、`main` ブランチにマージします。

![PR をレビュー可能な状態に変更する操作](../../../_images/ex7-ready-for-review.png)

## 関連ゲーム機能をレビューする

次に、ゲーム詳細ページに関連ゲームを表示する Issue に対して、Copilot が作成した PR を確認します。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Show related games on the game details page** などのタイトルが付いた PR を選択します。
4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 更新内容に問題がなければ、**Conversation** タブに戻り、下へスクロールします。
6. ワークフローの承認待ちを示すインジケーターがあることを確認します。
7. 承認待ちのワークフローがある場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行するボタン](../../../_images/shared-approve-workflows.png)

8. PR のチェック欄にワークフローが登録されます。問題がなければ、単一の Astro アプリに対するプロジェクトのチェックが成功します。完了まで数分かかる場合があります。
9. **任意:** Codespace でこのブランチに切り替え、関連ゲーム機能を手動でテストできます。Codespace に移動し、ターミナルで次のコマンドを実行します。`<branch-name>` は Copilot が作成したブランチ名 (例: **copilot/fix-8**) に置き換えます。

   ```bash
   git fetch origin
   git checkout <branch-name>
   ```

Copilot が関連ゲーム機能を作成しました。前と同様に、cloud agent にコメントを追加し、更新を依頼して反復できます。たとえば、表示する関連ゲームの数を調整したり、コメント ヘッダーや TSDoc ドキュメント コメントを追加したりできます。この Issue はカスタム命令の更新前に割り当てたため、追加指示が必要な場合があります。**Conversation** タブにコメントを追加すると、Copilot が新しいセッションを開始します。

## アクセシビリティ機能をレビューする

最後に、アクセシビリティ用カスタムエージェントで実装した機能をレビューします。この PR には、Exercise 3 で依頼したハイコントラスト モードと、Exercise 4 の Mission Control で追加を依頼したライト モードの両方が含まれます。

1. GitHub.com のリポジトリに戻ります。
2. **Pull Requests** タブを選択します。
3. **Add high contrast mode to website** などのタイトルが付いた PR を選択します。

> [!NOTE]
> Copilot がまだ作業中の場合、PR のタイトルに **[WIP]** が付いています。完了するまで数分待ちます。

4. **Files changed** タブを選択し、生成されたコードをレビューします。
5. 次の点に注意して実装を確認します。
   - モードを切り替えるトグル UI コンポーネント。
   - ユーザー設定を保存する local storage の使用。
   - ハイコントラスト モードとライト モードの CSS またはスタイル変更。
   - アクセシビリティ属性 (ARIA ラベル、キーボード ナビゲーションなど)。
   - モード切り替えを管理する JavaScript/TypeScript コード。
6. 更新内容に問題がなければ、**Conversation** タブに戻り、下へスクロールします。
7. ワークフローの承認待ちを示すインジケーターがあることを確認します。
8. 承認待ちのワークフローがある場合は、**Approve and run workflows** を選択します。

   ![ワークフローを承認して実行するボタン](../../../_images/shared-approve-workflows.png)

9. PR のチェック欄にワークフローが登録されます。問題がなければ、単一の Astro アプリに対するプロジェクトのチェックが成功します。完了まで数分かかる場合があります。
10. **任意:** Codespace でこのブランチに切り替え、アクセシビリティ機能を手動でテストできます。Codespace に移動し、ターミナルで次のコマンドを実行します。`<branch-name>` は Copilot が作成したブランチ名に置き換えます。

    ```bash
    git fetch origin
    git checkout <branch-name>
    ```

    続いてアプリを起動し、ブラウザーでハイコントラストとライト モードの切り替えを試します。ページを再読み込みしても設定が維持されることを確認します。

アクセシビリティ カスタムエージェントは、アクセシビリティのベストプラクティスに沿った機能を Copilot に実装させるための指針になりました。アクセシビリティ上の問題や改善点があれば、前の PR と同様にコメントで **@copilot** に依頼できます。

## オプションの演習 — 委任を続ける

バックログの実際の Issue をエージェントに任せ、その間に別の作業をする方法を試します。リポジトリにいくつか Issue を追加し、Copilot に割り当てます。

- ゲーム詳細ページに支援希望者向けのフォームを追加する。
- ゲーム一覧ページにページネーションを実装する。
- データアクセス ヘルパーに入力検証とエラー処理を追加する。

## まとめ

Cloud agent の操作環境で一連の作業を完了しました。各レッスンを通じて、次の作業を行いました。

- **リポジトリのカスタム命令ファイルを確認し、cloud agent の出力への影響を把握しました。**
- **Issue を Copilot cloud agent に割り当て、環境の準備、計画、非同期の実行を確認しました。**
- **アクセシビリティ用カスタムエージェントを作成して使い、ハイコントラストとライト モードの切り替えを追加しました。**
- **Copilot agents ページを Mission Control として使い、アクセシビリティ セッションを監視して指示しました。**
- **cloud agent の PR をレビューして反復し、@copilot に変更を依頼してワークフローを承認しました。**

## 振り返りと次のステップ

Cloud agent の操作環境での演習を完了しました。続けて取り組む場合、他の操作環境では関連する次の内容を学べます。

- 🖥️ **[VS Code の操作環境](../../vscode/)** — IDE から Copilot Chat のエージェントモードと MCP 統合を使います。
- 💻 **[CLI の操作環境](../../cli/)** — ターミナルから 2 つの変更をレビューしてリリースします。Plan と Autopilot モード、エージェント スキル、カスタム QA エージェント、Playwright MCP、Agent Merge、cloud agent に戻るための任意の `/delegate` を扱います。

自身のリポジトリでは、次のアイデアを試せます。

- リファクタリングやテスト範囲に関する Issue を cloud agent に割り当てる。
- 別の分野に対応するカスタムエージェントを作成する。
- 別の技術スタック用に `copilot-setup-steps.yml` を構成する。

## リソース

- [GitHub Copilot][github-copilot]
- [Copilot エージェントについて][copilot-agents]
- [GitHub Issue を Copilot に割り当てる][assign-issue]
- [Copilot cloud agent のセットアップ ワークフローのベストプラクティス][cloud-agent-best-practices]
- [Copilot cloud agent のファイアウォールを構成する][agent-firewall]

---

| [← 前のレッスン: エージェントの管理][previous-lesson] |
|:--|

[github-copilot]: https://github.com/features/copilot
[cloud-agent-overview]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[agent-firewall]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall
[previous-lesson]: ../4-managing-agents/

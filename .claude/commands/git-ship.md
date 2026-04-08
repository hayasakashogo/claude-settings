現在の全ての変更をステージングしてコミットし、リモートにpushします。
ブランチへの初回pushの場合はプルリクエストも作成します。

引数: $ARGUMENTS
- 引数がある場合: それをコミットメッセージとして使用する
- 引数がない場合: `git diff` の内容からコミットメッセージを自動生成する

以下の手順を Bash ツールで実行してください：

1. `git status --short` で変更の有無を確認する
   - 変更がなければ「変更はありません」と伝えて終了する
2. `git diff HEAD` および未追跡ファイルの内容を確認する
3. コミットメッセージを決定する:
   - 引数がある場合はそれをそのまま使用する
   - 引数がない場合は変更内容を分析して適切な日本語または英語のコミットメッセージを生成する（例: "feat: ○○を追加", "fix: ○○を修正" など）
4. 初回pushかどうか確認する:
   - `git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null` を実行し、出力が空またはエラーなら「初回push」と判断する
5. `git add -A` で全ての変更をステージングする
6. 以下の形式でコミットする（HEREDOCを使用）:
   ```
   git commit -m "$(cat <<'EOF'
   <コミットメッセージ>

   Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
   EOF
   )"
   ```
7. pushする:
   - 初回pushの場合: `git push --set-upstream origin <現在のブランチ名>`
   - 2回目以降: `git push`
8. 初回pushの場合はプルリクエストを作成する:
   - `git log main..HEAD --oneline` などでブランチの変更内容を把握する
   - `gh pr create` を使い、変更内容に基づいてタイトルと本文を自動生成する
   - 本文は以下の形式にする（HEREDOCを使用）:
     ```
     gh pr create --title "<PRタイトル>" --body "$(cat <<'EOF'
     ## Summary
     <変更内容の要約（箇条書き）>

     ## Test plan
     <テスト手順の箇条書き>

     🤖 Generated with [Claude Code](https://claude.com/claude-code)
     EOF
     )"
     ```
9. 完了後にpushしたブランチ名、コミットハッシュ、作成したPRのURL（該当する場合）を表示する

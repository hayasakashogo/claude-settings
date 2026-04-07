現在の作業ディレクトリを GitHub の新しいリポジトリとして公開します。

引数: $ARGUMENTS
- 第1引数: リポジトリ名（省略時はカレントディレクトリ名を使用）
- 第2引数: `private` を指定するとプライベートリポジトリになる（省略時は public）

以下の手順を Bash ツールで実行してください：

1. `pwd` でカレントディレクトリのパスを確認し、ディレクトリ名を取得する
2. 引数を解析する:
   - 第1引数が空または未指定ならカレントディレクトリ名をリポジトリ名とする
   - 第2引数が "private" なら `--private`、それ以外は `--public` フラグを使う
3. `git rev-parse --is-inside-work-tree 2>/dev/null` で git 初期化済みか確認する
   - 未初期化なら `git init` を実行する
4. `git remote get-url origin 2>/dev/null` でリモートが設定済みか確認する
   - **リモートがすでに設定されている場合**: `git push` を試みる。成功したらリモートURLを表示して終了。失敗したらエラー内容を表示して中断する
5. コミット履歴が空か確認する (`git log --oneline -1 2>/dev/null`)
   - コミットがなければ `git add -A` → `git commit -m "Initial commit"` を実行する
6. `gh repo create <リポジトリ名> --public/--private --source=. --remote=origin --push` を実行する
   - **同名リポジトリが GitHub 上にすでに存在してエラーになった場合**:
     - `gh repo view <GitHubユーザー名>/<リポジトリ名> --json url -q .url` でリポジトリURLを取得する
     - `git remote add origin <URL>` でリモートを設定する
     - `git push -u origin main` または `git push -u origin master` を実行する
7. 完了後にリポジトリのURLを表示する

# Git / GitHubについてまとめたこと
## Git
 - バージョン（変更履歴）を管理をするもの
## GitHub
 -  Gitのバージョン管理をオンラインで共有できる
 -  GItHubとはエンジニアのSNS
※ GitとGitHubは違う

## イメージ
- GitHubを使う時は川に例えられることが多い
- 本流:mainブランチ(以前はmasterブランチ)
[The GitHub Blog](https://github.blog/changelog/2020-10-01-the-default-branch-for-newly-created-repositories-is-now-main/)参照
- 支流:作業用ブランチ（Topic branch）
※本流は流れが止めてはいけない。必ず動いている状態であり、ここで作業をすることはチーム開発などの実際の現場ではやらない。支流でいろんな機能を作成して完璧に動くことを確認して、本流へ合流させる
- なにか変更する時は支流（Topic branch）で行って本流へマージするように心がける

- 本流（master）を汚染してはならない。汚染度合いは、自動テストが十分に存在しているのであれば、自動テストが全て通るかどうかで判断できる。
- 支流は一時的に汚染される（通らない自動テストが存在する）ことがありうるが、本流に合流させるときには浄化処理を済ませて置く（全てのテストが通る状態に修正する）必要がある
- 元からあったテストが通れば良いというだけでなく、基本的には、追加/修正したコードに対する十分なテストを常に追加すべき

## git-flow、GitHub Flowを理解しておく
[【図解】git-flow、GitHub Flowを開発現場で使い始めるためにこれだけは覚えておこう](https://www.atmarkit.co.jp/ait/articles/1708/01/news015.html)
## GitHub Flowでグランドルール
 1. masterブランチは常にデプロイ可能である
 2. 作業用ブランチをmasterから作成する（例：new-oauth2-scopes）
 3. 作業用ブランチを定期的にプッシュする
 4. プルリクエストを活用する
 5. プルリクエストが承認されたらmasterへマージする
 6. masterへのマージが完了したら直ちにデプロイを行う

## 一人とチーム開発の違い
### 一人開発の場合
- 本流だけで（main/masterブランチだけで）完結できるのでローカルの作業をadd/commit/pushする
### チーム開発の場合
- 実際のプロジェクトやチーム開発となると本流は限られたひと(リーダー職)が管理し、メンバーは支流（Topicブランチ）で作業、本流へ合流させていいかの確認（Pull request）を行う
- 自分が作業している完了したら必ずpushする前にはgit pullをして最新の状態かどうかを確認する（コンフリクトが起きるので）

 ## メモ

 ### GitHubでreadme.mdを作成してmain branch（デフォルト）ができた後にローカルでmaster branchを作成してしまったときに起きたこと
 #### エラー文
 - fatal: refusing to merge unrelated histories
 #### 事例
1. GitHub上でリポジトリを作り， readme.mdを作成
2. ローカルでgit initしてリポジトリを作り， git remote add origin <GitHubのリポジトリ>でリモートリポジトリを指定
3. ローカルで作業を行い，pushをしたら起きた
※このあとpullをすると
```
fatal: refusing to merge unrelated histories
```
と表示されpullもできない
### 原因
- 1.の作業でmainブランチができてしまった
- 2.でデフォルト（main）ブランチではなくmasterブランチを指定した
### 対応
#### git mergeコマンドに--allow-unrelated-historiesのオプションを使いして実行
 - masterブランチがデフォルトのとき
```
git merge --allow-unrelated-histories origin/master
```
 - mainブランチがデフォルトのとき
```
git merge --allow-unrelated-histories origin/main
```
- 実際はmasterブランチをデフォルトに切り替えてmainブランチは削除してブランチを１つにした（一人開発と初期段階のため）
[GitHubリポジトリのデフォルトブランチをdevelopに変更する](https://qiita.com/unsoluble_sugar/items/0603187d5a732f91d502)
[Githubでブランチ削除](https://hacknote.jp/archives/27338/)

### なぜ起きたか
- 通常のマージでは，共通の祖先から分岐したブランチ同士が結合される．しかし，今回はGithubでのコミット，ローカルでのコミットはそれぞれ共通の祖先がない状態から行われてしまっているためマージができなかった

 ## Tips
 ### 便利な設定
 -  [ターミナルで作業が図解される](https://bettamodoki.hatenadiary.jp/entry/20120427/1335512071)
```
git config --global alias.lola "log --graph --decorate --pretty=oneline --abbrev-commit --all"
```
をターミナル上で実行（内容はgit configを変更する
```
git lola
```
がターミナルで使えるようになる



 ### コマンド事例と説明
 [【Git】基本コマンド](https://qiita.com/konweb/items/621722f67fdd8f86a017)
 #### ヘルプコマンドの利用
  - git help <サブコマンド>
  最終的には、helpの内容を読んだだけで使い方が理解できるようになるのが望ましい
  （ググった情報や書籍の情報は、古くなってたり、嘘だったりするので
 #### ローカルリポジトリの作成
 - git init //初期化
 - git add * //現在あるファイル全部追加
 - git commit -m "initial commit" //コミット

### リモートリポジトリからプロジェクトをコピー
 - cd [ローカルリポジトリのパス]
 - git clone [リモートリポジトリパス] 
例）git@github.com:514daisuke/GitHub.git

#### ファイル更新までの基本手順
 -  git add [変更/追加したファイル名]
    - git add .（全部あげてもいい）
 - git commit -m"コメント" // ファイルをコミット
 - git push origin 【branch名】//※Topicブランチが多い/ ファイルを更新

#### git addの使用例
 - git add . //すべてのファイル・ディレクトリ
 - git add *.css //すべてのCSSファイル
 - git add -n //追加されるファイルを調べる
 - git add -u //変更されたファイルを追加する
 - git rm --cached //addしてしまったファイルを除外
#### addの取り消し
 - 間違えてgit addしてしまった場合はresetでキャンセルできる。
 - git reset HEAD
 - git reset HEAD {file_name}

#### git commitの使用例
 - git commit -a //変更のあったファイルすべて
 - git commit --amend //直前のコミットを取り消す
 - git commit -v //変更点を表示してコミット

#### commitの取り消し
 - git reset --soft HEAD~2 // 最新のコミットから2件分をワークディレクトリの内容を保持し取り消す
 - git reset --hard HEAD~2 // 最新のコミットから2件分のワークディレクトリの内容とコミットを取り消す

#### commitメッセージの修正
 - git rebase -i HEAD~2 // HEADから2件のコミットメッセージ
 - pick {commit_id} {commit_meessage} // 2件目
 - pick {commit_id} {commit_meessage} // 1件目(最新コミット)
 - git commit --amend
 - git rebase --continue


 #### ブランチの作成/移動/削除/変更/一覧
 - git branch [branch_name]  //ブランチの作成 
 - git checkout [branch_name]  //ブランチの移動
 - git branch -d [branch_name]  //ブランチの削除
 - git branch -m [branch_name]  //現在のブランチ名の変更
 - git branch // ローカルブランチの一覧
 - git branch -a //リモートとローカルのブランチの一覧
 - git branch -r //リモートブランチの一覧
 - git checkout -b branch_name origin/branch_name //リモートブランチへチェックアウト

 #### リモートブランチの確認（urlも表示）
 -  git remote -v


#### 編集をマージ
 - git checkout branch名（Topicブランチに切り替え）
 - git pull origin develop（リモートdevelopから最新の情報をpull）
 - git merge --no-ff develop（Topicブランチ作業にdevelopブランチをマージ）

 - git checkout develop（develop環境切り替え）
 - git merge --no-ff  branch名（Topicブランチ作業しているブランチのマージ）

##### staging環境（本流）にマージ
 - git checkout staging
 - git merge --no-ff develop
 - git push
 - git log -p


 #### マージの取り消し
- git merge --abort
コンフリクトが発生して一旦戻したい場合

 #### ブランチを切り替える
 - git checkout 【branch名】
 - git checkout -b 【branch名】//新しいブランチを作成して移動する
 - git checkout -t origin/develop // 名前の

 #### 今やってる作業を一時退避する
 - git stash
 - git stash pop //戻す場合
 - git stash list //退避の一覧
 - git stash clear //退避の消去

 #### ログ表示
 - git log //コミットのログが見れる
 - git log -p
 - git log --oneline
 - git reflog //いろいろ見れる
 - git reflog origin/branch_name //pushのログが見れる

#### ファイル名変更
 - git mv [変更前のファイル名] [変更後のファイル名]
 - git commit -a -m "rename"
 - git push origin master



 #### 差分チェック
 - git diff
 - git diff HEAD^ //最後のコミットからの差分を表示
 - git diff --name-only HEAD^ //差分ファイルを表示
 - git diff file1.txt file2.txt //特定フィイルの差分
 - git diff commit1 commit2 //コミットの差分


#### コンフリクトの解消
##### 手動で解決する場合
 - コンフリクトを解消しファイルを保存後、下記のコマンドを実行
 - git add {file_name}
 - git commit {file_name} -m "コミットメッセージ"
##### 自動で解決する場合
##### 現在のブランチを正とする
 - git checkout --ours {fime_name}
#### マージで指定したブランチを正とする
 - git checkout --theirs {fime_name}


### 参考サイト
[git](https://git-scm.com/book/ja/v2/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E7%AE%A1%E7%90%86%E3%81%AB%E9%96%A2%E3%81%97%E3%81%A6)
[git rebase についてまとめてみた](https://qiita.com/KTakata/items/d33185fc0457c08654a5)

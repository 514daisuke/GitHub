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
 #### ローカルリポジトリの作成


 #### 
 - git branch -a
 - git branch -m develop develop_old
 - git branch -d ブランチ名
 - git branch -D ブランチ名
 #### 
 - git log -p
 - git log --oneline

 -  git remote -v

 #### 
 - git add .
 - git commit -m"コメント"
 - git push

 #### 
 - git checkout 【branch名】
 - git checkout -b 【branch名】
 - git checkout -t origin/develop

 #### 
 - git stash

 #### 
 - git reflog
 - git reset --hard HEAD{ }


#### マージ作業
 - git checkout branch名（Topicブランチに切り替え）
 - git pull origin develop（リモートdevelopから最新の情報をpull）
 - git merge --no-ff develop（Topicブランチ作業にdevelopブランチをマージ）

 - git checkout develop（develop環境切り替え）
 - git merge --no-ff  branch名（Topicブランチ作業しているブランチのマージ）

 - git checkout staging
 - git merge --no-ff develop
 - git push
 - git log -p

### 参考サイト
[git](https://git-scm.com/book/ja/v2/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3%E7%AE%A1%E7%90%86%E3%81%AB%E9%96%A2%E3%81%97%E3%81%A6)
[git rebase についてまとめてみた](https://qiita.com/KTakata/items/d33185fc0457c08654a5)

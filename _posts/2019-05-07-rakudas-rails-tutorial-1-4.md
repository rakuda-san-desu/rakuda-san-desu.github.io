---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜1.4"
date: "2019-05-07 17:52:23"
categories: ["Git", "Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["git", "Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 572
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial-1-4/"
---
<h2>1.4 Gitによるバージョン管理</h2>
gitに関しては<a href="https://prog-8.com/" target="_blank" rel="noopener noreferrer">Progate</a>のGit学習コースをやっていたのだけど
実際使ってみると「こんな時どうするの？？」とか、そもそもの仕組みがあんまりわかっていなかったりしていたので
初心者にもわかりやすいと評判の<strong>わかばちゃんと学ぶ git使い方入門</strong>で勉強しました。
<a href="https://prog-8.com/" target="_blank" rel="noopener noreferrer">Progate</a>よりもうちょっと踏み込んだ、もうちょっと実践的な内容でした。
が、cloud9では本文で紹介されているアプリが使えなかったので、<a href="https://rakuda3desu.net/rakudas-git-lesson/">同じ内容をターミナルで行えるようにコマンドを調べた</a>りしました
この時点で言うのもなんですが、railsチュートリアルをやる前にProgateにプラスアルファでGitを勉強しておくと良さそうと思います。
<h3>1.4.1 インストールとセットアップ</h3>
<h4>git configで設定</h4>
別途gitを練習したりチュートリアルが2週目だったりするのでセットアップはしてある気がしますので確認します
<pre class="lang:default decode:true " title="ターミナル" >$ git config user.name
#登録されていればここに表示されます
$ git config user.email
#登録されていればここに表示されます

#登録されていなければ下記コマンドで登録
$ git config --global user.name "Your Name"
$ git config --global user.email your.email@example.com</pre>
<h4>git initでセットアップ</h4>
セットアップとか現在の状況の確認とか
<pre class="lang:default decode:true " >#何度も言うけどアプリのルートディレクトリに移動するの忘れないでねらくだ🐫！
$ cd hello_app
#リポジトリの初期化
$ git init
#すべての更新ファイルをステージに移動
$git add -A
#ステージングの状況を確認
$git status
#現状ではすべての更新ファイルがステージングされて緑で表示

#メッセージを追加するcommitのコマンド　メッセージ→"リポジトリを初期化"
$git commit -m "Initialize repository"
#ログ表示　「ｑ」で終了
$ git log</pre> 
コマンドの詳しい説明は割愛
<h3>1.4.2 Gitのメリット</h3>
Gitを使うメリットの一例
練習してみる場合は、念の為コピーしたデータなどを使いましょう
<pre class="lang:default decode:true " title="練習はコピーしたデータなどで
">#app/controllers/の内容を表示
$ ls app/controllers/
#ファイル：application_controller.rb  ディレクトリ：concerns　がある
application_controller.rb  concerns/
#app/controllers/を削除　削除の確認をしないで中身も全部消しますねのオプション（怖い・・・）
$ rm -rf app/controllers/
#app/controllers/の内容を表示
#無いものは表示できません！と言う感じのメッセージ
$ ls app/controllers/
ls: app/controllers/: No such file or directory</pre>
<pre class="lang:default decode:true " title="練習はコピーしたデータなどで
" >$ git status
#現在のブランチはmasterです
On branch master
#変更はあるけど更新はありません
Changed but not updated:
  (use "git add/rm ..." to update what will be committed)
  (use "git checkout -- ..." to discard changes in working directory)
      #削除された内容の表示
      deleted:    app/controllers/application_controller.rb
no changes added to commit (use "git add" and/or "git commit -a")</pre>
この状態を元に戻す方法がこちら
<pre class="lang:default decode:true " title="練習はコピーしたデータなどで
" >#強制的に変更を破棄して元に（最後にコミットした時の状態に）戻す的な奴
$ git checkout -f
$ git status
On branch master
nothing to commit (working directory clean)
#app/controllers/の内容を表示
$ ls app/controllers/
#削除してしまったデータが元に戻っている
application_controller.rb  concerns/</pre>
<h3>1.4.3 Bitbucket</h3>
1週目はBitbucketでやったんだけど今回はGitHubでやってみようと思います
公開鍵に関してはこちら↓
[blogcard url="https://rakuda3desu.net/git-lesson02/"]
GitHubのアカウントを用意してSSHで接続できるようにしておきます
<h4>GitHubでのリポジトリの作成～push</h4>
右上の「+」をクリックしてNewRepositoryをクリック
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/1.4-1.png"><img class="alignnone size-medium wp-image-582" src="https://rakuda3desu.net/wp-content/uploads/2019/05/1.4-1-300x218.png" alt="" width="300" height="218" /></a>
アプリ名を入力して公開・非公開を選択
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/1.4-2.png"><img class="alignnone wp-image-583 size-large" src="https://rakuda3desu.net/wp-content/uploads/2019/05/1.4-2-1024x657.png" alt="" width="1024" height="657" /></a>
表示のアドレスをコピー
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/1.4-3.png"><img class="alignnone wp-image-584 size-large" src="https://rakuda3desu.net/wp-content/uploads/2019/05/1.4-3-1024x335.png" alt="" width="1024" height="335" /></a>
<pre class="lang:default mark:(1-2) decode:true " title="ターミナル" >#リモートリポジトリを追加　GitHubで表示されていたアドレスをペースト
$  git remote add origin git@github.com:rakuda-san-desu/hello_app.git
#まほうのじゅもん（詳細は割愛）
$ git push -u origin --all</pre> 
ここでうまくプッシュできない場合、「https://~」で始まるアドレスをコピーしていないか確認してみて下さい。
<h3>1.4.4 ブランチ、編集、コミット、マージ</h3>
以下gitの説明部分についてはざっくり流します
別途勉強した方が絶対いいです
<h4>Branch (ブランチ)</h4>
<pre class="lang:default decode:true " title="ターミナル" >#ブランチmodify-READMEを作ってそこにチェックアウト（異動）
$ git checkout -b modify-README
Switched to a new branch 'modify-README'
#現在あるブランチを表示
$ git branch
  master
#使用中のブランチは頭に「*」が付いて緑の文字で表示
* modify-README</pre>
<h4>Edit (編集)</h4>
本文の通りにREADME.mdを編集→保存
<h4>Commit (コミット)</h4>
git statusで現状を確認→ステージングされていない更新（README.md）がある
<pre class="lang:default decode:true " title="ターミナル" >$ git commit -a -m "Improve the README file"</pre>
今回はすでに存在するファイルを更新したのでgit commit -a -mが使えるけど
前回のコミットの後に新しいファイルを作成している場合はgit add ファイル名(もしくは-A）を実行して
新しいファイルをバージョン管理下に置く（ステージングする）必要がある
このあたり、「わかばちゃんと学ぶ git使い方入門」を読んでやっと意味が分かりました。
読んでよかった。
<h4>Merge (マージ)</h4>
masterブランチにチェックアウトしてmodify-READMEブランチをマージ
現状はこう
<pre class="lang:default decode:true " title="ターミナル" >#ログを簡略化して表示
$ git log --oneline
#現在地　masterブランチ、modify-READMEブランチもあるよ　コミットメッセージ：Improve the README file
44c4365 (HEAD -> master, modify-README) Improve the README file
#リモートリポジトリのmasterブランチはここまで　コミットメッセージ：Initialize repository
1c0f94a (origin/master) Initialize repository
</pre>
ブランチを削除するとこうなる
<pre class="lang:default decode:true " title="ターミナル" >#modify-READMEブランチを削除
$ git branch -d modify-README
Deleted branch modify-README (was 44c4365)
#ブランチを一覧表示して確認してみる
$ git branch
* master</pre>
<h4>git branch -D</h4>
通常、マージしていないとブランチの削除は出来ない
→マージ前にブランチが消えるとそのブランチで作業したコミットが消えてしまう為
これを利用して
「トピックブランチで作業していたがミスって訳が分からなくなった。
このブランチでやった事を無かったことにしたい！」
↓
マスターブランチに移動して「git branch -D 削除したいブランチ名」で無かったことに！
<h4>Push (プッシュ)</h4>
プッシュ後、GitHubを確認するとREADME.mdが更新されているのが確認できます
因みにGitのログを確認するとこんな感じ
<pre class="lang:default decode:true " title="ターミナル" >$ git log --oneline
44c4365 (HEAD ->; master, origin/master) Improve the README file
1c0f94a Initialize repository
#modify-READMEブランチの表示がなくなっている（削除した為・マージした後なのでコミットは残っている）
#ローカルとリモートの位置が揃っている</pre>
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

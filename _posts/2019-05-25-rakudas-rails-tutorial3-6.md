---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜（3.5と）3.6"
date: "2019-05-25 20:30:37"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["git", "Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 711
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial3-6/"
---
<h2>3.5 最後に</h2>
本文ではmasterブランチにcheckoutしてマージなんですが
せっかく（?）なのでプルリクエスト→Pullの流れをセルフでやってみようと思います
一人でやってると使い所がない感じなのでプルリクエストの練習として。
<pre class="lang:default decode:true " title="ターミナル" >$ git add -A
$ git commit -m "Finish static pages"
$ git push</pre>
GitHubのリポジトリからプルリクエスト
↓
プルリクをマージ
↓
<pre class="lang:default decode:true " title="ターミナル" >(static-pages) #リモートの状況を確認
$ git fetch
・
・
・
(static-pages) $ git log origin/master --oneline --graph
            #リモートのmasterはココ
*   0d2c868 (origin/master) Merge pull request #1 from rakuda-san-desu/static-pages
|\  
             #<del datetime="2019-05-28T06:20:39+00:00">リモート</del>ローカルの現在位置はココ
| * 2d5bc69 (HEAD -> static-pages, origin/static-pages) Finish static pages
・
・
・
(static-pages) $ git checkout master
Switched to branch 'master'
Your branch is behind 'origin/master' by 18 commits, and can be fast-forwarded.
  (use "git pull" to update your local branch)
#この辺のメッセージについては<a href="https://rakuda3desu.net/git-lesson04/">こちらのSECTION22</a>に
ec2-user:~/environment/sample_app (master) $ git pull
#git merge origin/masterの方が正しい（？）かもですが、上記メッセージで「git pullしてね」とあるのでpullで
・
・
・
(master) $ git log --oneline --graph
#ローカルとリモートが揃いました
*   0d2c868 (HEAD -> master, origin/master) Merge pull request #1 from rakuda-san-desu/static-pages
|\
| * 2d5bc69 (origin/static-pages, static-pages) Finish static pages
・
・
・
</pre>
また、本文通りherokuにもpushしておきます
<h3>3.5.1 本章のまとめ</h3>
本文ママ
<h2>3.6 高度なセットアップ</h2>
ブランチがmasterになっているか確認してからセットアップ！
<h3>3.6.1 minitest reporters</h3>
らくだ🐫にもできるRailsチュートリアルでは<a href="https://rakuda3desu.net/rrakudas-rails-tutorial3-2/">3.2</a>で静的ページ作成でブランチを切る前にセットアップしております
テスト結果に色が付くやつです
<h3>3.6.2 Guardによるテストの自動化</h3>
guardのGemは既にインストールしてあるので初期化する
<pre class="lang:default decode:true " title="ターミナル" >(master) $ bundle exec guard init
10:22:21 - INFO - Writing new Guardfile to /home/ec2-user/environment/sample_app/Guardfile
10:22:21 - INFO - minitest guard added to Guardfile, feel free to edit it</pre>
続いて、cloud9の場合はtmuxをインストールとあるのだけど
<a href="https://rakuda3desu.net/rakudas-rails-tutorial3-3/">3.3</a>でインストールしてあるのでした
↓
続いて/sample_app/Guardfileを本文の通りに書き換え
↓
ファイルツリーの歯車マークをクリック
Show Hidden Filesにチェックを入れて隠しファイルを表示させ.gitignoreを編集
<pre class="lang:ruby mark:(8-9) decode:true " title="/sample_app/.gitignore" >・
・
・
# Ignore Byebug command history file.
.byebug_history

#ファイルの一番下にハイライト部分の2行を追加
# Ignore Spring files.
/spring/*.pid</pre> 
<h4>Guardを使ってみる</h4>
<strong>作業用やサーバー用とは別のターミナルをもう一つ開いてコマンドを入力</strong>
<pre class="lang:default decode:true " title="ターミナル（別途開いたヤツ・ディレクトリの移動を忘れずに！）" > $ bundle exec guard
10:37:26 - INFO - Guard::Minitest 2.4.4 is running, with Minitest::Unit 5.10.3!
10:37:26 - INFO - Guard is now watching at '/home/ec2-user/environment/sample_app'
/home/ec2-user/.rvm/gems/ruby-2.6.0/gems/guard-2.13.0/lib/guard/jobs/pry_wrapper.rb:279: warning: method Pry#input_array is deprecated. Use Pry#input_ring instead
[1] guard(main)> 
#enterを打刻でテストを試す
・
・
・
  4/4: [=========================================] 100% Time: 00:00:00, Time: 00:00:00
Finished in 0.40261s
4 tests, 7 assertions, 0 failures, 0 errors, 0 skips</pre>
本文通りにadd,commitする
<h3>まとめとか感想</h3>
今回は纏め+αなのでさっくりと。
次は苦手意識の強いRubyのコードの章だよ！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

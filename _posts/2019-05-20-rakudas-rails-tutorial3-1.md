---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜3.1"
date: "2019-05-20 14:56:11"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者", "参考サイト"]
published: true
wordpress_id: 649
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial3-1/"
---
とうとうここからが本番！って感じですよね。
とは言えまだまだ、本番の入り口。がんばれ🐫
<h2>3.1 セットアップ</h2>
先ずはサンプルデータがあるとの事なのでフォークしておくことにしました
完成版と言う事なので途中でどのくらい参考になるかはわからないけども

で、Bitbucketでフォークってどうやるのかなって調べてみたんですが
英語がわからな過ぎて公式helpから見つけられず......
行きつく解説ページとはUIが変わってしまっていてよくわからず......
あちこちいじってどうにか見つけたのでメモっておきます

自分のアカウントでログインしておく
リンク先のリポジトリのページの左上の赤丸部分をクリック
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/3.1-1.png"><img class="alignnone wp-image-652 size-medium" src="https://rakuda3desu.net/wp-content/uploads/2019/05/3.1-1-300x235.png" alt="" width="300" height="235" /></a>
青いバーの「+」をクリック
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/3.1-2.png"><img class="alignnone wp-image-653 size-medium" src="https://rakuda3desu.net/wp-content/uploads/2019/05/3.1-2-300x287.png" alt="" width="300" height="287" /></a>
メニューからフォークをクリックでOKです
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/3.1-3-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/05/3.1-3-1-236x300.png" alt="" width="236" height="300" class="alignnone size-medium wp-image-677" /></a>
そしてセットアップ
<pre class="lang:default decode:true " title="ターミナル" >
$ cd ~/environment
#toy_appから続けて作業する場合はディレクトリの移動を忘れずに
$ rails _5.1.6_ new sample_app
$ cd sample_app/</pre>
Gemfileを指定通りに書き換えて
指示通りにオプションを付けてbundle install
ですが、らくだ🐫はエラーになったのでbundle updateです

その後、本文に従ってGitリポジトリの初期化
README.mdの更新とコミットを行います

本文ではBitbbucketだけどGitHubにリポジトリを作成してリモートに設定→push
<pre class="lang:default decode:true " title="ターミナル" >$ git remote add origin git@github.com:ユーザー名/sample_app.git
$ git push -u origin --all</pre>
以降本文の通りにデプロイまで
ついでにアプリ名も変更
<pre class="lang:default decode:true " title="ターミナル" >$ heroku rename rakuda3desu-sample-app</em></pre>
<h4>演習</h4>
動作確認のみなので省略
どちらもOKでした🐫
<h4>GitHubからのお知らせメール</h4>
と、ここまで来たところでGithubからのお知らせメールに気が付きました
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/3.1-4.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/05/3.1-4-300x145.png" alt="" width="300" height="145" class="alignnone size-medium wp-image-659" /></a>
「actionview」というGemにセキュリティの問題があるようです
GitHubにリポジトリを見に行ったらアラートが表示されていました
で、GitHubのアラート表示に書いてあったヒントに従ってGem actionviewのbundle updateをしようと思ったんですが
どうにもエラーなのでrailsをアップデートしましたよ
と言うか、下記を見ると初めからrailsのアップデートで良かった感じですね？
[blogcard url="https://qiita.com/moriwm77/items/b727eb41d95509535538"]

以下手順
 
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/Gemfile" >source 'https://rubygems.org'

#バージョンを5.1.6から5.1.6.2へ書き換え
gem 'rails',        '5.1.6.2'
gem 'puma',         '3.9.1'
gem 'sass-rails',   '5.0.6'
gem 'uglifier',     '3.2.0'
・
・
・</pre> 
<pre class="lang:default decode:true " title="ターミナル" >ターミナル$ bundle update
$ git commit -am"Rails update 5.1.6.2</em>"
$ git push</pre>
GitHubのアラートページをリロードするとエラーが解消されているのがわかる
<h3>まとめとか感想</h3>
本番とは言え準備だし2週目だからスラスラ行くよ～🐫
なんて思ってたら思わぬ伏兵に出会いました
とは言え解決できたので良かったでした
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

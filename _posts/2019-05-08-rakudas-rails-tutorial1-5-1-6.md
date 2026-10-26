---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜1.5（と1.6）"
date: "2019-05-08 21:21:47"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Heroku", "Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 592
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial1-5-1-6/"
---
<h2>1.5 デプロイする</h2>
<h3>1.5.1 Herokuのセットアップ</h3>
環境によって導入するgem、しないgemを指定する
本番環境のみにインストール
<pre class="lang:default decode:true " >group :production do
  gem 'pg', '0.20.0'
end</pre>
開発・テスト環境にインストール（開発環境には導入されないようにする）
<pre class="lang:default decode:true " >group :development, :test do
  gem 'sqlite3', '1.3.13'
  gem 'byebug',  '9.0.6', platform: :mri
end</pre>

<a href="https://railstutorial.jp/chapters/beginning?version=5.1#code-gemfile_pg_gem" rel="noopener noreferrer" target="_blank">本文</a>の通りにGemfileを書き換え
<pre class="lang:default decode:true " title="ターミナル" >#指定したgemをローカル環境に反映させないためのオプション
$ bundle install -without production
#更新をステージに追加、コミット
git commit -a -m "Update Gemfile for Heroku"</pre>
2週目なのでHerokuがインストールされている様な、環境を作り直しているのでされていない様な。
なのでバージョンを確認してみます。
<pre class="lang:default decode:true " title="ターミナル" >$ heroku --version
#インストールされていませんでした
bash: heroku: command not found
#Heroku CLIをインストールするまほうのじゅもん
$ source <(curl -sL https://cdn.learnenough.com/heroku_install)
$ heroku --version
#バージョンをチェックするとインストールされていることがわかる↓
heroku/7.24.3 linux-x64 node-v11.14.0
</pre>
後これ、どこに書こうかちょっと迷ったんだけど
この先チュートリアルを進めて、キリのいい所でいったん休止して
休憩したり次の日だったり、時間を置いて続きをやろうとするとHerokuのコマンドを受け付けなくなることがあると思います。
その時は<a href="https://rakuda3desu.net/rails-railstutorial211-241/" rel="noopener noreferrer" target="_blank">こちら</a>の「2.1｜Herokuにプッシュ出来ない」をご覧下さい。
<h4>Herokuサインイン</h4>
最初にやった時は少し手間取った記憶があるのだけど、現在（2019.05.08 Rails5.1第4版）は修正されてるっぽいですね。
2週目のらくだ🐫は本文と少し違う感じでした
2週目（Herokuのアカウント取得済み）のらくだ🐫の状況はこちら
<pre class="lang:default mark:(4-5) decode:true " >$ heroku login --interactive
#ログイン情報を入力してねって言うメッセージ
heroku: Enter your login credentials
Email: rakuda.san.desu@gmail.com
Password: ***************
#rakuda.san.desu@gmail.comでログインしたよっていうメッセージ
Logged in as rakuda.san.desu@gmail.com
#Herokuに新しいアプリケーションが作成される
$ heroku create</pre> 
<h3>1.5.2 Herokuにデプロイする (1)</h3>
本文ママ
<h3>1.5.3 Herokuにデプロイする (2)</h3>
<pre class="lang:default mark:(3-3) decode:true " title="ターミナル" >$ heroku create
Creating app... done, ⬢ quiet-retreat-○○○○○
https://quiet-retreat-○○○○○.herokuapp.com/ | https://git.heroku.com/quiet-retreat-○○○○○.git</pre> 
heroku createした際に生成されたアドレスをクリック、もしくは自身のHerokuのページから公開されたアプリを見ることが出来ます。
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/1.5.3-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/05/1.5.3-1-300x203.png" alt="" width="300" height="203" class="alignnone size-medium wp-image-594" /></a>
goodbye, world!のままだったわｗ
<h4>演習</h4>
コードの内容は<a href="https://rakuda3desu.net/rakudas-rails-tutorial-1-3/" rel="noopener noreferrer" target="_blank">1.3.4</a>の演習と同じです
演習内容に沿ってコードを書き換える毎にadd、commit、push、push herokuで
Heroku app側の表示も確認
<h3>1.5.4 Herokuコマンド</h3>
アプリケーション名の変更方法は本文そのまま

サンプルコードの実行はこちら
1.3.2で起動したrailsサーバーをストップさせている場合は、再度作業用とは別のターミナルでrails sを実行してサーバーを起動させておいてください
<pre class="lang:default decode:true " title="ターミナル" >#Railsコンソールを起動させる　
$ rails console
$ ('a'..'z').to_a.shuffle[0..7].join
 #ランダムな半角英小文字7文字の配列が生成される
 => "fntrdvjb" 
#コンソールを終了する際はquit</pre>
このコードの内容は後程(4章）出てくると思います
現時点では
「サーバーを起動して　サーバーとは別のターミナルでコンソールを起動
そこでコードを実行すると結果が返ってくる」って流れを体験できれば。

<a href="https://prog-8.com/" rel="noopener noreferrer" target="_blank">Progate</a>のRubyコースでコンソールを使う流れを学んでいるのだけど
らくだ🐫はそれをすっかり忘れていて素のターミナルにコードを張り付けて「エラーだが？🤔」となってました💧
<h4>演習</h4>
<pre class="lang:default decode:true " title="ターミナル" >$ heroku help
#以下ヘルプの表示
$ heroku logs
#以下、ずらっとログ表示
</pre>
直近に発生したイベントは人それぞれかと思いますので下記ページなどを参考に読み解いてみましょう
[blogcard url="http://info-i.net/heroku-log"]
<h2>1.6 最後に</h2>
まとめなので省略
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

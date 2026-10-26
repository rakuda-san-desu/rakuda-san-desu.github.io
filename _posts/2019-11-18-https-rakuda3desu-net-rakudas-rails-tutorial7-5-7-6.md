---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜7.5（と7.6）"
date: "2019-11-18 09:52:04"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1044
wordpress_status: "publish"
permalink: "/https-rakuda3desu-net-rakudas-rails-tutorial7-5-7-6/"
---
<h2>7.5 プロのデプロイ</h2>
実際に動かせるアプリのデプロイ！
準備として現時点までをmasterブランチにマージしておく
<h3>7.5.1 本番環境でのSSL</h3>
<blockquote class="quote-rort">本章で開発したユーザー登録フォームで送信すると、名前やメールアドレス、パスワードといったデータがネットワーク越しに流されていきます。実は、このようなネットワークに流れるデータは途中で捕捉できるため、扱いには注意が必要です。これはサンプルアプリケーションの本質的なセキュリティ上の欠陥です。そしてこれを修正するためにSecure Sockets Layer (SSL)を使います 12。これはローカルのサーバからネットワークに流れる前に、大事な情報を暗号化する技術です。</blockquote>
<pre class="lang:ruby mark:(8-8) decode:true " title="/sample_app/config/environments/production.rb（本番環境用の設定ファイル）" >・
・
・
  # Force all access to the app over SSL, use Strict-Transport-Security, and use secure cookies.
  #現在の環境では48行目あたり
  #コメントアウトされているので有効にする
  #本番環境で本番環境ではSSLを使うようにするための設定
  config.force_ssl = true</pre> 
本番環境でSSLを使えるようにするためにはドメインごとにSSLのセットアップが必要
→Herokuのサブドメインを使用することでHerokuのSSL証明書に便乗
→結果自動的にSSLが有効化されている
→独自ドメインを使いたい場合は環境に合わせて設定
<h3>7.5.2 本番環境用のWebサーバー</h3>
<blockquote class="quote-rort">Herokuのデフォルトでは、Rubyだけで実装されたWEBrickというWebサーバを使っています。WEBrickは簡単にセットアップできたり動せることが特長ですが、著しいトラフィックを扱うことには適していません。つまり、WEBrickは本番環境として適切なWebサーバではありません。</blockquote>
PumaというWebサーバに置き換える
Rails5以降デフォルトで使えるようになっているのでRails側では何もしなくてよい
<h4>Heroku上でPumaのプロセスを走らせる</h4>
ルートディレクトリに設定ファイルを作る
<pre class="lang:default decode:true " title="ターミナル" >$ touch Procfile</pre>
<pre class="lang:default decode:true " title="/sample_app/Procfile" >web: bundle exec puma -C config/puma.rb</pre>
<h3>7.5.3 本番環境へのデプロイ</h3>
<pre class="lang:default decode:true " title="ターミナル" >$ rails test
$ git add -A
$ git commit -m "Use SSL and the Puma webserver in production"
$ git push
$ git push heroku
$ heroku run rails db:migrate</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>ブラウザから本番環境 (Heroku) にアクセスし、SSLの鍵マークがかかっているか、URLがhttpsになっているかどうかを確認してみましょう。</li>
<li>本番環境でユーザーを作成してみましょう。Gravatarの画像は正しく表示されているでしょうか?</li>
</ol>
</blockquote>
<a href="https://rakuda3desu.net/wp-content/uploads/2019/11/7.5.3-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/11/7.5.3-1.png" alt="" width="538" height="414" class="alignnone size-full wp-image-1047" /></a>
<ol>
<li>鍵マーク〇、URLがhttpsになっているか〇</li>
<li>本番環境でのユーザー作成〇、Gravatarの画像〇</li>
</ol>
<h2>7.6 最後に</h2>
8章9章では承認システムを導入して、ユーザーがログインとログアウトを出来る様にする
10章ではどのユーザーも自分のアカウントを更新できるようにする
また、管理者がユーザー削除を出来る様にする
以上でUsersリソースにRESTアクションがすべて実装される！
<h3>7.6.1 本章のまとめ</h3>
本文参照の事
<h4>まとめとか感想</h4>
作業的な感じなのでサラっとまとめちゃったけど
実際アプリを公開するときには結構重要なんじゃなかろうか。とか。

次からとうとう8章ですね！
益々頑張っていきたいと思います🐫
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

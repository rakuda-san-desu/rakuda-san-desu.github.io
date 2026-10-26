---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜11.4（と11.5）"
date: "2020-04-14 17:23:38"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1395
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial11-4-11-5/"
---
<h2>11.4 本番環境でのメール送信</h2>
開発環境でのアカウント有効化の流れが整ったので、本番環境でも実際にメールを送信できるようにする。
開発環境では日本語が混ざるとメールが文字化けしてたけど、本番環境では行けるらしいので期待してる！
<h4>SendGrid</h4>
SendGridというHerokuのアドオンを利用してアカウント検証をする
<blockquote class="quote-rort">本番環境からメール送信するために、「SendGrid」というHerokuアドオンを利用してアカウントを検証します (このアドオンを利用するためにはHerokuアカウントにクレジットカードを設定する必要がありますが、アカウント検証では料金は発生しません)。本チュートリアルでは、「starter tier」というサービスを使うことにします。</blockquote>
 Herokuのアプリケーションにsendgridを追加
<pre class="toolbar:2 lang:default decode:true " >heroku addons:create sendgrid:starter</pre> 
production環境のSMTPに情報を記入する→本番Webサイトのアドレスをhost変数に定義
<pre class="start-line:62 lang:ruby decode:true " title="/sample_app/config/environments/production.rb" >・
・
・
  # Ignore bad email addresses and do not raise email delivery errors.
  # Set this to true and configure the email server for immediate delivery to raise delivery errors.
  config.action_mailer.raise_delivery_errors = true
  config.action_mailer.delivery_method = :smtp
  #↓この行に注目ですよ後でトラブります
  host = '<your heroku app>.herokuapp.com'
  config.action_mailer.default_url_options = { host: host }
  ActionMailer::Base.smtp_settings = {
    :address        => 'smtp.sendgrid.net',
    :port           => '587',
    :authentication => :plain,
    :user_name      => ENV['SENDGRID_USERNAME'],
    :password       => ENV['SENDGRID_PASSWORD'],
    :domain         => 'heroku.com',
    :enable_starttls_auto => true
  }
・
・
・
</pre> 
<blockquote class="quote-rort">リスト 11.41のメール設定にはSendGridアカウントのuser_nameとpassword設定を記入する行もありますが、そこには記入せず、必ず環境変数「ENV」に設定するよう十分ご注意ください。本番運用するアプリケーションでは、暗号化されていないIDやパスワードのような重要なセキュリティ情報は「絶対に」ソースコードに直接書き込まないでください。</blockquote>
暗号化されていないIDやパスワードを記入する必要がある場合<strong>環境変数で設定する！</strong>
11章ではSendGridアドオンが自動的に設定してくれるけど、13章では自分で設定する必要があるらしい。
<h4>リスト 11.41で扱うHerokuの環境変数</h4> 
<pre class="lang:default decode:true " title="ターミナル" ># SENDGRID_USERNAMEを調べる
$ heroku config:get SENDGRID_USERNAME
# SENDGRID_PASSWORDを調べる
$ heroku config:get SENDGRID_PASSWORD</pre> 
<h4>本番環境でユーザー登録機能を確認</h4>
ひとまずいつもの流れでmasterにマージしてherokuにデプロイ
<h4>一筋縄ではいかない認証メール機能</h4>
1週目の時も散々苦労したんだけど、今回も本番環境での認証メール機能がスムーズに動きません
<ol>
	<li>Herokuデプロイ</li>
	<li>rootにアクセス</li>
	<li>サインイン</li>
	<li>メール届く</li>
	<li>クリックする</li>
	<li>アクセスできません！</li>
</ol>
<img src="https://rakuda3desu.net/wp-content/uploads/2020/04/11.4-1.png" alt="" width="476" height="320" class="alignnone size-full wp-image-1399" />
これは、ホストの設定が、おかしい？
コードを見直してみましょう。 
<pre class="toolbar:2 lang:default decode:true " >
# こ　れ　だ　!!!!!
host = '<your heroku app>.herokuapp.com'

# これ↑をこう↓します
host = '自分の環境に合わせて変更.herokuapp.com'
</pre> 
出来た！
<img src="https://rakuda3desu.net/wp-content/uploads/2020/04/11.4-2.png" alt="" width="450" height="306" class="alignnone size-full wp-image-1400" />
因みにユーザー名・本文ともに日本語が入ってOKでした（絵文字も行けた）
<h4>演習</h4>
<ol>
	<li>実際に本番環境でユーザー登録をしてみましょう。ユーザー登録時に入力したメールアドレスにメールは届きましたか?</li>
	<li>メールを受信できたら、実際にメールをクリックしてアカウントを有効化してみましょう。また、Heroku上のログを調べてみて、有効化に関するログがどうなっているのか調べてみてください。ヒント: ターミナルからheroku logsコマンドを実行してみましょう。</li>
</ol>
1.
動作確認のみにて省略

2.
メールのリンクをクリックで有効化完了！

有効化に関するログざっくり
↓
サインインページから情報を受け取る
有効化URLを乗せたメールを送信
有効化URLからgetのリクエスト→AccountActivationsController#edit（emailとトークンの値を渡す）
認証完了！"activated" = 't', "activated_at" = '認証時のタイムスタンプ'にDBが更新される
ユーザーページにリダイレクトされる
<h4>11.5 最後に</h4>
アカウント有効化を実装できたのでサンプルアプリケーションの「ユーザー登録」「ログイン」「ログアウト」の仕組みがほぼ完成！
次章でパスワードの再設定機能を実装したらパーフェクト！との事！
<h3>まとめとか感想</h3>
メール認証長かった！難しかった！！
再設定の方は仕組みが似ているとの事なのでもうちょっとサクサク進められたらいいなって思います。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

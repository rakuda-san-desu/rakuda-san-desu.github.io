---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜12.2"
date: "2020-04-25 15:42:28"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1429
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial12-2/"
---
※毎回書いたほうがいい気がするので書いておきます※
平文の部分をI18nでちょこちょこ日本語化しているのですが
その部分に関しては特に記述していなくてもたまにコードが違ったりしています。
t('.hogehoge')って部分です
どうぞお気になさらず。
<h2>12.2 パスワード再設定のメール送信</h2>
パスワードの再設定に関するメールの部分を作っていく
11章でUserメイラーを生成した際にデフォルトのpassword_resetメソッドなどもまとめて生成されているので
それを使って定義していく！
<h3>12.2.1 パスワード再設定のメールとテンプレート</h3>
<a href="https://railstutorial.jp/chapters/account_activation?version=5.1#sec-activation_test_and_refactoring" title="Railsチュートリアル本文11.3.3" rel="noopener noreferrer" target="_blank">11.3.3</a>で行ったリファクタリングと同様の作業を行っていく
<blockquote class="quote-rort">最初にUserメイラーにpassword_resetメソッドを作成し (リスト 12.7)、続いて、テキストメールのテンプレート (リスト 12.8) とHTMLメールのテンプレート (リスト 12.9) をそれぞれ定義します。</blockquote>
Userメイラーにpassword_resetメソッドを作成
<pre class="lang:ruby mark:(10-15) decode:true " title="/sample_app/app/mailers/user_mailer.rb" >class UserMailer < ApplicationMailer

  def account_activation(user)
    # インスタンス変数を定義
    @user = user
    # user.emailにタイトルが"t('.account_activation_title')"のメールを送信
    mail to: user.email, subject: t('.account_activation_title')
  end

  def password_reset(user)
    # インスタンス変数を定義
    @user = user
    # user.emailにタイトルが"t('.password_reset')"のメールを送信
    mail to: user.email, subject: t('.password_reset')
  end
end</pre>  
パスワード再設定のテンプレート (テキスト)
<pre class="lang:default decode:true " title="/sample_app/app/views/user_mailer/password_reset.text.erb" ><%= t('user_mailer.click_the_link') %>

<%= edit_password_reset_url(@user.reset_token, email: @user.email) %>

<%= t('user_mailer.expire_in_two_hours') %>

<%= t('user_mailer.ignore_this_email') %></pre> 
パスワード再設定のテンプレート (HTML)
<pre class="lang:default decode:true " title="/sample_app/app/views/user_mailer/password_reset.html.erb" ><h1><%= t('.reset_msg') %></h1>

<p><%= t('user_mailer.click_the_link') %></p>

<%= link_to t('.reset_msg'), edit_password_reset_url(@user.reset_token,
                                                      email: @user.email) %>

<p><%= t('user_mailer.expire_in_two_hours') %></p>

<p><%= t('user_mailer.ignore_this_email') %></p></pre> 
プレビューも見られるように設定 
<pre class="lang:ruby mark:（17-24） decode:true " title="/sample_app/test/mailers/previews/user_mailer_preview.rb" ># Preview all emails at http://localhost:3000/rails/mailers/user_mailer
class UserMailerPreview < ActionMailer::Preview

  # Preview this email at
  # https://5f7b98a0b66149149ecd6edabf11f3d6.vfs.cloud9.ap-northeast-1.amazonaws.com/rails/mailers/user_mailer/account_activation
  def account_activation
    # userにDBの1番目のユーザーを代入
    user = User.first
    # userのactivation_tokenに新しい有効化トークンを代入
    user.activation_token = User.new_token
    # UserMailerクラスのaccount_activationメソッドを呼び出し（引数にuserを渡す）
    UserMailer.account_activation(user)
  end

  # Preview this email at
  # https://5f7b98a0b66149149ecd6edabf11f3d6.vfs.cloud9.ap-northeast-1.amazonaws.com/rails/mailers/user_mailer/password_reset
  def password_reset
    # userにDBの一番目のユーザーを代入
    user = User.first
    # userのreset_tokenに新しい有効化トークンを代入
    user.reset_token = User.new_token
    # UserMailerクラスのpassword_resetメソッドを呼び出し（引数にuserを渡す）
    UserMailer.password_reset(user)
  end
end</pre> 
16行目のアドレスにアクセスするとプレビューを確認できる
→できる！
<h4>実際の動作を確認</h4>
日本語のままだと化けるので/sample_app/config/application.rbで言語をenにしておく
<pre class="toolbar:2 lang:default decode:true " >config.i18n.default_locale = :en</pre> 
パスワードの再設定フォームに正しいアドレスを入力してボタンを押すとこうなる
（メールを確認してねって感じのフラッシュメッセージが表示されているはずだけどI18nで英語の設定してないから翻訳エラーな文章）
<img src="https://rakuda3desu.net/wp-content/uploads/2020/04/12.2-1.png" alt="" width="1246" height="483" class="alignnone size-full wp-image-1441" />
サーバーのログはこうなる
（I18nで英語の設定してないから略） 
<pre class="lang:default decode:true " title="サーバーログのメーラー部分" >Sent mail to example@railstutorial.org (7.7ms)
Date: Thu, 23 Apr 2020 14:50:07 +0900
From: noreply@example.com
To: example@railstutorial.org
Message-ID: <5ea12c8f8cdf2_1e1b1ec0f4431668@ip-172-31-40-65.mail>
Subject: translation missing:
 en.user_mailer.password_reset.password_reset_title
Mime-Version: 1.0
Content-Type: multipart/alternative;
 boundary="--==_mimepart_5ea12c8f8b8b4_1e1b1ec0f4431542";
 charset=UTF-8
Content-Transfer-Encoding: 7bit


----==_mimepart_5ea12c8f8b8b4_1e1b1ec0f4431542
Content-Type: text/plain;
 charset=UTF-8
Content-Transfer-Encoding: 7bit

<span class="translation_missing" title="translation missing: en.user_mailer.click_the_link">Click The Link</span>

https://5f7b98a0b66149149ecd6edabf11f3d6.vfs.cloud9.ap-northeast-1.amazonaws.com/password_resets/qT0cwyy9La2jniwmC_0aTA/edit?email=example%40railstutorial.org

<span class="translation_missing" title="translation missing: en.user_mailer.expire_in_two_hours">Expire In Two Hours</span>

<span class="translation_missing" title="translation missing: en.user_mailer.ignore_this_email">Ignore This Email</span>

----==_mimepart_5ea12c8f8b8b4_1e1b1ec0f4431542
Content-Type: text/html;
 charset=UTF-8
Content-Transfer-Encoding: 7bit

<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <style>
      /* Email styles need to be inline */
    </style>
  </head>

  <body>
    <h1><span class="translation_missing" title="translation missing: en.user_mailer.password_reset.reset_msg">Reset Msg</span></h1>

<p><span class="translation_missing" title="translation missing: en.user_mailer.click_the_link">Click The Link</span></p>

<a href="https://5f7b98a0b66149149ecd6edabf11f3d6.vfs.cloud9.ap-northeast-1.amazonaws.com/password_resets/qT0cwyy9La2jniwmC_0aTA/edit?email=example%40railstutorial.org"><span class="translation_missing" title="translation missing: en.user_mailer.password_reset.reset_msg">Reset Msg</span></a>

<p><span class="translation_missing" title="translation missing: en.user_mailer.expire_in_two_hours">Expire In Two Hours</span></p>

<p><span class="translation_missing" title="translation missing: en.user_mailer.ignore_this_email">Ignore This Email</span></p>
  </body>
</html>

----==_mimepart_5ea12c8f8b8b4_1e1b1ec0f4431542--</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>ブラウザから、送信メールのプレビューをしてみましょう。「Date」の欄にはどんな情報が表示されているでしょうか?</li>
	<li>パスワード再設定フォームから有効なメールアドレスを送信してみましょう。また、Railsサーバーのログを見て、生成された送信メールの内容を確認してみてください。</li>
	<li>コンソールに移り、先ほどの演習課題でパスワード再設定をしたUserオブジェクトを探してください。オブジェクトを見つけたら、そのオブジェクトが持つreset_digestとreset_sent_atの値を確認してみましょう。</li>
</ol>
</blockquote>
<ol>
	<li>再設定フォームの送信ボタンを押した日時</li>
	<li>emailと再設定トークンの値を持ったURLが記載されたメール（textとHTML）が送信されている</li>
	<li>reset_digest　→　値が入っている
reset_sent_at　→　再設定フォームの送信ボタンを押した日時の値が入っている</li>
</ol>
<h3>12.2.2 送信メールのテスト</h3>
メイラーメソッドのテスト
（再設定の時とほぼ同じ→本文へのテストも同じくtextとHTMLで分ける）
（言語はjaに戻しておく） 
<pre class="lang:ruby decode:true " title="/sample_app/test/mailers/user_mailer_test.rb" >・
・
・
  test "password_reset" do
    user = users(:michael)
    user.reset_token = User.new_token
    mail = UserMailer.password_reset(user)
    assert_equal "Sample App アカウント再設定メール", mail.subject
    assert_equal [user.email], mail.to
    assert_equal ["noreply@example.com"], mail.from
    assert_match user.reset_token,        mail.text_part.body.encoded
    assert_match user.reset_token,        mail.html_part.body.encoded
    assert_match CGI.escape(user.email),  mail.text_part.body.encoded
    assert_match CGI.escape(user.email),  mail.html_part.body.encoded
  end
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>メイラーのテストだけを実行してみてください。このテストは greenになっているでしょうか</li>
	<li>リスト 12.12にある２つ目のCGI.escapeを削除すると、テストが redになることを確認してみましょう</li>
</ol>
</blockquote>
<ol>
	<li>GREEN</li>
	<li>RED</li>
</ol>
<h3>まとめとか感想</h3>
（おうちの都合で）間は結構空いちゃったけど、内容は割とさっくり。
実際本文も短めですもんね。
なるべくサクサク進みたい！と思っています！！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

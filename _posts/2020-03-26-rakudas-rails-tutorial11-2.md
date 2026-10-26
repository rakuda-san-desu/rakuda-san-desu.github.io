---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜11.2"
date: "2020-03-26 14:07:17"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1342
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial11-2/"
---
<h2>11.2 アカウント有効化のメール送信</h2>
準備ができたので、アカウント有効化メールに必要なコードを追加していく
<blockquote class="quote-rort">このメソッドではAction Mailerライブラリを使ってUserのメイラーを追加します。このメイラーはUsersコントローラのcreateアクションで有効化リンクをメール送信するために使います。</blockquote>
<h4>Action Mailerライブラリとなはんぞ</h4>
Railsを使ってメールを送信する仕組み（雑）
アクションやviewからメールを送信できるようになる
メイラーの動作はコントローラのアクションと似ているしテンプレートはviewに似ている
詳しくはこちらとか→<a href="https://railsguides.jp/action_mailer_basics.html" rel="noopener noreferrer" target="_blank">Railsガイド｜Action Mailer の基礎</a>
<h3>11.2.1 送信メールのテンプレート</h3>
メイラーもrails generateで生成する
<pre class="lang:default mark:(4-4,6-9) decode:true " title="ターミナル" > $ rails generate mailer UserMailer account_activation password_reset
Running via Spring preloader in process 5792
      create  app/mailers/user_mailer.rb
      invoke  erb
      create    app/views/user_mailer
      create    app/views/user_mailer/account_activation.text.erb
      create    app/views/user_mailer/account_activation.html.erb
      create    app/views/user_mailer/password_reset.text.erb
      create    app/views/user_mailer/password_reset.html.erb
      invoke  test_unit
      create    test/mailers/user_mailer_test.rb
      create    test/mailers/previews/user_mailer_preview.rb</pre> 
この章で必要なaccount_activationメソッドと12章で必要なpassword_resetメソッドが生成されている（app/mailers/user_mailer.rb内）
また、ビューのテンプレートがテキストメール用とHTMLメール用の2つずつ生成されている
<h4>生成されたメイラー</h4>
<pre class="lang:ruby decode:true " title="/sample_app/app/mailers/application_mailer.rb" >class ApplicationMailer < ActionMailer::Base
  #送信元アドレスはアプリケーション全体で共通
  default from: 'from@example.com'
  layout 'mailer'
end
</pre> 
生成されたUserメイラー 
<pre class="lang:ruby decode:true " title="/sample_app/app/mailers/user_mailer.rb" >class UserMailer < ApplicationMailer

  # Subject can be set in your I18n file at config/locales/en.yml
  # with the following lookup:
  #
  #   en.user_mailer.account_activation.subject
  #
  #この章で使うaccount_activationメソッド
  def account_activation
    @greeting = "Hi"

    mail to: "to@example.org"
  end

  # Subject can be set in your I18n file at config/locales/en.yml
  # with the following lookup:
  #
  #   en.user_mailer.password_reset.subject
  #
  #12章で使うpassword_reset
  def password_reset
    @greeting = "Hi"

    mail to: "to@example.org"
  end
end
</pre> 
<h4>メイラーをカスタマイズしていく</h4>
<pre class="lang:ruby mark:(2-3) decode:true " title="/sample_app/app/mailers/application_mailer.rb" >class ApplicationMailer < ActionMailer::Base
  # 送信元のメールアドレスを設定
  default from: "noreply@example.com"
  layout 'mailer'
end
</pre>  
<pre class="lang:ruby mark:(3-8) decode:true " title="/sample_app/app/mailers/user_mailer.rb" >class UserMailer < ApplicationMailer

  def account_activation(user)
    # インスタンス変数を定義
    @user = user
    # user.emailにタイトルが"Account activation"のメールを送信
    mail to: user.email, subject: "Account activation"
  end

  def password_reset
    @greeting = "Hi"

    mail to: "to@example.org"
  end
end
</pre> 
<h4>メイラーのview</h4>
テンプレートビューは通常のビューと同じでERBで自由にカスタマイズできる。
<h4>viewに含めるコード</h4>
ユーザー名を含む挨拶文と有効化リンクを追加する。
→Railsサーバーでユーザーをメールアドレスで検索して有効化トークンを認証できるようにするため、リンクにはメールアドレスとトークンを両方含めておく必要があります 
<pre class="toolbar:2 lang:default decode:true " >#このコードを考える
edit_account_activation_url(@user.activation_token, ...)

#このメソッドは
edit_user_url(user)
#↓このURLを生成
http://www.example.com/users/1/edit
#これに対応するアカウント有効化リンクのベースURLは↓
#ランダム文字列はnew_tokenメソッドで生成されたもの
http://www.example.com/account_activations/q5lt38hQDc_959PVoo6b7A/edit
</pre> 
<blockquote class="quote-rort">URLで使えるようにBase64でエンコードされています。これはちょうど/users/1/editの「1」のようなユーザーIDと同じ役割を果たします。このトークンは、特にAccountActivationsコントローラのeditアクションではparamsハッシュでparams[:id]として参照できます。</blockquote>
さらにクエリパラメータを使ってメールアドレスを組み込む 
<pre class="lang:default decode:true " >account_activations/q5lt38hQDc_959PVoo6b7A/edit?email=foo%40example.com
</pre> 
<h4>クエリパラメータとはなんぞ</h4>
<blockquote class="quote-rort">クエリパラメータとは、URLの末尾で疑問符「?」に続けてキーと値のペアを記述したものです</blockquote>
上記で言えば<strong>edit?email=foo%40example.com</strong>の部分
edit?に続けてキー「email」値「foo%40example.com」を呼んでいる
「%40」は「＠」のエスケープなので実際呼び出しているのは「foo@example.com」
Railsでクエリパラメータを設定するには、名前付きルートに対して次のようなハッシュを追加する
<pre class="toolbar:2 lang:ruby decode:true " >edit_account_activation_url(@user.activation_token, email: @user.email)</pre> 
こうすることでRailsが特殊な文字を自動的にエスケープしてくれるし、コントローラでparams[:email]からメールアドレスを取り出すときには、自動的にエスケープを解除してくれる。優しい。
<h4>アカウント有効化メールのview</h4>
mailers/user_mailer.rbで定義した@userインスタンス変数、editへの名前付きルート、ERBを組み合わせて、必要なリンクを作成する
<pre class="lang:ruby decode:true " title="/sample_app/app/views/user_mailer/account_activation.text.erb" >Hi <%= @user.name %>,

Welcome to the Sample App! Click on the link below to activate your account:

<!--host/account_activations/トークン/edit?email=メアド　へのURLが生成される-->
<%= edit_account_activation_url(@user.activation_token, email: @user.email) %></pre>  
<pre class="lang:ruby decode:true " title="/sample_app/app/views/user_mailer/account_activation.html.erb" ><h1>Sample App</h1>

<p>Hi <%= @user.name %>,</p>

<p>
Welcome to the Sample App! Click on the link below to activate your account:
</p>

<!--表示テキストが"Activate"　host/account_activations/トークン/edit?email=メアド　へのリンクが生成される-->
<%= link_to "Activate", edit_account_activation_url(@user.activation_token,
                                                    email: @user.email) %></pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>コンソールを開き、CGIモジュールのescapeメソッド (リスト 11.15) でメールアドレスの文字列をエスケープできることを確認してみましょう。このメソッドで"Don't panic!"をエスケープすると、どんな結果になりますか?</li>
</ol>
</blockquote> 
<pre class="lang:default decode:true " title="リスト 11.15: CGI.escapeを使ってエスケープする" >>> CGI.escape('foo@example.com')
=> "foo%40example.com"</pre>  
<pre class="lang:default decode:true " title="コンソール" >>> CGI.escape("Don't panic!")
#特殊文字が自動的にエスケープされる
=> "Don%27t+panic%21"</pre> 
<h3>11.2.2 送信メールのプレビュー</h3>
<blockquote class="quote-rort">Railsでは、特殊なURLにアクセスするとメールのメッセージをその場でプレビューすることができます。メールを実際に送信しなくてもよいので大変便利です。これを利用するには、アプリケーションのdevelopment環境の設定に手を加える必要があります</blockquote>


<pre class="lang:ruby decode:true " title="/sample_app/config/environments/development.rb" >Rails.application.configure do
  .
  .
  .
  config.action_mailer.raise_delivery_errors = true
  config.action_mailer.delivery_method = :test
  host = 'example.com' # ここをコピペすると失敗します。自分の環境に合わせてください。
  config.action_mailer.default_url_options = { host: host, protocol: 'https' }
  .
  .
  .
end

#hostの部分は環境に合わせて設定
#例えばcloud9
host = '***.vfs.cloud9.ap-northeast-1.amazonaws.com'
config.action_mailer.default_url_options = { host: host, protocol: 'https' }

# ローカル環境
host = 'localhost:3000'
config.action_mailer.default_url_options = { host: host, protocol: 'http' }</pre> 
developmentサーバーを再起動して設定を読み込んだらUserメイラーのプレビューファイルを更新する 
<pre class="lang:default mark:(7-12) decode:true " title="/sample_app/test/mailers/previews/user_mailer_preview.rb" ># Preview all emails at http://localhost:3000/rails/mailers/user_mailer
class UserMailerPreview < ActionMailer::Preview

  # Preview this email at
  # https://***.ap-northeast-1.amazonaws.com/rails/mailers/user_mailer/account_activation
  def account_activation
    # userにDBの1番目のユーザーを代入
    user = User.first
    # userのactivation_tokenに有効化トークンを代入
    user.activation_token = User.new_token
    # UserMailerクラスのaccount_activationメソッドを呼び出し（引数にuserを渡す）
    UserMailer.account_activation(user)
  end

  # Preview this email at
  # https://***.vfs.cloud9.ap-northeast-1.amazonaws.com/rails/mailers/user_mailer/password_reset
  def password_reset
    UserMailer.password_reset
  end
end</pre> 
以上で指定のURLでアカウント有効化メールをプレビューできる！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>Railsのプレビュー機能を使って、ブラウザから先ほどのメールを表示してみてください。「Date」の欄にはどんな内容が表示されているでしょうか?</li>
</ol>
</blockquote>
<ol>
	<li>アクセスした日時</li>
</ol>
<h3>11.2.3 送信メールのテスト</h3>
プレビューのテストを作成する
自動生成されているテストを利用すれば割と簡単との事
<pre class="lang:ruby mark:(9-9,17-17) decode:true " title="/sample_app/test/mailers/user_mailer_test.rb（自動生成されているテスト）" >require 'test_helper'

class UserMailerTest < ActionMailer::TestCase
  test "account_activation" do
    mail = UserMailer.account_activation
    assert_equal "Account activation", mail.subject
    assert_equal ["to@example.org"], mail.to
    assert_equal ["from@example.com"], mail.from
    assert_match "Hi", mail.body.encoded
  end

  test "password_reset" do
    mail = UserMailer.password_reset
    assert_equal "Password reset", mail.subject
    assert_equal ["to@example.org"], mail.to
    assert_equal ["from@example.com"], mail.from
    assert_match "Hi", mail.body.encoded
  end

end
</pre> 
<h4>assert_matchとはなんぞ</h4>
たびたび出てくるアサーションというヤツ
<a href="https://railsguides.jp/testing.html#%E5%88%A9%E7%94%A8%E5%8F%AF%E8%83%BD%E3%81%AA%E3%82%A2%E3%82%B5%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3" rel="noopener noreferrer" target="_blank">Railsガイドで見る</a>
<blockquote>assert_match( regexp, string, [msg] ) → stringは正規表現 (regexp) にマッチすると主張する。</blockquote>
正規表現で文字列をテストできるメソッド
<pre class="toolbar:2 lang:default decode:true " >assert_match 'foo', 'foobar'      # true
assert_match 'baz', 'foobar'      # false
assert_match /\w+/, 'foobar'      # true
assert_match /\w+/, '$#!*+@'      # false</pre> 
<h4>メイラーのプレビューのテストを実装</h4>
（日本語化しているので本文と多少違います）
<pre class="lang:ruby decode:true " title="/sample_app/test/mailers/user_mailer_test.rb" >require 'test_helper'

class UserMailerTest < ActionMailer::TestCase

  test "account_activation" do
    # userにテストユーザーmichaelを代入
    user = users(:michael)
    user.activation_token = User.new_token
    mail = UserMailer.account_activation(user)
    # "Sample App アカウント認証メール"とmail.subjectが等しい
    assert_equal "Sample App アカウント認証メール", mail.subject
    # [user.email]と mail.toが等しい
    assert_equal [user.email], mail.to
    # ["noreply@example.com"]と mail.fromが等しい
    assert_equal ["noreply@example.com"], mail.from
    # user.nameが本文に含まれている
    assert_match user.name,               mail.body.encoded
    # user.activation_tokenが本文に含まれている
    assert_match user.activation_token,   mail.body.encoded
    # 特殊文字をエスケープしたuser.mailが本文に含まれている
    assert_match CGI.escape(user.email),  mail.body.encoded
  end
end</pre> 
<h4>mail.body.encodedとは何ぞ</h4>
メール本文をメールの本文を（US-ASCIIに）エンコード
で、テストの前にテストファイル内のドメイン名を<strong>正しく</strong>設定する
<pre class="lang:default mark:(9-10) decode:true " >Rails.application.configure do
・
・
・
  # Tell Action Mailer not to deliver emails to the real world.
  # The :test delivery method accumulates sent emails in the
  # ActionMailer::Base.deliveries array.
  config.action_mailer.delivery_method = :test
  #自身の環境に合わせて正しく設定！
  config.action_mailer.default_url_options = { host: '***.vfs.cloud9.ap-northeast-1.amazonaws.com' }
・
・
・
end
</pre> 
<h4>しかしリスト11.20のテストはREDなのです！</h4>
テストの結果
<pre class="lang:default decode:true " title="ターミナル" >FAIL["test_account_activation", UserMailerTest, 1.1427533719997882]
 test_account_activation#UserMailerTest (1.14s)
        Expected /Michael\ Example/ to match # encoding: US-ASCII
</pre> 
よくわからないのだけど文字コードがマッチしないって感じです？と思ったので
<pre class="toolbar:2 lang:default decode:true " >#この部分を
mail.body.encoded

#それぞれこうしてテスト
mail.body</pre> 
帰ってきたエラーはこう 
<pre class="toolbar:2 lang:default decode:true " >TypeError:         TypeError: no implicit conversion of Mail::Body into String</pre> 
 →mail.bodyを（暗黙で）string（文字列）に変換する方法がないよ
変換は必要な模様。
そして最初のエラー文でググってみましたところ見つけたのがこちらなど
[blogcard url="https://hmng117.hatenablog.com/entry/2019/01/08/191000"]
Rubyは基本UTF-8なので比較元（第一引数）はUTF-8だけど、比較先（第2引数）をわざわざUS-ASCIIにエンコードしてるからエラーになる
なのでUTF-8にエンコードして比較しますって感じですね。
上記を参照に該当部分を書き換え
<pre class="toolbar:2 lang:ruby decode:true " >    # user.nameが本文に含まれている
    assert_match user.name,               mail.body.to_s.encode("UTF-8")
    # user.activation_tokenが本文に含まれている
    assert_match user.activation_token,   mail.body.to_s.encode("UTF-8")
    # 特殊文字をエスケープしたuser.mailが本文に含まれている
    assert_match CGI.escape(user.email),  mail.body.to_s.encode("UTF-8")</pre> 
テストの結果がこれ
<pre class="toolbar:2 lang:default decode:true " >Expected /Michael\ Example/ to match "".</pre> 
マッチするものがありません。って感じですよね🤔
bodyの中身が見えていない状態。ということのようです。
実はここまでテストのドメインホストを本文通りに設定していて、自分の環境に合わせていなかったのでそのせい！！って思ったりもしたんだけど
修正しても結果は変わらずだったのでした。
なのでやり直し。
エラー文でググった中でもうイッコ気になった記事がこちら
[blogcard url="https://teratail.com/questions/221420"]
メールはTEXTメールとHTMLメールがあるから別々に指定しているのかー。
なんか解説的なものないかな？と思ったところでRailsガイドを見ればいいんじゃないですかね？！と思い立つ🐫
そして参考になる部分に出会いました！
<a href="https://railsguides.jp/testing.html#%E5%9F%BA%E6%9C%AC%E7%9A%84%E3%81%AA%E3%83%86%E3%82%B9%E3%83%88%E3%82%B1%E3%83%BC%E3%82%B9" rel="noopener noreferrer" target="_blank">Railsガイド｜12.2.2 基本的なテストケース</a>
<blockquote>email.body.to_sは、HTMLまたはテキストで1回出現した場合にのみ存在するとみなされます。メイラーがどちらも提供している場合は、 email.text_part.body.to_sやemail.html_part.body.to_sを用いてそれぞれの一部に対するフィクスチャをテストできます。</blockquote>
と、いう訳で、本文にのっとりコードを書き換え
<pre class="toolbar:2 lang:ruby decode:true " >    # user.nameが本文に含まれている
    assert_match user.name,               email.text_part.body.to_s
    assert_match user.name,               email.html_part.body.to_s
　　#下記も同様にそれぞれ</pre> 
テスト結果はこう
<pre class="toolbar:2 lang:default decode:true " >NameError:         NameError: undefined local variable or method `email' for #<UserMailerTest:0x0000000004a414c0>
        Did you mean?  mail</pre> 
emailじゃなくてmailでした！
直したらテストが通ったよ！！
そこで疑問なんだけど、ちゃんと場所を指定してあげれば通るって事？
なので元々のテストコード　mail.body.encoded　にそれぞれtext_partとhtml_partを追加してみました
最終的なテストコードはこうなった 
<pre class="lang:ruby decode:true " title="/sample_app/test/mailers/user_mailer_test.rb" >require 'test_helper'

class UserMailerTest < ActionMailer::TestCase

  test "account_activation" do
    # userにテストユーザーmichaelを代入
    user = users(:michael)
    user.activation_token = User.new_token
    mail = UserMailer.account_activation(user)
    # "Sample App アカウント認証メール"とmail.subjectが等しい
    assert_equal "Sample App アカウント認証メール", mail.subject
    # [user.email]と mail.toが等しい
    assert_equal [user.email], mail.to
    # ["noreply@example.com"]と mail.fromが等しい
    assert_equal ["noreply@example.com"], mail.from
    # user.nameが本文に含まれている
    assert_match user.name,               mail.text_part.body.encoded
    assert_match user.name,               mail.html_part.body.encoded
    # user.activation_tokenが本文に含まれている
    assert_match user.activation_token,   mail.text_part.body.encoded
    assert_match user.activation_token,   mail.html_part.body.encoded
    # 特殊文字をエスケープしたuser.mailが本文に含まれている
    assert_match CGI.escape(user.email),  mail.text_part.body.encoded
    assert_match CGI.escape(user.email),  mail.html_part.body.encoded
  end
end</pre> 
こちらもGREEN!
「じゃぁ最初のExpected /Michael\ Example/ to match # encoding: US-ASCIIは何だったの？」ってお気持ちはあるけど
メールのプレビューも意図した形になってるし、これで行くことにしましょうそうしましょう🐫
<h4>演習</h4>
動作確認のみにて省略
<h3>11.2.4 ユーザーのcreateアクションを更新</h3>
createアクションを完成させて実際にメイラーを使えるようにする！
<blockquote class="quote-rort">登録時のリダイレクトの挙動が変更されている点にご注意ください。変更前は、ユーザーのプロフィールページ (7.4) にリダイレクトしていましたが、アカウント有効化を実装するうえでは無意味な動作なので、 リダイレクト先をルートURLに変更してあります。</blockquote>
<h4>createアクションにアカウント有効化のコードを追加</h4> 
<pre class="lang:ruby mark:(7-10) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
  def create
    @user = User.new(user_params)
    if @user.save
      #UserMailerの引数に@userwp定義したaccount_activationメソッドで今すぐメールを送信
      UserMailer.account_activation(@user).deliver_now
      flash[:info] = t('.check_your_email')
      redirect_to root_url
    else
      render 'new'
    end
  end
・
・
・</pre> 
テストは以前のままなので現状ではRED
あとで修正するので該当部分をいったんコメントアウトしておく
（本文とちょっと違うけどｷﾆｼﾅｲ!!） 
<pre class="lang:ruby mark:(13-16) decode:true " title="/sample_app/test/integration/users_signup_test.rb" >・
・
・
  test "valid signup information" do
    get signup_path
    assert_difference 'User.count', 1 do
      post users_path, params: { user: { name:  "Example User",
                                         email: "user@example.com",
                                         password:              "password",
                                         password_confirmation: "password" } }
    end
    follow_redirect!
    # assert_template 'users/show'
    # assert_not flash.empty?
    # #テストユーザーがログインしている
    # assert is_logged_in?
  end
end</pre> 
この状態で新規ユーザーの登録をしてみる！
rootにリダイレクトされて指定したflashメッセージが表示されている
<img src="https://rakuda3desu.net/wp-content/uploads/2020/03/11.2.3-1.png" alt="" width="611" height="435" class="alignnone size-full wp-image-1361" /> 
（動作としては）メールが送信されているのがサーバーログ上で確認できる
<pre class="lang:default decode:true " title="サーバーログ" >UserMailer#account_activation: processed outbound mail in 8.8ms
Sent mail to momomo@mail.com (4.9ms)
Date: Fri, 20 Mar 2020 17:43:41 +0900
From: noreply@example.com
To: momomo@mail.com
Message-ID: <5e74823dea82_23c81a4729c59328@ip-172-31-40-65.mail>
#本来はここにメールの内容が表示される
・
・
・</pre> 
<h4>ログが文字化けしている件</h4> 
<pre class="lang:default decode:true " title="サーバーログ" >UserMailer#account_activation: processed outbound mail in 180.4ms
Sent mail to momo-chan@mail.com (9.2ms)
Date: Mon, 23 Mar 2020 16:46:29 +0900
From: noreply@example.com
To: momo-chan@mail.com
Message-ID: <5e7869556abba_10201a6f18437094@ip-172-31-40-65.mail>
Subject: =?UTF-8?Q?Sample_App?=
 =?UTF-8?Q?_=E3=82=A2=E3=82=AB=E3=82=A6=E3=83=B3=E3=83=88=E8=AA=8D=E8=A8=BC=E3=83=A1=E3=83=BC=E3=83=AB?=
Mime-Version: 1.0
Content-Type: multipart/alternative;
 boundary="--==_mimepart_5e7869556914e_10201a6f18436937";
 charset=UTF-8
Content-Transfer-Encoding: 7bit


----==_mimepart_5e7869556914e_10201a6f18436937
Content-Type: text/plain;
 charset=UTF-8
Content-Transfer-Encoding: base64

U2FtcGxlIEFwcA0KDQrjgZPjgpPjgavjgaHjga/vvIHjgoLjgoLjgaHjgoPj
gpPjgZXjgpMNCg0K44K144Oz44OX44Or44Ki44OX44Oq44G444KI44GG44GT
44Gd77yB44Ki44Kr44Km44Oz44OI44KS5pyJ5Yq544Gr44GZ44KL44Gr44Gv
44CB5LiL6KiY44G444Ki44Kv44K744K544GX44Gm44GP44Gg44GV44GEDQpo
dHRwczovLzVmN2I5OGEwYjY2MTQ5MTQ5ZWNkNmVkYWJmMTFmM2Q2LnZmcy5j
bG91ZDkuYXAtbm9ydGhlYXN0LTEuYW1hem9uYXdzLmNvbS9hY2NvdW50X2Fj
dGl2YXRpb25zLzIwU2tHZjFUNnFYZ1EwcE9yblBRUVEvZWRpdD9lbWFpbD1t
b21vLWNoYW4lNDBtYWlsLmNvbQ0K

----==_mimepart_5e7869556914e_10201a6f18436937
Content-Type: text/html;
 charset=UTF-8
Content-Transfer-Encoding: base64

PCFET0NUWVBFIGh0bWw+DQo8aHRtbD4NCiAgPGhlYWQ+DQogICAgPG1ldGEg
aHR0cC1lcXVpdj0iQ29udGVudC1UeXBlIiBjb250ZW50PSJ0ZXh0L2h0bWw7
IGNoYXJzZXQ9dXRmLTgiIC8+DQogICAgPHN0eWxlPg0KICAgICAgLyogRW1h
aWwgc3R5bGVzIG5lZWQgdG8gYmUgaW5saW5lICovDQogICAgPC9zdHlsZT4N
CiAgPC9oZWFkPg0KDQogIDxib2R5Pg0KICAgIDxoMT5TYW1wbGUgQXBwPC9o
MT4NCg0KPHA+44GT44KT44Gr44Gh44Gv77yB44KC44KC44Gh44KD44KT44GV
44KTPC9wPg0KDQo8cD4NCuOCteODs+ODl+ODq+OCouODl+ODquOBuOOCiOOB
huOBk+OBne+8geOCouOCq+OCpuODs+ODiOOCkuacieWKueOBq+OBmeOCi+OB
q+OBr+OAgeS4i+iomOOBuOOCouOCr+OCu+OCueOBl+OBpuOBj+OBoOOBleOB
hA0KPC9wPg0KDQo8YSBocmVmPSJodHRwczovLzVmN2I5OGEwYjY2MTQ5MTQ5
ZWNkNmVkYWJmMTFmM2Q2LnZmcy5jbG91ZDkuYXAtbm9ydGhlYXN0LTEuYW1h
em9uYXdzLmNvbS9hY2NvdW50X2FjdGl2YXRpb25zLzIwU2tHZjFUNnFYZ1Ew
cE9yblBRUVEvZWRpdD9lbWFpbD1tb21vLWNoYW4lNDBtYWlsLmNvbSI+44GT
44GT44GL44KJ44Ki44Kv44K744K5PC9hPg0KICA8L2JvZHk+DQo8L2h0bWw+
DQo=

----==_mimepart_5e7869556914e_10201a6f18436937--

Redirected to https://5f7b98a0b66149149ecd6edabf11f3d6.vfs.cloud9.ap-northeast-1.amazonaws.com/
Completed 302 Found in 545ms (ActiveRecord: 6.5ms)</pre> 
チュートリアル1週目の時にもユーザー名を日本語にすると化けるっていうのがあって
この時は深追いせずにユーザー名に日本語を使わないで進めたのだけど
今回、本文の日本語化も行っている影響でユーザー名をアルファベットにしても文字化けが発生して
ユーザー名をアルファベット表記にしても認証用のアドレスが読めない状況です。
対策を調べたところ、Railsのバージョンが古いもの向けのやり方しか見つけられなかったり
テスト環境では化けるけど本番環境では大丈夫だったとのご意見も見つけたりしたので
ひとまず進めてしまおうと思います。
詰まったらその時考えるってことで！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>新しいユーザーを登録したとき、リダイレクト先が適切なURLに変わったことを確認してみましょう。その後、Railsサーバーのログから送信メールの内容を確認してみてください。有効化トークンの値はどうなっていますか?</li>
	<li>コンソールを開き、データベース上にユーザーが作成されたことを確認してみましょう。また、このユーザーはデータベース上にはいますが、有効化のステータスがfalseのままになっていることを確認してください。</li>
</ol>
</blockquote>
<ol>
	<li>
リダイレクト先はrootに設定されている
有効化トークンの値は暗号化されている（はず！）
</li>
	<li>
動作確認のみにて省略
</li>
</ol>
<h3>まとめとか感想</h3>
今回は本文に出てこないエラーに悩まされました！
まるっと解決できないまま進むのもちょっと怖い気もするけど
そういうこともあると思うので頑張りたい！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

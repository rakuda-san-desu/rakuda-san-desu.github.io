---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜11.3"
date: "2020-04-09 15:49:56"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1364
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial11-3/"
---
<h2>11.3 アカウントを有効化する</h2>
メールが生成できるようになったのでAccountActivationsコントローラのeditアクションを書いていく。
テストも書いて、しっかりテストできていたらUserモデルにコードを移していく (リファクタリング) 
<h3>authenticated?メソッドの抽象化</h3>
有効化トークンとメールはそれぞれparams[:id]とparams[:email]で参照できるので
次のようなコードでユーザーを検索して承認する
<pre class="toolbar:2 lang:default decode:true " >user = User.find_by(email: params[:email])
if user && user.authenticated?(:activation, params[:id])</pre> 
<blockquote class="quote-rort">(この後、上の式に論理値を１つ追加します。何が追加されるか考えてみましょう。)</blockquote>
（→予想：アカウントが有効化されていないことを確認する論理値　#とは　🤔）

上記のコードは、アカウント有効化のダイジェストと渡されたトークンが一致するかどうかをチェックするものだけど
authenticated?メソッドは記憶トークン用（↓参照）なので現在は正常に作動しない！
<pre class="toolbar:2 lang:ruby decode:true " ># トークンがダイジェストと一致したらtrueを返す
def authenticated?(remember_token)
  #remember_digestがnilである　が　falseだったら以下を返す
  return false if remember_digest.nil?
  #BCrypt::Password.new(remember_digest)はremember_tokenと等しい
  BCrypt::Password.new(remember_digest).is_password?(remember_token)
end</pre> 
<h4>メタプログラミングとは何ぞ</h4>
<blockquote class="quote-rort">メタプログラミングを一言で言うと「プログラムでプログラムを作成する」ことです。メタプログラミングはRubyが有するきわめて強力な機能であり、Railsの一見魔法のような機能 (「黒魔術」とも呼ばれます) の多くは、Rubyのメタプログラミングによって実現されています。</blockquote>
ちょっと何言ってるか（略
実例を見ていく
<blockquote class="quote-rort">remember_digestはUserモデルの属性なので、モデル内では次のように書き換えることができます</blockquote>
<pre class="toolbar:2 lang:default decode:true " >self.remember_digest</pre> 
さらに、このコードのrememberの部分を変数として扱えるようにしたい
→下記のように状況に応じて呼び出すメソッドを切り替えたい！
<pre class="toolbar:2 lang:default decode:true " >self.FOOBAR_digest</pre> 
authenticated?メソッドにこの手法を組み込んでいく
<h4>強力なsendメソッド</h4>
sendこのメソッドは、渡されたオブジェクトに「メッセージを送る」事で呼び出すメソッドを動的に決めることができる
🤔
<blockquote class="quote-rort">例を見てみましょう。Railsコンソールを開き、Rubyのオブジェクトに対してsendメソッドを実行し、配列の長さを得るとします。</blockquote>  
<pre class="lang:default decode:true " title="ターミナル" >>> a = [1, 2, 3]
>> a.length
=> 3
>> a.send(:length)
=> 3
>> a.send("length")
=> 3</pre> 
sendを通して渡した「シンボル:length」や「文字列"length"」は、どちらもlengthメソッドを渡している事になる
<pre class="lang:default mark:(8-10) decode:true " title="ターミナル" >>> user = User.first
>> user.activation_digest
=> "$2a$10$4e6TFzEJAVNyjLv8Q5u22ensMt28qEkx0roaZvtRcp6UZKRM6N9Ae"
>> user.send(:activation_digest)
=> "$2a$10$4e6TFzEJAVNyjLv8Q5u22ensMt28qEkx0roaZvtRcp6UZKRM6N9Ae"
>> user.send("activation_digest")
=> "$2a$10$4e6TFzEJAVNyjLv8Q5u22ensMt28qEkx0roaZvtRcp6UZKRM6N9Ae"
>> attribute = :activation
>> user.send("#{attribute}_digest")
=> "$2a$10$4e6TFzEJAVNyjLv8Q5u22ensMt28qEkx0roaZvtRcp6UZKRM6N9Ae"</pre> 
変数attributeにシンボル:activationを代入し、文字列の式展開を使った引数をsendに渡している
→よって、activation_digestとなり同じ結果を返している

文字列やシンボルをメソッドで呼び出せる
→変数展開した文字列をメソッドとして呼びだすこともできる
→動的！スゴイ！！
という事かな？
<h4>sendメソッドを使ってauthenticated?メソッドを書き換える</h4> 
<pre class="toolbar:2 lang:default decode:true " >def authenticated?(remember_token)
  digest = self.send("remember_digest")
  return false if digest.nil?
  BCrypt::Password.new(digest).is_password?(remember_token)
end

#↑これが↓こうなる
#2番目の引数tokenの名前を変更して一般化し、他の認証でも使えるようにしている

def authenticated?(attribute, token)
  digest = self.send("#{attribute}_digest")
  return false if digest.nil?
  BCrypt::Password.new(digest).is_password?(token)
end

#モデル内にあるのでselfは省略できるため↑これが↓こうなる

def authenticated?(attribute, token)
  digest = send("#{attribute}_digest")
  return false if digest.nil?
  BCrypt::Password.new(digest).is_password?(token)
end
</pre> 
これにより下記のコードでauthenticated?の従来の振舞いを再現できる 
<pre class="toolbar:2 lang:default decode:true " >user.authenticated?(:remember, remember_token)</pre> 
Usersモデルに実際に抽象化したauthenticated?メソッドを適用 
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # トークンがダイジェストと一致したらtrueを返す
  def authenticated?(attribute, token)
    digest = send("#{attribute}_digest")
    return false if digest.nil?
    BCrypt::Password.new(digest).is_password?(token)
  end
・
・
・</pre> 
<h4>テストの修正</h4>
この時点ではテストがRED
↓
<blockquote class="quote-rort">current_userメソッド (リスト 9.9) とnilダイジェストのテスト (リスト 9.17) の両方で、authenticated?が古いままになっており、引数も2つではなくまだ1つのままだからです。</blockquote>
両者を更新
<pre class="lang:ruby decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >・
・
・
  # 現在ログイン中のユーザーを返す (いる場合)
  def current_user
    if (user_id = session[:user_id])
      @current_user ||= User.find_by(id: user_id)
    elsif (user_id = cookies.signed[:user_id])
      user = User.find_by(id: user_id)
      if user && user.authenticated?(:remember, cookies[:remember_token])
        log_in user
        @current_user = user
      end
    end
  end
・
・
・</pre>  
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb" >・
・
・
  test "authenticated? should return false for a user with nil digest" do
    #falseである　→@userのauthenticated?(:remember,'')
    assert_not @user.authenticated?(:remember, '')
  end
end</pre> 
これでテストがGREEN！！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>コンソール内で新しいユーザーを作成してみてください。新しいユーザーの記憶トークンと有効化トークンはどのような値になっているでしょうか? また、各トークンに対応するダイジェストの値はどうなっているでしょうか?</li>
	<li>リスト 11.26で抽象化したauthenticated?メソッドを使って、先ほどの各トークン/ダイジェストの組み合わせで認証が成功することを確認してみましょう。</li>
</ol>
</blockquote>


1. 
有効化トークン・ダイジェストは　before_create で作成・代入されているため値が入っている
記憶トークン・ダイジェストは有効化された後に代入されるためnil
<pre class="lang:default decode:true " title="ターミナル" >>> user = User.create(name: "momo-chan", email: "momo-chan-01@mail.com", password: "123456", password_confirmation: "123456")
=> #<User id: 111, name: "momo-chan", email: "momo-chan-01@mail.com", created_at: "2020-03-29 01:31:17", updated_at: "2020-03-29 01:31:17", password_digest: "$2a$10$bBImW1IMoj.qMkqCnRWIeuOLOURI1BLLQMmnr5giITH...", remember_digest: nil, admin: false, activation_digest: "$2a$10$lKo/lJh4ZBNIcLZlvo2dE.hV1o8FVwHlJlUMLhtt9mh...", activated: false, activated_at: nil>
>> user.remember_token
=> nil
>> user.activation_token
=> "_xR3tpc-e0GxbF04AS8a4g"
>> user.remember_digest
=> nil
>> user.activation_digest
=> "$2a$10$lKo/lJh4ZBNIcLZlvo2dE.hV1o8FVwHlJlUMLhtt9mh48clINvXS6"</pre> 

2. 
認証が成功することを確認
<pre class="lang:default decode:true " title="ターミナル" >#有効化トークン・ダイジェストを認証の成功を確認
>> user.authenticated?(:activation, user.activation_token)
=> true
#記憶トークン・ダイジェストを作成
>> user.remember_token = User.new_token
=> "koiT0nXOavgR4xQpV5nqfw"
>> user.update_attribute(:remember_digest, User.digest(user.remember_token))
   (0.1ms)  SAVEPOINT active_record_1
  SQL (0.8ms)  UPDATE "users" SET "updated_at" = ?, "remember_digest" = ? WHERE "users"."id" = ?  [["updated_at", "2020-03-29 11:06:56.335781"], ["remember_digest", "$2a$10$cdNOii6mkWGK0YFyUoMEG.frNPra4ArbkEvajegYUNtIhiMZ9BMne"], ["id", 111]]
   (0.1ms)  RELEASE SAVEPOINT active_record_1
=> true
#記憶トークン・ダイジェストの認証の成功を確認
>> user.authenticated?(:remember,user.remember_token)
=> true</pre> 
<h3>11.3.2 editアクションで有効化</h3>
authenticated?メソッドが有効化トークン・ダイジェストにも対応できるようになったので
editアクションを書くいていく　→　paramsハッシュで渡されたメールアドレスに対応するユーザーを認証するアクション
<pre class="toolbar:2 lang:default decode:true " >if user && !user.activated? && user.authenticated?(:activation, params[:id])</pre> 
<blockquote class="quote-rort">!user.activated?という記述にご注目ください。先ほど「１つ論理値を追加します」と言ったのはここで利用したいからです。</blockquote>
上のコードはすでに有効なユーザーを再度有効化させないために必要。
!user.activated?　→　ユーザーがactivated で は な い

また、上の論理値に基づいてユーザー認証するには、ユーザー認証後activated_atタイムスタンプを更新する必要がある。
<pre class="toolbar:2 lang:default decode:true " >#userのactivatedの値をtrueで上書き
user.update_attribute(:activated,    true)
#userのactivated_atの値を現在時刻で上書き
user.update_attribute(:activated_at, Time.zone.now)</pre> 
これらを含めてeditアクションを定義していく
<blockquote class="quote-rort">有効化トークンが無効だった場合の処理も行われている点にご注目ください。トークンが無効になるようなことは実際にはめったにありませんが、もしそうなった場合はルートURLにリダイレクトされる仕組みです。</blockquote>
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/account_activations_controller.rb" >class AccountActivationsController < ApplicationController

  def edit
    # userに代入　→　Userテーブルから　URLから取得したemailの値を持つ　userデータを取得して
    user = User.find_by(email: params[:email])
    # userが存在する　かつ　userがactivatedではない　かつ　有効化トークンとparams[:id](activation_token)が持つ有効化ダイジェストが一致した場合
    if user && !user.activated? && user.authenticated?(:activation, params[:id])
      # userのactivatedの値をtrueに
      user.update_attribute(:activated,    true)
      # userのactivated_atの値を現在時刻に
      user.update_attribute(:activated_at, Time.zone.now)
      # userでログイン（Sessionsヘルパーのlog_inメソッドを呼び出し）
      log_in user
      # flashメッセージを表示
      flash[:success] = t(".account_activation_successful")
      # userページにリダイレクト
      redirect_to user
    #if文の評価がfalseの場合
    else
      # flashメッセージを表示
      flash[:danger] = t(".invalid_activation_link")
      # ルートURLにリダイレクト
      redirect_to root_url
    end
  end
end</pre> 
<blockquote class="quote-rort">リスト 11.31のコードを使うと、リスト 11.25にあるURLを貼り付けてユーザーを有効化できます。</blockquote>
ただし🐫の環境では日本語化による文字化けが起こっているので確認のために英語に戻しておく必要がある 
<pre class="lang:ruby mark:(-) decode:true " title="/sample_app/config/application.rb" >    # Settings in config/environments/* take precedence over those specified here.
    # Application configuration should go into files in config/initializers
    # -- all .rb files in that directory are automatically loaded.
    config.time_zone = 'Tokyo'
    config.active_record.default_timezone = :local
    config.i18n.default_locale = :en
    config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '**', '*.yml').to_s]
  end
end</pre> 
これでサンプルページからサインイン→サーバーログのURLをクリックでアカウントの有効化が成功する(実際は送られてきたメールに記載されたURLにアクセス）
view系のテストが一部REDになるなどがあるけどｷﾆｼﾅｲ!!
<blockquote class="quote-rort">もちろん、この時点ではユーザーのログイン方法を変更していないので、ユーザーの有効化にはまだ何の意味もありません。ユーザーの有効化が役に立つためには、ユーザーが有効である場合にのみログインできるようにログイン方法を変更する必要があります</blockquote>
user.activated?がtrueの場合にのみログインを許可
falseの場合はルートURLにリダイレクトしてwarningのFlashメッセージを表示
（現状テストはRED） 
<pre class="lang:ruby mark:(7-20) decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >・
・
・
  def create
    @user = User.find_by(email: params[:session][:email].downcase)
    if @user && @user.authenticate(params[:session][:password])
      # userが有効であれば
      if @user.activated?
        #session[:user_id] = @user　と言う事
        log_in @user
        # params[:session][:remember_me]が1の時@userを記憶　そうでなければuserを忘れる
        params[:session][:remember_me] == '1' ? remember(@user) : forget(@user)
        #SessionsHelperで定義したredirect_back_orメソッドを呼び出してリダイレクト先を定義
        redirect_back_or @user
      else
        message  = t('.account_not_activated')
        message += t('.check_your_email')
        flash[:warning] = message
        redirect_to root_url
      end
    else
      flash.now[:danger] = t('.login_error')
      render 'new'
    end
  end
・
・
・</pre> 
本文だと「user」になっている部分を「@user」にしないとエラーになったりするので上記参照で
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>コンソールから、11.2.4で生成したメールに含まれているURLを調べてみてください。URL内のどこに有効化トークンが含まれているでしょうか?</li>
	<li>先ほど見つけたURLをブラウザに貼り付けて、そのユーザーの認証に成功し、有効化できることを確認してみましょう。また、有効化ステータスがtrueになっていることをコンソールから確認してみてください。</li>
</ol>
</blockquote>
1.
<pre class="lang:default decode:true " title="コンソール" >#userに新規ユーザーを代入
>> user = User.create(name: "momo", email: "momo0331-02@mail.com", password: "123456")
=> #<User id: 118, name: "momo", email: "momo0331-02@mail.com", created_at: "2020-03-31 00:04:20", updated_at: "2020-03-31 00:04:20", password_digest: "$2a$10$X2.V6Nl9B8PDEDPZufacE./F1Hsp1hzOYZaiV3qU9b0...", remember_digest: nil, admin: false, activation_digest: "$2a$10$j3oAFhTn1i.XWJM.dHthduJn.kfd.FDqPJYyODZCjip...", activated: false, activated_at: nil>
#mailにuserを引数に持ったアカウント有効化メール代入
>> mail = UserMailer.account_activation(user)
  Rendering user_mailer/account_activation.html.erb within layouts/mailer
  Rendered user_mailer/account_activation.html.erb within layouts/mailer (2.9ms)
  Rendering user_mailer/account_activation.text.erb within layouts/mailer
  Rendered user_mailer/account_activation.text.erb within layouts/mailer (1.0ms)
UserMailer#account_activation: processed outbound mail in 886.8ms
=> #<Mail::Message:18441680, Multipart: true, Headers: <From: noreply@example.com>, <To: momo0331-02@mail.com>, <Subject: translation missing: en.user_mailer.account_activation.account_activation_title>, <Mime-Version: 1.0>, <Content-Type: multipart/alternative; boundary="--==_mimepart_5e82891fb0acc_11954df9a470622"; charset=UTF-8>>
#textタイプのmail本文をエンコードして出力（mail.body.encodedで両方出力されるけど見やすさ優先でtextタイプを出力）
>> mail.text_part.body.encoded
=> "Sample App\r\n\r\n<span class=\"translation_missing\" title=\"translation missing: en.user_mailer.hi, user_name: momo\">Hi</span>\r\n\r\n<span class=\"translation_missing\" title=\"translation missing: en.user_mailer.welcome_message\">Welcome Message</span>\r\nhttps://5f7b98a0b66149149ecd6edabf11f3d6.vfs.cloud9.ap-northeast-1.amazonaws.com/account_activations/7G1RkBM1H2VFV2at2cub2A/edit?email=momo0331-02%40mail.com\r\n"</pre> 
14行目の「7G1RkBM1H2VFV2at2cub2A」の部分が有効化トークン

2. 
<pre class="lang:default decode:true " title="ターミナル" >#変数userには認証前のuserの値が代入されている
>> user
=> #<User id: 118, name: "momo", email: "momo0331-02@mail.com", created_at: "2020-03-31 00:04:20", updated_at: "2020-03-31 00:04:20", password_digest: "$2a$10$X2.V6Nl9B8PDEDPZufacE./F1Hsp1hzOYZaiV3qU9b0...", remember_digest: nil, admin: false, activation_digest: "$2a$10$j3oAFhTn1i.XWJM.dHthduJn.kfd.FDqPJYyODZCjip...", activated: false, activated_at: nil>
#データベースから呼び出すと有効化ステータスがtrueになっていることが確認できる（activated:の値がtrueになっている。ちなみにactivated_atにも値が入っている）
>> User.last
  User Load (0.5ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" DESC LIMIT ?  [["LIMIT", 1]]
=> #<User id: 118, name: "momo", email: "momo0331-02@mail.com", created_at: "2020-03-31 00:04:20", updated_at: "2020-03-31 00:05:17", password_digest: "$2a$10$X2.V6Nl9B8PDEDPZufacE./F1Hsp1hzOYZaiV3qU9b0...", remember_digest: nil, admin: false, activation_digest: "$2a$10$j3oAFhTn1i.XWJM.dHthduJn.kfd.FDqPJYyODZCjip...", activated: true, activated_at: "2020-03-31 00:05:17"></pre> 
<h4>コンソールで生成したメールに含まれているURLを調べる</h4>
めちゃくちゃ嵌りました🐫💧
新規ユーザーを作ってどうすればいいんじゃろ。と。
まずはメールを作るのか。となって
<pre class="toolbar:2 lang:default decode:true " >UserMailer.account_activation(user)</pre> 
して、
メールが作られたっぽいログが出るけどどうやって本文を読みだせばいいのだ？？と
上のコードに何かつなげれば？何をつなげれば？？？？？
自分なりに手あたり次第やってみたけどダメで、コミュで質問しました
えぇ、コミュはこちらです→<a href="https://newburukai.github.io/" rel="noopener noreferrer" target="_blank">にゅ〜ぶる会</a>
「mail.bodyとかでメール本文出してなかった？」
あああああああああ！テストの時にさんざん悩んだアレねええええええええええええ！！！！！！！

いつもお世話になっております。本当にありがとうございます。
<h3>11.3.3 有効化のテストとリファクタリング</h3>
アカウント有効化の統合テストを追加する
<blockquote class="quote-rort">正しい情報でユーザー登録を行った場合のテスト (7.4.4) は既にあるので、リスト 7.33で書いたテストに若干手を加えることにします。</blockquote>
 
<pre class="lang:ruby mark:(5-8,24-25,33-54) decode:true " title="/sample_app/test/integration/users_signup_test.rb" >require 'test_helper'

class UsersSignupTest < ActionDispatch::IntegrationTest
  
  def setup
    # deliveries変数に配列として格納されたメールをクリア
    ActionMailer::Base.deliveries.clear
  end

  test "invalid signup information" do
    get signup_path
    assert_no_difference 'User.count' do
      post signup_path, params: { user: { name:  "",
                                         email: "user@invalid",
                                         password:              "foo",
                                         password_confirmation: "bar" } }
    end
    assert_template 'users/new'
    assert_select 'div#error_explanation'
    assert_select 'div.alert-danger'
    assert_select 'form[action="/signup"]'
  end
  
  # valid signup informationテストに機能追加
  test "valid signup information with account activation" do
    get signup_path
    assert_difference 'User.count', 1 do
      post users_path, params: { user: { name:  "Example User",
                                         email: "user@example.com",
                                         password:              "password",
                                         password_confirmation: "password" } }
    end
    # 引数の値が等しい　１とActionMailer::Base.deliveriesに格納された配列の数
    assert_equal 1, ActionMailer::Base.deliveries.size
    # userに@userを代入（通常統合テストからはアクセスできないattr_accessorで定義した属性の値にもアクセスできるようになる）
    user = assigns(:user)
    # userが有効ではない
    assert_not user.activated?
    # 有効化していない状態でログインしてみる
    log_in_as(user)
    # テストユーザーがログインしていない
    assert_not is_logged_in?
    # 有効化トークンが不正な場合
    get edit_account_activation_path("invalid token", email: user.email)
    # テストユーザーがログインしていない
    assert_not is_logged_in?
    # トークンは正しいがメールアドレスが無効な場合
    get edit_account_activation_path(user.activation_token, email: 'wrong')
    # テストユーザーがログインしていない
    assert_not is_logged_in?
    # 有効化トークンが正しい場合
    get edit_account_activation_path(user.activation_token, email: user.email)
    # userの値を再取得すると有効化している
    assert user.reload.activated?
    # 実際にリダイレクト先に移動
    follow_redirect!
    #sers/showが描写される
    assert_template 'users/show'
    #テストユーザーがログインしている
    assert is_logged_in?
  end
end</pre> 

<h4>ActionMailer::Base.deliveries.clearとはなんぞ</h4>
/sample_app/config/environments/test.rbでテスト中のメール送信モードをtestに設定している
これにより送信されたメールはdeliveriesという変数に配列として追加されていく
この追加されている内容をクリアするためclearメソッドを繋いでいる
（setupメソッドでdeliveries（変数を初期化しておかないと、並列する他のテストでエラーが発生する）
<a href="https://railsguides.jp/testing.html#%E5%9F%BA%E6%9C%AC%E7%9A%84%E3%81%AA%E3%83%86%E3%82%B9%E3%83%88%E3%82%B1%E3%83%BC%E3%82%B9" title="Railsガイド　12.2.2 基本的なテストケース" rel="noopener noreferrer" target="_blank"></a>
<h4>リファクタリング</h4>
テストがGREENになったのでリファクタリングを行う！
ユーザー操作の一部をコントローラからモデルに移行する
<blockquote class="quote-rort">activateメソッドを作成してユーザーの有効化属性を更新し、send_activation_emailメソッドを作成して有効化メールを送信します。</blockquote>
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # アカウントを有効にする
  def activate
    update_attribute(:activated,    true)
    update_attribute(:activated_at, Time.zone.now)
  end

  # 有効化用のメールを送信する
  def send_activation_email
    UserMailer.account_activation(self).deliver_now
  end
  
    private
・
・
・</pre>  
<pre class="lang:ruby mark:(7-8) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
  def create
    @user = User.new(user_params)
    if @user.save
      # Userモデルで定義したメソッド（send_activation_email）を呼び出して有効化メールを送信
      @user.send_activation_email
      flash[:info] = t('.check_your_email')
      redirect_to root_url
    else
      render 'new'
    end
  end
・
・
・</pre>  
<pre class="lang:ruby mark:(8-9) decode:true " title="/sample_app/app/controllers/account_activations_controller.rb" >class AccountActivationsController < ApplicationController

  def edit
    # userに代入　→　Userテーブルから　URLから取得したemailの値を持つ　userデータを取得して
    user = User.find_by(email: params[:email])
    # userが存在する　かつ　userがactivatedではない　かつ　有効化ダイジェストとparams[:id](有効化トークン)が一致した場合
    if user && !user.activated? && user.authenticated?(:activation, params[:id])
      # userで定義したactivateメソッドを呼び出してユーザーを有効化
      user.activate
      # userでログイン（Sessionsヘルパーのlog_inメソッドを呼び出し）
      log_in user
      # flashメッセージを表示
      flash[:success] = t(".account_activation_successful")
      # userページにリダイレクト
      redirect_to user
    #if文の評価がfalseの場合
    else
      # flashメッセージを表示
      flash[:danger] = t(".invalid_activation_link")
      # ルートURLにリダイレクト
      redirect_to root_url
    end
  end
end</pre> 
<h4>消えたuser.</h4>
Userモデルには「user」という変数はないので、コントローラのコードをそのまま移すとエラーになる
<pre class="toolbar:2 lang:ruby decode:true " >user.update_attribute(:activated,    true)
user.update_attribute(:activated_at, Time.zone.now)

#これ↑がこう↓なる

update_attribute(:activated,    true)
update_attribute(:activated_at, Time.zone.now)</pre> 
<blockquote class="quote-rort">(userをselfに切り替えるという手もあるのですが、selfはモデル内では必須ではないと6.2.5で解説したことを思い出しましょう。) Userメイラー内の呼び出しでは、@userがselfに変更されている点にもご注目ください。</blockquote> 
<pre class="toolbar:2 lang:ruby decode:true " >#なので
UserMailer.account_activation(@user).deliver_now

#これ↑がこう↓こうなる

UserMailer.account_activation(self).deliver_now</pre> 
<blockquote class="quote-rort">どんなに簡単なリファクタリングであっても、この手の変更はつい忘れてしまうものです。テストをきちんと書いておけば、この種の見落としを検出できます。以上でテストスイートは greenになるはずです。</blockquote>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>リスト 11.35にあるactivateメソッドはupdate_attributeを２回呼び出していますが、これは各行で１回ずつデータベースへ問い合わせしていることになります。リスト 11.39に記したテンプレートを使って、update_attributeの呼び出しを１回のupdate_columns呼び出しにまとめてみましょう (これでデータベースへの問い合わせが１回で済むようになります)。また、変更後にテストを実行し、 greenになることも確認してください。</li>
	<li>現在は、/usersのユーザーindexページを開くとすべてのユーザーが表示され、/users/:idのようにIDを指定すると個別のユーザーを表示できます。しかし考えてみれば、有効でないユーザーは表示する意味がありません。そこで、リスト 11.40のテンプレートを使って、この動作を変更してみましょう9。なお、ここで使っているActive Recordのwhereメソッドについては、13.3.3でもう少し詳しく説明します。</li>
	<li>ここまでの演習課題で変更したコードをテストするために、/users と /users/:id の両方に対する統合テストを作成してみましょう。
訳注: updateメソッドは、コールバックとバリデーションを実行せずにスキップしますので、コールバックやバリデーションをかける必要がある場合は注意が必要です。</li>
</ol>
</blockquote>
1. 
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # アカウントを有効にする
  def activate
    #指定のカラムを指定の値に、DBに直接上書き保存
    update_columns(activated: true, activated_at: Time.zone.now)
  end


  # 有効化用のメールを送信する
  def send_activation_email
    UserMailer.account_activation(self).deliver_now
  end
・
・
・</pre> 
update_columns→データベースに直接アクセスしてカラム（属性）の値を更新する
バリデーションやコールバックはスキップされる
<a href="https://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-update_columns" title="Railsガイド" rel="noopener noreferrer" target="_blank">Railsガイド｜update_columns</a>
2. 
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
  def index
    # インスタンス変数@usersに以下を代入
    # Userテーブルからactivated:がtrueのデータをすべて取り出してpaginate(page: params[:page])する
    @users = User.where(activated: true).paginate(page: params[:page])
  end
  
  def show
    # @userにUserテーブルから(params[:id])のデータを取り出して代入
    @user = User.find(params[:id])
    #root_urlにリダイレクト　trueの場合ここで処理が終了する→　@userが有効ではない場合
 　 #false(@userが有効）な場合はリダイレクトは実行されない
    redirect_to root_url and return unless @user.activated?
  end
・
・
・</pre> 
<a href="https://railsdoc.com/controller#render" title="Railsドキュメント" rel="noopener noreferrer" target="_blank">Railsドキュメント</a>のrenderメソッドの注意点に
<blockquote>同じアクション内でrenderメソッドを複数呼び出すと、エラーになるので、and returnを付ける</blockquote>
とある。
複数レイアウトを描画するメソッドがあると条件によって2重にレイアウトが描画されエラーになる。という事っぽい。
それを防ぐ為にand returnで明示的に処理を終了させる
redirect_toメソッドでも同様なんだけど、上記では複数呼び出していないのでand returnはなくてもエラーにならないけど何であるのかは謎
3.
準備：有効化していないテストユーザーを用意
<pre class="lang:default decode:true " title="/sample_app/test/fixtures/users.yml" >・
・
・
non_activated:
 name: Non Activated
 email: non_activated@mail.com
 password_digest: <%= User.digest('password')%>
 activated: false
 activated_at: <%= Time.zone.now %>
・
・
・</pre> 
最初に考えた手順
<ol>
	<li><del datetime="2020-04-07T01:32:48+00:00">有効化していないユーザーでログイン</del></li>
	<li><del datetime="2020-04-07T01:32:48+00:00">ログインユーザー</del>未認証ユーザーは有効化されていないことを確認<del datetime="2020-04-07T01:32:48+00:00">（必要?）</del></li>
	<li>/usersを表示</li>
	<li>有効化されていないユーザーは表示されていないことを確認</li>
	<li>有効化されていないログインユーザーの/users/:idへgetのリクエスト</li>
	<li>root_urlへリダイレクト</li>
</ol>
<del datetime="2020-04-07T04:45:01+00:00">ログインして確認するからログインテストに書く？（？</del>
書いてる途中で/sample_app/test/integration/users_index_test.rbのindex including paginationテストと書き出しがかぶっているので
そこに機能追加していくことにした！
言われてみればページネーションを含むインデックスページのテスト。ふむ。
<pre class="lang:Ruby mark:(8-8,18-20,40-43) decode:true " title="/sample_app/test/integration/users_index_test.rb" >require 'test_helper'

class UsersIndexTest < ActionDispatch::IntegrationTest

  def setup
    @admin     = users(:michael)
    @non_admin = users(:archer)
    @non_activated = users(:non_activated)
  end

  test "index including pagination" do
    # @adminでログイン
    log_in_as(@admin)
    # users_pathにgetのリクエスト
    get users_path
    # users/indexが描画される
    assert_template 'users/index'
    # users(:non_activated)が存在しないことを確認
    # 特定のHTMLタグが存在する a href パスはuser_path(@non_activated) 表示テキストは@non_activated.name カウントは0
    assert_select 'a[href=?]', user_path(@non_activated), text: @non_activated.name, count: 0
    # 特定のHTMLタグが存在する　div class="pagination"
    assert_select 'div.pagination' , count: 2
    # first_page_of_usersにUser.paginate(page: 1)（ユーザー一覧のページ目）を代入
    first_page_of_users = User.paginate(page: 1)
    # first_page_of_usersからuserを1ずつ取り出す
    first_page_of_users.each do |user|
      # 特定のHTMLタグが存在する a href パスはuser_path(user) 表示テキストはuser.name
      assert_select 'a[href=?]', user_path(user), text: user.name
      # user == @adminがfalseの場合（管理ユーザー 以 外 に削除の表示がある）
      unless user == @admin
        # 特定のHTMLタグが存在する　a href　パスはuser_path(user)　表示テキストは'削除'
        assert_select 'a[href=?]', user_path(user), text: '削除'
      end
    end
    # ブロックで渡されたものを呼び出す前後でUser.countが-1
    assert_difference 'User.count', -1 do
      # user_path(@non_admin)にdeleteのリクエスト
      delete user_path(@non_admin)
    end
    # user_path(@non_activated)にgetのリクエスト
    get user_path(@non_activated)
    # root_pathにリダイレクトされる
    assert_redirected_to root_path
  end
・
・
・</pre> 
<h3>まとめとか感想</h3>
今回は今のところ一番大変だった！
頑張ったし沢山助けてもらいました✨ありがたや✨
特にテストに関してはほぼノーヒントじゃん？
でも表記を変えてみて希望の挙動が返ってきてたらとりあえずは安心していいのかなって。
（このブログは添削してもらっているのでとても安心だけども）
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

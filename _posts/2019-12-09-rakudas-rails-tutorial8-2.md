---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜8.2"
date: "2019-12-09 17:06:04"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1076
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial8-2/"
---
<h2>8.2 ログイン</h2>
有効な値の送信があった場合にログインできるようにする！
→cookieを使った一時セッションでログイン
→ブラウザを閉じると自動的に期限切れになるようにする
<blockquote class="quote-rort">セッションを実装するには、様々なコントローラやビューでおびただしい数のメソッドを定義する必要があります。（中略）Sessionsコントローラ (8.1.1) を生成した時点で既にセッション用ヘルパーモジュールも (密かに) 自動生成されています。さらに、Railsのセッション用ヘルパーはビューにも自動的に読み込まれます。</blockquote>
ApplicationコントローラにSessionヘルパーモジュールを読み込ませてどのコントローラからでも使えるようにする
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/app/controllers/application_controller.rb" >class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  #Sessionヘルパーモジュールの読み込み
  include SessionsHelper
end</pre> 
<h3>8.2.1 log_inメソッド</h3>
Railsで元から定義されているsessionメソッドを使って単純なログインを行えるようにする
<strong>8.1.1で生成したSessionsコントローラとは無関係なので注意！！</strong>
[blogcard url="https://railsguides.jp/action_controller_overview.html#%E3%82%BB%E3%83%83%E3%82%B7%E3%83%A7%E3%83%B3"]
<pre class="lang:ruby decode:true " >#sessionメソッドはハッシュの様に扱えるメソッド
session[:user_id] = user.id</pre> 
<blockquote class="quote-rort">上のコードを実行すると、ユーザーのブラウザ内の一時cookiesに暗号化済みのユーザーIDが自動で作成されます。この後のページで、session[:user_id]を使ってユーザーIDを元通りに取り出すことができます。一方、cookiesメソッド (9.1) とは対照的に、sessionメソッドで作成された一時cookiesは、ブラウザを閉じた瞬間に有効期限が終了します。</blockquote>
Sessionsヘルパーにlog_inという名前のメソッドを定義して、同じログイン手法を使い回せるようにする
<pre class="lang:ruby decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >module SessionsHelper
 
  # 渡されたユーザーでログインする
  def log_in(user)
    #session[:user_id]にuser.idを代入する
    session[:user_id] = user.id
  end
end</pre> 
<h4>一時cookieと永続的なcookies</h4>
sessionメソッドで作成した一時cookiesは自動的に暗号化されるので
cookieから情報を盗み出されても、それを使って本物のユーザーとしてログインすることは出来ない
→一時的なセッションでブラウザを閉じると有効期限が終了する為
永続的なcookieに関してはセッションハイジャックという攻撃を受ける可能性が常につきまとう！
→9章に続く
<h4>createアクションの完了</h4>
作成したヘルパーメソッドを使ってユーザーとしてログインできる様に
sessionsコントローラーのcreateアクションを完成させる
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >・
・
・
  def create
    user = User.find_by(email: params[:session][:email].downcase)
    if user && user.authenticate(params[:session][:password])
      #log_inメソッド（session[:user_id]にuser.idを代入）
      log_in user
      #詳細画面にリダイレクト
      redirect_to user
    else
      flash.now[:danger] = t('.login_error')
      render 'new'
    end
  end
・
・
・</pre> 
ログインフォームが作動するようになっている！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>有効なユーザーで実際にログインし、ブラウザからcookiesの情報を調べてみてください。このとき、sessionの値はどうなっているでしょうか? ヒント: ブラウザでcookiesを調べる方法が分からない? 今こそググってみるときです!</li>
	<li>先ほどの演習課題と同様に、Expiresの値について調べてみてください。</li>
</ol>
</blockquote>
1.ブラウザ（chrome）の設定→詳細設定→プライバシーとセキュリティ→サイトの設定
→Cookie とサイトデータ→すべての Cookie とサイトデータを表示→
<a href="https://rakuda3desu.net/wp-content/uploads/2019/11/8.2-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/11/8.2-1.png" alt="" width="652" height="411" class="alignnone size-full wp-image-1078" /></a>
<a href="https://rakuda3desu.net/wp-content/uploads/2019/11/8.2-2.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/11/8.2-2.png" alt="" width="669" height="492" class="alignnone size-full wp-image-1079" /></a>
2.Expires→有効期限（ブラウザ セッションの終了時）
<h3>8.2.2 現在のユーザー</h3>
ユーザーIDを安全に一時セッションの中に置けるようになった！
なので、そのユーザーIDを別のページで取り出せるようにする
→current_userメソッドを定義する
<pre class="lang:ruby decode:true " >#ユーザーが存在しない場合、findメソッドでは例外が発生する
User.find(session[:user_id])
#find_byメソッドでは、ユーザーが存在しない場合はnilが返る
User.find_by(id: session[:user_id])</pre> 
<blockquote class="quote-rort">「ユーザーがログインしていない」などの状況が考えられる今回のケースでは、session[:user_id]の値はnilになりえます。</blockquote>
なのでfind_byメソッドを使ってcurrent_userを定義していく
<pre class="lang:ruby decode:true " title="コード解説" >def current_user
  #もしsessionにuser_idが有れば
  if session[:user_id]
    #Userモデルの中のidがsessionに入ったuser_idと合致するデータの1件目を取得
    User.find_by(id: session[:user_id])
  end
  #sessionにユーザーIDが無ければnilが返る
end
#→User.find_byの実行結果をインスタンス変数に保存する
#@current_userがnilであれば
if @current_user.nil?
  #@current_userに代入　Userモデルの中のidがsessionに入ったuser_idと合致するデータ
  @current_user = User.find_by(id: session[:user_id])
#nilでなければ
else
  #@current_user自身を返す
  @current_user
end
 
#演算子を使ってさらにコードを短縮
#@current_userに代入
#@current_user自身もしくはUserモデルの中のidがsessionに入ったuser_idと合致するデータ
@current_user = @current_user || User.find_by(id: session[:user_id])
 
#更に省略形に
@current_user ||= User.find_by(id: session[:user_id])</pre> 
<pre class="lang:ruby decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >#上記のコードをヘルパーに追加したもの
・
・
・
  # 現在ログイン中のユーザーを返す (いる場合)
  def current_user
    #もしsessionにuser_idが有れば
    if session[:user_id]
      #@current_user自身もしくはUserモデルの中のidがsessionに入ったuser_idと合致するデータを@current_userに代入
      @current_user ||= User.find_by(id: session[:user_id])
    end
  end
  
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>Railsコンソールを使って、User.find_by(id: ...)で対応するユーザーが検索に引っかからなかったとき、nilを返すことを確認してみましょう。</li>
	<li>先ほどと同様に、今度は:user_idキーを持つsessionハッシュを作成してみましょう。リスト 8.17に記したステップに従って、||=演算子がうまく動くことも確認してみましょう。</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="コンソール" >#1.
>> User.find_by(id: 10)
#対応ユーザーがいないのでnil
=> nil
 
#.2
#sessionに空のハッシュを代入
>> session = {}
#{}が返る
=> {}
#session[:user_id]にnilを代入
>> session[:user_id] = nil
#nilが返る
=> nil
#@current_user自身もしくはUserモデルの中のidがsessionに入ったuser_idと合致するデータを@current_userに代入
>> @current_user ||= User.find_by(id: session[:user_id])
#session[:user_id]がnilなのでnilが返る
=> nil
#session[:user_id]にUserモデルの1つ目のデータのid
を代入
>> session[:user_id]= User.first.id
#1が返る
=> 1
#@current_user自身もしくはUserモデルの中のidがsessionに入ったuser_idと合致するデータを@current_userに代入
>> @current_user ||= User.find_by(id: session[:user_id])
#（@current_userはまだ空なので）id: 1（sessionに入ったuser_id）をUserモデルから取得した内容が返る
=> #<User id: 1, name: "Rails Tutorial", email: "example@railstutorial.org", created_at: "2019-11-11 07:06:24", updated_at: "2019-11-11 07:06:24", password_digest: "$2a$10$IgiS5ygszAZUdJm6JKj.D.eNYlRHOmtjKolGn7W3ZFm...">
#@current_user自身もしくはUserモデルの中のidがsessionに入ったuser_idと合致するデータを@current_userに代入
>> @current_user ||= User.find_by(id: session[:user_id])
#@current_user自身（上で代入されたユーザデータ）が返る
=> #<User id: 1, name: "Rails Tutorial", email: "example@railstutorial.org", created_at: "2019-11-11 07:06:24", updated_at: "2019-11-11 07:06:24", password_digest: "$2a$10$IgiS5ygszAZUdJm6JKj.D.eNYlRHOmtjKolGn7W3ZFm..."></pre> 
<h4>上下（25行目と29行目）の@current_user ||= User.find_by(id: session[:user_id])
の違い</h4>
式は条件を満たしたら処理を行って終了する
上の@current_user ||= User.find_by(id: session[:user_id])は
@current_userに何も代入されていないので、次のUser.find_by(id: session[:user_id])に進む
下の@current_user ||= User.find_by(id: session[:user_id])は
@current_userにidが1のユーザーが代入されているので@current_userを返して終了
（Userモデルにデータを探しに行かない）
<h3>8.2.3 レイアウトリンクを変更する</h3>
ユーザーがログインしている時とそうでない時でレイアウトを変更する
→<a href="https://railstutorial.jp/chapters/basic_login?version=5.1#fig-login_success_mockup" rel="noopener noreferrer" target="_blank">RoRT　本文　8.2.3　図8.7参照</a>
ログインが成功している時はアカウントメニューとして下記を表示
<ul>	
<li>「ログアウト」リンク</li>
<li>「ユーザー設定」リンク</li>
<li>「ユーザー一覧」リンク</li>
<li>「プロフィール表示」リンク</li>
</ul>
→テスト駆動開発の流れとしてはここで統合テストを書くところだけど後でとこのと
<h4>logged_in?メソッドを定義</h4>
<pre class="lang:ruby decode:true " title="コード見本" >#レイアウトのリンクを変更するコードの例
<% if logged_in? %>
  # ログインユーザー用のリンク
<% else %>
  # ログインしていないユーザー用のリンク
<% end %></pre> 
このために必要な論理値を返すlogged_in?メソッドを定義していく
<blockquote　class="quote-rort">
ユーザーがログイン中の状態とは「sessionにユーザーidが存在している」こと、つまりcurrent_userがnilではないという状態を指します。これをチェックするには否定演算子 (4.2.3) が必要なので、! を使っていきます
</blockquote>
<pre class="lang:ruby mark:(16-18) decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >module SessionsHelper
  
  # 渡されたユーザーでcookieを使ってログインする
  def log_in(user)
    session[:user_id] = user.id
  end
  
  # 現在ログイン中のユーザーを返す (いる場合)
  def current_user
    if session[:user_id]
      @current_user ||= User.find_by(id: session[:user_id])
    end
  end
  
  # ユーザーがログインしていればtrue、その他ならfalseを返す
  def logged_in?
    !current_user.nil?
  end
end</pre> 
logged_in?ヘルパーメソッドを利用して
ログインの有無によってlayoutが変わるようにヘッダー部分を書き換える
<pre class="lang:default mark:(9-9,11-11,19-19,21-21,25-25,30-30,32-33) decode:true " title="/sample_app/app/views/layouts/_header.html.erb" ><header class="navbar navbar-fixed-top navbar-inverse">
  <div class="container">
    <%= link_to "sample app", root_path, id: "logo" %>
    <nav>
      <ul class="nav navbar-nav navbar-right">
        <li><%= link_to "Home", root_path %></li>
        <li><%= link_to "Help", help_path %></li>
        #ログインしていれば（current_userがnilでなければ）
        <% if logged_in? %>
          #10章で実装
          <li><%= link_to "Users", '#' %></li>
          #ドロップダウンメニュー
          <li class="dropdown">
            <a href="#" class="dropdown-toggle" data-toggle="dropdown">
              Account <b class="caret"></b>
            </a>
            <ul class="dropdown-menu">
              #自分のプロフィールページへのリンク
              <li><%= link_to "Profile", current_user %></li>
              #10章で実装
              <li><%= link_to "Settings", '#' %></li>
              <li class="divider"></li>
              <li>
                #ログアウト
                <%= link_to "Log out", logout_path, method: :delete %>
              </li>
            </ul>
          </li>
        #ログインしていない場合
        <% else %>
          #ログインリンク
          <li><%= link_to "Log in", login_path %></li>
        <% end %>
      </ul>
    </nav>
  </div>
</header></pre> 
<blockquote class="quote-rort">ドロップダウン機能を有効にするため、Railsのapplication.jsファイルを通して、Bootstrapに同梱されているJavaScriptライブラリとjQueryを読み込むようアセットパイプラインに指示します </blockquote>
 
<pre class="lang:js mark:(6-7) decode:true " title="/sample_app/app/assets/javascripts/application.js" >・
・
・
//
//= require rails-ujs
//= require jquery
//= require bootstrap
//= require turbolinks
//= require_tree .</pre> 
<blockquote　class="quote-rort">ログインパスにアクセスして有効なユーザー (ユーザー名がexample@railstutorial.org、パスワードがfoobar) としてログインできるようになっているので、これまでの3つのセクションのコードを効率よくテストできるようになります</blockquote>
また、ブラウザを閉じるとcookieが消去され、再度ログインを要求されるようになっている
→クラウドIDE利用の場合はそうならないっぽい（他のブラウザを使って試しましょうとの事）
<h4>演習</h4>
<blockquoteclass="quote-rort">
<ol>
	<li>ブラウザのcookieインスペクタ機能を使って (8.2.1.1)、セッション用のcookieを削除してみてください。ヘッダー部分にあるリンクは非ログイン状態のものになっているでしょうか? 確認してみましょう。</li>
	<li>もう一度ログインしてみて、ヘッダーのレイアウトが変わったことを確認してみましょう。その後、ブラウザを再起動させ、再び非ログイン状態に戻ったことも確認してみてください。注意: もしブラウザの [閉じたときの状態に戻す] 機能をオンにしていると、セッション情報も復元される可能性があります。もしその機能をオンにしている場合、忘れずにオフにしておきましょう</li>
</ol>
</blockquote>
動作確認のみにて省略
<h3>8.2.4 レイアウトの変更をテストする</h3>
ログインの統合テスト
<ol>	<li>ログイン用のパスを開く</li>
	<li>セッション用パスに有効な情報をpostする</li>
	<li>ログイン用リンクが表示されなくなったことを確認する</li>
	<li>ログアウト用リンクが表示されていることを確認する</li>
	<li>プロフィール用リンクが表示されていることを確認する</li>
</ol>
<h4>fixture</h4>
テスト用の初期データ（ざっくり）
<h4>自分でfixtureファイルを作成してデータを追加する</h4>
有効な名前とメールアドレス、パスワードを用意する
Sessionsコントローラのcreateアクションに送信されたパスワードと比較できるようにする必要もある
→password_digest属性をユーザーのfixtureに追加
<h4>digestメソッドを独自に定義する</h4>
<blockquote class="quote-rort">has_secure_passwordでbcryptパスワードが作成されるので、同じ方法でfixture用のパスワードを作成します。</blockquote>
<pre class="lang:ruby decode:true " title="コード解説" >#secure_passwordのソースコードのパスワード生成部分
#string→ハッシュ化する文字列　cost→ハッシュを算出するための計算コスト
BCrypt::Password.create(string, cost: cost)

#テスト中は最小で本番環境ではしっかりなコストパラメータの計算的な事
cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST :
                                              BCrypt::Engine.cost</pre> 
上記を利用したfixture向けのdigestメソッドをUserモデルに追加
<pre class="lang:ruby mark:(6-8) decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # 渡された文字列のハッシュ値を返す
  def User.digest(string)
    cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST :
                                                  BCrypt::Engine.cost
    BCrypt::Password.create(string, cost: cost)
  end
end
</pre> 
digestメソッドを定義したので有効なユーザーを表すfixtureが作成できるようになる
<pre class="lang:default mark:(4-5) decode:true " title="/sample_app/test/fixtures/users.yml" >michael:
  name: Michael Example
  email: michael@example.com
  #fixtureではERbを利用できる
  password_digest: <%= User.digest('password') %></pre> 
<blockquote class="quote-rort">has_secure_passwordで必要となるpassword_digest属性はこれで準備できましたが、ハッシュ化されていない生のパスワードも参照できると便利です。しかし残念なことに、fixtureではこのようなことはできません。</blockquote>
また、上記のusers.ymlにpassword属性を追加するとエラーが発生する（DBに「password」というカラムが存在しないため）
なので、よく使う手法としてテスト用のfixtureでは全員同じパスワード「password」とする
<pre class="lang:ruby decode:true " title="解説" >#有効なユーザー用のfixtureを作成したのでテストでfixtureのデータを参照できる
#users→fixtureのファイル名users.yml
#:michael→users.ymlで設定したユーザーを参照するためのキー
#userに　users.yml内の:michaelのデータを代入
user = users(:michael)
</pre> 
<h4>ログインのテスト</h4>
アクセス可能なユーザーデータが出来たのでレイアウトのリンクのテストを定義する
（前述の手順をコードに落とし込む）
<pre class="lang:ruby mark:（5-8,12-30） decode:true " title="/sample_app/test/integration/users_login_test.rb" >require 'test_helper'

class UsersLoginTest < ActionDispatch::IntegrationTest
  
  #fixtureのユーザーを読み込む
  def setup
    @user = users(:michael)
  end
・
・
・
  test "login with valid information" do
    #login_pathにgetのリクエスト
    get login_path
    #login_pathにposuのリクエスト　内容→params: { session: { email: @user.email, password: 'password' } }
    post login_path, params: { session: { email:    @user.email,
                                          password: 'password' } }
    #ユーザー詳細画面にリダイレクトされる
    assert_redirected_to @user
    #実際にリダイレクト先に移動
    follow_redirect!
    #sers/showが描写される
    assert_template 'users/show'
    #login_pathへのリンクの数が0である
    assert_select "a[href=?]", login_path, count: 0
    #logout_pathへのリンクがある
    assert_select "a[href=?]", logout_path
    #user_path(@user)へのリンクがある
    assert_select "a[href=?]", user_path(@user)
  end
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>試しにSessionヘルパーのlogged_in?メソッドから!を削除してみて、リスト 8.23が redになることを確認してみましょう。</li>
<li>先ほど削除した部分 (!) を元に戻して、テストが greenに戻ることを確認してみましょう。</li>
</ol>
</blockquote>
1.！を取るとユーザーがnilの時trueになるためRED
2.戻せば戻る
<h3>8.2.5 ユーザー登録時にログイン</h3>
<blockquote class="quote-rort">以上で認証システムが動作するようになりましたが、今のままでは、登録の終わったユーザーがデフォルトではログインしていないので、ユーザーがとまどう可能性があります。</blockquote>
ユーザー登録時にログインする設定
<pre class="lang:ruby mark:(7-8) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
  def create
    @user = User.new(user_params)
    if @user.save
      #sessions　helperで定義したlog_inメソッド
      log_in @user
      flash[:success] = t('.welcome_message')
      redirect_to @user
    else
      render 'new'
    end
  end
・
・
・</pre> 
<h4>ユーザー登録時にログインする動作のテスト</h4>
logged_in?ヘルパーメソッドとは別に、is_logged_in?ヘルパーメソッドを定義する
→取り違えやら何やら何やらを防ぐために名前をかぶらせないように気を付ける！
<blockquote>残念ながらヘルパーメソッドはテストから呼び出せないので、リスト 8.18のようにcurrent_userを呼び出せません。sessionメソッドはテストでも利用できるので、これを代わりに使います。</blockquote>
<pre class="lang:ruby mark:（8-12） decode:true " title="/sample_app/test/test_helper.rb" >・
・
・
  fixtures :all
  include ApplicationHelper

  # Add more helper methods to be used by all tests here...
  # テストユーザーがログイン中の場合にtrueを返す
  def is_logged_in?
    #sessionの:user_idがnilではない
    !session[:user_id].nil?
  end
end</pre> 
is_logged_in?ヘルパーメソッドを使って
ユーザー登録後にログイン状態になっているかをテストする
<pre class="lang:default mark:(15-16) decode:true " title="/sample_app/test/integration/users_signup_test.rb" >・
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
    assert_template 'users/show'
    assert_not flash.empty?
    #テストユーザーがログインしている
    assert is_logged_in?
  end
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>リスト 8.25のlog_inの行をコメントアウトすると、テストスイートは red になるでしょうか? それとも green になるでしょうか? 確認してみましょう。</li>
	<li>現在使っているテキストエディタの機能を使って、リスト 8.25をまとめてコメントアウトできないか調べてみましょう。また、コメントアウトの前後でテストスイートを実行し、コメントアウトすると red に、コメントアウトを元に戻すと green になることを確認してみましょう。ヒント: コメントアウト後にファイルを保存することを忘れないようにしましょう。また、テキストエディタのコメントアウト機能については Test Editor Tutorial の Commenting Out (英語) などを参照してみてください。</li>
</ol>
</blockquote>
1.log_inの行→コメントアウトするとユーザー登録直後にログイン状態にならないのでRED
2.[ctrl+A]で全選択→[ctrl+/]でまとめてコメントアウト（Win）→保存→RED→戻す→GREEN
<h3>まとめとか感想</h3>
ログインの仕組みを作る
ユーザー登録時にそのままログイン状態になるって言うのは大事！と、思いました🐫

ちゃんと頭でわかりながら進められているか不安だけど
分からない所はメモってるし、後で気づいたら書き足せばいいから良し！とする！！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

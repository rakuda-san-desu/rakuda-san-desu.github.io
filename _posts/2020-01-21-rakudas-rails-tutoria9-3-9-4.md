---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜9.3（と9.4）"
date: "2020-01-21 15:37:53"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1218
wordpress_status: "publish"
permalink: "/rakudas-rails-tutoria9-3-9-4/"
---
<h2>9.3 [Remember me] のテスト</h2>
<strong>動作をテストで確認できるようにしておくことが重要です！！</strong>
→ユーザーを永続化するコードのメイン部分は未だ全くテストされていないのであった
<h3>9.3.1 [Remember me] ボックスをテストする</h3>
<h4>log_in_asヘルパーメソッドを定義</h4>
ユーザーが記憶されるためにはログインが必要なので
テスト内でユーザーがログインできるようにするためのする
→単体テスト・統合テストそれぞれで使えるように定義していく
<pre class="lang:ruby mark:（16-19,22-31) decode:true " title="/sample_app/test/test_helper.rb" >・
・
・
class ActiveSupport::TestCase
  # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
  fixtures :all
  include ApplicationHelper

  # Add more helper methods to be used by all tests here...
  # テストユーザーがログイン中の場合にtrueを返す
  def is_logged_in?
    #sessionの:user_idがnilではない
    !session[:user_id].nil?
  end
  
  # テストユーザーとしてログインする
  def log_in_as(user)
    session[:user_id] = user.id
  end  
end

#統合テストで扱うヘルパーはActionDispatch::IntegrationTestクラスの中で定義
class ActionDispatch::IntegrationTest

  # テストユーザーとしてログインする
  def log_in_as(user, password: 'password', remember_me: '1')
    post login_path, params: { session: { email: user.email,
                                          password: password,
                                          remember_me: remember_me } }
  end
end</pre> 
メソッド名を同じlog_in_asとすることで
ログイン済の状態をテストしたい時には単体テストか統合テストかを意識しなくてもlog_in_asメソッドを呼び出せばいよいとの事！
<blockquote class="quote-rort">テストコードがより便利になるように、log_in_asメソッド (リスト 9.24) ではキーワード引数 (リスト 7.13) のパスワードと [remember me] チェックボックスのデフォルト値を、それぞれ'password'と'1'に設定しています。</blockquote>
<h4>2種類のテスト</h4>
チェックボックスの動作を確認するために
<ul>
	<li>チェックボックスがオンになっている場合のテスト</li>
	<li>チェックボックスがオフになっている場合のテスト</li>
</ul>
→関連するcookieがnilかどうかのチェックをする
<pre class="toolbar:2 lang:default decode:true " >#テスト内ではcookiesメソッドにシンボルを使えない
cookies[:remember_token]

#↑では常にnilになってしまうため、文字列をキーにすることで期待する値が返ってくるようにする
cookies['remember_token']</pre> 
ここまでを踏まえてテストを定義
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/users_login_test.rb" >・
・
・
  #チェックボックスオン時のテスト
  test "login with remembering" do
    #cookieを保存してログイン
    log_in_as(@user, remember_me: '1')
    #cookies['remember_token']はempty?ではない
    assert_not_empty cookies['remember_token']
  end

  #チェックボックスオフ時のテスト
  test "login without remembering" do
    # クッキーを保存してログイン
    log_in_as(@user, remember_me: '1')
    #logout_pathへdeleteのリクエスト
    delete logout_path
    # クッキーを保存しないでログイン
    log_in_as(@user, remember_me: '0')
    #cookies['remember_token']はempty?である
    assert_empty cookies['remember_token']
  end
end</pre> 
18行目、元々は「cookieを削除してログイン」ってしちゃってたんだけど
「保存しないでログイン」が良いかな？と助言をいただいて見直したところ
<ul>
	<li>16行目でログアウト（セッションを削除して@current_userをnilで上書き）している</li>
	<li>チェックボックス0は情報を記憶させない</li>
</ul>
なので「クッキーを保存しないでログイン」に修正しました
<h4>演習</h4>
<blockquote class="quote-rort">リスト 9.25の統合テストでは、仮想のremember_token属性にアクセスできないと説明しましたが、実は、assignsという特殊なテストメソッドを使うとアクセスできるようになります。コントローラで定義したインスタンス変数にテストの内部からアクセスするには、テスト内部でassignsメソッドを使います。このメソッドにはインスタンス変数に対応するシンボルを渡します。例えばcreateアクションで@userというインスタンス変数が定義されていれば、テスト内部ではassigns(:user)と書くことでインスタンス変数にアクセスできます。本チュートリアルのアプリケーションの場合、Sessionsコントローラのcreateアクションでは、userを (インスタンス変数ではない) 通常のローカル変数として定義しましたが、これをインスタンス変数に変えてしまえば、cookiesにユーザーの記憶トークンが正しく含まれているかどうかをテストできるようになります。このアイデアに従ってリスト 9.27とリスト 9.28の不足分を埋め (ヒントとして?やFILL_INを目印に置いてあります)、[remember me] チェックボックスのテストを改良してみてください。</blockquote>
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >・
・
・
  #変数userをインスタンス変数@userに変更
  def create
    @user = User.find_by(email: params[:session][:email].downcase)
    if @user && @user.authenticate(params[:session][:password])
      #session[:user_id] = @user　と言う事
      log_in @user
      #params[:session][:remember_me]が1の時@userを記憶　そうでなければuserを忘れる
      params[:session][:remember_me] == '1' ? remember(@user) : forget(@user)
      #user_url(user)　という名前付きルートになる
      redirect_to @user
    else
      flash.now[:danger] = t('.login_error')
      render 'new'
    end
  end
・
・
・</pre> 
<pre class="lang:ruby mark:(6-7) decode:true " title="/sample_app/test/integration/users_login_test.rb" >・
・
・
  #チェックボックスオン時のテスト
  test "login with remembering" do
    #cookieを保存してログイン
    log_in_as(@user, remember_me: '1')
    #cookiesのremember_tokenと@userのremember_tokenが等しいときにtrue
    assert_equal cookies['remember_token'], assigns(:user).remember_token
  end
・
・
・</pre> 
<h4>assignsメソッドとはなんぞ</h4>
テスト内部でインスタンス変数にアクセスするためのメソッド
インスタンス変数に対応するシンボルを渡すことでアクセスできる
→assigns(:user)　@userにアクセス
<h4>assert_equalとは何ぞ</h4>
→<a href="https://rakuda3desu.net/rakudas-rails-tutorial3-4#what_is_assert" rel="noopener noreferrer" target="_blank">らくだ🐫にもできるRailsチュートリアル｜3.4</a>アサーションメソッド
第一引数と第二引数の値が等しい時にtrue
<h3>9.3.2 [Remember me] をテストする</h3>
sessions_helperに定義したcurrent_user内にある分岐処理についてのテストが全く行われていないのであった！
<blockquote class="quote-rort">筆者はこのようなとき、テストを忘れている疑いのあるコードブロック内にわざと例外発生を仕込むというテクニックを使います。つまり、そのコードブロックがテストから漏れていれば、テストはパスしてしまうはずです。コードブロックがテストから漏れていなければ、例外が発生してテストが中断するはずです。</blockquote>
current_userメソッドに例外が発生するように手を加えてテストしてみる→GREEN→Ω ΩΩ< ﾅ､ﾅﾝﾀﾞｯﾃｰ!! 
<pre class="lang:ruby mark:(9-9) decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >  .
  .
  .
  # 記憶トークンcookieに対応するユーザーを返す
  def current_user
    if (user_id = session[:user_id])
      @current_user ||= User.find_by(id: user_id)
    elsif (user_id = cookies.signed[:user_id])
      raise       # テストがパスすれば、この部分がテストされていないことがわかる
      user = User.find_by(id: user_id)
      if user && user.authenticated?(cookies[:remember_token])
        log_in user
        @current_user = user
      end
    end
  end
  .
  .
  .</pre> 
9.3.1で定義したlog_in_asヘルパーメソッド（テスト用のログインを行うヘルパーメソッド）ではsession[:user_id]と定義していることもあり
current_userメソッドが抱えている複雑な分岐処理を統合テストでチェックするのは大変！

なんで？↓
<blockquote class="quote-rort">統合テストではsessionを直接取り扱うことができないので</blockquote>
解決策としてSessionsヘルパーのテストでcurrent_userを直接テストする
<pre class="lang:default decode:true " title="sessions_helper_test.rbをつくる" >$ touch test/helpers/sessions_helper_test.rb</pre> 
<blockquote class="quote-rort">
<ol>
	<li>fixtureでuser変数を定義する</li>
	<li>渡されたユーザーをrememberメソッドで記憶する</li>
	<li>current_userが、渡されたユーザーと同じであることを確認します</li>
</ol>
</blockquote>
 上記を踏まえたテストかこちら
<pre class="lang:ruby decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >require 'test_helper'

class SessionsHelperTest < ActionView::TestCase
  #リスト1.2、fixtureでuser変数を定義してrememberメソッドで記憶
  def setup
    @user = users(:michael)
    remember(@user)
  end
  
  #リスト3、current_userが、渡されたユーザーと同じであるか確認
  test "current_user returns right user when session is nil" do
    # @userとcurrent_userが等しければtrue
    assert_equal @user, current_user
    #テストユーザーがログイン中であればtrue
    assert is_logged_in?
  end

  #ユーザーの記憶ダイジェストが記憶トークンと正しく対応していない場合に現在のユーザーがnilになるか
  test "current_user returns nil when remember digest is wrong" do
    #@userのremember_digestを新しい値に更新して保存（setupと異なる値になるため現状のremember_tokenに対応しなくなる）
    @user.update_attribute(:remember_digest, User.digest(User.new_token))
    #current_userがnilの時true
    assert_nil current_user
  end
end
</pre> 
<h4>演習</h4>
動作確認のみにて省略
<h2>9.4 最後に</h2>
7章から9章でユーザー登録からログイン機構までを勉強しました🐫
<blockquote class="quote-rort">基本的な認証機構に必要な残りの作業は、ログイン状態やログイン済みのユーザーIDに基いて、ページへのアクセス権限を制限する実装 (認可モデル) だけです。次の章では、ログイン済みのユーザーであれば自分のプロフィール情報を編集できるようにしていきましょう。</blockquote>
<h4>Herokuのメンテナンスモード</h4>
HerokuにデプロイしてもHeroku上でマイグレーションを実行するまでは一時的にアクセスできない状態になる（エラーページが表示される）
→マイグレーションの実行が必要なデプロイをする場合メンテナンスモードをオンにしておくのが一般的
<pre class="lang:default decode:true " title="ターミナル" >$ heroku maintenance:on        #メンテナンスモードをオン
$ git push heroku              #デプロイ
$ heroku run rails db:migrate  #マイグレーションの実行
$ heroku maintenance:off       #メンテナンスモードをオフ</pre> 
<h3>9.4.1 本章のまとめ</h3>
<a href="https://railstutorial.jp/chapters/advanced_login?version=5.1#sec-advanced_login_what_we_learned_in_this_chapter">RoRT本文</a>ママ
<h3>まとめとか感想</h3>
ヘルパーメソッドの内容が思い出せなくて
あっちに行ったりこっちに行ったりしながらどうにか読み解くことができました。
出来ました？（？

あと全然関係ないんだけど、
ノートパソコンを手に入れたので念願のカフェもく☕しまして
この記事はお外で修正しましたっ✨ﾜｰｲ🐫
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

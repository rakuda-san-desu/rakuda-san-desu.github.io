---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜10.2"
date: "2020-02-18 15:36:42"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1265
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial10-2/"
---
<h2>10.2 認可</h2>
<blockquote class="quote-rort">ウェブアプリケーションの文脈では、認証 (authentication) はサイトのユーザーを識別することであり、認可 (authorization) はそのユーザーが実行可能な操作を管理することです。</blockquote>
現状ではログインしていないユーザーでもどのユーザー情報でも編集できてしまうため
ユーザーにログインを要求し、自分以外のユーザー情報を変更できないように制御する
→セキュリティ上の制御機構をセキュリティモデルと呼ぶ
<h4>やっていくこと</h4>
ログインしていないユーザーが保護されたページにアクセスしようとした場合ログインページに転送してメッセージを表示させる
さらに、許可されていないページにアクセス済のユーザーがいたらルートURLにリダイレクトさせる
<h3>10.2.1 ユーザーにログインを要求する</h3>
Usersコントローラの中でbeforeフィルターを使ってユーザーにログインを要求する
<h4>beforeフィルターとはなんぞ</h4>
<blockquote class="quote-rort">before_actionメソッドを使って何らかの処理が実行される直前に特定のメソッドを実行する仕組みです</blockquote>
<pre class="lang:ruby mark:(2-3,15-26) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  # 直前にlogged_in_userメソッドを実行　edit,updateアクションにのみ適用
  before_action :logged_in_user, only: [:edit, :update]
・
・
・
  # 外部に公開されないメソッド
  private

    def user_params
      params.require(:user).permit(:name, :email, :password,
                                   :password_confirmation)
    end
    
    # beforeアクション

    # ログイン済みユーザーかどうか確認
    def logged_in_user
      # logged_in?メソッドがfalseの場合
      unless logged_in?
        # flashｓでエラーメッセージを表示
        flash[:danger] = "Please log in."
        # login_urlにリダイレクト
        redirect_to login_url
      end
    end
end</pre> 
<h4>unless文</h4>
if文が条件式の評価がtrueの場合に処理を実行するのに対して
unless文は条件式の評価がfalseの場合に処理を実行する
上記の場合
ヘルパーに定義したlogged_in?メソッドがfalse（current_userがnil）の場合に処理（エラーメッセージの表示→login_urlにリダイレクト）が行われる
↓SessionsHelperに定義されているlogged_in?メソッドとlogged_in?メソッドで呼んでるcurrent_userメソッドはこちら
<pre class="lang:default decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >module SessionsHelper
・
・
・
  # 記憶トークンcookieに対応するユーザーを返す
  def current_user
    if (user_id = session[:user_id])
      @current_user ||= User.find_by(id: user_id)
    elsif (user_id = cookies.signed[:user_id])
      user = User.find_by(id: user_id)
      if user && user.authenticated?(cookies[:remember_token])
        log_in user
        @current_user = user
      end
    end
  end
  
  # ユーザーがログインしていればtrue、その他ならfalseを返す
  def logged_in?
    !current_user.nil?
  end
・
・
・</pre> 
<h4>現状のテストはRED </h4>
<blockquote class="quote-rort">原因は、editアクションやupdateアクションでログインを要求するようになったため、ログインしていないユーザーだとこれらのテストが失敗するようになったためです。</blockquote>
→editアクションやupdateアクションをテストする前にログインしておく必要がある！
→log_in_asヘルパー (test_helper.rbに定義) を使う
<pre class="lang:ruby mark:(5-6,13-14) decode:true " title="/sample_app/test/integration/users_edit_test.rb" >・
・
・
  test "unsuccessful edit" do
    # test userとしてログイン
    log_in_as(@user)
    # edit_user_path(@user)にgetのリクエスト
    get edit_user_path(@user)
・
・
・
  test "successful edit" do
    # test userとしてログイン
    log_in_as(@user)
    # edit_user_path(@user)にgetのリクエスト
・
・
・</pre> 
<blockquote class="quote-rort">(リスト 10.17のsetupメソッド内でログイン処理をまとめてしまうことも可能です。しかし、10.2.3で片方のテストをログインする前に編集ページにアクセスするように変更したいので、ここでまとめてしまっても結局は元に戻すことになってしまいます。)</blockquote>
<h4>セキュリティモデルに関するテスト</h4>
現状ではセキュリティモデルに関する実装を取り外しても（beforeフィルターをコメントアウト）テストが greenになってしまう！
これでは危険が危ないので検知できるようにテストを修正してく
→beforeフィルターは基本的にアクションごとに適用していく。よってUsersコントローラのテストもアクションごとに書いていく。
<ol>	<li>正しい種類のHTTPリクエストでeditアクション（getのリクエスト）とupdateアクション（patchのリクエスト）をそれぞれ実行</li>
	<li>flashにメッセージが代入されたか</li>
	<li>ログイン画面にリダイレクトされたか</li>
</ol>
<pre class="lang:ruby decode:true " title="/sample_app/test/controllers/users_controller_test.rb" >require 'test_helper'

class UsersControllerTest < ActionDispatch::IntegrationTest
・
・
・
  test "should get new" do
    get signup_path
    assert_response :success
  end
  
  test "should redirect edit when not logged in" do
    # edit_user_path(@user)にgetのリクエスト
    get edit_user_path(@user)
    # flashがemptyではない（flashが表示されている→エラー表示）
    assert_not flash.empty?
    # login_urlにリダイレクトされる
    assert_redirected_to login_url
  end

  test "should redirect update when not logged in" do
    # paramsハッシュに以下のデータを持たせてuser_path(@user)にpatchのリクエスト
    patch user_path(@user), params: { user: { name: @user.name,
                                              email: @user.email } }
    # flashがemptyではない（flashが表示されている→エラー表示）
    assert_not flash.empty?
    # login_urlにリダイレクトされる
    assert_redirected_to login_url
  end

end</pre> 
テストが意図の通りに作動するか確認
<pre class="lang:ruby mark:(2-4) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  #before_action～をコメントアウトするとテストはREDに
  #コメントアウトを外すとテストはGREENになる
  before_action :logged_in_user, only: [:edit, :update]
  .
  .
  .
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">デフォルトのbeforeフィルターは、すべてのアクションに対して制限を加えます。今回のケースだと、ログインページやユーザー登録ページにも制限の範囲が及んでしまうはずです (結果としてテストも失敗するはずです)。リスト 10.15のonly:オプションをコメントアウトしてみて、テストスイートがそのエラーを検知できるかどうか (テストが失敗するかどうか) 確かめてみましょう。</blockquote> 
<pre class="lang:default decode:true " title="ターミナルテスト結果" > FAIL["test_invalid_signup_information", UsersSignupTest, 0.8014602010002818]
 test_invalid_signup_information#UsersSignupTest (0.80s)
        expecting <"users/new"> but rendering with <[]>
        test/integration/users_signup_test.rb:13:in `block in <class:UsersSignupTest>'

 FAIL["test_valid_signup_information", UsersSignupTest, 0.8080838440000662]
 test_valid_signup_information#UsersSignupTest (0.81s)
        "User.count" didn't change by 1.
        Expected: 2
          Actual: 1
        test/integration/users_signup_test.rb:21:in `block in <class:UsersSignupTest>'

 FAIL["test_should_get_new", UsersControllerTest, 0.9231980270001259]
 test_should_get_new#UsersControllerTest (0.92s)
        Expected response to be a <2XX: success>, but was a <302: Found> redirect to <http://www.example.com/login>
        Response body: <html><body>You are being <a href="http://www.example.com/login">redirected</a>.</body></html>
        test/controllers/users_controller_test.rb:12:in `block in <class:UsersControllerTest>'
</pre> 
これちょっと悩んで、
本文には<strong>ログインページやユーザー登録ページにも制限の範囲が及んでしまうはず</strong>ってあるんだけど
ユーザー登録ページ関連にはエラーも出てるし実際動作もおかしくなる（ログインページに飛ばされるループになる）けど
ログインページではエラーも異常も出ないんだよね。
本文通りならログインページ関連にもエラーが出ないとおかしいのでは？どこか間違ってるのでは？ってなったんだけど
ログインページ関連はSessionsControllerなのでUsersControllerのbeforeフィルターの制限が及ぶわけないのよね。
と、納得した🐫
<h3>10.2.2 正しいユーザーを要求する</h3>
ログインを要求するだけではなく、ログインしたユーザーが自分だけの情報を編集できるようにする。
→現状ではログインさえしていれば他ユーザーの情報も更新できてしまう
ログインしているからと言って、他のユーザーの情報まで編集できてしまうのは🙅‍♀️
これをテスト駆動開発でやっていく！
<h4>他ユーザーの情報が編集できないかを確認するテスト</h4>
テスト用のデータに2人目のユーザーを作る
<pre class="lang:default mark:(6-9) decode:true " title="/sample_app/test/fixtures/users.yml" >michael:
  name: Michael Example
  email: michael@example.com
  password_digest: <%= User.digest('password') %>
  
 archer:
  name: Sterling Archer
  email: duchess@example.gov
  password_digest: <%= User.digest('password') %></pre> 
log_in_asメソッドを使って、editアクションとupdateアクションをテストする
<blockquote class="quote-rort">このとき、既にログイン済みのユーザーを対象としているため、ログインページではなくルートURLにリダイレクトしている点に注意してください。</blockquote>
<pre class="lang:ruby mark:(8-9,14-35） decode:true " title="/sample_app/test/controllers/users_controller_test.rb" >require 'test_helper'

class UsersControllerTest < ActionDispatch::IntegrationTest
  
  # テストユーザーを設定
  def setup
    @user = users(:michael)
    # 追加したユーザーを@other_userに代入
    @other_user = users(:archer)
  end
・
・
・
  test "should redirect edit when logged in as wrong user" do
    # @other_userとしてログインする
    log_in_as(@other_user)
    # @userのユーザー情報編集ページにgetのリクエスト
    get edit_user_path(@user)
    # フラッシュがemptyである
    assert flash.empty?
    # root_urlにリダイレクトされる
    assert_redirected_to root_url
  end

  test "should redirect update when logged in as wrong user" do
    # @other_userとしてログインする
    log_in_as(@other_user)
    # @userのupdateアクションにparamsハッシュのデータを持たせてPATCHのリクエスト　　
    patch user_path(@user), params: { user: { name: @user.name,
                                              email: @user.email } }
    # フラッシュがemptyである
    assert flash.empty?
    # root_urlにリダイレクトされる
    assert_redirected_to root_url
  end

end
</pre> 
<h4>別のユーザーのプロフィールを編集しようとしたらリダイレクトさせる</h4>
beforeフィルターで管理
→correct_userというメソッドを作成し、ログインユーザーが別のユーザーのプロフィールを編集しようとしたらリダイレクトさせる
<blockquote class="quote-rort">beforeフィルターのcorrect_userで@user変数を定義しているため、リスト 10.25ではeditとupdateの各アクションから、@userへの代入文を削除している点にも注意してください。</blockquote>
<pre class="lang:ruby mark:(4-5,11-12,16-17,49-54) decode:true " title="/sample_app/test/controllers/users_controller_test.rb" >class UsersController < ApplicationController
  # 直前にlogged_in_userメソッドを実行　edit,updateアクションにのみ適用
  before_action :logged_in_user, only: [:edit, :update]
  # 直前にcorrect_userメソッドを実行　edit,updateアクションにのみ適用
  before_action :correct_user,   only: [:edit, :update]
・
・
・
  #ユーザーのeditアクション
  def edit
    # 直前に実行されるcorrect_userメソッドで定義されているため下記代入文は削除
    # @user = User.find(params[:id])
  end
  
  def update
    # 直前に実行されるcorrect_userメソッドで定義されているため下記代入文は削除
    # @user = User.find(params[:id])
    #指定された属性の検証がすべて成功した場合@userの更新と保存を続けて同時に行う
    if @user.update_attributes(user_params)
      # 更新成功のフラッシュメッセージ
      flash[:success] = t('.profile_updated')
      # @user（プロフィールページ）へリダイレクト
      redirect_to @user
    else
      render 'edit'
    end
  end
  
  # 外部に公開されないメソッド
  private

    def user_params
      params.require(:user).permit(:name, :email, :password,
                                   :password_confirmation)
    end
    
    # beforeアクション

    # ログイン済みユーザーかどうか確認
    def logged_in_user
      # logged_in?がfalseの場合
      unless logged_in?
        # flashsでエラーメッセージを表示
        flash[:danger] = t('users.please_log_in')
        # login_urlにリダイレクト
        redirect_to login_url
      end
    end
    
    # 正しいユーザーかどうか確認
    def correct_user
      @user = User.find(params[:id])
      # root_urlにリダイレクト　以下の式がfalseの場合　@userとcurrent_userが等しい
      redirect_to(root_url) unless @user == current_user
    end
end</pre> 
<h4>リファクタリング</h4>
<blockquote class="quote-rort">一般的な慣習に倣ってcurrent_user?という論理値を返すメソッドを実装します</blockquote>
<pre class="toolbar:2 lang:default decode:true " >unless @user == current_user
#↑これ　が　↓こうなって　コードが少しわかりやすくなるとの事
unless current_user?(@user)</pre> 
correct_userの中で使えるようにする為にSessionsヘルパーの中に追加する
 
<pre class="lang:ruby mark:(15-18) decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >module SessionsHelper
  
  # 渡されたユーザーでcookieを使ってログインする
  def log_in(user)
    session[:user_id] = user.id
  end
  
  # ユーザーのセッションを永続的にする
  def remember(user)
    user.remember
    cookies.permanent.signed[:user_id] = user.id
    cookies.permanent[:remember_token] = user.remember_token
  end
  
  # 渡されたユーザーがログイン済みユーザーであればtrueを返す
  def current_user?(user)
    user == current_user
  end
・
・
・</pre> 
Usersコントローラーに反映させる
<pre class="lang:ruby mark:(18-18) decode:true " title="/sample_app/app/controllers/users_controller.rb" >    # beforeアクション

    # ログイン済みユーザーかどうか確認
    def logged_in_user
      # logged_in?がfalseの場合
      unless logged_in?
        # flashsでエラーメッセージを表示
        flash[:danger] = t('users.please_log_in')
        # login_urlにリダイレクト
        redirect_to login_url
      end
    end
    
    # 正しいユーザーかどうか確認
    def correct_user
      @user = User.find(params[:id])
      # root_urlにリダイレクト　current_user?メソッドがfalseの場合
      redirect_to(root_url) unless current_user?(@user)
    end
end</pre> 

<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>何故editアクションとupdateアクションを両方とも保護する必要があるのでしょうか? 考えてみてください。</li>
	<li>上記のアクションのうち、どちらがブラウザで簡単にテストできるアクションでしょうか?</li>
</ol>
</blockquote>
<ol>
	<li>他のユーザーのユーザー情報編集ページを表示させない為にeditアクション、データを更新させないためにupdateアクションをそれぞれ保護する必要がある</li>
	<li>viewが定義されているeditアクション</li>
</ol>
<h3>10.2.3 フレンドリーフォワーディング</h3>
各種リダイレクト先はユーザーが開こうとしていたページに設定するのが親切というもの！
<blockquote class="quote-rort">ログインしていないユーザーが編集ページにアクセスしようとしていたなら、ユーザーがログインした後にはその編集ページにリダイレクトされるようにするのが望ましい動作です。</blockquote>
<h4>フレンドリーフォワーディングのテスト</h4>
ログインのテスト（ログインした後に編集ページへアクセスするという順序）を逆にする
<ol>
	<li>編集ページにアクセス</li>
	<li>ログイン</li>
	<li>プロフィールページではなく編集ページにリダイレクト</li>
</ol>
<pre class="lang:ruby mark:(4-13) decode:true " title="/sample_app/test/integration/users_edit_test.rb" >・
・
・
  # successful editのテストを編集していく
  test "successful edit with friendly forwarding" do
    # edit_user_path(@user)にgetのリクエスト
    get edit_user_path(@user)
    # @userとしてログイン
    log_in_as(@user)
    # edit用のテンプレートはリダイレクトで描画されるので下記一文は削除
    # assert_template 'users/edit'
    # @userのユーザー編集ページにリダイレクトされる
    assert_redirected_to edit_user_url(@user)
    # nameに"Foo Bar"を代入
    name  = "Foo Bar"
    # emailに"foo@bar.com"を代入
    email = "foo@bar.com"
    # 有効なparams:を持ったuser_path(@user)でpatch（更新）のリクエスト
    patch user_path(@user), params: { user: { name:  name,
                                              email: email,
                                              password:              "",
                                              password_confirmation: "" } }
    # falseである→　flashが空っぽであるか
    assert_not flash.empty?
    # リダイレクトされている　→@user（プロフィールページ）
    assert_redirected_to @user
    # @user（プロフィールページ）を再読み込み
    @user.reload
    # name（入力値）と@user.name（DBの値）が等しい
    assert_equal name,  @user.name
    # email（入力値）と@user.email（DBの値）が等しい
    assert_equal email, @user.email
  end
end</pre> 
ん？
successful editのテストを編集するのででいいんだよね？（？
<h4>フレンドリーフォワーディングの実装 </h4>
<blockquote class="quote-rort">ユーザーを希望のページに転送するには、リクエスト時点のページをどこかに保存しておき、その場所にリダイレクトさせる必要があります。</blockquote>
Sessionヘルパーにstore_location（リクエスト時点のページを保存）とredirect_back_or（ユーザーを希望のページに転送）の二つのメソッドを定義する
<h4>２つのヘルパーメソッドの実装</h4>
store_locationとredirect_back_orの2つのメソッドを定義していく
 
<pre class="lang:ruby decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >・
・
・
  # 記憶したURL (もしくはデフォルト値) にリダイレクト
  def redirect_back_or(default)
    # リダイレクト（session[:forwarding_url]の値、値がnilデフォルト値へ)
    redirect_to(session[:forwarding_url] || default)
    # session変数の:forwarding_urlキーの値をdelete
    session.delete(:forwarding_url)
  end

  # アクセスしようとしたURLを覚えておく
  def store_location
    # session変数の:forwarding_urlキーに格納 request.original_urlで取得したリクエスト先urlにGETリクエストが送られたときのみ
    session[:forwarding_url] = request.original_url if request.get?
  end
end</pre> 
<h4>requestオブジェクト</h4>
ブラウザから返されるリクエストに関する情報が多数含まれている
[blogcard url="https://railsguides.jp/action_controller_overview.html#request%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88"]
request.original_url　→　現在のリクエストURLをStringとして返す
request.get?　→　HTTPメソッドがGETの時trueを返す
<h4>beforeフィルターを修正</h4>
store_locationメソッドを使って、早速beforeフィルターのlogged_in_userメソッドを修正
<pre class="lang:ruby mark:(10-11) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
    # beforeアクション

    # ログイン済みユーザーかどうか確認
    def logged_in_user
      # logged_in?がfalseの場合
      unless logged_in?
        # SessionsHelperのstore_locationメソッドを呼び出す
        store_location
        # flashsでエラーメッセージを表示
        flash[:danger] = t('users.please_log_in')
        # login_urlにリダイレクト
        redirect_to login_url
      end
    end
    
    # 正しいユーザーかどうか確認
    def correct_user
      @user = User.find(params[:id])
      # root_urlにリダイレクト　current_user?メソッドがfalseの場合
      redirect_to(root_url) unless current_user?(@user)
    end
end</pre> 
<h4>createアクションを修正</h4>
 createアクションにredirect_back_orメソッドを組み込んでリダイレクト先を呼び出す
<pre class="lang:ruby mark:(11-13) decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >・
・
・
  def create
    @user = User.find_by(email: params[:session][:email].downcase)
    if @user && @user.authenticate(params[:session][:password])
      #session[:user_id] = @user　と言う事
      log_in @user
      #params[:session][:remember_me]が1の時@userを記憶　そうでなければuserを忘れる
      params[:session][:remember_me] == '1' ? remember(@user) : forget(@user)
      #SessionsHelperで定義したredirect_back_orメソッドを呼び出してリダイレクト先を定義
      # 本文ではデフォルト値が「user」になってるけど「@user」が正解
      redirect_back_or @user
    else
      flash.now[:danger] = t('.login_error')
      render 'new'
    end
  end
・
・
・</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>フレンドリーフォワーディングで、渡されたURLに初回のみ転送されていることを、テストを書いて確認してみましょう。次回以降のログインのときには、転送先のURLはデフォルト (プロフィール画面) に戻っている必要があります。ヒント: リスト 10.29のsession[:forwarding_url]が正しい値かどうか確認するテストを追加してみましょう。</li>
	<li>7.1.3で紹介したdebuggerメソッドをSessionsコントローラのnewアクションに置いてみましょう。その後、ログアウトして /users/1/edit にアクセスしてみてください (デバッガーが途中で処理を止めるはずです)。ここでコンソールに移り、session[:forwarding_url]の値が正しいかどうか確認してみましょう。また、newアクションにアクセスしたときのrequest.get?の値も確認してみましょう (デバッガーを使っていると、ときどき予期せぬ箇所でターミナルが止まったり、おかしい挙動を見せたりします。熟練の開発者になった気になって (コラム 1.1)、落ち着いて対処してみましょう)。</li>
</ol>
</blockquote>
1.
<pre class="lang:ruby mark:(7-8,13-14) decode:true " title="/sample_app/test/integration/users_edit_test.rb" >・
・
・
  test "successful edit with friendly forwarding" do
    # edit_user_path(@user)にgetのリクエスト
    get edit_user_path(@user)
    # session[:forwarding_url]とedit_user_url(@user)が等しい時にtrue
    assert_equal session[:forwarding_url], edit_user_url(@user)
    # @userとしてログイン
    log_in_as(@user)
    # edit用のテンプレートはリダイレクトで描画されるので下記一文は削除
    # assert_template 'users/edit'
    # session[:forwarding_url]がnilの時true
    assert_nil session[:forwarding_url]
    # @userのユーザー編集ページにリダイレクトされる
    assert_redirected_to edit_user_url(@user)
    # nameに"Foo Bar"を代入
    name  = "Foo Bar"
    # emailに"foo@bar.com"を代入
    email = "foo@bar.com"
    # 有効なparams:を持ったuser_path(@user)でpatch（更新）のリクエスト
    patch user_path(@user), params: { user: { name:  name,
                                              email: email,
                                              password:              "",
                                              password_confirmation: "" } }
・
・
・</pre> 
2.
session[:forwarding_url]の値 → ～/users/1/editとなるはず
<pre class="toolbar:2 lang:default decode:true " >(byebug) session[:forwarding_url]
"https:/*******amazonaws.com/users/1/edit"</pre> 
request.get?の値 → trueとなるはず
<pre class="toolbar:2 lang:default decode:true " >(byebug) request.get?
true</pre> 
<h3>まとめとか感想</h3>
本文にちょこちょこ引っかかりまくって思いのほか時間がかかったりだけど、その分しっかり調べたから！と、思う🐫
フレンドリーフォワーディングたいせつ
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

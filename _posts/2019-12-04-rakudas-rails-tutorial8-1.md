---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜8.1"
date: "2019-12-04 17:03:59"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者", "参考サイト"]
published: true
wordpress_id: 1051
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial8-1/"
---
8章でやる事
→ユーザーがログインやログアウトを出来る様にする
<blockquote class="quote-rort">ここでいうログインの基本的な仕組みとは、ブラウザがログインしている状態を保持し、ユーザーによってブラウザが閉じられたら状態を破棄するといった仕組み (認証システム (Authentification System))です。この認証システムの基盤が出来上がったら、ログイン済みのユーザー (current user) だけがアクセスできるページや、扱える機能などを制御していきます。</blockquote>
※適宜日本語化しているので本文とコードが違う部分があります
<h2>8.1 セッション</h2>
HTTPのリクエスト1つ1つは独立した処理の単位で、それより前のリクエストの情報は利用できない
→あるページから別のページに移動した時にユーザー情報を保持する手段がHTTPプロトコル内には無い
→sessionと呼ばれる接続を別途設定する
<h4>cookie</h4>
<a href="https://ja.wikipedia.org/wiki/HTTP_cookie" rel="noopener noreferrer" target="_blank">cookie</a>→セッションを実装する一般的な方法
→ユーザーのブラウザに保存される小さなテキストデータ
<blockquote class="quote-rort">ログインの仕組みの大半は、cookiesを使った認証メカニズムによって構築されています。</blockquote>
例によって作業用ブランチを作ってから開始ー！
 
<pre class="lang:default decode:true " title="ターミナル" >$ git checkout -b basic-login</pre> 


<h3>8.1.1 Sessionsコントローラ</h3>
<blockquote class="quote-rort">ログインとログアウトの要素を、Sessionsコントローラの特定のRESTアクションにそれぞれ対応付けることにします。ログインのフォームは、この節で扱うnewアクションで処理します。createアクションにPOSTリクエストを送信すると、実際にログインします (8.2)。destroyアクションにDELETEリクエストを送信すると、ログアウトします (8.3) (表 7.1のHTTPメソッドとRESTアクションの関連付けを思い出しましょう)。</blockquote>
<pre class="lang:default decode:true " title="ターミナル" >rails generate controller Sessions new</pre> 
Rails gでアクションを生成すると、それに対応するビューも生成されるので
対応するビューが必要ないcreateやdestroyはコマンドで指定していない
<h3>routes.rb</h3>
SessionリソースではフルセットのRESTfulなルーティングは必要ない
→「名前付きルーティング」だけを使用
<pre class="lang:ruby mark:(3-3,12-12,15-17) decode:true " title="/sample_app/config/routes.rb" >Rails.application.routes.draw do
  #自動的に追加されているこの行を削除
  get 'sessions/new'
 
  root 'static_pages#home'
  get  '/help',    to:'static_pages#help'
  get  '/about',   to:'static_pages#about'
  get  '/contact', to:'static_pages#contact'
  
  get  '/signup',  to:'users#new'
  #本文では消えてるけど消すとエラーになるよ！
　post '/signup',  to: 'users#create'
  
  #sessionsリソースの名前付きルートを追加
  get    '/login',   to: 'sessions#new'
  post   '/login',   to: 'sessions#create'
  delete '/logout',  to: 'sessions#destroy'
  resources :users
end</pre> 
<pre class="lang:ruby mark:(7-7) decode:true " title="/sample_app/test/controllers/sessions_controller_test.rb" >require 'test_helper'
 
class SessionsControllerTest < ActionDispatch::IntegrationTest
 
  test "should get new" do
    #名前付きルートの修正
    get login_path
    assert_response :success
  end
end</pre> 
<h4>追加した全ルーティングを表示</h4>
<pre class="lang:default decode:true " title="ターミナル" >$ rails routes
   Prefix Verb   URI Pattern               Controller#Action
     root GET    /                         static_pages#home
     help GET    /help(.:format)           static_pages#help
    about GET    /about(.:format)          static_pages#about
  contact GET    /contact(.:format)        static_pages#contact
   signup GET    /signup(.:format)         users#new
          POST   /signup(.:format)         users#create
    login GET    /login(.:format)          sessions#new
          POST   /login(.:format)          sessions#create
   logout DELETE /logout(.:format)         sessions#destroy
    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
 new_user GET    /users/new(.:format)      users#new
edit_user GET    /users/:id/edit(.:format) users#edit
     user GET    /users/:id(.:format)      users#show
          PATCH  /users/:id(.:format)      users#update
          PUT    /users/:id(.:format)      users#update
          DELETE /users/:id(.:format)      users#destroy
</pre> 
まだよくわからないのも（PATCHとかPUTとか）あるけど多分後でやる
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
 	<li>GET login_pathとPOST login_pathとの違いを説明できますか? 少し考えてみましょう。</li>
 	<li>ターミナルのパイプ機能を使ってrails routesの実行結果とgrepコマンドを繋ぐことで、Usersリソースに関するルーティングだけを表示させることができます。同様にして、Sessionsリソースに関する結果だけを表示させてみましょう。現在、いくつのSessionsリソースがあるでしょうか? ヒント: パイプやgrepの使い方が分からない場合は Learn Enough Command Line to Be Dangerousの Section on Grep (英語) を参考にしてみてください。</li>
</ol>
</blockquote>
#1.
GET login_path　→　getのリクエスト　→　newアクションを呼び出す　→　viewの表示
POST login_path　→　postのリクエスト　→　createアクションを呼び出す　→　ログイン

#2.
本文の参考ページが英語過ぎて分からなかった🐫
[blogcard url="https://eng-entrance.com/linux-command-grep#grepand"]
<pre class="lang:default decode:true " title="ターミナル" >$ rails routes | grep users
   signup GET    /signup(.:format)         users#new
          POST   /signup(.:format)         users#create
    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
 new_user GET    /users/new(.:format)      users#new
edit_user GET    /users/:id/edit(.:format) users#edit
     user GET    /users/:id(.:format)      users#show
          PATCH  /users/:id(.:format)      users#update
          PUT    /users/:id(.:format)      users#update
          DELETE /users/:id(.:format)      users#destroy
$ rails routes | grep sessions
  #以下の3つ
    login GET    /login(.:format)          sessions#new
          POST   /login(.:format)          sessions#create
   logout DELETE /logout(.:format)         sessions#d</pre> 
<h3>8.1.2 ログインフォーム</h3>
セッションは<strong>Active Recordオブジェクトではない</strong>のでエラーメッセージの自動生成はされない
→flashメッセージでエラーを表示させる
<h4>Active Recordオブジェクトとはなんぞ</h4>
Active Recordを継承しているオブジェクト
Active Recordとは→
データオブジェクトの作成/保存/検索のためのメソッドを持つライブラリ
modelとDBの間でデータのやり取りをしてくれるモノ的な？
[blogcard url="https://qiita.com/Ryokky/items/a1d0b4e86bacbd7ef6e8"]
<h4>セッションフォームとユーザー登録フォームの違い</h4>
<blockquote class="quote-rort">セッションにはSessionモデルというものがなく、そのため@userのようなインスタンス変数に相当するものもない点です</blockquote>
<pre class="lang:ruby decode:true " title="コード解説" >#user登録フォームではインスタンス変数@userが使えた
<%= form_for(@user) do |f| %>
  .
  .
  .
<% end %>
 
#フォームのactionは/usersというURLへのPOSTと、自動判別してもらえた
form_for(@user)
 
#sessionの場合はモデルが無い(インスタンス変数にあたるものもない)のでリソース名と対応URLを具体的に指定する
form_for(:session, url: login_path)</pre> 
<h4>form_forとform_tag</h4>
どちらもフォームを作ってくれるヘルパーだけど働きがかなり違う
<blockquote class="quote-rort">form_forの代わりにform_tagを使うこともでき、Railsではこの方が慣用的な方法です。しかし、ユーザー登録フォームではform_forを使うのが一般的であり、並列構造を強調するためにもform_forを使いました。</blockquote>
参考
[blogcard url="https://railsguides.jp/form_helpers.html#%E3%83%A2%E3%83%87%E3%83%AB%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%82%92%E6%89%B1%E3%81%86"]
[blogcard url="https://qiita.com/chopesu_se/items/3749a4a657dbbba1258b"]
ざっくり言うと
モデルを通してテーブルに情報を追加・編集を行うフォーム→form_for
情報の追加・編集を行わないフォーム→form_tag
この考えで言うとログインフォームは情報の追加・編集が無いからform_tagじゃね?って思うけど
登録フォーム（modelに基づく）と並列構造なので敢えてform_forと言う事っぽい
<h4>from_with</h4>
form_forとform_tagはRails5.1では非推奨
→form_withに置き換えられる
[blogcard url="https://qiita.com/hmmrjn/items/24f3b8eade206ace17e2"]
<h4>ログインフォームのコード</h4>
ユーザー登録ページのコードを参考にログインフォームを作る
 
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/sessions_controller.rb" ><% provide(:title, "Log in") %>
<h1>Log in</h1>
 
<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(:session, url: login_path) do |f| %>
 
      <%= f.label :email, User.human_attribute_name(:email) %>
      <%= f.email_field :email, class: 'form-control' %>
 
      <%= f.label :password, User.human_attribute_name(:password) %>
      <%= f.password_field :password, class: 'form-control' %>
 
      <%= f.submit t('.login'), class: "btn btn-primary" %>
    <% end %>
 
    <p><%= t('.new_user?') %><%= link_to t('.signup'), signup_path %></p>
  </div>
</div></pre> 
<pre class="lang:default decode:true " title="ブラウザ（生成されたHTML）" >・
・
・
<form action="/login" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="✓" /><input type="hidden" name="authenticity_token" value="PdoqL4HNgCiEFSeDRDdgHw97G576CPgl5Y9LVNRmqECOCSHBk8GWePj6g5VrG1p1AZlYNBajurPwjwv88iuviA==" />
 
  <label for="session_email">メールアドレス</label>
  <input class="form-control" type="email" name="session[email]" id="session_email" />
 
  <label for="session_password">パスワード</label>
  <input class="form-control" type="password" name="session[password]" id="session_password" />
 
  <input type="submit" name="commit" value="ログインする" class="btn btn-primary" data-disable-with="ログインする" />
</form>
・
・
・</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">リスト 8.4で定義したフォームで送信すると、Sessionsコントローラのcreateアクションに到達します。Railsはこれをどうやって実現しているでしょうか? 考えてみてください。ヒント:表 8.1とリスト 8.5の１行目に注目してください。</blockquote>
<form accept-charset="UTF-8" action="/login" method="post">
→URL「/login」に「post」のリクエスト
→routes.rbに「post '/login', to: 'sessions#create'」が設定されている
→Sessionsコントローラのcreateアクションが実行される
<h3>8.1.3 ユーザーの検索と認証</h3>
無効な場合の処理から行っていく
<h4>最小限のアクションの設定</h4>
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >class SessionsController < ApplicationController
 
  def new
  end
 
  #loginフォームで「ログインする」ボタンを押すと
  #createアクションが実行され「sessions/new.html.erb」が描画される
  def create
    render 'new'
  end
 
  def destroy
  end
end</pre> 
<pre class="lang:default decode:true " title="ブラウザ（デバッグ情報）" >#<a href="https://railstutorial.jp/chapters/basic_login?version=5.1#fig-initial_failed_login_rails_3" rel="noopener noreferrer" target="_blank">RoRT本文</a>に倣ってemailとパスワードを入力したもの
  session: !ruby/hash:ActiveSupport::HashWithIndifferentAccess
    email: user@example.com
    password: foobar
  commit: ログインする
  controller: sessions
  action: create
permitted: false</pre>  
<pre class="lang:default decode:true " title="paramsハッシュはこうなっている" >{ session: { password: “foobar”, email: “user@example.com” } }</pre> 
<blockquote class="quote-rort">createアクションの中では、ユーザーの認証に必要なあらゆる情報をparamsハッシュから簡単に取り出せるということです。</blockquote>
<h4>ユーザーをデータベースから見つけて検証する</h4>
<pre class="lang:ruby mark:(9-9,11-11) decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >class SessionsController < ApplicationController
 
  def new
  end
 
  def create
    #userに代入　Userモデルから指定した条件の最初の1件を取得
    #条件→（emailカラム内にある　postされたsessionが持つemailの値と合致するデータをすべて小文字にしたもの）
    user = User.find_by(email: params[:session][:email].downcase)
    #userが存在する　かつ　postされたsessionが持つパスワードがそのuserのものと一致する
    if user && user.authenticate(params[:session][:password])
      # 後でユーザーログイン後にユーザー情報のページにリダイレクトするコードを追加する
    else
      # 後でエラーメッセージを作成するコードを追加する
      render 'new'
    end
  end
 
  def destroy
  end
end</pre> 
<blockquote class="quote-rort">Railsコンソールを使って、表 8.2のそれぞれの式が合っているか確かめてみましょう. まずはuser = nilの場合を、次にuser = User.firstとした場合を確かめてみてください。ヒント: 必ず論理値オブジェクトとなるように、4.2.3で紹介した!!のテクニックを使ってみましょう。例: !!(user && user.authenticate('foobar'))</blockquote>
動作確認のみにて省略
<h3>8.1.4 フラッシュメッセージを表示する</h3>
<blockquote class="quote-rort">ユーザー登録のエラーメッセージ表示にUserモデルのエラーメッセージをうまく利用したことを思い出しましょう。ユーザー登録の場合、エラーメッセージは特定のActive Recordオブジェクトに関連付けられていたのでその手が使えました。しかしセッションではActive Recordのモデルを使っていないため、その手が通用しません。</blockquote>
よって、flashでエラーメッセージを表示させる
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >#現状ではまだ正しくない
・
・
・
  def create
    user = User.find_by(email: params[:session][:email].downcase)
    if user && user.authenticate(params[:session][:password])
      # ユーザーログイン後にユーザー情報のページにリダイレクトする
    else
      flash[:danger] = 'Invalid email/password combination' # 本当は正しくない
      render 'new'
    end
  end
 
  def destroy
  end
end</pre> 
<pre class="lang:default mark:(8-8) decode:true " title="ブラウザ" >・
・
・
</header>
    <div class="container">
        #出力されたthmlのflashメッセージ部分
        #Bootstrapで適切なスタイルも与えられている
        <div class="alert alert-danger">Invalid email/password combination</div>
      <h1>Log in</h1>
・
・
・</pre> 
<h4>flashメッセージが消えない問題</h4>
現在のコードのままでは表示されたフラシュメッセージが残り続けてしまう
→renderメソッドで描画したページはリクエストとみなされないため
<h3>8.1.5 フラッシュのテスト</h3>
上記の「flashメッセージが消えない問題」はこのアプリの小さなバグ
→本文の<a href="https://railstutorial.jp/chapters/static_pages?version=5.1#aside-when_to_test" rel="noopener noreferrer" target="_blank">コラム 3.3</a>に照らし合わせると
「エラーをキャッチするテストを先に書いて、そのエラーが解決するようにコードを書く」に該当する状況
→早速テストを作成する！！
<h4>ログインフォームの送信についての統合テスト</h4>
統合テストを生成
<pre class="lang:default decode:true " title="ターミナル" >$ rails generate integration_test users_login
      invoke  test_unit
      create    test/integration/users_login_test.rb
</pre> 
下記の手順をテストコードで再現する
<blockquote class="quote-rort">
<ol>
 	<li>ログイン用のパスを開く</li>
 	<li>新しいセッションのフォームが正しく表示されたことを確認する</li>
 	<li>わざと無効なparamsハッシュを使ってセッション用パスにPOSTする</li>
 	<li>新しいセッションのフォームが再度表示され、フラッシュメッセージが追加されることを確認する</li>
 	<li>別のページ (Homeページなど) にいったん移動する</li>
 	<li>移動先のページでフラッシュメッセージが表示されていないことを確認する</li>
</ol>
</blockquote>
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/users_login_test.rb" >require 'test_helper'
 
class UsersLoginTest < ActionDispatch::IntegrationTest
 
  test "login with invalid information" do
    # login_pathにgetのリクエスト
    get login_path
    #sessions/newが描画される
    assert_template 'sessions/new'
    #login_pathにpostのリクエスト　内容→params: { session: { email: "", password: "" } }
    post login_path, params: { session: { email: "", password: "" } }
    #sessions/newが描画される
    assert_template 'sessions/new'
    #falseである　→flashはemptyか？
    assert_not flash.empty?
    #root_pathにgetのリクエスト
    get root_path
    #trueである　→flashはemptyか？
    assert flash.empty?
  end
end</pre> 
<pre class="lang:default decode:true " title="ターミナル" >#rails testの引数にテストファイルを与えるとそのテストファイルのみを実行する
$ rails test test/integration/users_login_test.rb
 
 FAIL["test_login_with_invalid_information", UsersLoginTest, 0.7772743069999706]
 test_login_with_invalid_information#UsersLoginTest (0.78s)
        Expected false to be truthy.
        #assert flash.empty?の部分でテストが失敗している
        test/integration/users_login_test.rb:19:in `block in <class:userslogintest>'
 
  1/1: [=================================================================] 100% Time: 00:00:00, Time: 00:00:00
 
Finished in 0.77969s
1 tests, 4 assertions, 1 failures, 0 errors, 0 skips
</class:userslogintest></pre> 
<h4>flash.now</h4>
flashをflash.nowに書き換える
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >・
・
・
    else
      flash.now[:danger] = t('.login_error')
      render 'new'
    end
  end
 
  def destroy
  end
end</pre> 
[blogcard url="https://railsguides.jp/action_controller_overview.html#flash-now"]
[blogcard url="https://qiita.com/shi-ma-da/items/ea433c337d2a691ff1bc"]
大雑把に考えると
flashメッセージを表示させたいページの描写が
→redirect_toの時はflash[:notice]
→renderの時はflash.now[:notice]
<h4>実習</h4>
<blockquote class="quote-rort">
<ol>
 	<li>8.1.4の処理の流れが正しく動いているかどうか、ブラウザで確認してみてください。特に、flashがうまく機能しているかどうか、フラッシュメッセージの表示後に違うページに移動することを忘れないでください。</li>
</ol>
</blockquote>
動作確認のみにて省略
<h3>まとめとか感想</h3>
ActiveRecordについて調べたりform_forとform_tagの違いについて調べたりした
8.1は結構スムーズに進んじゃったので逆に不安！
でも引っ掛かりのあったことはこのページにメモったから！大丈夫と思いたい！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

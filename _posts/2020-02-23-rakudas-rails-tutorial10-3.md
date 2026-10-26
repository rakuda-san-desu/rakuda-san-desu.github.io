---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜10.3"
date: "2020-02-23 17:40:44"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: []
published: true
wordpress_id: 1289
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial10-3/"
---
<h2>10.3 すべてのユーザーを表示する</h2>
indexアクションを追加してユーザー一覧を表示させる
→データベースにサンプルデータを追加する方法
→ページネーション（pagination=ページ分割）の方法
見本はこちら　→　<a href="https://railstutorial.jp/chapters/updating_and_deleting_users?version=5.1#fig-user_index_mockup" rel="noopener noreferrer" target="_blank">RoRT本文</a>
<h3>10.3.1 ユーザーの一覧ページ</h3>
ユーザーindexページはログインユーザーしか表示できないようにする
<h4>indexアクションのリダイレクト</h4>
UsersControllerTestにindexアクションが正しくリダイレクトするかを検証するテストを定義していく
<pre class="lang:ruby mark:(11-16) decode:true " title="/sample_app/test/controllers/users_controller_test.rb" >require 'test_helper'

class UsersControllerTest < ActionDispatch::IntegrationTest
  
  # テストユーザーを設定
  def setup
    @user = users(:michael)
    @other_user = users(:archer)
  end
  
  test "should redirect index when not logged in" do
    # users_pathにgetのリクエスト
    get users_path
    # login_urlにリダイレクトされる
    assert_redirected_to login_url
  end
・
・
・</pre> 
indexアクションを定義
UsersControllerのbeforeフィルターのlogged_in_userにindexアクションを追加して保護
<pre class="lang:ruby mark:(3-3,7-10) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  # 直前にlogged_in_userメソッドを実行　index,edit,updateアクションにのみ適用
  before_action :logged_in_user, only: [:index, :edit, :update]
  # 直前にcorrect_userメソッドを実行　edit,updateアクションにのみ適用
  before_action :correct_user,   only: [:edit, :update]
  
  def index
    #インスタンス変数@usersにすべてのuserを代入（後で修正あり）
    @users = User.all
  end
  
  def show
    @user = User.find(params[:id])
  end
・
・
・</pre> 
<h4>indexページを作成</h4>
index.html.erbを作る
<pre class="lang:ruby decode:true " title="/sample_app/app/views/users/index.html.erb" ><% provide(:title, t('.all_users')) %>
<h1><%= t('.all_users') %></h1>

<ul class="users">
  <!--eachメソッドでユーザー情報を1個ずつ取り出す-->
  <% @users.each do |user| %>
    <li>
      <!--gravatar_forヘルパーメソッド　ユーザーのgravatar登録画像を表示　サイズ指定-->
      <%= gravatar_for user, size: 50 %>
      <!--link_toメソッド　ユーザー名を表示　ユーザー詳細ページにリンク-->
      <%= link_to user.name, user %>
    </li>
  <% end %>
</ul></pre> 
スタイルを整える
<pre class="lang:sass decode:true " title="/sample_app/app/assets/stylesheets/custom.scss" >・
・
・
/* Users index */

.users {
  list-style: none;
  margin: 0;
  li {
    overflow: auto;
    padding: 10px 0;
    border-bottom: 1px solid $gray-lighter;
  }
}</pre> 
ヘッダーにユーザー一覧表示用のリンクを追加
<pre class="lang:ruby mark:(10-11) decode:true " title="/sample_app/app/views/layouts/_header.html.erb" ><header class="navbar navbar-fixed-top navbar-inverse">
  <div class="container">
    <%= link_to "sample app", root_path, id: "logo" %>
    <nav>
      <ul class="nav navbar-nav navbar-right">
        <li><%= link_to "Home", root_path %></li>
        <li><%= link_to "Help", help_path %></li>
        <!--ログインしていれば（current_userがnilでなければ）-->
        <% if logged_in? %>
          <!--ユーザー一覧ページへのリンク-->
          <li><%= link_to t('.Users'), users_path %></li>
          <!--ドロップダウンメニュー--></pre> 
<h4>演習</h4>


<blockquote class="quote-rort">
<ol>
	<li>レイアウトにあるすべてのリンクに対して統合テストを書いてみましょう。ログイン済みユーザーとそうでないユーザーのそれぞれに対して、正しい振る舞いを考えてください。ヒント: log_in_asヘルパーを使ってリスト 5.32にテストを追加してみましょう。</li>
</ol>
</blockquote>
1.
ログインしていない場合としている場合で
それぞれに表示されるリンクに合わせたテストを書く
<pre class="lang:default decode:true " title="/sample_app/test/integration/site_layout_test.rb" >require 'test_helper'

class SiteLayoutTest < ActionDispatch::IntegrationTest
  #ログインしていない場合のテスト
  test "layout links when not logged in" do
    # root_pathへgetのリクエスト
    get root_path
    # static_pages/homeが描画される
    assert_template 'static_pages/home'
    # 特定のHTMLタグが存在する　タグの種類(a href), リンク先のパス, タグの数
    assert_select "a[href=?]", root_path, count: 2
    assert_select "a[href=?]", help_path
    assert_select "a[href=?]", about_path
    assert_select "a[href=?]", contact_path
    assert_select "a[href=?]", signup_path
    assert_select "a[href=?]", login_path
    # contact_pathにgetのリクエスト
    get contact_path
    # 特定のHTMLタグが存在する　タグの種類（title）, タイトルの文字（full_titleヘルパーを呼び出し）
    assert_select "title", full_title("Contact")
    # signup_pathにgetのリクエスト
    get signup_path
    # 特定のHTMLタグが存在する　タグの種類（title）, タイトルの文字（full_titleヘルパーを呼び出し）
    assert_select "title", full_title("Sign up")
  end
  
  # 以下のテスト直前に@userにusers(:michael)を代入
  def setup
    @user = users(:michael)
  end
  
  #ログインしている場合のテスト
  test "layout links when logged in" do
    # ログインする
    log_in_as(@user)
    # root_pathへgetのリクエスト
    get root_path
    # static_pages/homeが描画される
    assert_template 'static_pages/home'
    # 特定のHTMLタグが存在する　タグの種類(a href), リンク先のパス, タグの数
    assert_select "a[href=?]", root_path, count: 2
    assert_select "a[href=?]", help_path
    assert_select "a[href=?]", users_path
    assert_select "a[href=?]", user_path(@user)
    assert_select "a[href=?]", edit_user_path(@user) 
    assert_select "a[href=?]", logout_path
    assert_select "a[href=?]", about_path
    assert_select "a[href=?]", contact_path
  end
end</pre> 
<h3>10.3.2 サンプルのユーザー</h3>
indexページの表示の確認のために複数のユーザーを登録したい！
→手作業で追加するのはつらい→Rubyを使って一気に作成！！
<h4>Faker gem</h4>
実際にいそうな名前のユーザー名を作成するgem
<blockquote class="quote-rort">faker gemは開発環境以外では普通使いませんが、今回は例外的に本番環境でも適用させる予定 (10.5) なので、次のようにすべての環境で使えるようにしています。</blockquote>
<pre class="lang:default mark:(5-5,13-13) decode:true " title="/sample_app/Gemfile" >source 'https://rubygems.org'

gem 'rails',        '5.1.6.2'
gem 'bcrypt',         '3.1.12'
gem 'faker',          '1.7.3'
・
・
・

group :development, :test do
  gem 'sqlite3', '1.3.13'
  gem 'byebug',  '9.0.6', platform: :mri
  #本来はここに入れるのが正しいかな？←あってた！
end</pre> 
それでbundle installするけど私の環境ではbundle update必要でした
次にこれ
<pre class="lang:ruby decode:true " title="/sample_app/db/seeds.rb" ># テーブル名.create!　カラムと持たせたい値
User.create!(name:  "Example User",
             email: "example@railstutorial.org",
             password:              "foobar",
             password_confirmation: "foobar")

# 99回繰り返す（timesメソッド）
99.times do |n|
  # nameに代入　Faker::Name.name
  name  = Faker::Name.name
  # emailに代入　example-#{n+1}@railstutorial.org　←それぞれのアドレスが変わるような指定
  email = "example-#{n+1}@railstutorial.org"
  password = "password"
  User.create!(name:  name,
               email: email,
               password:              password,
               password_confirmation: password)
end</pre> 
Example Userという名前とメールアドレスを持つ1人のユーザと、それらしい名前（Faker gemを使用）とメールアドレスを持つ99人のユーザーを作成する
なんで一人目は自分で指定してるの？→使いやすいからじゃん？
<blockquote class="quote-rort">create!は基本的にcreateメソッドと同じものですが、ユーザーが無効な場合にfalseを返すのではなく例外を発生させる (6.1.4) 点が異なります。こうしておくと見過ごしやすいエラーを回避できるので、デバッグが容易になります。</blockquote>
現状のデータベースをリセットしてサンプルデータを読み込ませる（Railsタスクを実行）
<pre class="lang:default decode:true " title="ターミナル" >$ rails db:migrate:reset
$ rails db:seed</pre> 
そこそこ待つ
あとうまくいかない場合はいったんRailssサーバーを止めると吉
→ユーザー一覧ページやコンソールでサンプルデータが100人分になっているのが確認できる
<h4>演習</h4>
動作確認のみにて省略
<h3>10.3.3 ページネーション</h3>
indexページにすべてのユーザーが表示されている
→もっとユーザーが増えた時を考えると1ページに全ユーザーが表示されるのでは大変！
<h4>ページネーションとは何ぞ</h4>
大雑把に言うとページを分割して表示する機能
今回の例で言えばユーザーを30件ずつ表示する。的な。
<blockquote class="quote-rort">Railsには豊富なページネーションメソッドがあります。今回はその中で最もシンプルかつ堅牢なwill_paginateメソッドを使ってみましょう。これを使うためには、Gemfileにwill_paginate gem とbootstrap-will_paginate gemを両方を含め、Bootstrapのページネーションスタイルを使ってwill_paginateを構成する必要があります。</blockquote>
<h4>gemの追加</h4>
<pre class="lang:default mark:(6-7) decode:true " title="/sample_app/Gemfile" >source 'https://rubygems.org'

gem 'rails',                   '5.1.6'
gem 'bcrypt',                  '3.1.12'
gem 'faker',                   '1.7.3'
gem 'will_paginate',           '3.1.6'
gem 'bootstrap-will_paginate', '1.0.0'
.
.
.</pre> 
bundle installしてサーバーを再起動
<h4>ビューの編集</h4>
<pre class="lang:ruby mark:(4-4,18-18) decode:true " title="/sample_app/app/views/users/index.html.erb" ><% provide(:title, t('.all_users')) %>
<h1><%= t('.all_users') %></h1>

<%= will_paginate %>

<ul class="users">
  <!--eachメソッドでユーザー情報を1個ずつ取り出す-->
  <% @users.each do |user| %>
    <li>
      <!--gravatar_forヘルパーメソッド　ユーザーのgravatar登録画像を表示　サイズ指定-->
      <%= gravatar_for user, size: 50 %>
      <!--link_toメソッド　ユーザー名を表示　ユーザー詳細ページにリンク-->
      <%= link_to user.name, user %>
    </li>
  <% end %>
</ul>

<%= will_paginate %></pre> 
<h4>アクションの編集</h4>
will_paginateメソッドはusersビューのコードの中から@usersオブジェクトを自動的に見つけ出してページネーションリンクを作成してくれる。すごい！
ただし、will_paginateではpaginateメソッドを使った結果が必要な為@usersに代入してあげる必要がある
<pre class="lang:ruby mark:(7-11) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  # 直前にlogged_in_userメソッドを実行　index,edit,updateアクションにのみ適用
  before_action :logged_in_user, only: [:index, :edit, :update]
  # 直前にcorrect_userメソッドを実行　edit,updateアクションにのみ適用
  before_action :correct_user,   only: [:edit, :update]
  
  def index
    # インスタンス変数@usersにUser.paginate(page: params[:page])を代入
    #params[:page])はwill_paginateによって自動的に生成されている
    @users = User.paginate(page: params[:page])
  end</pre> 
<blockquote class="quote-rort">paginateでは、キーが:pageで値がページ番号のハッシュを引数に取ります。User.paginateは、:pageパラメーターに基いて、データベースからひとかたまりのデータ (デフォルトでは30) を取り出します。</blockquote>
これにより、params[:page])が1なら1~30のユーザー、params[:page])が2なら31~60のユーザーが取り出され@usersに代入されるようになる
ちなみにページがnilの場合paginateは単に最初のページを返す
<h4>I18n</h4>
I18nを導入してデフォルト言語をen以外にしているとエラーっぽい表示が出ちゃう
<img src="https://rakuda3desu.net/wp-content/uploads/2020/02/missing.png" alt="" width="501" height="238" class="alignnone size-full wp-image-1297" />
なのでja.ymlを追加する
場所は/sample_app/config/locales/が良いっぽい
<pre class="lang:default decode:true " title="/sample_app/config/locales/ja.yml" >ja:
  #gem名を指定
  will_paginate:
    #ラベル名で指定
    previous_label: ←前
    next_label: 次→</pre> 
<img src="https://rakuda3desu.net/wp-content/uploads/2020/02/ja.png" alt="" width="258" height="183" class="alignnone size-full wp-image-1298" />
こうなる
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>Railsコンソールを開き、pageオプションにnilをセットして実行すると、１ページ目のユーザーが取得できることを確認してみましょう。</li>
	<li>先ほどの演習課題で取得したpaginationオブジェクトは、何クラスでしょうか? また、User.allのクラスとどこが違うでしょうか? 比較してみてください。</li>
</ol>
</blockquote>
<ol>
	<li>動作確認のみにて省略</li>
	<li>どちらもUser::ActiveRecord_Relationクラスで同じ</li>
</ol>
<h3>10.3.4 ユーザー一覧のテスト</h3>
<ol>
	<li>ログイン</li>
	<li>indexページにアクセス</li>
	<li>最初のページにユーザーがいることを確認</li>
	<li>ページネーションのリンクがあることを確認</li>
</ol>
この手順でテストをしていく
<h4>テストの準備</h4>
手順3，4ではテスト用のデータベースに31人以上のユーザーがいる必要があるので
埋め込みRubyを利用してユーザーを追加する
<pre class="lang:default mark:(11-19,21-27) decode:true " title="/sample_app/test/fixtures/users.yml" >michael:
  name: Michael Example
  email: michael@example.com
  password_digest: <%= User.digest('password') %>

archer:
  name: Sterling Archer
  email: duchess@example.gov
  password_digest: <%= User.digest('password') %>
  
 lana:
  name: Lana Kane
  email: hands@example.gov
  password_digest: <%= User.digest('password') %>

malory:
  name: Malory Archer
  email: boss@example.gov
  password_digest: <%= User.digest('password') %>

#コードを埋め込んでtimesメソッドで30件分のユーザーデータを作成
<% 30.times do |n| %>
user_<%= n %>:
  name:  <%= "User #{n}" %>
  email: <%= "user-#{n}@example.com" %>
  password_digest: <%= User.digest('password') %>
<% end %></pre> 
名前付きユーザー2件分は後で必要になるからついでに追加してるんだって
<h4>indexページに対する統合テスト</h4>
統合テストを生成
<pre class="lang:default mark:(1-1) decode:true " title="ターミナル" > $ rails generate integration_test users_index
Running via Spring preloader in process 24811
      invoke  test_unit
      create    test/integration/users_index_test.rb</pre> 
テストを定義
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/users_index_test.rb" >require 'test_helper'

class UsersIndexTest < ActionDispatch::IntegrationTest

  def setup
    @user = users(:michael)
  end

  test "index including pagination" do
    # @userでログイン
    log_in_as(@user)
    # users_pathにgetのリクエスト
    get users_path
    # users/indexが描画される
    assert_template 'users/index'
    # 特定のHTMLタグが存在する　div class="pagination"
    assert_select 'div.pagination'
    # User.paginate(page: 1)からuserを1ずつ取り出す
    User.paginate(page: 1).each do |user|
      # 特定のHTMLタグが存在する a href パスはuser_path(user) 表示テキストはuser.name
      assert_select 'a[href=?]', user_path(user), text: user.name
    end
  end
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>試しにリスト 10.45にあるページネーションのリンク (will_paginateの部分) を２つともコメントアウトしてみて、リスト 10.48のテストが redに変わるかどうか確かめてみましょう。</li>
	<li>先ほどは２つともコメントアウトしましたが、１つだけコメントアウトした場合、テストが greenのままであることを確認してみましょう。will_paginateのリンクが２つとも存在していることをテストしたい場合は、どのようなテストを追加すれば良いでしょうか? ヒント: 表 5.2を参考にして、数をカウントするテストを追加してみましょう。</li>
</ol>
</blockquote>
1.
動作確認のみにて省略
2. 
<pre class="lang:default mark:(17-17) decode:true " title="/sample_app/test/integration/users_index_test.rb" >require 'test_helper'

class UsersIndexTest < ActionDispatch::IntegrationTest

  def setup
    @user = users(:michael)
  end

  test "index including pagination" do
    # @userでログイン
    log_in_as(@user)
    # users_pathにgetのリクエスト
    get users_path
    # users/indexが描画される
    assert_template 'users/index'
    # 特定のHTMLタグが存在する　div class="pagination" 2個
    assert_select 'div.pagination' , count: 2
    # User.paginate(page: 1)からuserを1ずつ取り出す
    User.paginate(page: 1).each do |user|
      # 特定のHTMLタグが存在する a href パスはuser_path(user) 表示テキストはuser.name
      assert_select 'a[href=?]', user_path(user), text: user.name
    end
  end
end</pre> 
<h3>10.3.5 パーシャルのリファクタリング</h3>
<blockquote class="quote-rort">Railsにはコンパクトなビューを作成するための素晴らしいツールがいくつもあります。この節ではそれらのツールを使って一覧ページのリファクタリング (動作を変えずにコードを整理すること) を行うことにします。</blockquote>
ユーザーリストをrender呼び出しに置き換える
<pre class="lang:ruby mark:(9-9) decode:true " title="/sample_app/app/views/users/index.html.erb" ><% provide(:title, t('.all_users')) %>
<h1><%= t('.all_users') %></h1>

<%= will_paginate %>

<ul class="users">
  <!--eachメソッドでユーザー情報を1個ずつ取り出す-->
  <% @users.each do |user| %>
    <%= render user %>
  <% end %>
</ul>

<%= will_paginate %></pre> 
ここではrenderをパーシャル (ファイル名の文字列) に対してではなく、Userクラスのuser変数に対して実行している！
Railsは自動的に_user.html.erbという名前のパーシャルを探しにいく　→　なのでそのパーシャルを作る
パーシャルのファイルを呼んでいるのではないので「'user'」ではなくて「user」だし
user変数に対して実行しているので、呼び出すパーシャルが_user.html.erb
<pre class="lang:ruby decode:true " title="/sample_app/app/views/users/_user.html.erb" ><li>
  <%= gravatar_for user, size: 50 %>
  <%= link_to user.name, user %>
</li></pre> 
<h4>さらに改良</h4>
renderを@users変数に対して直接実行する
<pre class="lang:ruby mark:(9-9) decode:true " title="/sample_app/app/views/users/index.html.erb" ><% provide(:title, t('.all_users')) %>
<h1><%= t('.all_users') %></h1>

<%= will_paginate %>

<ul class="users">
  <!--eachメソッドでユーザー情報を1個ずつ取り出す-->
  <% @users.each do |user| %>
    <%= render @users %>
  <% end %>
</ul>

<%= will_paginate %>
</pre> 
<blockquote class="quote-rort">Railsは@users をUserオブジェクトのリストであると推測します。さらに、ユーザーのコレクションを与えて呼び出すと、Railsは自動的にユーザーのコレクションを列挙し、それぞれのユーザーを_user.html.erbパーシャルで出力します。</blockquote>
<h4>リファクタリング時の注意</h4>
リファクタリングを行う前後に必ずテストを行って、結果がGREENになることを確認する！
→コードを実行した結果が変わっていないことを確認！！
<h4>演習</h4>
動作確認のみにて省略
<h3>まとめとか感想</h3>
ページネーションって当たり前に思ってたけど、こうやって作ってるのかーってなりました（小並感）
今回もたくさん添削してもらったし、自信が持てないところも「OKだよ」ってしてもらえると安心できてありがたいですのだ。

宣伝ですよ　→懐かしさあふれるにゅ～ぶる会のご案内ページは<a href="https://newburukai.github.io/" rel="noopener noreferrer" target="_blank">こちら</a>
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

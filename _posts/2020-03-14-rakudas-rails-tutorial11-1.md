---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜11.1"
date: "2020-03-14 17:58:04"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1325
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial11-1/"
---
11章でやること
アカウントを有効化するためのメール認証システムを作る！
<blockquote class="quote-rort">
<ol>
	<li>ユーザーの初期状態は「有効化されていない」(unactivated) にしておく。</li>
	<li>ユーザー登録が行われたときに、有効化トークンと、それに対応する有効化ダイジェストを生成する。</li>
	<li>有効化ダイジェストはデータベースに保存しておき、有効化トークンはメールアドレスと一緒に、ユーザーに送信する有効化用メールのリンクに仕込んでおく</li>
	<li>ユーザーがメールのリンクをクリックしたら、アプリケーションはメールアドレスをキーにしてユーザーを探し、データベース内に保存しておいた有効化ダイジェストと比較することでトークンを認証する。</li>
	<li>ユーザーを認証できたら、ユーザーのステータスを「有効化されていない」から「有効化済み」(activated) に変更する。</li>
</ol>
</blockquote>
うむわからん！

こうやって書くとよくわからないけど、
パスワードや記憶トークンの仕組みと似た点が多いとの事なので、記事を見直しつつゆっくり理解しながら進めていきましょうそうしましょう。
<h2>11.1 AccountActivationsリソース</h2>
<blockquote class="quote-rort">セッション機能 (8.1) を使って、アカウントの有効化という作業を「リソース」としてモデル化することにします。アカウントの有効化リソースはActive Recordのモデルとはこの際関係ないので、両者を関連付けることはしません。その代わりに、この作業に必要なデータ (有効化トークンや有効化ステータスなど) をUserモデルに追加することにします。</blockquote>
また、アカウント有効化もリソースの使い方に今までとは異なる点があるので注意！
<blockquote class="quote-rort">有効化用のリンクにアクセスして有効化のステータスを変更する部分では、RESTのルールに従うとPATCHリクエストとupdateアクションになるべきです (表 7.1)。しかし、有効化リンクはメールでユーザーに送られることを思い出してください。ユーザーがこのリンクをクリックすれば、それはブラウザで普通にクリックしたときと同じであり、その場合ブラウザから発行されるのは (updateアクションで使うPATCHリクエストではなく) GETリクエストになってしまいます。このため、ユーザーからのGETリクエストを受けるために、(本来であればupdateのところを) editアクションに変更して使っていきます。</blockquote>
やっぱり文章で説明されてもよくわからないので進めながら理解できるように頑張りたい（希望）
<pre class="toolbar:2 lang:default decode:true " >#branchを切って作業開始！
git checkout -b account-activation</pre> 
<h3>11.1.1 AccountActivationsコントローラ</h3>
<pre class="lang:default decode:true " title="ターミナル" ># AccountActivationsコントローラを生成
$ rails generate controller AccountActivations
Running via Spring preloader in process 5709
      create  app/controllers/account_activations_controller.rb
      invoke  erb
      create    app/views/account_activations
      invoke  test_unit
      create    test/controllers/account_activations_controller_test.rb
      invoke  helper
      create    app/helpers/account_activations_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/account_activations.coffee
      invoke    scss
      create      app/assets/stylesheets/account_activations.scss</pre> 
<h4>有効化のメールに必要な名前付きルート</h4>
詳しくは11.2.1との事だけど
有効化のメールに含まれるURLにeditアクションへの名前付きルートが必要になる
<pre class="toolbar:2 lang:default decode:true " >edit_account_activation_url(activation_token, ...)</pre> 

なので名前付きルートを使えるように設定する
<pre class="lang:ruby mark:(15-16) decode:true " title="/sample_app/config/routes.rb" >Rails.application.routes.draw do

  root 'static_pages#home'
  get  '/help',    to:'static_pages#help'
  get  '/about',   to:'static_pages#about'
  get  '/contact', to:'static_pages#contact'
  
  get  '/signup',  to:'users#new'
  post '/signup',  to: 'users#create'
  
  get    '/login',   to: 'sessions#new'
  post   '/login',   to: 'sessions#create'
  delete '/logout',  to: 'sessions#destroy'
  resources :users
  # account_activations resourceのeditへのルーティングのみを生成
  resources :account_activations, only: [:edit]
end
</pre> 
これにより
/account_activation/<token>/editにgetのリクエストが送られるとaccount_activationsコントローラーのeditアクションが実行されるようになる
名前付きルートのedit_account_activation_url(token)も使えるようになる
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>現時点でテストスイートを実行すると greenになることを確認してみましょう。</li>
	<li>表 11.2の名前付きルートでは、_pathではなく_urlを使うように記してあります。なぜでしょうか? 考えてみましょう。ヒント: 私達はこれからメールで名前付きルートを使います。</li>
</ol>
</blockquote>
<ol>
	<li>green</li>
	<li>メール本文のURLからアクセスするから
（因みに_pathは相対パスを返し_urlは絶対パスを返すヤツ）</li>
</ol>
<h3>11.1.2 AccountActivationのデータモデル</h3>
アカウント有効化メールには一意の有効化トークンが必要
<blockquote class="quote-rort">パスワードの実装 (第6章) や記憶トークンの実装 (第9章) と同じように仮想的な属性を使ってハッシュ化した文字列をデータベースに保存するようにします。</blockquote>
 具体的にはこんな感じになる
<pre class="lang:ruby decode:true " title="参考コード" >#仮想属性の有効化トークンにアクセスしたり
user.activation_token

#ユーザーを承認できるようにしたり
user.authenticated?(:activation, token)

#ユーザーが有効かどうかのテストをしたり
if user.activated? ...</pre> 
あと、本文では使わないけどユーザーを有効にしたときの日時も念のために記録できるようにしておく
<ul>
	<li>activation_digest:string</li>
	<li>activated:boolean</li>
	<li>activated_at:datetime</li>
</ul>
この3つの属性をUserモデルに追加する
<pre class="lang:default decode:true " title="ターミナル" >$ rails generate migration add_activation_to_users \
> activation_digest:string activated:boolean activated_at:datetime</pre> 
 
<pre class="lang:default mark:(4-5) decode:true " title="/sample_app/db/migrate/タイムスタンプ_add_activation_to_users.rb" >class AddActivationToUsers < ActiveRecord::Migration[5.1]
  def change
    add_column :users, :activation_digest, :string
    # activated属性のデフォルトの論理値をfalseに指定
    add_column :users, :activated, :boolean, default: false
    add_column :users, :activated_at, :datetime
  end
end
</pre> 
<pre class="lang:default decode:true " title="ターミナル" >#マイグレーションの実行
$ rails db:migrate</pre> 
<h4>Activationトークンのコールバック</h4>
<blockquote class="quote-rort">ユーザーが新しい登録を完了するためには必ずアカウントの有効化が必要になるのですから、有効化トークンや有効化ダイジェストはユーザーオブジェクトが作成される前に作成しておく必要があります。</blockquote>
メースアドレスを保存する前に全て小文字に変換するように指定した際は
before_saveコールバックにdowncaseメソッドを関連付けてオブジェクトの保存の直前に実行されるようにしたけど
今回は保存直前ではなくオブジェクトが作成された時にのみコールバックを呼び出したい！
よってこう↓
<pre class="toolbar:2 lang:default decode:true " >before_create :create_activation_digest</pre> 
createの直前にcreate_activation_digestメソッドを呼び出している
→create_activation_digestメソッドは外部から見えないようにprivateキーワードを指定して隠ぺい！
<h4>rememberメソッドとの違い</h4>
コールバックに関連付けるメソッドは記憶トークンや記憶ダイジェストのために作ったメソッドを使いまわしてるけどもちろん違いもある！
<pre class="lang:ruby decode:true " title="参考コード" ># 永続セッションのためにユーザーをデータベースに記憶する
def remember
  self.remember_token = User.new_token
  update_attribute(:remember_digest, User.digest(remember_token))
end

# 有効化トークンとダイジェストを作成および代入する
def create_activation_digest
  self.activation_token  = User.new_token
  self.activation_digest = User.digest(activation_token)
end</pre> 
記憶トークンやダイジェストは既にデータベースにいるユーザーのために作成されるのでupdate_attributeを使って情報を更新・保存しているが
before_createコールバックの方はユーザーが作成される前に呼び出されることなので更新される属性がない→新しく取得
以上をもとにUserモデルにアカウント有効化のコードを追加
また以下も併せて
<ul>
	<li>せっかくなので（？）メールアドレスを小文字にするメソッドもメソッド参照に切り替える</li>
	<li>有効化トークンは本質的に仮のものでなければならないのでactivation_token属性をattr_accessorに追加</li>
</ul>
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  #仮想の属性:remember_token、activation_tokenをUserクラスに定義
  attr_accessor :remember_token, :activation_token
  #保存の直前に参照するメソッド
  before_save   :downcase_email
  # データ作成の直前に参照するメソッド
  before_create :create_activation_digest
・
・
・
    private

    # メールアドレスをすべて小文字にする
    def downcase_email
      self.email = email.downcase
    end

    # 有効化トークンとダイジェストを作成および代入する
    def create_activation_digest
      self.activation_token  = User.new_token
      self.activation_digest = User.digest(activation_token)
    end
end</pre> 
<h4>サンプルユーザーの生成とテスト</h4>
先に進む前に、サンプルデータとfixtureも更新してスムーズにテストできるように準備をしておく！
<blockquote class="quote-rort">なお、Time.zone.nowはRailsの組み込みヘルパーであり、サーバーのタイムゾーンに応じたタイムスタンプを返します。</blockquote> 
<pre class="lang:default mark:(8-10,23-25) decode:true " title="/sample_app/db/seeds.rb" ># テーブル名.create!　作るデータ→対応するカラムと値
User.create!(name:  "Example User",
             email: "example@railstutorial.org",
             password:              "foobar",
             password_confirmation: "foobar",
            # 管理者
             admin: true,
             #ユーザーが有効化されている
             activated: true,
             activated_at: Time.zone.now)

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
               password_confirmation: password,
               #ユーザーが有効化されている
              activated: true,
              activated_at: Time.zone.now)
end</pre> 
<pre class="lang:default mark:(6-7,13-14,20-21,27-28,36-37) decode:true " title="/sample_app/test/fixtures/users.yml" >michael:
  name: Michael Example
  email: michael@example.com
  password_digest: <%= User.digest('password') %>
  admin: true
  activated: true
  activated_at: <%= Time.zone.now %>

archer:
  name: Sterling Archer
  email: duchess@example.gov
  password_digest: <%= User.digest('password') %>
  activated: true
  activated_at: <%= Time.zone.now %>

lana:
  name: Lana Kane
  email: hands@example.gov
  password_digest: <%= User.digest('password') %>
  activated: true
  activated_at: <%= Time.zone.now %>

malory:
  name: Malory Archer
  email: boss@example.gov
  password_digest: <%= User.digest('password') %>
  activated: true
  activated_at: <%= Time.zone.now %>

#コードを埋め込んでtimesメソッドで30件分のユーザーデータを作成
<% 30.times do |n| %>
user_<%= n %>:
  name:  <%= "User #{n}" %>
  email: <%= "user-#{n}@example.com" %>
  password_digest: <%= User.digest('password') %>
  activated: true
  activated_at: <%= Time.zone.now %>
<% end %></pre> 
データベースのリセットとサンプルデータを再度生成
<pre class="lang:default decode:true " title="ターミナル" >$ rails db:migrate:reset
$ rails db:seed</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>本項での変更を加えた後、テストスイートが green のままになっていることを確認してみましょう。</li>
	<li>コンソールからUserクラスのインスタンスを生成し、そのオブジェクトからcreate_activation_digestメソッドを呼び出そうとすると (Privateメソッドなので) NoMethodErrorが発生することを確認してみましょう。また、そのUserオブジェクトからダイジェストの値も確認してみましょう。</li>
	<li>リスト 6.34で、メールアドレスの小文字化にはemail.downcase!という (代入せずに済む) メソッドがあることを知りました。このメソッドを使って、リスト 11.3のdowncase_emailメソッドを改良してみてください。また、うまく変更できれば、テストスイートは成功したままになっていることも確認してみてください。</li>
</ol>
</blockquote>
1.
green

2.
<pre class="lang:default decode:true " title="コンソール" >>> u = User.new
=> #<User id: nil, name: nil, email: nil, created_at: nil, updated_at: nil, password_digest: nil, remember_digest: nil, admin: false, activation_digest: nil, activated: false, activated_at: nil>
>> u.create_activation_digest
Traceback (most recent call last):
        1: from (irb):2
#private methodだから呼べないよってなってる
NoMethodError (private method `create_activation_digest' called for #<User:0x0000000002f9e730>)
Did you mean?  restore_activation_digest!
>> u.activation_digest
=> nil</pre> 
3.
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/app/models/user.rb" >・
・
・
# メールアドレスをすべて小文字にする
def downcase_email
  self.email.downcase!
end
・
・
・</pre> 
テストはgreen
<h3>まとめとか感想</h3>
アカウント有効化メール実装の準備
次からが本番ですね！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

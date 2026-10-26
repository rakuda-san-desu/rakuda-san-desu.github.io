---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜7.1"
date: "2019-09-03 20:13:24"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 912
wordpress_status: "publish"
permalink: "/rakudas-rails-tutoria7-1/"
---
7章でやる事
ユーザー登録機能を追加する
→HTML フォームを使ってWebアプリケーションに登録情報を送信
→ユーザーを新規作成して情報をデータベースに保存
→作成されたユーザーの新しいプロフィールを表示できるようにする準備
<h2>7.1 ユーザーを表示する</h2>
ユーザーの名前とプロフィール写真を表示する為のページを作る。
<a href="https://railstutorial.jp/chapters/sign_up?version=5.1#sec-showing_users">Railsチュートリアル本文に見本があります</a>
ブランチはこちら
→　<strong>$ git checkout -b sign-up</strong>
<h3>7.1.1 デバッグとRails環境</h3>
Webサイトのレイアウトにデバッグ情報を追加する
<pre class="lang:ruby mark:(11-11) decode:true " title="/sample_app/app/views/layouts/application.html.erb" ><!DOCTYPE html>
<html>
  ・
  ・
  ・
  <body>
    <%= render 'layouts/header' %>
    <div class="container">
      <%= yield %>
      <%= render 'layouts/footer' %>
      <%= debug(params) if Rails.env.development? %>
    </div>
  </body>
</html></pre> 
上記追加コード <strong>if Rails.env.development?</strong>　があることで
開発環境(development)のみで表示される
→テストや本番環境では挿入されない
→<strong>デバッグ情報は開発環境以外では表示させないべき！</strong>
<blockquote class="quote-rort">
Railsにはテスト環境 (test)、開発環境 (development)、そして本番環境 (production) の3つの環境がデフォルトで装備されています。Rails consoleのデフォルトの環境はdevelopmentです。
<a href="https://railstutorial.jp/chapters/sign_up?version=5.1#aside-rails_environments">コラム 7.1. Railsの3つの環境</a>
</blockquote>
<h4>デバッグ出力を整形する</h4>
custom.scssにスタイルを追加する
<pre class="lang:sass mark:(8-8,23-23) decode:true " title="/sample_app/app/assets/stylesheets/custom.scss" >@import "bootstrap-sprockets";
@import "bootstrap";

/* mixins, variables, etc. */

$gray-medium-light: #eaeaea;

@mixin box_sizing {
  -moz-box-sizing:    border-box;
  -webkit-box-sizing: border-box;
  box-sizing:         border-box;
}
.
.
.
/* miscellaneous */

.debug_dump {
  clear: both;
  float: left;
  width: 100%;
  margin-top: 45px;
  @include box_sizing;
}</pre> 
<h4>@mixinとは</h4>
まとめておいたコードを複数個所で簡単に呼び出すことでが出来る便利な機能
<strong>@mixin mixin名</strong>で定義して<strong>@include mixin名</strong>で呼び出す
詳しくはSassを勉強しようね🐫！
<h4>デバッグ出力</h4>
本文と違うね🤔
<a href="https://rakuda3desu.net/wp-content/uploads/2019/08/7.1.1-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/08/7.1.1-1.png" alt="" width="575" height="387" class="alignnone size-full wp-image-916" /></a>
<strong>controller: static_pages
action: home</strong>
ハッシュ形式で取り出されたページの状態
controller→static_pages_controller.rb
action→home
<h4>演習</h4>
<blockquote class="quote-rort">
<ul>
<li>
ブラウザから /about にアクセスし、デバッグ情報が表示されていることを確認してください。このページを表示するとき、どのコントローラとアクションが使われていたでしょうか? paramsの内容から確認してみましょう。</li>
<li>
Railsコンソールを開き、データベースから最初のユーザー情報を取得し、変数userに格納してください。その後、puts user.attributes.to_yamlを実行すると何が表示されますか? ここで表示された結果と、yメソッドを使ったy user.attributesの実行結果を比較してみましょう。
</li>
</ul>
</blockquote>
<pre class="lang:default mark:(6-7) decode:true " title="ブラウザ" >#1.該当部分
--- !ruby/object:ActionController::Parameters
#paramsの親クラスは↑だよ
parameters: !ruby/hash:ActiveSupport::HashWithIndifferentAccess
#parametersはハッシュで出来てるよ！中身はこれ↓だよ
  controller: static_pages
  action: about
permitted: false
#ハッシュの中身がストロングパラメーターで許可されてないよ！</pre>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#2.
>> user = User.first
=> #<User id: 1, name: "rakuda", email: "rakuda@mail.com", created_at: "2018-07-12 07:27:24", updated_at: "2019-07-12 07:27:24", password_digest: nil>
>> puts user.attributes.to_yaml
#出力　userに代入されている情報の属性をYAML形式で
---
id: 1
name: rakuda
email: rakuda@mail.com
created_at: !ruby/object:ActiveSupport::TimeWithZone
  utc: &1 2018-07-12 07:27:24.647259000 Z
  zone: &2 !ruby/object:ActiveSupport::TimeZone
    name: Etc/UTC
  time: *1
updated_at: !ruby/object:ActiveSupport::TimeWithZone
  utc: &3 2019-07-12 07:27:24.647955000 Z
  zone: *2
  time: *3
password_digest: 
=> nil
>> y user.attributes
#YAML形式で出力　userに代入されている情報の属性
---
id: 1
name: rakuda
email: rakuda@mail.com
created_at: !ruby/object:ActiveSupport::TimeWithZone
  utc: &1 2018-07-12 07:27:24.647259000 Z
  zone: &2 !ruby/object:ActiveSupport::TimeZone
    name: Etc/UTC
  time: *1
updated_at: !ruby/object:ActiveSupport::TimeWithZone
  utc: &3 2019-07-12 07:27:24.647955000 Z
  zone: *2
  time: *3
password_digest: 
=> nil
#結果は同じ</pre>
<h3>7.1.2 Usersリソース</h3>
ユーザー情報をwebapplication上に表示できるようにしていく
→id=1のユーザーにアクセスするためのページのURIは<strong>/users/1</strong>
現時点ではroutesが設定されていないため、このURLにアクセスしてもエラーになる
<pre class="lang:ruby mark:(9-9) decode:true " title="/sample_app/config/routes.rb" >Rails.application.routes.draw do

  root 'static_pages#home'
  get  '/help',    to:'static_pages#help'
  get  '/about',   to:'static_pages#about'
  get  '/contact', to:'static_pages#contact'
  
  get  '/signup',  to:'users#new'
  resources :users
  #Usersリソースを追加
end</pre> 
<blockquote class="quote-rort">
サンプルアプリケーションにこの１行を追加すると、ユーザーのURLを生成するための多数の名前付きルート (5.3.3) と共に、RESTfulなUsersリソースで必要となるすべてのアクションが利用できるようになるのです</blockquote>
<h4>viewを作る</h4>
ルーティングは有効になったけどviewが無いので表示されません
なのでviewを作ります
<pre class="lang:ruby decode:true " title="/sample_app/app/views/users/show.html.erb←ファイルを作る
"><%= @user.name %>, <%= @user.email %>
#最低限のビューを設定
#対応するアクションで@user変数を定義されている必要がある</pre>
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  
  def show
    @user = User.find(params[:id])
    #params変数→URLから値を取得
    #この場合　@userにUser.find(URLから取得したid）の値を代入
  end
  
  def new
  end
end</pre> 
<blockquote class="quote-rort">
ユーザーのid読み出しにはparamsを使いました。Usersコントローラにリクエストが正常に送信されると、params[:id]の部分はユーザーidの1に置き換わります。つまり、この箇所は6.1.4で学んだfindメソッドの User.find(1)と同じになります。(技術的な補足: params[:id]は文字列型の "1" ですが、findメソッドでは自動的に整数型に変換されます)。
</blockquote>
<h4>演習</h4>
<ul>
<li>埋め込みRubyを使って、マジックカラム (created_atとupdated_at) の値をshowページに表示してみましょう (リスト 7.4)。</li>
<li>埋め込みRubyを使って、Time.nowの結果をshowページに表示してみましょう。ページを更新すると、その結果はどう変わっていますか? 確認してみてください。</li>
</ul>
</blockquote>
<pre class="lang:ruby mark:(3-3,5-5) decode:true " title="/sample_app/app/views/users/show.html.erb" >#1.
<p><%= @user.name %>, <%= @user.email %><br>
<%= @user.created_at %>, <%= @user.updated_at %></p>
#2.ページを更新すると現在時刻が表示される
<p><%= Time.now %></p></pre> 
<h3>7.1.3 debuggerメソッド</h3>
byebug gemによるdebuggerメソッド
<pre class="lang:ruby mark:(5-5) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  
  def show
    @user = User.find(params[:id])
    debugger
  end
  
  def new
  end
end</pre> 
<pre class="lang:default mark:(14-14,16-16,18-18) decode:true " title="ターミナル（puma）" >[1, 10] in /home/ec2-user/environment/sample_app/app/controllers/users_controller.rb
    1: class UsersController < ApplicationController
    2:   
    3:   def show
    4:     @user = User.find(params[:id])
    5:     debugger
#この瞬間の状態を確認できる
=>  6:   end
    7:   
    8:   def new
    9:   end
   10: end
#(byebug)の後にコマンドを入力できる
(byebug) @user.name
"rakuda"
(byebug) @user.email
"rakuda@mail.com"
(byebug) params[:id]
"1"
(byebug)#Ctrl dで終了</pre> 
デバッグが終わったら<strong>debugger部分を削除しておく</strong>
<blockquote class="quote-rort">
今後Railsアプリケーションの中でよく分からない挙動があったら、上のようにdebuggerを差し込んで調べてみましょう。トラブルが起こっていそうなコードの近くに差し込むのがコツです。byebug gemを使ってシステムの状態を調査することは、アプリケーション内のエラーを追跡したりデバッグするときに非常に強力なツールになります。
</blockquote>
エラーログでいい気がするけどそういうものでもないのかな🤔
→<strong>エラーを調べる以外にも色々出来るので、何が出来るか学んでゆきましょう🐫</strong>
<h4>演習</h4>
<blockquote class="quote-rort">
<ul>
<li>
showアクションの中にdebuggerを差し込み (リスト 7.6)、ブラウザから /users/1 にアクセスしてみましょう。その後コンソールに移り、putsメソッドを使ってparamsハッシュの中身をYAML形式で表示してみましょう。ヒント: 7.1.1.1の演習を参考にしてください。その演習ではdebugメソッドで表示したデバッグ情報を、どのようにしてYAML形式で表示していたでしょうか?
</li>
<li>
newアクションの中にdebuggerを差し込み、/users/new にアクセスしてみましょう。@userの内容はどのようになっているでしょうか? 確認してみてください
</li>
</ul>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル(puma)" >#1.
(byebug) p = params
<ActionController::Parameters {"controller"=>"users", "action"=>"show", "id"=>"1"} permitted: false>
(byebug) puts p.to_yaml
--- !ruby/object:ActionController::Parameters
parameters: !ruby/hash:ActiveSupport::HashWithIndifferentAccess
  controller: users
  action: show
  id: '1'
permitted: false
nil
(byebug) 
#Ctrl dで終了させておく
#showアクションからdebuggerを削除しておく

#2.
#newアクションにdebuggerを追加してブラウザで/users/new にアクセスしてくる
[1, 10] in /home/ec2-user/environment/sample_app/app/controllers/users_controller.rb
    1: class UsersController < ApplicationController
    2:   
    3:   def show
    4:     @user = User.find(params[:id])
    5:   end
    6:   
    7:   def new
    8:     debugger
=>  9:   end
   10: end
(byebug) puts @user.attributes.to_yaml
*** NoMethodError Exception: undefined method `attributes' for nil:NilClass

nil
#newアクションには@user変数がないので中身がないのでattributesメソッド例外エラー
#確認後Ctrl dで終了させておく＆newアクションからdebuggerを削除しておく</pre>
<h3>7.1.4 Gravatar画像とサイドバー</h3>
無料の画像サービスGravatarを使ってユーザーページにプロフィール画像を表示させる
<blockquote class="quote-rort">
Gravatarは無料のサービスで、プロフィール写真をアップロードして、指定したメールアドレスと関連付けることができます。その結果、 Gravatarはプロフィール写真をアップロードするときの面倒な作業や写真が欠けるトラブル、また、画像の置き場所の悩みを解決します。というのも、ユーザーのメールアドレスを組み込んだGravatar専用の画像パスを構成するだけで、対応するGravatarの画像が自動的に表示されるからです
</blockquote>
<pre class="lang:ruby decode:true " title="/sample_app/app/views/users/show.html.erb
">#ユーザー表示ビューに名前とGravatarを表示する
<% provide(:title, @user.name) %>
<h1>
  <%= gravatar_for @user %>
  <%= @user.name %>
</h1></pre>
<pre class="lang:ruby mark:(5-5) decode:true " title="/sample_app/app/helpers/users_helper.rb" >#gravatar_forヘルパーメソッドを定義する
module UsersHelper
  # 引数で与えられたユーザーのGravatar画像を返す
  def gravatar_for(user)
    gravatar_id = Digest::MD5::hexdigest(user.email.downcase)
    gravatar_url = "https://secure.gravatar.com/avatar/#{gravatar_id}"
    image_tag(gravatar_url, alt: user.name, class: "gravatar")
  end
end</pre> 
（ヘルパーに定義してからviewに組み込む方が自然な流れじゃなかろうか🤔とか思いつつ）
<blockquote class="quote-rort">
GravatarのURLはユーザーのメールアドレスをMD5という仕組みでハッシュ化しています。Rubyでは、Digestライブラリのhexdigestメソッドを使うと、MD5のハッシュ化が実現できます。
（中略）
メールアドレスは大文字と小文字を区別しませんが (6.2.4)、MD5ハッシュでは大文字と小文字が区別されるので、Rubyのdowncaseメソッドを使ってhexdigestの引数を小文字に変換しています。(本チュートリアルでは、リスト 6.32のコールバック処理で小文字変換されたメールアドレスを利用しているため、ここで小文字変換を入れなくても結果は同じです。ただし、将来gravatar_forメソッドが別の場所から呼びだされる可能性を考えると、ここで小文字変換を入れることには意義があります。) 
</blockquote>
現状DBのデータは架空のデータなので画像表示はされない
本文に則ってサンプルデータを入力すると表示されるようになる。
<h4>サイドバーの最初のバージョン</h4>
本文に従ってshow.html.erbとcssを編集する
classのrowとcol-md-4はBootstrapのクラス名
<pre class="lang:ruby decode:true " title="/sample_app/app/views/users/show.html.erb
"><% provide(:title, @user.name) %>
<div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <h1>
        <%= gravatar_for @user %>
        <%= @user.name %>
      </h1>
    </section>
  </aside>
</div>
#ごっそり差し替え</pre>
<pre class="lang:sass decode:true " title="/sample_app/app/assets/stylesheets/custom.scss" >.
.
.
/* sidebar */

aside {
  section.user_info {
    margin-top: 20px;
  }
  section {
    padding: 10px 0;
    margin-top: 20px;
    &:first-child {
      border: 0;
      padding-top: 0;
    }
    span {
      display: block;
      margin-bottom: 3px;
      line-height: 1;
    }
    h1 {
      font-size: 1.4em;
      text-align: left;
      letter-spacing: -1px;
      margin-bottom: 3px;
      margin-top: 0px;
    }
  }
}

.gravatar {
  float: left;
  margin-right: 10px;
}

.gravatar_edit {
  margin-top: 15px;
}</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ul>
<li>
(任意) Gravatar上にアカウントを作成し、あなたのメールアドレスと適当な画像を紐付けてみてください。メールアドレスをMD5ハッシュ化して、紐付けた画像がちゃんと表示されるかどうか試してみましょう。
</li>
<li>
7.1.4で定義したgravatar_forヘルパーをリスト 7.12のように変更して、sizeをオプション引数として受け取れるようにしてみましょう。うまく変更できると、gravatar_for user, size: 50といった呼び出し方ができるようになります。重要: この改善したヘルパーは10.3.1で実際に使います。忘れずに実装しておきましょう。
</li>
<li>
オプション引数は今でもRubyコミュニティで一般的に使われていますが、Ruby 2.0から導入された新機能「キーワード引数 (Keyword Arguments)」でも実現することができます。先ほど変更したリスト 7.12を、リスト 7.13のように置き換えてもうまく動くことを確認してみましょう。この２つの実装方法はどういった違いがあるのでしょうか? 考えてみてください。
</li>
</ul>
</blockquote>
１．登録済みだったし表示された！
<pre class="lang:ruby mark:(5-5,8-8,10-10,20-20) decode:true " title="/sample_app/app/helpers/users_helper.rb" >#2.こうして
module UsersHelper

  # 引数で与えられたユーザーのGravatar画像を返す
  def gravatar_for(user, options = { size: 80 })
  #引数optionsにハッシュ size: 80 を代入
    gravatar_id = Digest::MD5::hexdigest(user.email.downcase)
    size = options[:size]
    #変数sizeに　optionsに代入されたハッシュのキー :size で呼び出された値 80 を代入
    gravatar_url = "https://secure.gravatar.com/avatar/#{gravatar_id}?s=#{size}"
    #呼び出した画像に変数 size を展開してサイズ指定するオプションが付いている
    image_tag(gravatar_url, alt: user.name, class: "gravatar")
  end
end

#3.こうじゃ
module UsersHelper

  # 引数で与えられたユーザーのGravatar画像を返す
  def gravatar_for(user, size: 80)
　#キーワード引数 size: 80 は　ハッシュ
    gravatar_id = Digest::MD5::hexdigest(user.email.downcase)
    gravatar_url = "https://secure.gravatar.com/avatar/#{gravatar_id}?s=#{size}"
    #ハッシュなのでキーワードで値を呼び出して展開できる
    image_tag(gravatar_url, alt: user.name, class: "gravatar")
  end
end</pre> 
キーワード引数についてこちらなどを参考にしました↓
[blogcard url="https://magazine.rubyist.net/articles/0041/0041-200Special-kwarg.html"]
オプション引数よりキーワード引数の方がコードがすっきりする！
<h3>まとめとか感想</h3>
デバッグの方法を学ぶなど
viewのデバッグで表示されている内容がﾜｹﾜｶﾗﾝ!!だったのだけど
頑張って調べたり<a href="https://newburu.github.io/" rel="noopener noreferrer" target="_blank">コミュ</a>の仲間にヒントをもらったり解説してもらったりしました。
ありがたや。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

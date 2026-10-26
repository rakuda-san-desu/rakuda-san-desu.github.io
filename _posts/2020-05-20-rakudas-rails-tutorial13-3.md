---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜13.3"
date: "2020-05-20 17:12:11"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1493
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial13-3/"
---
※毎回書いたほうがいい気がするので書いておきます※
平文の部分をちょこちょこI18nで日本語化しているのですが
その部分に関しては特に記述していなくてもたまにコードが違ったりしています。
t('.hogehoge')って部分です
どうぞお気になさらず。
<h2>13.3 マイクロポストを操作する</h2>
モデリングとテンプレートが完成したのでweb経由でマイクロポストを操作できるようにしていく
<h4>従来のRailsの開発の慣習と異なる部分</h4>
<blockquote class="quote-rort">Micropostsリソースへのインターフェイスは、主にプロフィールページとHomeページのコントローラを経由して実行されるので、Micropostsコントローラにはnewやeditのようなアクションは不要ということになります。つまり、createとdestroyがあれば十分です。</blockquote>
よってMicropostのリソースは下記↓のように
<pre class="lang:ruby mark:(9-10) decode:true " title="/sample_app/config/routes.rb" >・
・
・
  resources :users
  # account_activationsresourceのeditへのルーティングのみを生成
  resources :account_activations, only: [:edit]
  # password_resetsのnew、create、edit、updateのルーティングを生成 
  resources :password_resets,     only: [:new, :create, :edit, :update]
  # micropostsのcreateとdestroyのルーティングを生成
  resources :microposts,          only: [:create, :destroy]

end</pre> 
提供されるRESTfulなルート　→　<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#table-RESTful_microposts" rel="noopener noreferrer" target="_blank">RoRT本文</a>参照
<h3>13.3.1 マイクロポストのアクセス制御</h3>
Micropostsコントローラ内のアクセス制御から始める
→Micropostsは関連付けられたユーザーを通してアクセスされるので
createアクションやdestroyアクションを利用するユーザーはログインしている必要がある
<blockquote class="quote-rort">ログイン済みかどうかを確かめるテストでは、Usersコントローラ用のテストがそのまま役に立ちます</blockquote>
ログイン済でない場合の動作↓
<ol>
	<li>正しいリクエストを各アクションへ発行</li>
	<li>マイクロポストの数が変化していないか</li>
	<li>正しくリダイレクトされているか</li>
</ol> 
<pre class="lang:ruby decode:true " title="/sample_app/test/controllers/microposts_controller_test.rb" >require 'test_helper'

class MicropostsControllerTest < ActionDispatch::IntegrationTest

  def setup
    @micropost = microposts(:orange)
  end

  test "should redirect create when not logged in" do
    # ブロックで渡されたものを呼び出す前後でMicropost.countに違いがない
    assert_no_difference 'Micropost.count' do
      # microposts_pathに　paramsハッシュのデータを持たせてpostのリクエスト
      post microposts_path, params: { micropost: { content: "Lorem ipsum" } }
    end
    # login_urlにリダイレクト
    assert_redirected_to login_url
  end

  test "should redirect destroy when not logged in" do
    # ブロックで渡されたものを呼び出す前後でMicropost.countに違いがない
    assert_no_difference 'Micropost.count' do
      # micropost_path(@micropost)にdeleteのリクエスト
      delete micropost_path(@micropost)
    end
    # login_urlにリダイレクト
    assert_redirected_to login_url
  end
end</pre> 
<h4>実装前のリファクタリング</h4>
ログインの有無を確認するためにbeforeフィルターで定義したlogged_in_userメソッド
→Usersコントローラで定義してあるけどMicropostsコントローラでも使いたい！
→各コントローラが継承するApplicationコントローラに移動する 
<pre class="lang:ruby mark:(6-19) decode:true " title="/sample_app/app/controllers/application_controller.rb" >class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  #Sessionヘルパーモジュールの読み込み
  include SessionsHelper
  
  private
  
    # ログイン済みユーザーかどうか確認
    def logged_in_user
      # logged_in?がfalseの場合
      unless logged_in?
        # SessionsHelperメソッド　store_locationの呼び出し
        store_location
        # flashsでエラーメッセージを表示
        flash[:danger] = t('users.please_log_in')
        # login_urlにリダイレクト
        redirect_to login_url
      end
    end
    
end
</pre> 
重複しないようにUsersコントローラから該当部分を削除 
<pre class="lang:ruby mark:(6-18) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
    # beforeアクション

    # 以下、ハイライト部分を削除
    # # ログイン済みユーザーかどうか確認
    # def logged_in_user
    #   # logged_in?がfalseの場合
    #   unless logged_in?
    #     # SessionsHelperメソッド　store_locationの呼び出し
    #     store_location
    #     # flashsでエラーメッセージを表示
    #     flash[:danger] = t('users.please_log_in')
    #     # login_urlにリダイレクト
    #     redirect_to login_url
    #   end
    # end
    
    # 正しいユーザーかどうか確認
    def correct_user
      @user = User.find(params[:id])
      # root_urlにリダイレクト　current_user?メソッドがfalseの場合
      redirect_to(root_url) unless current_user?(@user)
    end
    
    # 管理者かどうか確認
    def admin_user
      # root_urlにリダイレクト current_user.admin?(ログイン中のユーザーが管理者であるか)がfalseの場合
      redirect_to(root_url) unless current_user.admin?
    end
end</pre> 
<h4> Micropostsコントローラへbeforフィルターを追加</h4> 
<pre class="lang:ruby mark:(2-3) decode:true " title="/sample_app/app/controllers/microposts_controller.rb" >class MicropostsController < ApplicationController
  # 直前にlogged_in_userメソッド（ApplicationController）を実行　:create, :destroyアクションにのみ適用
  before_action :logged_in_user, only: [:create, :destroy]
  
  def create
  end

  def destroy
  end
end
</pre> 
これでテストもGREEN！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>なぜUsersコントローラ内にあるlogged_in_userフィルターを残したままにするとマズイのでしょうか? 考えてみてください。</li>
</ol>
</blockquote>
1.
重複したコードはトラブルの元になる！
→片方だけ編集しちゃったりとか
<h4>重複したコードを残したまま片方を編集しちゃしまうとどうなるか</h4>
<pre class="toolbar:2 lang:default decode:true " >class UsersController < ApplicationController</pre> 
ApplicationControllerを継承したUsersControllerで重複したコードに一部変更があった場合
UsersControllerのコードが優先される→オーバーライド
<blockquote>オーバーライド（英：override）とは

オブジェクト指向におけるオブジェクトの継承の話で出てくる用語のひとつ

であり

親クラスにあるメソッドを子クラスで再定義することによって、子クラス上で親クラスのメソッドを上書きすること</blockquote>
[blogcard url="https://wa3.i-3-i.info/word138.html"]
今回は両方のコントローラーで同じ挙動をさせたいのにFインジェクション
重複コードを残したまま、それを忘れてどちらか一方を編集してしまうと呼び出すコントローラにより挙動が変わってしまう
→意図しない挙動になってしまうのでいくない！
<h3>13.3.2 マイクロポストを作成する</h3>
マイクロポストの作成用フォームはmicropost/newではなくルートパス（ホーム画面）に作る
参考画面は<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#fig-home_page_with_micropost_form_mockup" rel="noopener noreferrer" target="_blank">本文参考</a>
<blockquote class="quote-rort">最後にホーム画面を実装したときは (図 5.8)、[Sign up now!] ボタンが中央にありました。マイクロポスト作成フォームは、ログインしている特定のユーザーのコンテキストでのみ機能するので、この節の一つの目標は、ユーザーのログイン状態に応じて、ホーム画面の表示を変更することです。</blockquote>
<h4>Micropostのcreateアクション</h4>
基本的にはUsersコントローラのcreateアクションと似ているけど
User/Micropost関連付けを使って新しいマイクロポストを作る点が違っている
また、外部メソッドのmicropost_paramsでStrong Parametersを使い、マイクロポストのcontent属性だけをWeb経由で変更可能にしてある
実際のコード↓ 
<pre class="lang:ruby mark:(5-19,24-29) decode:true " title="/sample_app/app/controllers/microposts_controller.rb" >class MicropostsController < ApplicationController
  # 直前にlogged_in_userメソッド（ApplicationController）を実行　:create, :destroyアクションにのみ適用
  before_action :logged_in_user, only: [:create, :destroy]
  
  def create
    # @micropostに　ログイン中のユーザーに紐付いた新しいマイクロポストオブジェクトを返す（引数　micropost_params）
    @micropost = current_user.microposts.build(micropost_params)
    #  @micropostを保存できれば
    if @micropost.save
      # 成功のフラッシュメッセージを表示
      flash[:success] = t('.micropost_created')
      # ルートURLにリダイレクト
      redirect_to root_url
    # 保存できなければ
    else
      # static_pages/homeを描画
      render 'static_pages/home'
    end
  end

  def destroy
  end
  
  private

    def micropost_params
      # micropost属性必須　content属性のみ変更を許可
      params.require(:micropost).permit(:content)
    end
end
</pre> 
<h4>マイクロポスト作成フォーム</h4>
専用ページではなくstatic_pages/home.html.erbにフォームを作る
更に、userがログインしているかどうかで表示を変える（ログインしていなければ今まで通り、していたらフォームを表示）
ひとまずこう↓ 
<pre class="lang:default mark:(1-14,27-27) decode:true " title="/sample_app/app/views/static_pages/home.html.erb" ><!--ログインしていればこっち-->
<% if logged_in? %>
  <div class="row">
    <aside class="col-md-4">
      <section class="user_info">
        <%= render 'shared/user_info' %>
      </section>
      <section class="micropost_form">
        <%= render 'shared/micropost_form' %>
      </section>
    </aside>
  </div>
<!--ログインしていなければこっち-->
<% else %>
  <div class="center jumbotron">
    <h1><%= t('.Welcome_to_the_Sample_App') %></h1>
  
    <h2>
      <%= t('.welcome_message_html') %>
    </h2>
  
    <%= link_to "Sign up now!", signup_path, class: "btn btn-lg btn-primary" %>
  </div>
  
  <%= link_to image_tag("rails.png", alt: "Rails logo"),
              'http://rubyonrails.org/' %>
<% end %></pre> 
イマイチなので演習でリファクタリングするのだそうです

また、パーシャルもいくつか追加 
<pre class="lang:default decode:true " title="/sample_app/app/views/shared/_user_info.html.erb" ><!--サイドバーのユーザー情報部分-->
<%= link_to gravatar_for(current_user, size: 50), current_user %>
<h1><%= current_user.name %></h1>
<span><%= link_to "view my profile", current_user %></span>
<span><%= pluralize(current_user.microposts.count, "micropost") %></span></pre>  
<pre class="lang:default decode:true " title="/sample_app/app/views/shared/_micropost_form.html.erb" ><!--マイクロポスト投稿フォーム-->
<%= form_for(@micropost) do |f| %>
  <%= render 'shared/error_messages', object: f.object %>
  <div class="field">
    <%= f.text_area :content, placeholder: "Compose new micropost..." %>
  </div>
  <%= f.submit "Post", class: "btn btn-primary" %>
<% end %></pre> 
<h4>マイクロポストの投稿フォームを動かす</h4>
homeアクションにマイクロポストのインスタンス変数を追加 
<pre class="lang:ruby mark:(4-5) decode:true " title="/sample_app/app/controllers/static_pages_controller.rb" >class StaticPagesController < ApplicationController
  
  def home
    # @micropostに　ユーザーがログインしていればログイン中のユーザーに紐付いたMicropostオブジェクトを返す
    @micropost = current_user.microposts.build if logged_in?
  end

・
・
・</pre> 
これによりユーザーがログインしている時のみ@micropost変数が定義される
<h4>エラーメッセージのパーシャルを定義</h4>
_micropost_form.html.erb内のこのコード↓が動くようにするため 
<pre class="toolbar:2 lang:default decode:true " ><%= render 'shared/error_messages', object: f.object %></pre> 
現在のエラーパーシャルはこう 
<pre class="lang:default decode:true " title="/sample_app/app/views/shared/_error_messages.html.erb" ><% if @user.errors.any? %>
  <div id="error_explanation">
    <div class="alert alert-danger">
       <%= t('.errors count', errors_count: @user.errors.count) %>
    </div>
    <ul>
    <% @user.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %></pre> 
このままではviewのこの↓コードが動かない
<pre class="toolbar:2 lang:default decode:true " ><%= render 'shared/error_messages', object: f.object %></pre> 
元々のエラーメッセージパーシャルでは@users変数を直接参照している
→今回は@micropost変数を参照したい！
→@users変数でも@micropostでも参照できたい！
→フォーム変数fをf.objectにして関連付けられたオブジェクトにアクセスできるようにする！
<blockquote class="quote-rort">パーシャルにオブジェクトを渡すために、値がオブジェクトで、キーがパーシャルでの変数名と同じハッシュを利用します。これで、リスト 13.39の2行目のコードが完成します。言い換えると、object: f.objectはerror_messagesパーシャルの中でobjectという変数名を作成してくれるので、この変数を使ってエラーメッセージを更新すればよいということです</blockquote> 
<pre class="lang:default mark:(1-1,4-4,7-7) decode:true " title="/sample_app/app/views/shared/_error_messages.html.erb" ><% if object.errors.any? %>
  <div id="error_explanation">
    <div class="alert alert-danger">
      The form contains <%= pluralize(object.errors.count, "error") %>.
    </div>
    <ul>
    <% object.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %></pre> 
error_messagesパーシャルを更新してもテストはRED
→error_messagesパーシャルは他のviewからも呼び出されているため
 関連するviewを更新するとGREENになる！ 
※users/new.html.erbとusers/edit.html.erbのフォームはパーシャルでまとめているので
　本文と違ってusers/_form.html.erbを更新する
<pre class="lang:default mark:(3-3) decode:true " title="/sample_app/app/views/users/_form.html.erb" ><!--yieldメソッドで個別に設定したurlを渡す-->
<%= form_for(@user, url: yield(:url)) do |f| %>
    <%= render 'shared/error_messages', object: f.object %>

  <%= f.label :name %>
  <%= f.text_field :name, class: 'form-control' %></pre>  
<pre class="lang:default mark:(7-7) decode:true " title="/sample_app/app/views/password_resets/edit.html.erb" ><% provide(:title, t('.reset_password_title')) %>
<h1><%= t('.reset_password_title') %></h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(@user, url: password_reset_path(params[:id])) do |f| %>
      <%= render 'shared/error_messages', object: f.object %>

      <%= hidden_field_tag :email, @user.email %>
・
・
・</pre> 
GREENにならないね？って思ったら日本語化のアレでナニてったので直してGREEN！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>Homeページをリファクタリングして、if-else文の分岐のそれぞれに対してパーシャルを作ってみましょう。</li>
</ol>
</blockquote>
1.  
それぞれのファイルを作って該当部分を移動
（I18n対応はja.yml作り直すの面倒だったので階層で指定）
<pre class="lang:default decode:true " title="/sample_app/app/views/static_pages/_user_logged_in.html.erb" ><div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <%= render 'shared/user_info' %>
    </section>
    <section class="micropost_form">
      <%= render 'shared/micropost_form' %>
    </section>
  </aside>
</div></pre>  
<pre class="lang:default decode:true " title="/sample_app/app/views/static_pages/_user_logged_out.html.erb" ><div class="center jumbotron">
  <h1><%= t('static_pages.home.Welcome_to_the_Sample_App') %></h1>

  <h2>
    <%= t('static_pages.home.welcome_message_html') %>
  </h2>

  <%= link_to "Sign up now!", signup_path, class: "btn btn-lg btn-primary" %>
 </div>

<%= link_to image_tag("rails.png", alt: "Rails logo"),
              'http://rubyonrails.org/' %></pre> 
それぞれを呼び出すコードを追加
<pre class="lang:default mark:(3-3,6-6) decode:true " title="/sample_app/app/views/static_pages/home.html.erb" ><!--ログインしていればこっち-->
<% if logged_in? %>
  <%= render 'static_pages/user_logged_in' %>
<!--ログインしていなければこっち-->
<% else %>
  <%= render 'static_pages/user_logged_out' %>
<% end %></pre> 
<h3>13.3.3 フィードの原型</h3>
homeのページにマイクロポストが表示されるようにする→マイクロポストのフィードに実装
<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#fig-proto_feed_mockup" title="RoRT本文" rel="noopener noreferrer" target="_blank">RoRT本文</a>参照
全てのユーザーがフィードを持つので、feedメソッドはUserモデルに作る
→まずはログインユーザーのマイクロポストをすべて取得
→後々フォローしているユーザーのマイクロポストを含めたfeedを実装するので今回はwhereメソッドを使う 
<pre class="lang:ruby mark:(7-7) decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # 試作feedの定義
  # 完全な実装は次章の「ユーザーをフォローする」を参照
  def feed
    Micropost.where("user_id = ?", id)
  end

    private
・
・
・</pre> 
上記ハイライト行はセキュリティ上重要な役割を果たしている！
<blockquote class="quote-rort">SQLクエリに代入する前にidがエスケープされるため、SQLインジェクション (SQL Injection) と呼ばれる深刻なセキュリティホールを避けることができます。この場合のid属性は単なる整数 (すなわちself.idはユーザーのid) であるため危険はありませんが、SQL文に変数を代入する場合は常にエスケープする習慣をぜひ身につけてください。</blockquote>
<h4>SQLインジェクションとは何ぞ</h4>
<blockquote>穴埋めになっているSQL文の穴埋め部分に、作った人が意図しない内容を入れることによって、おかしな動きをさせること。もしくは、それができるようになっている状態</blockquote>
[blogcard url="https://wa3.i-3-i.info/word12800.html"]
<h4>Micropost.where("user_id = ?", id)とはなんぞ</h4>
プレースホルダー（?　の事）を使った書き方
第一引数の user_id =　？　の？に　第2引数の　id が置き換わる
[blogcard url="https://pikawaka.com/rails/where#プレースホルダの記述の仕方"]
SQLインジェクションを避けることが出来る！
また、上記のコードは現状コレ↓と同等だけど後々（14章）の為にこの↑形に 
<pre class="toolbar:2 lang:default decode:true " >def feed
  microposts
end</pre> 
<h4>フィード機能を実装していく</h4>
ログインユーザーのフィード用にインスタンス変数@feed_itemsを追加 
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/static_pages_controller.rb" >class StaticPagesController < ApplicationController
  
  def home
    # もしログインしていたら
    if logged_in?
      # @micropostに　current_userに紐付いたMicropostオブジェクトを代入
      @micropost  = current_user.microposts.build
      # @feed_itemsにcurrent_userに紐付いたフィードのpaginate(page: params[:page])を代入
      @feed_items = current_user.feed.paginate(page: params[:page])
    end
  end
・
・
・</pre> 
<pre class="toolbar:2 lang:default decode:true " ># 以前のコードが
@micropost = current_user.microposts.build if logged_in?

↓

# こうなっている
if logged_in?
  @micropost  = current_user.microposts.build
  @feed_items = current_user.feed.paginate(page: params[:page])
 end</pre>
これはRubyの慣習で、1行の時は後置if文、2行以上の時は前置if文を使う為

Homeページにフィード用のパーシャルを追加  
<pre class="lang:default mark:(3-3) decode:true " title="/sample_app/app/views/shared/_feed.html.erb" ><% if @feed_items.any? %>
  <ol class="microposts">
    <%= render @feed_items %>
  </ol>
  <%= will_paginate @feed_items %>
<% end %></pre> 
この時 
<pre class="toolbar:2 lang:default decode:true " ><%= render @feed_items %></pre> 
@feed_itemsにはcurrent_userに紐付いたfeedのpaginate(page: params[:page])が代入されており 
<pre class="toolbar:2 lang:default decode:true " ># @feed_itemsにcurrent_userに紐付いたfeedのpaginate(page: params[:page])を代入
@feed_items = current_user.feed.paginate(page: params[:page])</pre> 
feedってなんだっけって言うと 
<pre class="toolbar:2 lang:default decode:true " >def feed
  # Micropostテーブルからuser_idがidのユーザーをすべて取得
  Micropost.where("user_id = ?", id)
end</pre> 
つまり@feed_itemsの各要素はMicropostクラスを持っている
よってRailsはMicropostのパーシャル（_micropost.html.erb）を呼び出すことができる。
<blockquote class="quote-rort">このように、Railsは対応する名前のパーシャルを、渡されたリソースのディレクトリ内から探しにいくことができます。</blockquote>
→Homeページにステータスフィードを追加 
<pre class="lang:default mark:(10-13) decode:true " title="/sample_app/app/views/static_pages/_user_logged_in.html.erb" ><div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <%= render 'shared/user_info' %>
    </section>
    <section class="micropost_form">
      <%= render 'shared/micropost_form' %>
    </section>
  </aside>
    <div class="col-md-8">
      <h3>Micropost Feed</h3>
      <%= render 'shared/feed' %>
    </div>
</div></pre> 
※本文はapp/views/static_pages/home.html.erbになってるけど
13.3.2の演習でパーシャルに分けているので
「ログイン済の際に表示されるほう」のパーシャルにコードを追加する
<img src="https://rakuda3desu.net/wp-content/uploads/2020/05/13.3.3-01png.png" alt="" width="794" height="401" class="alignnone size-full wp-image-1508" />
<h4>現状の問題</h4>
<blockquote class="quote-rort">現時点では、新しいマイクロポストの作成は図 13.15で示したように期待どおりに動作しています。ただしささいなことではありますが、マイクロポストの投稿が失敗すると、 Homeページは@feed_itemsインスタンス変数を期待しているため、現状では壊れてしまいます。</blockquote>
→「NoMethodError in Microposts#create」になる
回避用のコードを追加する 
<pre class="lang:ruby mark:(10-10) decode:true " title="/sample_app/app/controllers/microposts_controller.rb" >・
・
・
  def create
    @micropost = current_user.microposts.build(micropost_params)
    if @micropost.save
      flash[:success] = "Micropost created!"
      redirect_to root_url
    else
      @feed_items = []
      render 'static_pages/home'
    end
  end
・
・
・</pre> 
この状態でマイクロポストの投稿を失敗させるとfeedの表示がおかしくなる→[]を代入したため@feed_itemsがemptyの状態
(上記の件どこでなおすとか書いてないけど、14章で完成させる機能っぽいから14章でかなぁ🐫）
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>新しく実装したマイクロポストの投稿フォームを使って、実際にマイクロポストを投稿してみましょう。Railsサーバーのログ内にあるINSERT文では、どういった内容をデータベースに送っているでしょうか? 確認してみてください。</li>
	<li>コンソールを開き、user変数にデータベース上の最初のユーザーを代入してみましょう。その後、Micropost.where("user_id = ?", user.id)とuser.microposts、そしてuser.feedをそれぞれ実行してみて、実行結果がすべて同じであることを確認してみてください。ヒント: ==で比較すると結果が同じかどうか簡単に判別できます。</li>
</ol>
</blockquote>
1. 
<pre class="lang:default decode:true " title="サーバーログ" >  SQL (0.9ms)  INSERT INTO "microposts" ("content", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?) 
　 [["content", "てすとのとうこう"], ["user_id", 1], ["created_at", "2020-05-16 17:36:16.920347"], ["updated_at", "2020-05-16 17:36:16.920347"]]</pre> 
macropostsテーブルの指定のカラムに対応した値を配列の形で送信している

2.
動作確認のみにて省略
<h3>13.3.4 マイクロポストを削除する</h3>
マイクロポストを削除する機能を実装する！！！
→ユーザー削除と同様に「delete（削除）」リンクによって作動させる
ユーザーの削除は管理者ユーザーのみが実行できるという制限だったけど
マイクロポストの削除は投稿した本人のみが削除できるようにする
<h4>viewにリンクの追加とdestroyアクションの実装</h4> 
<pre class="lang:ruby mark:(12-15) decode:true " title="/sample_app/app/views/microposts/_micropost.html.erb" ><!--CSSのidにマイクロポストのidを割り振っている　一般的に良いとされる慣習との事-->
<li id="micropost-<%= micropost.id %>">
  <!--gravatarからユーザの画像を呼び出して表示＆ユーザー詳細へリンク-->
  <%= link_to gravatar_for(micropost.user, size: 50), micropost.user %>
  <span class="user"><%= link_to micropost.user.name, micropost.user %></span>
  <span class="content"><%= micropost.content %></span>
  <span class="timestamp">idv
    <!--〇〇分前に投稿的な文字列を出してくれるヘルパーメソッド-->
    <!--Posted <%= time_ago_in_words(micropost.created_at) %> ago.-->
    <!--上記を取り入れて日本語化-->
    <%= t('.posted', time_ago: time_ago_in_words(micropost.created_at) ) %>
    <% if current_user?(micropost.user) %>
      <%= link_to t('.delete'), micropost, method: :delete,
                                       data: { confirm: t('.sure') } %>
    <% end %>
  </span>
</li></pre> 
Micropostsコントローラにdestroyアクションを定義していく
<blockquote class="quote-rort">関連付けを使ってマイクロポストを見つけるようにしている点です。これにより、あるユーザーが他のユーザーのマイクロポストを削除しようとすると、自動的に失敗するようになります。</blockquote>
→correct_userフィルターを定義し、その中で削除したいマイクロポストがログインしているユーザーのものかを確認する 
<pre class="lang:ruby mark:(4-5,9-14,23-28) decode:true " title="/sample_app/app/controllers/microposts_controller.rb" >class MicropostsController < ApplicationController
  # 直前にlogged_in_userメソッド（ApplicationController）を実行　:create, :destroyアクションにのみ適用
  before_action :logged_in_user, only: [:create, :destroy]
  # 直前にcorrect_userメソッドを実行　destroyアクションにのみ適用
  before_action :correct_user,   only: :destroy
・
・
・
  def destroy
    @micropost.destroy
    flash[:success] = t('.micropost_deleted')
    # リダイレクト　（request.referrerで返される）一つ前のURL　もしくはroot_url
    redirect_to request.referrer || root_url
  end
  
  private

    def micropost_params
      # micropost属性必須　content属性のみ変更を許可
      params.require(:micropost).permit(:content)
    end
    
    def correct_user
      # @microposutに代入　current_userのmicropostsからparams[:id]を持つmicropostを取得
      @micropost = current_user.microposts.find_by(id: params[:id])
      # root_urlにredirect　もし@micropostがnilなら
      redirect_to root_url if @micropost.nil?
    end
end</pre> 
<h4>request.referrerメソッドとは何ぞ</h4>
<blockquote class="quote-rort">このメソッドはフレンドリーフォワーディングのrequest.url変数 (10.2.3) と似ていて、一つ前のURLを返します (今回の場合、Homeページになります)13 。このため、マイクロポストがHomeページから削除された場合でもプロフィールページから削除された場合でも、request.referrerを使うことでDELETEリクエストが発行されたページに戻すことができるので、非常に便利です。</blockquote> 
<pre class="toolbar:2 lang:default decode:true " >redirect_to request.referrer || root_url</pre> 
また、元に戻すURLが見つからなかった場合は、||演算子を使ってroot_urlにリダイレクトされるようになっている
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>マイクロポストを作成し、その後、作成したマイクロポストを削除してみましょう。次に、Railsサーバーのログを見てみて、DELETE文の内容を確認してみてください。</li>
	<li>redirect_to request.referrer || root_urlの行をredirect_back(fallback_location: root_url)と置き換えてもうまく動くことを、ブラウザを使って確認してみましょう (このメソッドはRails 5から新たに導入されました)。</li>
</ol>
</blockquote>
1. 
idがエスケープされている
<pre class="toolbar:2 lang:default decode:true " >   (0.1ms)  begin transaction
  SQL (0.2ms)  DELETE FROM "microposts" WHERE "microposts"."id" = ?  [["id", 304]]
   (4.6ms)  commit transaction</pre> 
2.
→動く 
<pre class="toolbar:2 lang:default decode:true " >redirect_back(fallback_location: root_url)</pre> 
redirect_backメソッドで直前に実行したアクションへリダイレクト
引数のfallback_locationオプションで例外エラーが発生した際のリダイレクト先にroot_urlを指定している
<h3>13.3.5 フィード画面のマイクロポストをテストする</h3>
Micropostsコントローラの認可をチェックするテストと
まとめの統合テストを書いていく
<h4>テストの準備</h4>
マイクロポスト用のFixtureにそれそれ別のユーザーに紐付けられたマイクロポストを追加する
（今回は1つしか使わないけど後で他のマイクロポストも利用するとの事） 
<pre class="lang:default decode:true " title="/sample_app/test/fixtures/microposts.yml" >・
・
・
#  別のユーザーに所属しているマイクロポストを追加
ants:
  content: "Oh, is that what you want? Because that's how you get ants!"
  created_at: <%= 2.years.ago %>
  user: archer

zone:
  content: "Danger zone!"
  created_at: <%= 3.days.ago %>
  user: archer

tone:
  content: "I'm sorry. Your words made sense, but your sarcastic tone did not."
  created_at: <%= 10.minutes.ago %>
  user: lana

van:
  content: "Dude, this van's, like, rolling probable cause."
  created_at: <%= 4.hours.ago %>
  user: lana</pre> 
<h4>Micropostsコントローラの認可をチェックするテスト</h4>
自分以外のユーザーのマイクロポストを削除しようとすると適切にリダイレクトされるテスト 
<pre class="lang:ruby decode:true " title="/sample_app/test/controllers/microposts_controller_test.rb" >・
・
・
  test "should redirect destroy for wrong micropost" do
    # michaelでログイン
    log_in_as(users(:michael))
    # microposutに代入　（fixturesのマイクロポスト）antsを代入
    micropost = microposts(:ants)
    # ブロックで渡されたものを呼び出す前後でMicropost.countに違いがない
    assert_no_difference 'Micropost.count' do
      # micropost_path(micropost)にdeleteのリクエスト
      delete micropost_path(micropost)
    end
    # root_urlにリダイレクト
    assert_redirected_to root_url
  end
end</pre> 
次に統合テスト
<ol>
	<li>ログイン</li>
	<li>マイクロポストのページネーションの確認</li>
	<li>無効なマイクロポストを投稿</li>
	<li>マイクロポストの削除</li>
	<li>他のユーザーのマイクロポストには [delete] リンクが表示されない</li>
</ol>
といった手順で書いていく
→テストを生成 
<pre class="lang:default decode:true " title="ターミナル" >$ rails generate integration_test microposts_interface
Running via Spring preloader in process 10139
      invoke  test_unit
      create    test/integration/microposts_interface_test.rb</pre> 
テストを実装 
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/microposts_interface_test.rb" >require 'test_helper'

class MicropostsInterfaceTest < ActionDispatch::IntegrationTest

  def setup
    # @userにmichaelを代入
    @user = users(:michael)
  end

  test "micropost interface" do
    # @userでログイン
    log_in_as(@user)
    # root_pathにgetのリクエスト
    get root_path
    # 特定のHTMLタグが存在する→ class = "pagination"を持つdiv
    assert_select 'div.pagination'
    # 無効な送信
    # ブロックで渡されたものを呼び出す前後でMicropost.countに違いがな
    assert_no_difference 'Micropost.count' do
      # microposts_pathにpostのリクエスト　→　micropost: { content: "" }（無効なデータ）
      post microposts_path, params: { micropost: { content: "" } }
    end
    # 特定のHTMLタグが存在する→　id = "error_explanation"を持つdiv
    assert_select 'div#error_explanation'
    # 有効な送信
    # contentに代入　→　"This micropost really ties the room together"
    content = "This micropost really ties the room together"
    # ブロックで渡されたものを呼び出す前後でMicropost.countが+1
    assert_difference 'Micropost.count', 1 do
      # microposts_pathにpostのリクエスト　→　micropost: { content: content }（有効なデータ）
      post microposts_path, params: { micropost: { content: content } }
    end
    # root_urlにリダイレクト
    assert_redirected_to root_url
    #　指定されたリダイレクト先に移動
    follow_redirect!
    # 表示されたページのHTML本文すべての中にcontentが含まれている
    assert_match content, response.body
    # 投稿を削除する
    # 特定のHTMLタグが存在する→　text: '削除'を持つa
    assert_select 'a', text: '削除'
    # first_micropostに代入　@user.micropostsの1ページ目の1番目のマイクロポスト
    first_micropost = @user.microposts.paginate(page: 1).first
    # ブロックで渡されたものを呼び出す前後でMicropost.countが-1
    assert_difference 'Micropost.count', -1 do
      # micropost_path(first_micropost)にdeleteのリクエスト
      delete micropost_path(first_micropost)
    end
    # 違うユーザーのプロフィールにアクセス (削除リンクがないことを確認)
    # user_path(users(:archer))にgetのリクエスト
    get user_path(users(:archer))
    # 特定のHTMLタグが存在する→　text: '削除'を持つaが0個
    assert_select 'a', text: '削除', count: 0
  end
end</pre> 
機能は実装済なのでテストはGREEN！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>リスト 13.55で示した４つのコメント (「無効な送信」など) のそれぞれに対して、テストが正しく動いているか確認してみましょう。具体的には、対応するアプリケーション側のコードをコメントアウトし、テストが redになることを確認し、元に戻すと greenになることを確認してみましょう。</li>
	<li>サイドバーにあるマイクロポストの合計投稿数をテストしてみましょう。このとき、単数形 (micropost) と複数形 (microposts) が正しく表示されているかどうかもテストしてください。ヒント: リスト 13.57を参考にしてみてください。</li>
</ol>
</blockquote>
<ol>
	<li>動作確認のみにて省略</li>
	<li>I18nで言語をjaにしている影響で単数形と複数形は関係ない感じになっちゃってるけどこう↓</li>
</ol> 
<pre class="lang:ruby mark:(9-10,23-25) decode:true " title="/sample_app/test/integration/microposts_interface_test.rb" >・
・
・
    test "micropost sidebar count" do
    # @userでログイン
    log_in_as(@user)
    # root_pathにgetのリクエスト
    get root_path
    # 表示されたページのHTML本文すべての中に　#{@user.microposts.count}個の投稿があります　が含まれている
    assert_match "#{@user.microposts.count}個の投稿があります", response.body
    # other_userに代入 → users(:malory)　（まだマイクロポストを投稿していないユーザー）
    other_user = users(:malory)
    # other_user出pログイン
    log_in_as(other_user)
    # root_pathにgetのリクエスト
    get root_path
    # 表示されたページのHTML本文すべての中に　0個の投稿があります　が含まれている
    assert_match "0個の投稿があります", response.body
    # other_userに紐づいたmicropostを作成（content属性に値"A micropost"をセット）
    other_user.microposts.create!(content: "A micropost")
    # root_pathにgetのリクエスト
    get root_path
    # 表示されたページのHTML本文すべての中に　1個の投稿があります　が含まれている
    # 日本語にしていなければFILL_INは"1 micropost"（micropostsではなく単数形にする）
    assert_match "1個の投稿があります", response.body
  end
end</pre> 
<h3>まとめとか感想</h3>
長かったねええええええ！
本文に書いたけど、フィードの挙動がちゃんとしてないけど追々するのかな？
どこでするかわかったら追記するようにしたいと思います（曖昧
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

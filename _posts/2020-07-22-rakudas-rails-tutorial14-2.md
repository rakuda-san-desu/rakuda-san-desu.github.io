---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜14.2"
date: "2020-07-22 17:26:13"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1557
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial14-2/"
---
<h2>14.2 [Follow] のWebインターフェイス</h2>
フォロー/フォロー解除の基本的なインターフェイスを実装し
フォローしているユーザーとフォロワーにそれぞれ表示用のページを作成する！
<h3>14.2.1 フォローのサンプルデータ</h3>
rails db:seedを使ってDBにサンプルデータを登録する
先にサンプルデータを自動作成できるようにしておくことにより
WEBページの見た目から先に取り掛かることが出来、バックエンド機能の実装を後に回すことが出来る
<pre class="lang:ruby decode:true " title="/sample_app/db/seeds.rb" >・
・
・
# リレーションシップのサンプルを追加
# usersにすべてのユーザーを代入
users = User.all
# userにUserテーブルの1番目のユーザーを代入
user  = users.first
# followingにusersの3番目～51番目を代入
following = users[2..50]
# followersにusersの4番目～41番目を代入
followers = users[3..40]
# followingを順に取り出してブロック内を実行
# 取り出した要素をfollowedに代入　userがfollowedをフォロー
following.each { |followed| user.follow(followed) }
# followersを順に取り出してブロック内を実行
# 取り出した要素をfollowerに代入　followerがユーザーをフォロー
followers.each { |follower| follower.follow(user) }</pre> 
<ul>
	<li>userが3番目～51番目のユーザーをフォロー</li>
	<li>4番目～41番目のユーザーがuserをフォロー</li>
</ul> 
<pre class="lang:default decode:true " title="ターミナル" >$ rails db:migrate:reset
$ rails db:seed</pre> 
準備完了！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>コンソールを開き、User.first.followers.countの結果がリスト 14.14で期待している結果と合致していることを確認してみましょう。</li>
	<li>先ほどの演習と同様に、User.first.following.countの結果も合致していることを確認してみましょう。</li>
</ol>
</blockquote> 
<pre class="lang:default decode:true " title="ターミナル" ># User.firstがフォローされている数
>> User.first.followers.count
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
   (0.2ms)  SELECT COUNT(*) FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."follower_id" WHERE "relationships"."followed_id" = ?  [["followed_id", 1]]
=> 38
# User.firstがフォローしている数
>> User.first.following.count
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
   (0.2ms)  SELECT COUNT(*) FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."followed_id" WHERE "relationships"."follower_id" = ?  [["follower_id", 1]]
=> 49</pre> 
1も2も期待した通りの結果になっている
seedで作ったデータは（userに代入した）User.firstがフォローするほう（following）が先で
フォローされるほう（followers）が後だから
演習結果のカウントを見ると一瞬🤔ってなるね（コードを見ろと言われたらその通りなんだけど）
<h3>14.2.2 統計と [Follow] フォーム</h3>
サンプルユーザーにフォロイーとフォロワーが出来たので
プロフィールページとHomeページを更新してこれらが反映されるようにしていく！
<ol>
	<li>フォロイーとフォロワーの統計情報を表示するパーシャルを作る</li>
	<li>フォロー用とフォロー解除用のフォームを作る</li>
	<li>フォロイーとフォロワーそれぞれの一覧ページを作る</li>
</ol>
Twitterの慣習に倣ってフォロー数の単位には「following」を使うって事で<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-stats_partial_mockup" rel="noopener noreferrer" target="_blank">参考画像</a>も出てるけど
日本語ページだと「フォロー」「フォロワー」で出てるのでそうしようと思います
<blockquote class="quote-rort">統計情報には、現在のユーザーがフォローしている人数と、現在のフォロワーの人数が表示されています。それぞれの表示はリンクになっており、専用の表示ページに移動できます。第5章では、これらのリンクはダミーテキスト'#'を使って無効にしていました。しかしルーティングについての知識もだいぶ増えてきたので、今回は実装することにしましょう</blockquote>
実際のページは次のセクションで作成するんだけどルーティングは先に実装しちゃうとの事  
<pre class="lang:ruby mark:(4-12) decode:true " title="/sample_app/config/routes.rb" >・
・
・
  # resources :usersにネストを追加
  resources :users do
    # 以下でユーザーidが含まれるURLを扱えるようになる
    member do
      # :following, :followersのgetのルーティングを生成 
      get :following, :followers
    end
  end
  # account_activationsresourceのeditへのルーティングのみを生成
  resources :account_activations, only: [:edit]
  # password_resetsのnew、create、edit、updateのルーティングを生成 
  resources :password_resets,     only: [:new, :create, :edit, :update]
  # micropostsのcreateとdestroyのルーティングを生成
  resources :microposts,          only: [:create, :destroy]

end</pre> 
これにより/users/1/followingや/users/1/followersといったURL
following_user_path(1)やfollowers_user_path(1)といった名前付きルートが使えるようになる
（<a href="https://railstutorial.jp/chapters/following_users?version=5.1#table-following_routes" title="カスタムルールで提供するRESTfulルート" rel="noopener noreferrer" target="_blank">RoRT本文参照</a>）
<h4>ルーティングのネスト</h4>
[blogcard url="https://railsguides.jp/routing.html#%E3%83%8D%E3%82%B9%E3%83%88%E3%81%97%E3%81%9F%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9"][blogcard url="https://qiita.com/keisukegdk/items/beb5a62c17278c25c00d"]
ルーティングをネストすることで紐付いているモデルの親子関係を表すことが出来るようになる
<h4>統計情報を表示するパーシャルを作成＆表示</h4> 
<pre class="lang:default mark:(6-6,13-13) decode:true " title="/sample_app/app/views/shared/_stats.html.erb" ><!--@userに @user もしくは@userがnilの場合current_user を代入-->
<!--プロフィールページとHomeページの両方に表示させるため現在のユーザーを取得-->
<% @user ||= current_user %>
<div class="stats">
  <a href="<%= following_user_path(@user) %>">
    <strong id="following" class="stat">
      <!--@userのfollowing数を（DB内で）count-->
      <%= @user.following.count %>
    </strong>
    <%= t('.following') %>
  </a>
  <a href="<%= followers_user_path(@user) %>">
    <strong id="followers" class="stat">
      <!--@userのfollowers数を（DB内で）count-->
      <%= @user.followers.count %>
    </strong>
    <%= t('.followers') %>
  </a>
</div></pre> 
ハイライト行、
CSS idを指定しているのは、14.2.5でAjaxを実装するときに便利な為のと事 
<pre class="lang:default mark:(6-8) decode:true " title="/sample_app/app/views/static_pages/_user_logged_in.html.erb" ><div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <%= render 'shared/user_info' %>
    </section>
    <section class="stats">
      <%= render 'shared/stats' %>
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
出来上がったパーシャルが表示されるようにrenderで呼び出すんだけど
本文はhome.html.erbに追加してるけど
どこかでhome.html.erbの内容もログインしてるかしてないかのif文でパーシャルを呼び出すようにしているので
ログインしている際に呼び出される_user_logged_in.html.erbに該当コードを追加する 
<pre class="lang:sass mark:(12-47) decode:true " title="/sample_app/app/assets/stylesheets/custom.scss" >・
・
・
/* sidebar */
・
・
・
.gravatar_edit {
  margin-top: 15px;
}

.stats {
  overflow: auto;
  margin-top: 0;
  padding: 0;
  a {
    float: left;
    padding: 0 10px;
    border-left: 1px solid $gray-lighter;
    color: gray;
    &:first-child {
      padding-left: 0;
      border: 0;
    }
    &:hover {
      text-decoration: none;
      color: blue;
    }
  }
  strong {
    display: block;
  }
}

.user_avatars {
  overflow: auto;
  margin-top: 10px;
  .gravatar {
    margin: 1px 1px;
  }
  a {
    padding: 0;
  }
}

.users.follow {
  padding: 0;

/* forms */
・
・
・</pre> 
スタイルも追加（14章で使うすべてのスタイルを追加しているので、まだ出てきてない属性もあるけどｷﾆｼﾅｲ!!）
<h4>フォロー/フォロー解除フォームのパーシャル</h4> 
<pre class="lang:default decode:true " title="/sample_app/app/views/users/_follow_form.html.erb" ><!--@userがログインユーザー（current_user）でなければブロック内を表示-->
<% unless current_user?(@user) %>
  <div id="follow_form">
  <!--@userをログインユーザー（current_user）がフォローしていたらブロック内を表示-->
  <% if current_user.following?(@user) %>
    <!--unfollowのパーシャルを呼び出し-->
    <%= render 'unfollow' %>
  <!--そうでなければ-->
  <% else %>
    <!--followのパーシャルを表示-->
    <%= render 'follow' %>
  <% end %>
  </div>
<% end %></pre> 
↑これはfollowとunfollowのパーシャルに表示を分けているだけ
自分（ログインユーザー＝current_user）に対してはフォロー・フォロー解除は存在しないので
unless current_user?(@user)の時のみブロック内を表示する
また、それぞれのパーシャルではRelationshipsリソース用の新しいルーティングが必要な為
routes.rbにコードを追加 
<pre class="lang:ruby mark:(10-11) decode:true " title="/sample_app/config/routes.rb" >・
・
・
  # account_activationsresourceのeditへのルーティングのみを生成
  resources :account_activations, only: [:edit]
  # password_resetsのnew、create、edit、updateのルーティングを生成 
  resources :password_resets,     only: [:new, :create, :edit, :update]
  # micropostsのcreateとdestroyのルーティングを生成
  resources :microposts,          only: [:create, :destroy]
  # relationshipsのcreateとdestroyのルーティングを生成
  resources :relationships,       only: [:create, :destroy]
end</pre> 
ユーザーをフォローするフォームとフォロー解除するフォームでは
どちらもform_forを使ってRelationshipモデルオブジェクトを操作する
ユーザーをフォローするフォームでは新しいリレーションシップを作成するため
POSTリクエストをRelationshipsコントローラに送信してcreate (作成) する 
<pre class="lang:default decode:true " title="/sample_app/app/views/users/_follow.html.erb" ><!--current_user（ログイン中のユーザー）と結び付けた新しいRelationshipオブジェクトを返すフォーム-->
<%= form_for(current_user.active_relationships.build) do |f| %>
  <!--followed_idに@user.idを渡す-->
  <div><%= hidden_field_tag :followed_id, @user.id %></div>
  <%= f.submit t('.follow_btn'), class: "btn btn-primary" %>
<% end %></pre> 
ユーザーのフォローを解除するフォームでは既存のリレーションシップを見つけ出し
DELETEリクエストを送信してリレーションシップをdestroy (削除) する 
<pre class="lang:default decode:true " title="/sample_app/app/views/users/_unfollow.html.erb" ><!--current_user（ログイン中のユーザー）と結び付いたリレーションシップからfollowed_idが @user.idのデータを取得-->
<!--deleteメソッドを指定-->
<%= form_for(current_user.active_relationships.find_by(followed_id: @user.id),
             html: { method: :delete }) do |f| %>
  <%= f.submit t('.unfollow_btn'), class: "btn" %>
<% end %></pre> 
<blockquote class="quote-rort">最終的に、このフォロー/フォロー解除フォームにはボタンしかないことを理解していただけたと思います。しかし、それでもこのフォームはfollowed_idをコントローラに送信する必要があります。これを行うために、リスト 14.21のhidden_field_tagメソッドを使います。</blockquote>
hidden_field_tagメソッドが次のようなフォーム用のHTMLを生成してくれる
<pre class="toolbar:2 lang:default decode:true " ><input id="followed_id" name="followed_id" type="hidden" value="3" />
</pre> 
パスワードの再設定フォームの時のように
隠しフィールドのinputタグを使うことで、ブラウザ上に表示させずに適切な情報を含めることが出来る！

これらをまとめたプロフィール画面がこれ↓ 
<pre class="lang:default mark:(10-13,16-17) decode:true " title="/sample_app/app/views/users/show.html.erb" ><% provide(:title, @user.name) %>
<div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <h1>
        <%= gravatar_for @user %>
        <%= @user.name %>
      </h1>
    </section>
    <!--フォロー中・フォロワーの統計情報を表示-->
    <section class="stats">
      <%= render 'shared/stats' %>
    </section>
  </aside>
  <div class="col-md-8">
    <!--フォロー・フォロー解除のフォーム（ボタン）を表示-->
    <%= render 'follow_form' if logged_in? %>
    <!-- @user.micropostsがあったら表示する-->
    <% if @user.microposts.any? %>
      <h3><%= t('.microposts', microposts_count: @user.microposts.count) %></h3>
      <ol class="microposts">
        <%= render @microposts %>
      </ol>
      <%= will_paginate @microposts %>
    <% end %>
  </div>
</div></pre> 
ユーザー一覧ページから
未フォローのユーザーを表示させるとフォローボタンが
フォロー済のユーザーを選ぶとフォロー解除ボタンが表示される！
<h4>フォローボタンを動作させる為の2通りの方法</h4>
フォローボタンを使えるようにするためには
標準的（今までやってきた通り）な方法としてRelationshipsコントローラを作って制御する方法（14.2.4でやる）と
Ajaxという仕組みを使う方法（14.2.5でやる）がある
けど、その前に14.2.3でフォローしているユーザーとフォロワーを表示するページをそれぞれ作成してHTMLインターフェイスを完成させちゃうんだって！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>ブラウザから /users/2 にアクセスし、フォローボタンが表示されていることを確認してみましょう。同様に、/users/5 では [Unfollow] ボタンが表示されているはずです。さて、/users/1 にアクセスすると、どのような結果が表示されるでしょうか?</li>
	<li>ブラウザからHomeページとプロフィールページを表示してみて、統計情報が正しく表示されているか確認してみましょう。</li>
	<li>Homeページに表示されている統計情報に対してテストを書いてみましょう。ヒント: リスト 13.28で示したテストに追加してみてください。同様にして、プロフィールページにもテストを追加してみましょう。
</li>
</ol>
</blockquote>
<ol>
	<li>/users/2も/users/5も書かれている通りに表示される
/users/1は自分（current_user）なのでボタンは表示されない</li>
	<li>される</li>
	<li><a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#code-user_profile_test" rel="noopener noreferrer" target="_blank">リスト13.28</a>で示されているのがプロフィールページのテストな気が🤔
Homeページのテストだと/sample_app/test/integration/site_layout_test.rbと思うのでなんか作ったらいいのかな？</li>
</ol> 
 
<pre class="lang:ruby mark:(20-27) decode:true " title="/sample_app/test/integration/site_layout_test.rb" >・
・
・
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
    # 特定のHTMLタグが存在する→ strong id="following"
    assert_select 'strong#following'
    # 描写されたページに@user.following.countを文字列にしたものが含まれる
    assert_match @user.following.count.to_s, response.body
    # 特定のHTMLタグが存在する→ strong id="followers"
    assert_select 'strong#followers'
    # 描写されたページに@user.followers.countを文字列にしたものが含まれる
    assert_match @user.followers.count.to_s, response.body
  end
end</pre> 
<pre class="lang:ruby mark:(6-13) decode:true " title="/sample_app/test/integration/users_profile_test.rb" >・
・
・
    # 特定のHTMLタグが存在する→ h1のタグに含まれるimg.gravatar
    assert_select 'h1>img.gravatar'
    # 特定のHTMLタグが存在する→ strong id="following"
    assert_select 'strong#following'
    # 描写されたページに@user.following.countを文字列にしたものが含まれる
    assert_match @user.following.count.to_s, response.body
    # 特定のHTMLタグが存在する→ strong id="followers"
    assert_select 'strong#followers'
    # 描写されたページに@user.followers.countを文字列にしたものが含まれる
    assert_match @user.followers.count.to_s, response.body
    # 描画されたページに　@userのマイクロポストのcountを文字列にしたものが含まれる　
    assert_match @user.microposts.count.to_s, response.body
    # 特定のHTMLタグが存在する→ class = "pagination"を持つdivが1個
    assert_select 'div.pagination', count: 1
    # @user.micropostsのページネーションの1ページ目の配列を1個ずつ取り出してmicropostに代入
    @user.microposts.paginate(page: 1).each do |micropost|
      # micropostにmicropost.contentが含まれる
      assert_match micropost.content, response.body
    end
  end
end</pre> 
<h3>14.2.3 [Following] と [Followers] ページ</h3>
フォローしているユーザーを表示するページとフォロワーを表示するページは
プロフィールページとユーザー一覧ページを合わせたような作りになる
どちらもフォローの統計情報などのユーザー情報を表示するサイドバーとユーザーのリストがある
さらに小さいユーザープロフィール画像のリンクを格子状に並べて表示する
モックアップは<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-following_mockup" rel="noopener noreferrer" target="_blank">RoRT本文参照</a>
まずはフォローしているユーザーのリンクとフォロワーのリンクを動くようにする
Twitterに倣ってどちらのページでもユーザーログインを要求する形に
<h4>フォロー/フォロワーページの認可をテストする</h4>
今回のテストでは<a href="https://railstutorial.jp/chapters/following_users?version=5.1#table-following_routes" rel="noopener noreferrer" target="_blank">followingとfollowersの名前付きルート</a>を使っている 
<pre class="lang:ruby decode:true " title="/sample_app/test/controllers/users_controller_test.rb" >  test "should redirect following when not logged in" do
    # /users/@userのid/followingへgetのリクエスト
    get following_user_path(@user)
    # login_urlへリダイレクト
    assert_redirected_to login_url
  end

  test "should redirect followers when not logged in" do
    # /users/@userのid/followersへgetのリクエスト
    get followers_user_path(@user)
    # login_urlへリダイレクト
    assert_redirected_to login_url
  end
end</pre> 
現状はまだRED 
→Usersコントローラに2つの新しいアクションを追加する必要がある
<blockquote class="quote-rort">これはリスト 14.15で定義した2つのルーティングにもとづいており、これらはそれぞれfollowingおよびfollowersと呼ぶ必要があります。それぞれのアクションでは、タイトルを設定し、ユーザーを検索し、@user.followingまたは@user.followersからデータを取り出し、ページネーションを行なって、ページを出力する必要があります。</blockquote> 
<pre class="lang:ruby mark:(4-4,9-14,18-23) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  # 直前にlogged_in_userメソッド（ApplicationControllerにある）を実行　index,edit,update,following,followersアクションにのみ適用
  before_action :logged_in_user, only: [:index, :edit, :update, :destroy,
                                         :following, :followers]
・
・
・
  def following
    @title = t('.following_title')
    # @userにDBから取得したparams[:id]のuserを代入
    @user  = User.find(params[:id])
    # @usersに@user.followingのページネーションを代入
    @users = @user.following.paginate(page: params[:page])
    render 'show_follow'
  end

  def followers
    @title = t('.followers_title')
    # @userにDBから取得したparams[:id]のuserを代入
    @user  = User.find(params[:id])
    # @usersに@user.followersのページネーションを代入
    @users = @user.followers.paginate(page: params[:page])
    render 'show_follow'
  end
  
  # 外部に公開されないメソッド
  private
・
・
・
</pre> 
Railsはアクションに対応するビューを暗黙的に呼び出してくれる
→例）showアクションの最後でshow.html.erbを呼び出す
上記のfollowing・followers両アクションはrenderを明示的に呼び出し、show_followという同じビューを出力している
よって、それぞれのアクションに別々のビューを用意するのではなくshow_followというビューを1つ作ればOK 
<pre class="lang:default decode:true " title="/sample_app/app/views/users/show_follow.html.erb" ><!--ページtitleに →　Ruby on Rails @title|Tutorial Sample Appが表示される-->
<% provide(:title, @title) %>
<div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <%= gravatar_for @user %>
      <h1><%= @user.name %></h1>
      <span><%= link_to "view my profile", @user %></span>
      <span><b>Microposts:</b> <%= @user.microposts.count %></span>
    </section>
    <section class="stats">
      <%= render 'shared/stats' %>
      <% if @users.any? %>
        <div class="user_avatars">
          <% @users.each do |user| %>
            <%= link_to gravatar_for(user, size: 30), user %>
          <% end %>
        </div>
      <% end %>
    </section>
  </aside>
  <div class="col-md-8">
    <h3><%= @title %></h3>
    <% if @users.any? %>
      <ul class="users follow">
        <%= render @users %>
      </ul>
      <%= will_paginate %>
    <% end %>
  </div>
</div></pre> 
実行されたアクションによってフォローしているユーザー・フォロワーのいづれかの一覧が表示される
また「現在のユーザー」を使っていない為、他のユーザーの一覧ページも正しく表示される
<h4>show_followの統合テスト</h4>
ここまでのテストはGREEN
ここからshow_followの描写結果の統合テストを書いていく
HTML構造を網羅的にチェックするテストは少しの変更でもREDになり、逆に生産性を落としかねない
→正しい数が表示されているかどうかと、正しいURLが表示されているかどうかの２つの基本的なテストに留める 
<pre class="toolbar:2 lang:default decode:true " title="ターミナル" >$ rails generate integration_test following
      invoke  test_unit
      create    test/integration/following_test.rb</pre> 
統合テストを用意したらテスト用のデータをそろえる
→リレーションシップ用のfixtureにデータを追加 
<pre class="lang:default decode:true " title="/sample_app/test/fixtures/relationships.yml" >one:
  follower: michael
  followed: lana

two:
  follower: michael
  followed: malory

three:
  follower: lana
  followed: michael

four:
  follower: archer
  followed: michael</pre> 
<blockquote class="quote-rort">fixtureでは、前半の2つでMichaelがLanaとMaloryをフォローし、後半の2つでLanaとArcherがMichaelをフォローしています。</blockquote>
準備が出来たのでテストを書く 
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/following_test.rb" >require 'test_helper'

class FollowingTest < ActionDispatch::IntegrationTest
  # setupでmichaelを@userに代入ログイン済とする
  def setup
    @user = users(:michael)
    log_in_as(@user)
  end

  test "following page" do
    # /users/@userのid/followingにgetのリクエスト
    get following_user_path(@user)
    # falseである→　@user.followingがempty
    assert_not @user.following.empty?
    # trueである→ @user.followingのcountを文字列にしたものが本文に一致
    assert_match @user.following.count.to_s, response.body
    # @user.followingを順に取り出してuserに代入
    @user.following.each do |user|
      # 特定のHTMLタグが存在する→ a href = "/users/userのid"
      assert_select "a[href=?]", user_path(user)
    end
  end

  test "followers page" do
    # /users/@userのid/followersにgetのリクエスト
    get followers_user_path(@user)
    # falseである→　@user.followersがempty
    assert_not @user.followers.empty?
    # trueである→ @user.followersのcountを文字列にしたものが本文に一致
    assert_match @user.followers.count.to_s, response.body
    # @user.followersを順に取り出してuserに代入
    @user.followers.each do |user|
      # 特定のHTMLタグが存在する→ a href = "/users/userのid"
      assert_select "a[href=?]", user_path(user)
    end
  end
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>ブラウザから /users/1/followers と /users/1/following を開き、それぞれが適切に表示されていることを確認してみましょう。サイドバーにある画像は、リンクとしてうまく機能しているでしょうか?</li>
	<li>リスト 14.29のassert_selectに関連するコードをコメントアウトしてみて、テストが正しく red に変わることを確認してみましょう。</li>
</ol>
</blockquote>
動作確認のみにて省略
<h3>[Follow] ボタン (基本編)</h3>
ビューが整ったので[Follow] / [Unfollow] （[フォローする]/[フォロー解除]）ボタンを動作させる
フォローとフォロー解除はそれぞれリレーションシップの作成と削除に対応しているので、まずはRelationshipsコントローラを作成！ 
<pre class="toolbar:2 lang:default decode:true " >$ rails generate controller Relationships</pre> 
<h4>リレーションシップの基本的なアクセス制御とそれに対するテスト</h4>
まずはテストから！と言うことで
Relationshipsコントローラのアクションにアクセスする時、ログイン済のユーザーであるかどうかをチェックするテストを書く
ログイン済でなかった場合ログインページにリダイレクトされる→Relationshipのカウントが変わっていないことを確認 
<pre class="lang:ruby decode:true " title="/sample_app/test/controllers/relationships_controller_test.rb" >require 'test_helper'

class RelationshipsControllerTest < ActionDispatch::IntegrationTest

  test "create should require logged-in user" do
    # ブロックで渡されたものを呼び出す前後でUser.countに違いがない　
    assert_no_difference 'Relationship.count' do
      # relationships_pathにPOSTのリクエスト
      post relationships_path
    end
    # login_urlにリダイレクト
    assert_redirected_to login_url
  end

  test "destroy should require logged-in user" do
    # # ブロックで渡されたものを呼び出す前後でUser.countに違いがない
    assert_no_difference 'Relationship.count' do
      # oneのidのrelationship_pathにdeleteのリクエスト
      delete relationship_path(relationships(:one))
    end
    # login_urlにリダイレクト
    assert_redirected_to login_url
  end
end</pre> 
このテストをパスするためにRelationshipsコントローラのアクションに対してlogged_in_userフィルターを追加 
<pre class="lang:ruby mark:(2-2) decode:true " title="/sample_app/app/controllers/relationships_controller.rb" >class RelationshipsController < ApplicationController
  before_action :logged_in_user

  def create
  end

  def destroy
  end
end</pre> 
<h4> Relationshipsコントローラのそれぞれのアクション</h4>
<blockquote class="quote-rort">[Follow] / [Unfollow] ボタンを動作させるためには、フォーム (<a href="https://railstutorial.jp/chapters/following_users?version=5.1#code-follow_form" rel="noopener noreferrer" target="_blank">リスト 14.21</a>と<a href="https://railstutorial.jp/chapters/following_users?version=5.1#code-unfollow_form" rel="noopener noreferrer" target="_blank">リスト 14.22</a>) から送信されたパラメータを使って、followed_idに対応するユーザーを見つけてくる必要があります。</blockquote>
その後、見つけてきたユーザーに対して適切にfollow/unfollowメソッドを実行する 
<pre class="lang:default mark:(5-12,14-21) decode:true " title="/sample_app/app/controllers/relationships_controller.rb" >class RelationshipsController < ApplicationController
  # 直前にlogged_in_userメソッド（ApplicationController）を実行
  before_action :logged_in_user

  def create
    # userに代入　params[:followed_id]のデータをUserテーブルから取得
    user = User.find(params[:followed_id])
    # current_userでuserをフォローする
    current_user.follow(user)
    # userのプロフィールページにリダイレクト
    redirect_to user
  end

  def destroy
    # userに代入　Relationshipテーブルからfollowedカラムの内容がparams[:id]のデータを取得
    user = Relationship.find(params[:id]).followed
    # current_userでuserをアンフォローする
    current_user.unfollow(user)
    # userのプロフィールページにリダイレクト
    redirect_to user
  end
end</pre> 
<blockquote class="quote-rort">もしログインしていないユーザーが (curlなどのコマンドラインツールなどを使って) これらのアクションに直接アクセスするようなことがあれば、current_userはnilになり、どちらのメソッドでも2行目で例外が発生します。エラーにはなりますが、アプリケーションやデータに影響は生じません。このままでも支障はありませんが、やはりこのような例外には頼らない方がよいので、上ではひと手間かけてセキュリティのためのレイヤーを追加しました。</blockquote>
logged_in_userフィルターはなくても支障はないけど、ちゃんと設定しておこうというお話
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>ブラウザ上から /users/2 を開き、[Follow] と [Unfollow] を実行してみましょう。うまく機能しているでしょうか?</li>
	<li>先ほどの演習を終えたら、Railsサーバーのログを見てみましょう。フォロー/フォロー解除が実行されると、それぞれどのテンプレートが描画されているでしょうか?</li>
</ol>
</blockquote>
<ol>
	<li>動作確認のみにて省略</li>
	<li>どちらもusers/show.html.erb</li>
</ol>
<h3>14.2.5 [Follow] ボタン (Ajax編)</h3>
<blockquote class="quote-rort">ユーザーはプロフィールページを最初に表示し、それからユーザーをフォローし、その後すぐ元のページにリダイレクトされるという流れになります。ユーザーをフォローした後、本当にそのページから離れて元のページに戻らないといけないのでしょうか</blockquote>
Ajaxを使ってWebページからサーバーに「非同期」で、ページを移動することなくリクエストを送信する
[blogcard url="https://wa3.i-3-i.info/word12672.html"]
Railsでも簡単にAjaxを実装することが出来る 
<h4>Ajaxを使ったフォーム</h4>
<pre class="toolbar:2 lang:default decode:true " ># これを
form_for

# こうするだけ
form_for ..., remote: true</pre> 
実際に定義するとこうなる 
<pre class="lang:default mark:(3-3) decode:true " title="/sample_app/app/views/users/_follow.html.erb" ><!--current_user（ログイン中のユーザー）と結び付けた新しいRelationshipオブジェクトを返すフォーム-->
<!--Ajaxを採用　→　form_for・・・,remote: true-->
<%= form_for(current_user.active_relationships.build, remote: true) do |f| %>
  <!--followed_idに@user.idを渡す-->
  <div><%= hidden_field_tag :followed_id, @user.id %></div>
  <%= f.submit t('.follow_btn'), class: "btn btn-primary" %>
<% end %></pre>  
<pre class="lang:default mark:(6-6) decode:true " title="/sample_app/app/views/users/_unfollow.html.erb" ><!--current_user（ログイン中のユーザー）と結び付いたリレーションシップからfollowed_idが @user.idのデータを取得-->
<!--deleteメソッドを指定-->
<!--Ajaxを採用　→　form_for・・・,remote: true-->
<%= form_for(current_user.active_relationships.find_by(followed_id: @user.id),
             html: { method: :delete },
             remote: true) do |f| %>
  <%= f.submit t('.unfollow_btn'), class: "btn" %>
<% end %></pre> 
生成されるTHMLに関しての解説などは<a href="https://railstutorial.jp/chapters/following_users?version=5.1#code-unfollow_form_ajax" rel="noopener noreferrer" target="_blank">RoRT本文参照</a>
<h4>Ajaxリクエストに対応するRelationshipsコントローラ</h4>
<blockquote class="quote-rort">こういったリクエストの種類によって応答を場合分けするときは、respond_toメソッドというメソッドを使います。</blockquote> 
<pre class="toolbar:2 lang:default decode:true " ># リクエストの種類によってブロック内のいずれか1行が実行される
respond_to do |format|
  format.html { redirect_to user }
  format.js
end</pre> 
RelationshipsコントローラでAjaxに対応させるためにrespond_toメソッドをそれぞれのアクションに追加する
<blockquote class="quote-rort">このとき、ユーザーのローカル変数 (user) をインスタンス変数 (@user) に変更した点に注目してください。</blockquote>
初めはインスタンス変数は必要なかったけど、Ajaxを使ったフォームを実装したことによりビューでインスタンス変数が必要になったため 
<pre class="lang:ruby mark:(10-14,22-26) decode:true " title="/sample_app/app/controllers/relationships_controller.rb" >class RelationshipsController < ApplicationController
  # 直前にlogged_in_userメソッド（ApplicationController）を実行
  before_action :logged_in_user

  def create
    # @userに代入　userテーブルからparams[:followed_id]のデータを取得
    @user = User.find(params[:followed_id])
    # current_userで@userをフォローする
    current_user.follow(@user)
    # リクエストの種類によってブロック内のいずれか1行が実行される
    respond_to do |format|
      format.html { redirect_to @user }
      format.js
    end
  end

  def destroy
    # @userに代入　Relationshipテーブルからfollowedカラムの内容がparams[:id]のデータを取得
    @user = Relationship.find(params[:id]).followed
    # current_@serで@userをアンフォローする
    current_@user.unfollow(@user)
    # リクエストの種類によってブロック内のいずれか1行が実行される
    respond_to do |format|
      format.html { redirect_to @user }
      format.js
    end
  end
end</pre> 
また、今度はブラウザ側でJavaScriptが無効になっていた場合でもうまく動くようにする
（JavaScriptが無効→Ajaxリクエストが送れない） 
<pre class="lang:ruby decode:true " title="/sample_app/config/application.rb" >・
・
・
    # 認証トークンをremoteフォームに埋め込む
    config.action_view.embed_authenticity_token_in_remote_forms = true
  end
end</pre> 
難しいことはよくわからなかったんだけど
JSを有効にする設定という事？
[blogcard url="https://railsguides.jp/configuring.html#action-view%E3%82%92%E8%A8%AD%E5%AE%9A%E3%81%99%E3%82%8B"]
ユーザーをフォローしたときやフォロー解除したときにプロフィールページを更新するために
JavaScript用の埋め込みRuby (.js.erb) ファイル (create.js.erbやdestroy.js.erbなど) を編集していく
<blockquote class="quote-rort">純粋なJavaScriptと異なり、JS-ERbファイルでは組み込みRuby (ERb) が使えます。create.js.erbファイルでは、フォロー用のフォームをunfollowパーシャルで更新し、フォロワーのカウントを更新するのにERbを使っています (もちろんこれは、フォローに成功した場合の動作です)。</blockquote> 
<pre class="lang:default decode:true " title="/sample_app/app/views/relationships/create.js.erb" ><!--（_follow_form.html.erb内の）id="follow_form"にHTMLを挿入→render('users/unfollow')-->
$("#follow_form").html("<%= escape_javascript(render('users/unfollow')) %>");
<!--（_stats.html.erb内の）id="followers"の中にHTMLを挿入→<%= @user.followers.count %>-->
$("#followers").html('<%= @user.followers.count %>');</pre>  
フォロー解除した場合はfollowパーシャルで更新
<pre class="lang:default decode:true " title="/sample_app/app/views/relationships/destroy.js.erb" ><!--（_follow_form.html.erb内の）id="follow_form"にHTMLを挿入→render('users/follow')-->
$("#follow_form").html("<%= escape_javascript(render('users/follow')) %>");
<!--（_stats.html.erb内の）id="followers"の中にHTMLを挿入→<%= @user.followers.count %>-->
$("#followers").html('<%= @user.followers.count %>');</pre> 
<h4>escape_javascriptメソッドとは何ぞ</h4>
<blockquote class="quote-rort">JavaScriptファイル内にHTMLを挿入するときに実行結果をエスケープする</blockquote>
エスケープしておかないと文字化けでうまく作動しなくなる
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>ブラウザから /users/2 にアクセスし、うまく動いているかどうか確認してみましょう。</li>
	<li>先ほどの演習で確認が終わったら、Railsサーバーのログを閲覧し、フォロー/フォロー解除を実行した直後のテンプレートがどうなっているか確認してみましょう。</li>
</ol>
</blockquote>
<ol>
	<li>動作確認のみにて省略</li>
	<li>フォロー→　relationships/create.js.erb
フォロー解除→　relationships/destroy.js.erb</li>
</ol>
<h3>14.2.6 フォローをテストする</h3>
フォローボタンを設置できたので、バグを検知するためにシンプルなテストを書いていく！
<blockquote class="quote-rort">ユーザーのフォローに対するテストでは、 /relationshipsに対してPOSTリクエストを送り、フォローされたユーザーが1人増えたことをチェックします。</blockquote>
通常の、コントローラからアクションを呼び出す形式のフォロー/フォロー解除のテストに加え
Ajaxを利用した場合のテストも必要になる！→「xhr :true」オプションを追加するだけ！
 
<pre class="lang:ruby mark:(7-7,13-53) decode:true " title="/sample_app/test/integration/following_test.rb" >require 'test_helper'

class FollowingTest < ActionDispatch::IntegrationTest
  # setupでmichaelを@userに代入、archerを@otherに代入しそれぞれログイン済とする
  def setup
    @user = users(:michael)
    @other = users(:archer)
    log_in_as(@user)
  end
・
・
・  
  test "should follow a user the standard way" do
    # ブロック内の処理の前後で@user.following.countが1増える
    assert_difference '@user.following.count', 1 do
      # relationships_pathにpostのリクエスト（@other をフォローする）
      post relationships_path, params: { followed_id: @other.id }
    end
  end

  test "should follow a user with Ajax" do
    # ブロック内の処理の前後で@user.following.countが1増える
    assert_difference '@user.following.count', 1 do
      # relationships_pathにAjaxでpostのリクエスト（@other をフォローする）
      post relationships_path, xhr: true, params: { followed_id: @other.id }
    end
  end

  test "should unfollow a user the standard way" do
    # @userが@otherをフォロー
    @user.follow(@other)
    # relationshipに代入　→DBの@userのactive_relationshipsからfollowed_id:が@other.idと一致するデータ
    relationship = @user.active_relationships.find_by(followed_id: @other.id)
    # ブロック内の処理の前後で@user.following.countが-1
    assert_difference '@user.following.count', -1 do
      # relationship_pathにdeleteのリクエスト（relationshipを削除する）
      delete relationship_path(relationship)
    end
  end

  test "should unfollow a user with Ajax" do
    # @userが@otherをフォロー
    @user.follow(@other)
    # relationshipに代入　→DBの@userのactive_relationshipsからfollowed_id:が@other.idと一致するデータ
    relationship = @user.active_relationships.find_by(followed_id: @other.id)
    # ブロック内の処理の前後で@user.following.countが-1
    assert_difference '@user.following.count', -1 do
      # relationship_pathAjaxでにdeleteのリクエスト（relationshipを削除する）
      delete relationship_path(relationship), xhr: true
    end
  end
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>リスト 14.36のrespond_toブロック内の各行を順にコメントアウトしていき、テストが正しくエラーを検知できるかどうか確認してみましょう。実際、どのテストケースが落ちたでしょうか?</li>
	<li>リスト 14.40のxhr: trueがある行のうち、片方のみを削除するとどういった結果になるでしょうか? このとき発生する問題の原因と、なぜ先ほどの演習で確認したテストがこの問題を検知できたのか考えてみてください。</li>
</ol>
</blockquote>
1. 
<pre class="lang:default decode:true " ># createアクションのformat.html { redirect_to @user }をコメントアウトするとココが落ちる
  test "should follow a user the standard way" do
    # ブロック内の処理の前後で@user.following.countが1増える
    assert_difference '@user.following.count', 1 do
      # relationships_pathにpostのリクエスト（@other をフォローする）
      post relationships_path, params: { followed_id: @other.id }
    end
  end

# 続いてcreateアクションのformat.jsをコメントアウトうとするとココも落ちる
  test "should follow a user with Ajax" do
    # ブロック内の処理の前後で@user.following.countが1増える
    assert_difference '@user.following.count', 1 do
      # relationships_pathにAjaxでpostのリクエスト（@other をフォローする）
      post relationships_path, xhr: true, params: { followed_id: @other.id }
    end
  end

# destroyアクションのformat.html { redirect_to @user }をコメントアウトするとココが落ちる
  test "should unfollow a user the standard way" do
    # @userが@otherをフォロー
    @user.follow(@other)
    # relationshipに代入　→DBの@userのactive_relationshipsからfollowed_id:が@other.idと一致するデータ
    relationship = @user.active_relationships.find_by(followed_id: @other.id)
    # ブロック内の処理の前後で@user.following.countが-1
    assert_difference '@user.following.count', -1 do
      # relationship_pathにdeleteのリクエスト（relationshipを削除する）
      delete relationship_path(relationship)
    end
  end

# 続いてdestroyアクションのformat.jsをコメントアウトするとココも落ちる
  test "should unfollow a user with Ajax" do
    # @userが@otherをフォロー
    @user.follow(@other)
    # relationshipに代入　→DBの@userのactive_relationshipsからfollowed_id:が@other.idと一致するデータ
    relationship = @user.active_relationships.find_by(followed_id: @other.id)
    # ブロック内の処理の前後で@user.following.countが-1
    assert_difference '@user.following.count', -1 do
      # relationship_pathAjaxでにdeleteのリクエスト（relationshipを削除する）
      delete relationship_path(relationship), xhr: true
    end
  end
end</pre> 
それぞれのformat.js行のみをコメントアウトした場合はテストは落ちない
実際の処理が何で行われているかではなくてブロック内の処理の前後で@user.following.countが変化しているかをテストしているからってことだと思う
with Ajaxじゃないのって思うけど「そういうもの」と思っていてよいっぽい
2. 
<pre class="lang:default decode:true " ># test "should follow a user with Ajax"の該当行を削除
FAIL["test_should_follow_a_user_with_Ajax", FollowingTest, 1.5874034440003015]
 test_should_follow_a_user_with_Ajax#FollowingTest (1.59s)
        "@user.following.count" didn't change by 1.
        Expected: 3
          Actual: 2
        test/integration/following_test.rb:49:in `block in <class:FollowingTest>'

# test "should unfollow a user with Ajax"の該当行を削除
FAIL["test_should_unfollow_a_user_with_Ajax", FollowingTest, 1.5098085720001109]
 test_should_unfollow_a_user_with_Ajax#FollowingTest (1.51s)
        "@user.following.count" didn't change by -1.
        Expected: 2
          Actual: 3
        test/integration/following_test.rb:73:in `block in <class:FollowingTest>'</pre> 
@user.following.countが変わらないのでエラーになる
なぜか→ブロック内で何の処理も行っていない為
<h3>まとめとか感想</h3>
難しかったって言うか
14.2.6の演習が、そもそも日本語の文章の意図がわからなくてわからん🤔となりましたね。
結局難しくて追いきれなかったんだけど
「そういうもの」って事でいいのかな？って不安だったところを
「そういうものって事でいいかと」って言ってもらったので心置きなくそういう事にしました！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

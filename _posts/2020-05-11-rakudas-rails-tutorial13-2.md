---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜13.2"
date: "2020-05-11 14:19:02"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1480
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial13-2/"
---
<h2>13.2 マイクロポストを表示する</h2>
マイクロポストの表示予定図はこんな感じ
<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#fig-user_microposts_mockup">RoRT本文参照</a>です
<blockquote class="quote-rort">ここでは、Twitterのような独立したマイクロポストのindexページは作らずに、図 13.4のモックアップに示したように、ユーザーのshowページで直接マイクロポストを表示させることにします。ユーザープロフィールにマイクロポストを表示させるため、最初に極めてシンプルなERbテンプレートを作成します。次に、10.3.2のサンプルデータ生成タスクにマイクロポストのサンプルを追加して、画面にサンプルデータが表示されるようにしてみます。</blockquote>
<h3>13.2.1 マイクロポストの描画</h3>
ユーザーのプロフィール画面 (show.html.erb) に、そのユーザーのマイクロポストを表示させたり、そのユーザーが投稿した総数も表示させたりしていく
→一旦データベースをリセット＆サンプルデータの再生成
<pre class="toolbar:2 lang:default decode:true " >$ rails db:migrate:reset
$ rails db:seed</pre> 
そしてコントローラを生成
（いろいろ生成される中でここではviewのみ使う。コントローラは次の13.3から使用）
<pre class="lang:default decode:true " title="ターミナル" > $ rails generate controller Microposts
Running via Spring preloader in process 11058
      create  app/controllers/microposts_controller.rb
      invoke  erb
      create    app/views/microposts
      invoke  test_unit
      create    test/controllers/microposts_controller_test.rb
      invoke  helper
      create    app/helpers/microposts_helper.rb
      invoke    test_
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/microposts.coffee
      invoke    scss
      create      app/assets/stylesheets/microposts.scss</pre> 
<h4>ユーザーごとにすべてのマイクロポストを描画</h4>
<pre class="toolbar:2 lang:default decode:true " ># _user.html.erbパーシャルを使って自動的に@users変数内のそれぞれのユーザーを出力していたユーザー一覧のコードを参考に
<ul class="users">
  <%= render @users %>
</ul>

# _micropost.html.erbパーシャルを使ってマイクロポストのコレクションを表示しようとするとこう↓
<ol class="microposts">
  <%= render @microposts %>
</ol></pre> 
ユーザー一覧と違いulタグではなくolタグを使っている
→マイクロポストが特定の順序に依存しているため
<h4>1つのマイクロポストを表示するパーシャル</h4>
一つのマイクロポストを表示するためのパーシャル 
→/sample_app/app/views/microposts/に_micropost.html.erbファイルを生成
<pre class="lang:ruby decode:true " title="/sample_app/app/views/microposts/_micropost.html.erb" ><!--CSSのidにマイクロポストのidを割り振っている　一般的に良いとされる慣習との事-->
<li id="micropost-<%= micropost.id %>">
  <!--gravatarからユーザの画像を呼び出して表示＆ユーザー詳細へリンク-->
  <%= link_to gravatar_for(micropost.user, size: 50), micropost.user %>
  <span class="user"><%= link_to micropost.user.name, micropost.user %></span>
  <span class="content"><%= micropost.content %></span>
  <span class="timestamp">
    <!--どれくらい前かを文字列で返すヘルパーメソッド-->
    Posted <%= time_ago_in_words(micropost.created_at) %> ago.
  </span>
</li></pre> 
<h4>time_ago_in_wordsとは何ぞ</h4>
<blockquote class="quote-rort">ここではtime_ago_in_wordsというヘルパーメソッドを使っています。これはメソッド名の表すとおりですが、「3分前に投稿」といった文字列を出力します。具体的な効果について13.2.2で説明します。</blockquote>
<h4>ページネーションを使って表示</h4>
一度にすべてのマイクロポストが表示されてしまうことを防ぐ為に
ユーザー一覧ページと同じくページネーションを導入 
<pre class="toolbar:2 lang:default decode:true " ># ユーザー一覧ページで使ったページネーションのコード
<%= will_paginate %>

# 今回使うページネーションのコード
<%= will_paginate @microposts %></pre> 
<blockquote class="quote-rort">上のコードは引数なしで動作していました。これはwill_paginateが、Usersコントローラのコンテキストにおいて、@usersインスタンス変数が存在していることを前提としているためです。</blockquote>
ActiveRecord::Relationクラスな@usersインスタンス変数はUsersコントローラのコンテキスト（Usersコントローラに含まれる情報）なので、同じくUsersコントローラのコンテキスト（であるところのViewのテンプレート上）からwill_paginateを呼び出しているため（@usersが存在するという前提で）will_paginateがよしなにやってくれていた
今回はUsersコントローラのコンテキストから、異なるコンテキストのマイクロポストをページネーションしたいので
（明示的に）@microposts変数をwill_paginateに渡す必要がある
<blockquote class="quote-rort">したがって、そのようなインスタンス変数をUsersコントローラのshowアクションで定義しなければなりません</blockquote> 
<pre class="lang:ruby mark:(7-8) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
  def show
    # @userにUserテーブルから(params[:id])のデータを取り出して代入
    @user = User.find(params[:id])
    # @micropostに　@userのmicropostsのページネーションの指定ページ（params[:page]）を代入
    @microposts = @user.microposts.paginate(page: params[:page])
・
・
・</pre> 
belongs_to/has_manyの関連付けを行っているので
paginateメソッドでmicropostテーブルから必要な（user_idが@user.idと等しい）マイクロポストのページを引き出せる
<h4>コンテキストとはなんぞ</h4>
Usersコントローラのコンテキスト
↓
Usersコントローラの制御情報
↓
Usersコントローラに含まれる各種アクションや設定など諸々
<blockquote>コンテキストとは、プログラムの実行に必要な各種情報のことである。

「context」（コンテキスト）は、「文脈」、「前後関係」などと訳されるが、IT用語としては意味がイメージしづらく、単にコンテキストとある場合は、何らかの制御情報と考える方がわかりやすいことが多い。</blockquote>
[blogcard url="https://www.sophia-it.com/content/%E3%82%B3%E3%83%B3%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88"]
<h4>マイクロポストの投稿数の表示</h4>
関連付けをとおしてcountメソッドを呼び出せる
<pre class="toolbar:2 lang:default decode:true " >user.microposts.count</pre> 
<h4>要素が出そろったので実装！</h4> 
<pre class="lang:default mark:(11-20) decode:true " title="/sample_app/app/views/users/show.html.erb" ><% provide(:title, @user.name) %>
<div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <h1>
        <%= gravatar_for @user %>
        <%= @user.name %>
      </h1>
    </section>
  </aside>
  <div class="col-md-8">
    <!-- @user.micropostsがあったら表示する-->
    <% if @user.microposts.any? %>
      <h3>Microposts (<%= @user.microposts.count %>)</h3>
      <ol class="microposts">
        <%= render @microposts %>
      </ol>
      <%= will_paginate @microposts %>
    <% end %>
  </div>
</div></pre> 
新しくなったユーザー詳細ページをブラウザで確認！
しても、マイクロポストがないので表示が変わりません
→次でマイクロポストのサンプルを追加する
その前に演習！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>7.3.3で軽く説明したように、今回ヘルパーメソッドとして使ったtime_ago_in_wordsメソッドは、Railsコンソールのhelperオブジェクトから呼び出すことができます。このhelperオブジェクトのtime_ago_in_wordsメソッドを使って、3.weeks.agoや6.months.agoを実行してみましょう。</li>
	<li>helper.time_ago_in_words(1.year.ago)と実行すると、どういった結果が返ってくるでしょうか?</li>
	<li>micropostsオブジェクトのクラスは何でしょうか? ヒント: リスト 13.23内のコードにあるように、まずはpaginateメソッド (引数はpage: nil) でオブジェクトを取得し、その後classメソッドを呼び出してみましょう。</li>
</ol>
</blockquote>
1. 
<pre class="lang:default decode:true " title="コンソール" >>> helper.time_ago_in_words(3.weeks.ago)
=> "21日"
>> helper.time_ago_in_words(6.months.ago)
=> "6ヶ月"</pre> 
2. 
<pre class="lang:default decode:true " title="コンソール" >>> helper.time_ago_in_words(1.year.ago)
=> "約1年"</pre> 
3. 
<pre class="lang:default decode:true " title="コンソール" ># 取ってくるユーザーは誰でもよい
>> user = User.find(1)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-05-03 08:10:09", updated_at: "2020-05-03 08:10:09", password_digest: "$2a$10$/PI2i0b1VZTXANJl8YJ0U.WCtj3VdrqDGbpOkiXwWU/...", remember_digest: nil, admin: true, activation_digest: "$2a$10$lAYqQwkllJQAKs0445gLY.noAS2ByqtI/KgOVlfb5aV...", activated: true, activated_at: "2020-05-03 08:10:09", reset_digest: nil, reset_sent_at: nil>
# mに　userのmicropostsのページネーションの最初のページ（page: nil）を代入
>> m = user.microposts.paginate(page: nil)
  Micropost Load (0.1ms)  SELECT  "microposts".* FROM "microposts" WHERE "microposts"."user_id" = ? ORDER BY "microposts"."created_at" DESC LIMIT ? OFFSET ?  [["user_id", 1], ["LIMIT", 11], ["OFFSET", 0]]
=> #<ActiveRecord::AssociationRelation []>
>> m.class
=> Micropost::ActiveRecord_AssociationRelation</pre> 
2の"約1年"が"1年"じゃないのは仕様だから！という事
<h3>13.2.2 マイクロポストのサンプル</h3>
マイクロポストのサンプルも追加して表示を確認できるようにしたい！
<blockquote class="quote-rort">すべてのユーザーにマイクロポストを追加しようとすると時間が掛かり過ぎるので、takeメソッドを使って最初の6人だけに追加します。（中略）この6人については、1ページの表示限界数 (30) を越えさせるために、それぞれ50個分のマイクロポストを追加するようにしています。</blockquote>
<pre class="toolbar:2 lang:default decode:true " >User.order(:created_at).take(6)</pre> 
<h4>orderメソッドとは何ぞ</h4>
[blogcard url="https://pikawaka.com/rails/order"]
上の例は
<pre class="toolbar:2 lang:default decode:true " ># Userモデルを　created_atの順に並び替えて　上から6個を（配列として）返す
User.order(:created_at).take(6)</pre> 
<h4>Faker gemでサンプルを追加</h4>
Faker gemのLorem.sentenceというメソッドを使ってsampleを追加する。
 
<pre class="lang:ruby decode:true " title="/sample_app/db/seeds.rb" >・
・
・
# マイクロポストのサンプルを追加
# usersに　Userモデルを　created_atの順に並び替えて　上から（6個を）配列として　代入
users = User.order(:created_at).take(6)
# 50回繰り返す
50.times do
  # contetに　Faker::Loremで作ったサンプルを代入（Faker::Loremから文章を5個取り出す）
  content = Faker::Lorem.sentence(5)
  # usersを順番に取り出してブロック内を実行
  # 取り出した要素をuserに代入　userに紐づいたmicropostを作成（content属性に変数contentの値）
  users.each { |user| user.microposts.create!(content: content) }
end</pre> 
「user1のマイクロポスト×５０、user2のマイクロポスト×５０・・・」ではなく
「user1のマイクロポスト１・・・user6のマイクロポスト1、user1のマイクロポスト2・・・user6のマイクロポスト2」といった繰り返しになっている
→タイムラインを実装するときの見栄えが良い
<pre class="lang:default decode:true " title="ターミナル" ># DBをリセットからの再生成
$ rails db:migrate:reset
$ rails db:seed

# サーバー再起動
$ rails s</pre> 
 
<pre class="lang:css decode:true " title="/sample_app/app/assets/stylesheets/custom.scss" >/* スタイルを整える */
/* microposts */

.microposts {
  list-style: none;
  padding: 0;
  li {
    padding: 10px 0;
    border-top: 1px solid #e8e8e8;
  }
  .user {
    margin-top: 5em;
    padding-top: 0;
  }
  .content {
    display: block;
    margin-left: 60px;
    img {
      display: block;
      padding: 5px 0;
    }
  }
  .timestamp {
    color: $gray-light;
    display: block;
    margin-left: 60px;
  }
  .gravatar {
    float: left;
    margin-right: 10px;
    margin-top: 5px;
  }
}

aside {
  textarea {
    height: 100px;
    margin-bottom: 5px;
  }
}

span.picture {
  margin-top: 10px;
  input {
    border: 0;
  }
}</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>(1..10).to_a.take(6)というコードの実行結果を推測できますか? 推測した値が合っているかどうか、実際にコンソールを使って確認してみましょう。</li>
	<li>先ほどの演習にあったto_aメソッドの部分は本当に必要でしょうか? 確かめてみてください。</li>
	<li>Fakerはlorem ipsum以外にも、非常に多種多様の事例に対応しています。Fakerのドキュメント (英語) を眺めながら画面に出力する方法を学び、実際に大学名や電話番号、Hipster IpsumやChuck Norris facts (参考: チャック・ノリスの真実) を画面に出力してみましょう。(訳注: もちろん日本語にも対応していて、例えば沖縄らしい用語を出力するfaker-okinawaもあります。ぜひ遊んでみてください。)</li>
</ol>
</blockquote>
1. 
<pre class="toolbar:2 lang:default decode:true " ># 1-10を　配列にして　先頭から6個を取り出す
>> (1..10).to_a.take(6)
=> [1, 2, 3, 4, 5, 6]
</pre> 
2. 
<pre class="lang:default decode:true " ># takeが「要素を配列にして先頭から引数個返す」ためto_aは不要
>> (1..10).take(6)
=> [1, 2, 3, 4, 5, 6]</pre> 
3. 
<pre class="lang:default decode:true " >>> Faker::University.name
=> "太田医科大学"
>> Faker::PhoneNumber.phone_number
=> "081-529-2436"
>> Faker::Hipster.sentence
=> "Beard kombucha retro fingerstache carry."
>> Faker::ChuckNorris.fact
=> "When Chuck Norris' code fails to compile the compiler apologises."
# 沖縄のやつは別途GEMが必要なので割愛</pre> 
<h3>13.2.3 プロフィール画面のマイクロポストをテストする</h3>
プロフィール画面が正しく描画されるかのテストはアカウントの有効化テストで行っている
→ここではプロフィール画面で表示されるマイクロポストに対しての統合テストを書いていく 
<h4>テストの準備</h4>
<pre class="lang:default decode:true " title="ターミナル" ># 統合テストを作成
 $ rails generate integration_test users_profile
Running via Spring preloader in process 10674
      invoke  test_unit
      create    test/integration/users_profile_test.rb</pre> 
<blockquote class="quote-rort">プロフィール画面におけるマイクロポストをテストするためには、ユーザーに紐付いたマイクロポストのテスト用データが必要になります。</blockquote>
<pre class="toolbar:2 lang:default mark:(2-2,13-13) decode:true " ># users.ymlにあるユーザー名
michael:
  name: Michael Example
  email: michael@example.com
  .
  .
  .

# microposts.ymlのデータにユーザーを紐付ける
orange:
  content: "I just ate an orange!"
  created_at: <%= 10.minutes.ago %>
  user: michael</pre> 
userにuserのnameの値を渡すと、Railsがfixtureファイル内の対応するユーザーを探して、見つかったらマイクロポストに関連付けてくれる
スゴイ！
また、埋め込みRubyを使ってテストデータを追加する 
<pre class="lang:default mark:(4-4,9-9,14-14,19-19,21-27) decode:true " title="/sample_app/test/fixtures/microposts.yml" >orange:
  content: "I just ate an orange!"
  created_at: <%= 10.minutes.ago %>
  user: michael

tau_manifesto:
  content: "Check out the @tauday site by @mhartl: http://tauday.com"
  created_at: <%= 3.years.ago %>
  user: michael

cat_video:
  content: "Sad cats are sad: http://youtu.be/PKffm2uI4dk"
  created_at: <%= 2.hours.ago %>
  user: michael

most_recent:
  content: "Writing a short test"
  created_at: <%= Time.zone.now %>
  user: michael

# user: michaelのマイクロポストを30個
<% 30.times do |n| %>
micropost_<%= n %>:
  content: <%= Faker::Lorem.sentence(5) %>
  created_at: <%= 42.days.ago %>
  user: michael
<% end %></pre> 
<h4>テストを実装</h4>
<blockquote class="quote-rort">今回のテストでは、プロフィール画面にアクセスした後に、ページタイトルとユーザー名、Gravatar、マイクロポストの投稿数、そしてページ分割されたマイクロポスト、といった順でテストしていきます。</blockquote> 
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/users_profile_test.rb" >require 'test_helper'

class UsersProfileTest < ActionDispatch::IntegrationTest
  # ApplicationHelperを読み込む→full_titleヘルパーが利用できる
  include ApplicationHelper

  def setup
    @user = users(:michael)
  end

  test "profile display" do
    # user_path(@user)にgetのリクエスト
    get user_path(@user)
    # テンプレートが描画される　'users/show
    assert_template 'users/show'
    # 特定のHTMLタグが存在する→　title, full_title(@user.name)→@user.name | Ruby on Rails Tutorial Sample App
    assert_select 'title', full_title(@user.name)
    # 特定のHTMLタグが存在する→ h1, text: @user.name
    assert_select 'h1', text: @user.name
    # 特定のHTMLタグが存在する→ h1のタグに含まれるimg.gravatar
    assert_select 'h1>img.gravatar'
    # 描画されたページに　@userのマイクロポストのcountを文字列にしたものが含まれる　
    assert_match @user.microposts.count.to_s, response.body
    # 特定のHTMLタグが存在する→ class = "pagination"を持つdiv
    assert_select 'div.pagination'
    # @user.micropostsのページネーションの1ページ目の配列を1個ずつ取り出してmicropostに代入
    @user.microposts.paginate(page: 1).each do |micropost|
      # このページにmicropost.contentが含まれる
      assert_match micropost.content, response.body
    end
  end
end</pre> 
<h4>response.bodyとはなんぞ</h4>
前にも少し書いてましたが改めて。
<blockquote class="quote-rort">response.bodyにはそのページの完全なHTMLが含まれています (HTMLのbodyタグだけではありません)</blockquote>
bodyの内容だけではなくてページ内のすべてのHTMLが含まれるという事
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>リスト 13.28にある２つの'h1'のテストが正しいか確かめるため、該当するアプリケーション側のコードをコメントアウトしてみましょう。テストが green から redに変わることを確認してみてください。</li>
	<li>リスト 13.28にあるテストを変更して、will_paginateが１度のみ表示されていることをテストしてみましょう。ヒント: 表 5.2を参考にしてください。</li>
</ol>
</blockquote>
1.
動作確認のみにて省略

2. 
<pre class="lang:ruby mark:(6-7) decode:true " title="/sample_app/test/integration/users_profile_test.rb" >・
・
・
    # 描画されたページに　@userのマイクロポストのcountを文字列にしたものが含まれる　
    assert_match @user.microposts.count.to_s, response.body
    # 特定のHTMLタグが存在する→ class = "pagination"を持つdivが1個
    assert_select 'div.pagination', count: 1
    # @user.micropostsのページネーションの1ページ目の配列を1個ずつ取り出してmicropostに代入
    @user.microposts.paginate(page: 1).each do |micropost|
      # このページにmicropost.contentが含まれる
      assert_match micropost.content, response.body
    end
  end
end</pre> 
<h3>まとめとか感想</h3>
細かいところに？！っとなりつつ
全体はスムーズに進められた、と、思います！
見た目が整っていくの楽しい✨
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

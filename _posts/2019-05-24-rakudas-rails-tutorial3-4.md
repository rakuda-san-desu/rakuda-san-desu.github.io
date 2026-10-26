---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜3.4"
date: "2019-05-24 19:58:05"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 686
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial3-4/"
---
<h2>3.4 少しだけ動的なページ</h2>
Home、Help、Aboutそれぞれのページタイトルが表示されるようにしていく
準備として元々用意されているレイアウトのファイルを一時的に無効に
<pre class="lang:default decode:true " title="ターミナル" >#ファイルの移動（＆リネーム）
#app/views/layoutsにあるapplication.html.erbを　ルート直下に移動＆ファイル名をlayout_fileに
$ mv app/views/layouts/application.html.erb layout_file
</pre>
<h3>3.4.1 タイトルをテストする (Red)</h3>
HTMLの構造についてのおさらい

assert_selectメソッド　→　特定のHTMLタグが存在するかテストするメソッド
<title>タグ内に「Home | Ruby on RailsTutorial Sample App」が存在するかと言う1文をそれぞれのテストに追加
<pre class="lang:ruby mark:(8-8,14-14,20-20) decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb" >require 'test_helper'

class StaticPagesControllerTest < ActionDispatch::IntegrationTest

  test "should get home" do
    get static_pages_home_url
    assert_response :success
    assert_select "title", "Home | Ruby on RailsTutorial Sample App" 
  end

  test "should get help" do
    get static_pages_help_url
    assert_response :success
    assert_select "title", "Help | Ruby on RailsTutorial Sample App" 
  end

  test "should get about" do
    get static_pages_about_url
    assert_response :success
    assert_select "title", "About | Ruby on Rails Tutorial Sample App"
  end
end</pre> 
<h4 id="what_is_assert">アサーションメソッドとは何ぞ</h4>
assert（assert_○○）なメソッド
テストの判定をしてくれる便利で重要な物。いろいろな種類がある。
<a href="https://railsguides.jp/testing.html#%E5%88%A9%E7%94%A8%E5%8F%AF%E8%83%BD%E3%81%AA%E3%82%A2%E3%82%B5%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3" rel="noopener noreferrer" target="_blank">Rails テスティングガイド　2.6 利用可能なアサーション</a>
<h4>テストスイートとは何ぞ</h4>
何を調べるのか
test　～　do
・
・
・
end
この固まりがテストケース
で、テストケースの固まりがテストスイート

で、この状態でテストをすると<span class="red">RED</span>
<h3>3.4.2 タイトルを追加する (Green)</h3>
本文ママ
<h4>演習</h4>
指示通りにテストコードを更新してテストも<span class="green">GREEN</span>
<pre class="lang:ruby mark:(5-9,14-14,20-20,26-26) decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb" >require 'test_helper'

class StaticPagesControllerTest < ActionDispatch::IntegrationTest

  #setupメソッド
  def setup
    #変数@base_titleにRuby on Rails Tutorial Sample Appを代入
    @base_title = "Ruby on Rails Tutorial Sample App"
  end

  test "should get home" do
    get static_pages_home_url
    assert_response :success
    assert_select "title", "Home | #{@base_title}"
  end

  test "should get help" do
    get static_pages_help_url
    assert_response :success
    assert_select "title", "Help | #{@base_title}"
  end

  test "should get about" do
    get static_pages_about_url
    assert_response :success
    assert_select "title", "About | #{@base_title}"
  end
end</pre> 
<blockquote class="quote-rort">setupという特別なメソッド (各テストが実行される直前で実行されるメソッド)</blockquote>
いまいちわかりずらいのですが前提条件って感じでしょうか。
この中で設定した変数は以降スコープ内のテストケースで使用できる。的な。

で、「@base_title = "Ruby on Rails Tutorial Sample App"」と設定してあるので
「"Home | #{@base_title}"」→変数展開→「"Home | Ruby on Rails Tutorial Sample App"」となる訳です（よね？）

表記は変わっているけどやっている内容は変わらないのでテストはGREEN
<h3>3.4.3 レイアウトと埋め込みRuby (Refactor)</h3>
重複したコードを整理してすっきりさせる
<h4>provideメソッドとは何ぞ</h4>
template（view）間で値を渡すためのメソッド
受け取るメソッドのyieldとセットで使われる
<pre class="lang:ruby mark:(3-3,9-9) decode:true " title="/sample_app/app/views/static_pages/home.html.erb" >#ラベル:title,文字列Homeが引数のprovideメソットを呼び出している
#埋め込みRuby→「<% ・・・ >」で囲まれたコードはHTMLに出力されない
<% provide(:title, "Home") %>
<!DOCTYPE html>
<html>
  <head>
    #引数にラベル:titleが付いたprovideメソッドの文字列をyieldメソッドで参照している
    #埋め込みRuby→「<%= ・・・ >」で囲まれたコードはHTMLに出力される
    <title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>
  </head>
  <body>
    <h1>Sample App</h1>
    <p>
      This is the home page for the
      <a href="https://railstutorial.jp/">Ruby on Rails Tutorial</a>
      sample application.
    </p>
  </body>
</html></pre> 
HelpとAboutのページも同じ様に変更する
<h4>リファクタリングの続き</h4>
それぞれのページの構造がほぼ同じものになっているため更に整理<br>
まず、初めに無効にしていたレイアウト用のファイルを元に戻します
<pre class="lang:default decode:true " title="ターミナル" >>$ mv layout_file app/views/layouts/application.html.erb</pre>
そして編集
 
<pre class="lang:ruby mark:(5-5) decode:true " title="/sample_app/app/views/layouts/application.html.erb" ><!DOCTYPE html>
<html>
  <head>
    #title部分を書き換え
    <title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>
    #セキュリティ関連のrailsのメソッド
    <%= csrf_meta_tags %>
    #スタイルシートを展開するためのコード
    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    #JavaScriptを展開するためのコード
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <%= yield %>
    #各ページの内容を挿入するためのコード
  </body>
</html></pre> 
さらにHome,Help,Aboutの各ページを編集
<pre class="lang:ruby mark:(2-7,14-15) decode:true " title="/sample_app/app/views/static_pages/home.html.erb" ><% provide(:title, "Home") %>
<!DOCTYPE html>
<html>
  <head>
    <title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>
  </head>
  <body>
    <h1>Sample App</h1>
    <p>
      This is the home page for the
      <a href="https://railstutorial.jp/">Ruby on Rails Tutorial</a>
      sample application.
    </p>
  </body>
</html>
#ハイライト行を削除
#Help,Aboutページも同様に</pre> 
テスト結果
<pre class="lang:default decode:true " title="ターミナル" >$ rails t
・
・
・
  3/3: [<span class="green">===================================</span>] 100% Time: 00:00:00, Time: 00:00:00

Finished in 0.33430s
3 tests, 6 assertions, <span class="green">0 failures, 0 errors</span>, 0 skips</pre>
<h4>演習</h4>
テストを書く（現状では<span class="red">RED</span>）
<pre class="lang:ruby decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb
">
・
・
・
  test "should get contact" do
    get static_pages_contact_url
    assert_response :success
    assert_select "title", "Contact | #{@base_title}"
  end

end
</pre>
ルーティング
<pre class="lang:ruby mark:(5-5) decode:true " title="/sample_app/config/routes.rb" >Rails.application.routes.draw do
  get 'static_pages/home'
  get 'static_pages/help'
  get 'static_pages/about'
  get 'static_pages/contact'
  root 'application#hello'
end</pre> 
アクション
<pre class="lang:ruby mark:(4-5) decode:true " title="/sample_app/app/controllers/static_pages_controller.rb
">
・
・
・
  def contact
  end
  
end</pre>
本文に従ってcontact.html.erbを作る →　テスト<span class="green">GREEN</span>
<h3>3.4.4 ルーティングの設定</h3>
本文ママ

但し、ルーティングを書き換えたためstatic_pages_home_urlヘルパーが無効になってテストが通らなくなる
以下の演習で修正
<h4>演習</h4>
<blockquote class="quote-rort">リスト 3.41にrootルーティングを追加したことで、root_urlというRailsヘルパーが使えるようになりました (以前、static_pages_home_urlが使えるようになったときと同じです)。リスト 3.42のFILL_INと記された部分を置き換えて、rootルーティングのテストを書いてみてください。</blockquote>
 
<pre class="lang:ruby mark:(11-12) decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb" >require 'test_helper'
class StaticPagesControllerTest < ActionDispatch::IntegrationTest
  
  def setup
    @base_title = "Ruby on Rails Tutorial Sample App"
  end
  
  test "should get root" do
    get root_url
    assert_response :success
 end
・
・
・</pre> 
<blockquote class="quote-rort">実はリスト 3.41のコードを書いていたので、先ほどの課題のテストは既に green になっているはずです。このような場合、テストを変更する前から成功していたのか、変更した後に成功するようになったのか、判断が難しいです。リスト 3.41のコードがテスト結果に影響を与えていることを確認するため、リスト 3.43のようにrootルーティングをコメントアウトして見て、 red になるかどうか確かめてみましょう (なおRubyのコメント機能については4.2.1で説明します)。最後に、コメントアウトした箇所を元に戻し (すなわちリスト 3.41に戻し)、テストが green になることを確認してみましょう。</blockquote>
該当箇所をコメントアウト　→　<span class="red">RED</span>
元に戻す　→　<span class="green">GREEN</span>
<h3>まとめとか感想</h3>
今更ですがあれですね。
こうやって纏めながらやり直してると理解が深まるし、新しい気付きもあっていいですね。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

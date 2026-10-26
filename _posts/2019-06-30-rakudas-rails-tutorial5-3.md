---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜5.3"
date: "2019-06-30 17:45:32"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 822
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial5-3/"
---
<h2>5.3 レイアウトのリンク</h2>
'#'でダミーリンクにしていた部分を書き換えていく。
<a href="/static_pages/about">About</a>の様に直接パスを指定するのではなく
Railsの慣習に倣った形式で記述していく
→<%= link_to "About", about_path %>
埋め込みRuby　Link_toヘルパー　表示される文字（第一引数）,リンクのパス(第2引数)
<h4>about_pathとは何ぞ</h4>
名前付きルート（○○_path）
config/routes.rbで設定することで使えるようになる
（詳しくは5.3.2、5.3.3）
<h4>名前付きルートのいい所</h4>
Aboutページへのリンクを例に・・・
/static_pages/aboutの様にパスをしているすると、リンク先のパスに変更があった場合にすべての記述個所を修正しないといけない
about_pathで指定していれば、リンク先のパスに変更があってもroutes.rbで該当箇所を修正するだけでOK
<h3>5.3.1 Contactページ</h3>
3.4.3の演習でつくったものがそのまま残っているのでした。
<pre class="lang:ruby decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb
">・
・
・
  test "should get contact" do
    get static_pages_contact_url
    assert_response :success
    #本文のリスト5.21はベースタイトルが文字列だけど、手元のデータは変数が定義されているのでそっちで
    assert_select "title", "Contact | #{@base_title}"
  end

end</pre>
<h3>5.3.2 RailsのルートURL</h3>
ルートURLの様にルーティングを定義するメリット
→名前付きルートを使えるようになる！→メソッドを通してURLを参照できる！
<pre class="lang:ruby decode:true " title="コード解説">#routes.rbにルートURLを定義する
root 'static_pages#home'

#下記の様にメソッドを通して呼び出せるようになる
#ルートULL以下の文字列を返す
root_path -> '/'
#完全なURLを返す
root_url  -> 'https://○○○○○.vfs.cloud9.ap-northeast-1.amazonaws.com/'</pre>
<blockquote class="quote-rort">
なお、Railsチュートリアルでは一般的な規約に従い、基本的には_path書式を使い、リダイレクトの場合のみ_url書式を使うようにします。これはHTTPの標準としては、リダイレクトのときに完全なURLが要求されるためです。
</blockquote>
<h4>静的ページの名前付きルートを定義する</h4>
<pre class="lang:ruby decode:true " title="コード解説">#routes.rbでgetルールを使って定義（helpページの例）
get 'static_pages/help'
#↑これを　こうする↓
get  '/help', to: 'static_pages#help'
#getリクエストが　/helpに送信されたとき,　static_pagesコントローラーの　helpアクションが　呼び出される

#上記を定義することで名前付きルートが使えるようになる
help_path -> '/help'
help_url  -> 'https://○○○○○.vfs.cloud9.ap-northeast-1.amazonaws.com/help'</pre>
上記に倣ってroutes.rbを編集 
static_pages_controller_test.rbの該当箇所も修正
<pre class="lang:ruby decode:true " title="/sample_app/config/routes.rb
">Rails.application.routes.draw do
  root 'static_pages#home'
  get '/help',    to:'static_pages#help'
  get '/about',   to:'static_pages#about'
  get '/contact', to:'static_pages#contact'
end</pre>
<pre class="lang:default mark:(10-10,16-16,2-22,28-28) decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb" >require 'test_helper'

class StaticPagesControllerTest < ActionDispatch::IntegrationTest
  
  def setup  #各テストが実行される直前で実行される
    @base_title = "Ruby on Rails Tutorial Sample App"
  end
  
  test "should get root" do
    get root_path
    assert_response :success
    assert_select "title", "#{@base_title}"
  end

  test "should get help" do
    get help_path
    assert_response :success
    assert_select "title", "Help | #{@base_title}"
  end

  test "should get about" do
    get about_path
    assert_response :success
    assert_select "title", "About | #{@base_title}"
  end
  
  test "should get contact" do
    get contact_path
    assert_response :success
    assert_select "title", "Contact | #{@base_title}"
  end

end</pre> 
尚ベースタイトルが略
<h4>演習</h4>
<ol>
<li>
実は名前付きルートは、as:オプションを使って変更することができます。有名なFar Sideの漫画に倣って、Helpページの名前付きルートをhelfに変更してみてください (リスト 5.29)。
</li>
<li>
先ほどの変更により、テストが redになっていることを確認してください。リスト 5.28を参考にルーティングを更新して、テストを greenにして見てください。
</li>
<li>
エディタのUndo機能を使って、今回の演習で行った変更を元に戻して見てください。
</li>
</ol>
<pre class="lang:ruby mark:(5-5) decode:true " title="/sample_app/config/routes.rb" >#1.
Rails.application.routes.draw do
  root 'static_pages#home
  #:asオプションで指定した文字列で名前付きルートが定義される
  get  '/help',    to: 'static_pages#help', as: 'helf'
  get  '/about',   to: 'static_pages#about'
  get  '/contact', to: 'static_pages#contact'
end</pre> 
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb" >#2.
・
・
・
  test "should get help" do
    get helf_path
    assert_response :success
    assert_select "title", "Help | #{@base_title}"
  end
・
・
・
#3.
#動作確認が出来たらCtrl+zでUndo（win）（routes.rbも）</pre> 
<h3>5.3.3 名前付きルート</h3>
名前付きルートを定義したので
本文に倣ってviewファイルのlink_toメソッドを書き換えていく
<h4>演習</h4>
<ol>
<li>リスト 5.29のようにhelfルーティングを作成し、レイアウトのリンクを更新してみてください。</li>
<li>前回の演習と同様に、エディタのUndo機能を使ってこの演習で行った変更を元に戻してみてください。</li>
</ol>
<pre class="lang:ruby decode:true " title="/sample_app/app/views/layouts/_header.html.erb
">#1.
#ルーティングは前回の演習と同様
・
・
・
      　<ul class="nav navbar-nav navbar-right">
        　<li><%= link_to "Home",   root_path %></li>
          <li><%= link_to "Help",   helf_path %></li>
          <li><%= link_to "Log in", '#' %></li>
        </ul>
・
・
・
</pre>
2.リンクが作動することを確認してUndo
<h3>5.3.4 リンクのテスト</h3>
統合テストを使ってリンクが正しく動いているかをチェックする
<h4>統合テストとは</h4>
アプリケーションの動作を端から端まで (end-to-end) シミュレートしてテストするもの
<pre class="lang:default decode:true " title="ターミナル" >$ rails generate integration_test site_layout
  #統合テストを生成するコマンド　テストの名前
      invoke  test_unit
      create    test/integration/site_layout_test.rb
      #test/integration/にファイルが生成される　指定の文字列の末尾に_testが追加される</pre>
<h4>レイアウトのリンクに対するテスト</h4>
<blockquote class="quote-rort">
<ol>
<li>ルートURL (Homeページ) にGETリクエストを送る.</li>
<li>正しいページテンプレートが描画されているかどうか確かめる.</li>
<li>Home、Help、About、Contactの各ページへのリンクが正しく動くか確かめる.</li>
</ol>
</blockquote>
上記の内容をコード化していく
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/site_layout_test.rb
">require 'test_helper'

class SiteLayoutTest < ActionDispatch::IntegrationTest

  test "layout links" do　　#layout links（テストの名前）
    get root_path                                   #ルートパスにgetのリクエスト
    assert_template 'static_pages/home'             #static_pages/homeが描写されている
    assert_select "a[href=?]", root_path, count: 2  #<a href="/">がある　2か所
    assert_select "a[href=?]", help_path            #<a href="/help">がある
    assert_select "a[href=?]", about_path           #<a href="/about">がある
    assert_select "a[href=?]", contact_path         #<a href="/contact">がある
  end
end</pre>
<h4>assert_selectメソッドとはなんぞ</h4>
様々なオプションを付けることが出来る便利で凄いメソッド（語彙）
<a href="https://railsguides.jp/testing.html#%E3%83%93%E3%83%A5%E3%83%BC%E3%82%92%E3%83%86%E3%82%B9%E3%83%88%E3%81%99%E3%82%8B" rel="noopener noreferrer" target="_blank">RailsGuideの説明ページ</a>
<a href="https://railstutorial.jp/chapters/filling_in_the_layout?version=5.1#table-assert_select" rel="noopener noreferrer" target="_blank">本文にあるassert_selectの使用例</a>
<pre class="lang:ruby decode:true " title="コード解説">assert_select #Railsが "?"にabout_pathを置き換えてくれる
"a[href=?]", about_path
#→<a href="/about"<.../a<と言うHTMLがあるかをチェックしている
#オプションを付けることも出来る（指定のリンクが2つあるかどうかのチェック）
assert_select "a[href=?]", root_path, count: 2
</pre>
<h4>Rakeタスクとは何ぞ</h4>
そもそもRakeとは？→Rubyで書かれたビルドツール（プログラムを制御・統合するもの）
<a href="http://railsdoc.com/rake#Rake%E3%81%A8%E3%81%AF" rel="noopener noreferrer" target="_blank">RailsドキュメントのRakeとは　のページ</a>
Rakeタスクとは→Rakefileというファイルに一連の処理を定義したもの

そういうものがある、と言う事を覚えておく！

統合テストが通るかどうかは下記のコマンド（Rakeタスク）を実行する事で試せる
<pre class="lang:default decode:true " title="ターミナル" >$ rails test:integration</pre>
→統合テストが成功したらすべてのテストが成功するかも確認する

<del datetime="2019-07-03T09:18:51+00:00">通常rake test:integrationで実行するものらしいし、調べた限りではrake test:integrationと出てくるですが
本文ではrails test:integrationなんですよね。
使い分け？とかどうなってるんでしょうか。はて。</del>
→Rails５からrakeタスクをrailsコマンドで実行できるようになったとの事です。
勉強になりました！
[blogcard url="https://techracho.bpsinc.jp/hachi8833/2017_03_01/36329"]
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
footerパーシャルのabout_pathをcontact_pathに変更してみて、テストが正しくエラーを捕まえてくれるかどうか確認してみてください。
</li>
<li>
リスト 5.35で示すように、Applicationヘルパーで使っているfull_titleヘルパーを、test環境でも使えるようにすると便利です。こうしておくと、リスト 5.36のようなコードを使って、正しいタイトルをテストすることができます。ただし、これは完璧なテストではありません。例えばベースタイトルに「Ruby on Rails Tutoial」といった誤字があったとしても、このテストでは発見することができないでしょう。この問題を解決するためには、full_titleヘルパーに対するテストを書く必要があります。そこで、Applicationヘルパーをテストするファイルを作成し、リスト 5.37のFILL_INの部分を適切なコードに置き換えてみてください。ヒント: リスト 5.37ではassert_equal <期待される値>, <実際の値>といった形で使っていましたが、内部では==演算子で期待される値と実際の値を比較し、正しいかどうかのテストをしています。
</li>
</ol>
</blockquote>
1.動作確認のみなので省略
2.↓
<pre class="lang:ruby mark:(8-8) decode:true " title="/sample_app/test/test_helper.rb" >・
・
・
class ActiveSupport::TestCase
  # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
  fixtures :all
  #ApplicationHelperを追加→ApplicationHelperで定義したヘルパーが使えるようになる
  include ApplicationHelper
・
・
・</pre> 
 
<pre class="lang:ruby mark:(6-6,9-9) decode:true " title="/sample_app/test/integration/site_layout_test.rb" data-url="  " >・
・
・
    assert_select "a[href=?]", contact_path
    #contact_pathへgetのリクエスト
    get contact_path
    #<title>Contact | Ruby on Rails Tutorial Sample App</title>がある
    #test_helperでApplicationHelperを使えるように定義した為full_titleが使えるようになっている
    assert_select "title", full_title("Contact")
  end
end</pre> 
/sample_app/app/helpers/application_helper.rbと
/sample_app/test/controllers/static_pages_controller_test.rbの
ベースタイトル部分を<strong>「Ruby on Rails Tutoial」</strong>に変更してテストしてみる→GREEN
誤字が起こっているのにテストがGREEN！→full_titleヘルパーをテストする
<pre class="lang:default mark:(7-7,9-9) decode:true " title="/sample_app/test/helpers/application_helper_test.rb" >#testのフォルダ の中の helperのテストのフォルダ に application_helperのテストのファイル を作る
require 'test_helper'

class ApplicationHelperTest < ActionView::TestCase
  test "full title helper" do
    #full_title == "Ruby on Rails Tutorial Sample App"の意味
    assert_equal full_title,         "Ruby on Rails Tutorial Sample App"
    #full_title("Help") == "Help | Ruby on Rails Tutorial Sample App"の意味
    assert_equal full_title("Help"), "Help | Ruby on Rails Tutorial Sample App"
  end
end</pre> 
→RED
エラーメッセージに沿って修正
<h4>assert_equalとはなんぞ</h4>
<a href="http://railsdoc.com/test" rel="noopener noreferrer" target="_blank">ユニットテストで使うメソッド</a>（Railsドキュメント）
assert_equal(値1, 値2)　→値1と値2が等しければ成功
値には変数も入る
<h3>まとめとか感想</h3>
名前付きルートはとても便利。

手元のコードと本文のコードで
「あれ？前回やった所はどこへ行ったの？？」みたいなことがちょこちょこ起こるんだけど
なるべく手元のコードを優先で対応していけるようにしたい。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

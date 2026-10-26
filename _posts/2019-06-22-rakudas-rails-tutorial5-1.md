---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜5.1"
date: "2019-06-22 21:35:32"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 787
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial5-1/"
---
5章でやる事
<ul>
<li>Bootstrap</li>
<li>Sass</li>
<li>静的ページをレイアウトに追加</li>
<li>ログイン機能のはじめの一歩</li>
</ul>
HTML/CSSについては分かってる部分はあまり深堀しない感じで
<h2>5.1 構造を追加する</h2>
<ul>
<li>サイトロゴ、ナビゲーションヘッダー、サイトフッターを含む静的ページを開発していく</li>
<li>モックアップ（＝ワイヤーフレーム)を参照しながらviewを整えていく</li>
<li>作業ブランチの作成　→$ git checkout -b filling-in-layout</li>
<li>レイアウトファイルの更新（クラスの多くはBootstrap特有の物・<a href="https://railstutorial.jp/chapters/filling_in_the_layout?version=5.1#sec-custom_css" rel="noopener noreferrer" target="_blank">本文5.1.2</a>に詳細アリ）</li>
</ul>
<h3>5.1.1 ナビゲーション</h3>
<pre class="lang:ruby mark:(9-10) decode:true " title="/sample_app/app/views/layouts/application.html.erb" ><!DOCTYPE html>
<html>
  <head>
    <title><%= full_title(yield(:title)) %></title>  #full_titleメソッド
    <%= csrf_meta_tags %>
    <%= stylesheet_link_tag    'application', media: 'all',
                               'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application',
                               'data-turbolinks-track': 'reload' %>
    #IEのバージョンが9より小さい時にスクリプトを実行する設定の条件付きコメント
    <!--[if lt IE 9]>
      <script src="//cdnjs.cloudflare.com/ajax/libs/html5shiv/r29/html5.min.js">
      </script>
    <![endif]-->
  </head>
  <body>
    <header class="navbar navbar-fixed-top navbar-inverse">
      <div class="container">
        <%= link_to "sample app", '#', id: "logo" %>　　#link_toメソッド（第3引数はidを追加するオプション）
        <nav>
          <ul class="nav navbar-nav navbar-right">
            <li><%= link_to "Home",   '#' %></li>  #link_toメソッド
            <li><%= link_to "Help",   '#' %></li>  #link_toメソッド
            <li><%= link_to "Log in", '#' %></li>  #link_toメソッド
          </ul>
        </nav>
      </div>
    </header>
    <div class="container">  #メインコンテンツ用のdiv
      <%= yield %>           #yieldメソッド（ページごとの内容が挿入される）
    </div>
  </body>
</html></pre> 
サインアップページへのリンクなどを含めたhome.html.erb
<pre class="lang:ruby decode:true " title="/sample_app/app/views/static_pages/home.html.erb
"><div class="center jumbotron">
  <h1>Welcome to the Sample App</h1>

  <h2>
    This is the home page for the
    <a href="https://railstutorial.jp/">Ruby on Rails Tutorial</a>
    sample application.
  </h2>

  <%= link_to "Sign up now!", '#', class: "btn btn-lg btn-primary" %>
</div>　　#サインアップページへのダミーリンク

<%= link_to image_tag("rails.png", alt: "Rails logo"),　　#link_toメソッドの第一引数をimage_tagで画像にしている
            'http://rubyonrails.org/' %></pre>
<h4>link_toヘルパーとは何ぞ（2019.06.25追記）</h4>
リンクを生成するヘルパーメソッド
link_to(文字列, パス [, オプション, HTMLオプション])
→文字列の部分をimage_tagヘルパーにすることで画像のリンクを生成できる
<h4>image_tagヘルパーとは何ぞ</h4>
Imageタグを生成するヘルパーメソッド
→image_tag(画像ファイルへのパス, [, (オプション or HTMLオプション)])
ロゴの画像ファイルをapp/assets/images/に設置
<pre class="lang:default decode:true " title="ターミナル" >#cURL（client for URL）→URLで示される場所からデータを送受信できるコマンド（ざっくり）
#DLしたデータをファイルとして保存→保存先　URLの内容をそのままファイルとしてDL（リダイレクトされてたら追う）→DL元
curl -o app/assets/images/rails.png -OL railstutorial.jp/rails.png　</pre>
 
<pre class="lang:ruby decode:true " title="コード解説" ><%= link_to image_tag("rails.png", alt: "Rails logo"),  #画像ファイルへのパス　alt属性
            'http://rubyonrails.org/' %>
↑これがHTMLに出力されるとこうなる↓
<img alt="Rails logo" src="/assets/rails-c094bc3a4bf50e5bb477109e5cb0d213af27ad75b481c4df249f50974dbeefe6.png">
#謎の文字列
#→データが更新されると文字列も変わる
#→文字列を元にキャッシュを読み込むかデータを読み込むか判断する
#imagesディレクトリが省略されている？
#→/assets/imagesと/assetsが紐づけられている為呼び出せる（高速化のための仕組み）（ざっくり）
</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
Webページと言ったらネコ画像、というぐらいにはWebにはネコ画像が溢れていますよね。リスト 5.4のコマンドを使って、図 5.3のネコ画像をダウンロードしてきましょう。
</li>
<li>
mvコマンドを使って、ダウンロードしたkitten.jpgファイルを適切なアセットディレクトリに移動してください (参考: 5.2.1)。</li>
<li>
image_tagを使って、kitten.jpg画像を表示してみてください (図 5.4)。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル" >#1.
$ curl -OL cdn.learnenough.com/kitten.jpg
#保存先を指定していないのでルート直下に元のファイル名で保存される
#2.
$ mv kitten.jpg app/assets/images/kitten.jpg
#移動　移動するデータのパス　移動先のデータのパス
</pre>
 
<pre class="lang:ruby mark:(7-7) decode:true " title="/sample_app/app/views/static_pages/home.html.erb" >・
・
・
#3.
<%= link_to image_tag("rails.png", alt: "Rails logo"),
            'http://rubyonrails.org/' %>
<%= image_tag("kitten.jpg", alt: "Kitten Photo" ) %></pre> 
※この演習での内容は後で使いますが、5.1.2で本文と表示が違って落ち着かないのでコメントアウトで
<h3>5.1.2 BootstrapとカスタムCSS</h3>
Bootstrapを使えるようにする
　→Gemfileを編集し、bundle install
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/Gemfile
" >source 'https://rubygems.org'

gem 'rails',          '5.1.6'
gem 'bootstrap-sass', '3.3.7'　　#←追加
・
・
・</pre>
<pre class="lang:default decode:true " title="ターミナル" >$ bundle install

#controllerごとのCSSファイルが自動生成されているが、全てのスタイルをまとめて管理した方が分かりやすい！
#なので共通のCSSのファイルを作る↓
$ touch app/assets/stylesheets/custom.scss
</pre><pre class="lang:ruby decode:true " title="/sample_app/app/assets/stylesheets/custom.scss
">#Bootstrap (とSprockets)を読み込む
#これによってクラスにBootstrapで使用されているクラス名を使用した部分にスタイルが適用される
@import "bootstrap-sprockets";
@import "bootstrap";</pre>
<a href="https://rakuda3desu.net/wp-content/uploads/2019/06/5.1.1-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/06/5.1.1-1-300x152.png" alt="" width="300" height="152" class="alignnone size-medium wp-image-794" /></a>
↑これが
　こうなる↓
<a href="https://rakuda3desu.net/wp-content/uploads/2019/06/5.1.2-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/06/5.1.2-1-300x129.png" alt="" width="300" height="129" class="alignnone size-medium wp-image-795" /></a>
更にcustom.scssを編集し、すべてのページに適用される共通のスタイルを追加する

 
<pre class="lang:sass decode:true " title="/sample_app/app/assets/stylesheets/custom.scss" >@import "bootstrap-sprockets";
@import "bootstrap";

/* universal */

body {
  padding-top: 60px;
}

section {
  overflow: auto;
}

textarea {
  resize: vertical;
}

.center {
  text-align: center;
}

.center h1 {
  margin-bottom: 10px;
}</pre> 
↓こうなる
<a href="https://rakuda3desu.net/wp-content/uploads/2019/06/5.1.2-2.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/06/5.1.2-2-300x140.png" alt="" width="300" height="140" class="alignnone size-medium wp-image-796" /></a>

以下本文通まま
CSSの解説やスタイルの追加
<h4>演習</h4>
動作確認のみなので省略ー
1は5.1.1の演習直後にコメントアウトしているので
2の方は確認後削除で
<h3>5.1.3 パーシャル (partial)</h3>
<ul>
<li>同じようなコードをまとめる事が出来る</li>
<li>ファイル名はアンダースコアから始める（_shim.html.erbなど）</li>
<li>レンダーメソッドで呼びだす</li>
</ul>
<pre class="lang:ruby mark:(10-10, decode:true " title="/sample_app/app/views/layouts/application.html.erb" ><!DOCTYPE html>
<html>
  <head>
    <title><%= full_title(yield(:title)) %></title>
    <%= csrf_meta_tags %>
    <%= stylesheet_link_tag    'application', media: 'all',
                               'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application',
                               'data-turbolinks-track': 'reload' %>
    <%= render 'layouts/shim' %>   #IE用の条件付きコメント部分のパーシャル
  </head>
  <body>
    <%= render 'layouts/header' %>  #header部分のパーシャル13-13)
    <div class="container">
      <%= yield %>
    </div>
  </body>
</html></pre> 
renderメソッドで呼び出したいファイルを指定
「<%= render 'layouts/shim' %>」であれば
「app/views/layouts/_shim.html.erb」を探して、その内容をビューに挿入する

呼び出されるファイルはそれぞれこちら
<pre class="lang:default decode:true " title="ターミナル" >#app/views/layouts/に_shim.html.erbと_header.html.erbを作る
$ touch app/views/layouts/_shim.html.erb
$ touch app/views/layouts/_header.html.erb</pre>
<pre class="lang:ruby decode:true " title="/sample_app/app/views/layouts/_shim.html.erb"><!--[if lt IE 9]>
  <script src="//cdnjs.cloudflare.com/ajax/libs/html5shiv/r29/html5.min.js">
  </script>
<![endif]--></pre>
<pre class="lang:ruby decode:true " title="/sample_app/app/views/layouts/_header.html.erb
"><header class="navbar navbar-fixed-top navbar-inverse">
  <div class="container">
    <%= link_to "sample app", '#', id: "logo" %>
    <nav>
      <ul class="nav navbar-nav navbar-right">
        <li><%= link_to "Home",   '#' %></li>
        <li><%= link_to "Help",   '#' %></li>
        <li><%= link_to "Log in", '#' %></li>
      </ul>
    </nav>
  </div>
</header></pre>
多分説明のために「呼び出し元のファイルを編集→パーシャルのファイルを作る」になってるけど
実際の作業では「パーシャルのファイルを作る→（コードを移す→）レンダーメソッドを挿入」ですよね。

って事でfooterのパーシャルを作成
（ファイルは自動生成しない。好みの方法で手動で作る)
<pre class="lang:ruby decode:true " title="/sample_app/app/views/layouts/_footer.html.erb
">><footer class="footer">
  <small>
    The <a href="https://railstutorial.jp/">Ruby on Rails Tutorial</a>
    by <a href="http://www.michaelhartl.com/">Michael Hartl</a>
  </small>
  <nav>
    <ul>
      <li><%= link_to "About",   '#' %></li>
      <li><%= link_to "Contact", '#' %></li>
      <li><a href="http://news.railstutorial.org/">News</a></li>
    </ul>
  </nav>
</footer></pre>
<pre class="lang:default mark:(4-4) decode:true " title="/sample_app/app/views/layouts/application.html.erb" >#レイアウトにfooterのレンダーメソッドを追加
    <div class="container">
      <%= yield %>
      <%= render 'layouts/footer' %>
    </div>
  </body>
</html></pre> 
本文に従ってCSSも追加
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
Railsがデフォルトで生成するheadタグの部分を、リスト 5.18のようにrenderに置き換えてみてください。ヒント: 単純に削除してしまうと後でパーシャルを１から書き直す必要が出てくるので、削除する前にどこかに退避しておきましょう。
</li>
<li>
リスト 5.18のようなパーシャルはまだ作っていないので、現時点ではテストは redになっているはずです。実際にテストを実行して確認してみましょう。
</li>
<li>
layoutsディレクトリにheadタグ用のパーシャルを作成し、先ほど退避しておいたコードを書き込み、最後にテストが green に戻ることを確認しましょう。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル" >#1.
退避用にファイルを作っておく（後でファイル名を変えてパーシャルにする）
$ touch app/views/layouts/head</pre>
 
<pre class="lang:ruby mark:(5-9,12-12) decode:true " title="/sample_app/app/views/layouts/application.html.erb" ><!DOCTYPE html>
<html>
  <head>
    <title><%= full_title(yield(:title)) %></title>
    <%= csrf_meta_tags %>
    <%= stylesheet_link_tag    'application', media: 'all',
                               'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application',
                               'data-turbolinks-track': 'reload' %>
    #↑これを切り取ってheadファイルにペースト
    #↓こちらを追加
    <%= render 'layouts/rails_default' %>
    <%= render 'layouts/shim' %>
  </head></pre> 
<pre class="lang:default decode:true " title="ターミナル（guard）
" >・
・
・
ERROR["test_should_get_root", StaticPagesControllerTest, 5.178689764999945]
 test_should_get_root#StaticPagesControllerTest (5.18s)
ActionView::Template::Error:         ActionView::Template::Error: Missing partial layouts/_rails_default with {:locale=>[:en], :formats=>[:html], :variants=>[], :handlers=>[:raw, :erb, :html, :builder, :ruby, :coffee, :jbuilder]}. Searched in:
          * "/home/ec2-user/environment/sample_app/app/views"
        
            app/views/layouts/application.html.erb:5:in`_app_views_layouts_application_html_erb__4125667178896645475_25573720'
            test/controllers/static_pages_controller_test.rb:10:in `block in <class:StaticPagesControllerTest>'
・
・
・
#テンプレートのエラー　パーシャルが見つからないなど書いてある
#正に<%= render 'layouts/rails_default' %の行がおかしいと書いてある</pre>
<pre class="lang:default decode:true " title="ターミナル" >#ファイル名を変更
$ mv app/views/layouts/head app/views/layouts/_rails_default.html.erb
#テストがGREENになって表示もOK</pre>
<h3>まとめとか感想</h3>
やっぱり動いてるものを作るのは楽しいですね✨
パーシャルはリファクタリングをしていく上で重要なものになっていくと思うのでしっかり身に付けたい
っつーかなんでもしっかり身に付けたい訳なんですが。はい。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

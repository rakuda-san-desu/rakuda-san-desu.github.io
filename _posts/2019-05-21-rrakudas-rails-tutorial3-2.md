---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜3.2"
date: "2019-05-21 14:32:11"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 667
wordpress_status: "publish"
permalink: "/rrakudas-rails-tutorial3-2/"
---
<h3>3.6.1 minitest reporters</h3>
2019.05.24　<a href="https://rakuda3desu.net/rakudas-rails-tutorial3-3/">らくだ🐫にもできるRailsチュートリアル｜3.3</a>から移動

masterブランチから移動する前に
テスト結果を見易くするために<a href="https://railstutorial.jp/chapters/static_pages?version=5.1#sec-minitest_reporters" rel="noopener noreferrer" target="_blank">3.6.1</a>に書いてあるこちらの設定をしておきます
（ブランチを移動しているようでしたらmasterブランチにcheckoutしておいてください）
<pre class="lang:ruby mark:(5-6) decode:true " title="/sample_app/test/test_helper.rb" >ENV['RAILS_ENV'] ||= 'test'
require File.expand_path('../../config/environment', __FILE__)
require 'rails/test_help'
#下記の2行を追加します
require "minitest/reporters"
Minitest::Reporters.use!

class ActiveSupport::TestCase
  # Setup all fixtures in test/fixtures/*.yml for all tests in alphabetical order.
  fixtures :all

  # Add more helper methods to be used by all tests here...
end</pre> 
テスト結果に色が付くので見やすくなります
<h2>3.2 静的ページ</h2>
新しくブランチを作って作業していきます
（ところでブランチって作る？切る？←多分どっちでも）
<pre class="lang:default decode:true " title="ターミナル" >(master) $ git checkout -b static-pages
Switched to a new branch 'static-pages'
(static-pages) $ 
#cloud9のターミナルだと（）に現在のブランチ名が表示されていて便利</pre>
<h3>3.2.1 静的なページの生成</h3>
<h4>キャメルケースとは</h4>
CamelCase　スペースを詰めて単語の頭文字を大文字にする表記<br>
因みにスネークケース（snake_case）はスペースをアンダースコアに置き換えた表記<br>

controller名はキャメルケースで表記
<pre class="lang:default decode:true " title="ターミナル" >#homeアクションと helpアクションを持った StaticPagesコントローラを作成
$ rails generate controller StaticPages home help
Running via Spring preloader in process 11965
#以下自動生成されたファイルやコード
      create  app/controllers/static_pages_controller.rb
       route  get 'static_pages/help'
       route  get 'static_pages/home'
      invoke  erb
      create    app/views/static_pages
      create    app/views/static_pages/home.html.erb
      create    app/views/static_pages/help.html.erb
      invoke  test_unit
      create    test/controllers/static_pages_controller_test.rb
      invoke  helper
      create    app/helpers/static_pages_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/static_pages.coffee
      invoke    scss
      create      app/assets/stylesheets/static_pages.scss</pre>
Gitリポジトリに追加
<pre class="lang:default decode:true " title="ターミナル" >$ git add -A
$ git commit -m "Add a Static Pages controller"
#トピックブランチ（static-pages）をプッシュ　以降git pushでOK
$ git push -u origin static-pages</pre>
<h4>自動生成されたファイルやコードの取り消し（削除）</h4>
自動生成されたファイルやコードは多岐にわたっているので
取り消す際は手動で削除するのではなくコマンドで削除する
<pre class="lang:default decode:true " title="ターミナル" >#「generate」←生成・削除→「destroy」
$ rails generate controller StaticPages home help
$ rails destroy  controller StaticPages home help

#modelの自動生成を取り消す場合の引数は不要
$ rails generate model User name:string email:string
$ rails destroy model User

$ rails db:migrate
$ rails db:rollback  #直前の変更を取り消す場合
$ rails db:migrate VERSION=0  #指定したversionまで戻る（0は最初の状態まで)</pre> 
<h4>home アクションと helpアクション</h4>
<pre class="lang:ruby mark:(3-3-,5-5) decode:true " title="/sample_app/config/routes.rb" >Rails.application.routes.draw do
  #/static_pages/homeにアクセスがあった場合StaticPagesコントローラーのhomeアクションを実行
  get  'static_pages/home'
  #/static_pages/helpにアクセスがあった場合StaticPagesコントローラーのhelpアクションを実行
  get  'static_pages/help'
  root 'application#hello'
end</pre> 
上記のルーティングに対応するコントローラーとアクションはこちら↓
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/static_pages_controller.rb
">class StaticPagesController < ApplicationController
  def home
    #railsではアクションの中身が空白の場合、単に対応するviewを返す
    #対応するviewはこちら→/sample_app/app/views/static_pages
  end

  def help
  end
end</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
Fooというコントローラを生成し、その中にbarとbazアクションを追加してみてください。
コラム 3.1で紹介したテクニックを駆使して、Fooコントローラとそれに関連するアクションを削除してみてください。
</blockquote>
<pre class="lang:default decode:true " title="ターミナル" >>$ rails g controller Foo bar baz
Running via Spring preloader in process 17110
#以下、自動生成されたファイルやコード
      create  app/controllers/foo_controller.rb
       route  get 'foo/baz'
       route  get 'foo/bar'
      invoke  erb
      create    app/views/foo
      create    app/views/foo/bar.html.erb
      create    app/views/foo/baz.html.erb
      invoke  test_unit
      create    test/controllers/foo_controller_test.rb
      invoke  helper
      create    app/helpers/foo_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/foo.coffee
      invoke    scss
      create      app/assets/stylesheets/foo.scss
#controllerやviewファイル、routesの中身もチェック
$ rails d controller Foo bar baz
Running via Spring preloader in process 17203
      #以下、削除されたファイルやコード
      remove  app/controllers/foo_controller.rb
       route  get 'foo/baz'
       route  get 'foo/bar'
      invoke  erb
      remove    app/views/foo
      remove    app/views/foo/bar.html.erb
      remove    app/views/foo/baz.html.erb
      invoke  test_unit
      remove    test/controllers/foo_controller_test.rb
      invoke  helper
      remove    app/helpers/foo_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      remove      app/assets/javascripts/foo.coffee
      invoke    scss
      remove      app/assets/stylesheets/foo.scss</pre>
<h3>3.2.2 静的なページの調整</h3>
本文ママ
<h3>まとめとか感想</h3>
次からテストなので嵐の前の静けさ感ある
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

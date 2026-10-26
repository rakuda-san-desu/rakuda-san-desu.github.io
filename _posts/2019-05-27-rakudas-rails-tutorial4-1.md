---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜4.1"
date: "2019-05-27 18:01:37"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 728
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial4-1/"
---
Railsで重要なRubyの知識。的な事。

あ、せっかく3章でテストを勉強したことですし
本文にテスト結果の表示がある場合、合わせて表示させていきます

<h2>4.1 動機</h2>
Rubyの知識があった方が理解も深まるし作業効率も上がるのだ。的な事。

ブランチを作る
<pre class="lang:default decode:true " title="ターミナル" >(master) $ git checkout -b rails-flavored-ruby
Switched to a new branch 'rails-flavored-ruby'
(rails-flavored-ruby) $ </pre>
<h3>4.1.1 組み込みヘルパー</h3>
<h4>組み込みヘルパーとはなんぞ</h4>
初めから用意されている（Railsに<strong>組み込まれている</strong>）便利な部品・仕組み→組み込み関数・ビルドイン関数
viewに呼び出して<del datetime="2019-05-28T06:16:30+00:00">（組み込んで）</del>使う
<h4>stylesheet_link_tag </h4>
引数として指定されたスタイルシートを使えるようにする（ざっくり）
詳しくは　→　<a href="https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-stylesheet_link_tag">Rails API</a>
<pre class="lang:ruby decode:true " title="コードの例">#スタイルシートへのリンクタグを作る　リンクするのはapplication.css メディアタイプはすべてに対応
<%= stylesheet_link_tag 'application', media: 'all',
                                       'data-turbolinks-track': 'reload' %>
　　　　　　　　　　　　　　　　　　　　　　#必要に応じてりリロードする設定</pre>
この中にあるRubyの概念
「Railsの組み込み関数」「カッコを使わないメソッド呼び出し」「シンボル」「ハッシュ」について学んでいく等する
<h4>メディアタイプとは何ぞ</h4>
<a href="https://www.w3.org/TR/CSS2/media.html#media-sheets" rel="noopener noreferrer" target="_blank">メディアタイプ</a>　→　画面・印刷などの出力先
<h3>4.1.2 カスタムヘルパー</h3>
Railsのviewでは、元々用意されている組み込み関数を使えるだけでなく
自分で関数（メソッド）を作って使うことも出来る　→　カスタムヘルパー

3章で作ったタイトル表示に手を加えて
Home,Aboutなどのページタイトルがついていない場合に
「| Ruby on Rails Tutorial Sample App」ではなく
「Ruby on Rails Tutorial Sample App」と表示されるようにしていく
<pre class="lang:ruby decode:true " title="/sample_app/app/views/layouts/application.html.erb
">module ApplicationHelper
    
  # ページごとの完全なタイトルを返します。
  <del datetime="2019-05-29T07:49:21+00:00">#関数full_title（page_titleにそれぞれのページのtitleを代入したものを引数に設定）</del>
  #関数full_title（page_title = ''をデフォルト値に設定）
  def full_title(page_title = '')
    #base_titleに文字列Ruby on Rails Tutorial Sample Appを代入
    base_title = "Ruby on Rails Tutorial Sample App"
    #page_titleが空っぽの場合
    if page_title.empty?
      #base_title（Ruby on Rails Tutorial Sample App）を返す
      base_title
    #そうでない場合（page_titleが存在する場合）
    else
      #page_title | base_title(「About ｜ Ruby on Rails Tutorial Sample App」のような形)
      page_title + " | " + base_title
    end 
  end
  
end</pre>
作ったヘルパーを利用してapplication.html.erbをリファクタリング（！）
 
<pre class="lang:ruby mark:(5-5) decode:true " title="/sample_app/app/views/layouts/application.html.erb" ><!DOCTYPE html>
<html>
  <head>
    #full_titleヘルパーを呼び出す
    <title><%= full_title(yield(:title)) %></title>
    <%= csrf_meta_tags %>
・
・
・
</pre> 
さらにルートで表示されるHomeページのタイトルに「Home」の文字を表示させないようにする
まずはテストの"should get root"のスコープを編集
<pre class="lang:ruby mark:(6-8,12-12,17-17) decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb" >require 'test_helper'

class StaticPagesControllerTest < ActionDispatch::IntegrationTest
  
　#本文では表記が外れてますがせっかくなので生かしていきたい
  def setup
    @base_title = "Ruby on Rails Tutorial Sample App"
  end
  
  #3.4.4の演習で"should get root"に直したものが本文では"should get home"に戻っている
  #routes.rbも編集した関係から3.4.4の演習作った"should get root"のテスコスコープを生かす形で
  test "should get root" do
    get root_url
    assert_response :success
    #本文ではassert_select "title", "Ruby on Rails Tutorial Sample App"
    #せっかく設定したので@base_titleを変数展開する形で
    assert_select "title", "#{@base_title}"
  end
#以下のテストケースも本文では変数展開ではなくRuby on Rails Tutorial Sample App表記になっていますが
#わざわざ書き直さず変数展開な表記のままで
・
・
・</pre> 
テストが成功する様にhome.html.erbを編集
<pre class="lang:ruby mark:(2-2) decode:true " title="/sample_app/app/views/static_pages/home.html.erb GREEN" >#下の削除
<% provide(:title, "Home") %>
    <h1>Sample App</h1>
<p>
  This is the home page for the
  <a href="https://railstutorial.jp/">Ruby on Rails Tutorial</a>
  sample application.
</p></pre> 
<h3>まとめとか感想</h3>
後から修正するのはいつもの事（！）なんですが
特にカスタムヘルパーのコードの解釈は自信がないので後から修正する可能性が大ですね🐫💦
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

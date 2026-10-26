---
layout: post
title: "Rails｜ Ruby on Rails5道場コースⅣ｜9/14でドハマリした話"
date: "2019-02-20 13:22:26"
categories: ["Progate", "Ruby on Rails", "学習メモ", "未分類"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 333
wordpress_status: "publish"
permalink: "/rails-ruby-on-rails5-doujo4-9p-error/"
---
<a href="https://rakuda3desu.net/rails-ruby-on-rails5-11-win-bcrypt-error/">前回からの続き</a>
<p>考えながらとは言え、ノートを首っ引き状態でそこまで躓かない道場コースです。<br>
（書き写しのミスでエラーとかはある🐫ﾃﾍﾍﾟﾛ）</p>
<p>そんな中、なかなかのドハマリを致しましたので書いておきます<br>
<p>解決してみれば簡単な事ですが、ハマってる時はなかなか気づかないモノですね……</p>

<h2>9/14　いいね!ボタンを作ろう　でハマる</h2>
<p>ハマったのはこちら。<br>
Rails道場コースⅣの9/14での事でした</p>
<p>前項で作ったlikes_contorollerに<br>
createとdestroy、2つのアクションの中身を作るやつです</p>
<p>指示に従いつつ、ノートを見返しつつ<br>
正直そんなに難しい印象もなく作業してブラウザタブをクリック</p>
<p>うむ。エラーです。</p>

<h3>潰せたエラーと潰せないエラー</h3>
<p>始め出ていたのはコード中のスペルミスとか閉じ忘れとか<br>
見慣れてきた感があるのでエラー文を見ながらどこが間違えてるのか探せました</p>
<p>そしてハマったのがこちら</p>
<strong><p>Routing Error<br>
undefined method `before_action' for main:Object<br>
Rails.root: /home/progate/tweet_app</p>
<p>Application Trace | Framework Trace | Full Trace<br>
app/controllers/likes_controller.rb:2:in `<top (required)>'</P></strong>
<p>グーグル翻訳に突っ込みます<br>
<strong>main：Objectに対する未定義のメソッド `before_action '</strong></p>
<p>before_action :authenticate_userのスペルをチェックしてみたり<br>
リセットしてやり直してみたり<br>
サイトごと再読み込みしてやり直してみたり（それで行けることもある）</p>
<p>9/14で触った所をくまなく見直してみたのだけど分からず<br>
もう一度エラー文を見直して見る</p>
<h3>likes_controller.rb:2:in</h3>
<p>likes_contorollerの2行目……</p>
<p>今回の私のコード<br>
 before_action :authenticate_user<br>

  def create<br>
  @like = Like.new(<br>
    user_id: @current_user.id,<br>
    （後略）</p>
<p>学習コースをやりながら作ったローカルのコード<br>
class LikesController < ApplicationController<br>
  before_action :authenticate_user<br>

  def create<br>
  @like = Like.new(<br>
    user_id: @current_user.id,<br>
     （後略）<br>
end</p>
<p><strong>ApplicationControllerが継承されていないね！</strong></p>
<h2>原因</h2>
<p>原因はホント単純かつ基本的な事なんですが<br>
前項（8/14）でlikes_controllerを作った時に<br>
<strong>ターミナルでコマンドを打ち込む</strong>のではなく<br>
viewファイルと同じく<strong>フォルダで右クリックで作っちゃっていた</strong>せいでした</p>
<p>その時からクラスの継承のコードはなかったのだけど<br>
動作確認がなかったせいもあってエラーにならずチェックを通っちゃってたみたいです</p>
<h2>結論</h2>
<p>エラーを探すときはエラー文をよく読もう（声を大にして言う程の事でも……💧）</p>
<p>基本中の基本動作と思うので、身に付くまでモニタに付箋とか貼っておきたい</p>

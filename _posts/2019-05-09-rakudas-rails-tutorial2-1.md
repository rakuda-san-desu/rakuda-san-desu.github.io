---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜2.1"
date: "2019-05-09 20:31:00"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 608
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial2-1/"
---
<h4>scaffoldジェネレータとは何ぞ？</h4>
ざっくり言うと基本的な機能を自動生成してくれるスクリプト
<h4>RESTアーキテクチャとは何ぞ？</h4>
[blogcard url="https://ja.wikipedia.org/wiki/Representational_State_Transfer"]
分かるようなわからない様な？
一先ず進めていきます。
<h2>2.1 アプリケーションの計画</h2>
Gemfileのbundle installでエラーが出てbundle updateをする以外は本文の通りに
リモートリポジトリをGitHubに作る以外も本文の通りに
1章でやった事と同じなので特に迷いもなく。
<h3>2.1.1 ユーザーのモデル設計</h3>
usersモデル
（カラム名）：（データ型）
id:integer　数列（整数）
name：string　文字列
email：string　文字列
<h3>2.1.2 マイクロポストのモデル設計</h3>
micropostsモデル
（カラム名）：（データ型）
id：integer　数列（整数）
content：text　文字列
user_id：integer　数列（整数）
<h4>string形とtext形の違い</h4>
ざっくりと
string形：文字情報・短文　名前やemail等
text形：文章情報・長文　投稿内容等

短いですがキリがいいので区切りますー
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

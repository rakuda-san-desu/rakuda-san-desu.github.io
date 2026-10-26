---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜1.1と1.2"
date: "2019-05-03 18:20:30"
categories: ["Railsチュートリアル", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 511
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial1-1-1-2/"
---
「手を動かしながら読む」と言った感じで進めたRailsチュートリアルの1週目。学びも沢山あったけど、わからないままの所も沢山残っております。

と、言う訳で2週目。

今回は細かく調べつつ、記録しつつ、あとで見直したいときにサクっと情報を取り出せるように纏めたいなって思っています。

「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです
<h2>1.1 はじめに</h2>
<h3>1.1.1 前提知識</h3>
おすすめされている<a href="https://prog-8.com/" target="_blank" rel="noopener noreferrer">Progate</a>の学習コースは終了済
<h3>演習</h3>
検索検索ぅ
<h3>1.1.2 この本における取り決め</h3>
コードの記述、テストの結果は本文を参考に書いていきたいと思います
<h2>1.2 さっそく動かす</h2>
<h3>1.2.1 開発環境</h3>
1週目をcloud9で行ったので2週目はローカルで！と意気込んでいたりもしたのですが
win固有の環境問題と言うヤツに勝てなかったのでcloud9で行います。
<h3>1.2.2 Railsをインストールする</h3>
<pre class="lang:default decode:true " title="ターミナル" >$ printf "install: --no-rdoc --no-ri\nupdate:  --no-rdoc --no-ri\n" >> ~/.gemrc
#この文字列を出力→　install: --no-rdoc --no-ri\nupdate:  --no-rdoc --no-ri\n
#出力先は~/に.gemrcと言うファイルをる</pre> 
.gemrcって言うのはgemの設定ファイルで
この設定は「gemのインストールやアップデートの際にドキュメントファイル生成しない」的な奴
ドキュメントとは何ぞと言うと、イメージしか把握できませんでしたが説明書？設計書？でいいのかな？
で、Gemを使うに当たって絶対必要ではないし、サイズも大きくなるし時間もかかるからスルーします、的な。
（追々は読めるようになったら良いのだろうな、と思います）

ただこれ、現在は書式が違うのだそうです。
<a href="https://guides.rubygems.org/command-reference/#gem-install" target="_blank" rel="noopener noreferrer">gemの公式サイト</a>によると現在は下記の様式になるようです。
<pre class="lang:default decode:true " title="ターミナル" >$ printf "install: --no-document\nupdate: --no-document\n" >> ~/.gemrc
#もしくはこちら→ $ printf "install: -N\nupdate: -N\n" >> ~/.gemrc</pre>
Railsチュートリアルは基本的に書いてある通りに行うのが良いのだと思うのだけど、公式がそう言っていると言う事でこの書式でやってみたいと思います（問題が起こる様ならそれはその時）
因みにファイルの中身はこのようになっております
<pre class="lang:default decode:true " title="/.gemrc" >install: --no-document
update: --no-document
</pre>
ファイルの場所はこちら
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/1.2.2-1.png"><img class="size-medium wp-image-512 alignnone" src="https://rakuda3desu.net/wp-content/uploads/2019/05/1.2.2-1-281x300.png" alt="" width="281" height="300" /></a>
<h4>リスト 1.1: バージョンを指定してRailsをインストールする</h4>
<pre class="lang:default decode:true " title="ターミナル" >$ gem install rails -v 5.1.6
$ rails -v
#railsのバージョンを確認
Rails 5.1.6
$ ruby -v
#ついでにrubyのバージョンも確認
ruby 2.6.0p0 (2018-12-25 revision 66547) [x86_64-linux]
</pre>

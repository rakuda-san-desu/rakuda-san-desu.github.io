---
layout: post
title: "HTML&CSS自主練｜メモ｜マーク付きのボタン"
date: "2018-09-27 16:45:10"
categories: ["HTML&CSS", "学習メモ"]
tags: ["参考サイト", "模写", "自主練"]
published: true
wordpress_id: 37
wordpress_status: "publish"
permalink: "/html-css-voluntary-training-memo-button-with-mark/"
---
<h3>上手く出来なかったマーク付きのボタン</h3>
<h4>見本のボタン</h4>
<img class="hatena-fotolife" title="f:id:rakuda-san-desu:20180927155438p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20180927/20180927155438.png" alt="f:id:rakuda-san-desu:20180927155438p:plain" />

こういうヤツ。

シンプルで素敵です。

因みにhoverで反転です。

マークがなければMOREをセンタリングでOKだけど

マークが入りますよね。

スペースで位置調整は卒業したいしなぁ。と、悩みました。
<h4>最初に考えたボタン</h4>
＞の部分はFontAwesomeからリンクで。

ちょっと太いけどとりあえず。と言った感じで。

これをどうやっていい感じに配置するか。

<strong>MOREをセンタリングにしなければ！！</strong>と言う使命感から

MOREのbox要素と＞のimg要素をrelativeとabsoluteで重ねました。

これはブラウザ上の見た目は悪くなかったんだけど

hoverがうまく動きませんでした。

今思うと＞をimgだけじゃなくてdivに入れるとかしたらイケたかもです。

でもボタンの為に要素が二つとかhtmlが微妙。
<h4>その次のボタン</h4>
難しいことをしないで＞は文字で行くことにしました。

＞だと横長すぎるので>（半角）に。

目測であれこれ余白を設定。

昨日アップした時点ではこれでした。

<img class="hatena-fotolife" title="f:id:rakuda-san-desu:20180927162415p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20180927/20180927162415.png" alt="f:id:rakuda-san-desu:20180927162415p:plain" />

文字の>が微妙ですね。

しかもよく見るとMOREがセンターに合ってないよね。

やっぱりMOREはセンターに来たいし＞はマークにしたい。
<h4>ボタンをツールで検証</h4>
HTMLはすっきりMOREだけ。

＞は背景画像として使われていました。

それ！

作りながら学ぶ～の本でもやったよね！

リストマークをアイコンに設定するってやつでね！！

原理は分かったのでトライってみました。

背景で設定するにはFontAwesomeにリンクだとちょっと面倒そうなので

<iframe class="embed-card embed-webcard" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" title="Icons - Material Design" src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fmaterial.io%2Ftools%2Ficons%2F%3Ficon%3Darrow_forward_ios%26style%3Dsharp" frameborder="0" scrolling="no"></iframe><cite class="hatena-citation"><a href="https://material.io/tools/icons/?icon=arrow_forward_ios&style=sharp">material.io</a></cite>

こちらからDLしてきました。

ちょっと細くなって嬉しい。

backgroundで<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C1%EA%C2%D0%A5%D1%A5%B9">相対パス</a>を指定して位置を指定してノーリピート！

background: url("../images/<a class="keyword" href="http://d.hatena.ne.jp/keyword/sharp">sharp</a>_arrow_forward_<a class="keyword" href="http://d.hatena.ne.jp/keyword/ios">ios</a>_black_18dp.<a class="keyword" href="http://d.hatena.ne.jp/keyword/png">png</a>") 135px 15px no-repeat;

横位置は目測でやっちゃいましたが縦位置は計算して出したよ！

出来たのはこちら。

<img class="hatena-fotolife" title="f:id:rakuda-san-desu:20180927163402p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20180927/20180927163402.png" alt="f:id:rakuda-san-desu:20180927163402p:plain" />

ちょっとマークが太いけどいいんじゃないでしょうか！

カラー反転設定のhover。

アイコンが！消えた！←黒のままなので埋もれた

hoverのスタイルで白い＞を設定することで解決。
<h3>遠回りしたボタン問題解決</h3>
なんかずいぶん遠回りをした気がしますが

ボタン問題が解決しました。良かった。

千里の道も一歩から。

<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%EB%A5%AF%A5%ED%A1%BC%A5%C9">シルクロード</a>を渡れる🐫ですもの。　<span style="font-size: 80%;">←今思いついたよ</span>

ゆっくりでもしっかり進んでいきましょう。

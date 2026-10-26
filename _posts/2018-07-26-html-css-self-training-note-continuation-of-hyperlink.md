---
layout: post
title: "HTML&CSS自主練｜メモ｜ハイパーリンクの続き（8/4加筆修正アリ）"
date: "2018-07-26 16:15:49"
categories: ["HTML&CSS", "学習メモ"]
tags: ["参考サイト", "自主練"]
published: true
wordpress_id: 58
wordpress_status: "publish"
permalink: "/html-css-self-training-note-continuation-of-hyperlink/"
---
昼間の続き

<a class="keyword" href="http://d.hatena.ne.jp/keyword/%C1%EA%C2%D0%A5%D1%A5%B9">相対パス</a>の指定方法について、自分なりに纏めた図解（？

<img class="hatena-fotolife" title="f:id:rakuda-san-desu:20180804142756j:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20180804/20180804142756.jpg" alt="f:id:rakuda-san-desu:20180804142756j:plain" />

<span style="text-decoration: underline;">同階層への指定　<strong>./</strong></span>

<span style="text-decoration: underline;">上階層への指定　<strong>../</strong></span>

<span style="text-decoration: underline;">同階層への指定<strong>「./」</strong>は省略する事もできるけど</span>

<span style="text-decoration: underline;">理由がない限りは記載した方がいいとこの事（ブラウザによって作動しない可能性？）</span>

<span style="text-decoration: underline;">8/4加筆</span>

（間違いがあったら指摘していただけるとありがたいです）

（これにかかわらず突っ込み大歓迎です）

（8/4修正　画像表示にしました

細かいところですが02.htmlから03.htmlへの./の色を赤から緑へ変更しました）

ついでに

<a href="http://webkisomondai.jounin.jp/practice_kiso/index.html">練習問題 基礎 | Web基礎練習問題サイト</a>

こちらの<span style="color: #3d3f44; font-family: 'PT Sans', 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic Pro', Meiryo, 'MS PGothic', sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff; float: none; display: inline !important;">HTML基礎問題の成果↓</span>

<a href="https://rakuda-san-desu.github.io/lesson01/lesson01.html">練習01</a>

（リンク切れてます後で直します）

ついでに調べたもの

リンクの<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>

a:link {
}

リンク

a<a class="keyword" href="http://d.hatena.ne.jp/keyword/%3Avisited">:visited</a> {
}

閲覧済リンク

a:hover {
text-decoration: underline;
}

オンマウスのリンク

アンダーラインが出る指定

a:active {
}

アクティブなリンク

それぞれの色などを指定できる

この順番じゃないとうまく作動しない

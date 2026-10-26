---
layout: post
title: "HTML＆CSS 学習コース 中級編"
date: "2018-07-28 15:06:52"
categories: ["HTML&CSS", "Progate", "学習メモ"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 57
wordpress_status: "publish"
permalink: "/html-css-learning-course-intermediate-level/"
---
<h3>HTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>中級編</h3>
一昨日の夜、お小遣いをもらったらくだ🐫は早速Progateに課金しまして

HTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>の中級編に進みました。

とは言ってもパソを触れない時間になっていたので<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>から。

<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>からだと良くも悪くもゲーム感覚でサクサク進みますね。

ただ、結構感覚で入力しちゃってあんまり頭に入ってない感じ。

（PCからなら頭に入るかのような言い様）

まぁ予習として、流れをみる的な感じで。

そして今日はPCからのHTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>の中級編。

私のホームページへようこそ～ってやってた頃の知識では知らない事が多いのだけど

<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>から予習してたおかげで、戸惑う事はなくついていけました。

ただし英語が赤点だった私にはわからない単語も多く、調べながらやったので時間はそれなり。

まぁ早けりゃいいってものでもないしね。
<h3>メモ</h3>
<h5>要素のセンタリング</h5>
margin: 0 auto;

なんで0だけやautoだけじゃダメなの？問題。

そもそもmargin（padding）の指定方法を思い出せば簡単な話で、

4方向に同じ値を指定→margin: 40px;

上下・左右にそれぞれ同じ数値を指定→margin: 上下px 左右px;

上下左右それぞれ別の数値を指定→margin: 上px 右px 下px 左px;

数値の指定はpxだけでなく%やenでもOK

左右に15pxのmarginを指定したい場合

margin-leftとmargin-rightでそれぞれ指定するんじゃなくて

margin: 0 15px;（上下のmarginは0　左右のmargin15px)とまとめて指定できる

そこで私は気づきました。

<strong>「15pxをautoに置き換えたやつがセンタリングの指定のやつだ！」</strong>

0だけ、autoだけだと4方向に指定していることになるので、上下の中央合わせは出来ないので無効になる(と言う事だろう)

<strong>margin: 0</strong><span style="font-size: 80%;">（上下は0、指定なし) </span><strong> auto</strong><span style="font-size: 80%;">（左右はauto、センタリングされる)</span><strong>;</strong>

前回はよくわからないながら形式だけ覚えておこうとなったセンタリング問題ですが

意味が分かったので多分もう忘れない。と、思う。

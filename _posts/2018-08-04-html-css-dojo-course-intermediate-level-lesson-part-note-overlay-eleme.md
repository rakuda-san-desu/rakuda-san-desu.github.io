---
layout: post
title: "HTML＆CSS 道場コース 中級編レッスン部分｜メモ｜要素を重ねる（8/4加筆）"
date: "2018-08-04 15:49:14"
categories: ["HTML&CSS", "Progate", "学習メモ"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 53
wordpress_status: "publish"
permalink: "/html-css-dojo-course-intermediate-level-lesson-part-note-overlay-elements/"
---
<h3>HTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>道場コース中級</h3>
時間配分がヘタクソならくだ🐫です。

道場コース中級のレッスン部分をまとめようと思うのですが

連休を挟んじゃったので記憶が曖昧です。

曖昧な中でも覚えている事はそれだけ印象深かったと言う事で（？）

覚えていることをまとめたいと思います。
<h4>とにかく悩みまくった</h4>
レッスン部分で一番苦しんだのはパーツそれぞれのレイアウトですね。

学習コースで言う所のdiv class="lesson"を25%で横に並べる所までは良かったんだけど

要素同士を重ねるのはどう宣言するのか思い出せず

あちこちの要素をセンタリングするのも上手く出来ず

どのクラスにセンタリング指定するのか

text-alignなのかmargin:0 auto;なのかあれこれ試しまくり

なんでこれがセンタリングできないの？！って思ったら

centerのスペルが間違っているとかひどい有様でした。

（エディタなら補完してくれるし😢）

（「せんたー」で変換して確認したのは内緒だよ😢）

マジでcenterくらい覚えようね、🐫
<h4>とは言え</h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>の宣言で思い出せなかったのは要素の重ね方位だし

後は悩んだとは言え、自分の知識で何とか出来たのは良かったと思う。

HTMLはスラスラ書けたし。
<h3>メモ</h3>
<h4>要素の重ね方　absolute</h4>
<strong>position</strong><span style="font-size: 80%;">（場所）</span><strong>:</strong> <strong>absolute</strong><span style="font-size: 80%;">（絶対的な</span>）<strong>;　</strong>

top: 〇px;

left: 〇px;

サイトの左上（絶対的な場所）を基準として位置を指定

2つの要素に同じ値を指定する事で要素を重ねる
<h4>要素の重ね方　relative</h4>
（例）

<div class="box">
<img src="./camel.jpg">
<h1>らくだです</h1>
</div>

.box {
<strong style="font-weight: bolder; color: #3d3f44; font-family: 'PT Sans', 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic Pro', Meiryo, 'MS PGothic', sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff;">  position: relative</strong><span style="color: #3d3f44; font-family: 'PT Sans', 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic Pro', Meiryo, 'MS PGothic', sans-serif; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff; font-size: 12.8px;">(相対的な）</span><strong style="font-weight: bolder; color: #3d3f44; font-family: 'PT Sans', 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic Pro', Meiryo, 'MS PGothic', sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff;">;　</strong><span style="color: #3d3f44; font-family: 'PT Sans', 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic Pro', Meiryo, 'MS PGothic', sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff; float: none; display: inline !important;">←基準になるクラスを指定する</span>
text-align: center;
border: 2px solid blue;
width: 80％;
padding: 30px 0;
}

.box h1 {
<strong>  position:absolute;</strong>
<strong>top: 180px;</strong>                  ←基準になるクラスの左上からの距離を指定
<strong>width: 100%;</strong>　　　　 ←％の指定もOK
color: white;
text-shadow: 3px 4px 3px #000;
}

<img class="hatena-fotolife" title="f:id:rakuda-san-desu:20180804175100j:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20180804/20180804175100.jpg" alt="f:id:rakuda-san-desu:20180804175100j:plain" />

↑結果

（そっけなさ過ぎたので修正しました💧）

<span style="text-decoration: underline;">relativeを使っての指定の方が使う部分多そうですよね。</span>

<span style="text-decoration: underline;">（8/4例を修正・変更しました）</span>
<h4><span style="text-decoration: underline;">relativeで位置をずらす</span></h4>
<span style="text-decoration: underline;">relativeはabsoluteの基準位置を変えるだけでなく</span>

<span style="text-decoration: underline;">今ある位置から指定分だけずらすことが出来る</span>

<span style="text-decoration: underline;"> position: relative<span style="color: #3d3f44; font-family: 'PT Sans', 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic Pro', Meiryo, 'MS PGothic', sans-serif;"><span style="font-size: 12.8px;">;</span></span></span>

<span style="text-decoration: underline;"> top: 6px;　←上から6px離れる（下がって見える）</span>

<span style="text-decoration: underline;"> left: 6px;　←左から6px離れる（右に動いて見える）</span>

<span style="text-decoration: underline;">（8/4加筆）</span>

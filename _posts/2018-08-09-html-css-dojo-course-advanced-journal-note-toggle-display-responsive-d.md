---
layout: post
title: "HTML＆CSS 道場コース 上級編 ｜メモ｜レスポンシブデザインとブラウザ幅による表示非表示の切り替え"
date: "2018-08-09 15:23:47"
categories: ["HTML&CSS", "Progate", "学習メモ"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 51
wordpress_status: "publish"
permalink: "/html-css-dojo-course-advanced-journal-note-toggle-display-responsive-design-and-display-width-by-browser-width/"
---
<h3>HTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>道場コース上級編</h3>
勢い勇んで道場コースの上級編をやろうとしたら

最初からさっぱりわからなくて学習コースをやり直したらくだ🐫です。

いや、びっくりした。

結局「なにこれ分からん」ってなったのは

ヘッダー部分の各コンテンツへのリンクを、モバイルサイズの時はアイコンマークにするっていう部分位で

後はソコソコすんなり。

（モバイルだけ整えて<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BF%A5%D6%A5%EC%A5%C3%A5%C8">タブレット</a>何もしてなくてエラーとかはあった）

（気を付けたい）
<h3>メモ</h3>
<h4>レスポンシブデザイン</h4>
同じアドレスのHTML＆<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>コードで、見る人それぞれの環境（PC、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BF%A5%D6%A5%EC%A5%C3%A5%C8">タブレット</a>、モバイル）に合わせた適切なデザインを表示させるって感じですかね。
<h4>表示幅</h4>
画面サイズによって端が切れないようにwidthは％指定にする

PC表示では最大幅(max-width）を指定する

Progateでは

max-width1170px、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BF%A5%D6%A5%EC%A5%C3%A5%C8">タブレット</a>1000px、モバイル670pxで指定していたけど

一般的にはmax-width1000px程度、モバイル750pxが良さそうです。

<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BF%A5%D6%A5%EC%A5%C3%A5%C8">タブレット</a>の事は特に考えなくていいっぽい。
<h4>box-sizing</h4>
レスポンシブデザインに有効ってことで出てきたbox-sizingですが

もう常にbox-sizingでいいのでは？ってなってる。

<strong> * {</strong>

<strong>  box-sizing: border-box;</strong>

<strong>}</strong>
<h4>サイト幅によるアイコンの表示切替</h4>
モバイルは幅が狭い為

各コンテンツへのリンクを文字で横並びにすると改行されてデザインが崩れる状況になりがち

↓

メニューをアイコン表示にしてすっきり

（本来はJSなどでメニューが開くような動きを付ける）

（ここではHTMLと<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>の指定だけ）

htmlにメニューアイコン（class="menu-icon"）が表示されるようにタグを挿入

（ProgateではFont Awesomeを利用）

通常表示の<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>では表示させないような指定をする

<strong>.menu-icon {</strong>

<strong>  display: none;</strong>

<strong>}</strong>

モバイルサイズのメディアクリエ内に

text表示のリンク（class="menu-text")を消して、アイコン（class="menu-icon")を表示させるように指定

.menu-text {

display: none;

}

.menu-icon {

display: block;

}
<h3></h3>
<h3>今後</h3>
HTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>は他の方法で勉強しつつ、Progateでは<a class="keyword" href="http://d.hatena.ne.jp/keyword/Java">Java</a> Scriptを勉強していこうと思います。

---
layout: post
title: "HTML&CSS書籍｜メモ｜float解除とtimeタグとマークアップと言うもの"
date: "2018-09-02 17:16:46"
categories: ["HTML&CSS", "学習メモ"]
tags: ["作りながら学ぶHTML/CSSデザインの教科書", "書籍", "自主練"]
published: true
wordpress_id: 43
wordpress_status: "publish"
permalink: "/html-css-book-note-float-cancellation-time-tag-and-markup/"
---
<h3>作りながら学ぶHTML/<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>デザインの教科書</h3>
本日はサクサクと5章を。

ページヘッダーやページフッダーの追加と2カラムのレイアウトなど
<h3>カラムデザインと言えばfloat</h3>
floatの不具合回避として

Progateでは空のdivを挿入してfloat解除する方法を教わりましたが

こちらでは疑似要素を使うやり方を。

しかし段組みはフレックスボックスでやるようにしたいのと

疑似要素について調べ出すときりがないんじゃって事で

「こういうやり方もあるよ」っていう確認だけして

深堀はしていないのでした。
<h4></h4>
<h4>疑似要素を利用したfloatの解除</h4>
<div class="main>

<div class="sub">

</div>

<div class="sub">

</div>

</body>

.main {
border: 1px solid red;
}

.sub {
width: 300px;
height: 200px;
border: 1px solid #000;
float: left;
}

この状態だとsubのdivが浮いていてmainの高さがなくなっているのだけど

.main {
border: 1px solid red;
}

<strong>.main:after {　</strong><span style="font-size: 80%;">（親要素の最後に）</span>
<strong> content: "";　</strong><span style="font-size: 80%;">（空の要素を追加し）</span>
<strong> clear: both;　</strong><span style="font-size: 80%;">（floatを解除し）</span>
<strong> display: block;　</strong><span style="font-size: 80%;">（空の要素をブロック要素に）</span>
<strong>}</strong>

.sub {
width: 300px;
height: 200px;
border: 1px solid #000;
float: left;
}

太字部分を挿入することで

floatさせた要素の下に空divを挿入しての解除方法と同じ結果になるのでした。

例がしょぼいのは気にしないで頂ければ幸い。
<h3>時間を示すためのタグ</h3>
<h4>日時を示すタグを使う</h4>
更新履歴とかで日付を表示させる場合

pとかspanにクラスを付けてスタイルを適用させていたりしましたが

<time></time>と言うタグがあるのだそうです。

これは見た目の問題じゃなくて

<strong>コンピュータに日時を正しく認識させるため</strong>のタグ。

その為に形式も決まっていて
<ul>
 	<li>時間は「：」で区切る</li>
 	<li>日付は「-」で区切る</li>
 	<li>日付と日時は「T」で繋げる</li>
 	<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BF%A5%A4%A5%E0%A5%BE%A1%BC%A5%F3">タイムゾーン</a>も指定できる</li>
</ul>
正確に特定できない日時を表す時は❌
<h4>表示を変えたいとき</h4>
日付の表示は<strong><time>2018-09-02</time></strong>なのだけど

年月日とか．とか使いたいときもあるじゃないですか。

そういう時の表記はコレ。

<time <strong>datetime="2018-09-02"</strong>>2018.09.02</time>

<strong>pubdate属性</strong>

もう一つ紹介されていた<strong>pubdate属性</strong>ですが

現在は廃止になっている模様。

本にも打ち消し線を引いておきましたのだ。

<span style="font-size: 80%;">参考リンク</span>

<span style="font-size: 80%;"><a href="http://www.tohoho-web.com/html/time.htm">http://www.tohoho-web.com/html/time.htm</a></span>
<h3><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A1%BC%A5%AF%A5%A2%A5%C3%A5%D7">マークアップ</a>と言うもの</h3>
ProgateでHTML/<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>を勉強し始めて

分からないタグの意味を調べているうちに

「htmlタグと言うのはページの内容を表示させるためだけのものではなくて

それぞれに意味を与えているのだなぁ」と意識するようになったのだけど

この本の１章～５章を進めるうちにその思いが強くなりました。

元になるデザインがあって

それを再現するHTML/<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>を書くことをコーディングと言うそうですが

もう一歩踏み込んで

デザインに込められた情報をコンピュータが認識できるようにコーディングすることを<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A1%BC%A5%AF%A5%A2%A5%C3%A5%D7">マークアップ</a>と言う。って事でいいのかな。

今回出てきたtime然り、セクションタグとかfigureとか、より詳しく認識してもらえるように。と言う事なんだなぁ。きっと。
<h3><a class="keyword" href="http://d.hatena.ne.jp/keyword/%C4%C9%B5%AD">追記</a>（2018/09/03）</h3>
タイトルが<strong>ウェブフォントとtimeタグと<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A1%BC%A5%AF%A5%A2%A5%C3%A5%D7">マークアップ</a>と言うもの</strong>ってなってましたが

内容にそぐわないので<strong>float解除とtimeタグと<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A1%BC%A5%AF%A5%A2%A5%C3%A5%D7">マークアップ</a>と言うもの</strong>に訂正しました

勢いで書いているとは言えヒドイ。

---
layout: post
title: "HTML＆CSS書籍｜メモ｜line-heightと画像関連タグ"
date: "2018-08-28 20:07:07"
categories: ["HTML&CSS", "学習メモ"]
tags: ["作りながら学ぶHTML/CSSデザインの教科書", "書籍", "自主練"]
published: true
wordpress_id: 44
wordpress_status: "publish"
permalink: "/html-css-book-note-line-height-and-images-related-tags/"
---
<h3>作りながら学ぶHTML/<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>デザインの教科書</h3>
前回は1~2章

準備とメインの部分の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A1%BC%A5%AF%A5%A2%A5%C3%A5%D7">マークアップ</a>。

今回は3~4章。

<a class="keyword" href="http://d.hatena.ne.jp/keyword/css">css</a>による装飾と画像の挿入です。

画像が入るとにぎやかになりますね。

新しい知識もいくつか。
<h3>line-height</h3>
キレイに見せるためには丁寧にデザインしましょう。

と、言う事で

今までそこまで気にしていなかったline-heightにもテクニックが。

今までは行間ちょっと広げたいな、で、様子を見ながらpxで指定していたline-height

なのですが、行間指定の場合はpxじゃなくて文字サイズに対する割合で指定するのが良いとの事。
<h4>文中にサイズの大きな文字があった場合にも対応</h4>
細かい説明は端折りますが、例えば<span style="font-size: 200%;">こんな風に</span>大きな文字があった場合

px指定でいい感じの行間を取っていたつもりでも、上の行と被ってしまうんですね。

割合で指定していればそれを上手くカ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D0%A1%BC">バー</a>してくれる。と。
<h4>具体的には</h4>
<strong>line-height</strong>は<strong>指定した高さの中心に文字が来る</strong>ので

20pxの文字で行間を10pxにしたいと考えた場合

上部5px+文字サイズ20px+下部5px　=　30px

30/20＝1.5　（小数点は第4位以下を切り上げ）

<strong>line-height: 1.5;</strong>

と、なる訳です。
<h4>ブラウザ間の表示の差異をなるべく減らす</h4>
で、HTMLってブラウザによって若干見栄えが違うじゃないですか。

せっかくのデザインだからどんなブラウザでも同じように見えたいよね。

と言うわけで、line-heightも常に指定するように気を付けたほうが良さそう。

フォントを指定しておくのとかもソレですよね。
<h3>画像挿入関連</h3>
画像を挿入するにあたって

原寸表示だったら単純に<img src="〇〇.jpg">で良くない？とか思っていたのですが

そういう事でもないっぽい。
<h4>サイズ指定</h4>
HTMLで画像を表示させるにあたって

imgタグにサイズも表記していたほうがいいっていうのはなんとなく記憶にありまして。

サイズ表記がないと画像サイズをコンピューターが確認しないといけないのに対して

記述があればその分の時間が浮くと言う事だったような。

あとは例えば違う人がサイトの更新をする際にも記述があった方が親切ですよね。

画像から調べなくていいもんね。

他には、何らかの理由で画像が表示されない場合、サイズ指定があれば画像サイズの枠が表示される為にレイアウトが崩れにくい。とかもあるようです。
<h4>alt</h4>
代替えテキストを表示するためのaltです

本には<strong>必須なので内容を入れない場合でもalt=""で空欄表示する</strong>とあるんですが

<strong><a class="keyword" href="http://d.hatena.ne.jp/keyword/HTML5">HTML5</a>では空欄表示なら記述を省いていい</strong>そうです

で、らくだ🐫は「代替えテキスト＝写真のタイトル」位のイメージでいたのだけど

<strong>音声読み上げソフトや画像を表示しないブラウザの為に写真の内容を文章化したもの。</strong>

本文の内容にかかわる画像にはしっかり設定する必要があるようです。

対してaltが必要ないのはリストマーカーを画像で表示している場合とかですかね。

いちいち「リストマーカーです」とかいらないもんね。
<h4>figureとfigcaption</h4>
これはちょっとややこしくて、まだすんなり理解できていないのだけど

一先ず<strong>figcaption＝画像の説明・見出し</strong>

これは<strong>figureの中でのみ利用可</strong>

で、figureとは何ぞや
<blockquote><span style="color: #000000; font-family: Meiryo; font-size: medium; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 16px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-style: initial; text-decoration-color: initial; display: inline !important; float: none;"><figure>タグは、図表であることを示す際に使用します。 ここで言う図表とは、文書の本文（メインフロー）から参照されるようなイラスト・図・写真・<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BD%A1%BC%A5%B9%A5%B3%A1%BC%A5%C9">ソースコード</a>などのことです。</span>

<a href="http://www.htmq.com/html5/figure.shtml"><figure>－HTML5タグリファレンス</a></blockquote>
うーん。

おおざっぱに考えるとaltを付けたほうがいい画像＝figureも必要ってことかしら？

なかなか難しいです。

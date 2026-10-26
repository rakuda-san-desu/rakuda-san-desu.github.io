---
layout: post
title: "HTML＆CSS 学習コース 中級編 2週目｜メモ｜インライン要素の変更，クリック時の変化"
date: "2018-07-29 15:49:17"
categories: ["HTML&CSS", "学習メモ"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 55
wordpress_status: "publish"
permalink: "/html-css-learning-course-intermediate-2nd-week-note-changing-inline-element-change-when-clicked/"
---
<h3>7/30修正</h3>
<h3>HTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>学習コース中級編2週目</h3>
<h4>指示に従うだけなので</h4>
2週目だし、指示通りにタグ打ちするだけなので基本サクサクと。

2週目なのでスライドは飛ばして指示だけ見て書く様にしたんだけど、

「marginを15pxに」の様に超具体的な指示じゃなくて

「aをブロック要素へ」のような指示の場合

「あれ？プロパティとかどうするんだっけ？」となり

スライドやメモを見直したところが2か所
<h4>凡ミス</h4>
その他凡ミスエラーが数回

要素のミスタイプと<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BB%A5%EC%A5%AF%A5%BF">セレクタ</a>の閉じ忘れ

よくあることと思うけど、よくあることなだけに気を付けたいですよね。と言う。
<h3>絞りだせなかったプロパティのまとめ</h3>
<h4>インライン要素をブロック要素に</h4>
最初、TOP部分の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C4%A5%A4%A5%C3%A5%BF%A1%BC">ツイッター</a>や<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D5%A5%A7%A5%A4%A5%B9%A5%D6%A5%C3%A5%AF">フェイスブック</a>へのリンクボタンを作る際には

displayをinline-blockにって指示なんだけど（なので迷わないんだけど）

次のヘッダーのログ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%EA%A5%F3">インリン</a>クを作るところでは<a>をブロック要素にっていう指示でしてね。

プロパティが思い出せないのでした。

まぁスライドを見れば「そうそう！でぃすぷれいね！！」となる。

らくだ🐫の頭は前章の事を忘れる・・・（頑張りましょう🐫）

inline（直列）要素の<a>を

<strong>display</strong><span style="font-size: 80%;">（表示）</span><strong>:block</strong><span style="font-size: 80%;">（固まり）</span><strong>;</strong>

うーん。ちょっとわかりづらい。もうちょっとうまくdisplayって単語と結びつきたい。

あとこれ、

ログ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%EA%A5%F3">インリン</a>クの部分もインラインブロックじゃダメなんじゃろか。

試してみたけど表示的には変わらなく見えるんだよね。

学習コースでは「ブロック要素に」って指定されてるから

インラインブロックに指定するとエラーになるんだけど

道場コースではどうなるんだろう。

忘れなければやってみよ。
<h4>クリック時の変化</h4>
マウスが乗っている時の状態を指定するには　<strong>クラス名:hover</strong>

クリックした時の状態を指定するには　<strong>クラス名:active</strong>

これはリンクの色指定で使っていたので頭に入っていました

で、クラスにも指定できるんだねスゴイ！ってなりました。

で、またもやログ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%EA%A5%F3">インリン</a>クの部分なんだけど

アニメーション？あれ？どんな指定するんだっけ？？

と、プロパティがわからんどころでは済まないのでしたよ。

.div{

<strong>transition  <span style="text-decoration: line-through;">translation</span></strong><span style="font-size: 80%;">（時間的変化）</span><strong>: all</strong><span style="font-size: 80%;">（要素内全て）</span><strong> 1s</strong><span style="font-size: 80%;">(変化の時間）</span><strong>;</strong>

}

.div:hover{

background-color:red;

}

divの中全てを一秒かけて変化させる

マウスが乗ったら背景色を赤に

アニメーションっていうから紛らわしくて（プロパティをanimationって打ちたくなる）

「時間経過による変化」の方が覚えやすいかもしれない。

で、transition <span style="text-decoration: line-through;">translation</span>に指定できる要素は他にどんなものがあるのかな？って調べたら

なんか沢山あってパンクしそうだったので

今はこれ↑をしっかり覚えることに集中したいと思います。
<h3>道場コース中級編に向けて</h3>
「スライドは見ずに指示だけで作る」で詰まるんだからもっと大変だろうなーとか思いつつ。

トライ＆エラーで頑張りたい

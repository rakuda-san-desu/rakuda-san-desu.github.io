---
layout: post
title: "HTML＆CSS 道場コース 中級編トップとフヘッダー｜メモ｜アニメーションと要素の固定と角の丸め"
date: "2018-07-30 16:44:10"
categories: ["HTML&CSS", "学習メモ"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 54
wordpress_status: "publish"
permalink: "/html-css-dojo-course-intermediate-level-top-and-headers-notes-animation-and-fixation-of-elements-and-rounded-corners/"
---
<h3>HTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>道場コース中級編</h3>
今日は（今日も）集中力の無いらくだ🐫です

昨日の夕方～今日の午前中に学習コース上級編をやりまして

道場コースの中級編に入りました。

上級編の事は道場コースの時にでも。
<h4>最初はスムーズ</h4>
トップ（背景画像が付いている部分）はさほど悩まず。

Font Awesomeのクラスの指定方法だけ見直しました
<h4>見る力も備えたい</h4>
ヘッダー部分は

指示書だけじゃわからない部分を検証機能で調べるのはいいとして

ググったりスライドを見直したりメモを見たりしました。

ちなみに分からなくて調べるときは

自分で検索した方がスライドやメモを見返すよりいい気がして

なるべくそうしております。

あ、でも、ログ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A4%A5%F3%A5%EA%A5%F3">インリン</a>クのtransitionの時間とhover時の透明度がわからなくて、ここは素直に学習コースのコードから探しました。

何とか出来上がり「よしおーけー！」と思って判定するも2回エラー。

1回目はヘッダーが固定されていなかった

2回目はヘッダー部分のBGカラーでrgbaで指定するのにrgbになってた

<span style="color: #ff0000;">しっかり見直しをしましょう<span style="color: #000000; font-size: 80%;">（小学生のころから言われてたヤツ）</span></span>
<h3>メモ</h3>
<h4>transition</h4>
あれだけ言ってて覚えられないtransition

いやね、らくだ🐫ホント英語だめでね。

高校時代、先生のお情けで進級・卒業って感じのまじヤバイ赤点でしたのでね。

「hoverと組み合わせて時間経過で色が変わるように指定するやつだよね！

……なんだっけ？」

なのでした。

.div {

background-color: rgba(255,255,255,0.5)

<strong> transition</strong>: all 0.8s;

}

.div:hover {

background-color: rgba(255,255,255,1)　←<span style="text-decoration: underline;">スペースじゃなくカンマで区切る</span>
<h4>要素の固定</h4>
ヘッダー部分が動かなくなっていることを見逃してエラーになりました

学習コースだと最後にやったじゃん？

油断せずしっかり観察しないといけないよね。

で、コードもどうするんだっけ？？で、調べて書きました。

<strong>position</strong><span style="font-size: 80%;">（場所）</span><strong>: fixed</strong><span style="font-size: 80%;">（固定）</span><strong>;</strong>

<strong>top</strong><span style="font-size: 80%;">（どこから）</span><strong>: 0</strong><span style="font-size: 80%;">（どれくらい）</span><strong>;</strong>

重なり順を指定しておくことは覚えていたけどコードがわからず

っていうか解説ページに一緒に乗ってた

<strong>x-index</strong><span style="font-size: 80%;">（X=高さ方向・重なりの順番）</span><strong>: 10</strong><span style="font-size: 80%;">（0が一番下・数字が大きいほど上）</span><strong>;</strong>
<h4>角の丸め方</h4>
ボックス要素の角の丸め方も全然思い出せなくて。

borderを……どうするんだっけ？位思いたかった残念。

<strong>border</strong><span style="font-size: 80%;">（境界線の）</span><strong>-radiusu</strong><span style="font-size: 80%;">（半径）</span><strong>:</strong><span style="font-size: 80%;">5px(サイズ指定）</span><strong>;</strong>

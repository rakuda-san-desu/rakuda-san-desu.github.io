---
layout: post
title: "HTML&CSS 道場コース中級編｜メモ｜メッセージ部分とフッター文字の太さ"
date: "2018-08-04 17:09:14"
categories: ["HTML&CSS", "Progate", "学習メモ"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 52
wordpress_status: "publish"
permalink: "/html-css-dojo-course-intermediate-level-message-part-and-footer-memo-text-thickness/"
---
<h3 style="color: #2e3034; clear: both; margin: 1em 0px 0.8em; line-height: 1.3; font-size: 20.8px; font-family: 'PT Sans', 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic Pro', Meiryo, 'MS PGothic', sans-serif; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff; text-decoration-style: initial; text-decoration-color: initial;">HTML&<a class="keyword" style="background-color: transparent; color: #3d3f44; text-decoration: none; word-wrap: break-word; font-size: 20.8px; font-weight: inherit !important; font-style: inherit !important; pointer-events: auto !important; cursor: pointer !important; border-bottom: 1px dotted #d2d7e5;" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>道場コース中級終了</h3>
今日はなんだか調子がいい（気がする）らくだ🐫です。

道場コース中級、メッセージ部分とフッターはサクサク終了できました。

とは言えちょっとくらいは躓くよね。
<h4>凡ミス注意</h4>
思ったようにスタイルが適用されないぞ？と思うと

クラス名の指定を間違えていたり

message-rapperに指定したいのにmessageだけにしていたり

containerをcontentsってしていたり

解決策としては

<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EF%A5%A4%A5%E4%A1%BC%A5%D5%A5%EC%A1%BC%A5%E0">ワイヤーフレーム</a>を紙で作って

クラス名をメモって横に置いておく。とかかな？

あとは

class="btn message"にしていて

btnでdisplay: inline-boxにしてるのに

messageでも同じ指定をして変な事になっちゃったり

これも紙メモである程度防げそうかな？
<h3>メモ</h3>
<h4>文字の太さ</h4>
font……fontなんだっけ？でした。

face？とか。

<strong>font-weight</strong><span style="font-size: 80%;">（重さ）</span><strong>: normal</strong><span style="font-size: 80%;">（普通）</span><strong>;</strong>

太さ→重さ

今回は標準で太字の見出し文字（h2,h3）を細くしたいのでnormal指定

他にboldで太字、lighterで一段階細く、bolderで一段階太く

数字で指定も（100単位で100～900、標準は400）
<h3>次のステップ</h3>
明日は道場上級

その後は<a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>に行きたいと思います。

<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>からは見てるのでメモ作りながらやりたいなー。とか。

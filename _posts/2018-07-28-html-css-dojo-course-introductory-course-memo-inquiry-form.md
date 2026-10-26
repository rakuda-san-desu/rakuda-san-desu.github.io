---
layout: post
title: "HTML＆CSS 道場コース 初級編 ｜メモ｜問い合わせフォーム"
date: "2018-07-28 17:45:40"
categories: ["HTML&CSS", "学習メモ"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 56
wordpress_status: "publish"
permalink: "/html-css-dojo-course-introductory-course-memo-inquiry-form/"
---
<h3>HTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>道場コース初級編</h3>
<h4>勢いに乗ってやってみました</h4>
今日はやりたい事だけやろうの日。

なのでらくだ🐫はProgateを集中してやることにしました。

HTML&<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>の学習コース中級が終わったので、勢いに乗って道場コースの初級編へ。

「学習コースの時どうやって作ったんだっけ？」ではなく

「どうしたら仕様書（＆見本)を再現できるかな？」と考えて書く様にしました。
<h4>ミスった所</h4>
学習コースで書いたコードで言うと

contentsとcontact-foamの間の間隔をあけすぎているのに気づかずチェックを入れちゃいました。

ブラウザの検証からどこが間違ってるかチェック。すぐ直ったのでした。

すぐ直ったけど、完成と思う前に気が付きたい。
<h4>分からず調べたところ</h4>
<h5>フッターのfloat</h5>
「Progate」と「リンクのリスト」を左右に散らしたつもりが要素の頭が揃ってない。

検証で見てみると「Progate」にfloat:lert;が設定されていなかった為にDivが普通に上下に重なっていました。

カラム（段組み）っていうデザインの模様。
<h5>フォームとボタン</h5>
問い合わせフォームの各種フォームとボタンのタグを全然覚えていなくてググりましたとさ。

あと、余白の設定が上手く考えられなくてココだけ学習コースの時のメモ見た。テヘ。
<h3>メモ</h3>
<h4>問い合わせフォームに使われるタグ</h4>
<h5><textarea></textarea></h5>
複数行のテキスト入力エリア
<h5><input></h5>
いろんなフォームを構成するためのタグ

初期値はtextで一行入力フォーム

type="submit" で送信ボタン

他にもいろいろあるけど色々ありすぎなので適宜<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B0%A5%B0%A4%EB">ググる</a>か後でまとめる

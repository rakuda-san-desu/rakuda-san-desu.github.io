---
layout: post
title: "jQuery上級コース｜メモ｜=== と =="
date: "2018-08-27 15:00:11"
categories: ["jQuery", "Progate", "学習メモ"]
tags: ["Progate", "参考サイト", "学習サイト"]
published: true
wordpress_id: 45
wordpress_status: "publish"
permalink: "/jquery-advanced-course-notes-and/"
---
<h3>一見複雑なので構えちゃう上級</h3>
Progate、<a class="keyword" href="http://d.hatena.ne.jp/keyword/jQuery">jQuery</a>の上級コースに入りました。

とりあえずスライドの所（1/18～7/18）まで。

パッと見の複雑さで「難しそう……🐫💧」と構えちゃいますね。

集中力がかけていたこともあって混乱気味になりつつも

実際はパーツごとに指示されてるし

ノートやスライドを見直しながらではありますが

どうにか付いて行けました。

ただやっぱり凡ミス（；忘れやスペルミス）が多くてね。

落ち着きましょうらくだ🐫よ。
<h3>比較<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B1%E9%BB%BB%BB%D2">演算子</a></h3>
何とか進めていた上級編ですが

lengthメッソドのスライドで？？？となりました。

length自体は<a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>の方でやった事と一緒じゃん？って感じでまぁよかったんだけど

<strong>slideindex == 0 </strong>とな？

これは多分比較するやつよね。

<strong>=== </strong>じゃないの？また見逃した？？？となりました。
<h4>見直しするも表記無し</h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/jQuery">jQuery</a>→ES5→ES6とスライドを見直すも特に記載は無し

（見逃していたら本当に申し訳ないです💦）

比較<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B1%E9%BB%BB%BB%D2">演算子</a>以外の所にも何か書いてあるかも？と、ざっと見返したのだけど

結局わかりませんでした。

で、省略形とかなのかしら？とか思いつつ調べてみました。
<h4>厳密等価<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B1%E9%BB%BB%BB%D2">演算子</a>と等価<a class="keyword" href="http://d.hatena.ne.jp/keyword/%B1%E9%BB%BB%BB%D2">演算子</a></h4>
<strong>===</strong> と <strong>==</strong> はどちらも<strong>右辺と左辺が等しいかを比較する</strong>ものと言うのは間違いないのだけど

<strong>「=== は厳密に比較」　「</strong><strong>== は単純に比較」</strong>と言う事のようです。

例えば

5 （数字の5）と '5' （文字数としての5）を比較した時

<strong>5 == '5'</strong>　は、どちらも「5」とみなされ<strong>true</strong>

<strong>5 === '5'</strong>　だと数字と文字なので<strong>false</strong>

省略系だなんてとんでもないじゃん！

これって結構重要じゃないですかね。

特に理由がない場合は<strong>===</strong>を使う方が良さそう。

<span style="font-size: 80%;">（そしてあの場面で=== ではなく== だった意味と言うか意義って何かあるのかな？） </span>

因みに

===と==の違いについて、私にはこちらの解説がわかりやすかったです。

<iframe class="embed-card embed-webcard" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" title="JavaScript の == と === の違いを詳しくまとめてみる - Qiita" src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fqiita.com%2FNossa%2Fitems%2F358c467906c38cd04f91" frameborder="0" scrolling="no"></iframe><cite class="hatena-citation"><a href="https://qiita.com/Nossa/items/358c467906c38cd04f91">qiita.com</a></cite>

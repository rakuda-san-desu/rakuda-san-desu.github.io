---
layout: post
title: "JavaScript｜メモ｜constは上書きできない？"
date: "2018-08-19 16:55:00"
categories: ["JavaScript", "Progate", "学習メモ"]
tags: ["Progate", "参考サイト", "学習サイト"]
published: true
wordpress_id: 48
wordpress_status: "publish"
permalink: "/javascript-memo-can-not-overwrite-const/"
---
<h3>今更の疑問</h3>
Progateの<a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>（ES6）の学習コースを復習しておりましたら

疑問が湧きまして。
<h4>const（定数）は更新できないのでは？</h4>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>学習コースⅡの7章「配列の要素の更新」での事です。

<strong>定数　</strong>に代入された配列の要素を　<strong>更新　</strong>する訳です。

でも　<strong>定数</strong>　は<strong>更新できない</strong>って習ったじゃん？

<strong>更新できない</strong>　<strong>定数　</strong>に設定することで<strong>予期せぬ更新を防ぐ</strong>って習ったじゃん？

でも更新できちゃうの🤔？
<h3>調べた結果</h3>
<blockquote><span style="color: #333333; font-family: -apple-system, 'Segoe UI', 'Helvetica Neue', 'Hiragino Kaku Gothic ProN', メイリオ, meiryo, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff; text-decoration-style: initial; text-decoration-color: initial; display: inline !important; float: none;">変数の正体とはアドレスです。</span><br style="box-sizing: inherit; color: #333333; font-family: -apple-system, 'Segoe UI', 'Helvetica Neue', 'Hiragino Kaku Gothic ProN', メイリオ, meiryo, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff; text-decoration-style: initial; text-decoration-color: initial;" /><span style="color: #333333; font-family: -apple-system, 'Segoe UI', 'Helvetica Neue', 'Hiragino Kaku Gothic ProN', メイリオ, meiryo, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff; text-decoration-style: initial; text-decoration-color: initial; display: inline !important; float: none;">定数が制限するのはアドレス変更、つまり再代入です。</span><br style="box-sizing: inherit; color: #333333; font-family: -apple-system, 'Segoe UI', 'Helvetica Neue', 'Hiragino Kaku Gothic ProN', メイリオ, meiryo, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff; text-decoration-style: initial; text-decoration-color: initial;" /><span style="color: #333333; font-family: -apple-system, 'Segoe UI', 'Helvetica Neue', 'Hiragino Kaku Gothic ProN', メイリオ, meiryo, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: #ffffff; text-decoration-style: initial; text-decoration-color: initial; display: inline !important; float: none;">参照型ではアドレス変更をせずに値を変更できるため、定数でも値の変更ができます。</span>

<iframe class="embed-card embed-webcard" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" title="JavaScriptの定数(const)は「変更できない」わけではない。 - Qiita" src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fqiita.com%2FNoobieGull%2Fitems%2F11d8c2bdcd82a01e8146" frameborder="0" scrolling="no"></iframe><cite class="hatena-citation"><a href="https://qiita.com/NoobieGull/items/11d8c2bdcd82a01e8146">qiita.com</a></cite></blockquote>
まだちょっとわからなかったので自分なりにかみ砕いてみました
<h4>どこを更新しているのか</h4>
まず基本はこう。

const i = 9;　←<strong>定数</strong>　i　に　9　を<strong>代入</strong>

<span style="text-decoration: line-through;">i = 3;　←<strong>定数</strong>　i　を　３　で<strong>更新</strong></span>　←error

定数は更新できないってヤツですね。

で、7章でやっていることは　<strong>配列の要素の更新</strong>

const animals =<strong> ["dog", "cat", "sheep"]</strong>;　←定数　<strong>animals</strong>　に　<strong>配列</strong>を代入

animals<strong>[2]</strong> = "rabbit";　←定数animalsに代入された　<strong>配列の3番目</strong>を　rabbitに　<strong>更新</strong>

<strong>配列の内容を更新している　≠　定数を更新している</strong>　と言う事のようです

そうかナルホド！と言う程すっきりした訳でもないけど一応納得出来ました。

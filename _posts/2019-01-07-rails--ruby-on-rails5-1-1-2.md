---
layout: post
title: "Rails｜ Ruby on Rails5学習コース・Ⅰ-1～2"
date: "2019-01-07 18:27:57"
categories: ["Progate", "Ruby on Rails", "学習メモ"]
tags: ["Progate", "参考サイト", "学習サイト"]
published: true
wordpress_id: 298
wordpress_status: "publish"
permalink: "/rails%ef%bd%9c-ruby-on-rails5%e2%80%901-1%ef%bd%9e2/"
---
<!-- wp:heading -->
<h2>ローカルでも同じことをやるをやってみよう</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>と、言うわけで（？）ProgateさんのRails学習コースを再開と言うか初めからやり直すことにしたので、Twitterで見かけた「ローカルでも同じことをやってみよう」と言うのにチャレンジしてみることにしました。</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3>環境設定と早速はまる🐫</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>環境設定はこちらを参考に</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><a rel="noreferrer noopener" aria-label=" (新しいタブで開く)" href="https://qiita.com/ruxes7/items/d3e7c095947db4116402" target="_blank">https://qiita.com/ruxes7/items/d3e7c095947db4116402</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>途中まではサクサク進んでいたのですが、サーバーを立ち上げてローカルで表示する部分でエラーになりました</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3>Rubyのバージョン</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>ググって最初に出てきたのがこちら</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><a href="https://ja.stackoverflow.com/questions/42982/rails-s-%E3%81%A7-sqlite3%E9%96%A2%E9%80%A3%E3%81%AE-loaderror-%E3%81%8C%E3%81%A7%E3%81%BE%E3%81%99">https://ja.stackoverflow.com/questions/42982/rails-s-%E3%81%A7-sqlite3%E9%96%A2%E9%80%A3%E3%81%AE-loaderror-%E3%81%8C%E3%81%A7%E3%81%BE%E3%81%99</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Rubyは以前インストールしていて、その時の最新バージョン2.5.〇（メモしてないうちに消しちゃったよ💦）だったのだけど、必要なGEMが非対応と言う事のようです。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>なので一旦Rubyをアンインストール。残ったフォルダなんかも削除しましてRuby 2.4.5をインストール→Railsのインストール（5.2.2でした）</p>
<!-- /wp:paragraph -->

<!-- wp:image {"id":301,"align":"center","width":378,"height":329} -->
<div class="wp-block-image"><figure class="aligncenter is-resized"><img src="https://rakuda3desu.net/wp-content/uploads/2019/01/20190107rails.png" alt="" class="wp-image-301" width="378" height="329"/></figure></div>
<!-- /wp:image -->

<!-- wp:paragraph -->
<p>無事、ローカルでも表示することが出来ました！</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>（そりゃできるだろ。と言うｗ）</p>
<!-- /wp:paragraph -->

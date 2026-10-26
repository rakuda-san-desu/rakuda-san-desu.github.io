---
layout: post
title: "Rails｜ Ruby on Rails5学習コース・Ⅱ-9～13"
date: "2019-01-14 15:15:08"
categories: ["Progate", "Ruby on Rails", "学習メモ"]
tags: ["Progate", "参考サイト", "学習サイト"]
published: true
wordpress_id: 304
wordpress_status: "publish"
permalink: "/rails%ef%bd%9c-ruby-on-rails5%e5%ad%a6%e7%bf%92%e3%82%b3%e3%83%bc%e3%82%b9%e3%83%bb%e2%85%a1-9%ef%bd%9e13/"
---
<!-- wp:paragraph -->
<p><a href="https://rakuda3desu.net/rails%EF%BD%9C-ruby-on-rails5%E2%80%901-1%EF%BD%9E2/">前回からの続き</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>この辺は一度流してやっているのでそこまで詰まることもなく、Progateをメモを取りつつ＆ローカルでも同じことをしつつ進める→ノートに纏めるといった感じで</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>何をしているのかもどうにか理解しつつ進められております</p>
<!-- /wp:paragraph -->

<!-- wp:heading -->
<h2>現状分からないこと</h2>
<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3>SCSS</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>CSSの書き方のうちの一つと言うイメージ。どういう風に働いてるのかはどうにか追いかけることが出来るけど自分では書けない。後程勉強します。</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3>データベース</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>概念はなんとなく理解できるけど、データの見方とか全然わからん。今やっていることで言えば\tweet_app\db\development.sqlite3ってヤツの事かな？Progateで言うとSQLってヤツがそうかな？？位の理解です。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p> 後程略ですが１章だけでもやって置けばよかったかな？ </p>
<!-- /wp:paragraph -->

<!-- wp:heading -->
<h2>rails console</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>とは言え寄り道すると果てしなくなりそうなのでRailsコースを進めます。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>newメソッドとかもやったやつだよねうんうん。と、プロゲート上では躓くこともなくローカルへ。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>コードを実行できるというconsole。サーバーの時とは違ってサクっと立ち上がりました。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>>post1 = Post.new(</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>そして引数として 「content: "今日から~」部分をコピペ</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3>文字化け？暴走？するターミナル</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>記号的なものがドバーっと表示された後、終了されているconsole。なんかミスったかな？と操作を繰り返すも同じ事に。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>コピペがあかんのか？と手入力するも]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]]略みたいになってconsoleが終了</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>どうやら日本語入力がダメなご様子。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>まず参考にしたのはこちら</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><a href="https://ja.stackoverflow.com/questions/42963/rails-new-%E3%81%A7%E3%82%A8%E3%83%A9%E3%83%BC-invalid-byte-sequence-in-windows-31j-%E3%81%8C%E3%81%A7%E3%81%BE%E3%81%99">https://ja.stackoverflow.com/questions/42963/rails-new-%E3%81%A7%E3%82%A8%E3%83%A9%E3%83%BC-invalid-byte-sequence-in-windows-31j-%E3%81%8C%E3%81%A7%E3%81%BE%E3%81%99</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>状況は違うのだけど、要はエンコードの問題っぽい？と言う事で、回答にあるように set RUBYOPT=-EUTF-8 と入力してみる</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>結果、貼り付け＆日本語入力は出来るようになったけど半角英数の入力自体を受け付けない状態に</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3>winの民である🐫</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>調べていくうちにmacとwinで対処方法が違う様子という事に気づきました。そしてwinでターミナルにコマンドプロンプトを使っている現状では完璧な解決方法がないらしいと言う事も。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ターミナルでの文字化け問題を解決するには「cloud9で開発する」等の方法があるようですが、一先ず現状でどうにか行ける方法も見つかったのでそちらで続けていくことに。</p>
<!-- /wp:paragraph -->

<!-- wp:heading -->
<h2>Windowsでrails consoleの文字化け問題を回避する方法</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>参考サイトはこちら</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><a href="https://qiita.com/jnchito/items/08b5be458134073c60e3#rails-console%E3%81%AE%E5%A0%B4%E5%90%88">https://qiita.com/jnchito/items/08b5be458134073c60e3#rails-console%E3%81%AE%E5%A0%B4%E5%90%88</a></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>C:\Users\(ユーザ名) に .irbrc と言う名前のファイルを作る→ IRB.conf[:USE_READLINE] = false と記述。以上です。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>これでターミナルが日本語入力を受け付けてくれるようになりました。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>ターミナル上では文字化けが発生するけどデータベースに渡す際には適切な文字コードを選択してくれている模様です。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>詳しくは上のリンクを読んでください。</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p></p>
<!-- /wp:paragraph -->

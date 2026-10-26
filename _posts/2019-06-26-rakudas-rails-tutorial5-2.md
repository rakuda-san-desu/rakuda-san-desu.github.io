---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜5.2"
date: "2019-06-26 15:10:53"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 810
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial5-2/"
---
<h2>5.2 Sassとアセットパイプライン</h2>
<h3>5.2.1 アセットパイプライン</h3>
アセットパイプラインとは、JavaScriptやCSSのアセットを最小化 (スペースや改行を詰めるなど＝minify) または圧縮して連結するもの
アセットパイプラインを理解するための3つの主要な機能を理解する
↓
<h4>そもそもアセット（asset）とは何ぞ</h4>
JavaScript・CSS・画像ファイルなどの静的コンテンツに関連する様々なファイル
<h4>アセットディレクトリとは何ぞ</h4>
Railsのアセットパイプラインには静的ファイルを目的別に分類する3つのディレクトリが使われている
<ul>
	<li>app/assets　→アプリケーション固有のアセット</li>
	<li>lib/assets　→自分たちの開発チームによって作成されたライブラリ用のアセット</li>
	<li>vendor/assets　→サードパーティのアセット</li>
</ul>
それぞれのディレクトリにはアセットクラス用のサブディレクトリが用意されている
→config  images  javascripts  stylesheets

5.1.2で作成したcustom.scssはsample_app固有のアセットなのでapp/assets/stylesheets/custom.scssに配置している
<h4>マニフェストファイルとは何ぞ</h4>
アセットディレクトリに配置されたCSSとJavaScriptのファイルをどのようにまとめるか指示するもの
→実際に処理を行うのはSprocketsというgem
→画像ファイルには適用されない
<pre class="lang:css decode:true " title="/sample_app/app/assets/stylesheets/application.css（CSSのマニフェストファイル）" >/*
 * This is a manifest file that'll be compiled into application.css, which will include all the files
 * listed below.
 *
 * Any CSS and SCSS file within this directory, lib/assets/stylesheets, or any plugin's
 * vendor/assets/stylesheets directory can be referenced here using a relative path.
 *
 * You're free to add application-wide styles to this file and they'll appear at the bottom of the
 * compiled file so the styles you add here take precedence over styles defined in any other CSS/SCSS
 * files in this directory. Styles in this file should be added after the last require_* statement.
 * It is generally better to create a new file per style scope.
 *
 *= require_tree .    #app/assets/stylesheets以下の全てのCSSファイルを読み込む指定
 *= require_self      #application.css自身も読み込み対象に含める指定
 */</pre> 
→Railsチュートリアルでは必要はないけど、自分で変更することも出来る
<h4>プリプロセッサエンジンとはなんぞ</h4>
それぞれのファイルを読み込んで（ブラウザが読み込めるように）変換する仕組み（ざっくり)
→どのプリプロセッサを使うかはファイルの拡張子で判断される（.sass .erbなど）
→繋げて実行することも出来る（順番が重要になる！）
例）foobar.js.erb.coffee
　→CoffeeScriptがJavaScriptに展開され、次にERbがHTMLに展開され、最終的にfoobarと言う名前のJavaScriptとしてブラウザへ出力される
<h4>本番環境での効率性</h4>
Asset Pipeline　→　開発時は読みやすさ重視で整理しておいたファイルを、本番環境では効率的に読み込まれる様に変換して出力してくれる便利な仕組み
<blockquote class="quote-rort">
Asset Pipelineがすべてのスタイルシートを1つのCSSファイル (application.css) にまとめ、すべてのJavaScriptファイルを1つのJSファイル (javascripts.js) にまとめてくれます。さらに、それらのファイルすべてに対して 不要な空白やインデントを取り除く処理を行い、ファイルサイズを最小化してくれます。結果として、開発環境と本番環境という、2つの異なった状況に対してそれぞれ最高の環境を提供してくれます。
</blockquote>
<h3>5.2.2 素晴らしい構文を備えたスタイルシート</h3>
Sassとは　→　ネスト（階層）や変数、@mixinなどの便利な機能が使えるcssの記法（ざっくり）
別途学習する方が絶対いいので内容は割愛
（Progateをやった時の学習メモなどを参照）

→app/assets/stylesheets/custom.scssをSass記法で書き直してみる
→　本文のリスト5.20と見比べて確認
（以下の演習とかぶってるけど）
<h4>演習</h4>
<blockquote class="quote-rort">5.2.2で提案したように、footerのCSSを手作業で変換してみましょう。具体的には、リスト 5.17の内容を１つずつ変換していき、リスト 5.20のようにしてみてください。</blockquote>
ネストを色分けするとこんな感じ↓
<a href="https://rakuda3desu.net/wp-content/uploads/2019/06/5.2.2-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/06/5.2.2-1.png" alt="" width="398" height="388" class="alignnone size-full wp-image-815" /></a>
<h3>まとめとか感想</h3>
アセットパイプラインは分かったようでよく分からないなんとなくわかった気持ちの物。
ドキュメントとかも読んで勉強しましょう🐫
SassもProgateでやった程度の知識しかないので別途勉強したい。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

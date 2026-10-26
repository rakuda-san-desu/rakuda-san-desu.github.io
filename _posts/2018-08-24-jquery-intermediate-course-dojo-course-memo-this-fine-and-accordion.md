---
layout: post
title: "jQuery中級コース＆道場コース｜メモ｜アラフォーthisとfineとアコーディオン"
date: "2018-08-24 16:07:18"
categories: ["jQuery", "Progate", "学習メモ"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 47
wordpress_status: "publish"
permalink: "/jquery-intermediate-course-dojo-course-memo-this-fine-and-accordion/"
---
<h3><a class="keyword" href="http://d.hatena.ne.jp/keyword/jQuery">jQuery</a>始めました</h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>コースは計算！って感じだったけど

<a class="keyword" href="http://d.hatena.ne.jp/keyword/jQuery">jQuery</a>コースは動きがあって🐫は楽しいです。

特に中級はサイトを作ってる感があって良いでした。

とは言え、楽しいけど馴染めてないので頭を使いますね。

中級コースは3週やってどうにか理解（消化は出来ていない模様）

道場コースはメモを見ながら何とかクリアでした。

一つ一つのメソッドやイベントがわかってきても

目的のためにどうやって組み立てるのかが難しいですね。

数学で言ったら「公式に当てはめる」ってことなのかな？
<h3>何度も詰まったthisとfine</h3>
<strong>this</strong> と <strong>fine</strong> 、詰まりまくりました。

初級の問題についてはさくさく進められたのだけど

中級の2章で出てきたときには「うむわからん」となりました。

これは「どう書いたらいいのかわからない」と言うより

「何を言われているのかわからない」状態。

とりあえず答えを写して進めちゃうも、何がどうなっているのかよくわからないのでした。
<h4>書いてみると理解できた気分になる法則</h4>
頭に残ってないのだなってことで

ポイントをメモに纏めながら初級を復習。

続いて中級も復習。

けどやっぱり何を言われているかがイマイチわからないので答えをコピペ。

更に答えをメモに取って、注釈をつけてみる。
<h4>6/9のhoverイベント</h4>
$('.lesson-hover').hover(
function() {
<strong>　　<span style="color: #286f2c;">$(this)</span>.<span style="color: #1464b3;">find('.text-contents')</span>.<span style="color: #ff5252;">addClass('text-active');</span></strong>

<strong>　<span style="color: #286f2c;">オンマウスになっている.lesson-hover</span>　<span style="color: #1464b3;">の中の.text-contentsに　<span style="color: #ff5252;">クラスtext-active</span></span><span style="color: #ff5252;">を付ける</span></strong>
},
function() {
$(this).find('.text-contents').removeClass('text-active');
}
);

なるほどー。となりました。

取り合えず、何が書かれているのかは理解。
<h4>8/9の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A5%B3%A1%BC%A5%C7%A5%A3%A5%AA%A5%F3">アコーディオン</a></h4>
納得が出来たので続きは答えを見なくてもできました。

$('.faq-list-item').click(function(){
<strong> <span style="color: #ff5252;">var $answer =</span><span style="color: #286f2c;"> $(this)<span style="color: #1464b3;">.</span></span><span style="color: #1464b3;">find('.answer');</span></strong>

<strong> <span style="color: #286f2c;">クリックした.faq-list-item</span>　<span style="color: #1464b3;">の中にある.answerを</span>　<span style="color: #ff5252;">$answerに代入</span></strong>

・・・

・・・

<strong><span style="color: #286f2c;"> $(this).</span><span style="color: #1464b3;">find('span')</span>.<span style="color: #ff5252;">text('-');</span></strong>

<strong><span style="color: #286f2c;">クリックした.faq-list-item　<span style="color: #1464b3;">の中にあるspanの</span>　<span style="color: #ff5252;">テキストを - に</span></span></strong>
}
});

これで繰り返しやっていくうちに覚えられそうです。

少なくともメモのどこを見ればいいかは把握。
<h3>openはどこから来てどこへ行くのか</h3>
学習コースの時は指示に従ってフムフムと書き進めていた<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A5%B3%A1%BC%A5%C7%A5%A3%A5%AA%A5%F3">アコーディオン</a>部分。

道場コースにきて疑問が。

<strong>「クラスopen」ってどこから来たの？</strong>

htmlのファイルを見ても<a class="keyword" href="http://d.hatena.ne.jp/keyword/css">css</a>のファイルを見ても

どこにも記述のない謎のクラス「open」

これはどちら様なんでしょうか。

と言うか、中級コースをやっていた時にアレ？と思いたいものです。

ちゃんと考えながらやらないから後で悩むことになるのだ🐫よ。
<h4>スライドを見返した結果</h4>
openの正体ははっきりくっきり説明されていた訳ではないのですが

（🐫には見つけられなかっただけかもしれないけど）

8/9のスライドをじっくり見直して、自分なりに理解した結果

<strong>openと言うクラスが存在するわけではなくhasClassで判定するためのスイッチのようなもの</strong>

となりました。

クリックした.faq-list-itemの中の.answerに.<strong>openが付いていたら</strong>

→.answerから.openを外して、.answerを隠して、<span>の文字を+に

クリックした.faq-list-itemの中の.answerに.<strong>openが付いていなければ</strong>

→.answerに.openを追加して（class="answer open"になる）、.answerを表示して、<span>の文字を-に

openを付けたり外したりすることで判定が変わる→開いたり閉じたりする

翻訳（？）したら分かった気分になる法則
<h4>検証も見てみました</h4>
つまりhtmlが書き換わってるのよね。と。

そういう処理がされているのよね。と。

ふと思いついてブラウザでHTMLを表示、検証モードにして<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A5%B3%A1%BC%A5%C7%A5%A3%A5%AA%A5%F3">アコーディオン</a>部分をクリック。

class="answer"にopenが付いたり消えたりしていました。

頭では分かっていたつもりだったけど、目にしてみるとすごい説得力。

ついでに他の部分も見てみたりして。

面白いでした。
<h3>今後の予定</h3>
ProgateではjQery上級→<a class="keyword" href="http://d.hatena.ne.jp/keyword/ruby">ruby</a>

同時進行で書籍「作りながら学ぶHTML/<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>デザインの教科書」→自サイトリニューアル

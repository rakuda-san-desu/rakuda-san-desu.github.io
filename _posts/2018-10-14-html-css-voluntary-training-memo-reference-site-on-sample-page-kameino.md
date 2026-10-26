---
layout: post
title: "HTML＆CSS自主練｜メモ｜サンプルページ『亀井野酒造』を作った際の参考サイトなど"
date: "2018-10-14 16:25:23"
categories: ["HTML&CSS", "学習メモ"]
tags: ["参考サイト", "自主練"]
published: true
wordpress_id: 35
wordpress_status: "publish"
permalink: "/html-css-voluntary-training-memo-reference-site-on-sample-page-kameino-brewed/"
---
<h3>サンプルページ『亀井野酒造』を作りました</h3>
<a href="https://rakuda-san-desu.github.io/sample2018-10-09/">サンプルページ　亀井野酒造</a>

<a href="https://rakuda-san.hatenablog.com/entry/2018/10/12/200124">HTML＆CSS自主練：サンプルページ『亀井野酒造』 - アラフォー専業主婦が緩くプログラミングを学ぶブログ</a>

手順としてはざっくり下記の流れです
<ol>
 	<li>何を作るか決める（山梨にある酒造って事にする）</li>
 	<li>情報収集（酒蔵のサイト巡り）</li>
 	<li>メインターゲットを考える（<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BB%A5%AB%A5%F3%A5%C9%A5%E9%A5%A4%A5%D5">セカンドライフ</a>を楽しむ世代）</li>
 	<li>イメージを固める（落ち着いた和風の雰囲気）</li>
 	<li>手書きでざっくりラフ</li>
 	<li><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EF%A5%A4%A5%E4%A1%BC%A5%D5%A5%EC%A1%BC%A5%E0">ワイヤーフレーム</a>を作る</li>
 	<li>画像を用意</li>
 	<li>コーディング</li>
 	<li>チェック×修正</li>
 	<li>アップ</li>
</ol>
<h4></h4>
<h4>デザインする上で気を付けた事</h4>
酒造のページって事で

成人男女、特に旅行などで訪れる人→<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%BB%A5%AB%A5%F3%A5%C9%A5%E9%A5%A4%A5%D5">セカンドライフ</a>を楽しむ世代

よってPCでも<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>でも見易くわかり易く操作し易いページになる様に。

LPページではなくてサイトのトップページとして作るけど、ページ内である程度の情報（所在地、営業時間、ネットショップ、直近イベント）が手に入る事。

特に<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>から見る場合、文字が小さくなりすぎないように。メニューのオープン・クローズも文字を付ける。
<h4></h4>
<h4><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EF%A5%A4%A5%E4%A1%BC%A5%D5%A5%EC%A1%BC%A5%E0">ワイヤーフレーム</a></h4>
今まではラフスケッチから勢いでコーディングって事しかしてこなかったのだけど

前回サイト模写をやって、ある程度しっかり<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EF%A5%A4%A5%E4%A1%BC%A5%D5%A5%EC%A1%BC%A5%E0">ワイヤーフレーム</a>を作って構造とかを考えてからコーディングした方がスムーズと気が付いたので作りました。

今後、お仕事としてサイトを作る時の事を考えるとあった方がいいと思うし。

使ってみるとカラーもしっかり決められてバランスも見られるので良いでした。

いくつか試して、使ったのはこちらのサービス。<cite class="hatena-citation"></cite>

<iframe class="embed-card embed-webcard" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" title="Online Diagram and Flowchart Software | Cacoo" src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fcacoo.com%2F" frameborder="0" scrolling="no"></iframe><cite class="hatena-citation"><a href="https://cacoo.com/">cacoo.com</a></cite>

作った<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EF%A5%A4%A5%E4%A1%BC%A5%D5%A5%EC%A1%BC%A5%E0">ワイヤーフレーム</a>はこちら。

<a href="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20181014/20181014152008.png"><img class="hatena-fotolife alignnone" title="f:id:rakuda-san-desu:20181014152008p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20181014/20181014152008.png" alt="f:id:rakuda-san-desu:20181014152008p:plain" width="278" height="1024" /></a><a href="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20181014/20181014152017.png"><img class="hatena-fotolife alignnone" title="f:id:rakuda-san-desu:20181014152017p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20181014/20181014152017.png" alt="f:id:rakuda-san-desu:20181014152017p:plain" width="114" height="1024" /></a>
<h4></h4>
<h4>チェック＆修正</h4>
思った通りの挙動になっているかとか、細かいバランスとかは勿論だけど

一番悩んだのが環境による見え方の違い。

とりあえず<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>で見た際の画面サイズは<a class="keyword" href="http://d.hatena.ne.jp/keyword/chrome">chrome</a>の検証からも確認できるので

サイズ感はそこでチェックしつつ。

ブラウザとOSによる違いはどうしたものか。と思っていたらこんなのを見つけました。

<iframe class="embed-card embed-webcard" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" title="Most Reliable Mobile App & Browser Testing Platform | BrowserStack" src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fwww.browserstack.com%2F" frameborder="0" scrolling="no"></iframe><cite class="hatena-citation"><a href="https://www.browserstack.com/">www.browserstack.com</a></cite>

各種ブラウザの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%EC%A5%F3%A5%C0%A5%EA%A5%F3%A5%B0">レンダリング</a>見本を一覧表示してくれるそうです。便利！

で、実際見た結果がこれ。

<a href="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20181014/20181014152802.png"><img class="hatena-fotolife alignnone" title="f:id:rakuda-san-desu:20181014152802p:plain" src="https://cdn-ak.f.st-hatena.com/images/fotolife/r/rakuda-san-desu/20181014/20181014152802.png" alt="f:id:rakuda-san-desu:20181014152802p:plain" width="230" height="1024" /></a>

<a class="keyword" href="http://d.hatena.ne.jp/keyword/IE">IE</a>はflexboxがやばいと聞いていたので覚悟はしていたのだけど<a class="keyword" href="http://d.hatena.ne.jp/keyword/FireFox">FireFox</a>どうしてこうなった？

FFを導入してみた所、普通に表示されてるけどはてさて。
<h5>構文チェック</h5>
よくわからないので一先ず構文チェックと言うものを行いました。

<a href="https://validator.w3.org/unicorn/?ucn_lang=ja">https://validator.w3.org/unicorn/?ucn_lang=ja</a>

すると画像指定のパスの/と\が逆になっておりましたよ。コードを書く際にパスをコピーして貼り付けしたのが良くなかったみたいです。

ここを直したらFFでの表示は治りました。

ほかにもいくつかミスが指定されていたので修正。

h1使いすぎとか、逆にh1がないよ！とか言われたところもあったのですがこれはスルーで。

また、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%CE%A1%BC%A5%DE%A5%E9%A5%A4%A5%BA">ノーマライズ</a>や<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%E9%A5%B0%A5%A4%A5%F3">プラグイン</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>ファイルにも不備があるとの事ですがここもスルーさせて頂く事に。
<h5>flexboxとかを古い<a class="keyword" href="http://d.hatena.ne.jp/keyword/IE">IE</a>に対応させる</h5>
学習に使った書籍（作りながら学ぶHTML/<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>デザインの教科書）に古い<a class="keyword" href="http://d.hatena.ne.jp/keyword/IE">IE</a>に未対応の<a class="keyword" href="http://d.hatena.ne.jp/keyword/CSS">CSS</a>を認識させるScriptが紹介されていたのでそれを導入。

<iframe class="embed-card embed-webcard" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" title="HTML5入門 - IE対応「html5shiv.js」の使い方 - Webkaru" src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fwebkaru.net%2Fhtml5%2Fie-html5shiv%2F" frameborder="0" scrolling="no"></iframe><cite class="hatena-citation"><a href="https://webkaru.net/html5/ie-html5shiv/">webkaru.net</a></cite>

これで前述のBrowserStackでチェックをしてみるもレイアウトは崩れたままでした。

なのでfrexboxを対応させるという部分に絞って検索。

<iframe class="embed-card embed-webcard" style="display: block; width: 100%; height: 155px; max-width: 500px; margin: 10px 0px;" title="IE8・9にもFlexboxを対応させる、flexibility.jsがとっても便利！" src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fwww.webcreatorbox.com%2Ftech%2Fie8-flexbox" frameborder="0" scrolling="no"></iframe><cite class="hatena-citation"><a href="https://www.webcreatorbox.com/tech/ie8-flexbox">www.webcreatorbox.com</a></cite>

これは便利そう！と言う事で早速<span style="color: #000000;">flexibility.jsを</span>導入。

そして前述の（略

あれ？表示崩れたままですぞ。

そこでPCに<a class="keyword" href="http://d.hatena.ne.jp/keyword/IE">IE</a>がインストされたままなのを思い出して起動させてみました。

バージョンは11。

11では普通に表示。

そして<a class="keyword" href="http://d.hatena.ne.jp/keyword/IE">IE</a>の開発者ツールから古いバージョンの<a class="keyword" href="http://d.hatena.ne.jp/keyword/IE">IE</a>で見た場合の再現が出来ると言う事なのでそちらからチェック。

10では普通に表示されましたがやはり9以前のバージョンで表示が崩れますね。

うーん。

ターゲット年齢を高く設定したって事もあって<a class="keyword" href="http://d.hatena.ne.jp/keyword/IE">IE</a>での表示にもこだわりたかったのだけど、多分他にもやり方はあると思うのだけど

ブラウザのシェア率とか、<a class="keyword" href="http://d.hatena.ne.jp/keyword/IE">IE</a>のサポート情報を見るにさすがに9以降は捨ててもいいんじゃないかと言う思いが湧き上がって抑えることが出来ないので今回はここまでにします！
<h3>今後の課題</h3>
精度を上げつつスピードもアップ→数をこなしてどんどん慣れたい

ググり力アップ→アンテナを伸ばして検索する単語・文字列を蓄えたい

画像サイズにこだわる→<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%DE%A5%DB">スマホ</a>で見ると若干ボケる<a class="keyword" href="http://d.hatena.ne.jp/keyword/Retina">Retina</a>とか調べて考える

時間を測っておく

デザインの引き出しも増やしたいし使える技も増やしたいし

アレもしたいしコレもしたいよね！

一先ず具体的に浮かぶことからひとつづつ、一歩一歩進みたいです🐫

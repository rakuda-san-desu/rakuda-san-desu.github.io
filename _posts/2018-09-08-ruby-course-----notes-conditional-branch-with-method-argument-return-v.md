---
layout: post
title: "Ruby学習コースⅠ～Ⅲ｜メモ｜メッソドと引数と戻り値とreturnでの条件分岐"
date: "2018-09-08 20:15:59"
categories: ["Progate", "Ruby", "学習メモ"]
tags: ["Progate", "学習サイト"]
published: true
wordpress_id: 42
wordpress_status: "publish"
permalink: "/ruby-course-%e2%85%a0-%e2%85%a2-notes-conditional-branch-with-method-argument-return-value-and-return/"
---
<h3><a class="keyword" href="http://d.hatena.ne.jp/keyword/Ruby">Ruby</a>始めました</h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>＆<a class="keyword" href="http://d.hatena.ne.jp/keyword/jQuery">jQuery</a>はさっくり目に終わらせて

とうとう<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ruby">Ruby</a>コースに入りました。

プログラム感あるよね付いて行けるかな？！

とドキドキならくだ🐫でしたが

<a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>をやっていたおかげかそこまで躓くこともなく進めております。

まぁ言っても初歩の初歩だもんね。

🐫頑張る。
<h3>ちょっと悩んだreturnでの条件分岐</h3>
こうすれば出来るっていうのは分かったけど

なんで出来るのかが良くわからなかったreturnでの条件分岐ですが

メモをノートに纏めていたら突然ストンと納得したので纏めてみます

その前にメソッドと引数と戻り値についてさらっと復習
<h4>メソッド</h4>
繰り返し同じ処理をするときなどに便利な機能

<strong>def<span style="font-size: 80%;">（メソッドの実行を宣言して）　</span>メソッド名を決めて　内容（処理）を書いて　end<span style="font-size: 80%;">（閉じる）</span></strong>

<strong>呼び出す（実行）</strong>

def wallet<span style="font-size: 80%;">（walletと言う名前のメソッドを宣言）</span>

<span style="color: #999999;"> #ここから処理の内容</span>

puts "私の財布です"　<span style="font-size: 80%;">（私の財布です　と出力）</span>

<span style="color: #999999;">  ＃ここまで処理の内容</span>

<span style="color: #000000;">end<span style="font-size: 80%;">（メソッドの宣言を修了）</span></span>

wallet<span style="font-size: 80%;">（”私の財布です”と出力するように設定されたwalletと言う名前のメソッドを呼び出す）</span>

↓<span style="font-size: 80%;">出力</span>

私の財布です
<h4>引数</h4>
メソッドを呼び出した時に値を設定して処理に代入（引数展開）できる便利な機能

複数設定することもできる

→複数の引数がある時、値の並びは引数の並びと同じになるが

<strong>引数の数が多くなると分かりづらくなるのでキーワードとしてセット</strong>する

def wallet(bills: , coins:) <span style="font-size: 80%;">（bills とcoinと言う2つの引数をセット）</span>

puts "私の財布です"

puts "紙幣で#{bills}円あります"　<span style="font-size: 80%;">（引数billsを展開）</span>

puts "硬貨で#{coins}円あります"　<span style="font-size: 80%;">（引数coinsを展開）</span>

end

wallet(bills: 12000 <span style="font-size: 80%;">（引数billsに12000を渡す）</span>, coins: 372<span style="font-size: 80%;">（引数coinsに372を渡す）</span>)

wallet

↓<span style="font-size: 80%;">出力</span>

私の財布です

紙幣で12000円あります　<span style="font-size: 80%;">(billsに渡された12000が代入されている）</span>

硬貨で372円あります　　<span style="font-size: 80%;">（coinsに渡された372が代入されている）</span>
<h4></h4>
<h4>戻り値</h4>
呼び出し元に値を返す

def wallet(bills: , coins:)

<strong><span style="color: #ff0000;"> return</span> <span style="color: #000000;">bills</span> <span style="color: #2196f3;">+</span> <span style="color: #000000;">coins</span></strong>

end

sum = wallet(bills: 12000, coins: 372)

<span style="font-size: 80%;">　　（walletに12000を渡した<strong>dills</strong>と372を渡した<strong>coins</strong>を<strong><span style="color: #2196f3;">足して</span><span style="color: #ff0000;">返す</span></strong>）←これをsumに代入</span>

puts  "私の所持金は#{sum}円です"

↓<span style="font-size: 80%;">出力</span>

私の所持金は12372円です
<h4>真偽値を返すreturn</h4>
条件式をreturnの値にすると呼び出し元の値の真偽値を返してくる

※真偽値を返すreturnを使う場合にはメソッド名の末尾に<strong>？</strong>を付ける

def  <span lang="en">budget?(wallet)</span>

return wallet >= 10000<span style="font-size: 80%;">（条件式　walletは10000以上）</span>

end

puts budget?(8000)<span style="font-size: 80%;">（呼び出し部分　walletに渡す値は8000）</span>

<strong>（条件を満たさない）</strong>

↓<span style="font-size: 12.8px;">出力</span>

falst<span style="font-size: 80%;">（条件を満たしていればtrue）</span>

さらさらっとですがこんな理解です。
<h4>returnの特性と複数のreturnを組み合わせた条件分岐</h4>
returnは戻り値を返すだけではなく

<strong>メソッドの処理を終わらせることが出来る為</strong>

<strong>if文と組み合わせて条件分岐を作ることが出来る</strong>

よくわからないので例に沿って考えてみる

def  <span lang="en">budget(wallet)</span>

<span style="text-decoration: underline;">  if wallet >= 10000　　<span style="font-size: 80%;">（walletが10000以上なら）</span></span>

<span style="text-decoration: underline;"> 　 return "買い物して良い"　<span style="font-size: 80%;">（買い物して良い　を返す）</span></span>

<span style="text-decoration: underline;">  end</span>

return "我慢しましょう"　<span style="font-size: 80%;">（我慢しましょう　を返す）</span>

end

puts budget(12000)

↓<span style="font-size: 80%;">出力</span>　<span style="font-size: 80%;">walletが10000以上なのでreturn"買い物して良い"が実行され処理が終了する</span>

<span style="font-size: 80%;">　　　処理が終了しているためreturn"我慢しましょう"は実行されない</span>

買い物して良い

puts budget(8000)

↓<span style="font-size: 80%;">出力</span>　<span style="font-size: 80%;">walletが10000以下なのでreturn"買い物して良い"は返らず<strong>処理が続く</strong></span>

<span style="font-size: 80%;">　　　  return "我慢しましょう" が実行される</span>

我慢しましょう

初め、何が引っ掛かったのかと言うと

<strong>endの後なのに処理が続くの？</strong>と言うところ。

ちょっと落ち着いて考えればわかる事なんだけど

return "我慢しましょう"の前についているendはif文（下線部）を閉じるendで

メソッド自体を閉じるendではないんですね。

なぜか、手書きで、メモではなくてノートに清書するまで気づきませんでした。

らくだ🐫はもっと、<strong>スコープと言うものを意識</strong>した方が良さそうです。

あと、Progateは指示に従ってサクサク進めることが出来ちゃうけど

流してやってたらダメ！ってことですよね。

とは言え拘り過ぎても進まなくなると思うし

たまたま気づけてラッキー✨と思っておりますﾃﾍﾍﾟﾛ

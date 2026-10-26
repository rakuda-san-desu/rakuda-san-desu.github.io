---
layout: post
title: "Rails｜Railsチュートリアル2.1.1～2.4.1｜2.1と2.2で詰まる　追記アリ（20190228）"
date: "2019-02-26 18:02:26"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["AWS", "cloud9", "Railsチュートリアル", "参考サイト"]
published: true
wordpress_id: 355
wordpress_status: "publish"
permalink: "/rails-railstutorial211-241/"
---
<h2>分からないことが沢山</h2>
<a href="https://rakuda3desu.net/rails-railstutorial151-161/" target="_blank" rel="noopener noreferrer">前回からの続き</a>

Progateさんの様に1から10まで説明してくれる訳でもないし
説明が硬めの文章なので
理解と言うか頭に入ってくるのに時間がかかりますね
まぁしっかり頭を使いたまえよ🐫と言う事で頑張りたい
<h2>詰まった所など</h2>
<h3>2.1｜Herokuにプッシュ出来ない</h3>
gemのインストールもストレートに行かなくて
でもココはまぁ、前回同様先にupdateしてからやったんですが
heroku createでコマンドがないですが？と言うエラー
heroku -vするも同じエラーなのでインストールを実行
するとディレクトリが空じゃないんですが？と言うエラーが出るんだけど使えるように
謎です

で、取り合えずそのまま進めちゃってて今これを書く時にググったりしていたら見つけました

[blogcard url="https://qiita.com/bashinkei/items/c8d9bd1e622811e9c950"]

しかしやり方が良くわかりません💧とりあえず適宜インストールで！😢

<strong>追記201902208</strong>

上記の件、出来ました！

リンク先ページの
「「$HOME/.bash_profile」に$PATHの設定を追加」って言う部分で
ターミナルから一生懸命（？）ファイルの場所を探したけど見つけられなかったんですが
ブラウザ上で画面をいじっていたら見つけることが出来ました
<a href="https://rakuda3desu.net/wp-content/uploads/2019/02/bash_profile.png"><img class="alignnone wp-image-360 size-full" src="https://rakuda3desu.net/wp-content/uploads/2019/02/bash_profile.png" alt="AWS　cloud9の画面です" width="661" height="457" /></a>
<ul>
 	<li>赤で囲んだ部分の歯車マークをクリック</li>
 	<li>緑で囲んだ部分にチェックを付ける</li>
 	<li>水色で囲んだ部分のファイルを開く</li>
 	<li>指定部分にコードを追加</li>
</ul>
で、
再起動でOKになるのですが再起動の仕方も良くわからなかったんですよね💧
でも30分触らなければセッション切れる設定にした記憶があるし
ブラウザ閉じて
しばらく時間をおいて戻ってきたら（ログインしなおしたら）herokuにログインし直さなくてもコマンドが使えるようになっておりました
と言う訳で解決しました！ありがたや！！
<h3>2.2｜<span class="hll">rails generateでエラー</span></h3>
rails generate scaffold User name:string email:stringするじゃろ？
エラーが起きてちゃんとしたファイルが作れません
こんなやつ
/usr/local/rvm/gems/ruby-2.4.0/gems/spring-2.0.2/lib/spring/configuration.rb:37:in `pwd': No such file or directory - getcwd (Errno::ENOENT)

何回かやり直してお手上げだったので調べました

[blogcard url="https://qiita.com/m1kw/items/0163c3720bf5b3b89877"]

こちらで解決できました！

因みにherokuにデプロイ時のWARNINGログに関しても解決方法が乗ってるんですが
チュートリアル本文で「今は放置でいいよ」的に書いてあるので今は放置にします！

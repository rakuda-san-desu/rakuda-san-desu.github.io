---
layout: post
title: "Win10HomeでDockerを使いたい！"
date: "2020-08-24 16:38:48"
categories: ["学習メモ", "未分類"]
tags: ["Docker", "Win10Home", "WSL2"]
published: true
wordpress_id: 1609
wordpress_status: "publish"
permalink: "/win10home-docker/"
---
タイトル通りです

WindowsのHomeなエディションでDockerを使おうとすると以前はなかなかつらかったと聞くのだけど
WSL2というものが導入されて手軽感が増したとか。
とは言えご家庭向けなマシンスペックのらくだ🐫環境ですので
どこまで耐えうるのかという問題もありつつチャレンジ！
<h2>WSL2を導入する</h2>
自動更新でのWindows10の最新バージョンは2020年8月現在1909なんだけど
WSL2を動かすには手動でバージョンアップが必要（2020/08/23現在）との事で
下記を参考にやっていきます
[blogcard url="https://qiita.com/tomokei5634/items/27504849bb4353d8fef8"]
<h4>WSL2 を既定のバージョンとして設定する際のエラー</h4>
上記の通りに進めて
<pre class="lang:default decode:true " title="PowerShell" >wsl --set-default-version 2</pre> 
これを実行したところ、下記のエラー
 
<pre class="lang:default decode:true " title="PowerShell" >wsl : 用語 'wsl' は、コマンドレット、関数、スクリプト ファイル、または操作可能なプログラムの名前として認識されません。
名前が正しく記述されていることを確認し、パスが含まれている場合はそのパスが正しいことを確認してから、再試行してください
。
発生場所 行:1 文字:1
+ wsl --set-default-version 2
+ ~~~
    + CategoryInfo          : ObjectNotFound: (wsl:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException</pre> 
wsl自体がないという雰囲気かな？
<h3>WSL1をインストール</h3>
[blogcard url="https://qiita.com/Aruneko/items/c79810b0b015bebf30bb"]
こちらを参考にWSLをインストール
<h4>Windows Subsystem for Linux</h4>
<a href="https://qiita.com/Aruneko/items/c79810b0b015bebf30bb">引用元ページ</a>
<blockquote>Windows Subsystem for Linux (Beta)を探し、チェックを入れます。</blockquote>
スタートボタン右クリック
↓
アプリと機能
↓
右上のプログラムと機能（らくだの環境では下部でした）
↓
一覧からWindowsの機能の有効化または無効化
↓
Windows Subsystem for Linux (Beta)を探し、チェックを入れます。
って設定をするのだけど
らくだ🐫の環境では
「Linux用Windowsサブシステム」ってなってたのでちょっとわかりにくかったでした
<img src="https://rakuda3desu.net/wp-content/uploads/2020/08/wsl-01.png" alt="" width="591" height="518" class="alignnone size-full wp-image-1614" />
<h3>WSL2のインストールに戻る</h3>
戻る
書いてある通りにやってるつもりなのにうまくいかないなー？だったのだけど
<ul>
	<li>共用アカウント→管理者　自分のアカウント→標準アカウントだったのを
自分のアカウントを管理者に変更</li>
	<li>コマンド実行ごとに再起動</li>
</ul>
で、できました！
<h2>Dockerを導入する</h2>
先に<a href="https://hub.docker.com/" rel="noopener noreferrer" target="_blank">DockerHub</a>にアカウントを作っておく
[blogcard url="https://qiita.com/KoKeCross/items/a6365af2594a102a817b"]
で、こちらを参考に進めていきます
特に問題なく書いてある通りでした！

これでご家庭スペックのWindows10HomeなPCでDockerを使う準備が整いました！

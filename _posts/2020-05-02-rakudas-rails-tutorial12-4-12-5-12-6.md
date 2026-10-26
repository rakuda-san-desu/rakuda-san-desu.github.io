---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜12.4 （と12.5と12.6）"
date: "2020-05-02 15:07:05"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1459
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial12-4-12-5-12-6/"
---
<h2>12.4 本番環境でのメール送信 (再掲)</h2>
本番環境のセットアップは11章で終わってるのでマージしてデプロイ
随時コミットはしているので
テストしてgit statusしてコミット忘れないかチェックしてブランチのままpushして
GitHubでプルリクしてマージして戻ってプルするいつもの流れ
<h3>Herokuへデプロイ</h3>
<pre class="lang:default decode:true " title="ターミナル" >$ rails test
$ git push
$ git push heroku
$ heroku run rails db:migrate</pre> 
→パスワードの再設定を試してみる
<img src="https://rakuda3desu.net/wp-content/uploads/2020/04/12.4-1.png" alt="" width="940" height="426" class="alignnone size-full wp-image-1462" />
できた！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>production環境でユーザー登録を試してみましょう。ユーザー登録時に入力したメールアドレスにメールは届きましたか?</li>
	<li>メールを受信できたら、実際にメールをクリックしてアカウントを有効化してみましょう。また、Heroku上のログを調べてみて、有効化に関するログがどうなっているのか調べてみてください。ヒント: ターミナルからheroku logsコマンドを実行してみましょう。</li>
	<li>アカウントを有効化できたら、今度はパスワードの再設定を試してみましょう。正しくパスワードの再設定ができたでしょうか?</li>
</ol>
</blockquote>
1.
<pre class="lang:default decode:true " title="ターミナル" ># 空いてるメアドがなかったのでHerokuのDBをいったんリセット
$ heroku pg:reset DATABASE

# からの再作成
$ heroku run rails db:migrate</pre> 
Heroku上のアプリにアクセス→新規登録→メール届く

2.
有効化に関するログざっくり

サインインページから情報を受け取る
有効化URLを乗せたメールを送信
有効化URLからgetのリクエスト→AccountActivationsController#edit（emailとトークンの値を渡す）
認証完了！”activated” = ‘t’, “activated_at” = ‘認証時のタイムスタンプ’にDBが更新される
ユーザーページにリダイレクトされる

3.
できた！
<h2>12.5 最後に</h2>
12章のまとめと今後の予告！
<blockquote class="quote-rort">残りの章では、Twitterのようなマイクロポスト機能 (第13章) と、フォロー中のユーザーの投稿を表示するステータスフィード機能 (第14章) を実装していきましょう。</blockquote>
あと2章と言えども内容が濃ゆそう！！
<h2>12.6 証明: 期限切れの比較</h2> 
<pre class="toolbar:2 lang:default decode:true " >reset_sent_at < 2.hours.ago</pre> 
についての補足説明
むずかしいすうがくのはなし（？）っぽいけど
要は
<blockquote class="quote-rort"><記号を「〜より少ない」ではなく「〜より早い時刻」と解釈</blockquote>
というお話
<h3>まとめとか感想</h3>
これでとうとうユーザー情報関連の部分が終わりました！
長かったね！！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

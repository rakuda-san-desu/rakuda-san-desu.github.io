---
layout: page
title: "らくだ🐫にもできるRailsチュートリアルのまとめ"
date: "2019-05-09 18:06:34"
categories: []
tags: []
published: true
wordpress_id: 535
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial/"
---
<h3>らくだ🐫の環境・状況</h3>
<ul>
	<li>win/cloud9(chromeから）/GitHub</li>
	<li>Progateで事前学習を推奨されているコースを勉強した</li>
	<li>Railsチュートリアル1週目を何とか終わらせた（読み終わった）</li>
	<li>わかばちゃんでGitの勉強をした</li>
</ul>
こんな感じの「ド」が付く素人・らくだ🐫が勉強するRailsチュートリアル（2週目・Rails 5.1 対応 第4版）の学習記録をまとめました。
自分用に記録していますが、お役に立つことがあれば幸いです。
途中からi18nを導入して適宜日本語化をしています影響で本文とコードが違う部分があります→t('.hogehoge')とかです

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです
<h2>第1章ゼロからデプロイまで</h2>
ゼロから始めて基本的な操作を体験する感じ

<a href="https://rakuda3desu.net/rakudas-rails-tutorial1-1-1-2/">らくだ🐫にもできるRailsチュートリアル｜1.1と1.2 さっそく動かす</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial-1-3/">らくだ🐫にもできるRailsチュートリアル｜1.3 最初のアプリケーション</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial-1-4/">らくだ🐫にもできるRailsチュートリアル｜1.4 Gitによるバージョン管理</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial1-5-1-6/">らくだ🐫にもできるRailsチュートリアル｜1.5（と1.6） デプロイする</a>
<h2>第2章Toyアプリケーション</h2>
scaffoldジェネレータと言うものを使ってもうちょっと踏み込む感じ

<a href="https://rakuda3desu.net/rakudas-rails-tutorial2-1/">らくだ🐫にもできるRailsチュートリアル｜2.1 アプリケーションの計画</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial2-2/">らくだ🐫にもできるRailsチュートリアル｜2.2 Usersリソース</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial2-3/">らくだ🐫にもできるRailsチュートリアル｜2.3（と2.4）Micropostsリソース</a>
<h2>第3章ほぼ静的なページの作成</h2>
ここから本番！の、入り口！静的なページや初めてのテスト

<a href="https://rakuda3desu.net/rakudas-rails-tutorial3-1/">らくだ🐫にもできるRailsチュートリアル｜3.1 セットアップ</a>
<a href="https://rakuda3desu.net/rrakudas-rails-tutorial3-2/">らくだ🐫にもできるRailsチュートリアル｜3.2 静的ページ</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial3-3/">らくだ🐫にもできるRailsチュートリアル｜3.3 テストから始める</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial3-4/">らくだ🐫にもできるRailsチュートリアル｜3.4 少しだけ動的なページ</a>
<h2>第4章Rails風味のRuby</h2>
理解を深めるためにもRubyの要素を合わせて学んでいく感じ
いかにも勉強的な感じが多くて苦手なパートなので頑張りたい

<a href="https://rakuda3desu.net/rakudas-rails-tutorial4-1/">らくだ🐫にもできるRailsチュートリアル｜4.1 動機</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial4-2/">らくだ🐫にもできるRailsチュートリアル｜4.2 文字列とメソッド</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial4-3/">らくだ🐫にもできるRailsチュートリアル｜4.3 他のデータ構造</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial4-4/">らくだ🐫にもできるRailsチュートリアル｜4.4 Rubyにおけるクラス</a>
<h2>第5章レイアウトを作成する</h2>
Bootstrapを導入したりパーシャルの使い方を覚えてapplication.html.erbをリファクタリングしたりしていくっぽい

<a href="https://rakuda3desu.net/rakudas-rails-tutorial5-1/" rel="noopener noreferrer" target="_blank">らくだ🐫にもできるRailsチュートリアル｜5.1 構造を追加する</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial5-2/">らくだ🐫にもできるRailsチュートリアル｜5.2 Sassとアセットパイプライン</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial5-3/">らくだ🐫にもできるRailsチュートリアル｜5.3 レイアウトのリンク</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial5-4/">らくだ🐫にもできるRailsチュートリアル｜5.4（と5.5） ユーザー登録: 最初のステップ</a>
<h2>第6章 ユーザーのモデルを作成する</h2>
ユーザー登録ページを作っていく
まずはユーザー用のデータモデルの作成と、データを保存する手段の確保について

<a href="https://rakuda3desu.net/rakudas-rails-tutorial6-1/">らくだ🐫にもできるRailsチュートリアル｜6.1 Userモデル</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial6-2/">らくだ🐫にもできるRailsチュートリアル｜6.2 ユーザーを検証する</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial6-3/">らくだ🐫にもできるRailsチュートリアル｜6.3（と6.4）セキュアなパスワードを追加する</a>
<h2>第7章　ユーザー登録</h2>
ユーザー登録機能を追加していく

<a href="https://rakuda3desu.net/rakudas-rails-tutoria7-1/" rel="noopener noreferrer" target="_blank">らくだ🐫にもできるRailsチュートリアル｜7.1 ユーザーを表示する</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial7-2/">らくだ🐫にもできるRailsチュートリアル｜7.2 ユーザー登録フォーム</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial7-3/">らくだ🐫にもできるRailsチュートリアル｜7.3 ユーザー登録失敗</a>
<h2>第8章　基本的なログイン機構</h2>
ユーザーがログインやログアウトを行えるようにしていく

<a href="https://rakuda3desu.net/rakudas-rails-tutorial8-1/">らくだ🐫にもできるRailsチュートリアル｜8.1 セッション</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial8-2/">らくだ🐫にもできるRailsチュートリアル｜8.2 ログイン</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial8-3-8-4/">らくだ🐫にもできるRailsチュートリアル｜8.3（と8.4）</a>
<h2>第9章　発展的なログイン機構</h2>
ユーザーのログイン情報を任意でオン/オフできるようにする

<a href="https://rakuda3desu.net/rakudas-rails-tutorial9-1/">らくだ🐫にもできるRailsチュートリアル｜9.1　Remember me 機能</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial9-2/">らくだ🐫にもできるRailsチュートリアル｜9.2 [Remember me] チェックボックス</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutoria9-3-9-4/" title="らくだにもできるRailsチュートリアル｜9.3（と9.4）">らくだ🐫にもできるRailsチュートリアル｜9.3（と9.4） [Remember me] のテスト</a>
<h2>第10章　ユーザーの更新・表示・削除</h2>
RESTアクションを完成させ、ユーザーの更新・表示・削除をできるようにする

<a href="https://rakuda3desu.net/rakudas-rails-tutoria10-1/">らくだ🐫にもできるRailsチュートリアル｜10.1 ユーザーを更新する</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial10-2/">らくだ🐫にもできるRailsチュートリアル｜10.2 認可</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial10-3/">らくだ🐫にもできるRailsチュートリアル｜10.3 すべてのユーザーを表示する</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial10-4-10-5/">らくだ🐫にもできるRailsチュートリアル｜10.４（と10.5） ユーザーを削除する</a>
<h2>第11章　アカウントの有効化</h2>
メール認証を使ってアカウントを有効化出来るようにする

<a href="https://rakuda3desu.net/rakudas-rails-tutorial11-1/">らくだ🐫にもできるRailsチュートリアル｜11.1 AccountActivationsリソース</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial11-2/">らくだ🐫にもできるRailsチュートリアル｜11.2 アカウント有効化のメール送信</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial11-3/" title="らくだにもできるRailsチュートリアル｜11.3">らくだ🐫にもできるRailsチュートリアル｜11.3 アカウントを有効化する</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial11-4-11-5/" title="らくだ🐫にもできるRailsチュートリアル｜11.4（と11.5）">らくだ🐫にもできるRailsチュートリアル｜11.4（と11.5）本番環境でのメール送信</a>
<h2>第12章　パスワードの再設定</h2>
メール認証を使ってアカウントを再設定出来るようにする

<a href="https://rakuda3desu.net/rakudas-rails-tutorial12-1/" title="らくだ🐫にもできるRailsチュートリアル｜12.1">らくだ🐫にもできるRailsチュートリアル｜12.1 PasswordResetsリソース</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial12-2/" title="らくだ🐫にもできるRailsチュートリアル｜12.2">らくだ🐫にもできるRailsチュートリアル｜12.2 12.2 パスワード再設定のメール送信</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial12-3/" title="らくだ🐫にもできるRailsチュートリアル｜12.3">らくだ🐫にもできるRailsチュートリアル｜12.3  パスワードを再設定する</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial12-4-12-5-12-6/" title="らくだ🐫にもできるRailsチュートリアル｜12.4 （と12.5と12.6）">らくだ🐫にもできるRailsチュートリアル｜12.4 12.4 本番環境でのメール送信 (再掲) （と12.5と12.6）</a>
<h2>第13章　ユーザーのマイクロポスト</h2>
140文字の投稿（マイクロポスト）を色々する

<a href="https://rakuda3desu.net/rakudas-rails-tutorial13-1/">らくだ🐫にもできるRailsチュートリアル｜13.1 Micropostモデル</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial13-2/" title="らくだ🐫にもできるRailsチュートリアル｜13.2">らくだ🐫にもできるRailsチュートリアル｜13.2 マイクロポストを表示する</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial13-3/" title="らくだ🐫にもできるRailsチュートリアル｜13.3">らくだ🐫にもできるRailsチュートリアル｜13.3 マイクロポストを操作する</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial13-4-13-5/" title="らくだ🐫にもできるRailsチュートリアル｜13.4（と13.5）">らくだ🐫にもできるRailsチュートリアル｜13.4 マイクロポストの画像投稿</a>
<h2>第14章　ユーザーをフォローする</h2>
SNSのメインの仕組みともいえる他のユーザーをフォロー（及びフォロー解除）の仕組みと
フォローしているユーザーの投稿をフィードに表示したりとかしていく

<a href="https://rakuda3desu.net/rakudas-rails-tutorial14-1/">らくだ🐫にもできるRailsチュートリアル｜14.1 Relationshipモデル</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial14-2/" title="らくだ🐫にもできるRailsチュートリアル｜14.2">らくだ🐫にもできるRailsチュートリアル｜14.2 [Follow] のWebインターフェイス</a>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial14-3/" title="らくだ🐫にもできるRailsチュートリアル｜14.3">らくだ🐫にもできるRailsチュートリアル｜14.3 ステータスフィード</a>
<h2>番外編</h2>
passwordのバリデーションをアレンジする（正規表現についてなど）
<a href="https://rakuda3desu.net/out-of-rakudas-rails-tutoria-password-validation/">らくだ🐫にもできるRailsチュートリアル｜番外編・パスワードのバリデーション</a>

Gem I18nを使ってエラーメッセージなどを日本語化する
<a href="https://rakuda3desu.net/out-of-rakudas-rails-tutorial-i18n/">らくだ🐫にもできるRailsチュートリアル｜番外編・I18n</a>

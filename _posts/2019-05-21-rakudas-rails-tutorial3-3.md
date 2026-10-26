---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜3.3"
date: "2019-05-21 18:35:41"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者", "参考サイト"]
published: true
wordpress_id: 672
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial3-3/"
---
<h2>3.3 テストから始める</h2>
テストのタイミングのガイドライン
<blockquote class="quote-rort">
<ul>
	<li>アプリケーションのコードよりも明らかにテストコードの方が短くシンプルになる (=簡単に書ける) のであれば、「先に」書く</li>
	<li>動作の仕様がまだ固まりきっていない場合、アプリケーションのコードを先に書き、期待する動作を「後で」書く</li>
	<li>セキュリティが重要な課題またはセキュリティ周りのエラーが発生した場合、テストを「先に」書く</li>
	<li>バグを見つけたら、そのバグを再現するテストを「先に」書き、回帰バグを防ぐ体制を整えてから修正に取りかかる</li>
	<li>すぐにまた変更しそうなコード (HTML構造の細部など) に対するテストは「後で」書く</li>
	<li>リファクタリングするときは「先に」テストを書く。特に、エラーを起こしそうなコードや止まってしまいそうなコードを集中的にテストする</li>
</ul>
</blockquote>
<h4>リファクタリングとは何ぞ</h4>
プログラムの動きは変えずにソースを整理して見やすくする事的な感じ
<h3>3.6.1 minitest reporters</h3>
<del datetime="2019-05-24T12:20:49+00:00">テストを実行する前にちょっと先走って
<a href="https://railstutorial.jp/chapters/static_pages?version=5.1#sec-minitest_reporters" rel="noopener noreferrer" target="_blank">3.6.1</a>に書いてあるこちらの設定をしておきます
テスト結果に色が付くので見やすくなります</del>
2019.05.24
後から気づいたんですがこの内容ってmasterブランチでやっておく様に本文に書いてあるんですね。
なんでだろうって考えたんですが
例えば何かしら理由があってstatic-pagesブランチの内容を丸っと削除してしまうとこの更新も消えてしまうからとか。
（マージ後だとしても、マージを取り消して→ブランチを丸っとなかったことにすると消えますよね）
と、言う訳でブランチを切る前にこの設定をやるように<a href="https://rakuda3desu.net/rrakudas-rails-tutorial3-2/">らくだ🐫にもできるRailsチュートリアル｜3.2</a>に移動します。
<h3>3.3.1 最初のテスト</h3>
rails generate controller StaticPages home helpdeで生成されたテストコード 
<pre class="lang:ruby decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb
">require 'test_helper'

class StaticPagesControllerTest < ActionDispatch::IntegrationTest
  #homeページのテスト
  test "should get home" do
    #/static_pages/homeのURLに　getのリクエスト
    get static_pages_home_url
    #レスポンスは成功　に　なるはず
    assert_response :success
  end

  #helpページのテスト
  test "should get help" do
    #/static_pages/helpのURLに　getのリクエスト
    get static_pages_help_url
    #レスポンスは成功　に　なるはず
    assert_response :success
  end

end
</pre>
ではテストを実行してみます
<pre class="lang:default mark:(18-18) decode:true " title="ターミナル" >(static-pages) $ rails t
Running via Spring preloader in process 15469
/home/ec2-user/environment/sample_app/db/schema.rb doesn't exist yet. Run `rails db:migrate` to create it, then try again. If you do not intend to use a database, you should instead alter /home/ec2-user/environment/sample_app/config/application.rb to limit the frameworks that will be loaded.
Started with run options --seed 44061

  2/2: [======================================] 100% Time: 00:00:00, Time: 00:00:00

Finished in 0.30129s
2 tests, 2 assertions, 0 failures, 0 errors, 0 skips
#エラーは無かったけど何やら怪しい表示↓
Traceback (most recent call last):
        33: from -e:1:in `
'
・
・
・
         1: from /home/ec2-user/.rvm/gems/ruby-2.6.0/gems/notiffany-0.1.1/lib/notiffany/notifier/tmux.rb:69:in `_check_available'
/home/ec2-user/.rvm/gems/ruby-2.6.0/gems/notiffany-0.1.1/lib/notiffany/notifier/tmux/client.rb:12:in `version': undefined method `[]' for nil:NilClass (NoMethodError)
#この辺が怪しいですね</pre> 
どうやらエラーの模様です
下記サイトを参照に修正します
[blogcard url="https://qiita.com/uranosatoshi/items/db46aef8c7d8a09489c4"]
<pre class="lang:default decode:true " title="ターミナル" >#管理者権限でtmuxをインストール的なコマンド
$ sudo yum install -y tmux
・
・
・
#インストールが終了したら再度test
$ rails t
・
・
・
  2/2: [======================================] 100% Time: 00:00:00, Time: 00:00:00

Finished in 0.30332s
2 tests, 2 assertions, <span class="green">0 failures, 0 errors</span>, 0 skips
#テストも通るしエラーも出なくなりました</pre>
<h3>3.3.2 Red</h3>
「失敗するテストを書く→コードを書いて成功させる→必要があればリファクタリング」
この流れに沿ってAboutページを作っていきます

まずはテスト
homeやhelpと同じ内容でアクセス先をaboutに変更
<pre class="lang:ruby mark:(15-20) decode:true " title="/sample_app/test/controllers/static_pages_controller_test.rb" >require 'test_helper'

class StaticPagesControllerTest < ActionDispatch::IntegrationTest

  test "should get home" do
    get static_pages_home_url
    assert_response :success
  end

  test "should get help" do
    get static_pages_help_url
    assert_response :success
  end

  test "should get about" do
    #/static_pages/aboutのURLに　getのリクエスト
    get static_pages_about_url
    #レスポンスは成功　に　なるはず
    assert_response :success
  end
end</pre> 
aboutページからレスポンスは成功しないためtestはRED（エラー）
と言うより「/static_pages/aboutへのURLが見つからない（定義されていない）ため、エラー判定が途中でストップしちゃってます」→GREENにならない（便宜上RED）の方が近いっぽい
<pre class="lang:default mark:(8-8,11-11) decode:true " title="ターミナル" >$ rails t
・
・
・
ERROR["test_should_get_about", StaticPagesControllerTest, 0.14728970400000208]
 test_should_get_about#StaticPagesControllerTest (0.15s)
#/static_pages/aboutへのURLが見つからないよ的なエラーメッセージ
NameError:         NameError: undefined local variable or method `static_pages_about_url' for #

#上記のエラーがどこで引っかかっているのか教えてくれているメッセージ
            test/controllers/static_pages_controller_test.rb:15:in `block in '

  3/3: [===================================] 100% Time: 00:00:00, Time: 00:00:00

Finished in 0.43253s
3 tests, 2 assertions, 0 failures, 1 errors, 0 skips</pre> 
<h3>3.3.3 Green</h3>
<h4>ルートを追加</h4>
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/config/routes.rb" >Rails.application.routes.draw do
  get  'static_pages/home'
  get  'static_pages/help'
  get  'static_pages/about'
  root 'application#hello'
end
</pre> 
ルーティングを指定したことにより
自動的に「static_pages_about_url」が使えるようになる
<h4>ヘルパーとは何ぞ</h4>
滅茶苦茶ざっくりですが「便利な部品・機能」
例えば上の「static_pages_about_url」は「/static_pages/aboutというURL」を表している
<h4>再テストとアクションの追加</h4>
<pre class="lang:default decode:true " title="ターミナル" >$ rails t
・
・
・
AbstractController::ActionNotFound: The action 'about' could not be found for StaticPagesController
#static_pagesコントローラにaboutアクションがないよ的なエラーメッセージ
・
・
・
</pre>
<pre class="lang:ruby mark:(9-10) decode:true " title="/sample_app/app/controllers/static_pages_controller.rb" >class StaticPagesController < ApplicationController
  def home
  end

  def help
  end
  
  #aboutアクションを追加
  def about
  end
  
end
</pre> 
<h4>再テストとビューの追加</h4>
<pre class="lang:default decode:true " title="ターミナル" >$ rails t
・
・
・
#aboutのテンプレート(view)がないよ的なエラーメッセージ
ActionController::UnknownFormat: StaticPagesController#about is missing a template for this request format and variant.
・
・
・</pre>
<pre class="lang:default decode:true " title="ターミナル" >#app/views/static_pagesにabout.html.erbが作成される
$ touch app/views/static_pages/about.html.erb
#ファイルツリーに表示されない場合、更新すると出てくる
</pre>
本文に従ってファイルを編集→テストが<span class="green">GREEN</span>に
<h3>3.3.4 Refactor</h3>
本文ママ
<h3>まとめとか感想</h3>
ちょこちょこ本文にないエラーに遭遇するし
1週目には無かったエラーにもであったりもするけど
エラー文とかで調べれば解決策が出てくるのでありがたいことです

2019.05.24
3.2.2のテストREDの解釈に解釈と言うか理解について
ツイッターで教えてもらったことがあったので追加・修正しました
独学だとどうしても限界があったりするので
新しい気付きを貰えて、とてもありがたく思っています。感謝。

<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

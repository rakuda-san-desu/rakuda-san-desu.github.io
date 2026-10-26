---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜1.3"
date: "2019-05-05 19:29:05"
categories: ["Railsチュートリアル", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 520
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial-1-3/"
---
<h2>1.3 最初のアプリケーション</h2>
<h4>リスト 1.3: rails newを実行する (バージョン番号を指定)</h4>
<pre class="lang:default mark:(2-2) decode:true " title="ターミナル" >#cloud9の人はディレクトリの移動は特に必要ないと思います
$ rails _5.1.6_ new hello_app
#rails バージョン指定 new アプリ名</pre> 
そして早速エラーが出ました
が、2週目のらくだ🐫は知っています。
後でgemファイルの編集をすることを！
なので、gemのインストールが出来ていないよ的な部分はこの部分ではスルーで。
しかし気になる文章も。
 
<pre class="lang:default mark:(8-9,11-14,15-16) decode:true " title="ターミナル" >・
・
・
Bundle complete! 16 Gemfile dependencies, 70 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
Post-install message from i18n:

HEADS UP! i18n 1.1 changed fallbacks to exclude default locale.
But that may break your application.

Please check your Rails app for 'config.i18n.fallbacks = true'.
If you're using I18n (>= 1.1.0) and Rails (< 5.2.2), this should be
'config.i18n.fallbacks = [I18n.default_locale]'.
If not, fallbacks will be broken in your app by I18n 1.1.x.

For more info see:
https://github.com/svenfuchs/i18n/releases/tag/v1.1.0
・
・
・</pre> 
break your application.とか怖くないですか？と言う訳で調べました。
解説と解決策はこちら
[blogcard url="http://midnight-engineering.hatenadiary.jp/entry/2019/01/02/181645"]
指定のファイルを開きまして、らくだ🐫の環境では71行目
 
<pre class="lang:default mark:(1-1,3-3) decode:true " title="ターミナル" >config.i18n.fallbacks = true
#↑これを↓こうじゃ
config.i18n.fallbacks =  [I18n.default_locale]</pre> 
<h3>1.3.1 Bundler</h3>
本文のリスト1.5の内容で/hello_app/Gemfileを書き換え
<pre class="lang:default mark:(8-8) decode:true " title="ターミナル" >$ cd hello_app/
#ディレクトリを移動
$ bundle install
・
・
・
#エラーが出ましたよ！
Running `bundle update` will rebuild your snapshot from scratch, using only
the gems in your Gemfile, which may resolve the conflict.</pre> 
本文にもある様にbundle updatをやってみてねって言うメッセージです
赤い文字で出るので怖いですね
 
<pre class="lang:default mark:(10-10) decode:true " title="ターミナル" >$ bundle update
The dependency tzinfo-data (>= 0) will be unused by any of the platforms Bundler is installing for. Bundler is installing for ruby but the dependency is only for x86-mingw32, x86-mswin32, x64-mingw32, java. To add those platforms to the bundle, run `bundle lock --add-platform x86-mingw32 x86-mswin32 x64-mingw32 java`.
Fetching gem metadata from https://rubygems.org/............
Fetching gem metadata from https://rubygems.org/.
・
・
・
Fetching web-console 3.5.1 (was 3.7.0)
Installing web-console 3.5.1 (was 3.7.0)
Bundle updated!</pre> 
無事アップデートできました。
<h3>1.3.2 rails server</h3>
本文にもある様に新しいターミナルを開いてサーバーを起動します
<pre class="lang:default decode:true " title="ターミナル" >#ディレクトリに移動するのを忘れない事！！
$ cd hello_app/
$ rails server</pre>
ローカルのターミナルだと起動時のフォルダを指定しておくことが出来たんですが
cloud9ではそういった機能が無い様なので（ご存知の方いらしたら教えてください！）新しくターミナルを開いたら必ずディレクトリに移動します。
rails serverでエラーが出る場合、現在位置を確認してみて下さい（らくだ🐫は稀によくある）
また、<strong>$ rails server</strong>は<strong>$ rails s</strong>に省略可能です。
サーバーが起動出来たらプレビュー表示ですがcloud9の環境上では表示されないので若干焦ります。
本文にもありますがプレビュータブから別途ブラウザを開くと表示されます
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/1.3.2-1.png"><img class="size-full wp-image-532 alignnone" src="https://rakuda3desu.net/wp-content/uploads/2019/05/1.3.2-1.png" alt="" width="274" height="240" /></a>
↓こうじゃ！
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/1.3.2-2.png"><img class="alignnone size-medium wp-image-530" src="https://rakuda3desu.net/wp-content/uploads/2019/05/1.3.2-2-300x213.png" alt="" width="300" height="213" /></a>
わーい！
<h4>演習</h4>
<a href="https://rakuda3desu.net/rakudas-rails-tutorial1-1-1-2/">前のページ</a>でrailsをインストールした際、勢いで確認した奴ですね。○○ -vでいろんなもののversionを確認できます
<pre class="lang:default decode:true " title="ターミナル" >>$ ruby -v
ruby 2.6.0p0 (2018-12-25 revision 66547) [x86_64-linux]
$ rails -v
Rails 5.1.6</pre>
1.2共に全て一致
<h3>1.3.3 Model-View-Controller (MVC)</h3>
この辺のことは別途しっかり勉強したいですね。と思う🐫であった。
<h3>1.3.4 Hello, world!</h3>
<pre class="lang:default decode:true " title="ターミナル" >#app/controllers/の中にある〇〇controller.rbと言うファイルは？
$ ls app/controllers/*_controller.rb
#結果
app/controllers/application_controller.rb</pre>
因みにls(list segments)なので大文字のI（アイ）ではなく小文字のl（エル）です
<pre class="lang:default mark:(5-5,7-8) decode:true " title="/hello_app/app/controllers/application_controller.rb" >class ApplicationController < ActionController::Base
  #自動生成されているセキュリティに関するコード
  protect_from_forgery with: :exception
  #helloアクション
  def hello
    #HTMLで表示したい:"表示したい内容"
    render html: "hello, world!"
  end
end</pre> 
自動生成されているコードについてはこちら
[blogcard url="https://kossy-web-engineer.hatenablog.com/entry/2018/10/13/062454"]
現段階では特に気にしなくてもよさそうです
<pre class="lang:default mark:(3-3) decode:true " title="/hello_app/config/routes.rb" >Rails.application.routes.draw do
  #root（一番上、index.htmlに当たる）では'application_controllerのhelloアクション'を実行
  root 'application#hello'
end
</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">リスト 1.7のhelloアクションを書き換え、「hello, world!」の代わりに「hola, mundo!」と表示されるようにしてみましょう。</blockquote>
<pre class="lang:default mark:(6-6) decode:true " title="/hello_app/app/controllers/application_controller.rb" >class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  
  def hello
    #アクションの内容を書き換える
    render html: "hola, mundo!"
  end
end</pre> 
<blockquote class="quote-rort">Railsでは「非ASCII文字」もサポートされています。「¡Hola, mundo!」にはスペイン語特有の逆さ感嘆符「¡」が含まれています (図 1.16)18。「¡」文字をMacで表示するには、Optionキーを押しながら1キーを押します。この文字をコピーして自分のエディタに貼り付ける方が早いかもしれません。
（図の表示省略）
図 1.16: ルートルーティングで「¡Hola, mundo!」を表示するよう変更する</blockquote>
これも上記の通りアクションの内容を書き換えれば良さそうな感じなんですが、「ルートルーティングで」とあるので悩みますね。
ルーティングで表示を変更→対応アクションを作って/hello_app/config/routes.rbを編集って思うのだけど
事項である演習3を見ると、この時点で「2つ目のアクションを追加」とあります。なので演習2の時点ではやはりアクションの内容を書き換えるのが正解？

正直どちらでもいいのかなって思います（笑）「¡」が表示できるよって事がわかればいいのだと。はい。
<blockquote class="quote-rort">リスト 1.7のhelloアクションを参考にして、２つ目のアクションgoodbyeを追加しましょう。このアクションは、「goodbye, world!」というテキストを表示します。リスト 1.9のルーティングを編集して、ルートルーティングの割り当て先をhelloアクションからgoodbyeアクションに変更します (図 1.17)。</blockquote>
<pre class="lang:default mark:(10-12) decode:true " title="/hello_app/app/controllers/application_controller.rb" >class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  
  def hello
    ・
    ・
  end
  
  #goodbyeアクションを作る
  def goodbye
    render html: "goodbye, world!"    
  end
end</pre> 
 
<pre class="lang:default mark:(3-3) decode:true " title="/hello_app/config/routes.rb" >Rails.application.routes.draw do
  #対応アクションを書き換え
  root 'application#goodbye'
end</pre> 
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

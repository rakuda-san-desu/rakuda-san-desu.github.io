---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜2.3（と2.4）"
date: "2019-05-19 20:50:53"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Heroku", "Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 636
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial2-3/"
---
<h2>2.3 Micropostsリソース</h2>
<h3>2.3.1 マイクロポストを探検する</h3>
Usersリソースの時と同様にmicropostsリソースを生成する
<pre class="lang:default decode:true " title="ターミナル" >$ rails generate scaffold Micropost content:text user_id:integer
#content:text user_id:integer を持つMicropostsリソースをscaffoldで生成
・
・
・
$ rails db:migrate
#生成されたファイルに則りデータベースを設定するコマンド
・
・
・
</pre>
<pre class="lang:ruby mark:(3-3) decode:true " title="/toy_app/config/routes.rb" >Rails.application.routes.draw do
  #Micropostsリソース用のルール
  resources :microposts
  resources :users
  root 'users#index'
end</pre> 
scaffoldでMicropostsリソースを生成したのでroutesファイルが更新されている外、
controllerやviewファイルも自動生成されている
<h4>マイクロポストを作成してみる</h4>
/microposts/newにアクセスしていくつか投稿してみる
<h4>演習</h4>
<blockquote class="quote-rort">CSSを知っている読者へ: 新しいマイクロポストを作成し、ブラウザのHTMLインスペクター機能を使って「Micropost was successfully created.」の箇所を調べてみてください。ブラウザをリロードすると、その箇所はどうなるでしょうか?</blockquote>
<pre class="lang:default decode:true " title="ブラウザのデベロッパーツール
" ><p id="notice">Micropost was successfully created.</p>
#↑これが↓こうなる
<p id="notice"></p></pre>
<blockquote class="quote-rort">マイクロポストの作成画面で、ContentもUserも空にして作成しようとするどうなるでしょうか?</blockquote>
どちらも問題なく登録できてしまう（本来は良くないのでエラーになりたい）
<blockquote class="quote-rort">141文字以上の文字列をContentに入力した状態で、マイクロポストを作成しようとするとどうなるでしょうか? (ヒント: WikipediaのRubyの記事にある１段落目がちょうど150文字程度ですが、どうなりますか?)</blockquote>
問題なく登録できてしまう（本来は良くないのでエラーになりたい）
<blockquote class="quote-rort">上記の演習で作成したマイクロポストを削除してみましょう。</blockquote>
/micropostsの一覧表示にあるdeleteボタンを使って削除
<h3>2.3.2 マイクロポストをマイクロにする</h3>
文字数の制限
 
<pre class="lang:ruby mark:(3-3) decode:true " title="/toy_app/app/models/micropost.rb" >class Micropost < ApplicationRecord
  #データのチェック　：検証するカラム名,　検証内容
  validates :content, length: { maximum: 140 }
end</pre> 
バリデーション　＝　検証 設定した内容に該当しない場合、データベースに保存されずエラーになる
この場合、contentsカラムの内容が最大140文字を超えるとエラーになる
<h4>演習</h4>
<blockquote class="quote-rort">先ほど2.3.1.1の演習でやったように、もう一度Contentに141文字以上を入力してみましょう。どのように振る舞いが変わったでしょうか?</blockquote>
エラー表示が出て投稿できなくなっている
<blockquote class="quote-rort">CSSを知っている読者へ: ブラウザのHTMLインスペクター機能を使って、表示されたエラーメッセージを調べてみてください。</blockquote>
<pre class="lang:default decode:true " title="ブラウザのデベロッパーツール
" >
#エラーメッセージを表示させるためのerror_explanationというidのdivが作られている
<div id="error_explanation">
  #Micropostを保存する際に1つのエラーが出ています的な見出し
  <h2>1 error prohibited this micropost from being saved:</h2>
  <ul>
    #コンテンツが長すぎ（最大140文字だよ）的なメッセージ
   <li>Content is too long (maximum is 140 characters)</li>
  </ul>
</div></pre>
<h3>2.3.3 ユーザーはたくさんマイクロポストを持っている</h3>
<pre class="lang:ruby mark:(3-3) decode:true " title="/toy_app/app/models/user.rb" >class User < ApplicationRecord
  #「1人のuserは複数のmicropostsを持っている」と言う関連付け
  has_many :microposts
end</pre> 
「has_many」↑と↓「belongs_to」は対になっている
<pre class="lang:ruby mark:(3-3) decode:true " title="/toy_app/app/models/micropost.rb" >class Micropost < ApplicationRecord
  #「一つのmicropostは一人のuserにのみ属する」と言う関連付け
  belongs_to :user
  validates :content, length: { maximum: 140 }
end</pre> 
この関連付けをすることで
「user_idが1のmicropostsをすべて表示する」
「このmicropostの投稿者のUserページを表示する」などの動作を付けられるようになる。
<h4>rails console</h4>
<pre class="lang:default mark:(4-4,8-8,13-13,17-17) decode:true " title="ターミナル" >#コンソールを起動「rails c」でもOK
$ rails console
#first_user に Userテーブルにある1番目のデータを代入
> first_user = User.first
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
 => #"ももちゃん", email: "momo@mail.com", created_at: "2019-05-11 10:00:18", updated_at: "2019-05-11 10:00:18"> 
#first_userのmicroposts（全て）
> first_user.microposts
  Micropost Load (0.1ms)  SELECT  "microposts".* FROM "microposts" WHERE "microposts"."user_id" = ? LIMIT ?  [["user_id", 1], ["LIMIT", 11]]
#user_idが1の投稿は2つ作ってあったので2つ表示されている
 => #"に ゃーん", user_id: 1, created_at: "2019-05-18 07:05:46", updated_at: "2019-05-18 07:05:46">, #"Secondにゃーん", user_id: 1, created_at: "2019-05-18 07:07:13", updated_at: "2019-05-18 07:07:13">]> 
#micropost に first_userの1番目のmicropostを代入
> micropost = first_user.microposts.first
  Micropost Load (0.2ms)  SELECT  "microposts".* FROM "microposts" WHERE "microposts"."user_id" = ? ORDER BY "microposts"."id" ASC LIMIT ?  [["user_id", 1], ["LIMIT", 1]]
 => # "にゃーん", user_id: 1, created_at: "2019-05-18 07:05:46", updated_at: "2019-05-18 07:05:46"> 
#micropostのuser
> micropost.user
 => #"ももちゃん", email: "momo@mail.com", created_at: "2019-05-11 10:00:18", updated_at: "2019-05-11 10:00:18"> 
#コンソールの終了
> exit（もしくはctrl+d）</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">ユーザーのshowページを編集し、ユーザーの最初のマイクロポストを表示してみましょう。同ファイル内の他のコードから文法を推測してみてください (コラム 1.1で紹介した技術の出番です)。うまく表示できたかどうか、/users/1 にアクセスして確認してみましょう。</blockquote>
<pre class="lang:ruby mark:(8-13) decode:true " title="/toy_app/app/views/users/show.html.erb" >・
・
・
<p>
 　<strong>Email:</strong>
  <%= @user.email %>
</p>
<p>
  <strong>Microposts:</strong>
  #@userのmicropostsの1番目のcontentを表示
  <%= @user.microposts.first.content %>
  #因みにらくだ🐫は最初「@user.microposts.first」にしてしまいいました。惜しい！
</p>
・
・
・</pre> 
表示できました！
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/2.3.3-1.png"><img class="alignnone wp-image-643 size-full" src="https://rakuda3desu.net/wp-content/uploads/2019/05/2.3.3-1.png" alt="" width="789" height="240" /></a>
（User_idが2の投稿もあるので、/users/2にもmicropostが表示されていました）
<blockquote class="quote-rort">リスト 2.16は、マイクロポストのContentが存在しているかどうかを検証するバリデーションです。マイクロポストが空でないことを検証できているかどうか、実際に試してみましょう (図 2.16のようになっていると成功です)。</blockquote>
<pre class="lang:ruby mark:(3-3) decode:true " title="/toy_app/app/models/micropost.rb" >class Micropost < ApplicationRecord
  belongs_to :user
  validates :content, length: { maximum: 140 }, presence: true
  #本文では改行表示されているけど実際にはこういう一文なので
  #文字数のバリデーションの後に区切りの「,」を忘れないこと！
end</pre> 
<blockquote class="quote-rort">リスト 2.17のFILL_INとなっている箇所を書き換えて、Userモデルのnameとemailが存在していることを検証してみてください (図 2.17)。</blockquote>
<pre class="lang:ruby mark:(3-4) decode:true " title="/toy_app/app/models/user.rb" >class User < ApplicationRecord
  has_many :microposts
  validates :name, presence: true 
  validates :email, presence: true 
  #「:」を忘れずに！
end</pre> 
<h3>2.3.4 継承の階層</h3>
本文ママ
<h4>演習</h4>
<blockquote class="quote-rort">Applicationコントローラのファイルを開き、ApplicationControllerがActionController::Baseを継承している部分のコードを探してみてください。</blockquote>
<pre class="lang:ruby mark:(1-1) decode:true " title="/toy_app/app/controllers/application_controller.rb" >class ApplicationController < ActionController::Base
#↑ここ
  protect_from_forgery with: :exception

  def hello
    render html: "hello,world!"
  end
end</pre> 
<blockquote class="quote-rort">ApplicationRecordがActiveRecord::Baseを継承しているコードはどこにあるでしょうか? 先ほどの演習を参考に、探してみてください。ヒント: コントローラと本質的には同じ仕組みなので、app/modelsディレクトリ内にあるファイルを調べてみると...?)</blockquote>
<pre class="lang:ruby mark:(1-1) decode:true " title="/toy_app/app/models/application_record.rb
" >class ApplicationRecord < ActiveRecord::Base
#↑ここ
  self.abstract_class = true
end</pre>
<h3>2.3.5 アプリケーションをデプロイする</h3>
本文ママ
また、2.3.3.1の実習内容がそのままだったので
show.html.erbに追加したMicropoft表示用のコードをコメントアウトして再push
<h4>演習</h4>
<blockquote class="quote-rort">本番環境で２〜３人のユーザーを作成してみましょう。</blockquote>
Herokuのアプリを開いて、名前とメールを入力してボタンをクリック。
所がエラー表示！
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/2.3.5-1.png"><img class="alignnone wp-image-644 size-full" src="https://rakuda3desu.net/wp-content/uploads/2019/05/2.3.5-1.png" alt="" width="599" height="171" /></a>
ログを確認しろとの事なのでターミナルから確認します
<pre class="lang:default decode:true " title="ターミナル" >$ heroku logs
・
・
・
2019-05-19T10:46:09.270406+00:00 app[web.1]: F, [2019-05-19T10:46:09.270345 #4] FATAL -- : [ac9b9900-76f4-4689-8fd7-62656c2eedde]
2019-05-19T10:46:09.270500+00:00 app[web.1]: F, [2019-05-19T10:46:09.270443 #4] FATAL -- : [ac9b9900-76f4-4689-8fd7-62656c2eedde] <span class="point">app/views/users/show.html.erb:15:in</span> `_app_views_users_show_html_erb__2106187406813746292_69896532737100'
・
・
・</pre>

怪しい部分を教えてくれます。ありがたや。
そしてapp/views/users/show.html.erbの15行目と言うのが先ほどコメントアウトしたMicropoft表示用のコード
はて？コメントアウトはいかんのか？と思いつつ、削除して再push
/users/show.html.erbが無事表示されました※URLは/users/3（user_id）

２，３については動作確認のみ、エラーなしだったので省略
<h2>2.4 最後に</h2>
まとめなので省略
<h3>まとめとか感想</h3>
基本的には2.2の復習的な内容だったのでさほど詰まらず
エラーもあったけどサクっと対応できたので良かったでした。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

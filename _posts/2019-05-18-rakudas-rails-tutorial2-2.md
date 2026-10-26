---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜2.2"
date: "2019-05-18 15:41:54"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 611
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial2-2/"
---
<h3>2.2 Usersリソース</h3>
<h4>Webインターフェイスとはなんぞ</h4>
一言で言おうとすると逆によくわからなくなる感じなのですが、webから操作するための仕組み的な？
railsで言うとviewとかcontrollerあたりがそれにあたるのかな。と思っています。
<h4>リソースとは</h4>
それぞれのデータを作ったり・呼び出したり・更新したり・削除したりできるようにした一塊の仕組み
Usersリソースなら「user」の情報を色々する為の、UsersモデルとWebインターフェイスを合わせたもの。

この辺は多分「こういうもの」っていう意識を持っていればいいのかな。と思います。
<pre class="lang:default decode:true " title="ターミナル" >#name:string email:stringを持つUsersリソースをscaffoldジェネレータで生成
$ rails generate scaffold User name:string email:string
#生成されたファイルに則りデータベースを設定するコマンド
$ rails db:migrate</pre>
rails generate scaffold~によって、データベースを設定するためのファイル（/toy_app/db/migrate/20190509105455</em>_create_users.rb）が自動生成されている
このファイルに則ってデータベースの設定をしてもらうとサーバーが実行できるようになる
<pre class="lang:default decode:true " title="作業用とは別のターミナルを開いて入力！" >$ rails server</pre>
<h3>2.2.1 ユーザーページを探検する</h3>
現在ルートURLを開くと「hello, world!」が表示されるようになっているがそれ以外のページもすでに用意されている
ただし出来立ての状態ではユーザーのデータはまだないので「/users/1」と「/users/1/edit」を表示させようとするとエラーになる。「/users/new」でデータを登録した後では表示される
以降本文に沿って機能のお試し
<h4>演習</h4>
<blockquote class="quote-rort">CSSを知っている読者へ: 新しいユーザーを作成し、ブラウザのHTMLインスペクター機能を使って「User was successfully created.」の箇所を調べてみてください。ブラウザをリロードすると、その箇所はどうなるでしょうか?</blockquote>
HTMLインスペクター機能→chromeで言う所のデベロッパーツール（winではCtrl+shift+i、もしくは設定ー→その他のツール→デベロッパーツール、もしくは右クリック→検証)
<pre class="lang:default decode:true " title="ブラウザのデベロッパーツール" ><p id="notice">User was successfully updated.</p>
#↑これが↓こうなる
<p id="notice"></p></pre>
<blockquote class="quote-rort">emailを入力せず、名前だけを入力しようとした場合、どうなるでしょうか?
「@example.com」のような間違ったメールアドレスを入力して更新しようとした場合、どうなるでしょうか?</blockquote>
どちらも問題なく登録できてしまう（本来は良くないのでエラーになりたい）
<blockquote class="quote-rort">上記の演習で作成したユーザーを削除してみてください。ユーザーを削除したとき、Railsはどんなメッセージを表示するでしょうか?</blockquote>
<pre class="lang:default decode:true " title="ブラウザ" >
User was successfully destroyed.</pre>
<h3>2.2.2 MVCの挙動</h3>
ざっくりでも雰囲気でもいいのでこの仕組みを頭に入れておくと
「何のために何をしているのか」がイメージしやすくなる気がします。

ルーティングを変更してみる
<pre class="lang:ruby mark:(6-6) decode:true " >Rails.application.routes.draw do
  #この部分の解説は4.3.3との事
  #ざっくり言うとscaffoldで自動生成されたUsersリソースのルーティングを纏めているもの
  resources :users
  #ルートURLにアクセスしたら　UsersControllerのindexアクションを実行
  root 'users#index'
end</pre> 
プレビューを見るとルートURLにusers/index.html.erb（ユーザー一覧）が表示されている
<h4>Usersコントローラ</h4>
内容はscaffoldで自動生成されている
アクションに対応するURLが重複しているものもあるが、リクエストがPOSTなのかGETなのかの違いがある<del datetime="2019-06-02T12:21:36+00:00">（3.3で詳しく説明されるとの事)</del>
（<a href="https://railstutorial.jp/chapters/static_pages?version=5.1#sec-generated_static_pages" rel="noopener noreferrer" target="_blank">3.2.1</a>の「コラム3.2. GETやその他のHTTPメソッド」で説明されています）

以下説明通り
<h4>演習</h4>
<blockquote class="quote-rort">図 2.11を参考にしながら、/users/1/edit というURLにアクセスしたときの振る舞いについて図を書いてみてください。</blockquote>
<ol>
<li>
ブラウザから「/users/1/edit」のリクエスト
</li>
<li>
routes.rbによってUsersコントローラのeditアクションが割り当てられる
</li>
<li>
editアクションが実行され、idが1のユーザーデータを取り出すように問い合わせる
</li>
<li>
Userモデルはidが1のユーザーをデータベースから取り出す
</li>
<li>
取り出したユーザーのデータをUsersコントローラに返す
</li>
<li>
usersコントローラは受け取ったユーザーのデータを@userに保存しedit.html.erbに渡す
</li>
<li>
editビューが起動し、ERBを実行してHTMLを生成
</li>
<li>
Users Controllerはビューで生成されたHTMLをブラウザに返す
</li>
</ol>
※本文の図解に当てはめてみていただければー
<blockquote class="quote-rort">
図示した振る舞いを見ながら、Scaffoldで生成されたコードの中でデータベースからユーザー情報を取得しているコードを探してみてください。
</blockquote>
 
<pre class="lang:ruby decode:true " title="/toy_app/app/controllers/users_controller.rb" >・
・
・
    #らくだ🐫の環境では66行目
    def set_user
      @user = User.find(params[:id])
    end
・
・
・</pre> 
<blockquote class="quote-rort">
ユーザーの情報を編集するページのファイル名は何でしょうか?
</blockquote>
/toy_app/app/views/users/edit.html.erb
<h3>2.2.3 Usersリソースの欠点</h3>
本文ママ
<h3>まとめとか感想</h3>
そろそろ難しい話も増えてきましたが
「詳しくは後程説明」な部分も多いし、何となく付いて行けていればいいのかなって思います。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

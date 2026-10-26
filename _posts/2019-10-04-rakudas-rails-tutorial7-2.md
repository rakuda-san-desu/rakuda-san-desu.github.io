---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜7.2"
date: "2019-10-04 10:19:32"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 928
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial7-2/"
---
<h2>7.2 ユーザー登録フォーム</h2>
ユーザー登録フォームを作成していく
見本は<a href="https://railstutorial.jp/chapters/sign_up?version=5.1#sec-signup_form" rel="noopener noreferrer" target="_blank">Railsチュートリアル本文</a>参照
<h3>7.2.1 form_forを使用する</h3>
<blockquote class="quote-rort">
ユーザー登録ページで重要な点は、ユーザー登録に欠かせない情報を入力するためのformです。Railsでform_forヘルパーメソッドを使います。このメソッドはActive Recordのオブジェクトを取り込み、そのオブジェクトの属性を使ってフォームを構築します。
</blockquote>
ユーザ登録ページの/signupのルーティングはUsersコントローラーのnewアクションに紐づけられている
（<a href="https://rakuda3desu.net/rakudas-rails-tutorial5-4/" rel="noopener noreferrer" target="_blank">5.4.1 Usersコントローラ</a>参照）
→form_forの引数として必要なUserオブジェクトを作成
→@user変数を定義する
<pre class="lang:ruby mark:(8-8) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController

  def show
    @user = User.find(params[:id])
  end

  def new
    @user = User.new
  end
end</pre> 
フォームの見た目を整える（組み込みのコードについては後程7.2.2にて）
<pre class="lang:ruby decode:true " title="/sample_app/app/views/users/new.html.erb
"><% provide(:title, 'Sign up') %>
<h1>Sign up</h1>

#customなCSSで定義されていないクラス名はBootstrapの固有クラスっぽい
<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(@user) do |f| %>
      <%= f.label :name %>
      <%= f.text_field :name %>

      <%= f.label :email %>
      <%= f.email_field :email %>

      <%= f.label :password %>
      <%= f.password_field :password %>

      <%= f.label :password_confirmation, "Confirmation" %>
      <%= f.password_field :password_confirmation %>

      <%= f.submit "Create my account", class: "btn btn-primary" %>
    <% end %>
  </div>
</div></pre>
<pre class="lang:sass mark:(6-6,11-11) decode:true " title="/sample_app/app/assets/stylesheets/custom.scss" >.
.
.
/* forms */
#上記のコードに含まれていない要素名・クラス名がある！
input, textarea, select, .uneditable-input {
  border: 1px solid #bbb;
  width: 100%;
  margin-bottom: 15px;
  #box_sizingミックスインを再利用している！
  @include box_sizing;
}

input {
  height: auto !important;
}</pre> 
<pre class="lang:default decode:true " title="ブラウザ" >#上記のviewとCSSでブラウザに描写されたHTMLはこちら
#viewのコードには含まれていなかったinputという要素名が書き出されている→定義したスタイルが適用される
#セレクタで指定されている要素とクラスがnew.html.erbで描画されたテンプレート内で全て使われている訳ではないけど
#追々どこかで使うんじゃないかな？
<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <form class="new_user" id="new_user" action="/users" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="✓" /><input type="hidden" name="authenticity_token" value="2/viMufKfFihXFDWwk6I8tw66FJaR4LN5FbmrfESzdEm4lMYWgYOHLL53jZRzcrLSWyvKgy5Smg15IlGaI48mA==" />
      <label for="user_name">Name</label>
      <input type="text" name="user[name]" id="user_name" />

      <label for="user_email">Email</label>
      <input type="email" name="user[email]" id="user_email" />

      <label for="user_password">Password</label>
      <input type="password" name="user[password]" id="user_password" />

      <label for="user_password_confirmation">Confirmation</label>
      <input type="password" name="user[password_confirmation]" id="user_password_confirmation" />

      <input type="submit" name="commit" value="Create my account" class="btn btn-primary" data-disable-with="Create my account" />
　　</form>
　</div>
</div></pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
試しに、リスト 7.15にある:nameを:nomeに置き換えてみましょう。どんなエラーメッセージが表示されるようになりますか?
</li>
<li>
試しに、ブロックの変数fをすべてfoobarに置き換えてみて、結果が変わらないことを確認してみてください。確かに結果は変わりませんが、変数名をfoobarとするのはあまり良い変更ではなさそうですね。その理由について考えてみてください。
</li>
</ol>
</blockquote>
１．
NoMethodError in Users#new

undefined method `nome' for #<User:0x00007f9a52820368>
Did you mean?  name

→Users#newにメソッドがないよ！というエラー
→Userクラスのメソッドにはnomeって定義されてないんですけど！？
→もしかしてnameじゃない？（親切！）

２．
変数名は好きに付けられる言えばつけられるけど意味を持たせた方が良い（良いではなくて、持たせるべき？）
foobarはhogeなどと同意で意味を持たない文字列なので良くない
因みにこの場合、「ｆ」は「form」のｆ
<h3>7.2.2 フォームHTML</h3>
新規ユーザーのためのユーザー登録フォーム（app/views/users/new.html.erb）を読み解く<blockquote class="quote-rort">通常、Railsヘルパーを使っている場合、実装の詳細について知っておく必要はありません。ただしfというオブジェクトが何をするのかは知っておく必要があります。</blockquote>
と、言う事なのでざっくりしますが
form_forヘルパー = 
フォームを作るに当たり
渡された引数の持つ属性とオブジェクト（ｆ）を結び付けていい感じのHTMLにしてくれる便利な奴
<pre class="lang:ruby decode:true " title="/sample_app/app/views/users/new.html.erb
">><%= form_for(@user) do |f| %>
  .
  .
  .
<% end %>
</pre>
form_forヘルパーは引数（@user変数）の為のformタグを作る
更にブロックを取る
そのブロック変数（ｆ）は第一引数に渡したオブジェクト（@user）の属性に対するinputタグを生成するメソッドを呼び出す
参考↓
[blogcard url="https://www.atmarkit.co.jp/ait/articles/1403/28/news035_3.html"]
<div class="code-wrapper"></div>
<h4>それぞれの埋め込みrubyのコードと作成されたHTML</h4>
<pre class="lang:ruby mark:(6-6,13-13,20-20) decode:true " title="/sample_app/app/views/users/new.html.erbとHTML" >#これが
<%= f.label :name %>
<%= f.text_field :name %>
#HTMLに出力されるとこうなる
<label for="user_name">Name</label>
<input id="user_name" name="user[name]" type="text" />

#これが
<%= f.label :email %>
<%= f.email_field :email %>
#HTMLに出力されるとこうなる
<label for="user_email">Email</label>
<input id="user_email" name="user[email]" type="email" />

#これが
<%= f.label :password %>
<%= f.password_field :password %>
#HTMLに出力されるとこうなる
<label for="user_password">Password</label>
<input id="user_password" name="user[password]" type="password" /></pre> 
type="text"　内容をそのまま表示
type="email"　内容をそのまま表示（モバイル端末では英数キーボードが出る様になったりする）
type="password"入力した文字を隠して表示
<h4>inputごとにある特殊なname属性</h4>
上記コードのハイライト部分の事
詳しくは7.4、7.3などとの事
<h4>formタグ</h4>
<pre class="lang:default decode:true " title="ブラウザ" ><form action="/users" class="new_user" id="new_user" method="post"></pre>
<blockquote class="quote-rort">Railsはformタグを作成するときに@userオブジェクトを使います。すべてのRubyオブジェクトは自分のクラスを知っている (4.4.1) ので、Railsは@userのクラスがUserであることを認識します。また、@userは新しいユーザーなので、 Railsはpostメソッドを使ってフォームを構築すべきだと判断します</blockquote>
action="/users"（/usersに対して）（中略）method="post"（POSTのリクエストを送る）
（結果、users_controllerのcreateアクションが呼び出される）（まだ作ってない）

<h4>ブラウザでは表示されないタグ</h4>
<pre class="lang:default decode:true " title="ブラウザ" ><input name="utf8" type="hidden" value="✓" />
<input name="authenticity_token" type="hidden"
       value="NNb6+J/j46LcrgYUC60wQ2titMuJQ5lLqyAbnbAUkdo=" /></pre>
<blockquote class="quote-rort">簡潔にまとめると、Unicode文字の「✓ (チェックマーク ✓)」を使ってブラウザが正しい文字コードを送信できるようにしたり、 Cross-Site Request Forgery (CSRF) と呼ばれる攻撃を阻止するために信頼できるトークンを含めたりしています</blockquote>
<h4>演習</h4>
<blockquote class="quote-rort"><a href="https://www.learnenough.com/html-tutorial/html_intro" rel="noopener noreferrer" target="_blank">Learn Enough HTML to Be Dangerous</a>ではHTMLをすべて手動で書き起こしていますが、なぜformタグを使わなかったのでしょうか? 理由を考えてみてください。</blockquote>
postメソッドを必要としないため
（新しいオブジェクトを作成するリクエストが無い）
<h3>まとめとか感想</h3>
form_forヘルパーって何なのって所でだいぶ引っかかっておりましたが
ちょっとは仲良くなれた気がします。気がするだけ。
<del datetime="2019-10-04T01:34:06+00:00">あとなんか長くなりそうなので、キリのいい所でいったん切りました。
続きは後編で～🐫</del>
なんか勘違いしてた7.2.1～7.2.2で終わってた
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜7.3"
date: "2019-10-16 20:20:07"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 945
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial7-3/"
---
<h2>7.3 ユーザー登録失敗</h2>
無効なデータ送信を受け付けた際にエラー一覧が表示されるようにする
<h3>7.3.1 正しいフォーム</h3>
<blockquote class="quote-rort">特に、/usersへのPOSTリクエストはcreateアクションに送られます。私たちはここで、createアクションでフォーム送信を受け取り、User.newを使って新しいユーザーオブジェクトを作成し、ユーザーを保存 (または保存に失敗) し、再度の送信用のユーザー登録ページを表示するという方法で機能を実装しようと思います</blockquote>
ユーザー登録フォームのコード
<pre class="lang:default decode:true " title="ブラウザ" >#action=”/users”（/usersに対して）（中略）method=”post”（POSTのリクエストを送る）
<form action="/users" class="new_user" id="new_user" method="post"></pre>
リクエストはcreateアクションに送られるけどまだcreateアクションは作っていないのでここから作っていきます！
<h4>createアクション</h4>
<pre class="lang:ruby mark:(11-20) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  
  def show
    @user = User.find(params[:id])
  end
  
  def new
    @user = User.new
  end
  
  #これで完成ではないので注意！！
　def create
    @user = User.new(params[:user])
    if @user.save
      #ここに保存成功時の処理が入る
    else
      #成功しなかった時はnewアクションに対応したviewが返る
      render 'new'
    end
  end

end</pre> 
<h4>無効なユーザー登録データを送信してみる</h4>
現状エラーになりますのでエラーなページが表示される
デバッグ情報↓
<a href="https://rakuda3desu.net/wp-content/uploads/2019/10/7.3-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/10/7.3-1.png" alt="" width="737" height="163" class="alignnone size-full wp-image-950" /></a>
この中のパラメーターハッシュのuserの部分見ていく
<pre class="lang:default decode:true " title="ブラウザ" >"user" => { "name" => "Foo Bar",
            "email" => "foo@invalid",
            "password" => "[FILTERED]",
            "password_confirmation" => "[FILTERED]"
          }</pre>
↑Usersコントローラにparamsとして渡されるハッシュ

ユーザー登録情報を送信する場合、
paramsにはーuser（キ） => 子ハッシュ（値）が含まれている
この子ハッシュにはinput name属性の値（キー） => 送信された値（値）が含まれている
<blockquote class="quote-rort">ハッシュのキーはデバッグ情報では文字列となっていますが、Railsは文字列ではなく、params[:user]のように「シンボル」としてUsersコントローラに渡している</blockquote>
User.newの引数で必要なデータの形になっている！
<pre class="lang:ruby decode:true " title="コードの解説">@user = User.new(params[:user])
#上下のコードは ほぼ 同意
@user = User.new(name: "Foo Bar", email: "foo@invalid",
                 password: "foo", password_confirmation: "bar")</pre>
<h4>マスアサインメント</h4>
<pre class="lang:ruby decode:true " title="コードの解説">#このようなコードをマスアサインメントと呼ぶ
@user = User.new(params[:user])</pre>
マスアサインメントには下記のような問題があるため、Rails 4.0以降ではエラーになるので注意！
<blockquote class="quote-rort">実は、悪意のあるユーザーによってアプリケーションのデータベースが書き換えられないように慎重な対策をとる必要があり、しかも、その対策が別のエラーを引き起こす危険性 (マスアサインメント脆弱性) もありました。</blockquote>
<h3>7.3.2 Strong Parameters</h3>
上で触れたマスアサインメントは値のハッシュを使ってRubyの変数を初期化する
→この方法だとユーザーが送信した情報すべてがUser.newに渡される
→含めたくない情報のハッシュを持っていても渡されたハッシュ全てを使ってUser.newを初期化
→<strong>よって、渡されたparamsハッシュ全体で初期化する行為はセキュリティ上極めて危険！！</strong>
→Strong Parametersというテクニックを使って危険を回避！
詳しくはこちら↓
<h4>必須のパラメータと許可されたパラメータを指定</h4>
<blockquote class="quote-rort">Strong Parametersを使うことで、必須のパラメータと許可されたパラメータを指定することができます。さらに、上のようにparamsハッシュをまるごと渡すとエラーが発生するので、Railsはデフォルトでマスアサインメントの脆弱性から守られる</blockquote>
<pre class="lang:ruby decode:true " title="コードの解説">#:user必須
#名前、メールアドレス、パスワード、パスワードの確認の属性をそれぞれ許可
#それ以外は許可しない
params.require(:user).permit(:name, :email, :password, :password_confirmation)
</pre>
requireメソッド　→　必須の属性（無いとエラーになる）・paramsのハッシュのキーが入る
permitメソッド　→　保存が許可さる属性・子ハッシュのキーが入る
<h4>user_params</h4>
習慣として「user_params」という外部メソッドを使う
params[:user]の代わりとして使われ、適切に初期化したハッシュを返してくれる
また、Usersコントローラの内部でのみ実行されるためWeb経由で外部ユーザーにさらされない
<blockquote class="quote-rort">Rubyのprivateキーワードを使って外部から使えないようにします (privateキーワードの詳細については11.1.2で説明します)。</blockquote>
<pre class="lang:ruby mark:(6-6,14-19) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  .
  .
  .
  def create
    @user = User.new(user_params)
    if @user.save
      # 保存の成功をここで扱う。
    else
      render 'new'
    end
  end

  private

    def user_params
      params.require(:user).permit(:name, :email, :password,
                                   :password_confirmation)
    end
end</pre> 
→見つけやすくするためにprivateキーワード以降のインデントを一段下げている
　（必須ではないけど見易くて良いっぽい）
<h4>残った問題</h4>
ユーザー登録フォームは動くようになったが
<ol>
<li>間違った送信をしてもフィードバックが帰って来ない</li>
<li>有効なデータを送信しても実際にはデータが作られていない（保存されない）</li>
</ol>
1.を7.3.3で、2.を7.4で解決していくとの事です
<h4>演習</h4>
<blockquote class="quote-rort">/signup?admin=1 にアクセスし、paramsの中にadmin属性が含まれていることをデバッグ情報から確認してみましょう。</blockquote>
<pre class="lang:default mark:(4-4,7-7) decode:true " title="ブラウザ" >#デバッグ情報部分
--- !ruby/object:ActionController::Parameters
parameters: !ruby/hash:ActiveSupport::HashWithIndifferentAccess
  admin: '1'
  controller: users
  action: new
permitted: false</pre> 
admin属性が含まれている
ちなみに（ストロングパラメーターで）許可されていないよ！と出ている
<h4>/signup?admin=1はどこにアクセスしてるの問題</h4>
唐突に出てきた「/signup?admin=1」はドコに何のリクエストを送っているので？？？
ってなりまして
調べても良くわからなくて<a href="https://newburu.github.io/" rel="noopener noreferrer" target="_blank">参加しているコミュ</a>で聞いてみたりしたんですが
10章で実装するって教えてもらったし見直したら本文にしっかり書いてあったので
ページ内検索もちゃんとしましょうね、🐫よ。（この流れ以前もやった気がする・・・）
で、どこにどんなリクエストを送っているかはきっと10章で分かるはず
<h3>7.3.3 エラーメッセージ</h3>
<blockquote class="quote-rort">ユーザー登録に失敗した場合の最後の手順として、問題が生じたためにユーザー登録が行われなかったということをユーザーにわかりやすく伝えるエラーメッセージを追加しましょう</blockquote>
RailsではUserモデルの検証時に自動でメッセージを生成してくれる
→保存し失敗するとオブジェクトに関連付けられたエラーメッセージの一覧が生成される
<h4>エラーメッセージが表示される様にする</h4>
userのnewページに@userオブジェクトに関連したエラーメッセージのパーシャルを出力する
（エラーメッセージのパーシャルを先に作る方が自然では🤔）
<pre class="lang:ruby mark:(7-7,10-10,13-13,16-16,19-19) decode:true " title="/sample_app/app/views/users/new.html.erb" ><% provide(:title, 'Sign up') %>
<h1>Sign up</h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(@user) do |f| %>
      <%= render 'shared/error_messages' %>

      <%= f.label :name %>
      <%= f.text_field :name, class: 'form-control' %>

      <%= f.label :email %>
      <%= f.email_field :email, class: 'form-control' %>

      <%= f.label :password %>
      <%= f.password_field :password, class: 'form-control' %>

      <%= f.label :password_confirmation, "Confirmation" %>
      <%= f.password_field :password_confirmation, class: 'form-control' %>
      <%= f.submit "Create my account", class: "btn btn-primary" %>
    <% end %>
  </div>
</div></pre> 
render（描写）　'書き出したいパーシャルのパス'
また、それぞれのフォームにclass: 'form-control'を追加（後でスタイルを付けます）
<h4>エラーメッセージ用のパーシャルを作る</h4>
<blockquote class="quote-rort">Rails全般の慣習として、複数のビューで使われるパーシャルは専用のディレクトリ「shared」によく置かれます (実際このパーシャルは10.1.1でも使います)。</blockquote>
ディレクトリもファイルもないので作る
<pre class="lang:default decode:true " title="ターミナル" >$ mkdir app/views/shared
$ mkdir app/views/shared/_error_messages.html.erb</pre>
<pre class="lang:ruby decode:true " title="/sample_app/app/views/shared/_error_messages.html.erb
">#エラーがあったらtrue（表示する）
<% if @user.errors.any? %>
  <div id="error_explanation">
    <div class="alert alert-danger">
      #フォームに何個のエラーがあるか
      The form contains <%= pluralize(@user.errors.count, "error") %>.
    </div>
    <ul>
    #eachメソッドで取り出す
    <% @user.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul&>
  </div>
<% end %></pre>
<a href="https://rakuda3desu.net/wp-content/uploads/2019/10/7.3-2.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/10/7.3-2.png" alt="" width="591" height="404" class="alignnone size-full wp-image-957" /></a>
何も入力しないで送信ボタンを押してみた結果↑
上手に機能しました♪
<h4>countメソッド</h4>
@user.errors.count 
→　@user.errorsのエラーメッセージを数える
lengthじゃダメなの？って思ったけど
lengthメソッドは配列の要素の数を数えるメソッドなので@user.errorsにつなげても機能しない
<pre class="lang:default mark:(9-10) decode:true " title="ターミナル" >>> user = User.new
=> #
>> user.save
=> false
>> user.errors
=> #<ActiveModel::Errors:0x0000000003576fb8 
   @base=#<User id: nil, name: nil, email: nil,
           created_at: nil, updated_at: nil, password_digest: nil>, 
   @messages={:name=>["can't be blank"], :email=>["can't be blank", "is invalid"], 
             :password=>["can't be blank", "can't be blank", "is too short (minimum is 6 characters)"]}, 
   @details={:name=>[{:error=>:blank}], :email=>[{:error=>:blank}, {:error=>:invalid, :value=>nil}], 
             :password=>[{:error=>:blank}, {:error=>:blank}, {:error=>:too_short, :count=>6}]}
>
#user.errors.messagesにlengthを繋げてみる
>> user.errors.messages.length
#要素の数≠エラーメッセージの数なので期待の数値は帰って来ない
=> 3</pre> 
.countは@messagesからエラーメッセージ（ハッシュの値）を取り出して配列に直してその配列の要素として数えている
（多分そんな感じ）
[blogcard url="https://api.rubyonrails.org/classes/ActiveModel/Errors.html#method-i-count"]
<h4>any?メソッド</h4>
<del datetime="2019-10-16T08:23:52+00:00">要素がある場合tureを返す</del>
<del datetime="2019-10-16T08:23:52+00:00">→empty?メソッドと逆の働き</del>
empty?は要素が0の時true
any?は<strong>真となる要素が存在している</strong>場合にtrue
似て非なるものなのでした（と、教えてもらいました！）
[blogcard url="https://qiita.com/siman/items/51e2ae97caa06798ca46"]
<h4>pluralizeヘルパー</h4>
<blockquote class="quote-rort">pluralizeの最初の引数に整数が与えられると、それに基づいて2番目の引数の英単語を複数形に変更したものを返します。このメソッドの背後には強力なインフレクター (活用形生成) があり、不規則活用を含むさまざまな単語を複数形にすることができます。</blockquote>
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/app/views/shared/_error_messages.html.erb" >#エラーがあったらtrue（表示する）
<% if @user.errors.any? %>
  <div id="error_explanation">
    <div class="alert alert-danger">
      #エラーが1つなら"error"　複数なら"errors"と出力される
      The form contains <%= pluralize(@user.errors.count, "error") %>.
    </div>
・
・
・
<% end %></pre> 
<h4>エラーメッセージにスタイルを与える</h4>
#error_explanationと.field_with_errorsのスタイルの指定
.field_with_errorsには@extend関数（クラスの継承）を使ってBootstrapのhas-errorというCSSクラスを適用する
<pre class="lang:sass decode:true " title="/sample_app/app/assets/stylesheets/custom.scss" >.
.
.
/* forms */
.
.
.
#error_explanation {
  color: red;
  ul {
    color: red;
    margin: 0 0 30px 0;
  }
}

.field_with_errors {
  #.has-errorクラスを継承
  @extend .has-error;
  .form-control {
    color: $state-danger-text;
  }
}</pre> 
このスタイルを適用したことにより、エラーになっているフォームに色が付く
→どこがエラーなのか分かりやすくなっている
<h4>Password can't be blankが2個表示される件</h4>
<blockquote class="quote-rort">presence: trueによるバリデーションも、has_secure_passwordによるバリデーションも空のパスワード (nil) を検知してしまうため、ユーザー登録フォームで空のパスワードを入力すると2つの同じエラーメッセージが表示されてしまいます</blockquote>
→後程解決予定との事
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
最小文字数を5に変更すると、エラーメッセージも自動的に更新されることを確かめてみましょう。
</li>
<li>
未送信のユーザー登録フォーム (図 7.12) のURLと、送信済みのユーザー登録フォーム (図 7.18) のURLを比べてみましょう。なぜURLは違っているのでしょうか? 考えてみてください。
</li>
</ol>
</blockquote>
<ol>
<li>動作確認のみにて省略</li>
<li>
<ul>
<li>送信前→URLは/signup
（→Usersコントローラのnewアクションを実行）
（→new.html.erbを返す）
</li>
<li>送信後→Createアクションにpostのリクエストを送信
→URLは/users
（→Usersコントローラのcreateアクションを実行）
（→新規作成に失敗した場合newアクションに対応したviewを描写）
</li>
<li>
なのでエラーメッセージが出ている以外は同じ見た目だけどURLが異なる
</li>
</ul>
</li>
</ol>
<h3>7.3.4 失敗時のテスト</h3>
<blockquote class="quote-rort">
まずは、新規ユーザー登録用の統合テストを生成するところから始めていきます。コントローラーの慣習である「リソース名は複数形」に因んで、統合テストのファイル名はusers_signupとします
</blockquote>
ターミナル$ rails generate integration_test users_signup</pre>
ユーザー登録ボタンを押したときに（ユーザーが無効の為）ユーザーが作成されないことを確認するテストを作る
<h4>countメソッド</h4>
テストを作るに当たってcountメソッドを使うのだけど
7.3.3で使ったのと動きが違うね？っとなりました。
[blogcard url="https://www.pikawaka.com/rails/count#%E3%83%A2%E3%83%87%E3%83%AB%E3%81%AEcount%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89%E3%81%A8%E9%85%8D%E5%88%97%E3%81%AEcount%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89%E3%81%A8%E3%81%AE%E9%81%95%E3%81%84%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6"]
名前が同じでも挙動が違うのだ！
User（モデル名）.count（レコードを数える）
[blogcard url="https://api.rubyonrails.org/classes/ActiveRecord/Associations/CollectionProxy.html#method-i-count"]
<h4>無効なユーザー登録に対するテスト</h4>
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/users_signup_test.rb
">require 'test_helper'

class UsersSignupTest < ActionDispatch::IntegrationTest

  test "invalid signup information" do
    #ユーザー登録ページにアクセス
    get signup_path
    #ブロックで渡されたものを呼び出す前後でUser.countに違いがない　
    assert_no_difference 'User.count' do
      #users_pathにpostリクエスト　→　内容は無効なユーザーデータを持つparams[:user]ハッシュ
      post users_path, params: { user: { name:  "",
                                         email: "user@invalid",
                                         password:              "foo",
                                         password_confirmation: "bar" } }
    end
    #users/newが描写されている
    assert_template 'users/new'
  end
end</pre>
→コード実装済なのでGREENです！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>リスト 7.20で実装したエラーメッセージに対するテストを書いてみてください。どのくらい細かくテストするかはお任せします。リスト 7.25にテンプレートを用意しておいたので、参考にしてください。</li>
<li>ユーザー登録フォームのURLは /signup ですが、無効なユーザー登録データを送付するとURLが /users に変わってしまいます。これはリスト 5.43で追加した名前付きルート (/signup) と、RESTfulなルーティング (リスト 7.3) のデフォルト設定との差異によって生じた結果です。リスト 7.26とリスト 7.27の内容を参考に、この問題を解決してみてください。うまくいけばどちらのURLも /signup になるはずです。あれ、でもテストは greenのままになっていますね...、なぜでしょうか? (考えてみてください)</li>
<li>リスト 7.25のpost部分を変更して、先ほどの演習課題で作った新しいURL (/signup) に合わせてみましょう。また、テストが greenのままになっている点も確認してください。</li>
<li>リスト 7.27のフォームを以前の状態 (リスト 7.20) に戻してみて、テストがやはり greenになっていることを確認してください。これは問題です! なぜなら、現在postが送信されているURLは正しくないのですから。assert_selectを使ったテストをリスト 7.25に追加し、このバグを検知できるようにしてみましょう (テストを追加して redになれば成功です)。その後、変更後のフォーム (リスト 7.27) に戻してみて、テストが green になることを確認してみましょう。ヒント: フォームから送信してテストするのではなく、'form[action="/signup"]'という部分が存在するかどうかに着目してテストしてみましょう。</li>
</ol>
</blockquote>
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/users_signup_test.rb
">#1.
・
・
・
    assert_template 'users/new'
    #id = "error_explanation"を持つdivがある
    assert_select 'div#error_explanation'
    #class = "alert-dange"を持つdivがある
    assert_select 'div.alert-danger'
  end
end</pre>
2.
本文に従って
routes.rbにpost '/signup',  to: 'users#create'を追加
→/signupにpostのリクエストをしたらusersコントローラのcreateアクションを実行
users/new.html.erbのフォームに送信先アドレスを指定
→<%= form_for(@user, <strong>url: signup_path</strong>) do |f| %>（第2引数で指定）

→postリクエストのsignup_pathに関するテストは書かれていないから
　（テストされていないからREDが出るはずもない→GREEN）

3.動作確認のみにて省略

4.
users/new.html.erbのフォームの送信先を戻す
→GREEN
→test "invalid signup information"に
　assert_select 'form[action="/signup"]'（action="/signup"を持つformがある）追加
→RED
→users/new.html.erbのフォームに送信先を付け直す
→GREEN
<h3>まとめとか感想</h3>
なげぇwwwは、置いといて
countメソッドにだいぶ悩まされたり、でも調べつつ、教えてもらいつつ仲良くなれて良かったでした。
調べる時に<a href="https://api.rubyonrails.org/" rel="noopener noreferrer" target="_blank">rails document</a>（？）と<a href="https://railsguides.jp/" rel="noopener noreferrer" target="_blank">Railsガイド</a>を見るのも覚えてきました
あと正直演習はヒントがあるからどうにかなってるけど、ヒントなかったらかける気がしない…ｺﾜｲ……🐫
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

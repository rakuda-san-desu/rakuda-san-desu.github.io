---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜10.1"
date: "2020-02-03 10:09:55"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1236
wordpress_status: "publish"
permalink: "/rakudas-rails-tutoria10-1/"
---
10章でやること
ユーザーの更新・表示・削除
→未実装のedit、update、index、destroyアクションを加えてRESTアクションを完成させる
<ul>
	<li>ユーザーが自身でプロフィールを更新できるようにする</li>
	<li>ログイン済の場合ユーザー一覧を表示できるようにする</li>
	<li>管理ユーザーを作成し、管理ユーザーのみユーザーを削除できるようにする</li>
</ul>
<h2>10.1 ユーザーを更新する</h2>
ユーザー情報を編集するパターンは新規ユーザーの作成を参考に進められる
<blockquote class="quote-rort">新規ユーザー用のビューを出力するnewアクションと同じようにして、ユーザーを編集するためのeditアクションを作成すればよいのです。同様に、POSTリクエストに応答するcreateの代わりに、PATCHリクエストに応答するupdateアクションを作成します </blockquote>
ただし、ユーザー登録は誰でもできるけどユーザー情報の更新はそのユーザー自身に限られる！
→8章の承認機構を使ってアクセスを制御する
ブランチを切って作業開始ー
<pre class="lang:default decode:true " title="ターミナル" >$ git checkout -b updating-users</pre> 
<h3>10.1.1 編集フォーム</h3>
見本は<a href="https://railstutorial.jp/chapters/updating_and_deleting_users?version=5.1#fig-edit_user_mockup" rel="noopener noreferrer" target="_blank">RoRT本文</a>
まずはUsersコントローラーにeditアクションを追加、それに対応するeditビューを実装する
<blockquote class="quote-rort">ここではデータベースから適切なユーザーデータを読み込む必要があります。ここで注意して頂きたいのは、表 7.1ではユーザー編集ページの正しいURLが/users/1/editとなっていることです (ユーザーのidが1の場合)。ユーザーのidはparams[:id]変数で取り出すことができるのを思い出してください</blockquote>
コードはこうなる
<pre class="lang:ruby mark:(4-6) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
  def edit
    @user = User.find(params[:id])
  end

  private

    def user_params
      params.require(:user).permit(:name, :email, :password,
                                   :password_confirmation)
    end
end</pre> 
ビューも作る（ファイルは手動で作る）
<pre class="lang:ruby decode:true " title="/sample_app/app/views/users/edit.html.erb" ><% provide(:title, "Edit user") %>
<h1>Update your profile</h1>

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

      <%= f.submit "Save changes", class: "btn btn-primary" %>
    <% end %>

    <div class="gravatar_edit">
      <%= gravatar_for @user %>
      <a href="http://gravatar.com/emails" target="_blank">change</a>
    </div>
  </div>
</div></pre> 
<a href="https://railstutorial.jp/chapters/sign_up?version=5.1#code-signup_form" rel="noopener noreferrer" target="_blank">リスト 7.15</a>
のUsers/newのビューと重複している部分が多い→重複部分をパーシャルにまとめられる→演習でやるよ！
<h4>target="_blank"</h4>
Gravatarへのリンクでtarget="_blank"が使われている
リンク先を新しいタブ（もしくはウィンドウ）で開くタグだけど、セキュリティ上の問題がある。→演習でやるよ
<h4>レンダリングされたページを見ていく</h4>
@userインスタンス変数を使うことで"Name"や"Email"の部分に自動的に値が入力される。
→Railsによって@user変数の属性情報から引き出されている
<h4>value="patch"</h4> 
<pre class="lang:default mark:(3-3) decode:true " title="出力されたHTML" ><form accept-charset="UTF-8" action="/users/1" class="edit_user"
      id="edit_user_1" method="post">
  <input name="_method" type="hidden" value="patch" />
  .
  .
  .
</form></pre> 
<blockquote class="quote-rort">WebブラウザはネイティブではPATCHリクエスト (表 7.1でRESTの慣習として要求されている) を送信できないので、RailsはPOSTリクエストと隠しinputフィールドを利用してPATCHリクエストを「偽造」しています。</blockquote>
どういうことだってばよ🤔
→本文曰く詳細を気にする必要はないということなので、もうちょっと強くなったころに考えてみたりするかもしれません
<h4>POSTリクエストとPATCHリクエスト</h4>
上記のform_for(@user)のコードはユーザー登録フォームのコードと完全に一致
→ではRailsはどうやってPOSTリクエストとPATCHリクエストを区別しているのか
<blockquote class="quote-rort">Railsは、ユーザーが新規なのか、それともデータベースに存在する既存のユーザーであるかを、Active Recordのnew_record?論理値メソッドを使って区別できるからです。</blockquote>
<pre class="lang:ruby decode:true " title="参考コード" >$ rails console
>> User.new.new_record?
=> true
>> User.first.new_record?
=> false</pre> 
trueのときにはPOSTを、falseのときにはPATCHを使う用にRailsが判断してくれる。

ところでform_forはform_withになったのでは？
→form_withでも同様に処理されるようです
<h4>ナビゲーションバーの更新</h4>
パーシャルの_header.html.erbを編集してユーザー設定リンクを有効にする
<pre class="lang:default mark:(8-10) decode:true " >・
・
・
<ul class="dropdown-menu">
  <!--自分のプロフィールページへのリンク-->
  <li><%= link_to t('.Profile'), current_user %></li>
  <!--ユーザー設定-->
  #edit_user_path→/users/(current_user)/editへの名前付きルート
  #current_user→記憶トークンcookieに対応するユーザーを返すヘルパーメソッド
  <li><%= link_to t('.Settings'), edit_user_path(current_user) %></li>
  <li class="divider"></li>
  <!--ログアウト-->
  <li><%= link_to t('.Log_out'), logout_path, method: :delete %></li>
</ul>
・
・
・</pre>  
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>先ほど触れたように、target="_blank"で新しいページを開くときには、セキュリティ上の小さな問題があります。それは、リンク先のサイトがHTMLドキュメントのwindowオブジェクトを扱えてしまう、という点です。具体的には、フィッシング (Phising) サイトのような、悪意のあるコンテンツを導入させられてしまう可能性があります。Gravatarのような著名なサイトではこのような事態は起こらないと思いますが、念のため、このセキュリティ上のリスクも排除しておきましょう。対処方法は、リンク用のaタグのrel (relationship) 属性に、"noopener"と設定するだけです。早速、リスト 10.2で使ったGravatarの編集ページへのリンクにこの設定をしてみましょう。</li>
	<li>リスト 10.5のパーシャルを使って、new.html.erbビュー (リスト 10.6) とedit.html.erbビュー (リスト 10.7) をリファクタリングしてみましょう (コードの重複を取り除いてみましょう)。ヒント: 3.4.3で使ったprovideメソッドを使うと、重複を取り除けます3。(関連するリスト 7.27の演習課題を既に解いている場合、この演習課題をうまく解けない可能性があります。うまく解けない場合は、既存のコードのどこに差異があるのか考えながらこの課題に取り組んでみましょう。例えば筆者であれば、リスト 10.5で用いた変数を渡すテクニックを使って、リスト 10.6やリスト 10.7で必要になるURLをリスト 10.5に渡してみるでしょう。)</li>
</ol>
</blockquote>
1.
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/app/views/users/edit.html.erb" >・
・
・
    <div class="gravatar_edit">
      <%= gravatar_for @user %>
      <a href="http://gravatar.com/emails" rel="noopener" target="_blank"><%= t('.change_image') %></a>
    </div>
  </div>
</div></pre> 
2.
 /sample_app/app/views/users/に_form.html.erbを作る
new.html.erbとedit.html.erbを修正
ハイライト部分は本文から変更有の箇所
<pre class="lang:ruby mark:(1-2) decode:true " title="/sample_app/app/views/users/_form.html.erb" >#yieldメソッドで個別に設定したurlを渡す
<%= form_for(@user, url: yield(:url)) do |f| %>
  <%= render 'shared/error_messages', object: @user %>

  <%= f.label :name %>
  <%= f.text_field :name, class: 'form-control' %>

  <%= f.label :email %>
  <%= f.email_field :email, class: 'form-control' %>

  <%= f.label :password %>
  <%= f.password_field :password, class: 'form-control' %>

  <%= f.label :password_confirmation %>
  <%= f.password_field :password_confirmation, class: 'form-control' %>

  #yieldメソッドで個別に設定したボタン名を渡す
  <%= f.submit yield(:button_text), class: "btn btn-primary" %>
<% end %></pre> 
<pre class="lang:ruby mark:(3-4) decode:true " title="/sample_app/app/views/users/new.html.erb" ><!--yieldメソッドに渡すページタイトル-->
<% provide(:title, 'Sign up') %>
<!--yieldメソッドに渡すページurl-->
<% provide(:url, signup_path) %>
<!--yieldメソッドに渡すボタンに表示する文字-->
<% provide(:button_text, t('.create_my_account')) %>
<h1>Sign up</h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <!--フォームのパーシャルを呼び出し-->
    <%= render 'form' %>
  </div>
</div></pre> 
<pre class="lang:ruby mark:(3-4) decode:true " title="/sample_app/app/views/users/edit.html.erb" ><!--yieldメソッドに渡すページタイトル-->
<% provide(:title, "Edit user") %>
<!--yieldメソッドに渡すページurl-->
<% provide(:url, user_path) %>
<!--yieldメソッドに渡すボタンに表示する文字-->
<% provide(:button_text, t('.update_your_profile')) %>
<h1><%= t('.update_your_profile') %></h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
        <!--フォームのパーシャルを呼び出し-->
    <%= render 'form' %>
    <div class="gravatar_edit">
      <%= gravatar_for @user %>
      <a href="http://gravatar.com/emails" rel="noopener" target="_blank"><%= t('.change_image') %></a>
    </div>
  </div>
</div></pre> 
<strong>現状ではupdateアクションを作っていないのでユーザー情報更新のボタンを押してもエラーで動かないし
テストも書いてないのでエラーのキャッチもされません！</strong>
（あれ？あれ？って嵌ってたのは内緒だよ！）
<h4>provideメソッド→renderメソッド</h4>
provideメソッドを使って値を渡している部分をrenderメソッドでも渡せるそうなので後で試してみる気持ち。
[blogcard url="https://pikawaka.com/rails/partial_template"]
<h3>10.1.2 編集の失敗</h3>
updateアクションを作成する！
createアクションと同様に、無効な情報が送られた場合falseが返されてelseに分岐し編集ページをレンダリングする
↑これを実装すると↓こうなる
<pre class="lang:ruby mark:(13-13,19-19,30-34-35)31, decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  
  def show
    @user = User.find(params[:id])
  end
  
  def new
    @user = User.new
  end
  
  def create
    @user = User.new(user_params)
    if @user.save
      #sessions　helperで定義したlog_inメソッド
      log_in @user
      flash[:success] = t('.welcome_message')
      redirect_to @user
    else
      render 'new'
    end
  end
  
  #ユーザーのeditアクション
  def edit
    @user = User.find(params[:id])
  end
  
  def update
    @user = User.find(params[:id])
    #指定された属性の検証がすべて成功した場合@userの更新と保存を続けて同時に行う
    if @user.update_attributes(user_params)
      # 更新に成功した場合の処理が入る。
    else
      #更新失敗時はeditアクションに対応したviewが返る
      render 'edit'
    end
  end
  
  # 外部に公開されないメソッド
  private

    def user_params
      params.require(:user).permit(:name, :email, :password,
                                   :password_confirmation)
    end
end</pre> 
<blockquote class="quote-rort">update_attributesへの呼び出しでuser_paramsを使っていることに注目してください。7.3.2でも説明したように、ここではStrong Parametersを使ってマスアサインメントの脆弱性を防止しています。

Userモデルのバリデーションとエラーメッセージのパーシャルが既にあるので (リスト 10.2)、無効な情報を送信すると役立つエラーメッセージが表示されるようになっています (図 10.3)。</blockquote>
<h4>演習</h4>
動作確認のみにて省略
<h3>10.1.3 編集失敗時のテスト</h3>
フォーム失敗時の実装をしたのでテストを書いていく！
↓統合テストを生成
<pre class="lang:default mark:(1-1) decode:true " title="ターミナル" >$ rails generate integration_test users_edit
Running via Spring preloader in process 4455
      invoke  test_unit
      create    test/integration/users_edit_test.rb</pre> 
<ol>
	<li>編集ページにアクセス時editビューが描画されるか</li>
	<li>無効な情報を送信するとeditビューが再描画されるか</li>
</ol>
<blockquote class="quote-rort">PATCHリクエストを送るためにpatchメソッドを使っていることに注目してください。これはgetやpost、deleteメソッドと同じように、HTTPリクエストを送信するためのメソッドです。</blockquote>
ptchメソッドなど、RESTアーキテクチャについて→<a href="https://railstutorial.jp/chapters/static_pages?version=5.1#aside-get_etc" rel="noopener noreferrer" target="_blank">RoRT本文</a>
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/users_edit_test.rb" >require 'test_helper'

class UsersEditTest < ActionDispatch::IntegrationTest

  def setup
    @user = users(:michael)
  end

  test "unsuccessful edit" do
    # edit_user_path(@user)にgetのリクエスト
    get edit_user_path(@user)
    # users/editが描写される
    assert_template 'users/edit'
    # 無効なparams:を持ったuser_path(@user)でpatch（更新）のリクエスト
    patch user_path(@user), params: { user: { name:  "",
                                              email: "foo@invalid",
                                              password:              "foo",
                                              password_confirmation: "bar" } }

    # users/editが描写される
    assert_template 'users/edit'
  end
end</pre> 
<h4>演習</h4>
<blockquote  class="quote-rort">
<ol>
	<li>リスト 10.9のテストに１行追加し、正しい数のエラーメッセージが表示されているかテストしてみましょう。ヒント: 表 5.2で紹介したassert_selectを使ってalertクラスのdivタグを探しだし、「The form contains 4 errors.」というテキストを精査してみましょう。</li>
</ol>
</blockquote>
<pre class="lang:ruby mark:(21-22) decode:true " title="/sample_app/test/integration/users_edit_test.rb" >require 'test_helper'

class UsersEditTest < ActionDispatch::IntegrationTest

  def setup
    @user = users(:michael)
  end

  test "unsuccessful edit" do
    # edit_user_path(@user)にgetのリクエスト
    get edit_user_path(@user)
    # users/editが描写される
    assert_template 'users/edit'
    # 無効なparams:を持ったuser_path(@user)でpatch（更新）のリクエスト
    patch user_path(@user), params: { user: { name:  "",
                                              email: "foo@invalid",
                                              password:              "foo",
                                              password_confirmation: "bar" } }
    # users/editが描写される
    assert_template 'users/edit'
    #特定のHTMLタグが存在する→　class="alert"のdiv,　フォームに4個のエラーがあります
    assert_select "div.alert", "フォームに4個のエラーがあります"
  end
end</pre> 
※文章は日本語にしているので上記だけど本文に合わせるならこう↓
<pre class="toolbar:2 lang:default decode:true " >assert_select "div.alert", "The form contains 4 errors."</pre> 
<h3>10.1.4 TDDで編集を成功させる</h3>
画像の変更は外部サービス（Gravatar）に任せているのでそれ以外の機能を実装していく！
<blockquote  class="quote-rort">アプリケーション用のコードを「実装する前に」統合テストを書いた方が便利だと気付いた読者もいるかもしれません。実際、そういったテストのことは「受け入れテスト (Acceptance Tests)」として呼ばれていて、ある機能の実装が完了し、受け入れ可能な状態になったかどうかを決めるテストとして知られています。</blockquote>
<h4>ユーザー情報編集の成功に対するテスト</h4>
<ol>
	<li>ユーザー情報を更新</li>
	<li>flashメッセージが空でないか</li>
	<li>プロフィールページにリダイレクトされるか</li>
	<li>データベース内のユーザー情報が正しく変更されたか</li>
</ol>
<h4>パスワードが空っぽ</h4>
パスワードを変更する必要がない場合はパスワードを入力せずに更新できると便利
→便利だけどいいのかな？とも思う
<h4>テストが書けたら</h4>
updateアクションを実装する
<pre class="lang:ruby mark:(8-11) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
  def update
    @user = User.find(params[:id])
    #指定された属性の検証がすべて成功した場合@userの更新と保存を続けて同時に行う
    if @user.update_attributes(user_params)
      # 更新成功のフラッシュメッセージ
      flash[:success] = "Profile updated"
      # @user（プロフィールページ）へリダイレクト
      redirect_to @user
    else
      render 'edit'
    end
  end
・
・
・</pre> 
現状ではテストはまだRED
→パスワードに対するバリデーションに引っかかるため
<blockquote  class="quote-rort">テストが greenになるためには、パスワードのバリデーションに対して、空だったときの例外処理を加える必要があります。</blockquote>
<h4>allow_nil: true</h4>
対象の値がnilの場合バリデーションをスキップする為のオプション
[blogcard url="https://qiita.com/ryuuuuuuuuuu/items/b7b985465fc0bf6dcaf8#allow_nil"]
これをバリデーションに追加する
<pre class="lang:ruby mark:(11-11) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  #仮想の属性:remember_tokenをUserクラスに定義
  attr_accessor :remember_token
  before_save { email.downcase! }
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
  has_secure_password
  validates :password, presence: true, length: { minimum: 6 }, allow_nil: true
・
・
・</pre> 
→ユーザー編集ページ動くようになりました✨
<h4>検証</h4>
<blockquote  class="quote-rort"><ol>
	<li>実際に編集が成功するかどうか、有効な情報を送信して確かめてみましょう。</li>
	<li>もしGravatarと紐付いていない適当なメールアドレス (foobar@example.comなど) に変更した場合、プロフィール画像はどのように表示されるでしょうか? 実際に編集フォームからメールアドレスを変更して、確認してみましょう。</li>
</ol>
</blockquote>
1.動作確認のみにて省略
2.Gravatarの初期画像が表示される（未登録扱い？）
<h3>まとめとか感想</h3>
過去の分の理解が足りてなかったり、注意力が足りてなかったりで余分にはまっちゃった感あるんだけど
余分な分は無駄じゃなくて積み重ねということでヒトツ！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

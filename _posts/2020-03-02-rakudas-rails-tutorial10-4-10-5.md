---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜10.４（と10.5）"
date: "2020-03-02 12:22:52"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1302
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial10-4-10-5/"
---
<h2>10.4 ユーザーを削除する</h2>
RESTに準拠した正統なアプリケーションとなる為に、最後の一つ、destroy機能を実装していく！
削除権限を持つ管理 （admin）ユーザーのクラスを作成し→ユーザーを削除するためのリンクを追加し→destroyアクションを実装する
見本はこちら→<a href="https://railstutorial.jp/chapters/updating_and_deleting_users?version=5.1#fig-user_index_delete_links_mockup" rel="noopener noreferrer" target="_blank">RoRT本文</a>
<h3>10.4.1 管理ユーザー</h3>
論理値をとるadmin属性をUserモデルに追加する　→　自動的に論理値を返すadmin?メソッドも使えるようになる　→　管理ユーザーの状態をテストできる
<h4>admin属性をUserモデルに追加</h4>
属性の型をbooleanと指定してマイグレートファイルを作る
boolean型 → 論理値を取る型
<pre class="lang:default mark:(1-1) decode:true " title="ターミナル" >$ rails generate migration add_admin_to_users admin:boolean
Running via Spring preloader in process 5082
      invoke  active_record
      create    db/migrate/20200219070406_add_admin_to_users.rb</pre> 
usersテーブルにadminカラムを追加するためのマイグレートファイルが生成されたので
default: falseという引数を追加する
<blockquote class="quote-rort">これは、デフォルトでは管理者になれないということを示すためです (default: false引数を与えない場合、 adminの値はデフォルトでnilになりますが、これはfalseと同じ意味ですので、必ずしもこの引数を与える必要はありません。ただし、このように明示的に引数を与えておけば、コードの意図をRailsと開発者に明確に示すことができます)。</blockquote>
実は結構重要なのできっちり指定が吉との事（言語・DBによってNull/True/Falseの解釈が違ったりするから）
<pre class="lang:default decode:true " title="/sample_app/db/migrate/ timestamp _add_admin_to_users.rb" >class AddAdminToUsers < ActiveRecord::Migration[5.1]
  def change
    add_column :users, :admin, :boolean, default: false
  end
end</pre> 
→マイグレーションの実行
<pre class="lang:default decode:true " title="ターミナル" >$rails db:migrate

#コンソールで動作を確認できる
$ rails console --sandbox
#userにUser.firstを代入
>> user = User.first
#userは管理者か
>> user.admin?
#false
=> false
#userの以下のキー（admin）の値をtoggle!メソッドで反転
>> user.toggle!(:admin)
#falseが反転してtrueに
=> true
#userは管理者か
>> user.admin?
#true
=> true</pre> 
10.3で作ったサンプルデータを
最初のユーザーがデフォルトで管理者になるように変更する
<pre class="lang:default mark:(6-7) decode:true " title="/sample_app/db/seeds.rb" ># テーブル名.create!　作るデータ対応するカラムと値
User.create!(name:  "Example User",
             email: "example@railstutorial.org",
             password:              "foobar",
             password_confirmation: "foobar",
             #管理者に設定
             admin: true)

# 99回繰り返す（timesメソッド）
99.times do |n|
  # nameに代入　Faker::Name.name
  name  = Faker::Name.name
  # emailに代入　example-#{n+1}@railstutorial.org　←それぞれのアドレスが変わるような指定
  email = "example-#{n+1}@railstutorial.org"
  password = "password"
  User.create!(name:  name,
               email: email,
               password:              password,
               password_confirmation: password)
end</pre> 
データベースのリセットと再生成
<pre class="lang:default decode:true " title="ターミナル" >$ rails db:migrate:reset
$ rails db:seed</pre> 
<h4>Strong Parameters、再び</h4>
例えば下記のようなPATCHリクエストを送信されたら？
<pre class="toolbar:2 lang:default decode:true " >patch /users/17?admin=1</pre> 
17番目のユーザーが管理者に変更されてしまう！コワイ！！
危ない危険を防ぐ為に編集してもよい安全な属性だけが更新できるように指定する！
<a href="https://railstutorial.jp/chapters/sign_up?version=5.1#sec-strong_parameters" rel="noopener noreferrer" target="_blank">7.3.2</a>と同じくStrong Parametersを使って対策する。
<pre class="lang:ruby mark:(7-9) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
  private

    #許可された属性リストにadminが含まれていない=adminは編集できない！
    def user_params
      params.require(:user).permit(:name, :email, :password,
                                   :password_confirmation)
    end
    </pre> 
<blockquote class="quote-rort">これにより、任意のユーザーが自分自身にアプリケーションの管理者権限を与えることを防止できます。</blockquote>
これはとても大切な問題なので編集可能になってはならない属性に対するテストを行う！（演習でやる）
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>Web経由でadmin属性を変更できないことを確認してみましょう。具体的には、リスト 10.56に示したように、PATCHを直接ユーザーのURL (/users/:id) に送信するテストを作成してみてください。テストが正しい振る舞いをしているかどうか確信を得るために、まずはadminをuser_paramsメソッド内の許可されたパラメータ一覧に追加するところから始めてみましょう。最初のテストの結果は redになるはずです。</li>
</ol>
</blockquote>
 /sample_app/test/controllers/users_controller_test.rbにテストを追加
<pre class="lang:ruby mark:(23-35) decode:true " title="/sample_app/test/controllers/users_controller_test.rb" >require 'test_helper'

class UsersControllerTest < ActionDispatch::IntegrationTest
  
  # テストユーザーを設定
  def setup
    @user = users(:michael)
    @other_user = users(:archer)
  end
・
・
・
  test "should redirect update when not logged in" do
    # paramsハッシュに以下のデータを持たせてuser_path(@user)にpatchのリクエスト
    patch user_path(@user), params: { user: { name: @user.name,
                                              email: @user.email } }
    # flashがemptyではない（flashが表示されている→エラー表示）
    assert_not flash.empty?
    # login_urlにリダイレクトされる
    assert_redirected_to login_url
  end
  
  test "should not allow the admin attribute to be edited via the web" do
    #@other_userとしてログイン(test_helper.rbで定義したlog_in_asヘルパー)
    log_in_as(@other_user)
    #@other_userのadmin?はtrueではない
    assert_not @other_user.admin?
    #user_path(@other_user)にpatchのリクエスト　→持たせるハッシュ
    patch user_path(@other_user), params: {
                                    user: { password:              'password',
                                            password_confirmation: 'password',
                                            admin: true } }
    #DBから再度取得した@other_userのadmin?はtrueではない
    assert_not @other_user.reload.admin?
  end
・
・
・
end</pre> 
usersコントローラーのuser_paramsメソッド内にadminを追加するとRED、削除（元に戻す）とGREENになる
<h3>10.4.2 destroyアクション</h3>
削除機能を完成させる
<h4>destroyアクションへのリンクを追加</h4>
ユーザー一覧ページに削除用のリンクを表示させる
<pre class="lang:ruby mark:(4-11) decode:true " title="/sample_app/app/views/users/_user.html.erb" ><li>
  <%= gravatar_for user, size: 50 %>
  <%= link_to user.name, user %>
    <!--if式　current_userが管理者　かつ　（削除対象の）userがcurrent_userではない-->
    <!--ここで言うuserはパーシャルの呼び出し元index.html.erbでeach文で取り出したuser変数の事-->
    <!--管理者が自分自身を削除できないようになっている-->
    <% if current_user.admin? && !current_user?(user) %>
    <!--削除ボタン-->
    | <%= link_to t('.delete'), user, method: :delete,
                                  data: { confirm: t('.confirmation') } %>
  <% end %>
</li></pre> 
ログインしているユーザーが管理者の場合のみ削除用リンクが表示される
（管理者自身のユーザー情報には削除用リンクは表示されない）
<blockquote class="quote-rort">ブラウザはネイティブではDELETEリクエストを送信できないため、RailsではJavaScriptを使って偽造します。つまり、JavaScriptがオフになっているとユーザー削除のリンクも無効になるということです。JavaScriptをサポートしないブラウザをサポートする必要がある場合は、フォームとPOSTリクエストを使ってDELETEリクエストを偽造することもできます。こちらはJavaScriptがなくても動作します</blockquote>
→<a href="http://railscasts.com/episodes/77-destroy-without-javascript" rel="noopener noreferrer" target="_blank">RailsCastの "JavaScriptを使わない削除" (英語サイト）</a>
<h4>destroyアクションの実装</h4>
destroyアクションでは
<ol>
	<li>該当するユーザーを見つける</li>
	<li>（Active Recordの）destroyメソッドを使って削除</li>
	<li>ユーザー一覧へリダイレクト</li>
</ol>
ユーザーを削除するためには（管理者として）ログインしていることが必須なので
:destroyアクションもlogged_in_userフィルターに追加する
<pre class="lang:default mark:(2-2,7-14) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  before_action :logged_in_user, only: [:index, :edit, :update, :destroy]
  before_action :correct_user,   only: [:edit, :update]
  .
  .
  .
  def destroy
    # 受け取ったidのUserを削除
    User.find(params[:id]).destroy
    # 削除成功のフラッシュメッセージ
    flash[:success] = "User deleted"
    # ユーザー一覧ページへリダイレクト
    redirect_to users_url
  end

  private
  .
  .
  .
end</pre> 
<h4>destroyアクションへのアクセス制御</h4>
destroyアクションには管理者だけがアクセスできるようにする
<pre class="lang:default mark:(4-4,12-16) decode:true " title="/sample_app/app/controllers/users_controller.rb" >class UsersController < ApplicationController
  before_action :logged_in_user, only: [:index, :edit, :update, :destroy]
  before_action :correct_user,   only: [:edit, :update]
  before_action :admin_user,     only: :destroy
  .
  .
  .
  private
    .
    .
    .
    # 管理者かどうか確認
    def admin_user
      # root_urlにリダイレクト current_user.admin?(ログイン中のユーザーが管理者であるか)がfalseの場合
      redirect_to(root_url) unless current_user.admin?
    end
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>管理者ユーザーとしてログインし、試しにサンプルユーザを２〜３人削除してみましょう。ユーザーを削除すると、Railsサーバーのログにはどのような情報が表示されるでしょうか?</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="Railsサーバーのログ" >・
・
・
   #ここから処理
   (0.1ms)  begin transaction
　#指定したidのユーザーを削除している
  SQL (0.2ms)  DELETE FROM "users" WHERE "users"."id" = ?  [["id", 6]]
   #ここまで処理
   (6.0ms)  commit transaction
・
・
・</pre> 
<h3>10.4.3 ユーザー削除のテスト</h3>
<blockquote class="quote-rort">ユーザーを削除するといった重要な操作については、期待された通りに動作するか確かめるテストを書くべきです。</blockquote>
超大事！と言うこと。
準備としてサンプルユーザーの一人を管理者にする。
<pre class="lang:default mark:(5-5) decode:true " title="/sample_app/test/fixtures/users.yml" >michael:
  name: Michael Example
  email: michael@example.com
  password_digest: <%= User.digest('password') %>
  admin: true

archer:
  name: Sterling Archer
  email: duchess@example.gov
  password_digest: <%= User.digest('password') %>

lana:
  name: Lana Kane
  email: hands@example.gov
  password_digest: <%= User.digest('password') %>

malory:
  name: Malory Archer
  email: boss@example.gov
  password_digest: <%= User.digest('password') %>

<% 30.times do |n| %>
user_<%= n %>:
  name:  <%= "User #{n}" %>
  email: <%= "user-#{n}@example.com" %>
  password_digest: <%= User.digest('password') %>
<% end %></pre> 
<h4>アクション単位での削除機能のテスト</h4>
アクション単位→コントローラーテスト
<pre class="lang:ruby decode:true " title="/sample_app/test/controllers/users_controller_test.rb" >require 'test_helper'

class UsersControllerTest < ActionDispatch::IntegrationTest

  def setup
    @user       = users(:michael)
    @other_user = users(:archer)
  end
  .
  .
  .
  test "should redirect destroy when not logged in" do
    # ブロックで渡されたものを呼び出す前後でUser.countに違いがない
    assert_no_difference 'User.count' do
      # user_path(@user)にdeleteのリクエスト
      delete user_path(@user)
    end
    # login_urlにリダイレクトされる
    assert_redirected_to login_url
  end

  test "should redirect destroy when logged in as a non-admin" do
    # other_userでログインする
    log_in_as(@other_user)
    # ブロックで渡されたものを呼び出す前後でUser.countに違いがない
    assert_no_difference 'User.count' do
      # user_path(@user)にdeleteのリクエスト
      delete user_path(@user)
    end
    # root_urlにリダイレクト
    assert_redirected_to root_url
  end
end</pre> 
ここでは管理者ではないユーザーのふるまいについてテストしている
→管理者ユーザーのふるまいと一緒にテストできるともっと良い！
<blockquote class="quote-rort">管理者であればユーザー一覧画面に削除リンクが表示される仕様を利用して、リスト 10.48のテスト（users_index_test.rb）に今回のテストを追加していくことにします。</blockquote>
<ol>
	<li>DELETEリクエストを適切なURLに向けて発行</li>
	<li>User.countを使ってユーザー数が1減ったか</li>
</ol>
<pre class="lang:ruby mark:(5-8,19-35,38-45) decode:true " title="/sample_app/test/integration/users_index_test.rb" >require 'test_helper'

class UsersIndexTest < ActionDispatch::IntegrationTest

  def setup
    @admin     = users(:michael)
    @non_admin = users(:archer)
  end

  test "index including pagination" do
    # @adminでログイン
    log_in_as(@admin)
    # users_pathにgetのリクエスト
    get users_path
    # users/indexが描画される
    assert_template 'users/index'
    # 特定のHTMLタグが存在する　div class="pagination"
    assert_select 'div.pagination' , count: 2
    # first_page_of_usersにUser.paginate(page: 1)（ユーザー一覧のページ目）を代入
    first_page_of_users = User.paginate(page: 1)
    # first_page_of_usersからuserを1ずつ取り出す
    first_page_of_users.each do |user|
      # 特定のHTMLタグが存在する a href パスはuser_path(user) 表示テキストはuser.name
      assert_select 'a[href=?]', user_path(user), text: user.name
      # （eachで取り出された）user == @adminがfalseの場合
      unless user == @admin
        # 特定のHTMLタグが存在する　a href　パスはuser_path(user)　表示テキストは'削除' （管理ユーザー 以 外 に削除の表示があるという状態）
        assert_select 'a[href=?]', user_path(user), text: '削除'
      end
    end
    # ブロックで渡されたものを呼び出す前後でUser.countが-1
    assert_difference 'User.count', -1 do
      # user_path(@non_admin)にdeleteのリクエスト
      delete user_path(@non_admin)
    end
  end
  
  test "index as non-admin" do
    # @non_adminでログイン
    log_in_as(@non_admin)
    # users_pathにgetのリクエスト
    get users_path
    # 特定のHTMLタグが存在する　a 表示テキストは'削除'　カウントは0個
    assert_select 'a', text: '削除', count: 0
  end
end</pre> 
<h4>演習</h4>
動作確認のみにて省略
<h2>10.5 最後に</h2>
<blockquote class="quote-rort">5.4でUsersコントローラをご紹介して以来、長い道のりをたどってきました。あの頃はユーザー登録すらありませんでしたが、今は登録もログインもログアウトもできます。プロフィールの表示も、設定の編集も、すべてのユーザーの一覧画面もあります。さらに、一部のユーザーは他のユーザーを削除することすらできるようになりました。</blockquote>
まじで長かった！がんばったね！！
<h4>章の終わりには</h4>
テストしてaddしてcommitしてbranchにpushしてGitHubで自分にプルリクしてmergeして戻ってmasterにcheckoutしてpullする
<h4>デプロイ！</h4>
<pre class="lang:default decode:true " title="ターミナル" >#テストして
$ rails test
#herokuにpush
$ git push heroku
#herokuのDBをリセット
$ heroku pg:reset DATABASE
#herokuのDBを初期化
$ heroku run rails db:migrate
#herokuのDBにサンプルデータを読み込む（読み込める設定にしているので）
$ heroku run rails db:seed
#herokuのリスタート
$ heroku restart</pre> 
<blockquote class="quote-rort">実際のWebサイトではサンプルデータを生成したくないという人もいるかと思いますが、これには理由があります (図 10.16)。それは、図 10.16が示すように、サンプルユーザーの表示順序が変化してしまい、図 10.11にあるようなローカル環境での表示順序と異なってしまうことです。これは現時点ではまだデフォルトの表示順序が指定されていないことが原因です。結果として、データベースの内容に応じて表示順序が異なってしまいます。</blockquote>
図に関しては<a href="https://railstutorial.jp/chapters/updating_and_deleting_users?version=5.1#fig-heroku_sample_users" rel="noopener noreferrer" target="_blank">RoRT本文</a>参照でヒトツ。

これはデフォルトの表示順序が指定されていないことが原因ということで、13.1.4で解決していくとの事。
<h3>まとめとか感想</h3>
上でもちょっと触れられてるけど、user関連長かったね！
でももうちょっとだけ続くんじゃ（メール認証と再設定）
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

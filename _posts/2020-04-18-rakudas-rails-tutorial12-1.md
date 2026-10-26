---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜12.1"
date: "2020-04-18 16:23:46"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1405
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial12-1/"
---
12章でやること
パスワードを忘れた時に再設定できるようにする仕組みを作る
見本は<a href="https://railstutorial.jp/chapters/password_reset?version=5.1#fig-login_forgot_password_mockup" rel="noopener noreferrer" target="_blank">本文参照</a>の事
11章ですでにパスワード再設定用のメーラーが生成されているので、そこにリソースとデータモデルを追加してパスワードの再設定を実装していく。
全体の流れはこちら
<blockquote class="quote-rort">
<ol>
	<li>ユーザーがパスワードの再設定をリクエストすると、ユーザーが送信したメールアドレスをキーにしてデータベースからユーザーを見つける</li>
	<li>該当のメールアドレスがデータベースにある場合は、再設定用トークンとそれに対応するリセットダイジェストを生成する</li>
	<li>再設定用ダイジェストはデータベースに保存しておき、再設定用トークンはメールアドレスと一緒に、ユーザーに送信する有効化用メールのリンクに仕込んでおく</li>
	<li>ユーザーがメールのリンクをクリックしたら、メールアドレスをキーとしてユーザーを探し、データベース内に保存しておいた再設定用ダイジェストと比較する (トークンを認証する)</li>
	<li>認証に成功したら、パスワード変更用のフォームをユーザーに表示する</li>
</ol>
</blockquote>
<h2>12.1 PasswordResetsリソース</h2>
branchを作ってから作業開始！

アカウント有効化リソースの際はeditアクションだけを使ったけど、
再設定では再設定用のフォームが必要→対応するviewを描写する必要がある→それぞれのアクションが必要
などから、RESTfulなルーティングが必要になる
<h3>12.1.1 PasswordResetsコントローラ</h3>
早速コントローラを生成
<blockquote class="quote-rort">今回はビューも扱うので、newアクションとeditアクションも一緒に生成している点に注意してください。</blockquote>
<pre class="lang:default decode:true " title="ターミナル" ># new editアクションを持ったPasswordResetsコントローラーを生成（テストは生成しない）
$ rails generate controller PasswordResets new edit --no-test-framework</pre> 
コントローラの単体テストをする代わりに、前章（11.3.3）から統合テストでカバーしていくため
<h4> パスワード再設定用リソースを追加</h4>
新しいパスワードを再設定するためのフォームとUserモデル内のパスワードを変更するためのフォームが必要なので↓下記
<pre class="lang:ruby mark:(21-21) decode:true " title="/sample_app/config/routes.rb" >Rails.application.routes.draw do

  get 'password_resets/new'

  get 'password_resets/edit'

  root 'static_pages#home'
  get  '/help',    to:'static_pages#help'
  get  '/about',   to:'static_pages#about'
  get  '/contact', to:'static_pages#contact'
  
  get  '/signup',  to:'users#new'
  post '/signup',  to: 'users#create'
  
  get    '/login',   to: 'sessions#new'
  post   '/login',   to: 'sessions#create'
  delete '/logout',  to: 'sessions#destroy'
  resources :users
  # account_activationsresourceのeditへのルーティングのみを生成
  resources :account_activations, only: [:edit]
  #password_resetsのnew、create、edit、updateのルーティングを生成 
  resources :password_resets,     only: [:new, :create, :edit, :update]
end</pre> 
<h4>パスワード再設定画面へのリンクを追加</h4> 
<pre class="lang:ruby mark:(12-12) decode:true " title="/sample_app/app/views/sessions/new.html.erb" ><% provide(:title, "Log in") %>
<h1>Log in</h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(:session, url: login_path) do |f| %>

      <%= f.label :email, User.human_attribute_name(:email) %>
      <%= f.email_field :email, class: 'form-control' %>

      <%= f.label :password, User.human_attribute_name(:password) %>
      <%= link_to t('.forgot_password'), new_password_reset_path %>
      <%= f.password_field :password, class: 'form-control' %>
      
      <%= f.label :remember_me, class: "checkbox inline" do %>
        <%= f.check_box :remember_me %>
        <span><%= t('.remember_me_checkbox') %></span>
      <% end %>

      <%= f.submit t('.login'), class: "btn btn-primary" %>
    <% end %>

    <p><%= t('.new_user?') %><%= link_to t('.signup'), signup_path %></p>
  </div>
</div></pre> 
ログインフォームの「パスワード」の横にリンクが来るように設置
<img src="https://rakuda3desu.net/wp-content/uploads/2020/04/12.1.1-1.png" alt="" width="428" height="393" class="alignnone size-full wp-image-1407" />
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>この時点で、テストスイートが greenになっていることを確認してみましょう。</li>
	<li>表 12.1の名前付きルートでは、_pathではなく_urlを使うように記してあります。なぜでしょうか? 考えてみましょう。ヒント: アカウント有効化で行った演習 (11.1.1.1) と同じ理由です。</li>
</ol>
</blockquote>
<ol>
	<li>GREEN</li>
	<li>ページ内の相対リンクからではなく、メール本文の絶対リンクからリクエストを送るため</li>
</ol>
<h3>12.1.2 新しいパスワードの設定</h3>
パスワードの再設定もアカウント有効化の場合と同じく、トークン用の仮想的な属性とそれに対応するダイジェストを用意する
何度も言われているように、トークンをハッシュ化せずに平文でDBに保存するとセキュリティ上大問題となる！
<blockquote class="quote-rort">攻撃者がユーザーのメールアドレスにパスワード再設定のリクエストを送信し、このメールと盗んだトークンを組み合わせて攻撃者がパスワード再設定リンクを開けば、アカウントを奪い取ることができてしまう、ということです。</blockquote>
→パスワードの再設定では！必ず！ダイジェストを使用する！！
また、再設定用のリンクはなるべく短時間で期限切れするようにしたい（本文には数時間とあるけど、30分以内に～とかよく見るよね）
→再設定用のメール送信時刻も記録する必要がある
<h4>パスワード再設定用のカラムをUserモデルに追加</h4>
上記に基づいてreset_digest属性とreset_sent_at属性をUserモデルに追加する
<pre class="lang:default mark:(1-2,6-6) decode:true " title="ターミナル" >$ rails generate migration add_reset_to_users reset_digest:string \
> reset_sent_at:datetime
Running via Spring preloader in process 13970
      invoke  active_record
      create    db/migrate/タイムスタンプ_add_reset_to_users.rb
$ rails db:migrate</pre> 
パスワード再設定用のカラムを追加したUserモデル→<a href="https://railstutorial.jp/chapters/password_reset?version=5.1#fig-user_model_password_reset" rel="noopener noreferrer" target="_blank">RailsTutorial本文</a>
<h4>パスワード再設定画面を作成</h4>
<a href="https://railstutorial.jp/chapters/basic_login?version=5.1#code-login_form" title="ログインフォームのコード" rel="noopener noreferrer" target="_blank">ログインフォームのコード</a>を参考にパスワードを再設定するためのフォームを作成する
<pre class="lang:ruby decode:true " title="/sample_app/app/views/password_resets/new.html.erb" ><% provide(:title, "Forgot password") %>
<h1><%= t('.forgot_password?') %></h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(:password_reset, url: password_resets_path) do |f| %>
      <%= f.label :email, User.human_attribute_name(:email) %>
      <%= f.email_field :email, class: 'form-control' %>

      <%= f.submit t('.reset'), class: "btn btn-primary" %>
    <% end %>
  </div>
</div></pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>リスト 12.4のform_forメソッドでは、なぜ@password_resetではなく:password_resetを使っているのでしょうか? 考えてみてください。</li>
</ol>
</blockquote>
<ol>
	<li>password_resetはpassword_resetモデルがない→インスタンス変数に相当するものがない→form_forヘルパーに追加の情報を独自に渡す必要がある（渡すオブジェクトがないのでリソースの名前とそれに対応するURLを具体的に指定する必要がある）
</li>
</ol>
<h3>12.1.3 createアクションでパスワード再設定</h3>
createアクションの動き
<blockquote class="quote-rort">フォームから送信を行なった後、メールアドレスをキーとしてユーザーをデータベースから見つけ、パスワード再設定用トークンと送信時のタイムスタンプでデータベースの属性を更新する必要があります。それに続いてルートURLにリダイレクトし、フラッシュメッセージをユーザーに表示します。送信が無効の場合は、ログイン (リスト 8.11) と同様にnewページを出力してflash.nowメッセージを表示します。変更の結果をリスト 12.5に示します。</blockquote>
 
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/password_resets_controller.rb" >class PasswordResetsController < ApplicationController
  def new
  end

  def create
    # @userに代入→（フォームに入力された）email(を小文字にしたやつ）を持ったuserをDBから見つけてる
    @user = User.find_by(email: params[:password_reset][:email].downcase)
    # もし　@userが存在すれば
    if @user
      # @userのパスワード再設定の属性を設定する（create_reset_digestはapp/models/user.rbにある）
      @user.create_reset_digest
      # @userにパスワード再設定メールを送る（send_password_reset_emailはapp/models/user.rbにある）
      @user.send_password_reset_email
      # （インフォで）flashメッセージを表示
      flash[:info] = "Email sent with password reset instructions"
      # rootにリダイレクト
      redirect_to root_url
    # （@userが）存在しなければ
    else
      # （デンジャーで）flashメッセージを表示
      flash.now[:danger] = "Email address not found"
      # newを描画
      render 'new'
    end
  end

  def edit
  end
end
</pre> 
Userモデルにメソッドを追加
<pre class="lang:ruby mark:(3-3,7-20) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  #仮想の属性:remember_token、:activation_token、:reset_tokenをUserクラスに定義
  attr_accessor :remember_token, :activation_token, :reset_token
・
・
・
  # パスワード再設定の属性を設定する
  def create_reset_digest
    # （呼び出し先で考えると）@userのreset_tokenに代入→User.new_token
    self.reset_token = User.new_token
    # :reset_digestの値をUser.digest(reset_token)で上書き保存
    update_attribute(:reset_digest, User.digest(reset_token))
    # :reset_sent_atの値をTime.zone.nowで上書き保存
    update_attribute(:reset_sent_at, Time.zone.now)
  end

  # パスワード再設定のメールを送信する
  def send_password_reset_email
    UserMailer.password_reset(self).deliver_now
  end

    private
・
・
・</pre> 
現状、無効なメールアドレスを入力した場合は正常に作動するけど
有効なメールアドレスを入力した場合はエラーになる
（パスワード再設定のメイラーメソッドを定義する必要があるので次の章でやるっぽい）
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>試しに有効なメールアドレスをフォームから送信してみましょう (図 12.6)。どんなエラーメッセージが表示されたでしょうか?</li>
	<li>コンソールに移り、先ほどの演習課題で送信した結果、(エラーと表示されてはいるものの) 該当するuserオブジェクトにはreset_digestとreset_sent_atがあることを確認してみましょう。また、それぞれの値はどのようになっていますか?</li>
</ol>
</blockquote>
1.
<img src="https://rakuda3desu.net/wp-content/uploads/2020/04/12.1.3-1.png" alt="" width="687" height="296" class="alignnone size-full wp-image-1414" />
引数のエラー→PasswordResetsController#create→問題のあるソース（app/mailers/user_mailer.rb:10:in `password_reset'）
2.
<pre class="lang:default mark:(7-10) decode:true " title="userをコンソールで呼び出した結果" >=> #<User id: 1, name: "Example User", email: "example@railstutorial.org",
    created_at: "2020-04-09 04:57:00", updated_at: "2020-04-14 07:10:23",
    password_digest: "$2a$10$69GKTgVlsOK/eis0pLfdw.xyqVvphrb030yUp.UCmCV...",
    remember_digest: nil, admin: true,
    activation_digest: "$2a$10$y3BA/9QXPjjE51k3UFPRoeLq5pE3uhVhXXaaWOXuTWb...",
    activated: true, activated_at: "2020-04-09 04:57:00",
    # ダイジェストが入力されている
    reset_digest: "$2a$10$SA0jULevKYw6Sgfq/fSJFe9hx80aBnD5kNavM11rO07...",
    #メールが送信された（はずの）時刻が入力されている
    reset_sent_at: "2020-04-14 07:10:23"></pre> 
<h3>まとめとか感想</h3>
フォームについて、わけ！わか！らん！となってかなり遡って見直したりしました
Railsチュートリアル内の話だし、実際はもっと違うのかもしれないとかも思いつつだけど
<del datetime="2020-04-14T09:28:57+00:00">自力でひとまず納得できたので良しとしましょうそうしましょう！</del>
「@password_resetではなく:password_resetを使っているのでしょうか? 」の回答にモヤっとしたものが残るけど進めていくうちにわかるかもしれないのでとりあえずほかにメモった。
あとアレ。タイポの癖ってあるよね😢
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

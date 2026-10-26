---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜9.1"
date: "2020-01-15 19:57:04"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1170
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial9-1/"
---
9章でやる事
（任意で）ユーザーのログイン情報を記憶しておけるようにする
→remember me機能
永続cookieを使ってremember me機能を実現
<h2>9.1 Remember me 機能</h2>
ブラウザを閉じた後でもログイン状態を有効にする[remember me] 機能を実装していく
<blockquote class="quote-rort">この機能を使うと、ユーザーが明示的にログアウトを実行しない限り、ログイン状態を維持することができるようになります。</blockquote>
 
<pre class="lang:default decode:true " title="ターミナル（作業用ブランチを切る）" >git checkout -b advanced-login</pre> 

<h3>9.1.1 記憶トークンと暗号化</h3>
8.2で行ったsessionメソッドを使ってユーザーIDを保存する方法ではブラウザを閉じるとユーザー情報が消えてしまう。
<blockquote class="quote-rort">本節では、セッションの永続化の第一歩として記憶トークン (remember token) を生成し、cookiesメソッドによる永続的cookiesの作成や、安全性の高い記憶ダイジェスト (remember digest) によるトークン認証にこの記憶トークンを活用します。</blockquote>
<h4>トークンとはなんぞ</h4>
パスワード→ユーザーが作成・管理する情報
トークン→コンピューターが作成・管理する情報
パスワードの<strong>平文と同じような</strong>秘密情報
<h4>永続的セッション作成作成の手順</h4>
<blockquote class="quote-rort">
<ol>
	<li>記憶トークンにはランダムな文字列を生成して用いる。</li>
	<li>ブラウザのcookiesにトークンを保存するときには、有効期限を設定する。</li>
	<li>トークンはハッシュ値に変換してからデータベースに保存する。</li>
	<li>ブラウザのcookiesに保存するユーザーIDは暗号化しておく。</li>
	<li>永続ユーザーIDを含むcookiesを受け取ったら、そのIDでデータベースを検索し、記憶トークンのcookiesがデータベース内のハッシュ値と一致することを確認する。</li>
</ol>
</blockquote>
<h4>remember_digest属性を追加</h4>
remember_digest属性をUserモデルに追加する
<pre class="lang:default mark:（2-2) decode:true " title="ターミナル" >#文字列のデータ型を持つremember_digest属性を追加
$ rails generate migration add_remember_digest_to_users remember_digest:string
Running via Spring preloader in process 4817
      invoke  active_record
      create    db/migrate/20191207002822_add_remember_digest_to_users.rb
#マイグレーションを実行
$ rails db:migrate
</pre> 
<h4>記憶トークンとして何を使うか</h4>
基本的には長くてランダムな文字列であれば何でもOK！
本文ではRuby標準ライブラリのSecureRandomモジュールにあるurlsafe_base64メソッドを利用する
→いろいろと都合が良い<a href="https://railstutorial.jp/chapters/advanced_login?version=5.1#cha-9_footnote-ref-6" rel="noopener noreferrer" target="_blank">（本文参照）</a>
<h4>ユーザーを記憶する仕組みを作る準備</h4>
記憶トークンを作成→ダイジェストに変換したものをDBに保存
なので、記憶トークンを作成するメソッドを定義する
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  #ランダムなトークンを返す
  def User.new_token
    SecureRanddom.urlsafe_base64
  end
end</pre> 
ダイジェストに変換するためのメソッドはfixture用に作った（<a href="https://railstutorial.jp/chapters/basic_login?version=5.1#code-digest_method" rel="noopener noreferrer" target="_blank">digestメソッド</a>）が利用できる
<h4>user.rememberメソッドを作成</h4>
記憶トークンとユーザーを関連付け、トークンに対応する記憶ダイジェストをDBに保存する。
Userモデルにはremember_digest属性を追加したがremember_token属性はまだ追加していない。
<blockquote class="quote-rort">user.remember_tokenメソッドを使ってトークンにアクセスできるようにし、かつ、トークンをデータベースに保存せずに実装する必要があります。
</blockquote>
→<a href="https://railstutorial.jp/chapters/modeling_users?version=5.1#sec-adding_a_secure_password" rel="noopener noreferrer" target="_blank">パスワードの実装と同じ手法で</a>実装する
パスワード実装の際は仮想のpassword属性はhas_secure_passwordメソッドで自動的に作成されたけど
remember_tokenの仮想の属性は自分で定義する必要がある
<h4>attr_accessorを使って「仮想の」属性を作成</h4>
attr_accessor→<a href="https://railstutorial.jp/chapters/rails_flavored_ruby?version=5.1#sec-a_user_class" rel="noopener noreferrer" target="_blank">RoRT本文</a>、<a href="https://rakuda3desu.net/rakudas-rails-tutorial4-4/" rel="noopener noreferrer" target="_blank">らくだ🐫にもできるRailsチュートリアル｜4.4（と4.5）</a>
<pre class="lang:ruby mark:(2-3,8-12) decode:true " title="コード解説" >class User < ApplicationRecord
  #remember_token属性をUserクラスに定義
  attr_accessor :remember_token
  .
  .
  .
  def remember
    #remember_tokenに要素を代入
    #（selfを付けるとクラス変数になる→この場合User.remember_tokenと同意）
    self.remember_token = ...
    #validationを無視して更新（引数にハッシュで更新したい属性と値をセット）
    update_attribute(:remember_digest, ...)
  end
end</pre> 
上記を考慮して実際にコードを書いていく
<blockquote class="quote-rort">最初にUser.new_tokenで記憶トークンを作成し、続いてUser.digestを適用した結果で記憶ダイジェストを更新します。
</blockquote>
<pre class="lang:ruby mark:(2-3,20-23,25-31) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  #:remember_token属性を定義
  attr_accessor :remember_token
  before_save { email.downcase! }
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
  has_secure_password
  validates :password, presence: true, length: { minimum: 6 }
  
  # 渡された文字列のハッシュ値を返す
  def User.digest(string)
    cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST :
                                                  BCrypt::Engine.cost
    BCrypt::Password.create(string, cost: cost)
  end
  
  # ランダムなトークンを返す
  def User.new_token
    SecureRandom.urlsafe_base64
  end

  # 永続セッションのためにユーザーをデータベースに記憶する
  def remember
    #remember_tokenに　User.new_tokenを代入
    self.remember_token = User.new_token
    #validationを無視して更新　（:remember_digest属性にハッシュ化したremember_tokenを）
    update_attribute(:remember_digest, User.digest(remember_token))
  end
end</pre> 

<h4>省略されるselfと省略できないself</h4>
上記の
<pre class="toolbar:2 start-line:26 lang:ruby decode:true " >  def remember
    #remember_tokenに　User.new_tokenを代入
    self.remember_token = User.new_token
    #validationを無視して更新　（:remember_digest属性にハッシュ化したremember_tokenを）
    update_attribute(:remember_digest, User.digest(remember_token))
  end</pre>
selfの有無によって　self.remember_token（インスタンス変数）　remember_token（メソッド内の変数）になっちゃうから
30行目の<strong>(remember_token)</strong>に<strong>self.</strong>つけないとじゃない？っていう話が出まして
この二つのself.remember_tokenとremember_tokenは何が違ってどう使い分けられてるのって思って調べたところ
本来はselfついてるけど省略されているよ！ということのようです。
[blogcard url="https://qiita.com/akira-hamada/items/4132d2fda7e420073ab7"]
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>コンソールを開き、データベースにある最初のユーザーを変数userに代入してください。その後、そのuserオブジェクトからrememberメソッドがうまく動くかどうか確認してみましょう。また、remember_tokenとremember_digestの違いも確認してみてください。</li>
	<li>リスト 9.3では、明示的にUserクラスを呼び出すことで、新しいトークンやダイジェスト用のクラスメソッドを定義しました。実際、User.new_tokenやUser.digestを使って呼び出せるようになったので、おそらく最も明確なクラスメソッドの定義方法であると言えるでしょう。しかし実は、より「Ruby的に正しい」クラスメソッドの定義方法が２通りあります。1つはややわかりにくく、もう1つは非常に混乱するでしょう。テストスイートを実行して、ややわかりにくいリスト 9.4の実装でも、非常に混乱しやすいリスト 9.5の実装でも、いずれも正しく動くことを確認してみてください。ヒント: selfは、通常の文脈ではUser「モデル」、つまりユーザーオブジェクトのインスタンスを指しますが、リスト 9.4やリスト 9.5の文脈では、selfはUser「クラス」を指すことにご注意ください。わかりにくさの原因の一部はこの点にあります</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="1.　ターミナル" >user = User.first
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
=> #<User id: 1, name: "Rails Tutorial", email: "example@railstutorial.org", created_at: "2019-11-11 07:06:24", updated_at: "2019-11-11 07:06:24", password_digest: "$2a$10$IgiS5ygszAZUdJm6JKj.D.eNYlRHOmtjKolGn7W3ZFm...", remember_digest: nil>
>> user.remember
   (0.1ms)  SAVEPOINT active_record_1
  SQL (2.0ms)  UPDATE "users" SET "updated_at" = ?, "remember_digest" = ? WHERE "users"."id" = ?  [["updated_at", "2019-12-07 11:18:30.679106"], ["remember_digest", "$2a$10$DuLlBBvdMatNAGszRMJYI.LYcGDgUDaYwk5JH9VdElgy/NU4qWTyu"], ["id", 1]]
   (0.1ms)  RELEASE SAVEPOINT active_record_1
=> true
>> user.remember_token
#User.new_tokenで作成したランダムな文字数
=> "82YlzKQ6VPoaFjebLs9DKg"
>> user.remember_digest
#User.new_tokenで作成したランダムな文字数をハッシュ化したもの
=> "$2a$10$DuLlBBvdMatNAGszRMJYI.LYcGDgUDaYwk5JH9VdElgy/NU4qWTyu"</pre> 
<pre class="lang:ruby mark:(6-7,14-15,29-30,39-39),32-32,, decode:true " title="2.app/models/user.rb" >class User < ApplicationRecord
  .
  .
  .
  # 渡された文字列のハッシュ値を返す
  #User.digest(string)と同じ意味
  def self.digest(string)
    cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST :
                                                  BCrypt::Engine.cost
    BCrypt::Password.create(string, cost: cost)
  end

  # ランダムなトークンを返す
  #User.new_tokenと同じ意味
  def self.new_token
    SecureRandom.urlsafe_base64
  end
  .
  .
  .
end

#↑コレを更にコウ↓する

class User < ApplicationRecord
  .
  .
  .
  #このスコープの中に書かれたメソッドはクラスメソッドになる
  class << self
    # 渡された文字列のハッシュ値を返す
    def digest(string)
      cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST :
                                                    BCrypt::Engine.cost
      BCrypt::Password.create(string, cost: cost)
    end

    # ランダムなトークンを返す
    def new_token
      SecureRandom.urlsafe_base64
    end
  end
  .
  .
  .</pre> 
<h4>selfとはなんぞ</h4>
[blogcard url="https://magazine.rubyist.net/articles/0046/0046-SingletonClassForBeginners.html"]
クラスメソッドを定義するもの
クラス名.メソッド名は明確な定義方法ではあるけれど
<blockquote class="quote-rort">より「Ruby的に正しい」クラスメソッドの定義方法</blockquote>
と言う事のようです
<h3>9.1.2 ログイン状態の保持</h3>
<blockquote class="quote-rort">user.rememberメソッドが動作するようになったので、ユーザーの暗号化済みIDと記憶トークンをブラウザの永続cookiesに保存して、永続セッションを作成する準備ができました。</blockquote>
cookiesメソッドを用いて永続セッションを作成
<h4>cookiesメソッド</h4>
sessionメソッドと同様にハッシュとして扱える
１つのvalue （値） とオプションのexpires （有効期限）から出来ている（有効期限は省略も可能）
参考記事
[blogcard url="https://qiita.com/coe401_/items/ad7dc2f3e319c5beaf40"]
<pre class="lang:ruby decode:true " title="参考コード" >#20年後に期限切れになる設定
cookies[:remember_token] = { value:   remember_token,
                             expires: 20.years.from_now.utc }

#よく使われる設定なのでpermanentという専用メソッドが追加されている
#20年後に期限切れになる設定のコードを更にコンパクトに
cookies.permanent[:remember_token] = remember_token
</pre> 
<h4>署名付きcookie</h4>
<pre class="lang:ruby decode:true " title="参考コード" >#ユーザーIDをcookiesに保存
cookies[:user_id] = user.id

#↑このままではIDが生のテキストとしてcookiesに保存されるため「署名付きcookie」を使い暗号化する
#signedはデジタル署名と暗号化をまとめて実行してくれる
cookies.signed[:user_id] = user.id

#ユーザーIDと記憶トークンはペアで扱うのでcookieも永続化する必要がある
#よってsignedとpermanentをメソッドチェーンで繋いで使う
cookies.permanent.signed[:user_id] = user.id

#cookiesを設定しておく事で、cookiesからuserを取り出すことが出来るようになる
#自動的にユーザーIDのcookiesの暗号が解除され元に戻る
User.find_by(id: cookies.signed[:user_id])
</pre> 
bcryptで確認する
<pre class="lang:ruby decode:true " title="参考コード" >#cookies[:remember_token]がremember_digestと一致することを確認
BCrypt::Password.new(remember_digest).is_password?(remember_token)</pre> 
ここまでの参考コードをもとにして記憶トークンと記憶ダイジェストを比較する
<pre class="lang:ruby mark:(30-33) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  attr_accessor :remember_token
  before_save { self.email = email.downcase }
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-.]+\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
  has_secure_password
  validates :password, presence: true, length: { minimum: 6 }

  # 渡された文字列のハッシュ値を返す
  def User.digest(string)
    cost = ActiveModel::SecurePassword.min_cost ? BCrypt::Engine::MIN_COST :
                                                  BCrypt::Engine.cost
    BCrypt::Password.create(string, cost: cost)
  end

  # ランダムなトークンを返す
  def User.new_token
    SecureRandom.urlsafe_base64
  end

  # 永続セッションのためにユーザーをデータベースに記憶する
  def remember
    self.remember_token = User.new_token
    update_attribute(:remember_digest, User.digest(remember_token))
  end

  # 渡されたトークンがダイジェストと一致したらtrueを返す
  def authenticated?(remember_token)
    BCrypt::Password.new(remember_digest).is_password?(remember_token)
  end
end</pre> 
<blockquote class="quote-rort">authenticated?メソッドのローカル変数として定義したremember_tokenは、リスト 9.3のattr_accessor :remember_tokenで定義したアクセサとは異なる点に注意してください (リスト 9.6)。今回の場合、is_password?の引数はメソッド内のローカル変数を参照しています。もう１つ、remember_digestの属性の使い方にもご注目ください。この使い方はself.remember_digestと同じであり、すなわち第6章のnameやemailの使い方と同じになります。</blockquote>
remember_digestの属性はデータベースのカラムに対応している→Active Recordによって簡単に取得したり保存したり出来る
<h4>rememberヘルパーメソッドを追加</h4>
<pre class="lang:default mark:(8-13) decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >module SessionsHelper
  
  # 渡されたユーザーでcookieを使ってログインする
  def log_in(user)
    session[:user_id] = user.id
  end
  
  # ユーザーのセッションを永続的にする
  def remember(user)
    user.remember
    cookies.permanent.signed[:user_id] = user.id
    cookies.permanent[:remember_token] = user.remember_token
  end

・
・
・</pre> 
<h4>ログインしてユーザーを保持</h4>
<pre class="lang:default mark:(10-11) decode:true " title="/sample_appapp/controllers/sessions_controller.rb" >class SessionsController < ApplicationController

  def new
  end

  def create
    user = User.find_by(email: params[:session][:email].downcase)
    if user && user.authenticate(params[:session][:password])
      log_in user
      #Sessionsヘルパーのrememberメソッド
      remember user
      redirect_to user
    else
      flash.now[:danger] = 'Invalid email/password combination'
      render 'new'
    end
  end

  def destroy
    log_out
    redirect_to root_url
  end
end</pre> 
ログインするユーザーはブラウザで有効な記憶トークンを得られるようになった！
→current_userメソッドが正常に作動しなくなっている！！（一時セッションしか扱っていないため）
<pre class="lang:ruby decode:true " title="参考コード" >#現在のコード
#session[:user_id]の値を取って
if session[:user_id]
  #@current_user自身　もしくは　Userモデルの中のidがsessionに入ったuser_idと合致する場合　データを返す
  @current_user ||= User.find_by(id: session[:user_id])
end

#↑コレを↓こうする

if session[:user_id]
  @current_user ||= User.find_by(id: session[:user_id])
#上記以外の場合　cookies.signed[:user_id]の値を取って
elsif cookies.signed[:user_id]
  #userに　cookiesから取り出したidの値がUser.idと一致するユーザーを代入
  user = User.find_by(id: cookies.signed[:user_id])
  #もし　userが存在する　かつ　cookieが持つトークンがダイジェストと一致する場合
  if user && user.authenticated?(cookies[:remember_token])
    #渡されたユーザーでログイン
    log_in user
    #@current_userにuserを代入
    @current_user = user
  end
end

#さらにローカル変数を使ってコードを整理
#もし　user_idに:user_idのsessionを代入した値を取って
if (user_id = session[:user_id])
  #current_user自身　もしくは　Userモデルの中のidがuser_idと合致する場合　データを返す
  @current_user ||= User.find_by(id: user_id)
#上記以外の場合　user_idにcookies.signed[:user_id]を代入
elsif (user_id = cookies.signed[:user_id])
　#userに　user_idの値がUser.idと一致するユーザーを代入
  user = User.find_by(id: user_id)
  if user && user.authenticated?(cookies[:remember_token])
    log_in user
    @current_user = user
  end
end</pre> 
上記を参考にcurrent_userヘルパーを定義
<pre class="lang:ruby decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >・
・
・
  # 記憶トークンcookieに対応するユーザーを返す
  def current_user
    if (user_id = session[:user_id])
      @current_user ||= User.find_by(id: user_id)
    elsif (user_id = cookies.signed[:user_id])
      user = User.find_by(id: user_id)
      if user && user.authenticated?(cookies[:remember_token])
        log_in user
        @current_user = user
      end
    end
  end
・
・
・</pre> 
<blockquote class="quote-rort">アプリケーションに現在残された問題はあと1つだけです。ブラウザのcookiesを削除する手段が未実装なので (20年待てば消えますが)、ユーザーがログアウトできません。これは当然テストスイートでキャッチすべき問題であり、redにならなければなりません。</blockquote>
→9.1.3で修正
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>ブラウザのcookieを調べ、ログイン後のブラウザではremember_tokenと暗号化されたuser_idがあることを確認してみましょう</li>
	<li>コンソールを開き、リスト 9.6のauthenticated?メソッドがうまく動くかどうか確かめてみましょう。</li>
</ol>
</blockquote>
動作確認のみにて省略
<h3>9.1.3 ユーザーを忘れる</h3>
ログアウトの機能を作る
<blockquote class="quote-rort">ユーザーを記憶するためのメソッドと同様の方法で、ユーザーを忘れるためのメソッドを定義します。このuser.forgetメソッドによって、user.rememberが取り消されます。具体的には、記憶ダイジェストをnilで更新します</blockquote>
<pre class="lang:ruby mark:(6-7) decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # ユーザーのログイン情報を破棄する
  def forget
    #validationを無視して更新（:remember_digestの値をnilに）
    update_attribute(:remember_digest, nil)
  end
end</pre> 
上記のforgetメソッドを使って永続セッションからログアウトするヘルパーメソッドを定義する
forgetヘルパーメソッドの新規作成とlog_outヘルパーメソッドへの追加
<pre class="lang:ruby mark:(4-9,13-13) decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >・
・
・
  # 永続的セッションを破棄する
  def forget(user)
    user.forget
    cookies.delete(:user_id)
    cookies.delete(:remember_token)
  end

  # 現在のユーザーをログアウトする
  def log_out
    forget(current_user)
    session.delete(:user_id)
    @current_user = nil
  end
end</pre> 
<h4>演習</h4>
動作確認のみにて省略
<h3>9.1.4 ２つの目立たないバグ</h3>
お互いが関連しあったバグが2つ残っている
<h4>一つ目のバグ</h4>
<blockquote class="quote-rort">ユーザーが1つのタブでログアウトし、もう1つのタブで再度ログアウトしようとするとエラーになってしまいます。これは、もう1つのタブで "Log out" リンクをクリックすると、current_userがnilとなってしまうため、log_outメソッド内のforget(current_user)が失敗してしまうからです</blockquote>
ユーザーがログイン中の場合にのみログアウトできるようにすることで回避
<h4>2つ目のバグ</h4>
<blockquote class="quote-rort">2番目の地味な問題は、ユーザーが複数のブラウザ (FirefoxやChromeなど) でログインしていたときに起こります。例えば、Firefoxでログアウトし、Chromeではログアウトせずにブラウザを終了させ、再度Chromeで同じページを開くと、この問題が発生します</blockquote>
→上記の例を具体的に考えてみる
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  #ユーザーがFirefoxからログアウトすると
  #forgetメソッドによりremember_digestがnilになる
  def forget
    update_attribute(:remember_digest, nil)
  end
end</pre> 
この時点ではまだ問題ない
<pre class="lang:ruby decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >・
・
・
  #この時log_outメソッドによりユーザーIDが削除されている
  def log_out
    forget(current_user)
    session.delete(:user_id)
    @current_user = nil
  end
end
</pre> 
これにより、下記でハイライトされている2行はfalseとなる
<pre class="lang:ruby mark:(6-6,8-8) decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >・
・
・
  # 記憶トークンcookieに対応するユーザーを返す
  def current_user
    if (user_id = session[:user_id])
      @current_user ||= User.find_by(id: user_id)
    elsif (user_id = cookies.signed[:user_id])
      user = User.find_by(id: user_id)
      if user && user.authenticated?(cookies[:remember_token])
        log_in user
        @current_user = user
      end
    end
  end</pre> 
結果、current_userメソッドの結果はにnilになる
↑ここまでFireFox
↓ここからChrome
<blockquote class="quote-rort">Chromeを閉じたとき、session[:user_id]はnilになります (これはブラウザが閉じたときに、全てのセッション変数の有効期限が切れるためです)。しかし、cookiesはブラウザの中に残り続けているため、Chromeを再起動してサンプルアプリケーションにアクセスすると、データベースからそのユーザーを見つけることができてしまいます。</blockquote>
下記ハイライト部分でユーザーを見つけることが出来てしまう
<pre class="lang:ruby mark:(8-9) decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >・
・
・
  # 記憶トークンcookieに対応するユーザーを返す
  def current_user
    if (user_id = session[:user_id])
      @current_user ||= User.find_by(id: user_id)
    elsif (user_id = cookies.signed[:user_id])
      user = User.find_by(id: user_id)
      if user && user.authenticated?(cookies[:remember_token])
        log_in user
        @current_user = user
      end
    end
  end
・
・
・</pre> 
その結果上記の10行目部分
<pre class="start-line:10 toolbar:2 lang:ruby decode:true " >user && user.authenticated?(cookies[:remember_token])</pre> 
残っていたcookiesの情報から取り出したユーザーの情報がuserに代入されている為
2番目の条件式まで評価が進む
→FireFoxでログアウト時remember_digestが削除されているが下記6行目のコードを実行してしまうためエラーが起こる 
（bcryptライブラリ内部での例外が発生）
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # 渡されたトークンがダイジェストと一致したらtrueを返す
  def authenticated?(remember_token)
    BCrypt::Password.new(remember_digest).is_password?(remember_token)
  end
・
・
・</pre> 
→これを解決するためにremember_digestが存在しないときはfalseを返す処理をauthenticated?に追加する必要がある
<h4>テスト開発駆動！</h4>
ユーザーログインの統合テストに2番目のウィンドウでログアウトをクリックした状況をシミュレートする
<pre class="lang:ruby mark:(8-8) decode:true " title="/sample_app/test/integration/users_login_test.rb" >・
・
・
    delete logout_path
    assert_not is_logged_in?
    assert_redirected_to root_url
    #2度目のlogout_pathへdeleteのリクエスト
    delete logout_path
    follow_redirect!
    assert_select "a[href=?]", login_path
    assert_select "a[href=?]", logout_path,      count: 0
    assert_select "a[href=?]", user_path(@user), count: 0
  end
end</pre> 
<blockquote class="quote-rort">current_userがないために2回目のdelete logout_pathの呼び出しでエラーが発生し、テストスイートは redになります。</blockquote>
テストが成功するようにコードを修正
→logged_in?がtrueの場合に限ってlog_outを呼び出す
 
<pre class="lang:ruby mark:(5-5) decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >  .
  .
  .
  def destroy
    log_out if logged_in?
    redirect_to root_url
  end
end</pre> 
<blockquote class="quote-rort">2番目の問題についてですが、統合テストで2種類のブラウザをシミュレートするのは正直かなり困難です。その代わり、同じ問題をUserモデルで直接テストするだけなら簡単に行えます。</blockquote>
記憶ダイジェストを持たないユーザーを用意
↓
チェーンメソッドでauthenticated?を呼び出す（記憶トークンが空欄のままなのは記憶トークンが使われる前にエラーが発生するから別になんでもいいからのとこと）
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb" >・
・
・
  test "authenticated? should return false for a user with nil digest" do
    #falseである　→@userのauthenticated?('')
    assert_not @user.authenticated?('')
  end
end
</pre> 
authenticated?メソッドの途中でエラーが発生→RED
これを通すため記憶ダイジェストがnilの場合にfalseを返すようにする
<pre class="lang:ruby mark:(6-7) decode:true " title="/sample_app/app/models/user.rb" >  .
  .
  .
  # 渡されたトークンがダイジェストと一致したらtrueを返す
  def authenticated?(remember_token)
    #記憶ダイジェストがnilの場合にfalseを返す
    return false if remember_digest.nil?
    BCrypt::Password.new(remember_digest).is_password?(remember_token)
  end

  # ユーザーのログイン情報を破棄する
  def forget
    update_attribute(:remember_digest, nil)
  end
end</pre> 
以上で目立たない二つのバグが修正されました🎉
<h4>演習</h4>
動作確認のみにて省略
<h3>まとめとか感想</h3>
今回の部分は本当に訳が分からなくて
何度も読み返しつつ行ったり来たりしました挙句
年末年始をはさんだりPCの新調があったりで余計間が空いたりでした。
いまだによくわからなかったりはしてるのだけど後から何度も見直していきたい！🐫
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

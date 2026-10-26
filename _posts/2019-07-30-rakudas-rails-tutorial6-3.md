---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜6.3（と6.4）"
date: "2019-07-30 17:41:00"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 880
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial6-3/"
---
<h2>6.3 セキュアなパスワードを追加する</h2>
セキュアなパスワード！超大切！！
パスワードをそのままの形ではなく、ハッシュ化してDBに保存する
<blockquote class="quote-rort">
ユーザーの認証は、パスワードの送信、ハッシュ化、データベース内のハッシュ化された値との比較、という手順で進んでいきます。比較の結果が一致すれば、送信されたパスワードは正しいと認識され、そのユーザーは認証されます。ここで、生のパスワードではなく、ハッシュ化されたパスワード同士を比較していることに注目してください。こうすることで、生のパスワードをデータベースに保存するという危険なことをしなくてもユーザーを認証できます。これで、仮にデータベースの内容が盗まれたり覗き見されるようなことがあっても、パスワードの安全性が保たれます。
</blockquote>
<h4>ハッシュ化とは</h4>
Twitterで見かけたやつなんですが
ジャガイモをハッシュポテトにすると元のジャガイモには戻せないって言うヤツがわかりやすかったです
<h3>6.3.1 ハッシュ化されたパスワード</h3>
Userモデルにhas_secure_passwordというメソッドを呼び出す
<pre class="lang:ruby mark:（5-5） decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
・
・
・
  has_secure_password
end</pre> 
<blockquote class="quote-rort">
<ul>
<li>
セキュアにハッシュ化したパスワードを、データベース内のpassword_digestという属性に保存できるようになる。
</li>
<li>
2つのペアの仮想的な属性 (passwordとpassword_confirmation) が使えるようになる。また、存在性と値が一致するかどうかのバリデーションも追加される。
</li>
<li>
authenticateメソッドが使えるようになる (引数の文字列がパスワードと一致するとUserオブジェクトを、間違っているとfalseを返すメソッド)。
</li>
</ul>
</blockquote>
<h4>password_digest属性</h4>
has_secure_passwordの機能を使えるようにするために
Userモデルにpassword_digest属性を追加する（passwordではないので注意！）
password_digest→暗号化用ハッシュ関数という用語が語源
<h4>DBにpassword_digestカラムを追加する</h4>
マイグレーションファイルを生成する
<pre class="lang:default decode:true " title="ターミナル" >#Rails generate　で　migrationファイルを作る
>> rails generate migration add_password_digest_to_users　password_digest:string
#タイムスタンプ_add_password_digest_to_users.rb（usersテーブルにpassword_digestを追加）　という名前のファイルが生成される
#ファイル名指定の末尾をto_usersにすることでusersテーブルにカラムを追加するマイグレーションが自動生成される
#password_digest:string　→追加するカラム名password_digest, 値:string
</pre>
<pre class="lang:ruby decode:true " title="/sample_app/db/migrate/タイムスタンプ_add_password_digest_to_users.rb
">class AddPasswordDigestToUsers < ActiveRecord::Migration[5.1]
  def change
    add_column :users, :password_digest, :string
  end
end
#内容は自動的に生成されている</pre>
<pre class="lang:default decode:true " title="ターミナル" >#migrateを実行してDBへ変更を反映させる
$ rails db:migrate
</pre>
<h4>bcrypt gemを追加</h4>
has_secure_passwordを使う為に必要なgemを追加
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/Gemfile" >source 'https://rubygems.org'

gem 'rails',          '5.1.6'
gem 'bcrypt',         '3.1.12'
.
.
.</pre> 
<pre class="lang:default decode:true " title="ターミナル" >$ bundle install</pre>
<h3>6.3.2 ユーザーがセキュアなパスワードを持っている</h3>
現状テストは成功しない
<blockquote class="quote-rort">
テストが失敗する理由は、6.3.1で触れたようにhas_secure_passwordには、仮想的なpassword属性とpassword_confirmation属性に対してバリデーションをする機能も(強制的に)追加されているからです。しかしリスト 6.26のテストでは、@user 変数にこのような値がセットされておりません。
</blockquote>
→値をセットする
<pre class="lang:default mark:(7-7) decode:true " title="/sample_app/test/models/user_test.rb" >require 'test_helper'

class UserTest < ActiveSupport::TestCase

  def setup
    @user = User.new(name: "Example User", email: "user@example.com",
                     password: "foobar", password_confirmation: "foobar")
  end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
この時点では、userオブジェクトに有効な名前とメールアドレスを与えても、valid?で失敗してしまうことを確認してみてください。</li>
<li>なぜ失敗してしまうのでしょうか? エラーメッセージを確認してみてください。</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール・サンドボックス）" >#1.
#nameとemailの値を設定したuserオブジェクトを作成
>> user = User.new(name: "Example User", email: "user@example.com")
=> #<User id: nil, name: "Example User", email: "user@example.com", created_at: nil, updated_at: nil, password_digest: nil>
#valid?で失敗していることを確認
>> user.valid?
=> false
#2.エラーメッセージを確認（パスワードを空白にはできない）
>> user.errors.full_messages
=> ["Password can't be blank"]</pre>
<h3>6.3.3 パスワードの最小文字数</h3>
パスワードを簡単に割り出されないようにする為にも
パスワードの最小文字数を設定しておく方が良い
（滅茶苦茶長くされても困るから最大も設定して○文字から○文字ってするのがいいよね？）
（などなどのアレンジを番外編的にやりましょうそうしましょう）
ひとまず、パスワードが空ではない・6文字以上であるの2つを設定する。
テストから↓
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb
">require 'test_helper'

class UserTest < ActiveSupport::TestCase

  def setup
    @user = User.new(name: "Example User", email: "user@example.com",
                     password: "foobar", password_confirmation: "foobar")
  end
  .
  .
  .
  test "password should be present (nonblank)" do
    @user.password = @user.password_confirmation = " " * 6
    assert_not @user.valid?
  end
  #@user.password = @user.password_confirmationに空白を6個代入した時
  #@userは有効か　→　Falseである　と言うテスト

  test "password should have a minimum length" do
    @user.password = @user.password_confirmation = "a" * 5
    assert_not @user.valid?
  end
  #@user.password = @user.password_confirmationにaを5個代入した時
  #@userは有効か　→　Falseである　と言うテスト
end
#現状ではバリデーションが設定されていない略でGREENにならない→RED</pre>
<h4>多重代入とは</h4>
同じ値を代入したい変数を=で繋いで同時に代入する事
<span class="point">@user.password = @user.password_confirmation = "a" * 5</span>
@user.passwordと@user.password_confirmationに対して同時に代入をしている
<h4>最小文字数と存在性のバリデーションを追加</h4>
<blockquote class="quote-rort">has_secure_passwordメソッドは存在性のバリデーションもしてくれるのですが、これは新しくレコードが追加されたときだけに適用される性質を持っています。したがって、例えばユーザーが ’ ’ (6文字分の空白スペース) といった文字列をパスワード欄に入力して更新しようとすると、バリデーションが適用されずに更新されてしまう問題が発生してしまうのです。</blockquote>
→このため存在性のバリデーションも改めて設定しておく
<pre class="lang:ruby mark:(10-10) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  before_save { self.email = email.downcase }
  validates :name, presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-.]+\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
  has_secure_password
  #passwordの検証　存在　→　true,　長さ　→　最小6
  validates :password, presence: true, length: { minimum: 6 }
end
#passwordの存在性と最小値のバリデーションを略でGREEN</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
有効な名前とメールアドレスでも、パスワードが短すぎるとuserオブジェクトが有効にならないことを確認してみましょう。
</li>
<li>上で失敗した時、どんなエラーメッセージになるでしょうか? 確認してみましょう。</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール・サンドボックス）" >#1.最小文字数以下のパスワードでuserオブジェクトを作成
>> @user = User.new(name: "Example User", email: "user@example.com", password: "1234")
=> #<User id: nil, name: "Example User", email: "user@example.com", created_at: nil, updated_at: nil, password_digest: "$2a$10$U572fkMuSESFH5w.XoRmn.X5KR7IMetyom9AQNM.ioN...">
>> @user.valid?
#パスワードが短ければエラーになる
=> false
>> @user.errors.full_messages
=> ["Password is too short (minimum is 6 characters)"]
#2.パスワードが短い（最小6文字）</pre>
<h3>6.3.4 ユーザーの作成と認証</h3>
コンソールからユーザーを作成してDBに反映されているかを確認する
<pre class="lang:default decode:true " title="ターミナル" >$ rails c
>> User.create(name: "Michael Hartl", email: "mhartl@example.com",
?>              password: "foobar", password_confirmation: "foobar")
=> #<User id: 4, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-29 10:36:43", updated_at: "2019-07-29 10:36:43", password_digest: "$2a$10$OxReO8cIWEMHyFKqrSO3s.v0YEFSVJPlY3ceHjsUW./..."></pre>
データベースを確認
<a href="https://rakuda3desu.net/wp-content/uploads/2019/07/6.3.4-1-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/07/6.3.4-1-1.png" alt="" width="897" height="331" class="alignnone size-full wp-image-886" /></a>
あれなんか余計なデータがあるね🤔？
とりあえず今回のはid:4のヤツですね
追加したカラム、password_digestにちゃんとハッシュ化されたパスワードが入ってます
コンソールからも確認できる↓
<pre class="lang:default decode:true " title="ターミナル" >>> user = User.find_by(email: "mhartl@example.com")
>> user.password_digest
=> "$2a$10$OxReO8cIWEMHyFKqrSO3s.v0YEFSVJPlY3ceHjsUW./ORgyZVh0wW"</pre>
Userオブジェクトを作成した際のpassword"foobar"がハッシュ化されている
<blockquote class="quote-rort">
また6.3.1で説明したように、has_secure_passwordをUserモデルに追加したことで、そのオブジェクト内でauthenticateメソッドが使えるようになっています。このメソッドは、引数に渡された文字列 (パスワード) をハッシュ化した値と、データベース内にあるpassword_digestカラムの値を比較します。
</blockquote>
<pre class="lang:default decode:true " title="ターミナル" >#authenticateメソッドを呼んで間違ったパスワードを渡すとfalseが
#正しいパスワードを渡せばユーザーオブジェクトが返ってくる
>> user.authenticate("not_the_right_password")
=> false
>> user.authenticate("foobaz")
=> false
>> user.authenticate("foobar")
=> #<User id: 4, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-29 10:36:43", updated_at: "2019-07-29 10:36:43", password_digest: "$2a$10$OxReO8cIWEMHyFKqrSO3s.v0YEFSVJPlY3ceHjsUW./..."></pre>
authenticateメソッドが返す論理値を利用してログインシステムを作る（8章）
<pre class="lang:default decode:true " title="ターミナル" >
>> !!user.authenticate("foobar")
=> true
#正しいパスワードを渡して!!するとtrueが返ってくる</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
コンソールを一度再起動して (userオブジェクトを消去して)、このセクションで作ったuserオブジェクトを検索してみてください。
</li>
<li>
オブジェクトが検索できたら、名前を新しい文字列に置き換え、saveメソッドで更新してみてください。うまくいきませんね...、なぜうまくいかなかったのでしょうか?
</li>
<li>
今度は6.1.5で紹介したテクニックを使って、userの名前を更新してみてください。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >1.userを検索
>> user = User.find_by(email: "mhartl@example.com")
=> #<User id: 4, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-29 10:36:43", updated_at: "2019-07-29 10:36:43", password_digest: "$2a$10$OxReO8cIWEMHyFKqrSO3s.v0YEFSVJPlY3ceHjsUW./...">
#2.名前を置き換えてsave
>> user.name = "RakudaSan"
=> "RakudaSan"
>> user.save
#saveの実行にはname以外の入力（保存）も必要な為falseになる
=> false
#3.update_attributeメソッドを使って検証を回避（name属性のみを更新）
>> user.update_attribute(:name, "RakudaSan")
=> true
#更新を確認してみる
>> user = User.find_by(email: "mhartl@example.com")
=> #<User id: 4, name: "RakudaSan", email: "mhartl@example.com", created_at: "2019-07-29 10:36:43", updated_at: "2019-07-29 11:11:31", password_digest: "$2a$10$OxReO8cIWEMHyFKqrSO3s.v0YEFSVJPlY3ceHjsUW./..."></pre>
<h2>6.4 最後に</h2>
章のまとめ。
本文ママ
<h3>まとめとか感想</h3>
Userモデルを作ってname・email・passwordの各属性やバリデーションの追加。
それらのテスト。
パスワードをセキュアに認証できる機能の実装。

基本の基本になると思うのでしっかり覚えていきたい🐫
次はちょっと寄り道して、パスワードのバリデーションをアレンジしてみるの巻き。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

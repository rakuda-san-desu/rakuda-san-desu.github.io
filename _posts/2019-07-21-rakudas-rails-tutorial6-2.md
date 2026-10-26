---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜6.2"
date: "2019-07-21 17:05:38"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者", "参考サイト"]
published: true
wordpress_id: 852
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial6-2/"
---
<h2>6.2 ユーザーを検証する</h2>
Validation（検証）機能を実装する
<h3>6.2.1 有効性を検証する</h3>
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb
">require 'test_helper'

class UserTest < ActiveSupport::TestCase

  def setup    #各テストが実行される直前で実行される
    @user = User.new(name: "Example User", email: "user@example.com")
    #@userにUserクラスのインスタンス（上記のダミー入り）を代入
  end

  test "should be valid" do
    assert @user.valid?
    #有効であればtrue →　@userが有効か？
  end
end
</pre>
Userモデルにバリデーションを設定していないため
無効になる内容が無い→GREEN
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
コンソールから、新しく生成したuserオブジェクトが有効 (valid) であることを確認してみましょう。
</li>
<li>
6.1.3で生成したuserオブジェクトも有効であるかどうか、確認してみましょう。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#1.
>> user = User.new
=> #<User id: nil, name: nil, email: nil, created_at: nil, updated_at: nil>
>> user.valid?
=> true
#2.
>> user = User.new(name: "Michael Hartl", email: "mhartl@example.com")
=> #<User id: nil, name: "Michael Hartl", email: "mhartl@example.com", created_at: nil, updated_at: nil>
>> user.valid?
=> true
>> foo = User.create(name: "Foo", email: "foo@bar.com")
>> foo.valid?
=> true
</pre>
バリデーションが無いので略
<h3>6.2.2 存在性を検証する</h3>
<h4>Presenceとはなんぞ</h4>
存在を検証するメソッド
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb
">・
・
・
  test "should be valid" do
    assert @user.valid?
  end

  #user.nameが存在することを確認するテスト
  test "name should be present" do
    @user.name = "     "
    #user.nameに空白を代入
    assert_not @user.valid?
    #falseである　→@userが有効か
  end
  #空白の@user.nameが有効か→falseである　←この時成功するテスト
  #現状は空白でもtrueになってしまう為このtestは成功しない
end</pre>
user.nameに対するバリデーションを設定する
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb
">class User < ApplicationRecord
  <span class="point">validates :name, presence: true</span>
  #上記のコードに括弧を付けてみるとメソッドであることがわかりやすい
  #validates(:name, presence: true)　
  #validates(検証したい場所, 検証したい内容) ←メソッドと引数
  #もう少し詳しく→　validates(検証するカラム名, バリデーションヘルパー:  検証パラメータ)
end</pre>
<h4>バリデーションヘルパーとは何ぞ</h4>
バリデーション用のヘルパー（ざっくり）
いろんな種類が用意されている
<a href="https://railsguides.jp/active_record_validations.html#%E3%83%90%E3%83%AA%E3%83%87%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%83%98%E3%83%AB%E3%83%91%E3%83%BC" rel="noopener noreferrer" target="_blank">バリデーションヘルパー</a>←RailsGuideの該当箇所
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> user = User.new(name: "", email: "mhartl@example.com")
   #nameが空欄のUserインスタンスをuserに代入
>> user.valid?
   #検証
=> false
   #設定したバリデーションの結果（空白なのでfalse）
   #複数のバリデーションが設定されている時、1つでも検証に失敗するとFalseが返ってくる
   #errorsオブジェクトを使う事でどの検証が失敗したか確認できる
>> user.errors.full_messages
=> ["Name can't be blank"]
   #失敗したバリデーションに対応したerrorメッセージが表示される
>> user.save
=> false
#バリデーションに失敗しているので保存できなくなっている</pre>
user.nameが空白だとfalseが返るのバリデーションを設定したので
name should be presentのテストが成功する
<h4>errors.full_messagesとはなんぞ</h4>
起こっているエラーを用意されているメッセージで教えてくれる便利なチェーンメソッド。
多分詳しくは後から出てくると思うのです。

email属性の存在性を検証する
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb
">・
・
・
  test "name should be present" do
    @user.name = ""
    assert_not @user.valid?
  end

  #user.nameの存在性のテストに倣ってテストコードを書く
  test "email should be present" do
    @user.email = ""
    assert_not @user.valid?
  end
end</pre>
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  validates :name,  presence: true
  #バリデーションを設定する
  validates :email, presence: true
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
新しいユーザーuを作成し、作成した時点では有効ではない (invalid) ことを確認してください。なぜ有効ではないのでしょうか? エラーメッセージを確認してみましょう。
</li>
<li>
u.errors.messagesを実行すると、ハッシュ形式でエラーが取得できることを確認してください。emailに関するエラー情報だけを取得したい場合、どうやって取得すれば良いでしょうか?
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル" >#1.
>> u = User.new
=> #<User id: nil, name: nil, email: nil, created_at: nil, updated_at: nil>
>> u.valid?
=> false
>> u.errors.full_messages
   #エラーメッセージを確認する（それぞれ、空白ではダメというメッセージ）
=> ["Name can't be blank", "Email can't be blank"]
#2.
>> u.errors.messages
=> {:name=>["can't be blank"], :email=>["can't be blank"]}
   #キー（email)を指定してハッシュの値を取り出す
>> u.errors.messages[:email]
=> ["can't be blank"]</pre>
<h3>6.2.3 長さを検証する</h3>
名前には50文字まで
メールアドレスには255字までの上限を設ける

まずはテストから。
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb
">・
・
・
  test "name should not be too long" do
    @user.name = "a" * 51
    assert_not @user.valid?
  end
  #@user.nameが51文字の時@userは有効か　→　falseである　というテスト

  test "email should not be too long" do
    @user.email = "a" * 244 + "@example.com"
    assert_not @user.valid?
  end
  #@user.emailが256文字の時@userは有効か　→　falseである　というテスト
end
#現状ではバリデーションが設定されていない略でGREENにならない→RED</pre>
 
<pre class="lang:default mark:(2-3) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  validates :name,  presence: true, length: { maximum: 50 }
  validates :email, presence: true, length: { maximum: 255 }
end
#長さの上限をバリデーションに追加し略でGREEN</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>長すぎるnameとemail属性を持ったuserオブジェクトを生成し、有効でないことを確認してみましょう。</li>
<li>長さに関するバリデーションが失敗した時、どんなエラーメッセージが生成されるでしょうか? 確認してみてください。</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル" >#1.
#51文字の名前と256文字のメールアドレスを持つuserオブジェクトを生成
>> user = User.new(name: "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa", email: "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
a@example.com")
=> #<User id: nil, name: "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa...", email: "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa\na...", created_at: nil, updated_at: nil>
>> user.valid?
=> false    #有効性はfalse
#2.
>> user.errors.messages
=> {:name=>["is too long (maximum is 50 characters)"], :email=>["is too long (maximum is 255 characters)"]}
#それぞれ上限超えてるよのエラーメッセージ </pre>
<h3>6.2.4 フォーマットを検証する</h3>
有効なメールアドレスかどうかの判定
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> %w[foo bar baz]   #%w　→　空白で区切って配列を作ってくれる記法
=> ["foo", "bar", "baz"]
>> addresses = %w[USER@foo.COM THE_US-ER@foo.bar.org first.last@foo.jp]
#文字列を配列にしてaddressesに代入
=> ["USER@foo.COM", "THE_US-ER@foo.bar.org", "first.last@foo.jp"]
#eachメソッドで各要素を繰り返し取り出す
>> addresses.each do |address|
?>   puts address
>> end
USER@foo.COM
THE_US-ER@foo.bar.org
first.last@foo.jp</pre>
上記のテクニックを利用してメールアドレスのフォーマットの検証のテストを書く
→有効なメールアドレスと無効なメールアドレスをいくつか用意してエラーを検知していく
<pre class="lang:ruby mark:(12-12) decode:true " title="/sample_app/test/models/user_test.rb" >・
・
・
  #有効なアドレスの有効性のテスト
  test "email validation should accept valid addresses" do
    valid_addresses = %w[user@example.com USER@foo.COM A_US-ER@foo.bar.org
                         first.last@foo.jp alice+bob@baz.cn]
    valid_addresses.each do |valid_address|
      @user.email = valid_address
      #第二引数にエラーメッセージを追加して、どのアドレスでテストが成功しなかったかを特定できるようにしている
      ##{valid_address.inspect}　→　テストが成功しなかったアドレスが変数展開される
      assert @user.valid?, "#{valid_address.inspect} should be valid"
    end
  end
  #バリデーションが設定されていないのでエラーが出ない→有効のテストは成功する
  
  #無効なアドレスの無効性のテスト
  test "email validation should reject invalid addresses" do
    invalid_addresses = %w[user@example,com user_at_foo.org user.name@example.
                           foo@bar_baz.com foo@bar+baz.com]
    invalid_addresses.each do |invalid_address|
      @user.email = invalid_address
      assert_not @user.valid?, "#{invalid_address.inspect} should be invalid"
    end
  end
  #バリデーションが設定されていないのでエラーが出ない→無効のテストは成功しないのでRED
end</pre> 
<h4>formatヘルパー</h4>
validates :email, format: { with: /<span class="point">正規表現</span>/ }
withオプションで与えられた正規表現と属性の値がマッチするか検証するためのヘルパー
<h4>正規表現とは</h4>
Regular Expression、regexとも表記される
[blogcard url="https://wa3.i-3-i.info/word11309.html"]
そういう記法があると言う事（ざっくり)
<blockquote class="quote-rort">有効なメールアドレスだけにマッチして、無効なメールアドレスにはマッチしない正規表現を組み立てる必要があります。</blockquote>
上記のような正規表現を組み立ててemailの値をマッチするかを検証する
→VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-.]+\.[a-z]+\z/i
<a href="https://railstutorial.jp/chapters/modeling_users?version=5.1#table-valid_email_regex" rel="noopener noreferrer" target="_blank">この正規表現に関する本文の解説を見る</a>
<a href="https://rubular.com/" rel="noopener noreferrer" target="_blank">Rubular</a>(正規表現を試せるWebサイト)
<pre class="lang:ruby mark:(4-4,6-6) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  validates :name,  presence: true, length: { maximum: 50 }
  #定数VALID_EMAIL_REGEXにメールアドレスのフォーマットを検証するための正規表現を代入
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-.]+\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX }
end
#メールアドレスのフォーマットに関するバリデーションを設定したことにより
#メールアドレスの無効性のテストも成功する
</pre> 
現状の正規表現には問題が残っている→演習で修正
<h4>定数ってなんだっけ</h4>
変数→代入した値を後から変更することが出来る
<del datetime="2019-07-26T09:03:59+00:00">定数→代入した値を<strong>後から変更することが出来ない</strong></del>
<del datetime="2019-07-26T09:03:59+00:00">文字通りですね</del>
定数→<strong>Rubyでは再代入しようとすると注意文が出るが代入できてしまう</strong>
でもしないでね!分かってるよね！！と言う事らしいっぽい
Rubyでは定数は大文字で始める　→　暗黙のルール的な物で全部大文字が良い
→メールアドレスのフォーマットの正規表現は変更させたくないので定数に代入する
<blockquote class="quote-rort">
<ol>
<li>
リスト 6.18にある有効なメールアドレスのリストと、リスト 6.19にある無効なメールアドレスのリストをRubularのYour test string:に転記してみてください。その後、リスト 6.21の正規表現をYour regular expression:に転記して、有効なメールアドレスのみがすべてマッチし、無効なメールアドレスはすべてマッチしないことを確認してみましょう。
</li>
<li>
先ほど触れたように、リスト 6.21のメールアドレスチェックする正規表現は、foo@bar..comのようにドットが連続した無効なメールアドレスを許容してしまいます。まずは、このメールアドレスをリスト 6.19の無効なメールアドレスリストに追加し、これによってテストが失敗することを確認してください。次に、リスト 6.23で示した、少し複雑な正規表現を使ってこのテストがパスすることを確認してください。
</li>
<li>
foo@bar..comをRubularのメールアドレスのリストに追加し、リスト 6.23の正規表現をRubularで使ってみてください。有効なメールアドレスのみがすべてマッチし、無効なメールアドレスはすべてマッチしないことを確認してみましょう。
</li>
</ol>
</blockquote>
1.eachメソッドでそれぞれのアドレスを取り出してから張り付けると○
<a href="https://rakuda3desu.net/wp-content/uploads/2019/07/6.2.4-1png.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/07/6.2.4-1png-1024x749.png" alt="" width="1024" height="749" class="alignnone size-large wp-image-865" /></a>
2.3.動作確認のみにて省略
<h4>使われている正規表現の内容</h4>
/\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
/　正規表現の開始
\A　文字列の先頭
[\w+\-.]+　英数字、_、+、-、.のいずれかを少なくとも1文字以上繰り返す
@　アットマーク
[a-z\d\-]+　英小文字、数字、-のいずれかを少なくとも1文字以上繰り返す
(\.　[a-z\d\-]+)*　（ドット　英小文字、数字、-のいずれかを少なくとも1文字以上繰り返す）←これを0回以上繰り返す
\.　ドット
[a-z]+　英小文字を少なくとも1文字以上繰り返す
\z　文字列の末尾
/　正規表現の終了
i　大文字小文字を無視
<h3>6.2.5 一意性を検証する</h3>
<h4>一意性とは何ぞ</h4>
他に同じデータはない、と言うこと（ざっくり
→沢山ある値の中から1つの情報を特定できる
バリデーションには:uniqueを使って設定する
<h4>一意性のテスト</h4>
一意性のテストのテストの為には実際にデータベースに情報を登録する必要がある
→テスト専用のデータベースdb/test.sqlite3がある

まずはテスト
<pre class="lang:ruby mark:(12-12) decode:true " title="/sample_app/test/models/user_test.rb" >require 'test_helper'

class UserTest < ActiveSupport::TestCase

  def setup
    @user = User.new(name: "Example User", email: "user@example.com")
  end
・
・
・  
  test "email addresses should be unique" do
    duplicate_user = @user.dup
　　#duplicate_user に @user.dup（@userの複製）を代入
    @user.save
    assert_not duplicate_user.valid?
    #falseである　→duplicate_user（@userを複製したデータ）は有効か？
  end
end</pre> 
<h4>@user.dupとは</h4>
dupは同じ属性を持つデータを複製するメソッド
上記の場合@userを保存済なので、
複製されたユーザーのメールアドレスは既に存在しており無効になる
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/app/models/user.rb
" >class User < ApplicationRecord
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX }<span class="point">,</span>
                    <span class="point">uniqueness: true</span>   #値が一意であるかの検証
end</pre>
<h4>メールアドレスでは大文字小文字が区別されない</h4>
現在のテストではfoo@bar.comとFOO@BAR.COMは別の物に判断されるが
実際は大文字と小文字で区別はされないため
検証でもメールアドレスの大文字と小文字が区別されないように書く必要がある
<pre class="lang:ruby mark:(7-7) decode:true " title="/sample_app/test/models/user_test.rb" >・
・
・
  test "email addresses should be unique" do
    duplicate_user = @user.dup
    #@user.dupのemailに　@user.emailを大文字にして代入
    duplicate_user.email = @user.email.upcase
    #@user.emailは大文字の状態で保存される
    @user.save
    assert_not duplicate_user.valid?
  end
end</pre> 
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
end</pre> 
<h4>case_sensitive</h4>
uniqueness:ヘルパーのオプション
一意性制約で大文字小文字を区別するかどうかを指定するもの
uniqueness:値が一意であるかの検証にcase_sensitive: false（大文字と小文字を区別しない）というオプションを追加している
<h4>インデックスを追加する</h4>
<blockquote class="quote-rort">
それはActive Recordはデータベースのレベルでは一意性を保証していないという問題です。具体的なシナリオを使ってその問題を説明します。
（中略）
上のシナリオが信じがたいもののように思えるかもしれませんが、どうか信じてください。RailsのWebサイトでは、トラフィックが多いときにこのような問題が発生する可能性があるのです
</blockquote>
→解決するためにemailのカラムにindexを追加する
<a href="https://railstutorial.jp/chapters/modeling_users?version=5.1#aside-database_indices" rel="noopener noreferrer" target="_blank">データベースのインデックスに関するコラム</a>
<pre class="lang:default decode:true " title="ターミナル" >$ rails generate migration add_index_to_users_email
#add_index_to_users_emailと言う名前のマイグレーションファイルを生成（中身は未定義）</pre>
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/db/migrate/[timestamp]_add_index_to_users_email.rb" >class AddIndexToUsersEmail < ActiveRecord::Migration[5.0]
  def change
　　#add_index（indexを追加するためのヘルパー）　テーブル名　カラム名　オプション
    add_index :users, :email, unique: true
  end
end</pre> 
<pre class="lang:default decode:true " title="ターミナル" >$ rails db:migrate</pre>
<blockquote class="quote-rort">テストDB用のサンプルデータが含まれているfixtures内で一意性の制限が保たれていないため、テストは red になります。</blockquote>
<h4>fixtureとはなんぞ</h4>
→テストDB用のサンプルデータが含まれるファイル

6.1でUserモデルを作成した際、
自動で生成されていたUserのデフォルトfixture内のデータで一意性の制限が保たれていない
→今はまだ使わないのでひとまずデータを削除しておく
<h4>データベースに保存される直前にすべての文字列を小文字に変換する</h4>
データベースによっては大文字と小文字を区別するものがあるため
Railsチュートリアルでは「データベースに保存される直前にすべての文字列を小文字に変換する」
（多分色々な方法があるんだと思う）
コールバックメソッドのbefore_saveを使って、オブジェクトが保存される時点で処理を実行する
<h4>コールバック (callback) メソッドとはなんぞ</h4>
オブジェクトの状態が切り替わる際の特定の瞬間に呼び出されるメソッド
→before_save　saveの直前
<a href="https://railsguides.jp/active_record_callbacks.html" rel="noopener noreferrer" target="_blank">railsガイドのコールバックのページ</a>
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  #saveの直前に　現在のユーザーのemailに　emailを小文字にしたものを代入
  #Userモデルの中ではself.email = self.email.downcaseの右側のselfは省略できる
  before_save { self.email = email.downcase }
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
end</pre> 
<blockquote class="quote-rort">これで、先に述べたアリスのシナリオはうまくいくようになります</blockquote>
良くわからないんですが、正気を失わずに済むと言う事です？
→ちょっと上のせっかちなアリスちゃんがSubmitを2回押しちゃったっていう話でCoCとは関係なかったっぽい
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
リスト 6.33を参考に、メールアドレスを小文字にするテストをリスト 6.32に追加してみましょう。ちなみに追加するテストコードでは、データベースの値に合わせて更新するreloadメソッドと、値が一致しているかどうか確認するassert_equalメソッドを使っています。リスト 6.33のテストがうまく動いているか確認するためにも、before_saveの行をコメントアウトして redになることを、また、コメントアウトを解除すると greenになることを確認してみましょう。</li>
<li>
テストスイートの実行結果を確認しながら、before_saveコールバックをemail.downcase!に書き換えてみましょう。<span class="point">ヒント: メソッドの末尾に!を付け足すことにより、email属性を直接変更できるようになります</span> (リスト 6.34)。
</li>
</ol>
</blockquote>
1.本文通り
2.
before_save { self.email = email.downcase }　→　<span class="point">before_save { email.downcase! }</span>
emailを直接変更しているので代入する必要が無い（コードが短く済む！）
<h3>まとめとか感想</h3>
良くわからない部分が多くて
時間も長かったし量も長かった！
けど、しっかり身になったのではないでしょうか？！
身に？実に？
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

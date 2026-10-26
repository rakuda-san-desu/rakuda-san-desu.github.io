---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜13.1"
date: "2020-05-04 13:27:51"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1464
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial13-1/"
---
ユーザーが短いメッセージを投稿できる機能を作っていく
→「マイクロポストリソース」を追加していく
<blockquote class="quote-rort">Micropostデータモデルを作成し、Userモデルとhas_manyおよびbelongs_toメソッドを使って関連付けを行い、さらに、結果を処理し表示するために必要なフォームとその部品を作成します (13.4で画像のアップロードも実装します)。</blockquote>
因みに（？）14章ではユーザーをフォローするという概念を導入してマイクロポストのフィードを受け取れるようにする（Twitterのミニクローンの完成！）
<h2>13.1 Micropostモデル</h2>
Micropostモデルを作成する
<blockquote class="quote-rort">今回のマイクロポストモデルは完全にテストされ、デフォルトの順序を持ち、また親であるユーザーが破棄された場合には自動的に破棄されるようにします。</blockquote>
（2章のtoy-appと違って～的なお話)
ではブランチを作成して作業開始！ 
<pre class="toolbar:2 lang:default decode:true " >$ git checkout -b user-microposts</pre> 
<h3>13.1.1 基本的なモデル</h3>
Micropostモデル　→　内容を保存するcontent、特定のユーザーとマイクロポストを関連付けるuser_idを持っている
（ほかに、自動で付加されるidとcreated_atとupdated_atがある）
構造図は<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#fig-micropost_model" title="Micropostデータモデル" rel="noopener noreferrer" target="_blank">RoRT本文参照</a><a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#fig-micropost_model" title="Micropostデータモデル" rel="noopener noreferrer" target="_blank">RoRT本文参照</a>
上記の図で、マイクロポストの投稿内容にString型ではなくText型が使われている
<h4>Text型とはなんぞ</h4>
255文字までの上限があるString型と違い、Text型はある程度の量のテキストを格納する時に使われる
今回実装するマイクロポストは140文字の文字制限をつける(予定)のでString型でもいいんだけど
Text型ならではの利点があるので、あえてText型を指定している
<blockquote class="quote-rort">投稿フォームにString用のテキストフィールドではなくてText用のテキストエリアを使うため、より自然な投稿フォームが実現できます。また、Text型の方が将来における柔軟性に富んでいて、例えばいつか国際化をするときに、言語に応じて投稿の長さを調節することもできます。</blockquote>
<h4>Micropostモデルを生成する</h4> 
<pre class="lang:default decode:true " title="ターミナル" >$ rails generate model Micropost content:text user:references
Running via Spring preloader in process 15033
      invoke  active_record
      create    db/migrate/20200430075846_create_microposts.rb
      create    app/models/micropost.rb
      invoke    test_unit
      create      test/models/micropost_test.rb
      create      test/fixtures/microposts.yml</pre> 
自動生成されたMicropostsモデル↓ 
<pre class="lang:ruby decode:true " title="/sample_app/app/models/micropost.rb" ># ApplicationRecordを継承している
class Micropost < ApplicationRecord
  # ユーザーと１対１の関係であることを表している
  # モデルをジェネレートする際に引数user:referencesを含めた事によって追加されている
  #詳しくは13.1.3でやるよ！
  belongs_to :user
end</pre> 
<h4>Userモデルとの違い</h4>
Userモデルとの最大の違いはreferences型を利用している点
<blockquote class="quote-rort">これを利用すると、自動的にインデックスと外部キー参照付きのuser_idカラムが追加され、UserとMicropostを関連付けする下準備をしてくれます</blockquote>
また、Userモデルの時と同じくt.timestampsという行 (マジックカラム) が自動的に生成されていることにより
created_atとupdated_atというカラムが追加される（created_atは13.1.4の実装を進めるうえで必要！） 

自動生成されたマイグレーションファイル↓
<pre class="lang:ruby mark:(9-10) decode:true " title="/sample_app/db/migrate/タイムスタンプ_create_microposts.rb" >class CreateMicroposts < ActiveRecord::Migration[5.1]
  def change
    create_table :microposts do |t|
      t.text :content
      t.references :user, foreign_key: true

      t.timestamps
    end
    # user_idとcreated_atカラムにインデックスを付与（この↓一行追加)
    add_index :microposts, [:user_id, :created_at]
  end
end</pre> 
インデックスを付与することでuser_idに関連付けられたすべてのマイクロポストを作成時刻の逆順で取り出しやすくしている
<blockquote class="quote-rort">また、user_idとcreated_atの両方を１つの配列に含めている点にも注目です。こうすることでActive Recordは、両方のキーを同時に扱う複合キーインデックス (Multiple Key Index) を作成します。(訳注: 複合キーインデックスは、スタックオーバーフローのWhat is a multiple key index?で詳しく解説されています。)</blockquote>
データベースを更新
<pre class="lang:default decode:true " title="ターミナル" >$ rails db:migrate</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>RailsコンソールでMicropost.newを実行し、インスタンスを変数micropostに代入してください。その後、user_idに最初のユーザーのidを、contentに "Lorem ipsum" をそれぞれ代入してみてください。この時点では、 micropostオブジェクトのマジックカラム (created_atとupdated_at) には何が入っているでしょうか?</li>
	<li>先ほど作ったオブジェクトを使って、micropost.userを実行してみましょう。どのような結果が返ってくるでしょうか? また、micropost.user.nameを実行した場合の結果はどうなるでしょうか?</li>
	<li>先ほど作ったmicropostオブジェクトをデータベースに保存してみましょう。この時点でもう一度マジックカラムの内容を調べてみましょう。今度はどのような値が入っているでしょうか?</li>
</ol>
</blockquote>
1.
created_atとupdated_atはnil
<pre class="toolbar:2 lang:default decode:true " title="ターミナル" >>> micropost = Micropost.new
=> #<Micropost id: nil, content: nil, user_id: nil, created_at: nil, updated_at: nil>
>> User.first
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-04-09 04:57:00", updated_at: "2020-04-29 07:46:39", password_digest: "$2a$10$4L1EmqW3mOD46Zkp/AH4qes4ldTJ7o3iWGqdek5frNI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$y3BA/9QXPjjE51k3UFPRoeLq5pE3uhVhXXaaWOXuTWb...", activated: true, activated_at: "2020-04-09 04:57:00", reset_digest: "$2a$10$L5Ve9H4KbFblp3nLdBwe/OOmHRL6.W8Y7c9Xncmx.vs...", reset_sent_at: "2020-05-02 05:49:25">
>> micropost.user_id = 1
=> 1
>> content = "Lorem ipsum" 
=> "Lorem ipsum"
>> micropost
=> #<Micropost id: nil, content: "Lorem ipsum", user_id: 1, created_at: nil, updated_at: nil></pre> 
2. 
<pre class="lang:default decode:true " title="ターミナル" >>> micropost.user
# micropostに代入されているuser_idのuserが呼ばれる
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-04-09 04:57:00", updated_at: "2020-04-29 07:46:39", password_digest: "$2a$10$4L1EmqW3mOD46Zkp/AH4qes4ldTJ7o3iWGqdek5frNI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$y3BA/9QXPjjE51k3UFPRoeLq5pE3uhVhXXaaWOXuTWb...", activated: true, activated_at: "2020-04-09 04:57:00", reset_digest: "$2a$10$L5Ve9H4KbFblp3nLdBwe/OOmHRL6.W8Y7c9Xncmx.vs...", reset_sent_at: "2020-05-02 05:49:25">
>> micropost.user.name
# 前略のuserの名前が返ってくる
=> "Example User"</pre> 
3. 
<pre class="lang:default decode:true " title="ターミナル" >>> micropost.save
   (0.1ms)  begin transaction
  SQL (2.1ms)  INSERT INTO "microposts" ("content", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["content", "Lorem ipsum"], ["user_id", 1], ["created_at", "2020-05-02 16:03:43.445877"], ["updated_at", "2020-05-02 16:03:43.445877"]]
   (6.8ms)  commit transaction
=> true
>> micropost
# マジックカラムにはどちらも保存日時の値が入っている
=> #<Micropost id: 1, content: "Lorem ipsum", user_id: 1, created_at: "2020-05-02 07:03:43", updated_at: "2020-05-02 07:03:43"></pre> 
<h3>13.1.2 Micropostのバリデーション</h3>
基本的なモデルが作成できたのでバリデーションを追加していく

マイクロポストは投稿したユーザーのid（user_id）を持たせるようにしたので
これを使って<strong>慣習的に正しくActive Recordの関連付け</strong>を実装する

まずはMicropostモデル単体を動くようにしていく→テスト駆動開発で！
<h4>Micropostの初期テスト</h4>
Micropostの初期テストはUserモデルの初期テストと似ている
<blockquote class="quote-rort">まずはsetupのステップで、fixtureのサンプルユーザーと紐付けた新しいマイクロポストを作成しています。次に、作成したマイクロポストが有効かどうかをチェックしてます。最後に、あらゆるマイクロポストはユーザーのidを持っているべきなので、user_idの存在性のバリデーションに対するテストも追加します。</blockquote>
ひとまずこれをまとめたコードがこちら 
<pre class="lang:ruby mark:(7-8) decode:true " title="/sample_app/test/models/micropost_test.rb" >require 'test_helper'

class MicropostTest < ActiveSupport::TestCase

  def setup
    @user = users(:michael)
    # このコードは慣習的に正しくない(13.1.3でなおす）
    @micropost = Micropost.new(content: "Lorem ipsum", user_id: @user.id)
  end

  test "should be valid" do
    # trueである→　@micropostは有効か
    assert @micropost.valid?
  end

  test "user id should be present" do
    # @micropostのuser_idにnilを代入
    @micropost.user_id = nil
    # falseである→　@micropostは有効か
    assert_not @micropost.valid?
  end
end</pre> 
上記のテストをパスさせるために存在性のバリデーションを追加する 
<pre class="lang:ruby mark:(3-4) decode:true " title="/sample_app/app/models/micropost.rb" >class Micropost < ApplicationRecord
  belongs_to :user
  # user_idが存在する
  validates :user_id, presence: true
end
</pre> 
GREEN！
<blockquote class="quote-rort">ちなみにRails 5では、リスト 13.5のバリデーションを追加しなくてもリスト 13.4のテストが成功してしまいます。しかしこれは、リスト 13.4でハイライトした「慣習的な意味で正しくない」というコードを書いた場合でのみ発生します。この部分を「慣習的に正しい」コードで実装すると、user_idに対する存在性のバリデーションが期待通りに動きます (リスト 13.12)。この部分を説明しておきたかったので、先ほどのコメントでハイライトしておきました。</blockquote>
次にcontent属性に対するバリデーションを追加
テスト開発駆動！（テスト→機能の実装）
※141文字（大きくて無効な値）だけじゃなくて139文字（小さくて有効な値）、140文字（設定値と同じな有効な値）もテストするべきって指摘をいただきまして
　なるほどちゃんと動くテストねってなったので追加してみました。
<pre class="lang:ruby decode:true " title="/sample_app/test/models/micropost_test.rb" >・
・
・
  test "content should be present" do
    # @micropost.contentに"   "を追加
    @micropost.content = "   "
    # falseである→　@micropostは有効か
    assert_not @micropost.valid?
  end

  test "content should be at most 140 characters" do
    # @micropost.contentにa×141（141文字）を追加
    @micropost.content = "a" * 141
    # falseである→　@micropostは有効か
    assert_not @micropost.valid?
    # 140文字の時有効
    @micropost.content = "a" * 140
    assert @micropost.valid?
    # 139文字の時有効
    @micropost.content = "a" * 139
    assert @micropost.valid?
  end
end</pre> 
バリデーションを追加 
<pre class="lang:ruby mark:(5-6) decode:true " title="/sample_app/app/models/micropost.rb" >class Micropost < ApplicationRecord
  belongs_to :user
  # user_idが存在する
  validates :user_id, presence: true
  # contentが存在する　長さは最大140文字
  validates :content, presence: true, length: { maximum: 140 }
end</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>Railsコンソールを開き、user_idとcontentが空になっているmicropostオブジェクトを作ってみてください。このオブジェクトに対してvalid?を実行すると、失敗することを確認してみましょう。また、生成されたエラーメッセージにはどんな内容が書かれているでしょうか?</li>
	<li>コンソールを開き、今度はuser_idが空でcontentが141文字以上のmicropostオブジェクトを作ってみてください。このオブジェクトに対してvalid?を実行すると、失敗することを確認してみましょう。また、生成されたエラーメッセージにはどんな内容が書かれているでしょうか?</li>
</ol>
</blockquote>
1. 
<pre class="lang:default decode:true " title="ターミナル" >>> m = Micropost.new

=> #<Micropost id: nil, content: nil, user_id: nil, created_at: nil, updated_at: nil>
>> 
>> m.valid?
=> false
>> m.errors.full_messages
# ユーザーが存在する必要がある
# ユーザーは空じゃダメです
# コンテンツは空じゃダメです
=> ["User must exist", "User can't be blank", "Content can't be blank"]</pre> 
2. 
<pre class="lang:default decode:true " title="ターミナル" ># 141文字の文字列
>> "a"*141
=> "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"
>> m = Micropost.new(content: "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa")
=> #<Micropost id: nil, content: "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa...", user_id: nil, created_at: nil, updated_at: nil>
>> m.valid?
=> false
>> m.errors.full_messages
# ユーザーが存在する必要がある
# ユーザーは空じゃダメです
# コンテンツが長すぎです（最大140文字）
=> ["User must exist", "User can't be blank", "Content is too long (maximum is 140 characters)"]</pre> 
m = Micropost.new(content: "a" * 141)でよくね？って言われたけどホンソレですね！
<h3>13.1.3 User/Micropostの関連付け</h3>
モデル間の関連付けを十分に考えておくことはとても重要
<blockquote class="quote-rort">今回の場合は、 2.3.3でも示したように、それぞれのマイクロポストは１人のユーザーと関連付けられ、それぞれのユーザーは (潜在的に) 複数のマイクロポストと関連付けられます。</blockquote>
<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#fig-micropost_belongs_to_user" rel="noopener noreferrer" target="_blank">RoRT本文</a>に図解があるよ
これらの関連付けを実装するための一環として
Micropostモデルに関するテストを作成＆Userモデルのテストに追加をしていく
<h4>belongs_to/has_many</h4>
belongs_to/has_manyの関連付けを定義することによって
紐付いたユーザーを通してマイクロポストを作成することが出来るようになる
Micropostモデル単体ではこうだったメソッドが
<pre class="toolbar:2 lang:default decode:true " >Micropost.create
Micropost.create!
Micropost.new</pre> 
こうなる 
<pre class="toolbar:2 lang:default decode:true " >user.microposts.create
user.microposts.create!
user.microposts.build</pre> 
新規のマイクロポストが上記のメソッドで作成されると
user_idに　自動的　に　正しい値　が設定される
<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#table-association_methods" rel="noopener noreferrer" target="_blank">user/micropost関連メソッドのまとめ</a>
<blockquote class="quote-rort">(newメソッドと同様に、buildメソッドはオブジェクトを返しますがデータベースには反映されません。) 一度正しい関連付けを定義してしまえば、@micropost変数のuser_idには、関連するユーザーのidが自動的に設定されます。</blockquote>
Micropostモデルに必要なbelongs_to :userというコードは自動的に生成されている
Userモデルに必要なhas_many :micropostsは手動で追加する 
<pre class="lang:ruby mark:(2-3) decode:true " title="/sample_app/app/models/micropost.rb" >class Micropost < ApplicationRecord
  # MicropostとそのUserは belongs_to (1対1) の関係性がある
  belongs_to :user
  # user_idが存在する
  validates :user_id, presence: true
  # contentが存在する　長さは最大140文字
  validates :content, presence: true, length: { maximum: 140 }
end</pre>  
<pre class="lang:ruby mark:(2-3) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  # UserとそのMicropostは has_many (1対多) の関係性がある
  has_many :microposts
・
・
・</pre> 
Micropostモデルのテストを修正 
<pre class="lang:ruby mark:(9-12) decode:true " title="/sample_app/test/models/micropost_test.rb" >require 'test_helper'

class MicropostTest < ActiveSupport::TestCase

  def setup
    @user = users(:michael)
    # このコードは慣習的に正しくない
    # @micropost = Micropost.new(content: "Lorem ipsum", user_id: @user.id)
    # なので正しいコードに修正↓
    # @micropostに以下を代入
    # @userに紐づいたMicropostオブジェクトを返す（content属性に"Lorem ipsum"の値を持つ）
    @micropost = @user.microposts.build(content: "Lorem ipsum")
  end
・
・
・</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>データベースにいる最初のユーザーを変数userに代入してください。そのuserオブジェクトを使ってmicropost = user.microposts.create(content: "Lorem ipsum")を実行すると、どのような結果が得られるでしょうか?</li>
	<li>先ほどの演習課題で、データベース上に新しいマイクロポストが追加されたはずです。user.microposts.find(micropost.id)を実行して、本当に追加されたのかを確かめてみましょう。また、先ほど実行したmicropost.idの部分をmicropostに変更すると、結果はどうなるでしょうか?</li>
	<li>user == micropost.userを実行した結果はどうなるでしょうか? また、user.microposts.first == micropost を実行した結果はどうなるでしょうか? それぞれ確認してみてください。</li>
</ol>
</blockquote>
1. 
<pre class="lang:default decode:true " title="ターミナル" >>> user = User.first
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-04-09 04:57:00", updated_at: "2020-04-29 07:46:39", password_digest: "$2a$10$4L1EmqW3mOD46Zkp/AH4qes4ldTJ7o3iWGqdek5frNI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$y3BA/9QXPjjE51k3UFPRoeLq5pE3uhVhXXaaWOXuTWb...", activated: true, activated_at: "2020-04-09 04:57:00", reset_digest: "$2a$10$L5Ve9H4KbFblp3nLdBwe/OOmHRL6.W8Y7c9Xncmx.vs...", reset_sent_at: "2020-05-02 05:49:25">
>> micropost = user.microposts.create(content: "Lorem ipsum")
   (0.1ms)  begin transaction
  SQL (4.6ms)  INSERT INTO "microposts" ("content", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["content", "Lorem ipsum"], ["user_id", 1], ["created_at", "2020-05-02 17:51:22.031502"], ["updated_at", "2020-05-02 17:51:22.031502"]]
   (9.8ms)  commit transaction
# userに紐づいたマイクロポストが作成される（user_idの値がuserの持つidの値と一致している）
=> #<Micropost id: 2, content: "Lorem ipsum", user_id: 1, created_at: "2020-05-02 08:51:22", updated_at: "2020-05-02 08:51:22"></pre> 
2. 
<pre class="lang:default decode:true " title="ターミナル" ># userのマイクロポストを探す(micropostのidを持つ)
>> user.microposts.find(micropost.id)
  Micropost Load (0.2ms)  SELECT  "microposts".* FROM "microposts" WHERE "microposts"."user_id" = ? AND "microposts"."id" = ? LIMIT ?  [["user_id", 1], ["id", 2], ["LIMIT", 1]]
=> #<Micropost id: 2, content: "Lorem ipsum", user_id: 1, created_at: "2020-05-02 08:51:22", updated_at: "2020-05-02 08:51:22">
>> user.microposts.find(micropost)
# 引数エラー（オブジェクトのIDを渡してね）
Traceback (most recent call last):
        1: from (irb):4
ArgumentError (You are passing an instance of ActiveRecord::Base to `find`. Please pass the id of the object by calling `.id`.)</pre> 
3. 
<pre class="lang:default decode:true " title="コンソール" ># user（User.firstが代入されている) == micropost.user（micropostに紐づいたユーザー→user）
>> user == micropost.user
# よってtrue
=> true
#userの一つ目のマイクロポスト（13.1.1の演習で作ったid: 1のマイクロポスト） == micropost(この演習で作ったid: 2のマイクロポスト）
>> user.microposts.first == micropost
  Micropost Load (0.2ms)  SELECT  "microposts".* FROM "microposts" WHERE "microposts"."user_id" = ? ORDER BY "microposts"."id" ASC LIMIT ?  [["user_id", 1], ["LIMIT", 1]]
# よってfalse
=> false
#　参考↓
>> user.microposts.first
  Micropost Load (0.1ms)  SELECT  "microposts".* FROM "microposts" WHERE "microposts"."user_id" = ? ORDER BY "microposts"."id" ASC LIMIT ?  [["user_id", 1], ["LIMIT", 1]]
=> #<Micropost id: 1, content: "Lorem ipsum", user_id: 1, created_at: "2020-05-02 07:03:43", updated_at: "2020-05-02 07:03:43">
>> micropost
=> #<Micropost id: 2, content: "Lorem ipsum", user_id: 1, created_at: "2020-05-02 08:51:22", updated_at: "2020-05-02 08:51:22"></pre> 
<h3>13.1.4 マイクロポストを改良する</h3>
UserとMicropostの関連付けを改良していく
→ユーザーのマイクロポストを特定の順序で取得できるようにする
→マイクロポストをユーザーに依存させて、ユーザーが削除されたらマイクロポストも削除されるようにする
<h4>デフォルトのスコープ</h4>
user.micropostsメソッドはデフォルトでは呼び出しの順序に何の保証もない（決まっていない）
→一般的な表示順として新しい順に表示されるようにする
これを実装するためにdefault scopeというテクニックを使う！
<h4>マイクロポストの順序のテスト</h4>
<blockquote class="quote-rort">この機能のテストは、見せかけの成功に陥りやすい部分で、「アプリケーション側の実装が本当は間違っているのにテストが成功してしまう」という罠があります。正しいテストを書くために、ここではテスト駆動開発で進めていきます。</blockquote>
まず、DB上の最初のマイクロポストがfixture内のマイクロポスト (most_recent) と同じであるか検証する
順序のテスト 
<pre class="lang:ruby decode:true " title="/sample_app/test/models/micropost_test.rb" >・
・
・
  test "order should be most recent first" do
    # 第一引数と第二引数が等しい　microposts（fixture）の:most_recent　と　Micropostオブジェクトの1つ目
    assert_equal microposts(:most_recent), Micropost.first
  end
end</pre> 
→マイクロポスト用のfixtureファイルからサンプルデータを読み出すのでデータを用意
<pre class="lang:default mark:(3-3,7-7,11-11,15-15) decode:true " title="/sample_app/test/fixtures/microposts.yml" >orange:
  content: "I just ate an orange!"
  created_at: <%= 10.minutes.ago %>

tau_manifesto:
  content: "Check out the @tauday site by @mhartl: http://tauday.com"
  created_at: <%= 3.years.ago %>

cat_video:
  content: "Sad cats are sad: http://youtu.be/PKffm2uI4dk"
  created_at: <%= 2.hours.ago %>

most_recent:
  content: "Writing a short test"</pre> 
→埋め込みRubyを使ってcreated_atカラムに値をセットしている
本来はRailsによって自動的に更新される値の為手動更新はできないが
fixtureファイルの中では更新可能になっているので、意図的に生成時間の順序を変更している
→ 現状では第一引数と第二引数は等しくない（4番目と1番目）なのでfalseでRED
<h4>テストを成功させるコード</h4>
Railsのdefault_scopeメソッドを使ってこのテストを成功させる
<blockquote class="quote-rort">このメソッドは、データベースから要素を取得したときの、デフォルトの順序を指定するメソッドです。特定の順序にしたい場合は、default_scopeの引数にorderを与えます。</blockquote>
[blogcard url="https://railsguides.jp/active_record_querying.html#%E3%83%87%E3%83%95%E3%82%A9%E3%83%AB%E3%83%88%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%97%E3%82%92%E9%81%A9%E7%94%A8%E3%81%99%E3%82%8B"]
created_atカラムの順にしたい場合の引数はこう↓ 
<pre class="toolbar:2 lang:default decode:true " >order(:created_at)</pre> 
ただしこれだと昇順なのでcreated_atの値が最も古いものから順にソートされてしまう
→さらに降順を指定する場合の引数はこう
<pre class="toolbar:2 lang:default decode:true " >order(created_at: :desc)</pre> 
このコードを使ってMicropostモデルを更新する 
<pre class="lang:ruby mark:(4-5) decode:true " title="/sample_app/app/models/micropost.rb" >class Micropost < ApplicationRecord
  # MicropostとそのUserは belongs_to (1対1) の関係性がある
  belongs_to :user
  # default_scope（順序を指定するメソッド） created_at:を降順にする
  default_scope -> { order(created_at: :desc) }
  # user_idが存在する
  validates :user_id, presence: true
  # contentが存在する　長さは最大140文字
  validates :content, presence: true, length: { maximum: 140 }
end</pre> 

<h4>ラムダ式 (Stabby lambda) とは何ぞ</h4>
Procやlambda (もしくは無名関数)と呼ばれるオブジェクトを作成する文法の事。
<blockquote class="quote-rort">->というラムダ式は、ブロック (4.3.2) を引数に取り、Procオブジェクトを返します。このオブジェクトは、callメソッドが呼ばれたとき、ブロック内の処理を評価します。</blockquote>
<pre class="toolbar:2 lang:default decode:true " ># 実際は default_scope -> { hogehoge } な形
>> -> { puts "foo" }
=> #<Proc:0x007fab938d0108@(irb):1 (lambda)>
>> -> { puts "foo" }.call
foo
=> nil</pre> 
ちょっと何言ってるかわからないですね🤔

[blogcard url="https://qiita.com/nagata03/items/919b0457409cc755ff0f"]
うーん
<blockquote class="quote-rort">(ProcはRubyの中でも少し高度なトピックなので、今すぐわからなくても心配する必要はありません。)</blockquote>
という事なので追々ます（ってことでいいかな？）

さておき、テストはGREENになりました！
<h4>Dependent: destroy</h4>
ユーザーが破棄された場合、ユーザーのマイクロポストも一緒に破棄されるべき！
→has_manyメソッドにオプションを渡すことで実装できる 
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  # UserとそのMicropostは has_many (1対多) の関係性がある
  # （ユーザーが削除された時）紐づいているマイクロポストも削除される
  has_many :microposts, dependent: :destroy
・
・
・</pre> 
※「マイクロポストは削除しない」指定をすることもできる
[blogcard url="https://qiita.com/kobukurosirokuma/items/b86ac6b620d2b15c0164"]
<blockquote class="quote-rort">これは、管理者がシステムからユーザーを削除したとき、持ち主の存在しないマイクロポストがデータベースに取り残されてしまう問題を防ぎます。</blockquote>
テストを書いて動作確認！→Userモデルを検証
<blockquote class="quote-rort"> (idを紐づけるための) ユーザーを作成することと、そのユーザーに紐付いたマイクロポストを作成する必要があります。その後、ユーザーを削除してみて、マイクロポストの数が1つ減っているかどうかを確認します。</blockquote>
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb" >・
・
・
  test "associated microposts should be destroyed" do
    # @userを保存
    @user.save
    # @userに紐付いたマイクロポストを作成（content:属性に"Lorem ipsum"の値）
    @user.microposts.create!(content: "Lorem ipsum")
    # ブロック内の処理の前後で'Micropost.countが1減っていればtrue
    assert_difference 'Micropost.count', -1 do
      # @userを削除
      @user.destroy
    end
  end
end
</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>Micropost.first.created_atの実行結果と、Micropost.last.created_atの実行結果を比べてみましょう。</li>
	<li>Micropost.firstを実行したときに発行されるSQL文はどうなっているでしょうか? 同様にして、Micropost.lastの場合はどうなっているでしょうか? ヒント: それぞれをコンソール上で実行したときに表示される文字列が、SQL文になります。</li>
	<li>データベース上の最初のユーザーを変数userに代入してください。そのuserオブジェクトが最初に投稿したマイクロポストのidはいくつでしょうか? 次に、destroyメソッドを使ってそのuserオブジェクトを削除してみてください。削除すると、そのuserに紐付いていたマイクロポストも削除されていることをMicropost.findで確認してみましょう。</li>
</ol>
</blockquote>
1.
（下記参照）
Micropost.first.created_atの方がMicropost.last.created_atより新しい投稿（降順に並び替えているため）
2.
<pre class="lang:default decode:true " title="ターミナル" >>> Micropost.first.created_at
  # DESC LIMIT ?  [["LIMIT", 1]] → 降順　取得件数上限　１
  # 一番最初のデータを取りたい → （降順に設定してある） → 並んでるデータの一初めのデータを取り出す
  Micropost Load (1.4ms)  SELECT  "microposts".* FROM "microposts" ORDER BY "microposts"."created_at" DESC LIMIT ?  [["LIMIT", 1]]
=> Sat, 02 May 2020 17:51:22 JST +09:00
>> Micropost.last.created_at
  # ACS LIMIT ?  [["LIMIT", 1]] → 昇順 取得件数上限　１
  # 一番最後のデータを取りたい　→　降順を昇順に並べ替え　→　並んでるデータの一初めのデータを取り出す（実質一番最後のデータ）
  Micropost Load (0.2ms)  SELECT  "microposts".* FROM "microposts" ORDER BY "microposts"."created_at" ASC LIMIT ?  [["LIMIT", 1]]
=> Sat, 02 May 2020 16:03:43 JST +09:00</pre> 
3. 
<pre class="lang:default decode:true " >>> user = User.first
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-04-09 04:57:00", updated_at: "2020-04-29 07:46:39", password_digest: "$2a$10$4L1EmqW3mOD46Zkp/AH4qes4ldTJ7o3iWGqdek5frNI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$y3BA/9QXPjjE51k3UFPRoeLq5pE3uhVhXXaaWOXuTWb...", activated: true, activated_at: "2020-04-09 04:57:00", reset_digest: "$2a$10$L5Ve9H4KbFblp3nLdBwe/OOmHRL6.W8Y7c9Xncmx.vs...", reset_sent_at: "2020-05-02 05:49:25">
# 最初に投稿したマイクロポスト → 最初(first)ではなく、古い（DBの最後→last）
# Micropost id: 1
>> user.microposts.last
  Micropost Load (0.2ms)  SELECT  "microposts".* FROM "microposts" WHERE "microposts"."user_id" = ? ORDER BY "microposts"."created_at" ASC LIMIT ?  [["user_id", 1], ["LIMIT", 1]]
=> #<Micropost id: 1, content: "Lorem ipsum", user_id: 1, created_at: "2020-05-02 07:03:43", updated_at: "2020-05-02 07:03:43">
>> user.destroy
   (0.1ms)  begin transaction
  Micropost Load (0.2ms)  SELECT "microposts".* FROM "microposts" WHERE "microposts"."user_id" = ? ORDER BY "microposts"."created_at" DESC  [["user_id", 1]]
  SQL (2.3ms)  DELETE FROM "microposts" WHERE "microposts"."id" = ?  [["id", 2]]
  SQL (0.1ms)  DELETE FROM "microposts" WHERE "microposts"."id" = ?  [["id", 1]]
  SQL (0.7ms)  DELETE FROM "users" WHERE "users"."id" = ?  [["id", 1]]
   (7.6ms)  commit transaction
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-04-09 04:57:00", updated_at: "2020-04-29 07:46:39", password_digest: "$2a$10$4L1EmqW3mOD46Zkp/AH4qes4ldTJ7o3iWGqdek5frNI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$y3BA/9QXPjjE51k3UFPRoeLq5pE3uhVhXXaaWOXuTWb...", activated: true, activated_at: "2020-04-09 04:57:00", reset_digest: "$2a$10$L5Ve9H4KbFblp3nLdBwe/OOmHRL6.W8Y7c9Xncmx.vs...", reset_sent_at: "2020-05-02 05:49:25">
# destroyしたuserのマイクロポストであるmicropost.idが1のマイクロポストを取り出そうとすると・・・
>> Micropost.find(1)
  Micropost Load (0.1ms)  SELECT  "microposts".* FROM "microposts" WHERE "microposts"."id" = ? ORDER BY "microposts"."created_at" DESC LIMIT ?  [["id", 1], ["LIMIT", 1]]
Traceback (most recent call last):
        1: from (irb):29
# idが１のマイクロポストは見つかりませんなメッセージ → 削除されたことがわかる
ActiveRecord::RecordNotFound (Couldn't find Micropost with 'id'=1)

#「userを削除すると紐付いたマイクロポストも削除されている」の確認なのでこっちでは？って思ったりする
>> Micropost.find_by(user_id: 1)
  Micropost Load (0.1ms)  SELECT  "microposts".* FROM "microposts" WHERE "microposts"."user_id" = ? ORDER BY "microposts"."created_at" DESC LIMIT ?  [["user_id", 1], ["LIMIT", 1]]
=> nil
</pre> 
因みにuser.destroyした時のログで紐付いたマイクロポストも削除されているのが読み取れる
<h3>まとめとか感想</h3>
SQLもやっておくといいよを実感する章
Progateとかでざっくりでいいので、やっておくだけでも全然違うと思いました。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

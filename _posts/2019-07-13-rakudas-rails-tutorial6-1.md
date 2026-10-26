---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜6.1"
date: "2019-07-13 19:06:41"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 847
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial6-1/"
---
6章でやる事
ユーザー用のデータモデルの作成と、データを保存する手段の確保
→ユーザー登録を出来る様にする
<h2>6.1 Userモデル</h2>
<blockquote class="quote-rort">
Railsでは、データモデルとして扱うデフォルトのデータ構造のことをモデル (Model) と呼びます (1.3.3で言うMVCのMのことです)
</blockquote>
railsの標準のデータ構造→model
こんな雰囲気でいいのかな?

railsではデータベースを使ってデータを長期保存している
→データベースとやり取りをしてデータオブジェクトの作成・保存・検索を行うメソッドが用意されている
→マイグレーション (Migration)機能により、データの機能をRubyで記述できる
<h4>マイグレーションとはなんぞ</h4>
migration→移行・移動　的な言葉
railsで言うマイグレーションとは
→データベースにテーブルを作成したり、既存のテーブルにカラムを追加するなどの変更を加えたりできる機能
→使用するデータベースがMySQLでもSQLiteでも対応できる
→専用のファイルを作成して実行する（詳細は後程）
<h4>リレーショナルデータベースとはなんぞ</h4>
<a href="https://ja.wikipedia.org/wiki/%E9%96%A2%E4%BF%82%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9" rel="noopener noreferrer" target="_blank">関係データベース（リレーショナルデータベース）</a>
考えないで感じましょう😊（ひとまず）

作業を始める前にブランチを切る
→$ git checkout -b modeling-users
<h3>6.1.1 データベースの移行</h3>
nameとemailの2つの属性を持つUserモデルを作成する
→保存したいデータ
　id（数字）・name（短い文字列）・email（短い文字列）

→対応するデータモデル案
（カラム名）：（データ型）
id:integer　数列（整数）
name：string　文字列
email：string　文字列
これに沿ってモデルを作成する
<pre class="lang:default decode:true " title="ターミナル" >#controller名では複数形だったけどmodelは単数形
$ rails generate model User name:string email:string
  
Running via Spring preloader in process 4752
      invoke  active_record
                #マイグレーションファイル
      create    db/migrate/20190708020426_create_users.rb
      create    app/models/user.rb
      invoke    test_unit
      create      test/models/user_test.rb
      create      test/fixtures/users.yml</pre>
<h4>マイグレーションファイルとはなんぞ</h4>
データベースに与える変更を定義したchangeメソッドの集まり
ファイル名の頭にタイムスタンプが付く
<pre class="lang:ruby decode:true " title="/sample_app/db/migrate/20190708020426_create_users.rb
">class CreateUsers < ActiveRecord::Migration[5.1]
  def change
    create_table :users do |t|　
      t.string :name     #stringの型を持つnameカラム
      t.string :email    #stringの型を持つemailカラム

      t.timestamps       #created_atとupdated_atを作成する
    end
  end
end</pre>
<h4>created_atとupdated_atとはなんぞ</h4>
それぞれ、そのデータの作成時刻・更新時刻を自動的に記録したタイムスタンプ
<h4>マジックカラム（MagicColumn）とはなんぞ</h4>
マジックカラム→予約語→プログラムでもともと「予約（設定）」されている単語
使われる予定がある単語なので変数名や関数名に定義できない
<h4>実際生成されるUserモデル</h4>
usersテーブル
（カラム名）：（データ型）
         id：integer
       name：string
      email：string
 created_at：datetime
 updated_at：datetime
<h4>マイグレーションの適用</h4>
マイグレーションはコマンドを入力することで実行され、データベースに変更を加える
<pre class="lang:default decode:true " title="ターミナル" >$ rails db:migrate</pre>
初めてdb:migrateを実行した時にdb/development.sqlite3という名前のファイルが生成される
→SQLite5データベースの実体
（DB Browser for SQLiteと言うツールを使用してデータベースの構造を見ることが出来る）
（本文参照にて）
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
Railsはdb/ディレクトリの中にあるschema.rbというファイルを使っています。これはデータベースの構造 (スキーマ (schema) と呼びます) を追跡するために使われます。さて、あなたの環境にあるdb/schema.rbの内容を調べ、その内容とマイグレーションファイル (リスト 6.2) の内容を比べてみてください。
</li>
<li>
ほぼすべてのマイグレーションは、元に戻すことが可能です (少なくとも本チュートリアルにおいてはすべてのマイグレーションを元に戻すことができます)。元に戻すことを「ロールバック (rollback)と呼び、Railsではdb:rollbackというコマンドで実現できます。
上のコマンドを実行後、db/schema.rbの内容を調べてみて、ロールバックが成功したかどうか確認してみてください (コラム 3.1ではマイグレーションに関する他のテクニックもまとめているので、参考にしてみてください)。上のコマンドでは、データベースからusersテーブルを削除するためにdrop_tableコマンドを内部で呼び出しています。これがうまくいくのは、drop_tableとcreate_tableがそれぞれ対応していることをchangeメソッドが知っているからです。この対応関係を知っているため、ロールバック用の逆方向のマイグレーションを簡単に実現することができるのです。なお、あるカラムを削除するような不可逆なマイグレーションの場合は、changeメソッドの代わりに、upとdownのメソッドを別々に定義する必要があります。詳細については、Railsガイドの「Active Record マイグレーション」を参照してください。
</li>
<li>
もう一度rails db:migrateコマンドを実行し、db/schema.rbの内容が元に戻ったことを確認してください。
</li>
</ol>
</blockquote>
1.
<pre class="lang:ruby decode:true " title="/sample_app/db/migrate/20190708020426_create_users.rb
">class CreateUsers < ActiveRecord::Migration[5.1]
  def change
    create_table :users do |t|
      t.string :name
      t.string :email

      t.timestamps
    end
  end
end</pre>
<pre class="lang:ruby mark:(11-12) decode:true " title="/sample_app/db/schema.rb" >・
・
・
#（マイグレーションファイルのタイムスタンプ）
ActiveRecord::Schema.define(version: 20190708020426) do

  create_table "users", force: :cascade do |t|
    t.string "name"
    t.string "email"
    #t.timestampsに含まれるマジックカラムが展開（？）されている（nullはfalse）
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false

  end

end</pre> 
内容はほぼ同じ
force: :cascade→外部キーが適切であればスキーマが再読み込みできるようにするための記述
外部キー→他のテーブルと結びつけるためのキー（ざっくり）
スキーマ→データベースの構造（ざっくり）
（このあたりの用語を念頭に感じ取る！）
2.3.は動作確認のみにて省略
<h3>6.1.2 modelファイル</h3>
Modelファイルの確認
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
Railsコンソールを開き、User.newでUserクラスのオブジェクトが生成されること、そしてそのオブジェクトがApplicationRecordを継承していることを確認してみてください (ヒント: 4.4.4で紹介したテクニックを使ってみてください)。</li>
<li>
同様にして、ApplicationRecordがActiveRecord::Baseを継承していることについて確認してみてください。
</li>

</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル" >#1.
>> @user.class
=> User(id: integer, name: string, email: string, created_at: datetime, updated_at: datetime)
>> @user.class.superclass
=> ApplicationRecord(abstract)
#2.
>> @user.class.superclass.superclass
=> ActiveRecord::Base</pre>
<h3>6.1.3 ユーザーオブジェクトを作成する</h3>
sandboxモード　→データベースに変更を加えたくないときに使用
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> User.new
   #引数が無いのですべての値がnilで帰ってくる
=> #<User id: nil, name: nil, email: nil, created_at: nil, updated_at: nil>
>> user = User.new(name: "Michael Hartl", email: "mhartl@example.com")
   #インスタンスをuserに代入　引数に値も渡す
=> #<User id: nil, name: "Michael Hartl", email: "mhartl@example.com", created_at: nil, updated_at: nil>
>> user.valid?
   #オブジェクトが有効かどうかを確認するメソッド→.valid?
=> true　　#有効なのでtrue
>> user.save
   #saveメソッドを呼び出してオブジェクトを保存
=> true   #保存できたのでtrue
>> user   #保存したuserオブジェクトを呼び出す
=> #<User id: 1, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-10 09:28:16", updated_at: "2019-07-10 09:28:16">
   #保存したことによりidと作成と更新のタイムスタンプが入力された

   #ドット記法を用いて属性にアクセスできる
>> user.name
=> "Michael Hartl"
>> user.email
=> "mhartl@example.com"
>> user.updated_at
=> Wed, 10 Jul 2019 09:28:16 UTC +00:00

   #modelの生成と保存を同時に行う方法もある↓
   #trueかfalseの代わりにオブジェクト自身が返ってくる
>> User.create(name: "A Nother", email: "another@example.org")
=> #<User id: 2, name: "A Nother", email: "another@example.org", created_at: "2019-07-10 09:38:57", updated_at: "2019-07-10 09:38:57">
   #変数に代入することも出来る
>> foo = User.create
=> #<User id: 3, name: nil, email: nil, created_at: "2019-07-10 09:39:47", updated_at: "2019-07-10 09:39:47">

   #データの削除と、削除したと言う事の保存を同時に行うことも出来る
>> foo.destroy
=> #<User id: 3, name: nil, email: nil, created_at: "2019-07-10 09:39:47", updated_at: "2019-07-10 09:39:47">
   #削除したオブジェクトはメモリ上には残っている
>> foo
=> #<User id: 3, name: nil, email: nil, created_at: "2019-07-10 09:39:47", updated_at: "2019-07-10 09:39:47"></pre>
オブジェクトが本当に削除されているか、などの検索方法は次項で
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>user.nameとuser.emailが、どちらもStringクラスのインスタンスであることを確認してみてください。</li>
<li>created_atとupdated_atは、どのクラスのインスタンスでしょう</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール・上記の続き）" >#1.#2.それぞれ.classで確認
>> user.name.class
=> String
>> user.email.class
=> String
>> user.created_at.class
=> ActiveSupport::TimeWithZone
>> user.created_at.class.superclass
=> Object
>> user.updated_at.class
=> ActiveSupport::TimeWithZone
>> user.updated_at.class.superclass
=> Object</pre>
<h3>6.1.4 ユーザーオブジェクトを検索する</h3>
様々な検索方法
<pre class="lang:default decode:true " title="ターミナル（コンソール・上記の続き）" >>> User.find(1)
   #Userモデルから引数の（値や配列）のレコードを取得
   #()は省略できる奴
=> #<User id: 1, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-10 09:28:16", updated_at: "2019-07-10 09:28:16">
>> User.find(3)
   #上記で削除したレコード（id: 3）を呼び出そうとしたので例外が発生
ActiveRecord::RecordNotFound (Couldn't find User with 'id'=3)

>> User.find_by(email: "mhartl@example.com")
   #Userモデルから、指定した条件の最初の1件を取得する
   #()は省略できる奴
=> #<User id: 1, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-10 09:28:16", updated_at: "2019-07-10 09:28:16">

>> User.first
 　#Userモデルの最初の1件を取得
=> #<User id: 1, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-10 09:28:16", updated_at: "2019-07-10 09:28:16">

>> User.all
   #Userモデルの全てのデータを取得
=> #<ActiveRecord::Relation [#<User id: 1, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-10 09:28:16", updated_at: "2019-07-10 09:28:16">, #<User id: 2, name: "A Nother", email: "another@example.org", created_at: "2019-07-10 09:38:57", updated_at: "2019-07-10 09:38:57">]</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
nameを使ってユーザーオブジェクトを検索してみてください。また、 find_by_nameメソッドが使えることも確認してみてください (古いRailsアプリケーションでは、古いタイプのfind_byをよく見かけることでしょう)。
</li>
<li>
実用的な目的のため、User.allはまるで配列のように扱うことができますが、実際には配列ではありません。User.allで生成されるオブジェクトを調べ、ArrayクラスではなくUser::ActiveRecord_Relationクラスであることを確認してみてください。
</li>
<li>
User.allに対してlengthメソッドを呼び出すと、その長さを求められることを確認してみてください (4.2.3)。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール・上記の続き）" >#1.()は！省略！出来る！！（でもあった方がわかりやすくないです？）
>> user.name
=> "Michael Hartl"
>> User.find_by name: "Michael Hartl"
=> #<User id: 1, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-10 09:28:16", updated_at: "2019-07-10 09:28:16">
>> User.find_by_name "Michael Hartl"
=> #<User id: 1, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-10 09:28:16", updated_at: "2019-07-10 09:28:16">
#2.　.classで調べる
>> User.all.class
=> User::ActiveRecord_Relation

#3. .lengthで調べる（現状データは2個なので合ってる）
>> User.all.length
=> 2</pre>
<h3>6.1.5 ユーザーオブジェクトを更新する</h3>
<pre class="lang:default decode:true " title="ターミナル（コンソール・上記の続き）" >>> user
   #userの中身を確認
=> #<User id: 1, name: "Michael Hartl", email: "mhartl@example.com", created_at: "2019-07-12 06:37:54", updated_at: "2019-07-12 06:37:54">
>> user.email = "mhartl@example.net"
   #user.emailに新しい情報を代入
=> "mhartl@example.net"
>> user.save 
   #情報を保存
=> true
>> user
    #userの中身を確認(emailが変更されている　updated_atも変更されている）
=> #<User id: 1, name: "Michael Hartl", email: <"mhartl@example.net", created_at: "2019-07-12 06:37:54", updated_at: "2019-07-12 06:39:16">
</pre>
saveで保存をせずに再読み込みをすると変更が取り消される
（DBから情報を読み込むため）
<pre class="lang:default decode:true " title="ターミナル" >>> user.email
   #userのemailを呼び出す
=> "mhartl@example.net"
>> user.email = "foo@bar.com"
   #新しい情報を代入
=> "foo@bar.com"
>> user.reload.email
   #上書きしていない状態で再読み込み
=> "mhartl@example.net"
   #DBから情報を読み込むので元のemailになる</pre>
<h4>update_attributesメソッド</h4>
属性のハッシュを受け取り、検証に成功した場合に更新と保存を同時に行うメソッド
<pre class="lang:default decode:true " title="ターミナル（コンソール・上記の続き）" >>> user.update_attributes(name: "The Dude", email: "dude@abides.org")
=> true    #成功するとtrueが返ってくる
>> user.name
=> "The Dude"
>> user.email
=> "dude@abides.org"
#update_attributesは一つでも検証に失敗すると呼び出せない
#特定の属性のみを更新したい場合はupdate_attribute（←単数形）を使う
>> user.update_attribute(:name, "El Duderino")
#nameのみを更新する
=> true
>> user.name
=> "El Duderino"
</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>userオブジェクトへの代入を使ってname属性を使って更新し、saveで保存してみてください。</li>
<li>今度はupdate_attributesを使って、email属性を更新および保存してみてください。</li>
<li>
同様にして、マジックカラムであるcreated_atも直接更新できることを確認してみてください。ヒント: 更新するときは「1.year.ago」を使うと便利です。これはRails流の時間指定の１つで、現在の時刻から１年前の時間を算出してくれます。</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール・上記の続き）" >>> user   #現状を確認
=> #<User id: 1, name: "El Duderino", email: "dude@abides.org", created_at: "2019-07-12 06:37:54", updated_at: "2019-07-12 07:20:41">
#1.
>> user.name = "rakuda"
=> "rakuda"
>> user.save
=> true
#2.
>> user.update_attribute(:email, "rakuda@mail.com")                            
=> true
#3.
>> user.update_attribute(:created_at, 1.year.ago)
=> true
#データの更新を確認してみる
>> user
=> #<User id: 1, name: "rakuda", email: "rakuda@mail.com", created_at: "2018-07-12 07:27:24", updated_at: "2019-07-12 07:27:24"></pre>
<h3>まとめとか感想</h3>
Userモデルを作成して、
その後はコンソールでデータの作り方などを色々確認。
4章の時と似たような感じでしたね。

<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

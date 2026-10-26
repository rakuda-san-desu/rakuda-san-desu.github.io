---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜14.1"
date: "2020-06-25 20:06:55"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1539
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial14-1/"
---
14章では他のユーザーをフォロー及びフォロー解除できる仕組みと
フォローしているユーザーの投稿をステータスフィールドに表示する機能を追加していく。
<ol>
	<li>ユーザー間の関係をどうモデリングするか学ぶ</li>
	<li>モデリングに対応するWebインターフェイスを実装</li>
	<li>ステータスフィールドの完成版を実装</li>
</ol>
という流れ。

チュートリアルの中で最も難易度が高い手法をいくつか使っているとの事でｶﾞｸ🐫ﾌﾞﾙですねぇ！

<blockquote class="quote-rort">ページ操作の全体的なフローは次のようになります。あるユーザー (John Calvin) は自分のプロフィールページを最初に表示し (図 14.1)、フォローするユーザーを選択するためにUsersページ (図 14.2) に移動します。Calvinは2番目のユーザーThomas Hobbes (図 14.3) を表示し、[Follow] ボタンを押してフォローします。これにより、[Follow] ボタンが [Unfollow] に変わり、Hobbes の [followers] カウントが1人増えます (図 14.4)。CalvinがHomeページに戻ると、[following] カウントが1人増え、Hobbesのマイクロポストがステータスフィードに表示されるようになっていることがわかります (図 14.5)。</blockquote>
図解は<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-page_flow_profile_mockup">RoRT</a>本文参照にて
<h2>14.1 Relationshipモデル</h2>
データモデルの構成を考える
<blockquote class="quote-rort">素朴に考えれば、has_many (1対多) の関連付けを用いて「1人のユーザーが複数のユーザーをhas_manyとしてフォローし、1人のユーザーに複数のフォロワーがいることをhas_manyで表す」といった方法でも実装できそうです。</blockquote>
後述ありとの事だけども、この方法ではすぐに壁に突き当たるとの事
→has_many throughというものを使う

ひとまずブランチを作って開始ー
 
<pre class="toolbar:2 lang:default decode:true " >$ git checkout -b following-users</pre> 
<h3>データモデルの問題 (および解決策)</h3>
具体例で考えてみる
🐫目線→🐫は🐹をフォローしている
🐹目線→🐹は🐫にフォローされている
🐫は🐹から見ればフォロワー (follower) であり、🐫が🐹をフォロー (followed) した形
<blockquote class="quote-rort">Railsにおけるデフォルトの複数形の慣習に従えば、あるユーザーをフォローしているすべてのユーザーの集合はfollowersとなり、user.followersはそれらのユーザーの配列を表すことになります。しかし残念なことに、この名前付けは逆向きではうまくいきません (Railsというより英語の都合ですが)。</blockquote>
これに従うと🐫がフォローしているユーザーの集合はfollowedsとなって英語の文法にも合わないし見苦しい！
よって、ここではTwitterの慣習に倣ってfollowingという呼称を使っていく

呼称が決まったところでモデリングをしていく
<blockquote class="quote-rort">user.followingはユーザーの集合でなければならないため、followingテーブルのそれぞれの行は、followed_idで識別可能なユーザーでなければなりません (これはfollower_idの関連付けについても同様です)2 。さらに、それぞれの行はユーザーなので、これらのユーザーに名前やパスワードなどの属性も追加する必要があるでしょう。</blockquote>
図解は<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-naive_user_has_many_following">RoRT本文</a>参照
上記のデータモデルでは何かと無駄が多い（followingモデルにも名前やメールアドレスがありusersテーブルと被っている）
さらにfollowersの方をモデリングするときにも、同じぐらい無駄の多いテーブルを別に作成しなければならなくなってしまいます
→メンテナンスの観点から見て最悪です！
現状ではユーザー名を変更するたびにusersテーブルだけでなく
followingとfollowersの両テーブルもそのユーザーが含まれるすべての行を更新しなければならない！！
<h4>必要な抽象化</h4>
<blockquote class="quote-rort">この問題の根本は、必要な抽象化を行なっていないことです。正しいモデルを見つけ出す方法の1つは、Webアプリケーションにおけるfollowingの動作をどのように実装するかをじっくり考えることです。</blockquote>
<ol>
	<li> あるユーザーが別のユーザーをフォローするとき何が作成されるか</li>
	<li> あるユーザーが別のユーザーをフォロー解除するとき何が削除されるか</li>
</ol>
→それぞれあるユーザーと別のユーザーの「関係 (リレーションシップ)」が作成されたり削除されたりしている
一人のユーザーは1対多の関係を持てるし、
更にユーザーはリレーションシップを経由することにより、多くのfollowing (またはfollowers) と関係を持てるという事
<h4>左右非対称な関係性</h4>
Facebookなどでは左右対称なデータモデルが成り立ちますが（Facebook知らないからわかんないけどｗ）
Twitterのようなフォロー関係では左右非対称の性質がある。
🐫は🐹をフォローしていても、🐹は🐫をフォローしていないといった関係が成り立っている
→それぞれを能動的関係 (Active Relationship)と受動的関係 (Passive Relationship)と呼ぶ
上記の例で言えば🐫は🐹に対して「能動的関係」を持っている。逆に、🐹は🐫に対して「受動的関係」を持っている。と、いう事。
<h4>能動的関係</h4>
フォローしているユーザーを生成するために能動的関係に焦点を当てていく！
follower_idとfollowed_idを持つテーブルで
followed_idを通して、usersテーブルのフォローされているユーザーを見つけるようにする
<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-user_has_many_following" rel="noopener noreferrer" target="_blank">RoRT本文</a>参照
<blockquote class="quote-rort">能動的関係も受動的関係も、最終的にはデータベースの同じテーブルを使うことになります。したがって、テーブル名にはこの「関係」を表す「relationships」を使いましょう。モデル名はRailsの慣習にならって、Relationshipとします。</blockquote>
更に<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-relationship_model" rel="noopener noreferrer" target="_blank">RoRT本文</a>参照
<h4>Relationshipデータモデルを実装</h4>
<pre class="lang:default decode:true " title="ターミナル" ># マイグレーションを生成
$ rails generate model Relationship follower_id:integer followed_id:integer</pre> 
Relationshipモデルはfollower_idとfollowed_idで頻繁に検索することになるので
それぞれのカラムにインデックスを追加する 
<pre class="lang:default mark:(8-13) decode:true " title="/sample_app/db/migrate/timestamp_create_relationships.rb" >class CreateRelationships < ActiveRecord::Migration[5.1]
  def change
    create_table :relationships do |t|
      t.integer :follower_id
      t.integer :followed_id

      t.timestamps
    end
    add_index :relationships, :follower_id
    add_index :relationships, :followed_id
    # follower_idとfollowed_idの組み合わせが必ずユニークであることを保証する
    # 複合キーインデックスとオプションunique
    add_index :relationships, [:follower_id, :followed_id], unique: true
  end
end</pre> 
<pre class="lang:default decode:true " title="マイグレーションを実行" >$ rails db:migrate</pre> 
<h4>複合キーインデックスとは何ぞ</h4>
上記のコードで言えばfollower_idとfollowed_idの組み合わせが必ず一意であることを保証する仕組み
これにより同じユーザーを2回以上フォローすることを防ぐ
<blockquote class="quote-rort">もちろん、このような重複 (2回以上フォローすること) が起きないよう、インターフェイス側の実装でも注意を払います(14.1.4)。しかし、ユーザーが何らかの方法で (例えばcurlなどのコマンドラインツールを使って) Relationshipのデータを操作するようなことも起こり得ます。そのような場合でも、一意なインデックスを追加していれば、エラーを発生させて重複を防ぐことができます。</blockquote>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>図 14.7のid=1のユーザーに対してuser.following.map(&:id)を実行すると、結果はどのようになるでしょうか? 想像してみてください。ヒント: 4.3.2で紹介したmap(&:method_name)のパターンを思い出してください。例えばuser.following.map(&:id)の場合、idの配列を返します。</li>
	<li>図 14.7を参考にして、id=2のユーザーに対してuser.followingを実行すると、結果はどのようになるでしょうか? また、同じユーザーに対してuser.following.map(&:id)を実行すると、結果はどのようになるでしょうか? 想像してみてください。</li>
</ol>
</blockquote>
<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-user_has_many_following" rel="noopener noreferrer" target="_blank">RoRT本文・図14.7</a>
<ol>
	<li>id=1のユーザーが持つfollewd_idが配列で返る
[2,7,10,8]</li>
        <li>
<ul>
	<li>id=2のユーザーが持つfollewd_idと関連付いたidのユーザーデータが返る
[User id:1, name:Michael Hartl, email:mhartl@example.com]</li>
	<li>id=2のユーザーが持つfollewd_idが配列で返る
[1]</li>
</ul>
        </li>
</ol>
<h3>14.1.2 User/Relationshipの関連付け</h3>
UserとRelationshipの関連付けを行う
1人のユーザーにはhas_many（1対多）のリレーションシップがある
→このリレーションシップは2人のユーザー間の関係なので、フォローしているユーザーとフォロワーの両方に属する（belongs_to）
次のようなユーザー関連付けのコードを使って新しいリレーションシップを作成したいと考える
<pre class="toolbar:2 lang:default decode:true " ># userに紐づいた新しいリレーションシップを作成（フォローするユーザーのid）
user.active_relationships.build(followed_id: ...)
</pre> 
<h4>マイクロポストと関連付けをした際のコードとの違い</h4>
ユーザーとマイクロポストを関連付けした際、Userモデルでは下記のようなコードを書いた 
<pre class="toolbar:2 lang:default mark:(2-2) decode:true " >class User < ApplicationRecord
  has_many :microposts
  .
  .
  .
end</pre> 
Railsは引数の:micropostsシンボルに対応するMicropostモデルを探し出すことが出来たけど
今回も同じように書いてしまうと 
<pre class="toolbar:2 lang:ruby mark:(1-1) decode:true " >has_many :active_relationships</pre> 
となり、RelationshipモデルではなくActiveRelationshipモデルを探してしまう
→探してほしいモデルのクラス名を明示的に伝える必要がある！

反対にMicropostモデルでは下記のように書いた 
<pre class="toolbar:2 lang:ruby mark:(2-2) decode:true " >class Micropost < ApplicationRecord
  belongs_to :user
  .
  .
  .
end</pre> 
<blockquote class="quote-rort">Railsはデフォルトでは外部キーの名前を<class>_idといったパターンとして理解し、 <class>に当たる部分からクラス名 (正確には小文字に変換されたクラス名) を推測します</blockquote>
よって、micropostテーブルにあるuser_idをたどって所有者 (ユーザー) を特定することが出来る
今回の場合はfollower_idという外部キーを使ってフォロワーを特定したいけどfollowerと言うクラスは存在しない為
Railsに正しいクラス名を伝える必要がある！

これらを踏まえるとUserとRelationshipの関連付けはこのようになる 
<pre class="lang:ruby mark:(5-9) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  # UserとMicropostは has_many (1対多) の関係性がある
  # （ユーザーが削除された時）紐づいているマイクロポストも削除される
  has_many :microposts, dependent: :destroy
  # Userモデルと:active_relationshipsはhas_many (1対多) の関係性がある
  # クラスはRelationship、外部キーはfollower_id、（ユーザーが削除された時）紐づいているactive_relationshipsも削除される
  has_many :active_relationships, class_name:  "Relationship",
                                  foreign_key: "follower_id",
                                  dependent:   :destroy
・
・
・</pre> 
※マイクロポストの時と同様に、ユーザーを削除したらユーザーのリレーションシップも同時に削除される必要があるため
関連付けにdependent: :destroyも追加している 
<pre class="lang:ruby mark:(2-5) decode:true " title="/sample_app/app/models/relationship.rb" >class Relationship < ApplicationRecord
  # Relationshipとfollowerは1対1の関係にある　クラスはUser
  belongs_to :follower, class_name: "User"
  # Relationshipとfollowedは1対1の関係にある　クラスはUser
  belongs_to :followed, class_name: "User"
end</pre> 
<h4>突然出てきたように見えるactive_relationshipsとは何ぞ</h4>
4.1.1で触れられているように能動的関係 (Active Relationship)を表している
フォローした時、フォローされている時を同じモデルを使ってあれこれしたいので
使うモデルはRelationshipモデルだけど
関係性はモデル名ではなく別途定義している→明示的に伝えるモデル名はRelationship
と、いう事。
この章では「フォローする（能動的関係）」を扱っているためactive_relationshipsという関係性を結んでいる
あとで「フォローされている（受動的関係）」を扱う際にpassive_relationshipsという関係性も定義する
なんで別々の名前を使うのか→それぞれの名前を使ったメソッドや外部キーが使えるようになる
参考↓
[blogcard url="https://qiita.com/rapidliner00/items/2f68f3cda401c1c5227d"]
上記の関連付けを行ったことにより<a href="https://railstutorial.jp/chapters/following_users?version=5.1#table-association_methods_relationships" rel="noopener noreferrer" target="_blank">RoRT本文参照</a>なメソッドが使えるようになっている
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>コンソールを開き、表 14.1のcreateメソッドを使ってActiveRelationshipを作ってみましょう。データベース上に２人以上のユーザーを用意し、最初のユーザーが２人目のユーザーをフォローしている状態を作ってみてください</li>
	<li>先ほどの演習を終えたら、active_relationship.followedの値とactive_relationship.followerの値を確認し、それぞれの値が正しいことを確認してみましょう</li>
</ol>
</blockquote>
1.  
<pre class="lang:default decode:true " title="ターミナル" ># userにDBの1番目のユーザーを代入
>> user = User.first
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-06-14 08:55:22", updated_at: "2020-06-14 08:55:22", password_digest: "$2a$10$YNClbugN7F0OkBgI9te9xeTy3Q/O6E290W6saxP2cKI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$JG3OXrAi.s4vCJ9S1uViD.dBRBlupiM2VPyPoWWCVGm...", activated: true, activated_at: "2020-06-14 08:55:22", reset_digest: nil, reset_sent_at: nil>
# other_userにDBの２番目のユーザーを代入
>> other_user = User.second
  User Load (0.2ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ? OFFSET ?  [["LIMIT", 1], ["OFFSET", 1]]
=> #<User id: 2, name: "藤原 空", email: "example-1@railstutorial.org", created_at: "2020-06-14 08:55:23", updated_at: "2020-06-14 08:55:23", password_digest: "$2a$10$Geswjjb4620mgY5BHOydPOBEE/c5o1mS9ytVUgrlHdp...", remember_digest: nil, admin: false, activation_digest: "$2a$10$zT6jv4hDEwacD8HUgpKtqursQL0koL3ZSkPPi6f3QsU...", activated: true, activated_at: "2020-06-14 08:55:23", reset_digest: nil, reset_sent_at: nil>
# active_relationshipに代入　userがother_userをフォローしている状態を作成
>> active_relationship = user.active_relationships.create(followed_id: other_user.id)
   (0.1ms)  SAVEPOINT active_record_1
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (1.2ms)  INSERT INTO "relationships" ("follower_id", "followed_id", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["follower_id", 1], ["followed_id", 2], ["created_at", "2020-06-17 16:46:26.421815"], ["updated_at", "2020-06-17 16:46:26.421815"]]
   (0.1ms)  RELEASE SAVEPOINT active_record_1
=> #<Relationship id: 1, follower_id: 1, followed_id: 2, created_at: "2020-06-17 07:46:26", updated_at: "2020-06-17 07:46:26"></pre> 
2. 
<pre class="lang:default decode:true " title="ターミナル" ># active_relationshipに代入されたフォロー関係の、（userが）フォローしているユーザー（other_user）
>> active_relationship.followed
=> #<User id: 2, name: "藤原 空", email: "example-1@railstutorial.org", created_at: "2020-06-14 08:55:23", updated_at: "2020-06-14 08:55:23", password_digest: "$2a$10$Geswjjb4620mgY5BHOydPOBEE/c5o1mS9ytVUgrlHdp...", remember_digest: nil, admin: false, activation_digest: "$2a$10$zT6jv4hDEwacD8HUgpKtqursQL0koL3ZSkPPi6f3QsU...", activated: true, activated_at: "2020-06-14 08:55:23", reset_digest: nil, reset_sent_at: nil>
#　active_relationshipに代入されたフォロー関係の、フォロワー（other_userのフォロワー　→　user）
>> active_relationship.follower
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-06-14 08:55:22", updated_at: "2020-06-14 08:55:22", password_digest: "$2a$10$YNClbugN7F0OkBgI9te9xeTy3Q/O6E290W6saxP2cKI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$JG3OXrAi.s4vCJ9S1uViD.dBRBlupiM2VPyPoWWCVGm...", activated: true, activated_at: "2020-06-14 08:55:22", reset_digest: nil, reset_sent_at: nil>
</pre> 
表現がちょっとややこしいねぇ🐫
<h3>14.1.3 Relationshipのバリデーション</h3>
Relationshipモデルのテストとバリデーションを追加する
<blockquote class="quote-rort">User用のfixtureファイル (リスト 6.30) と同じように、生成されたRelationship用のfixtureでは、マイグレーション (リスト 14.1) で制約させた一意性を満たすことができません。ということで、ユーザーのときと同じで (リスト 6.31でfixtureの内容を削除したように)、今の時点では生成されたRelationship用のfixtureファイルも空にしておきましょう (リスト 14.6)。</blockquote> 
<pre class="lang:ruby decode:true " title="/sample_app/test/models/relationship_test.rb" >require 'test_helper'

class RelationshipTest < ActiveSupport::TestCase

  def setup
    # @relationshipに　新しいrelationshipオブジェクトを代入（michaelがarcherをフォローしている）
    @relationship = Relationship.new(follower_id: users(:michael).id,
                                     followed_id: users(:archer).id)
  end

  test "should be valid" do
    # @relationshipが有効である
    assert @relationship.valid?
  end

  test "should require a follower_id" do
    # @relationshipのfollower_idにnilを代入
    @relationship.follower_id = nil
    # @relationshipは無効である
    assert_not @relationship.valid?
  end

  test "should require a followed_id" do
    # @relationshipのfollowed_idにnilを代入
    @relationship.followed_id = nil
    # @relationshipは無効である
    assert_not @relationship.valid?
  end
end</pre>  
<pre class="lang:ruby mark:(6-9) decode:true " title="/sample_app/app/models/relationship.rb" >class Relationship < ApplicationRecord
  # Relationshipとfollowerは1対1の関係にある　クラスはUser
  belongs_to :follower, class_name: "User"
  # Relationshipとfollowedは1対1の関係にある　クラスはUser
  belongs_to :followed, class_name: "User"
  # follower_idが存在する
  validates :follower_id, presence: true
  # followed_idが存在する
  validates :followed_id, presence: true
end
</pre>  
<pre class="lang:default decode:true " title="/sample_app/test/fixtures/relationships.yml" ># 元々あるコードを削除して空にする</pre> 
テストはGREEN！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>リスト 14.5のバリデーションをコメントアウトしても、テストが成功したままになっていることを確認してみましょう。(以前のRailsのバージョンでは、このバリデーションが必須でしたが、Rails 5から必須ではなくなりました。今回はフォロー機能の実装を優先しますが、この手のバリデーションが省略されている可能性があることを頭の片隅で覚えておくと良いでしょう。)</li>
</ol>
</blockquote>
動作確認のみにて省略
<h3>14.1.4 フォローしているユーザー</h3>
<blockquote class="quote-rort">いよいよRelationshipの関連付けの核心、followingとfollowersに取りかかります。今回はhas_many throughを使います</blockquote>
ユーザーには複数の「フォローする」「フォローされる」といった関係性がある→多対多の関係性
デフォルトのhas_many throughという関連付けではモデル名 (単数形) に対応する外部キーを探す 
<pre class="toolbar:2 lang:default decode:true " >has_many :followeds, through: :active_relationships</pre> 
上記のコードは
「followeds」というシンボル名を「followed」という単数形に変えて、 relationshipsテーブルのfollowed_idを使って対象のユーザーを取得する働きを持つ
しかし！
「user.followeds」は英語として不適切なので「user.following」という名前を使いたい！
<blockquote class="quote-rort">そのためには、Railsのデフォルトを上書きする必要があります。ここでは:sourceパラメーター (リスト 14.8) を使って、「following配列の元はfollowed idの集合である」ということを明示的にRailsに伝えます。</blockquote> 
<pre class="lang:ruby mark:(10-12) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  # UserとMicropostは has_many (1対多) の関係性がある
  # （ユーザーが削除された時）紐づいているマイクロポストも削除される
  has_many :microposts, dependent: :destroy
  # Userモデルと:active_relationshipsはhas_many (1対多) の関係性がある
  # クラスはRelationship、外部キーはfollower_id、（ユーザーが削除された時）紐づいているactive_relationshipsも削除される
  has_many :active_relationships, class_name:  "Relationship",
                                  foreign_key: "follower_id",
                                  dependent:   :destroy
  # Userとfollowingはactive_relationshipsを介して多対多の関係を持っている
  # 関連付け（following）元の名前はfollowed
  has_many :following, through: :active_relationships, source: :followed
・
・
・</pre> 
これによりフォローしているユーザーを配列の様に扱えるようにる！
例えばこんなことが出来るようになっている↓
<pre class="toolbar:2 lang:default decode:true " ># user.followingにother_userが含まれている（user.followedから探す）
user.following.include?(other_user)
# user.followingからother_userを取得（user.followedから探す）
user.following.find(other_user)</pre> 
また、followingで取得したオブジェクトは、配列と同じく要素を追加したり削除したりすることも可能 
<pre class="toolbar:2 lang:default decode:true " ># user.followingの最後にother_userを追加
user.following << other_user
#user.followingからother_userを削除
user.following.delete(other_user)</pre> 
<blockquote class="quote-rort">followingメソッドで配列のように扱えるだけでも便利ですが、Railsは単純な配列ではなく、もっと賢くこの集合を扱っています</blockquote> 
<pre class="toolbar:2 lang:default decode:true " ># followingにother_userが含まれているか　というコード
# DBからすべてのユーザーを取得してメソッドを実行しているのではなく
# DBの中で直接比較をしている！
following.include?(other_user)</pre> 
<h4>follow・unfollowメソッドを追加</h4>
followingで取得した集合を簡単に扱えるようにするために
followやunfollowといった便利なメソッドを追加する！
さらにfollowing?論理値メソッドも追加し、特定のユーザーが誰かをフォローしているか確認できるようにする。
まずはテスト！
<blockquote class="quote-rort">というのも、Webインターフェイスなどで便利メソッドを使うのはまだ先なので、すぐに使える場面がなく、実装した手応えを得にくいからです。一方で、Userモデルに対するテストを書くのは簡単かつ今すぐできます。そのテストの中で、これらのメソッドを使っていきます。</blockquote>
<ol>
	<li>following?メソッドであるユーザーをまだフォローしていないことを確認</li>
	<li>followメソッドを使ってそのユーザーをフォロー</li>
	<li>following?メソッドを使ってフォロー中になったことを確認</li>
	<li>nfollowメソッドでフォロー解除できたことを確認</li>
</ol>
メソッドを作ってないからREDになるけど上記の手順で作ったテスト↓ 
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb" >・
・
・
  test "should follow and unfollow a user" do
    michael = users(:michael)
    archer  = users(:archer)
    # michaelはarcherをフォローしていない
    assert_not michael.following?(archer)
    # michaelがarcherをフォロー
    michael.follow(archer)
    # michaelはarcherをフォローしている
    assert michael.following?(archer)
    # michaelのarcherへのフォローをやめる
    michael.unfollow(archer)
    # michaelはarcherをフォローしていない
    assert_not michael.following?(archer)
  end
end</pre> 
さらに、<a href="https://railstutorial.jp/chapters/following_users?version=5.1#table-association_methods_relationships" rel="noopener noreferrer" target="_blank">表 14.1</a>のメソッドを参考にしながら、followingによる関連付けを使ってfollow・unfollow・following?メソッドを実装
この時、可能な限りselfを省略する！ 
→model内ではselfが省略できるので省略できるものはなるべく省略するのだ！
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # ユーザーをフォローする
  def follow(other_user)
    # followingの最後にother_userを追加
    following << other_user
  end

  # ユーザーをフォロー解除する
  def unfollow(other_user)
    # active_relationshipsからfollowed_idがother_user.idのデータを取得して削除
    active_relationships.find_by(followed_id: other_user.id).destroy
  end

  # 現在のユーザーがフォローしてたらtrueを返す
  def following?(other_user)
    # followingにother_userが含まれているか
    following.include?(other_user)
  end
    private
・
・
・</pre> 
これにてGREEN！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>コンソールを開き、リスト 14.9のコードを順々に実行してみましょう。</li>
	<li>先ほどの演習の各コマンド実行時の結果を見返してみて、実際にはどんなSQLが出力されたのか確認してみましょう。</li>
</ol>
</blockquote>
1. 
<pre class="toolbar:2 lang:default decode:true " >>> michael = User.first
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-06-14 08:55:22", updated_at: "2020-06-14 08:55:22", password_digest: "$2a$10$YNClbugN7F0OkBgI9te9xeTy3Q/O6E290W6saxP2cKI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$JG3OXrAi.s4vCJ9S1uViD.dBRBlupiM2VPyPoWWCVGm...", activated: true, activated_at: "2020-06-14 08:55:22", reset_digest: nil, reset_sent_at: nil>
>> archer = User.second
  User Load (0.2ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ? OFFSET ?  [["LIMIT", 1], ["OFFSET", 1]]
=> #<User id: 2, name: "藤原 空", email: "example-1@railstutorial.org", created_at: "2020-06-14 08:55:23", updated_at: "2020-06-14 08:55:23", password_digest: "$2a$10$Geswjjb4620mgY5BHOydPOBEE/c5o1mS9ytVUgrlHdp...", remember_digest: nil, admin: false, activation_digest: "$2a$10$zT6jv4hDEwacD8HUgpKtqursQL0koL3ZSkPPi6f3QsU...", activated: true, activated_at: "2020-06-14 08:55:23", reset_digest: nil, reset_sent_at: nil>
>> michael.following?(archer)
  User Exists (0.2ms)  SELECT  1 AS one FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."followed_id" WHERE "relationships"."follower_id" = ? AND "users"."id" = ? LIMIT ?  [["follower_id", 1], ["id", 2], ["LIMIT", 1]]
=> false
>> michael.follow(archer)
   (0.1ms)  SAVEPOINT active_record_1
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  SQL (1.8ms)  INSERT INTO "relationships" ("follower_id", "followed_id", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["follower_id", 1], ["followed_id", 2], ["created_at", "2020-06-22 15:16:25.770528"], ["updated_at", "2020-06-22 15:16:25.770528"]]
   (0.1ms)  RELEASE SAVEPOINT active_record_1
  User Load (0.1ms)  SELECT  "users".* FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."followed_id" WHERE "relationships"."follower_id" = ? LIMIT ?  [["follower_id", 1], ["LIMIT", 11]]
=> #<ActiveRecord::Associations::CollectionProxy [#<User id: 2, name: "藤原 空", email: "example-1@railstutorial.org", created_at: "2020-06-14 08:55:23", updated_at: "2020-06-14 08:55:23", password_digest: "$2a$10$Geswjjb4620mgY5BHOydPOBEE/c5o1mS9ytVUgrlHdp...", remember_digest: nil, admin: false, activation_digest: "$2a$10$zT6jv4hDEwacD8HUgpKtqursQL0koL3ZSkPPi6f3QsU...", activated: true, activated_at: "2020-06-14 08:55:23", reset_digest: nil, reset_sent_at: nil>]>
>> michael.following?(archer)
  User Exists (0.2ms)  SELECT  1 AS one FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."followed_id" WHERE "relationships"."follower_id" = ? AND "users"."id" = ? LIMIT ?  [["follower_id", 1], ["id", 2], ["LIMIT", 1]]
=> true
>> michael.unfollow(archer)
  Relationship Load (0.2ms)  SELECT  "relationships".* FROM "relationships" WHERE "relationships"."follower_id" = ? AND "relationships"."followed_id" = ? LIMIT ?  [["follower_id", 1], ["followed_id", 2], ["LIMIT", 1]]
   (0.1ms)  SAVEPOINT active_record_1
  SQL (0.2ms)  DELETE FROM "relationships" WHERE "relationships"."id" = ?  [["id", 3]]
   (0.0ms)  RELEASE SAVEPOINT active_record_1
=> #<Relationship id: 3, follower_id: 1, followed_id: 2, created_at: "2020-06-22 06:16:25", updated_at: "2020-06-22 06:16:25">
>> michael.following?(archer)
  User Exists (0.2ms)  SELECT  1 AS one FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."followed_id" WHERE "relationships"."follower_id" = ? AND "users"."id" = ? LIMIT ?  [["follower_id", 1], ["id", 2], ["LIMIT", 1]]
=> false</pre> 
2. 
SQL文を感じましょう🐫（雰囲気は読み取れるよね）
<h3>14.1.5 フォロワー</h3>
user.followingメソッドと対になるuser.followersメソッドを追加する
<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-user_has_many_followers">RoRT本文</a>の図解参照だけどactive_relationshipsテーブルのfollower_idとfollowed_idを入れ替える形で活用できるっぽいので
実装はこのようになる↓ 
<pre class="lang:ruby mark:(10-14,18-20) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  # UserとMicropostは has_many (1対多) の関係性がある
  # （ユーザーが削除された時）紐づいているマイクロポストも削除される
  has_many :microposts, dependent: :destroy
  # Userモデルと:active_relationshipsはhas_many (1対多) の関係性がある
  # クラスはRelationship、外部キーはfollower_id、（ユーザーが削除された時）紐づいているactive_relationshipsも削除される
  has_many :active_relationships, class_name:  "Relationship",
                                  foreign_key: "follower_id",
                                  dependent:   :destroy
  # Userモデルと:passive_relationshipsはhas_many (1対多) の関係性がある
  # クラスはRelationship、外部キーはfollowed_id、（ユーザーが削除された時）紐づいているpassive_relationshipsも削除される
  has_many :passive_relationships, class_name:  "Relationship",
                                   foreign_key: "followed_id",
                                   dependent:   :destroy
  # Userとfollowingはactive_relationshipsを介して多対多の関係を持っている
  # 関連付け（following）元の名前はfollowed
  has_many :following, through: :active_relationships, source: :followed
  # Userとfollowingはpassive_relationshipsを介して多対多の関係を持っている
  # 関連付け（following）元の名前はfollower　←source: :followerは省略可能
  has_many :followers, through: :passive_relationships, source: :follower
・
・
・</pre> 
source: :followerが省略可能なのは
「followers」属性の場合、followedと違いRailsが「followers」を単数形にして自動的にfollower_idを探すことが出来るから！
しかし類似性を強調するためにあえて残しているとの事。
<h4>followersに対するテスト</h4>
followers.include?メソッドを使ってデータモデルをテストしていく 
<pre class="lang:ruby mark:(13-14) decode:true " title="/sample_app/test/models/user_test.rb" >・
・
・
  test "should follow and unfollow a user" do
    michael = users(:michael)
    archer  = users(:archer)
    # michaelはarcherをフォローしていない
    assert_not michael.following?(archer)
    # michaelがarcherをフォロー
    michael.follow(archer)
    # michaelはarcherをフォローしている
    assert michael.following?(archer)
    # archerのフォロワーにmichaelが含まれる
    assert archer.followers.include?(michael)
    # michaelのarcherへのフォローをやめる
    michael.unfollow(archer)
    # michaelはarcherをフォローしていない
    assert_not michael.following?(archer)
  end
end</pre> 
<blockquote class="quote-rort">リスト 14.13ではリスト 14.9に1行だけ追加していますが、実際には多くの処理が正しく動いていなければパスしません。つまり、リスト 14.12の実装に対するテストは、実装の影響を受けやすいテストだといえます。</blockquote>
ともあれテストはGREEN！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>コンソールを開き、何人かのユーザーが最初のユーザーをフォローしている状況を作ってみてください。最初のユーザーをuserとすると、user.followers.map(&:id)の値はどのようになっているでしょうか?</li>
	<li>上の演習が終わったら、user.followers.countの実行結果が、先ほどフォローさせたユーザー数と一致していることを確認してみましょう。</li>
	<li>user.followers.countを実行した結果、出力されるSQL文はどのような内容になっているでしょうか? また、user.followers.to_a.countの実行結果と違っている箇所はありますか? ヒント: もしuserに100万人のフォロワーがいた場合、どのような違いがあるでしょうか? 考えてみてください。</li>
</ol>
</blockquote>
 
<pre class="toolbar:2 lang:default decode:true " >>> user = User.first
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ?  [["LIMIT", 1]]
=> #<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-06-14 08:55:22", updated_at: "2020-06-14 08:55:22", password_digest: "$2a$10$YNClbugN7F0OkBgI9te9xeTy3Q/O6E290W6saxP2cKI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$JG3OXrAi.s4vCJ9S1uViD.dBRBlupiM2VPyPoWWCVGm...", activated: true, activated_at: "2020-06-14 08:55:22", reset_digest: nil, reset_sent_at: nil>
>> user2 = User.second
  User Load (0.2ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ? OFFSET ?  [["LIMIT", 1], ["OFFSET", 1]]
=> #<User id: 2, name: "藤原 空", email: "example-1@railstutorial.org", created_at: "2020-06-14 08:55:23", updated_at: "2020-06-14 08:55:23", password_digest: "$2a$10$Geswjjb4620mgY5BHOydPOBEE/c5o1mS9ytVUgrlHdp...", remember_digest: nil, admin: false, activation_digest: "$2a$10$zT6jv4hDEwacD8HUgpKtqursQL0koL3ZSkPPi6f3QsU...", activated: true, activated_at: "2020-06-14 08:55:23", reset_digest: nil, reset_sent_at: nil>
>> user3 = User.third
  User Load (0.1ms)  SELECT  "users".* FROM "users" ORDER BY "users"."id" ASC LIMIT ? OFFSET ?  [["LIMIT", 1], ["OFFSET", 2]]
=> #<User id: 3, name: "松田 陽太", email: "example-2@railstutorial.org", created_at: "2020-06-14 08:55:23", updated_at: "2020-06-14 08:55:23", password_digest: "$2a$10$YgaVBmyjy6s33rsi5RX4y.fuUoca2GUUioGa1d.aXK4...", remember_digest: nil, admin: false, activation_digest: "$2a$10$Rwsl/ANf4fOjd6CjVjpMuOF8m7oWUbTFdUuPLU3rfD6...", activated: true, activated_at: "2020-06-14 08:55:23", reset_digest: nil, reset_sent_at: nil>
>> user4 = User.find(4)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 4], ["LIMIT", 1]]
=> #<User id: 4, name: "安藤 太郎", email: "example-3@railstutorial.org", created_at: "2020-06-14 08:55:23", updated_at: "2020-06-14 08:55:23", password_digest: "$2a$10$Kfrh72qXWxdWPZADg6iyBO5dH6.d.b5XBx9Uo9Ims8e...", remember_digest: nil, admin: false, activation_digest: "$2a$10$jcJiCN5T/1AQIu6jOy5J/e6cLTMxrmzgz0r3FftIcVN...", activated: true, activated_at: "2020-06-14 08:55:23", reset_digest: nil, reset_sent_at: nil>
>> user2.follow(user)
   (0.1ms)  SAVEPOINT active_record_1
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (1.9ms)  INSERT INTO "relationships" ("follower_id", "followed_id", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["follower_id", 2], ["followed_id", 1], ["created_at", "2020-06-22 17:28:06.304851"], ["updated_at", "2020-06-22 17:28:06.304851"]]
   (0.1ms)  RELEASE SAVEPOINT active_record_1
  User Load (0.1ms)  SELECT  "users".* FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."followed_id" WHERE "relationships"."follower_id" = ? LIMIT ?  [["follower_id", 2], ["LIMIT", 11]]
=> #<ActiveRecord::Associations::CollectionProxy [#<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-06-14 08:55:22", updated_at: "2020-06-14 08:55:22", password_digest: "$2a$10$YNClbugN7F0OkBgI9te9xeTy3Q/O6E290W6saxP2cKI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$JG3OXrAi.s4vCJ9S1uViD.dBRBlupiM2VPyPoWWCVGm...", activated: true, activated_at: "2020-06-14 08:55:22", reset_digest: nil, reset_sent_at: nil>]>
>> user3.follow(user)
   (0.1ms)  SAVEPOINT active_record_1
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 3], ["LIMIT", 1]]
  SQL (0.2ms)  INSERT INTO "relationships" ("follower_id", "followed_id", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["follower_id", 3], ["followed_id", 1], ["created_at", "2020-06-22 17:28:28.084640"], ["updated_at", "2020-06-22 17:28:28.084640"]]
   (0.1ms)  RELEASE SAVEPOINT active_record_1
  User Load (0.1ms)  SELECT  "users".* FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."followed_id" WHERE "relationships"."follower_id" = ? LIMIT ?  [["follower_id", 3], ["LIMIT", 11]]
=> #<ActiveRecord::Associations::CollectionProxy [#<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-06-14 08:55:22", updated_at: "2020-06-14 08:55:22", password_digest: "$2a$10$YNClbugN7F0OkBgI9te9xeTy3Q/O6E290W6saxP2cKI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$JG3OXrAi.s4vCJ9S1uViD.dBRBlupiM2VPyPoWWCVGm...", activated: true, activated_at: "2020-06-14 08:55:22", reset_digest: nil, reset_sent_at: nil>]>
>> user4.follow(user)
   (0.1ms)  SAVEPOINT active_record_1
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 4], ["LIMIT", 1]]
  SQL (0.1ms)  INSERT INTO "relationships" ("follower_id", "followed_id", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["follower_id", 4], ["followed_id", 1], ["created_at", "2020-06-22 17:28:32.244272"], ["updated_at", "2020-06-22 17:28:32.244272"]]
   (0.1ms)  RELEASE SAVEPOINT active_record_1
  User Load (0.1ms)  SELECT  "users".* FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."followed_id" WHERE "relationships"."follower_id" = ? LIMIT ?  [["follower_id", 4], ["LIMIT", 11]]
=> #<ActiveRecord::Associations::CollectionProxy [#<User id: 1, name: "Example User", email: "example@railstutorial.org", created_at: "2020-06-14 08:55:22", updated_at: "2020-06-14 08:55:22", password_digest: "$2a$10$YNClbugN7F0OkBgI9te9xeTy3Q/O6E290W6saxP2cKI...", remember_digest: nil, admin: true, activation_digest: "$2a$10$JG3OXrAi.s4vCJ9S1uViD.dBRBlupiM2VPyPoWWCVGm...", activated: true, activated_at: "2020-06-14 08:55:22", reset_digest: nil, reset_sent_at: nil>]>
>> user.followers.map(&:id)
  User Load (0.2ms)  SELECT "users".* FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."follower_id" WHERE "relationships"."followed_id" = ?  [["followed_id", 1]]
=> [2, 3, 4]
>> user.followers.count
   (0.2ms)  SELECT COUNT(*) FROM "users" INNER JOIN "relationships" ON "users"."id" = "relationships"."follower_id" WHERE "relationships"."followed_id" = ?  [["followed_id", 1]]
=> 3
>> user.followers.to_a.count
=> 3</pre> 
<ol>
	<li>userをフォローしたユーザーのidを順に取り出したものになっている</li>
	<li>一致</li>
	<li>to_aメソッドが入るとDBのデータを直接ではなく配列を生成してからカウントしているので時間が掛かるしDBに負担が掛かる</li>
</ol>
<h3>まとめとか感想</h3>
最難関というだけあって、難しいっていうかややこしいっていうか難しかったでした！
けど、多対多って言うのは活用場面多そうなので頑張ってついていきたい🐫
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜14.3"
date: "2020-08-08 17:22:03"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1587
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial14-3/"
---
<h2>14.3 ステータスフィード</h2>
ステータスフィードを実装する！
→フォローされているユーザーのマイクロポストとログイン中のユーザーのマイクロポストを合わせて表示する
見本はこちら→<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-home_page_feed_mockup" rel="noopener noreferrer" target="_blank">RoRT本文参照</a>
<h3>14.3.1 動機と計画</h3>
<blockquote class="quote-rort">現在のユーザーによってフォローされているユーザーに対応するユーザーidを持つマイクロポストを取り出し、同時に現在のユーザー自身のマイクロポストも一緒に取り出すことです。</blockquote>
<a href="https://railstutorial.jp/chapters/following_users?version=5.1#fig-user_feed" rel="noopener noreferrer" target="_blank">図</a>
テストの方が明確な雰囲気なのでテストから書いていく（テスト駆動開発！）
<h4>フィードのテスト</h4>
<ol>
	<li> フォローしているユーザーのマイクロポストがフィードに含まれている</li>
	<li> 自分自身のマイクロポストもフィードに含まれている</li>
	<li>フォローしていないユーザーのマイクロポストがフィードに含まれていない</li>
</ol>
上記3つの条件を満たすテストを定義していく
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb" >  test "feed should have the right posts" do
    # michaelはlanaをフォローしている　archerはフォローしていない
    # /sample_app/test/fixtures/relationships.yml参照
    michael = users(:michael)
    archer  = users(:archer)
    lana    = users(:lana)
    # フォローしているユーザーの投稿を確認
    # lanaのmicropostsを順に取り出してpost_followingに代入
    lana.microposts.each do |post_following|
      # michaelのfeedにpost_followingが含まれている
      assert michael.feed.include?(post_following)
    end
    # 自分自身の投稿を確認
    # michaelのmicropostsを順に取り出してpost_selfに代入
    michael.microposts.each do |post_self|
      # michaelのfeedにpost_selfが含まれている
      assert michael.feed.include?(post_self)
    end
    # フォローしていないユーザーの投稿を確認
    # archerのmicropostsを順に取り出してpost_unfollowedに代入
    archer.microposts.each do |post_unfollowed|
      # michaelのfeedにpost_unfollowedが含まれていない
      assert_not michael.feed.include?(post_unfollowed)
    end
  end
end</pre> 
feedに何も定義していないのでテストはRED
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>マイクロポストのidが正しく並んでいると仮定して (すなわち若いidの投稿ほど古くなる前提で)、図 14.22のデータセットでuser.feed.map(&:id)を実行すると、どのような結果が表示されるでしょうか? 考えてみてください。ヒント: 13.1.4で実装したdefault_scopeを思い出してください。</li>
</ol>
</blockquote>
user.feedのidを配列にして取り出す→[1,2,4,5,7,9,10]
投稿日時が古い順に並んでしまう
<h3>14.3.2 フィードを初めて実装する</h3>
<blockquote class="quote-rort">ステータスフィードに対する要件定義はリスト 14.42のテストで明確になったので (つまりこのテストにパスすれば良いので)、早速フィードの実装に着手してみましょう。</blockquote>
<a href="https://wa3.i-3-i.info/word1453.html" title="要件定義" rel="noopener noreferrer" target="_blank">要件定義</a> #とは
最終的な形は込み入っているのでパーツ毎に確認しつつ導入していくとの事。
<h4>フィードで必要なクエリ</h4>
<blockquote class="quote-rort">micropostsテーブルから、あるユーザー (つまり自分自身) がフォローしているユーザーに対応するidを持つマイクロポストをすべて選択 (select) することです。</blockquote>
これをコードにするとこう↓
<pre class="toolbar:2 lang:default decode:true " ># micropostsテーブルを検索
SELECT * FROM microposts
# 取得条件　user_idに　<list of ids>か　user_id = <user id>　を含むもの
WHERE user_id IN (<list of ids>) OR user_id = <user id></pre> 
<>は例えの証でコードの一部じゃないんだって🐫
<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#sec-a_proto_feed" rel="noopener noreferrer" target="_blank">13.3.3</a>でプロトタイプを作成した際は
現在のユーザーに対するユーザーidを持つマイクロポストを選択したけど
<pre class="toolbar:2 lang:default decode:true " >Micropost.where("user_id = ?", id)</pre> 
今回はもう少し複雑になる
例えばこう↓ 
<pre class="toolbar:2 lang:default decode:true " >Micropost.where("user_id IN (?) OR user_id = ?", following_ids, id)
</pre> 
（following_idsについては読み進めればわかる🐫よくあるパターン）
これらの条件から、フォローされているユーザーに対するidの配列が必要であることがわかる
→mapメソッドで実装可能！
<blockquote class="quote-rort">このメソッドはすべての「列挙可能 (enumerable)」なオブジェクト (配列やハッシュなど、要素の集合で構成されるあらゆるオブジェクト) で使えます</blockquote>
例えばこんな事も 
<pre class="toolbar:2 lang:default decode:true " ># 配列を文字列に変換してひとつづつ取り出す
>> [1, 2, 3, 4].map { |i| i.to_s }
=> ["1", "2", "3", "4"]

# 短縮表記が使える
>> [1, 2, 3, 4].map(&:to_s)
=> ["1", "2", "3", "4"]

# さらにjoinメソッドを繋げてidの集合をカンマで区切った文字列へ
>> [1, 2, 3, 4].map(&:to_s).join(', ')
=> "1, 2, 3, 4"</pre> 
これによりuser.followingにある各要素のidを呼び出して、フォローしているユーザーのidを配列として扱うことができる
例えばこんな感じ 
<pre class="lang:default decode:true " >>> User.first.following.map(&:id)
=> [3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22,
23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41,
42, 43, 44, 45, 46, 47, 48, 49, 50, 51]</pre> 
これはとても便利なのでActive Recordでは次のようなメソッドも用意されている 
<pre class="toolbar:2 lang:default decode:true " >>> User.first.following_ids
=> [3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22,
23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41,
42, 43, 44, 45, 46, 47, 48, 49, 50, 51]</pre> 
<blockquote class="quote-rort">このfollowing_idsメソッドは、has_many :followingの関連付けをしたときにActive Recordが自動生成したものです (リスト 14.8)。これにより、user.followingコレクションに対応するidを得るためには、関連付けの名前の末尾に_idsを付け足すだけで済みます</blockquote>
結果、フォローしているユーザーidの文字列を取得するにはこう 
<pre class="toolbar:2 lang:default decode:true " >>> User.first.following_ids.join(', ')
=> "3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22,
23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41,
42, 43, 44, 45, 46, 47, 48, 49, 50, 51"</pre> 
なんだけど、
実際はRailsがよしなにやってくれるのでfollowing_idsメソッドを使えばOK
<pre class="lang:default decode:true " >Micropost.where("user_id IN (?) OR user_id = ?", following_ids, id)</pre> 
実装するとこう↓ 
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # ユーザーのステータスフィードを返す
  def feed
    # Micropostテーブルから取得　条件→user_idにフォローしているユーザーのidか現在のユーザーのidを持つもの
    Micropost.where("user_id IN (?) OR user_id = ?", following_ids, id)
  end
・
・
・</pre> 
<blockquote class="quote-rort">いくつかのアプリケーションにおいては、この初期実装だけで目的が達成され、十分に思えるかもしれません。しかしリスト 14.44にはまだ足りないものがあります。それが何なのか、次に進む前に考えてみてください (ヒント: フォローしているユーザーが5,000人もいたらどうなるでしょうか?)。</blockquote>
→読み込みデータが多すぎて重たくなる？（？
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>リスト 14.44において、現在のユーザー自身の投稿を含めないようにするにはどうすれば良いでしょうか? また、そのような変更を加えると、リスト 14.42のどのテストが失敗するでしょうか?</li>
	<li>リスト 14.44において、フォローしているユーザーの投稿を含めないようにするにはどうすれば良いでしょうか? また、そのような変更を加えると、リスト  14.42のどのテストが失敗するでしょうか?</li>
	<li>リスト 14.44において、フォローしていないユーザーの投稿を含めるためにはどうすれば良いでしょうか? また、そのような変更を加えると、リスト 14.42のどのテストが失敗するでしょうか? ヒント: 自分自身とフォローしているユーザー、そしてそれ以外という集合は、いったいどういった集合を表すのか考えてみてください。</li>
</ol>
</blockquote>
1. 
<pre class="toolbar:2 lang:default decode:true " ># 現在のユーザー自身の投稿を含めない（フォローしているユーザーの投稿のみ）
def feed
  Micropost.where("user_id IN (?)", following_ids)
end

#テストが失敗するのはここらへん
# 自分自身の投稿を確認
# michaelのmicropostsを順に取り出してpost_selfに代入
michael.microposts.each do |post_self|
  # michaelのfeedにpost_selfが含まれている
  assert michael.feed.include?(post_self)
end

# エラーメッセージはこれ
 FAIL["test_feed_should_have_the_right_posts", UserTest, 0.8520699739983684]
 test_feed_should_have_the_right_posts#UserTest (0.85s)
        Expected false to be truthy.
        test/models/user_test.rb:125:in `block (2 levels) in <class:UserTest>'
        test/models/user_test.rb:123:in `block in <class:UserTest>'</pre> 

2. 
<pre class="toolbar:2 lang:default decode:true " ># フォローしているユーザーの投稿を含めない（現在のユーザー自身の投稿のみ）
def feed
  Micropost.where("user_id = ?", id)
end

#テストが失敗するのはここらへん
# フォローしているユーザーの投稿を確認
# lanaのmicropostsを順に取り出してpost_followingに代入
lana.microposts.each do |post_following|
  # michaelのfeedにpost_followingが含まれている
  assert michael.feed.include?(post_following)
end

# エラーメッセージはこれ
 FAIL["test_feed_should_have_the_right_posts", UserTest, 0.8283004599998094]
 test_feed_should_have_the_right_posts#UserTest (0.83s)
        Expected false to be truthy.
        test/models/user_test.rb:119:in `block (2 levels) in <class:UserTest>'
        test/models/user_test.rb:117:in `block in <class:UserTest>'
</pre> 
3. 
<pre class="toolbar:2 lang:default decode:true " >#フォローしていないユーザーの投稿を含める（すべての投稿を含める）
def feed
  Micropost.all
end

#テストが失敗するのはここらへん
# フォローしていないユーザーの投稿を確認
# archerのmicropostsを順に取り出してpost_unfollowedに代入
archer.microposts.each do |post_unfollowed|
  # michaelのfeedにpost_unfollowedが含まれていない
  assert_not michael.feed.include?(post_unfollowed)
end

# エラーメッセージはこれ
 FAIL["test_feed_should_have_the_right_posts", UserTest, 0.8982799360001081]
 test_feed_should_have_the_right_posts#UserTest (0.90s)
        Expected true to be nil or false
        test/models/user_test.rb:131:in `block (2 levels) in <class:UserTest>'
        test/models/user_test.rb:129:in `block in <class:UserTest>'</pre> 
<h3>14.3.3 サブセレクト</h3>
現在のフィードの設計では投稿されたマイクロポストの数が膨大になったときにWebサービス全体が遅くなるなどの弊害があるなどする。
<blockquote class="quote-rort">14.3.2で示したコードの問題点は、following_idsでフォローしているすべてのユーザーをデータベースに問い合わせし、さらに、フォローしているユーザーの完全な配列を作るために再度データベースに問い合わせしている点です</blockquote>
もっと効率的なコードに置き換える→SQLのサブセレクト (subselect) を使うことで解決できる！！
<h4>サブセレクトとは何ぞ</h4>
わからないので調べたのだけど、
サブセレクトとサブクエリがごっちゃになって良くわかりません！ってなったので
<a href="http://newburukai.github.io" title="にゅ〜ぶる会" rel="noopener noreferrer" target="_blank">にゅ〜ぶる会</a>で質問したところ
<blockquote>サブクエリは、入れ子になってる子供のクエリの事。
サブセレクトは、入れ子になったセレクト文全体の事。
かな？</blockquote>
と、教えてもらいましてスッキリ感（いつもありがとうございますだねぇ🐫）
というわけでやっていく。
<pre class="toolbar:2 lang:default decode:true " ># これを
Micropost.where("user_id IN (?) OR user_id = ?", following_ids, id)

#こうする
Micropost.where("user_id IN (:following_ids) OR user_id = :user_id",
    following_ids: following_ids, user_id: id)</pre> 
同じ変数を複数の場所に挿入したい場合はプレースホルダー（？）ではなくハッシュで置き換えるほうが便利
ハッシュ形式で記述しておいてもSQLインジェクションを防げる！

コードの解説はこう↓ 
<pre class="toolbar:2 lang:default decode:true " ># following_idsをSQLに置き換えるとこうなる
following_ids = "SELECT followed_id FROM relationships
                 WHERE  follower_id = :user_id"

# 上のコードを使用したサブセレクトはこうなる
SELECT * FROM microposts
WHERE user_id IN (SELECT followed_id FROM relationships
                  WHERE  follower_id = :user_id)
      OR user_id = :user_id</pre> 
<blockquote class="quote-rort">このサブセレクトは集合のロジックを (Railsではなく) データベース内に保存するので、より効率的にデータを取得することができます。</blockquote>
これを実際のコードに落としこむとこう↓ 
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >・
・
・
・
# feedの定義
  def feed
    following_ids = "SELECT followed_id FROM relationships
                    WHERE follower_id = :user_id"
    Micropost.where("user_id IN (#{following_ids})
                     OR user_id = :user_id", user_id: id)
  end
・
・
・</pre> 
※「following_ids」という文字列はエスケープされているのではなく、見やすさを重視して式展開の形にしている
<blockquote class="quote-rort">もちろん、サブセレクトを使えばいくらでもスケールできるなどということはありません。大規模なWebサービスでは、バックグラウンド処理を使ってフィードを非同期で生成するなどのさらなる改善が必要でしょう。ただし、Webサービスをスケールさせる技術は非常に高度かつデリケートな問題なので、本書ではここまでの改善で止めておきます。</blockquote>
これにて完全なフィードが実装できた！！

いつもの流れでGitHubにpushとherokuにデプロイ！！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>Homeページで表示される1ページ目のフィードに対して、統合テストを書いてみましょう。リスト 14.49はそのテンプレートです。</li>
	<li>リスト 14.49のコードでは、期待されるHTMLをCGI.escapeHTMLメソッドでエスケープしています (このメソッドは11.2.3で扱ったCGI.escapeと同じ用途です)。このコードでは、なぜHTMLをエスケープさせる必要があったのでしょうか? 考えてみてください。ヒント: 試しにエスケープ処理を外して、得られるHTMLの内容を注意深く調べてください。マイクロポストの内容が何かおかしいはずです。また、ターミナルの検索機能 (Cmd-FもしくはCtrl-F) を使って「sorry」を探すと原因の究明に役立つはずです。</li>
</ol>
</blockquote>
1. 
<pre class="lang:default mark:(7-8,14-22) decode:true " >require 'test_helper'

class FollowingTest < ActionDispatch::IntegrationTest
  # setupでmichaelを@userに代入、archerを@otherに代入しそれぞれログイン済とする
  def setup
    @user = users(:michael)
    # 関係ないけど本文のリストには@other抜けてる
    @other = users(:archer)
    log_in_as(@user)
  end
・
・
・
  test "feed on Home page" do
    # root_pathにgetのリクエスト
    get root_path
    # @userのfeedのページネートの1ページ目から配列をひとつづつ取り出してmicropostに代入
    @user.feed.paginate(page: 1).each do |micropost|
      # response.bodyに特殊文字をエスケープしたmicropost.contentが含まれている
      assert_match CGI.escapeHTML(micropost.content), response.body
    end
  end
end</pre> 
2.
エスケープしないと記号が特殊文字で出力されてしまうため
<pre class="toolbar:2 lang:default decode:true " ># 「sorry」で検索すると出てくる　「'」が「&#39;」になっている
I&#39;m sorry. Your words made sense, but your sarcastic tone did not.</pre> 
<h4>feedメソッドをリファクタリング</h4>
記事を書きあがって添削していただいた際に
「feedメソッドってこれで完成形じゃないよね？」というお話がありまして。
現状だと書かれているのはSQL文なんですね。
と、いう訳で考えました。
[blogcard url="https://railsguides.jp/active_record_querying.html#%E3%82%B5%E3%83%96%E3%82%BB%E3%83%83%E3%83%88%E6%9D%A1%E4%BB%B6"]
この辺を参考に、まずはフォローしている人のmicropostを取り出す方法を考える
<pre class="toolbar:2 lang:ruby decode:true " >def feed
  Micropost.where(user_id: active_relationships.select(:followed_id))
end</pre> 
自身の投稿も含まれるようにする
[blogcard url="https://railsguides.jp/active_record_querying.html#or%E6%9D%A1%E4%BB%B6"] 
<pre class="toolbar:2 lang:ruby decode:true " >def feed
  Micropost.where(user_id: active_relationships.select(:followed_id))
           .or(Micropost.where(user_id: id))
end</pre> 
テストも通って良い感じにできたっぽい！
チェックしてもらうと、更にこんな形を教えていただきました！
よりすっきり！！ 
<pre class="toolbar:2 lang:ruby decode:true " >def feed
  Micropost.where(user: following).or(Micropost.where(user_id: id))
end</pre> 
<h2>14.4 最後に</h2>
まとめにつき割愛
機能拡張も割愛
ただ、I18nを使っての日本語化は随時やってましたです
あとはローカル環境を作ってテンプレートをslim&Sassで書いてみようと思っています。練習。
<h3>まとめとか感想</h3>
さあ！
これにてらくだ🐫にもできるRailsチュートリアルのまとめ、終了です！！
ブログにまとめながら、疑問をなるべく残さないように調べながらゆっくり進めた2週目。
理解度もかなり上がったと思うし、記録してあるので調べやすいｗ
今後は自分なりに何かを作ったりしていきたいと思います。
web（セルフ）サービス！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

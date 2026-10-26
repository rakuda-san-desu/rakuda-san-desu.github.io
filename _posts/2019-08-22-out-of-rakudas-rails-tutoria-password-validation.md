---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜番外編・パスワードのバリデーション"
date: "2019-08-22 17:14:29"
categories: ["Railsチュートリアル", "Ruby on Rails"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル番外編", "正規表現"]
published: true
wordpress_id: 895
wordpress_status: "publish"
permalink: "/out-of-rakudas-rails-tutoria-password-validation/"
---
番外編では本編からちょっと外れた話なんかを書いていきます
<h2>パスワードのバリデーションをアレンジする</h2>
チュートリアル本編でUserモデルのバリデーションの話が出てきました
せっかく（？）なので、パスワードのバリデーションをアレンジしていこうと思います

本文では「最小6文字」だけだったけど
「最大13文字」→「英・数・記号を各1文字以上」→「英大文字・英小文字・数・記号を各1文字以上」
こんな感じにステップアップ（？）していきます
<h3>現在の状態</h3>
パスワードのバリデーション　（パスワードが）存在する　,長さ　最小6文字
<pre class="lang:ruby mark:(9-9) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  before_save { email.downcase! }
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
  has_secure_password
  validates :password, presence: true, length: { minimum: 6 }
end</pre> 
<h3>最大12文字</h3>
実際は100文字とか、もっと多い設定でいいっぽいんだけど
ひとまず最大値を12文字としてみます。
<h4>テスト</h4>
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb
">・
・
・  
  test "password should have a maximum length" do
    @user.password = @user.password_confirmation = "a" * 13
    assert_not @user.valid?
　　#@user.password = @user.password_confirmationにaを13個代入した時
    #@userは有効か　→　Falseである　と言うテスト
  end
end</pre>
<h4>バリデーション</h4>
<pre class="lang:ruby mark:(9-9) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  before_save { email.downcase! }
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
  has_secure_password
  validates :password, presence: true, length: { minimum: 6, <span class="point">maximum: 12</span> }
end</pre>
コンソールで色々試して、
13文字以上のパスワードを設定するとちゃんとfalseになりました
<h3>英・数・記号を各1文字以上</h3>
<h4>まずは厄介そうな正規表現の組み立てから</h4>
<a href="https://rubular.com/" rel="noopener noreferrer" target="_blank">Rubular</a>
上記のサイトで色々試しながらやっていきます
a~zから一文字　→　a-z
数字一文字　→　\d
<del datetime="2019-08-21T08:37:10+00:00">記号を一文字　→　\D</del>
\Dは英字と記号両方（数字以外）なので　\Wに
\W →　単語以外の文字　（\wで表現される任意の単語文字（文字、数字、アンダースコア）以外の文字）

これを組み合わせて
<strong>[a-z\d\W]</strong>　←これだとどれか1個が入っていればOKになっちゃうからアウト
<strong>[a-z]\d\W</strong>　←これだと各1文字以上はクリアするけど順不同にできない

わかる範囲の事を色々試すも上手くいかないのでググりまして
こちらを参考に組み立てられそうな気持になるものの
[blogcard url="https://qiita.com/mpyw/items/886218e7b418dfed254b"]
書かれている内容がどういうことなのか良くわからない
<a href="https://newburu.github.io/" rel="noopener noreferrer" target="_blank">Slack</a>で悩んでいると参考ページを教えていただけましたｱﾘｶﾞﾀﾔ
[blogcard url="https://qiita.com/momotaro98/items/460c6cac14473765ec14"]
そして、これで良さそうかな？という正規表現が出来ました
<strong>\A(?=.*?[a-z])(?=.*?\d)(?=.*?\W)[!-~]+\z</strong>
(?=.*?[a-z])　→　「任意の0回以上の文字列（文字があってもなくてもいい）とa-zのうちの一文字」が条件
(?=.*?\d)　→　「任意の0回以上の文字列（文字があってもなくてもいい）と数字のうちの一文字」が条件
(?=.*?\W)　→　「任意の0回以上の文字列（文字があってもなくてもいい）と文字、数字、アンダースコア以外の文字のうちの一文字」が条件
[!-~]+　→<a href="https://ja.wikipedia.org/wiki/ASCII" rel="noopener noreferrer" target="_blank">ASCIIコード</a>の順番の!-~のうちのいずれかを一文字以上繰り返す
「○○を一文字以上」の条件を指定する表現と、使う文字の指定の表現

使う文字の指定が「.(任意の文字）」じゃダメなのは空白を含んじゃうからかな？

と言う訳で
<strong>/\A(?=.*?[a-z])(?=.*?\d)(?=.*?\W)[!-~]+\z/i</strong>
これで試してみることにしました（因みにこのままではまだ間違ってるｗ）
<h4>テスト</h4>
emailの部分を参考に@user.password= @user.password_confirmationが
trueの時に成功するテストとfalseの時に成功するテストを書く
<pre class="lang:ruby decode:true " title="/sample_app/test/models/user_test.rb
">・
・
・
  test "password validation should accept valid password" do
    valid_password = %w[123abc#! 1a`df55 $12@vfl]
    valid_password.each do |valid_password|
      @user.password= @user.password_confirmation = valid_password
      assert @user.valid?,"#{valid_password.inspect} should be valid"
    end
  end
  
  test "password validation should reject invalid password" do
    valid_password = %w[123abc %12@._65 pf$?/vp;]
    valid_password.each do |valid_password|
      @user.password= @user.password_confirmation = valid_password
      assert_not @user.valid?,"#{valid_password.inspect} should be valid"
    end
  end</pre>
因みにこのままでは問題が残っています🐫
<h4>バリデーション</h4>
<pre class="lang:ruby mark:(8-8,11-11) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  before_save { email.downcase! }
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
  VALID_PASSWORD_REGEX = /\A(?=.*?[a-z])(?=.*?\d)(?=.*?\W)[!-~]+\z/i
  has_secure_password
  validates :password, presence: true, length: { minimum: 6, maximum: 12 },
                      format: { with: VALID_PASSWORD_REGEX }
end</pre> 
<h4>テスト</h4>
ここでテストは赤に
<pre class="lang:default mark:（11-11） decode:true " title="ターミナル（コンソール）" > FAIL["test_email_validation_should_accept_valid_addresses", UserTest, 0.43194788899927516]
 test_email_validation_should_accept_valid_addresses#UserTest (0.43s)
        "user@example.com" should be valid
        test/models/user_test.rb:39:in `block (2 levels) in '
        test/models/user_test.rb:37:in `each'
        test/models/user_test.rb:37:in `block in '

 FAIL["test_should_be_valid", UserTest, 0.4572523850001744]
 test_should_be_valid#UserTest (0.46s)
        Expected false to be truthy.
        test/models/user_test.rb:11:in `block in '

ERROR["test_email_addresses_should_be_saved_as_lower-case", UserTest, 0.46287917500012554]
 test_email_addresses_should_be_saved_as_lower-case#UserTest (0.46s)
ActiveRecord::RecordNotFound:         ActiveRecord::RecordNotFound: Couldn't find User without an ID
            test/models/user_test.rb:63:in `block in '</pre> 
特に注目したのが上記ハイライト部分
コードの該当部分は下記
<pre class="lang:default mark:(11-11) decode:true " title="/sample_app/test/models/user_test.rb" >require 'test_helper'

class UserTest < ActiveSupport::TestCase

  def setup
    @user = User.new(name: "Example User", email: "user@example.com",
                     password: "Foo123", password_confirmation: "Foo123")
  end

  test "should be valid" do
    assert @user.valid?
  end
・
・
・</pre> 
@userがtrueの時に成功するテストが失敗しているんですね

ん？@user？
って事でsetupメソッドの中のオブジェクトに注目してみる
パスワードが正しい形になっていませんね？！
と言う事で該当箇所を修正
<pre class="lang:ruby mark:(7-7) decode:true " title="/sample_app/test/models/user_test.rb" >require 'test_helper'

class UserTest < ActiveSupport::TestCase

  def setup
    @user = User.new(name: "Example User", email: "user@example.com",
                     password: "foo123!", password_confirmation: "foo123!")
  end

  test "should be valid" do
    assert @user.valid?
  end
・
・
・</pre> 
無事、テストが通るようになりました✨🐫
<h4>コンソールで試してみる</h4>
念の為（？）コンソールでいくつか試してみたところ
タイポしてないのにfalseが返ってきました
<pre class="lang:default decode:true " title="ターミナル（コンソール・サンドボックス）" >>> user = User.new(name: "rakuda3", email: "rakuda3@mail.com", password: "example_password")
=> #<User id: nil, name: "rakuda3", email: "rakuda3@mail.com", created_at: nil, updated_at: nil, password_digest: "$2a$10$Axy1OiVT83rz2ixL6IK.cenySbG1UqU48h/1w0OyM0d...">
>> user.valid?
=> false
>> user.errors.messages
=> {:password=>["is invalid"]}</pre>
パスワードがダメっぽい？
記号は「_（アンダーバー）」使ってるのに🐫？

・・・・・・！
らくだ🐫は思い出しました！
<strong>\W →　単語以外の文字　（\wで表現される任意の単語文字（文字、数字、アンダースコア）以外の文字）</strong>
\Wには「_」が含まれていないと言う事に！！
なので正規表現の該当部分を修正
<pre class="lang:ruby mark:(8-8) decode:true " title="/sample_app/app/models/user.rb" >class User < ApplicationRecord
  before_save { email.downcase! }
  validates :name,  presence: true, length: { maximum: 50 }
  VALID_EMAIL_REGEX = /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  validates :email, presence: true, length: { maximum: 255 },
                    format: { with: VALID_EMAIL_REGEX },
                    uniqueness: { case_sensitive: false }
  VALID_PASSWORD_REGEX = /\A(?=.*?[a-z])(?=.*?\d)(?=.*?[\W_])[!-~]+\z/i
  has_secure_password
  validates :password, presence: true, length: { minimum: 6, maximum: 12 },
                       format: { with: VALID_PASSWORD_REGEX }
end</pre> 
<h4>エラーにはならない問題点</h4>
さて、ここで気づいたんだけどエディタのuser_test.rbに⚠マークが
<a href="https://rakuda3desu.net/wp-content/uploads/2019/08/oo.p-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/08/oo.p-1.png" alt="" width="542" height="251" class="alignnone size-full wp-image-901" /></a>
テストではエラー出てないのに🤔となりつつ
ポインターを合わせるとメッセージが出たのでググってみました

こちらでずばり
[blogcard url="https://yu8mada.com/2018/06/16/about-the-warning-of-shadowing-outer-local-variable-in-ruby/"]
配列を代入した変数名とブロックパラメータが同じなのは良くないと言う事ですね
emailの方を見直して見ると確かに別々になっていました
なのでサクッと修正します
<pre class="lang:ruby mark:(2-3,10-11) decode:true " title="/sample_app/test/models/user_test.rb" > test "password validation should accept valid password" do
    valid_passwords = %w[123abc#! 1a`df55 $12@vfl]
    valid_passwords.each do |valid_password|
      @user.password= @user.password_confirmation = valid_password
      assert @user.valid?,"#{valid_password.inspect} should be valid"
    end
  end
  
  test "password validation should reject invalid password" do
    valid_passwords = %w[123abc %12@._65 pf$?/vp;]
    valid_passwords.each do |valid_password|
      @user.password= @user.password_confirmation = valid_password
      assert_not @user.valid?,"#{valid_password.inspect} should be valid"
    end
  end </pre> 
これで<strong>英・数・記号を各1文字以上</strong>の検証を追加することが出来ました✨
→そもそも規約に合わせて配列の変数名は複数形にしよう！ですよ！

因みに、
ここでは英字の小文字と大文字を区別しないので
正規表現の末尾に<strong>「i」</strong>を付けています。
<del datetime="2019-08-26T06:06:15+00:00">なので、validationも<strong>「before_save { email.downcase!, password.downcase! }」</strong>と付けたほうがいいのかもでした。</del>
小文字に変換してからvalidationにかけられる様に「before_save { email.downcase!, password.downcase! }」</strong>と付けたほうがいいのかもでした。
→本文に倣っての「before_save」でしたが、タイミング的にこれでは遅いっぽい？
[blogcard url="https://qiita.com/rtoya/items/29cef3e328299781a328"]
→やるならbefore_validationが適切っぽい
→でも入力された数値を勝手に変換するのはよくなくない？
→なので「大文字小文字の区別はされません」などの注意書きがあった方が良い感じ
<h3>英大文字・英小文字・数・記号を各1文字以上</h3>
上記が出来ていれば後は簡単アレンジ🐫
<h4>正規表現</h4>
<strong>/\A(?=.*?[a-z])(?=.*?[A-Z])(?=.*?\d)(?=.*?[\W_])[!-~]+\z/</strong>
英小文字と英大文字をそれぞれ別々に条件設定
末尾に「i」（大文字と小文字を区別させないオプション）は付けない
<h4>テスト</h4>
<pre class="lang:ruby mark:(7-7,13-13,21-21) decode:true " title="/sample_app/test/models/user_test.rb" >require 'test_helper'

class UserTest < ActiveSupport::TestCase

  def setup
    @user = User.new(name: "Example User", email: "user@example.com",
                     password: "Foo123!", password_confirmation: "Foo123!")
  end
・
・
・
  test "password validation should accept valid password" do
    valid_passwords = %w[123aBc#! 1a`Df55 $12@vFl S65_mL:]
    valid_passwords.each do |valid_password|
      @user.password= @user.password_confirmation = valid_password
      assert @user.valid?,"#{valid_password.inspect} should be valid"
    end
  end
  
  test "password validation should reject invalid password" do
    valid_passwords = %w[123abc %12@._65 pf$?/vp; 123abc#!]
    valid_passwords.each do |valid_password|
      @user.password= @user.password_confirmation = valid_password
      assert_not @user.valid?,"#{valid_password.inspect} should be valid"
    end
  end  

end</pre> 
setupメソッド内のpassword部分を英大文字・英小文字・数字・記号それぞれを含むように修正
また、上記で追加したテストも該当部分を修正
<h4>バリデーション</h4>
正規表現部分を英大文字と英小文字が区別される様に差し替え
<h4>テスト</h4>
テストもGREEN
サンドボックスなコンソールで色々試した結果も希望通りの物でした🎵🐫
<h3>まとめとか感想</h3>
参加している<a href="http://newburu.github.io" rel="noopener noreferrer" target="_blank">コミュニティ</a>で
パスワードのバリデーションに最大文字数も設定してみようと呟いたところ
「英数字記号１文字以上必須にしよう！」
「英字は大文字小文字それぞれにしちゃう？」
と、ハードルを丁度良く上げていただいたことがきっかけでとても勉強になりました🐫

詰まった時にヒントをもらえたりは勿論なんだけど
そもそも一人だったらこのタイミングでここまで挑戦しなかっただろうなって。
なので、とても感謝しておりますのだ。

他にもemailと部分一致は不可にするとかもあったんだけど
とりあえずパスワードのバリデーションのアレンジ」はひと段落としますｗ（いずれ挑戦したいです）

次からRailsチュートリアルの7章に入りますー


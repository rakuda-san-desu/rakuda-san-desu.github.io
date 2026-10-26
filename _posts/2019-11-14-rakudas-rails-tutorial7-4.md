---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜7.4"
date: "2019-11-14 12:31:33"
categories: ["Railsチュートリアル", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 1024
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial7-4/"
---
<h2>7.4 ユーザー登録成功</h2>
新規ユーザーのデータが有効な場合、データベースに保存できるようにする
<h3>7.4.1 登録フォームの完成</h3>
users_controller.rbにコードを追加
↓
createアクションに対応するテンプレートを作るのではなく
新しく作成されたユーザーの詳細画面にリダイレクトするように設定
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/app/controllers/users_controller.rb" >  def create
    @user = User.new(user_params)
    if @user.save
      #redirect_to user_url(@user)と同意
      #ユーザーの詳細画面に自動で変換してくれる
      redirect_to @user
    else
      render 'new'
    end
  end
  ・
  ・
  ・</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>有効な情報を送信し、ユーザーが実際に作成されたことを、Railsコンソールを使って確認してみましょう。</li>
<li>リスト 7.28を更新し、redirect_to user_url(@user)とredirect_to @userが同じ結果になることを確認してみましょう。</li>
</ol>
</blockquote>
動作確認のみにて省略
<h3>7.4.2 flash</h3>
<blockquote class="quote-rort">
登録完了後に表示されるページにメッセージを表示し (この場合は新規ユーザーへのウェルカムメッセージ)、2度目以降にはそのページにメッセージを表示しないようにするというものです。
</blockquote>
<h4>flash</h4>
Flashは直後のリクエストでのみ参照可能
ハッシュとしてアクセスする→<a href="https://api.rubyonrails.org/classes/ActionDispatch/Flash/FlashHash.html" rel="noopener noreferrer" target="_blank">FlashHash</a>インスタンス
<pre class="lang:ruby mark:(8-8) decode:true " title="/sample_app/app/controllers/users_controller.rb" >・
・
・
  def create
    @user = User.new(user_params)
    if @user.save
　　　#成功時のメッセージを代入するキーは:successとするのが一般的
      flash[:success] = "Welcome to the Sample App!"
      redirect_to @user
    else
      render 'new'
    end
  end
・
・
・</pre> 
<pre class="lang:ruby mark:(6-6,9-10) decode:true " title="/sample_app/app/views/layouts/application.html.erb" >・
・
・
    <div class="container">
      #each文でキーと値をそれぞれ取り出す
      <% flash.each do |message_type, message| %>
      #クラス名にキー（message_type）　表示内容に値（message）が出力される
      #キーの内容によって異なるスタイルを適用させられる
        <div class="alert alert-<%= message_type %>"><%= message %></div>
      <% end %>
      <%= yield %>
      <%= render 'layouts/footer' %>
      <%= debug(params) if Rails.env.development? %>
    </div>
・
・
・</pre> 
ハッシュに対してeachメソッドを実行すると
そのハッシュが持つキーと値のペアごとに処理を繰り返す
（4.3.3参照）
[blogcard url="https://rakuda3desu.net/rakudas-rails-tutorial4-3/"]
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>コンソールに移り、文字列内の式展開 (4.2.2) でシンボルを呼び出してみましょう。例えば"#{:success}"といったコードを実行すると、どんな値が返ってきますか? 確認してみてください。</li>
<li>先ほどの演習で試した結果を参考に、リスト 7.30のflashはどのような結果になるか考えてみてください</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#1.
>> "#{:success}"
=> "success"

#2.
   #7.30のflashの配列
>> flash = { success: "It worked!", danger: "It failed." }
=> {:success=>"It worked!", :danger=>"It failed."}
   #上記からsuccessのハッシュを展開
>> "#{flash[:success]}"
=> "It worked!"
</pre>
と、言う事？
<h4>フラッシュメッセージを日本語化してみる</h4>
I18nの記事に追加
[blogcard url="https://rakuda3desu.net/rort-memo-i18n/"]
<h3>7.4.3 実際のユーザー登録</h3>
一旦データベースをクリア
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >$ rails db:migrate:reset</pre>
本文に従ってユーザー登録してみる
<a href="https://rakuda3desu.net/wp-content/uploads/2019/11/7.4.3-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/11/7.4.3-1.png" alt="" width="801" height="346" class="alignnone size-full wp-image-1033" /></a>
出来た！flashメッセージも日本語になってる✨🐫
→リロードするとflashメッセージは消える
<h4>演習</h4>
<ol>
<li>Railsコンソールを使って、新しいユーザーが本当に作成されたのかもう一度チェックしてみましょう。結果は、リスト 7.32のようになるはずです。</li>
<li>自分のメールアドレスでユーザー登録を試してみましょう。既にGravatarに登録している場合、適切な画像が表示されているか確認してみてください。</li>
</ol>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#本文には無いけどusersデータが1個な事も確認
>> User.count
=> 1
>> User.find_by(email: "example@railstutorial.org")
=> #<User id: 1, name: "Rails Tutorial", email: "example@railstutorial.org", created_at: "2019-11-11 07:06:24", updated_at: "2019-11-11 07:06:24", password_digest: "$2a$10$IgiS5ygszAZUdJm6JKj.D.eNYlRHOmtjKolGn7W3ZFm..."></pre>
#2は確認のみにて省略(登録済のため画像表示アリ）
<del datetime="2019-11-11T07:03:00+00:00">所で時間が日本時間にあってないね？</del>
データベースでは標準時間だけどviewに表示させると日本時間になってた
ただし表示形式を指定してあげる必要があるっぽい！
<h3>7.4.4 成功時のテスト</h3>
何か忘れてると思った！テスト！！
→有効な情報が送信されてユーザーが作成されたことを確認する
<h4>assert_differenceメソッド</h4>
<blockquote class="quote-rort">このメソッドは第一引数に文字列 ('User.count') を取り、assert_differenceブロック内の処理を実行する直前と、実行した直後のUser.countの値を比較します。第二引数はオプションですが、ここには比較した結果の差異 (今回の場合は1) を渡します。</blockquote>
<pre class="lang:ruby decode:true " title="/sample_app/test/integration/users_signup_test.rb
">・
・
・
  test "valid signup information" do
    #signup_pathにgetのリクエスト
    get signup_path
    #ブロック内の処理の前後でUser.countが1増えていればtrue
    assert_difference 'User.count', 1 do
      #users_pathにpostのリクエスト→内容は有効なparamas[:user]ハッシュ
      post users_path, params: { user: { name:  "Example User",
                                         email: "user@example.com",
                                         password:              "password",
                                         password_confirmation: "password" } }
    end
    #POSTの送信結果に沿って指定されたリダイレクト先に移動
    follow_redirect!
    #users/showテンプレートが描写される
    assert_template 'users/show'
  end
end</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>7.4.2で実装したflashに対するテストを書いてみてください。どのくらい細かくテストするかはお任せします。リスト 7.34に最小限のテンプレートを用意しておいたので、参考にしてください (FILL_INの部分を適切なコードに置き換えると完成します)。ちなみに、テキストに対するテストは壊れやすいです。文量の少ないflashのキーであっても、それは同じです。筆者の場合、flashが空でないかをテストするだけの場合が多いです。</li>
<li>本文中でも指摘しましたが、flash用のHTML (リスト 7.31) は読みにくいです。より読みやすくしたリスト 7.35のコードに変更してみましょう。変更が終わったらテストスイートを実行し、正常に動作することを確認してください。なお、このコードでは、Railsのcontent_tagというヘルパーを使っています。</li>
<li>リスト 7.28のリダイレクトの行をコメントアウトすると、テストが失敗することを確認してみましょう。</li>
<li>リスト 7.28で、@user.saveの部分をfalseに置き換えたとしましょう (バグを埋め込んでしまったと仮定してください)。このとき、assert_differenceのテストではどのようにしてこのバグを検知するでしょうか? テストコードを追って考えてみてください</li>
</ol>
</blockquote>
#1.flashが空でないかのテスト
<pre class="lang:ruby mark:(7-7) decode:true " title="/sample_app/test/integration/users_signup_test.rb" >・
・
・
    follow_redirect!
    assert_template 'users/show'
    #falseである　→flashが空っぽか
    assert_not flash.empty?
  end
end
</pre> 
初め「assert_not flash.nil」にしてエラー
nilはnilでtrue/falseの判定にならない→論理値で返すために?を付ける
所でnilって存在しないって意味だよね？
このテストは中身が空っぽの場合？flash自体が存在しない場合？
→試してみたところ、nil?では期待する結果が出ませんでした
→<strong>flashが空でないか</strong>のテストって書いてあるね
と、言う訳で「assert_not flash.empty?」に落ち着きました
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#nilがtrueになるのはnilの時だけで""や" "でもfalseが返るためこのケースの判定にならない
#nilはnilであり""や" "はnilじゃない！
>> nil.nil?
=> true
>> a = ""
=> ""
>> a.empty?
=> true
>> a.nil?
=> false</pre>
ここらへんとか
[blogcard url="https://rakuda3desu.net/rakudas-rails-tutorial4-2/"]
#2,#3は動作確認のみなので省略
<pre class="lang:default decode:true " title="ターミナル（テスト結果）" >#4.
 FAIL["test_valid_signup_information", UsersSignupTest, 0.8270390810012032]
 test_valid_signup_information#UsersSignupTest (0.83s)
        #User.countが1変更されていない（増えていない）
        "User.count" didn't change by 1.
        #予想
        Expected: 1
          #実際
          Actual: 0
        #該当箇所
        test/integration/users_signup_test.rb:21:in `block in <class:UsersSignupTest>'</pre>
@user.saveが実行されない→ユーザー増えない→エラー
<h4>content_tagヘルパー</h4>
htmlタグを生成するヘルパーメソッド
<pre class="lang:ruby mark:(7-7) decode:true " title="/sample_app/app/views/layouts/application.html.erb" >・
・
・
<div class="container">
      <% flash.each do |message_type, message| %>
        #content_tag（出力したいタグ,表示させたい内容 , クラス名（変数展開も出来る））
        <%= content_tag(:div, message, class: "alert alert-#{message_type}") %>
      <% end %>
      <%= yield %>
      <%= render 'layouts/footer' %>
      <%= debug(params) if Rails.env.development? %>
    </div>
・
・
・</pre> 
<pre class="lang:default decode:true " title="ブラウザ" >#ハイライト部分の出力結果
<div class="alert alert-success">サンプルアプリへようこそ！</div></pre>
<h4>まとめとか感想</h4>
nil誤解してた
調べ直してなるほどーって思ったけどまたすぐ勘違いしそう💦
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

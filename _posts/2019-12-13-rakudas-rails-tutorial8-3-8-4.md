---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜8.3（と8.4）"
date: "2019-12-13 19:56:30"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1162
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial8-3-8-4/"
---
<h2>8.3 ログアウト</h2>
ログインが出来たらログアウトも出来たい！！
<blockquote class="quote-rort">
セッションを破棄するdestroyアクションも、引き続き同じ要領で作成することにします。ただし、ログインの場合 (リスト 8.15とリスト 8.25) と異なり、ログアウト処理は1か所で行えるので、destroyアクションに直接ログアウト処理を書くことにします。
</blockquote>
ログインの場合と異なり～→登録済ユーザーのログインとユーザー登録時のログインの2パターンあった
<h4>ログアウト処理の仕組み</h4>
sessionからuser_idを削除することでlog_inメソッドの実行結果を取り消す
<pre class="lang:ruby decode:true " title="コード見本" >session.delete(:user_id)</pre> 
このコードを実行することにより現在のユーザーがnilになる
→Sessionヘルパーにlog_outメソッドとして定義
<pre class="lang:ruby decode:true " title="/sample_app/app/helpers/sessions_helper.rb" >・
・
・
  # 現在のユーザーをログアウトする
  def log_out
    session.delete(:user_id)
    @current_user = nil
  end
end
</pre> 
<blockquote class="quote-rort">インスタンス変数@current_userをnilにする必要があるのは、@current_userがdestroyアクションより前に作成され (作成されていない場合)、かつ、リダイレクトを直接発行しなかった場合だけです。（中略）ここではセキュリティ上の死角を万が一にでも作り出さないためにあえてnilに設定しています。</blockquote>
sessionssコントローラのdestroyアクションに、上記で定義したlog_outメソッドを使う
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >・
・
・
  def destroy
    #ログアウトする（sessions_helperのlog_outメソッド）
    log_out
    #ルートURLにリダイレクト
    redirect_to root_url
  end
end</pre> 
<h4>ログアウトのテスト</h4>
<ul>	<liログアウトパスにdleteのリクエスト</li>
	<li>ユーザーがログアウトしてルートURLにリダイレクトされる</li>
	<li>ログイン用リンクが再度表示</li>
	<li>ログアウト用リンクとプロフィール用リンクが非表示</li>
</ul>
8.2.4のテストに上記の手順を組み込む
<pre class="lang:default mark:(4-7,11-12,25-38) decode:true " title="/sample_app/test/integration/users_login_test.rb" >・
・
・
  #テスト名も変更
  test "login with valid information followed by logout" do
    #login_pathにgetのリクエスト
    get login_path
    #login_pathにposuのリクエスト　内容→params: { session: { email: @user.email, password: 'password' } }
    post login_path, params: { session: { email:    @user.email,
                                          password: 'password' } }
    #テストユーザーがログインしている(test_helper.rbのis_logged_in?メソッド)
    assert is_logged_in?
    #ユーザー詳細画面にリダイレクトされる
    assert_redirected_to @user
    #実際にリダイレクト先に移動
    follow_redirect!
    #sers/showが描写される
    assert_template 'users/show'
    #login_pathへのリンクが0である
    assert_select "a[href=?]", login_path, count: 0
    #logout_pathへのリンクがある
    assert_select "a[href=?]", logout_path
    #user_path(@user)へのリンクがある
    assert_select "a[href=?]", user_path(@user)
    #logout_pathへdeleteのリクエスト
    delete logout_path
    #falseである  →テストユーザーがログインしている
    assert_not is_logged_in?
    #ルートURLへリダイレクト
    assert_redirected_to root_url
    #実際にリダイレクト先に移動
    follow_redirect!
    #login_pathへのリンクがある
    assert_select "a[href=?]", login_path
    #logout_pathへのリンクが0である
    assert_select "a[href=?]", logout_path,      count: 0
    #user_path(@user)へのリンクが0である
    assert_select "a[href=?]", user_path(@user), count: 0
  end
end</pre> 
<blockquote class="quote-rort">セッションのdestroyアクションの定義とテストも完成したので、ついにサンプルアプリケーションの基本となる「ユーザー登録・ログイン・ログアウト」の機能すべてが完成しました</blockquote>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>ブラウザから [Log out] リンクをクリックし、どんな変化が起こるか確認してみましょう。また、リスト 8.31で定義した３つのステップを実行してみて、うまく動いているかどうか確認してみましょう。</li>
	<li>cookiesの内容を調べてみて、ログアウト後にはsessionが正常に削除されていることを確認してみましょう。</li>
</ol>
</blockquote>
1.ログアウトするとレイアウトが変更される
　→ログインリンク表示・ログアウトリンク非表示・ユーザー詳細画面へのリンク非表示
2.削除されている
<h2>8.4 最後に</h2>
セルフプルリクからのセルフ取り込みでリポジトリとデータを揃える
herokuへデプロイ
以下省略
<h3>まとめとか感想</h3>
便利なヘルパーメソッドをオリジナルアプリを作る時に上手に使えるのだろうか
とか
基本的な、ではあるけど
サインイン→ログイン←→ログアウトが出来る様になって、それっぽくなってきて嬉しい！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

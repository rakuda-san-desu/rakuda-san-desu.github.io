---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜5.4（と5.5）"
date: "2019-07-04 16:44:51"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 837
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial5-4/"
---
<h2>5.4 ユーザー登録: 最初のステップ</h2>
ユーザー登録ページを作っていく
<h3>5.4.1 Usersコントローラ</h3>
新規ユーザー用の登録ページを作る
→Usersコントローラを作成
→newアクションを引数にしてgenerateを実行
（<a href="https://rakuda3desu.net/rakudas-rails-tutorial2-1/">2.1でやった</a>RESTアーキテクチャの規約に合わせる）
<h4>スタブとはなんぞ</h4>
スタブ→使い残し、半券、切り株などの意味
未完成の機能の代用になるもの、テスト用の仮のパーツ
<pre class="lang:default decode:true " title="ターミナル" >$ rails generate controller Users new
Running via Spring preloader in process 4173
      create  app/controllers/users_controller.rb
       route  get 'users/new'
      invoke  erb
      create    app/views/users
      create    app/views/users/new.html.erb
      invoke  test_unit
      create    test/controllers/users_controller_test.rb
      invoke  helper
      create    app/helpers/users_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/users.coffee
      invoke    scss
      create      app/assets/stylesheets/users.scss
      #引数に設定したnewアクションに対応するアクション・ルート・viewも自動的に生成されている</pre>
<h4>演習</h4>
<blockquote class="quote-rort"><ol>
<li>
表 5.1を参考にしながらリスト 5.41を変更し、users_new_urlではなくsignup_pathを使えるようにしてみてください。
</li>
<li>
先ほどの変更を加えたことにより、テストが redになったことを確認してください。なお、この演習はテスト駆動開発 (コラム 3.3) で説明した red/green のリズムを作ることを目的としています。このテストは次の5.4.2で greenになるよう修正します。
</li>
</ol>
</blockquote>
<pre class="lang:ruby mark:(9-9) decode:true " title="/sample_app/config/routes.rb" >#1.
Rails.application.routes.draw do

  root 'static_pages#home'
  get  '/help',    to:'static_pages#help'
  get  '/about',   to:'static_pages#about'
  get  '/contact', to:'static_pages#contact'
  
  get  '/signup',  to:'users#new'
end</pre> 
<pre class="lang:default decode:true " title="ターミナル（guard）" >#2.
ERROR["test_should_get_new", UsersControllerTest, 0.40452832200026023]
 test_should_get_new#UsersControllerTest (0.40s)
NameError:         NameError: undefined local variable or method `users_new_url' for #<UsersControllerTest:0x0000000005f2d898>
#`users_new_url'は未定義的なエラーメッセージ
            test/controllers/users_controller_test.rb:5:in `block in <class:UsersControllerTest>'

  7/7: [==============================================] 100% Time: 00:00:00, Time: 00:00:00

Finished in 0.43794s
7 tests, 16 assertions, 0 failures, 1 errors, 0 skips</pre>
ここからテストが通るようにコードを修正していく
<h3>5.4.2 ユーザー登録用URL</h3>
ユーザー登録用のページへのリンクが/signupになるようにルートを追加
（先ほどの演習1の内容）
→合わせてテストも修正
<pre class="lang:ruby mark:(5-8) decode:true " title="/sample_app/test/controllers/users_controller_test.rb" >require 'test_helper'

class UsersControllerTest &lt; ActionDispatch::IntegrationTest
  test "should get new" do
    #この行↓を消して
    get users_new_url 
    #この行↓に書き換え
    get signup_path    
    assert_response :success
  end

end</pre> 
本文に沿って
home.html.erbにあるSign up now!のリンクにsignup_pathを追加
new.html.erbを編集
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
もしまだ5.4.1.1の演習に取り掛かっていなければ、まずはリスト 5.41のように変更し、名前付きルートsignup_pathを使えるようにしてください。また、リスト 5.43で名前付きルートが使えるようになったので、現時点でテストが greenになっていることを確認してください。
</li>
<li>
先ほどのテストが正しく動いていることを確認するため、signupルートの部分をコメントアウトし、テスト redになることを確認してください。確認できたら、コメントアウトを解除して greenの状態に戻してください。
</li>
<li>
リスト 5.32の統合テストにsignupページにアクセスするコードを追加してください (getメソッドを使います)。コードを追加したら実際にテストを実行し、結果が正しいことを確認してください。ヒント: リスト 5.36で紹介したfull_titleヘルパーを使ってみてください。
</li>
</ol>
</blockquote>
1.2.は動作確認のみなので省略
<pre class="lang:default mark:(5-5,8-9) decode:true " title="/sample_app/test/integration/site_layout_test.rb" >・
・
・
    assert_select "a[href=?]", contact_path
    assert_select "a[href=?]", signup_path
    get contact_path
    assert_select "title", full_title("Contact")
    get signup_path
    assert_select "title", full_title("Sign up")
    
  end
end</pre> 
<h2>5.5 最後に</h2>
まとめと次回予告＆pushとデプロイとまとめ
<h3>まとめとか感想</h3>
Users登録ページの最初のステップと言う事で
controllerを作ったり名前付きルートを設定したりと、復習的な内容で短めにさっくり出来ました。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

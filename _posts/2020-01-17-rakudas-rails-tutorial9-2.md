---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜9.2"
date: "2020-01-17 16:00:40"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1210
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial9-2/"
---
<h2>[Remember me] チェックボックス</h2>
[remember me] チェックボックスでログインの保持を選択できるようにする
見本は<a href="https://railstutorial.jp/chapters/advanced_login?version=5.1#fig-login_remember_me_mockup" rel="noopener noreferrer" target="_blank">RoRT本文</a>
<h4>チェックボックスを追加する</h4>
チェックボックスはヘルパーメソッドを使って作成できる
ただし、下記のようにラベルの内側に配置する必要がある！
<pre class="toolbar:2 lang:ruby decode:true " ><%= f.label :remember_me, class: "checkbox inline" do %>
  <%= f.check_box :remember_me %>
  <span>Remember me on this computer</span>
<% end %></pre> 
これを反映したログインフォームのコード
<pre class="lang:ruby mark:(4-7) decode:true " title="/sample_app/app/views/sessions/new.html.erb" >・
・
・
      <%= f.label :remember_me, class: "checkbox inline" do %>
        <%= f.check_box :remember_me %>
        <span><%= t('.remember_me_checkbox') %></span>
      <% end %>

      <%= f.submit t('.login'), class: "btn btn-primary" %>
    <% end %>

    <p><%= t('.new_user?') %><%= link_to t('.signup'), signup_path %></p>
  </div>
</div></pre> 
スタイルも追加
<pre class="lang:ruby decode:true " title="/sample_app/app/assets/stylesheets/custom.scss" >・
・
・
/* forms */
・
・
・
.checkbox {
  margin-top: -10px;
  margin-bottom: 10px;
  span {
    margin-left: 20px;
    font-weight: normal;
  }
}

#session_remember_me {
  width: auto;
  margin-left: 0;
}</pre> 
<h4>実装はわずか1行</h4>
<blockquote class="quote-rort">チェックボックスがオンのときにユーザーを記憶し、オフのときには記憶しないようにします。信じられないかもしれませんが、必要な準備はすべて終わっているので、実装はわずか1行で終わります。</blockquote>
→ログインフォームから送信されたparamsハッシュにはすでにチェックボックスの値が含まれている
<pre class="toolbar:2 lang:default decode:true " >#以下の値はチェックボックスがオンの時に'1'になり、オフの時に'0'
params[:session][:remember_me]</pre> 
params[:session][:remember_me]の値に基づいてユーザーを記憶するか忘れるかを判断する
<pre class="toolbar:2 lang:ruby decode:true " >#params[:session][:remember_me]が１(オン）の時
if params[:session][:remember_me] == '1'
  #userを記憶
  remember(user)
#そうでなければ
else
  #userを忘れる
  forget(user)
end

＃三項演算子を使うことで上記のコードをさらに短く↓
params[:session][:remember_me] == '1' ? remember(user) : forget(user)
</pre> 
コントローラーに反映させると↓こう
<pre class="lang:ruby mark:(11-12) decode:true " title="/sample_app/app/controllers/sessions_controller.rb" >class SessionsController < ApplicationController

  def new
  end

  def create
    user = User.find_by(email: params[:session][:email].downcase)
    if user && user.authenticate(params[:session][:password])
      #session[:user_id] = user　と言う事
      log_in user
      #params[:session][:remember_me]が1の時userを記憶　そうでなければuserを忘れる
      params[:session][:remember_me] == '1' ? remember(user) : forget(user)
      #user_url(user)　という名前付きルートになる
      redirect_to user
    else
      flash.now[:danger] = t('.login_error')
      render 'new'
    end
  end
・
・
・</pre> 
<h4>三項演算子とは何ぞ</h4>
<pre class="toolbar:2 lang:default decode:true " >if boolean?
  何かをする
else
  別のことをする
end

↑これを↓こうする

論理値? ? 何かをする : 別のことをする</pre> 
3つの部分から構成される演算子ということ
三項演算子についての<a href="https://railstutorial.jp/chapters/advanced_login?version=5.1#aside-ternary_operator" rel="noopener noreferrer" target="_blank">RoRT本文のコラム</a>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>ブラウザでcookies情報を調べ、[remember me] をチェックしたときに意図した結果になっているかどうかを確認してみましょう。</li>
	<li>コンソールを開き、三項演算子を使った実例を考えてみてください</li>
</ol>
</blockquote>
１．〇（チェックを入れた時はuser_idとremember_tokenがcookieに保存されている）
<pre class="lang:ruby decode:true " title="2." >>> def come_home(onaka)
>>   onaka == "hungry" ? "ごはんにする" : "おふろにする"
>> end
=> :come_home
>> come_home("full")
=> "おふろにする"
>> come_home("hungry")
=> "ごはんにする"</pre> 
<h3>まとめとか感想</h3>
ちょっと短いけど本文に倣ってこれにて。

<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

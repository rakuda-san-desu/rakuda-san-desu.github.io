---
layout: post
title: "Rails｜Railsチュートリアル10.1.1~10.5.1｜10.2.3で詰まる"
date: "2019-03-18 18:18:42"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ", "未分類"]
tags: ["Railsチュートリアル", "参考サイト"]
published: true
wordpress_id: 374
wordpress_status: "publish"
permalink: "/rails-railstutorial1011-1051/"
---
<h2>流されるまま進む</h2>
<a href="https://rakuda3desu.net/rails-railstutorial211-241/">前回からの続き</a>

その後、大きくハマることもなく
かと言ってしっかり理解しながら進めいてる訳でもなく←
テスト部分に至ってはほぼｸﾞｸﾞｯﾃｺﾋﾟﾍﾟなんだけども
どうにかココまでやってきました。

そして久々にドハマリましたん
<h2>テストがGREENにならない10.2.3</h2>
それは10.2.3を進めていた時の事です
下の方のリスト10.23の部分です
createアクションに『redirect_back_or user』追加
テストの結果は？
<strong>RED!</strong>
<h3>一先ずコピペ</h3>
「〇〇を追加」みたいな指示でも
そこだけコピペってもダメな事ってあったりしないですか？
と言う訳で（？）createアクションを丸々コピペってみました
しかしやはりRED
はてさて
<h3>エラー文を見てみる</h3>
ERROR["test_login_with_remembering", UsersLoginTest, 0.7639187360000506]
test_login_with_remembering#UsersLoginTest (0.76s)
<strong>NoMethodError: NoMethodError: undefined method `remember_token' for nil:NilClass</strong>
test/integration/<strong>users_login_test.rb:33:in</strong> `block in '
33/33: [===========================================] 100% Time: 00:00:00, Time: 00:00:00
Finished in 0.83569s
33 tests, 82 assertions, 0 failures, 1 errors, 0 skips

remember_tokenメソッドがない事と
users_login_test.rbの33行目にあたる部分がおかしいらしいと言う事は分かった
しかし該当箇所を見てもなんだかよくわからないのであった
<h3>無理せずググる</h3>
完全自力解決が好ましいけどﾑﾘｼﾅｲ!!方針なので早速ググります

そしてこちらの回答を読んでいって解決しました
[blogcard url="https://teratail.com/questions/141284"]

ここ、ちょっと前に変数userに「＠」を付けるように編集していたと思うんだけど
10.32のリストの内容では「＠」が消えてるんですね。
そのせいで「@userを読み込みたいのに@userがないじゃん！」で、エラーになっていた。と。

直した結果こうなって

def create
@user = User.find_by(email: params[:session][:email].downcase)
if @user && @user.authenticate(params[:session][:password])
log_in @user
params[:session][:remember_me] == '1' ? remember(@user) : forget(@user)
redirect_back_or @user
else
flash.now[:danger] = 'Invalid email/password combination'
render 'new'
end
end

テストもGREENになりました
<h2>最近引っかからなくなってきたコト</h2>
ちょっと前はイチイチ「？？」ってなってたんだけど<br>
最近引っかからなくなってきたことがあります。

<strong>Gemを追加したらRails serverを再起動させる！</strong>

大げさに言う程の事ではないですね💧
なんだけど、初心者？初学者は結構引っ掛かりやすい罠なんじゃないかなぁ？
私だけかな🐫ﾃﾍ

---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜番外編・I18n"
date: "2019-11-03 18:29:53"
categories: ["Ruby on Rails", "学習メモ"]
tags: ["らくだにも出来るRailsチュートリアル番外編", "参考サイト"]
published: true
wordpress_id: 989
wordpress_status: "publish"
permalink: "/out-of-rakudas-rails-tutorial-i18n/"
---
番外編では本編からちょっと外れた話なんかを書いていきます
<h2>エラーメッセージを日本語化したい！</h2>
だって英語で言われてもわからないじゃん？（？
なので
本文でバリデーションエラーのメッセージ云々が出てきたところで
チャレンジしていきたいと思います。

作業用に、現状から新しくブランチ（i18n）を切ります
（add-i18nとかのが良かったかもね？）
（生やす場所ももっと考えた方が良かったかもね？）
（masterから生やすとかね）

RailsガイドのRails 国際化 (i18n) APIのページ↓
[blogcard url="https://railsguides.jp/i18n.html#ruby-on-railsにおけるi18nのしくみ"]
正直何をどうしたらいいのかわからないので参考ページを探します。
[blogcard url="https://qiita.com/Ushinji/items/242bfba84df7a5a67d5b"]
一先ずこちらを参考に作業していきますよ

<h3>i18nを導入</h3>
Gemを導入
<pre class="lang:r mark:(7-7) decode:true " title="/sample_app/Gemfile" >source 'https://rubygems.org'

（中略）
gem 'turbolinks',   '5.0.1'
gem 'jbuilder',     '2.7.0'
gem "nokogiri", ">= 1.10.4"
gem 'rails-i18n'
・
・
・</pre> 
ターミナル$ bundle install</pre>
設定する
<pre class="lang:ruby mark:(12-12) decode:true " title="/sample_app/config/application.rb" >・
・
・
module SampleApp
  class Application < Rails::Application
    # Initialize configuration defaults for originally generated Rails version.
    config.load_defaults 5.1

    # Settings in config/environments/* take precedence over those specified here.
    # Application configuration should go into files in config/initializers
    # -- all .rb files in that directory are automatically loaded.
    config.i18n.default_locale = :ja
  end
end</pre> 
→言語設定（default_local）を日本語に（:ja）をする
<a href="https://rakuda3desu.net/wp-content/uploads/2019/10/i18n-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/10/i18n-1.png" alt="" width="468" height="225" class="alignnone size-full wp-image-992" /></a>
おおー✨
日本語になってる！（まだ作業は続きます）
<h4>ロケールファイルとは何ぞ</h4>
[blogcard url="http://e-words.jp/w/%E3%83%AD%E3%82%B1%E3%83%BC%E3%83%AB.html"]
言語の設定ファイルと言う事かな
本来は各言語の設定ファイル（ロケールファイル）をDLしてきて導入しないとだけど
Gem i18nを導入することで標準的な設定であれば別途用意しなくても使えるようになる
的な？
<h4>そもそもI18nとは何ぞ</h4>
国際化・多言語化を意味するinternationalizationを短縮したもの
RubyとかRailsに限った言葉では無い（いろんな言語にある）
日本語にするためのものと思い込んでた節があるんだけど
そうではなくて　国際化→その中の一つとして日本のフォーマットに合わせる　の方が合ってる感
そして他にも種類があると教えていただきました✨
[blogcard url="https://trans-notes.net/whylocalize"]
奥が深い・・・
<h3>カラム名を日本語化する</h3>
modelの属性名はそのままでは変換されないので
日本語変換用のファイルを作って設定してあげる必要がある
<pre class="lang:default decode:true " title="ターミナル" >#config/locales内にmodelsディレクトリを作る
$ mkdir config/locales/models
#config/locales/models内にja.ymlファイルを作る（model用の日本語変換設定ファイル）
$ touch config/locales/models/ja.yml</pre>
<pre class="lang:yaml decode:true " title="/sample_app/config/locales/models/ja.yml" >ja:
  activerecord:
    #設定したいモデル名を指定
    models:
      user: ユーザー
    #属性名を指定
    attributes:
      #設定したいモデル名
      user:
        #カラム名と表示したい日本語
        name: 名前
        email: メールアドレス
        password_digest: パスワード　#←後で修正します</pre> 
<h4>ファイルの読み込み設定</h4>
config/application.rbにファイル読み込みのコードを追加
<pre class="lang:ruby mark:(5-5) decode:true " title="/sample_app/config/application.rb" >・
・
・
    config.i18n.default_locale = :ja
    config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '**', '*.yml').to_s]
  end
end</pre> 
config.i18n.load_path ……　読み込むファイルのパスを設定する
+=　……　演算子（設定に追加の意）
Dir[]　……　パターンに合うファイル名を文字列の配列にして返す[ファイル名のパターン]
Rails.root.join('config', 'locales', '**', '*.yml').to_s　……　Railsアプリのルートdir/config/locales/どんなフォルダ名も含むしあってもなくてもいい/どんな名前も含む.yml
つまりこう→config.i18n.load_path = config.i18n.load_path+[ファイルまでのアドレスを文字列にした配列]
これによりconfig/locales以下のymlファイル全てが読み込まれるようになる！
<a href="https://rakuda3desu.net/wp-content/uploads/2019/10/i18n-2.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/10/i18n-2.png" alt="" width="554" height="238" class="alignnone size-full wp-image-997" /></a>
<h4>password部分の指定</h4>
DBの属性名がpassword_digestだったから何の疑いもなくpassword_digestにしちゃったけど
validationのエラーなのでDBに書き込む前だね？と言う事で
属性名の指定をpassword_digest: →　password:に変更したらOKでした！
<h4>ここも日本語化したいの部分</h4>
最初は、単純に文字表示の部分はそのまま日本語でもいいんじゃね？の気持ちから下記の様に
<pre class="lang:ruby mark:(4-4) decode:true " title="/sample_app/app/views/shared/_error_messages.html.erb" ><% if @user.errors.any? %>
  <div id="error_explanation">
    <div class="alert alert-danger">
      フォームには<%= pluralize(@user.errors.count, "個のエラーがあります") %>.
    </div>
    <ul>
    <% @user.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %></pre> 
更に気になる部分・・・
<a href="https://rakuda3desu.net/wp-content/uploads/2019/10/i18n-3.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/10/i18n-3.png" alt="" width="757" height="747" class="alignnone size-full wp-image-999" /></a>
<h4>使い分けを考える</h4>
上で一回書いたけど
単純に文字表示の部分はそのまま日本語でもいいんじゃね？の気持ちで進めようと思ったのだけど
上部の部分もI18nで日本語化できるよ！と、教えてもらいまして
じゃぁ練習もかねて、って事で
今回は日本語化したい部分はマルっとI18nを使って日本語化することにしました。
<h3>ここも日本語化したいの部分再び</h3>
と、言う訳でいったんコードを元に戻しまして作業していきます。
まずは簡単そうなところから。
<h4>confirmation部分</h4>
<pre class="lang:yaml decode:true " title="/sample_app/config/locales/models/ja.yml
" >ja:
  activerecord:
    models:
      user: ユーザー
    attributes:
      user:
        name: 名前
        email: メールアドレス
        password: パスワード
        <span class="point">password_confirmation: パスワード（確認用）</span></pre>
と、言う訳でmodelsのja.ymlファイルを編集しました。
が、表示に変化なし。
はて？と思って見直したらviewファイルに答えがありました！
<pre class="lang:ruby mark:(7-7) decode:true " title="/sample_app/app/views/users/new.html.erb" >・
・
・
      <%= f.label :password %>
      <%= f.password_field :password, class: 'form-control' %>

      <%= f.label :password_confirmation, "Confirmation" %>
      <%= f.password_field :password_confirmation, class: 'form-control' %>

      <%= f.submit "Create my account", class: "btn btn-primary" %>
    <% end %>
  </div>
</div></pre> 
ハイライト部分、第2引数で表示する文字列を指定しているのでした
なのでその部分を削除
→希望の通りに表示されました✨
<h4>view用のファイルを作る準備</h4>
こちらを参考にフォルダとファイルを配置
[blogcard url="https://qiita.com/shi-ma-da/items/7e5c3d75c9a9f51abdd5"]
<a href="https://rakuda3desu.net/wp-content/uploads/2019/11/i18n-4.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/11/i18n-4.png" alt="" width="151" height="205" class="alignnone size-full wp-image-1006" /></a>
こんな感じになりました
<h4>ボタン部分</h4>
フォームのデータを送信するためのボタン部分（青いところ）
<pre class="lang:yaml decode:true " title="/sample_app/config/locales/views/users/ja.yml
" >ja:
  #views/usersを指定している
  users:
    #views/users/new.html.erbを指定している
    new:
      Create my account: 'アカウントを作る！'</pre>
<pre class="lang:ruby mark:(11-11) decode:true " title="/sample_app/app/views/users/new.html.erb" >・
・
・
      <%= f.label :password_confirmation %>
      <%= f.password_field :password_confirmation, class: 'form-control' %>

      #modelと違ってコードを書き換える必要がある
      #t(引数)の形で呼び出す　引数にはja.ymlで設定したキーが入る
      #「.Create my account」は「user.new.Create my account」の略
      #()も省略できるっぽいけど、省略したらCSSが崩れたので付けた。ので、付けるほうに統一
      <%= f.submit t('.Create my account'), class: "btn btn-primary" %>
    <% end %>
  </div>
</div></pre> 
tって何？！ってなったんだけど
translate（翻訳する）の頭文字のtでした！
翻訳するためのメソッドって事ですね。
[blogcard url="https://qiita.com/tiktak/items/a70ef7940fa4710f37cb"]
<h4>エラー数の表示の部分</h4>
この部分はnew.html.erbではなくエラー文用のパーシャルを編集するんだよ！（ちょっと探した）
<pre class="lang:yaml decode:true " title="/sample_app/config/locales/views/shared/ja.yml
" >ja:
  shared:
    #フォルダ名の頭の「_」は取る
    error_messages:
      #元のコードをマルっとキーにしようと思ってたけど長すぎて変なのでやめた
      #変数を使う事で動的な表示にも対応
      errors count: 'フォームに%{errors_count}個のエラーがあります'</pre>
<pre class="lang:ruby mark:(5-5) decode:true " title="/sample_app/app/views/shared/_error_messages.html.erb" ><% if @user.errors.any? %>
  <div id="error_explanation">
    <div class="alert alert-danger">
      #第一引数に表示したい部分のキー　第二引数に変数名と値をハッシュで
      <%= t('.errors count', errors_count: @user.errors.count) %>
・
・
・</pre> 
<a href="https://rakuda3desu.net/wp-content/uploads/2019/11/i18n-5.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/11/i18n-5.png" alt="" width="457" height="504" class="alignnone size-full wp-image-1007" /></a>
できたっぽい！
<h3>ついでにやってみよう</h3>
日本語に直したいところを設定していきます
（これから）
（英語表記の方が雰囲気良くない？なところはそのままにしています）
<h4>時刻を日本時間に</h4>
コンソールのデータを見ていてタイムゾーンが違っているのがずっと気になっていたので設定します
[blogcard url="https://qiita.com/shi-ma-da/items/7e5c3d75c9a9f51abdd5"]
<pre class="lang:ruby mark:（5-5,7-7） decode:true " title="/sample_app/config/application.rb" >・
・
・
    #ActiveRecord（created_atなど）に影響する
    config.time_zone = 'Tokyo'
    #DBに読み書きされる時刻に影響する
    config.active_record.default_timezone = :local
    config.i18n.default_locale = :ja
    config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '**', '*.yml').to_s]
  end
end</pre> 
[blogcard url="https://qiita.com/aosho235/items/a31b895ce46ee5d3b444"]
で、コンソールでデータを作ってみたところ、時刻表示が変わってないな？
<h4>cloud9の時刻を日本時間に</h4>
もしかしてcloud9でやってるから？
と思ったらそうっぽい。
下記ページを参考に時間を競ってします。
これ多分、一番最初にやった方がいいやつだよね。
[blogcard url="https://dot-blog.jp/news/cloud9-linux-changing-timezone/"]
出来ました！
<h3>まとめとか感想</h3>
時刻表示がcloud9の影響と言うのは盲点でした！
後は、今後も必要そうな部分を随時日本語にしていきたいと思います🐫
国際化という部分で考えたら、英語表示用のymlファイルも作らないとじゃね？って思うんだけど
そこには気が付いているよって事で良しとしたい！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

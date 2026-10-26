---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜13.4（と13.5）"
date: "2020-06-07 16:34:55"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1515
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial13-4-13-5/"
---
※毎回書いたほうがいい気がするので書いておきます※
平文の部分をちょこちょこI18nで日本語化しているのですが
その部分に関しては特に記述していなくてもたまにコードが違ったりしています。
t('.hogehoge')って部分です
どうぞお気になさらず。
<h2>13.4 マイクロポストの画像投稿</h2>
マイクロポストの基本的な操作は実装できたが
応用として画像付きのマイクロポストを投稿できるようにしていく。

開発環境用のβ版を実装
↓
改善
↓
本番環境用の完成版を実装
<h4>.gitignoreの編集</h4>
開発環境でアップロードされた画像がgitにpushされないように.gitignoreを編集しておく
（本文では最後の方に出てくるけど、途中でもコミットするのであれば設定しておく🐫） 
<pre class="lang:default decode:true " title="/sample_app/.gitignore" >.
.
.
# アップロードされたテスト画像を無視する
/public/uploads</pre> 
※隠しファイルになっているので注意
<h4>画像アップロード機能を追加するために</h4>
アップロードするためのフォームと投稿された画像本体が必要！
[Upload image] ボタンと画像付きマイクロポストの見本は<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#fig-micropost_image_mockup" rel="noopener noreferrer" target="_blank">RoRT本文</a>参照
<h3>13.4.1 基本的な画像アップロード</h3>
<a href="https://github.com/carrierwaveuploader/carrierwave" rel="noopener noreferrer" target="_blank">CarrierWave</a>という画像アップローダーを使って実装していく
<blockquote class="quote-rort">まずはcarrierwave gemをGemfileに追加しましょう (リスト 13.58)15 。このとき、リスト 13.58ではmini_magick gemとfog gemsも含めている点に注目してください。これらのgemは画像をリサイズしたり (13.4.3)、本番環境で画像をアップロードする (13.4.4) ために使います。</blockquote>
<pre class="lang:default mark:(6-7,15-15） decode:true " title="/sample_app/Gemfile" >source 'https://rubygems.org'

gem 'rails',                   '5.1.6'
gem 'bcrypt',                  '3.1.12'
gem 'faker',                   '1.7.3'
gem 'carrierwave',             '1.2.2'
gem 'mini_magick',             '4.7.0'
gem 'will_paginate',           '3.1.5'
gem 'bootstrap-will_paginate', '1.0.0'
.
.
.
group :production do
  gem 'pg',  '0.20.0'
  gem 'fog', '1.42'
  end
.
.
.</pre> 
そして
<pre class="lang:default decode:true " title="ターミナル" >$ bundle install</pre> 
CarrierWaveを導入すると、Railsのジェネレーターで画像のアップローダーが生成できるようになる。
スゴイ！
<pre class="lang:default decode:true " title="ターミナル" ># Pictureという名前の画像アップローダーを作る
$ rails generate uploader Picture</pre> 
<h4>Active Recordモデルの属性と関連付け</h4>
CarrierWaveでアップロードされた画像はActive Recordモデルの属性と関連付けされているべき！
→関連付けされる属性には画像のファイル名が格納される→ファイル名なので型はString型にしておく

picture属性を追加したマイクロポストのデータモデルはこちらの→<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#fig-micropost_model_picture" rel="noopener noreferrer" target="_blank">RoRT本文</a>
picture属性をMicropostモデルに追加する 
<pre class="lang:default decode:true " title="ターミナル" ># マイグレーションファイルを生成
$ rails generate migration add_picture_to_microposts picture:string
# 開発環境のDBに適用
$ rails db:migrate</pre> 
<blockquote class="quote-rort">CarrierWaveに画像と関連付けたモデルを伝えるためには、mount_uploaderというメソッドを使います。このメソッドは、引数に属性名のシンボルと生成されたアップローダーのクラス名を取ります。</blockquote>
Micropostモデルにアップローダーを追加 
<pre class="lang:ruby mark:(6-7) decode:true " title="/sample_app/app/models/micropost.rb" >class Micropost < ApplicationRecord
  # MicropostとそのUserは belongs_to (1対1) の関係性がある
  belongs_to :user
  # default_scope（順序を指定するメソッド） created_at:を降順にする
  default_scope -> { order(created_at: :desc) }
  # mount_uploader（CarrierWaveへ画像と関連付けたモデルを伝えるメソッド）画像のファイル名の格納先の属性名、,アップローダーのクラス名
  mount_uploader :picture, PictureUploader
  # user_idが存在する
  validates :user_id, presence: true
  # contentが存在する　長さは最大140文字
  validates :content, presence: true, length: { maximum: 140 }
end
</pre> 
アップローダーのクラス名は自動生成されたpicture_uploader.rbで定義されている
<pre class="lang:ruby mark:(1-1) decode:true " title="/sample_app/app/uploaders/picture_uploader.rb" >class PictureUploader < CarrierWave::Uploader::Base
  # Include RMagick or MiniMagick support:
  # include CarrierWave::RMagick
  # include CarrierWave::MiniMagick
・
・
・</pre> 
<h4>viewにアップローダーを追加</h4>
Homeページにアップローダーを追加→_micropost_form.html.erbを編集 
<pre class="lang:default mark:(8-10) decode:true " title="/sample_app/app/views/shared/_micropost_form.html.erb" ><!--マイクロポスト投稿フォーム-->
<%= form_for(@micropost) do |f| %>
  <%= render 'shared/error_messages', object: f.object %>
  <div class="field">
    <%= f.text_area :content, placeholder: t('.content_msg') %>
  </div>
  <%= f.submit t('.post_btn'), class: "btn btn-primary" %>
  <span class="picture">
    <%= f.file_field :picture %>
  </span>
<% end %></pre> 
また、Webから更新できる許可リストにpicture属性を追加する 
<pre class="lang:ruby mark:(8-8) decode:true " title="/sample_app/app/controllers/microposts_controller.rb" >・
・
・
  private

    def micropost_params
      # micropost属性必須　content属性:pictureと属性のみ変更を許可
      params.require(:micropost).permit(:content, :picture)
    end
・
・
・</pre> 
<h4>アップロードされた画像を表示できるようにする</h4>
<blockquote class="quote-rort">一度画像がアップロードされれば、Micropostパーシャルのimage_tagヘルパーでその画像を描画できるようになります (リスト 13.62)。また、画像の無い (テキストのみの) マイクロポストでは画像を表示させないようにするために、picture?という論理値を返すメソッドを使っている点に注目してください。</blockquote>
「picture?」は画像用の属性名に応じて（今回はpicture）CarrierWaveが自動的に生成してくれます 
<pre class="lang:default mark:(8-9) decode:true " title="/sample_app/app/views/microposts/_micropost.html.erb" ><!--CSSのidにマイクロポストのidを割り振っている　一般的に良いとされる慣習との事-->
<li id="micropost-<%= micropost.id %>">
  <!--gravatarからユーザの画像を呼び出して表示＆ユーザー詳細へリンク-->
  <%= link_to gravatar_for(micropost.user, size: 50), micropost.user %>
  <span class="user"><%= link_to micropost.user.name, micropost.user %></span>
  <span class="content">
    <%= micropost.content %>
    <!--pictureがtrueの時に画像を表示-->
    <%= image_tag micropost.picture.url if micropost.picture? %>
    </span>
・
・
・</pre> 
<h4>image_tagヘルパーとは何ぞ</h4>
image_tagヘルパーは画像を表示するためのヘルパーメソッド
<blockquote>HTML imgタグを返します。画像へのフルパス、またはapp/assets/imagesディレクトリ内にあるファイルを引数として与えられます。</blockquote>
[blogcard url="https://railsguides.jp/action_view_overview.html#image-tag"]
[blogcard url="https://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#method-i-image_tag"]
image_tag micropost.picture.url　→生成されるHTML→　<img src="画像のソース" alt="ファイル名が入る">
第2引数でオプションを指定することも可能（詳しくは上記リンクなどで勉強しましょう）
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>画像付きのマイクロポストを投稿してみましょう。もしかして、大きすぎる画像が表示されてしまいましたか? (心配しないでください、次の13.4.3でこの問題を直します)。</li>
	<li>リスト 13.63に示すテンプレートを参考に、13.4で実装した画像アップローダーをテストしてください。テストの準備として、まずはサンプル画像をfixtureディレクトリに追加してください (コマンド例: cp app/assets/images/rails.png test/fixtures/)。リスト 13.63で追加したテストでは、Homeページにあるファイルアップロードと、投稿に成功した時に画像が表示されているかどうかをチェックしています。なお、テスト内にあるfixture_file_uploadというメソッドは、fixtureで定義されたファイルをアップロードする特別なメソッドです18 。ヒント: picture属性が有効かどうかを確かめるときは、11.3.3で紹介したassignsメソッドを使ってください。このメソッドを使うと、投稿に成功した後にcreateアクション内のマイクロポストにアクセスするようになります。</li>
</ol>
</blockquote>
1.
動作確認のみにて省略
2.
※本文と若干違う部分があるけど手元のコード重視で 
<pre class="lang:ruby mark:(11-12,22-23,26-29,31-32) decode:true " title="/sample_app/test/integration/microposts_interface_test.rb" >・
・
・
  test "micropost interface" do
    # @userでログイン
    log_in_as(@user)
    # root_pathにgetのリクエスト
    get root_path
    # 特定のHTMLタグが存在する→ class = "pagination"を持つdiv
    assert_select 'div.pagination'
    # 特定のHTMLタグが存在する→ type="file"を持つinput
    assert_select 'input[type="file"]'
    # ブロックで渡されたものを呼び出す前後でMicropost.countに違いがない
    assert_no_difference 'Micropost.count' do
      # microposts_pathにpostのリクエスト　→　micropost: { content: "" }（無効なデータ）
      post microposts_path, params: { micropost: { content: "" } }
    end
    # 特定のHTMLタグが存在する→　id = "error_explanation"を持つdiv
    assert_select 'div#error_explanation'
    # contentに代入　→　"This micropost really ties the room together"
    content = "This micropost really ties the room together"
    # pictureに代入→ fixtureで定義されたファイルをアップロードするメソッド（パス, タイプ）
    picture = fixture_file_upload('test/fixtures/rails.png', 'image/png')
    # ブロックで渡されたものを呼び出す前後でMicropost.countが+1
    assert_difference 'Micropost.count', 1 do
      # microposts_pathにpostのリクエスト　→　micropost: { content: content, picture: picture }（有効なデータ）
      post microposts_path, params: { micropost: 
                                { content: content,
                                  picture: picture } }
    end
    # @micropostにpictureが含まれる
    assert assigns(:micropost).picture?
    # root_urlにリダイレクト
    assert_redirected_to root_url
    #　指定されたリダイレクト先に移動
    follow_redirect!
・
・
・</pre> 
<h3>13.4.2 画像の検証</h3>
現状では画像サイズに対する制限がないので大きな画像も上げ放題！
また、フォーマットの指定もしていないので
大きなサイズや謎のファイルも上げ放題で危ない！
ので、バリデーションを設定する→サーバー用とクライアント用両方に追加する！！
<h4>有効な画像の種類を制限</h4>
<blockquote class="quote-rort">生成されたアップローダーの中にコメントアウトされたコードがありますが、ここのコメントアウトを取り消すことで、画像のファイル名から有効な拡張子 (PNG/GIF/JPEGなど) を検証することができます</blockquote>
本文だとstore_dirメソッドのすぐ下にある感じだけど
🐫の環境では36行目～でした 
<pre class="lang:ruby mark:(6-8) decode:true " title="/sample_app/app/uploaders/picture_uploader.rb" >・
・
・
  # Add a white list of extensions which are allowed to be uploaded.
  # For images you might use something like this:
  def extension_whitelist
    %w(jpg jpeg gif png)
  end
・
・
・</pre> 
<h4>画像サイズの制御</h4>
Micropostモデルに定義していく
画像サイズに関するバリデーションはpresenceやlengthなどと違ってRailsの既存オプションにはないので
手動で「picture_size」という独自のバリデーションを定義していく 
<pre class="lang:ruby mark:(5-7,9-9,11-16) decode:true " title="/sample_app/app/models/micropost.rb" >class Micropost < ApplicationRecord
・
・
・
  # 独自のバリデーションを定義するためvalidatesではなくvalidateメソッドを使っている
  # 引数にシンボルを取り、シンボル名に対応したメソッド（picture_size）を呼び出す
  validate  :picture_size

  private

    # アップロードされた画像のサイズをバリデーションする
    def picture_size
      if picture.size > 5.megabytes
        errors.add(:picture, "should be less than 5MB")
      end
    end
end</pre> 
<h4>バリデーションをViewに組み込む</h4> 
<pre class="lang:default mark:(10-10,14-22) decode:true " title="/sample_app/app/views/shared/_micropost_form.html.erb" ><!--マイクロポスト投稿フォーム-->
<%= form_for(@micropost) do |f| %>
  <%= render 'shared/error_messages', object: f.object %>
  <div class="field">
    <%= f.text_area :content, placeholder: t('.content_msg') %>
  </div>
  <%= f.submit t('.post_btn'), class: "btn btn-primary" %>
  <span class="picture">
    <!--acceptパラメータを使って許可したファイル形式をMIMEタイプで指定-->
    <%= f.file_field :picture, accept: 'image/jpeg,image/gif,image/png' %>
  </span>
<% end %>

<!--jQueryでファイル合図の警告を出す-->
<script type="text/javascript">
  $('#micropost_picture').bind('change', function() {
    var size_in_megabytes = this.files[0].size/1024/1024;
    if (size_in_megabytes > 5) {
      alert('Maximum file size is 5MB. Please choose a smaller file.');
    }
  });
</script></pre> 
上記の10行目acceptパラメーターは、送信できる（app/uploaders/picture_uploader.rbで許可した）ファイル形式をMIMEタイプで指定している
<h4>MIMEタイプとは何ぞ</h4>
[blogcard url="https://ja.wikipedia.org/wiki/Multipurpose_Internet_Mail_Extensions#Content-Type"]
大分類/詳細な形式
→image/png（imageファイル/png形式）、text/html（textファイル/html形式）とか
<h4>アラート用のjQueryのコード</h4>
<pre class="toolbar:2 lang:default decode:true " ><script type="text/javascript">
  // id = "micropost_picture"を持つ要素の値が変更された時に実行
  $('#micropost_picture').bind('change', function() {
    // size_in_megabytesに代入→このファイルのサイズを取得してMBに換算（size/1024/1024）
    var size_in_megabytes = this.files[0].size/1024/1024;
    // size_in_megabytesが5より大きい時
    if (size_in_megabytes > 5) {
      // アラートを出す
      alert('Maximum file size is 5MB. Please choose a smaller file.');
    }
  });
</script></pre> 
ただしこのコードでは規定サイズ以上のファイルのアップロードは阻止できない
→メッセージを無視してアップロードを強行することも可能
<blockquote class="quote-rort">本書はRailsチュートリアルなので、今回は「リスト 13.66のようなコードでは実装はまだ不完全である」という点だけ覚えておけば十分です。</blockquote>
今回Micropostモデルで設定したように、サーバー側でのバリデーションが重要！
<h4>演習</h4>
<blockquote class="quote-rort"><ol>
	<li>5MB以上の画像ファイルを送信しようとした場合、どうなりますか?</li>
	<li>無効な拡張子のファイルを送信しようとした場合、どうなりますか?
13.4.3 </li>
</ol>
</blockquote>
<ol>
        <li>
<ul>
        <li>ファイル選択時Maximum file size is 5MB. Please choose a smaller file.のダイアログが出る</li>
	<li>気にせず投稿ボタンを押すとバリデーションエラーでメッセージが出る</li>
</ul>
	</li>
	<li>
<ul>
	<li>そもそも投稿ファイルは指定の拡張子から選ぶようになってるので無効な拡張子を選ぶことはなかなかない</li>
	<li>「すべてのファイル」から選んで送信ボタンを押すとバリデーションエラーでメッセージが出る</li>
</ul>
        </li>
</ol>
<h3>13.4.3 画像のリサイズ</h3>
ファイルサイズに対するバリデーションを設定したけど画像サイズ（縦横の長さ）に対する制限がないので
（縦横のサイズ的に）大きすぎる画像がアップロードされるとレイアウトが崩れてイヤンな気持ちになる
→適切なサイズにリサイズする
<blockquote class="quote-rort">他の解決策としてCSSで表示サイズを調整する方法もありますが、これだとファイルサイズが変わりません。結果として、ファイルサイズの大きな画像によって、読み込み時間が長くなるといった問題が発生します。</blockquote>
<h4>画像をリサイズするプログラムを導入</h4>
ImageMagickというプログラムを使うので、これを開発環境にインストールする
※環境によって手順が異なる！（ググろう）※Herokuではもともと導入されている
クラウドIDEでは下記 
<pre class="toolbar:2 lang:default decode:true " >$ sudo yum install -y ImageMagick</pre> 
<blockquote class="quote-rort">次に、MiniMagickというImageMagickとRubyを繋ぐgemを使って、画像をリサイズしてみましょう。</blockquote>
今回は最大幅を400pxに定める→縦横どちらかが400pxを超えていた場合に適切にリサイズ 
<pre class="lang:ruby mark:(4-7) decode:true " title="/sample_app/app/uploaders/picture_uploader.rb" >class PictureUploader < CarrierWave::Uploader::Base
  # Include RMagick or MiniMagick support:
  # include CarrierWave::RMagick
  # ↓この行のコメントアウトを外す
  include CarrierWave::MiniMagick
  # 縦横どちらかが400pxを超えていた場合、適切なサイズに縮小するオプション
  process resize_to_limit: [400, 400]
・
・
・</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>解像度の高い画像をアップロードし、リサイズされているかどうか確認してみましょう。画像が長方形だった場合、リサイズはうまく行われているでしょうか?</li>
	<li>既にリスト 13.63のテストを追加していた場合、この時点でテストスイートを走らせると紛らわしいエラーメッセージが表示されることがあります。このエラーを取り除いてみましょう。ヒント: リスト 13.68にある設定ファイルを修正し、テスト時はCarrierWaveに画像のリサイズをさせないようにしてみましょう。</li>
</ol>
</blockquote>
<ol>
	<li>縦横比を保ったままリサイズされる</li>
	<li>エラーにならなかったのでわかりません🤔</li>
</ol>
<h4>13.4.4 本番環境での画像アップロード</h4>
<blockquote class="quote-rort">13.4.3で実装した画像アップローダーは、開発環境で動かす分には問題ないのですが、本番環境には適していません。これはリスト 13.67のstorage :fileという行によって、ローカルのファイルシステムに画像を保存するようになっているからです</blockquote>
なので、本番環境でも使えるようにする
→本番環境ではクラウドストレージサービスに画像を保存→13.4.1で導入したfog gemを使うと簡単！ 
<pre class="lang:ruby mark:(9-14) decode:true " title="/sample_app/app/uploaders/picture_uploader.rb" >class PictureUploader < CarrierWave::Uploader::Base
  # Include RMagick or MiniMagick support:
  # include CarrierWave::RMagick
  # ↓この行のコメントアウトを外す
  include CarrierWave::MiniMagick
  # 縦横どちらかが400pxを超えていた場合、適切なサイズに縮小するオプション
  process resize_to_limit: [400, 400]
  
  # 本番環境かそれ以外かで保存先を切り替え
  if Rails.env.production?
    storage :fog
  else
    storage :file
  end
・
・
・</pre> 
<h4>環境のセッティング</h4>
RoRT<a href="https://railstutorial.jp/chapters/user_microposts?version=5.1#cha-13_footnote-ref-25" rel="noopener noreferrer" target="_blank">本文</a>に則ってAWSで色々する
/sample_app/config/initializers/carrier_wave.rbを作って（本来はファイルがあるものっぽい？）S3用に定義 
<pre class="lang:ruby decode:true " title="/sample_app/config/initializers/carrier_wave.rb" >if Rails.env.production?
  CarrierWave.configure do |config|
    config.fog_credentials = {
      # Amazon S3用の設定
      :provider              => 'AWS',
      :region                => ENV['S3_REGION'],     # 例: 'ap-northeast-1'
      :aws_access_key_id     => ENV['S3_ACCESS_KEY'],
      :aws_secret_access_key => ENV['S3_SECRET_KEY']
    }
    config.fog_directory     =  ENV['S3_BUCKET']
  end
end</pre> 
環境変数を設定 
<pre class="toolbar:2 lang:default decode:true " >$ heroku config:set S3_ACCESS_KEY="ココに先ほどメモしたAccessキーを入力"
$ heroku config:set S3_SECRET_KEY="同様に、Secretキーを入力"
$ heroku config:set S3_BUCKET="Bucketの名前を入力"
$ heroku config:set S3_REGION="Regionの名前を入力"</pre> 
いつものようにテストしてaddしてcommitしてpushしてぎとはぶでプルリクしてマージして戻ってきてpullる
そしてHerokuへデプロイる 
<pre class="toolbar:2 lang:default decode:true " >$ git push heroku
$ heroku pg:reset DATABASE
$ heroku run rails db:migrate
$ heroku run rails db:seed</pre> 
<h4>演習</h4>
<blockquote class="quote-rort"><ol>
	<li>本番環境で解像度の高い画像をアップロードし、適切にリサイズされているか確認してみましょう。長方形の画像であっても、適切にリサイズされていますか?</li>
</ol>
</blockquote>
<ol>
	<li>される！</li>
</ol>
<h2>13.5 最後に</h2>
まとめにつき割愛
<h3>まとめとか感想</h3>
本文より寄り道な部分に時間がかかった気がしますが<a href="https://newburukai.github.io/" rel="noopener noreferrer" target="_blank">コミュ</a>で助けてもらいつつ頑張りました！
さぁ!もう一息頑張ろうねええええ！！！！！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜12.3"
date: "2020-05-02 15:03:50"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル"]
published: true
wordpress_id: 1444
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial12-3/"
---
※毎回書いたほうがいい気がするので書いておきます※
平文の部分をちょこちょこI18nで日本語化しているのですが
その部分に関しては特に記述していなくてもたまにコードが違ったりしています。
t('.hogehoge')って部分です
どうぞお気になさらず。
<h2>12.3 パスワードを再設定する</h2>
passwordResetsコントローラのeditアクションを実装していく
テストは認証メールの時と同じで、統合テストで行っていく
<h3>12.3.1 editアクションで再設定</h3>
パスワード再設定メールに含まれるリンクを機能させるために、パスワード再設定フォームを表示するビューが必要
<blockquote class="quote-rort">このビューはユーザーの編集フォーム (リスト 10.2) と似ていますが、今回はパスワード入力フィールドと確認用フィールドだけで十分です。</blockquote>
<h4>パスワード再設定フォーム</h4>
前項でのパスワード再設定フォームとこの項で言うパスワード再設定フォームは別のもので
ここで言うパスワード再設定フォームは再設定を実行するためのフォーム
前項までのパスワード再設定フォームは再設定を申し込むためのフォーム
<h4>面倒な問題</h4>
メールアドレスをキーとしてユーザーを検索する為にeditアクションとupdateアクションの両方でメールアドレスが必要
<blockquote class="quote-rort">メールアドレス入りリンクのおかげで、editアクションでメールアドレスを取り出すことは問題ありません。しかしフォームを一度送信してしまうと、この情報は消えてしまいます</blockquote>
→再設定を実行するためのフォームにはパスワード入力フィールドと確認用フィールドしか作っていない為
→隠しフィールドを作ってメールアドレスを保存
→フォームの情報を送信するときにメールアドレスの情報も一緒に送信できる
<h4>異なるフォームタグヘルパー</h4>
今までの書き方はこう 
<pre class="toolbar:2 lang:default decode:true " >f.hidden_field :email, @user.email</pre> 
これだとparams[:user][:email] にメールアドレスが保存される
<blockquote>form_withやform_forで渡すインスタンスがある場合(もしくはそれらのヘルパーを使っている場合)は、f.hidden_fieldを使います。</blockquote>
今回はparams[:email]にメールアドレスを保存させる↓
<pre class="toolbar:2 lang:default decode:true " >hidden_field_tag :email, @user.email</pre> 
<blockquote>一個だけパラメータを他のアクションへ単体で渡したい時は、hidden_field_tagを使います。</blockquote>
引用元はこちら↓
[blogcard url="https://sakurawi.hateblo.jp/entry/hidden_field"]
本文読んでて「hidden_field初めて出てきたけどこれまでの書き方とは🤔」ってなったけど
「これまでの（フォームの）書き方」って事か。
<h4>PasswordResetsコントローラのeditアクション</h4>
params[:email]のメールアドレスに対応するユーザーを保存するための@user変数を定義する
続けて、params[:id]の再設定用トークンとauthenticated?メソッドを使って、このユーザーが正当なユーザーであることを確認する
正当なユーザー＝(ユーザーが存在する、有効化されている、認証済みである）
更にこれ↓
<blockquote class="quote-rort">editアクションとupdateアクションのどちらの場合も正当な@userが存在する必要があるので、いくつかのbeforeフィルタを使って@userの検索とバリデーションを行います </blockquote>
<pre class="lang:ruby decode:true " title="/sample_app/app/controllers/password_resets_controller.rb" >class PasswordResetsController < ApplicationController
  # フィルタの内容は下部private以下
  before_action :get_user,   only: [:edit, :update]
  before_action :valid_user, only: [:edit, :update]
・
・
・
  def edit
  end
  
    private

    # @userに代入　→params[:email]のメールアドレスに対応するユーザー
    def get_user
      @user = User.find_by(email: params[:email])
    end

    # 正しいユーザーかどうか確認する
    def valid_user
      # 条件がfalseの場合（@userが存在する　かつ　@userが有効化されている　かつ　@userが認証済である）
      unless (@user && @user.activated? &&
              @user.authenticated?(:reset, params[:id]))
        # root_urlにリダイレクト
        redirect_to root_url
      end
    end
end</pre> 
→再設定用のURLにアクセスすると再設定を実行するためのフォームが表示されるようになった！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>12.2.1.1で示した手順に従って、Railsサーバーのログから送信メールを探し出し、そこに記されているリンクを見つけてください。そのリンクをブラウザから表示してみて、図 12.11のように表示されるか確かめてみましょう。</li>
	<li>先ほど表示したページから、実際に新しいパスワードを送信してみましょう。どのような結果になるでしょうか?</li>
</ol>
</blockquote>
<ol>
	<li>表示される</li>
	<li>Unknown action
The action 'update' could not be found for PasswordResetsController
（updateアクションがないよなエラー）</li>
</ol>
<h3>12.3.2 パスワードを更新する</h3>
フォームからの送信に対応するupdateアクションが必要！
<blockquote class="quote-rort">
<ol>
	<li>パスワード再設定の有効期限が切れていないか</li>
	<li>無効なパスワードであれば失敗させる (失敗した理由も表示する)</li>
	<li>新しいパスワードが空文字列になっていないか (ユーザー情報の編集ではOKだった)</li>
	<li>新しいパスワードが正しければ、更新する</li>
</ol>
</blockquote>
<h4>パスワード再設定の有効期限が切れていないか</h4>
editとupdateアクションに期限切れかどうかを確認するメソッドとbeforeフィルターを用意する
<pre class="lang:ruby decode:true " title=" (1) への対応案" ># beforeフィルター
before_action :check_expiration, only: [:edit, :update]

# 有効期限をチェックするPrivateメソッドとしてcheck_expirationを定義
def check_expiration
  # password_reset_expired→期限切れかどうかを確認するインスタンスメソッド→詳しくは後程
  if @user.password_reset_expired?
    # 再設定の有効期限切れなflashメッセージ
    flash[:danger] = "Password reset has expired."
    # new_password_reset_urlにリダイレクト
    redirect_to new_password_reset_url
  end
end</pre> 
<h4>無効なパスワードは失敗、有効なパスワードは更新</h4>
有効期限が切れていないかをチェックするbeforeフィルターで保護したupdateアクションを使うことで2.4のケースに対応できそう
→パスワードが無効で更新に失敗する際はeditのビューが再描画される(flashメッセージも出す）
→パスワードが有効で更新に成功する際は、パスワードを再設定し、ログイン成功と同様の処理を進める（ユーザー詳細ページにリダイレクト、かな？）
<h4>新しいパスワードが空文字列になっていないか</h4>
Userモデルではパスワードは空でもよいという実装をしている
（<a href="https://railstutorial.jp/chapters/updating_and_deleting_users?version=5.1#code-allow_blank_password" title="Railsチュートリアル" rel="noopener noreferrer" target="_blank">Railsチュートリアル｜リスト10.13</a><a href="https://railstutorial.jp/chapters/updating_and_deleting_users?version=5.1#code-allow_blank_password" title="Railsチュートリアル" rel="noopener noreferrer" target="_blank">Railsチュートリアル｜リスト10.13</a>
しかし再設定ではパスワードのフィールドが空では再設定にならないので
明示的に（パスワードのフィールドが空であることを）キャッチするコードを追加する！
（確認フィールドが空の場合は、確認フィールドのバリデーションで検出されてエラーメッセージが表示される）
→@userオブジェクトにエラーメッセージを追加
<pre class="toolbar:2 lang:default decode:true " >@user.errors.add(:password, :blank)</pre> 
オブジェクト.errors.add(対象のカラム, 'エラーの内容')
エラーの内容にblankオプションを指定することでI18nで多言語している場合でも言語に合わせた適切なメッセージを表示してくれる
<h4>パスワード再設定のupdateアクション</h4>
上記をまとめるとパスワード再設定のupdateアクションが完成する
（ただし、password_reset_expired?の実装はまだなのでした）
<pre class="lang:ruby mark:(5-5,12-31,35-40,59-68) decode:true " title="/sample_app/app/controllers/password_resets_controller.rb" >class PasswordResetsController < ApplicationController
  # フィルタの内容は下部private以下
  before_action :get_user,   only: [:edit, :update]
  before_action :valid_user, only: [:edit, :update]
  before_action :check_expiration, only: [:edit, :update]
・
・
・
  def edit
  end
  
  def update
    # params[:user][:password]がemptyの場合
    if params[:user][:password].empty?
      # @user.errorsに:password, :blankを追加
      @user.errors.add(:password, :blank)
      # editのビューを描画
      render 'edit'
    # 指定された属性の検証がすべて成功した場合@userの更新と保存を続けて同時に行う
    elsif @user.update_attributes(user_params)
      # @userとしてログイン
      log_in @user
      # 成功のフラッシュメッセージを表示
      flash[:success] = "Password has been reset."
      # ユーザー詳細ページにリダイレクト
      redirect_to @user
    else
      # editのビューを描画
      render 'edit'
    end
  end

  
    private
    #:user必須
    #パスワード、パスワードの確認の属性をそれぞれ許可
    #それ以外は許可しない
    def user_params
      params.require(:user).permit(:password, :password_confirmation)
    end

    # beforeフィルタ
    
    # @userに代入　→params[:email]のメールアドレスに対応するユーザー
    def get_user
      @user = User.find_by(email: params[:email])
    end

    # 正しいユーザーかどうか確認する
    def valid_user
      # 条件がfalseの場合（@userが存在する　かつ　@userが有効化されている　かつ　@userが認証済である）
      unless (@user && @user.activated? &&
              @user.authenticated?(:reset, params[:id]))
        # root_urlにリダイレクト
        redirect_to root_url
      end
    end
    
    # トークンが期限切れかどうか確認する
    def check_expiration
      # password_reset_expired→期限切れかどうかを確認するインスタンスメソッド→詳しくは後程
      if @user.password_reset_expired?
        # 再設定の有効期限切れなflashメッセージ
        flash[:danger] = "Password reset has expired."
        # new_password_reset_urlにリダイレクト
        redirect_to new_password_reset_url
      end
    end
end</pre> 
user_paramsメソッドを使ってpasswordとpassword_confirmation属性を精査している
<h4>password_reset_expired?の実装</h4>
<blockquote class="quote-rort">今回は先回りして、始めからUserモデルに移譲する前提で次のようにコードを書いていました。</blockquote>
と、いう事なのでpassword_reset_expired?メソッドをUserモデルに定義していく
→2時間以上パスワードが再設定されなかった場合は期限切れになる処理
これをRubyで書くとこうなる
<pre class="toolbar:2 lang:ruby decode:true " >reset_sent_at < 2.hours.ago</pre> 
「<」の記号は「～より少ない」ではなく「〜より早い時刻」と読む 
<pre class="lang:ruby decode:true " title="/sample_app/app/models/user.rb" >・
・
・
  # パスワード再設定の期限が切れている場合はtrueを返す
  def password_reset_expired?
    # reset_sent_atの値（再設定メールの送信時刻）　右辺より早い時刻　2時間前
    reset_sent_at < 2.hours.ago
  end

    private
・
・
・</pre> 
→updateアクションが動作する！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>12.2.1.1で得られたリンク (Railsサーバーのログから取得) をブラウザで表示し、passwordとconfirmationの文字列をわざと間違えて送信してみましょう。どんなエラーメッセージが表示されるでしょうか?</li>
	<li>コンソールに移り、パスワード再設定を送信したユーザーオブジェクトを見つけてください。見つかったら、そのオブジェクトのpassword_digestの値を取得してみましょう。次に、パスワード再設定フォームから有効なパスワードを入力し、送信してみましょう (図 12.13)。パスワードの再設定は成功したら、再度password_digestの値を取得し、先ほど取得した値と異なっていることを確認してみましょう。ヒント: 新しい値はuser.reloadを通して取得する必要があります。</li>
</ol>
</blockquote>
<ol>
	<li>Password confirmation doesn't match Password
（パスワードと確認用パスワードが一致しないよ的なやつ）</li>
	<li>更新後のpassword_digestは更新前の値と異なっている</li>
</ol>
<h3>12.3.3 パスワードの再設定をテストする</h3>
送信に成功した場合と失敗した場合の統合テストを作成する
まずはテストファイルを作成 
<pre class="lang:default decode:true " title="ターミナル" >$ rails generate integration_test password_resets
Running via Spring preloader in process 4658
      invoke  test_unit
      create    test/integration/password_resets_test.rb</pre> 
アカウント有効化のテストとも共通点が多い
以下手順
<blockquote class="quote-rort">最初に「forgot password」フォームを表示して無効なメールアドレスを送信し、次はそのフォームで有効なメールアドレスを送信します。後者ではパスワード再設定用トークンが作成され、再設定用メールが送信されます。続いて、メールのリンクを開いて無効な情報を送信し、次にそのリンクから有効な情報を送信して、それぞれが期待どおりに動作することを確認します。</blockquote>
みっちりお読みくださいとの事なのでみっちりコメントをつけてみました長い 
<pre class="lang:ruby mark:(59-72) decode:true " title="/sample_app/test/integration/password_resets_test.rb" >require 'test_helper'

class PasswordResetsTest < ActionDispatch::IntegrationTest

  def setup
    # deliveries変数に配列として格納されたメールをクリア
    ActionMailer::Base.deliveries.clear
    # @userにusers(:michael)を代入
    @user = users(:michael)
  end

  test "password resets" do
    # new_password_reset_path(password_resets#new)へgetのリクエスト
    get new_password_reset_path
    # password_resets/newを描画
    assert_template 'password_resets/new'
    # password_resets_path（password_resets#create）にpostのリクエスト　無効なemailの値
    post password_resets_path, params: { password_reset: { email: "" } }
    # falseである→　flashがemptyである
    assert_not flash.empty?
    # password_resets/newを描画
    assert_template 'password_resets/new'
    # password_resets_path（password_resets#create）にpostのリクエスト　有効なemailの値
    post password_resets_path,
         params: { password_reset: { email: @user.email } }
    # 引数の値が同じものではない→　@user.reset_digestと@user.reload.reset_digest
    assert_not_equal @user.reset_digest, @user.reload.reset_digest
    # 引数の値が等しい　１とActionMailer::Base.deliveriesに格納された配列の数
    assert_equal 1, ActionMailer::Base.deliveries.size
    # falseである→　flashがemptyである
    assert_not flash.empty?
    # リダイレクトされる→　root_urlに
    assert_redirected_to root_url
    # userに@userを代入（通常統合テストからはアクセスできないattr_accessorで定義した属性の値にもアクセスできるようになる）
    user = assigns(:user)
    # edit_password_reset（password_resets#edit）にgetのリクエスト（有効なuser.reset_tokenと無効なemailを） 
    get edit_password_reset_path(user.reset_token, email: "")
    # リダイレクトされる→　root_urlに
    assert_redirected_to root_url
    # userの以下のキー（:activated）の値をtoggle!メソッドで反転（無効なユーザーに）
    user.toggle!(:activated)
    # edit_password_reset（password_resets#edit）にgetのリクエスト　（無効なトークンと無効なemailを）
    get edit_password_reset_path(user.reset_token, email: user.email)
    # リダイレクトされる→　root_urlに
    assert_redirected_to root_url
    # userの以下のキー（:activated）の値をtoggle!メソッドで反転（無効なユーザーにしたのをさらに反転して有効なユーザーに）
    user.toggle!(:activated)
    # edit_password_reset（password_resets#edit）にgetのリクエスト　（無効なトークンと有効なemailを）
    get edit_password_reset_path('wrong token', email: user.email)
    # リダイレクトされる→　root_urlに
    assert_redirected_to root_url
    # edit_password_reset（password_resets#edit）にgetのリクエスト（有効なトークンとemailを）
    get edit_password_reset_path(user.reset_token, email: user.email)
    # password_resets/editが描画される
    assert_template 'password_resets/edit'
    # 特定のHTMLタグが存在する→　
    # input name="email" type="hidden" value="michael@example.com"(第2引数のuser.emailが入る)
    assert_select "input[name=email][type=hidden][value=?]", user.email
    # 引数にuser.reset_tokenを持ったpassword_reset_pathにpatchのリクエスト
    # email: user.emailと無効なパスワードとパスワード確認（それぞれの値が合わない）
    patch password_reset_path(user.reset_token),
          params: { email: user.email,
                    user: { password:              "foobaz",
                            password_confirmation: "barquux" } }
    # 特定のHTMLタグが存在する→　div id="error_explanation"
    assert_select 'div#error_explanation'
    # 引数にuser.reset_tokenを持ったpassword_reset_pathにpatchのリクエスト
    # email: user.emailと空のパスワード
    patch password_reset_path(user.reset_token),
          params: { email: user.email,
                    user: { password:              "",
                            password_confirmation: "" } }
    # 特定のHTMLタグが存在する→　div id="error_explanation"
    assert_select 'div#error_explanation'
    # 引数にuser.reset_tokenを持ったpassword_reset_pathにpatchのリクエスト
    # email: user.emailと有効なパスワードとパスワード確認
    patch password_reset_path(user.reset_token),
          params: { email: user.email,
                    user: { password:              "foobaz",
                            password_confirmation: "foobaz" } }
    # trueである　テストユーザーがログイン（test_helper.rbからメソッドの呼び出し）
    assert is_logged_in?
    # falseである　flashがemptyである
    assert_not flash.empty?
    # userの詳細ページにリダイレクトされる
    assert_redirected_to user
  end
end</pre> 
上記ハイライト部分、59~72行目
入力したパスワードと確認用パスワードが異なっている場合も、入力欄が空白だった場合も
チェックする内容が同じ（エラー表示用のdivについているidの名前）になってるけど
より詳しくテストする場合には
それぞれのエラーメッセージでチェックするとどっちで引っかかっているのか、両方引っかかっているのかなどがわかって良い。となる。
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
	<li>リスト 12.6にあるcreate_reset_digestメソッドはupdate_attributeを２回呼び出していますが、これは各行で１回ずつデータベースへ問い合わせしていることになります。リスト 12.20に記したテンプレートを使って、update_attributeの呼び出しを１回のupdate_columns呼び出しにまとめてみましょう (これでデータベースへの問い合わせが１回で済むようになります)。また、変更後にテストを実行し、 greenになることも確認してください。ちなみにリスト 12.20にあるコードには、前章の演習 (リスト 11.39) の解答も含まれています。</li>
	<li>リスト 12.21のテンプレートを埋めて、期限切れのパスワード再設定で発生する分岐 (リスト 12.16) を統合テストで網羅してみましょう (12.21 のコードにあるresponse.bodyは、そのページのHTML本文をすべて返すメソッドです)。期限切れをテストする方法はいくつかありますが、リスト 12.21でオススメした手法を使えば、レスポンスの本文に「expired」という語があるかどうかでチェックできます (なお、大文字と小文字は区別されません)。</li>
	<li>２時間経ったらパスワードを再設定できなくする方針は、セキュリティ的に好ましいやり方でしょう。しかし、もっと良くする方法はまだあります。例えば、公共の (または共有された) コンピューターでパスワード再設定が行われた場合を考えてみてください。仮にログアウトして離席したとしても、２時間以内であれば、そのコンピューターの履歴からパスワード再設定フォームを表示させ、パスワードを更新してしまうことができてしまいます (しかもそのままログイン機構まで突破されてしまいます!)。この問題を解決するために、リスト 12.22のコードを追加し、パスワードの再設定に成功したらダイジェストをnilになるように変更してみましょう</li>
	<li>リスト 12.18に１行追加し、１つ前の演習課題に対するテストを書いてみましょう。ヒント: リスト 9.25のassert_nilメソッドとリスト 11.33のuser.reloadメソッドを組み合わせて、reset_digest属性を直接テストしてみましょう。</li>
</ol>
</blockquote>
1. 
<pre class="lang:ruby mark:(4-9,21-21） decode:true " title="/sample_app/app/models/user.rb　テスト→GREEN" >・
・
・
  #前章の演習 (リスト 11.39) の解答部分
  # アカウントを有効にする
  def activate
    #指定のカラムを指定の値に、DBに直接上書き保存
    update_columns(activated: true, activated_at: Time.zone.now)
  end

  # 有効化用のメールを送信する
  def send_activation_email
    UserMailer.account_activation(self).deliver_now
  end
  
  # パスワード再設定の属性を設定する
  def create_reset_digest
    # （呼び出し先で考えると）@userのreset_tokenに代入→User.new_token
    self.reset_token = User.new_token
    # 指定のカラムを指定の値に、DBに直接上書き保存
    update_columns(reset_digest: User.digest(reset_token), reset_sent_at: Time.zone.now)
  end
・
・
・</pre> 
2. 
<pre class="lang:default mark:(24-26) decode:true " >・
・
・
  test "expired token" do
    # new_password_reset_path(password_resets#new)へgetのリクエスト
    get new_password_reset_path
    # password_resets_pathにpostのリクエスト　有効なemailの値
    post password_resets_path,
         params: { password_reset: { email: @user.email } }
    # @userに@userを代入（通常統合テストからはアクセスできないattr_accessorで定義した属性の値にもアクセスできるようになる）
    @user = assigns(:user)
    # @userのreset_sent_atを3時間前に上書き
    @user.update_attribute(:reset_sent_at, 3.hours.ago)
    # @user.reset_tokenを引数に持ったpassword_reset_pathにpacthのリクエスト
    # @user.emailと有効なパスワードとパスワード確認
    patch password_reset_path(@user.reset_token),
          params: { email: @user.email,
                    user: { password:              "foobar",
                            password_confirmation: "foobar" } }
    # レスポンスは以下になるはず　→　リダイレクト
    assert_response :redirect
    #　POSTの送信結果に沿って指定されたリダイレクト先に移動
    follow_redirect!
    # リダイレクトされたページに'有効期限が切れています'が含まれている
    # 日本語化の影響で下記だけど、本文通りの場合は→ assert_match /expired/i, response.body
    assert_match '有効期限が切れています', response.body
  end
end</pre> 
3. 
<pre class="lang:ruby mark:(15-16) decode:true " title="/sample_app/app/controllers/password_resets_controller.rb" >・
・
・
  def update
    # params[:user][:password]がemptyの場合
    if params[:user][:password].empty?
      # @user.errorsに:password, :blankを追加
      @user.errors.add(:password, :blank)
      # editのビューを描画
      render 'edit'
    # 指定された属性の検証がすべて成功した場合@userの更新と保存を続けて同時に行う
    elsif @user.update_attributes(user_params)
      # @userとしてログイン
      log_in @user
      # @userの:reset_digestの値をnilに更新して保存
      @user.update_attribute(:reset_digest, nil)
      # 成功のフラッシュメッセージを表示
      flash[:success] = t('.password_has_been_reset')
      # ユーザー詳細ページにリダイレクト
      redirect_to @user
    else
      # editのビューを描画
      render 'edit'
    end
  end
・
・
・</pre> 
4. 
<pre class="lang:ruby mark:(18-19) decode:true " title="/sample_app/test/integration/password_resets_test.rb" >・
・
・
  test "password resets" do
    # new_password_reset_path(password_resets#new)へgetのリクエスト
    get new_password_reset_path
・
・
・
    # 引数にuser.reset_tokenを持ったpassword_reset_pathにpatchのリクエスト
    # email: user.emailと有効なパスワードとパスワード確認
    patch password_reset_path(user.reset_token),
          params: { email: user.email,
                    user: { password:              "foobaz",
                            password_confirmation: "foobaz" } }
    # trueである　テストユーザーがログイン（test_helper.rbからメソッドの呼び出し）
    assert is_logged_in?
    # nilであればture → 再取得したuserのreset_digest
    assert_nil user.reload.reset_digest
    # falseである　flashがemptyである
    assert_not flash.empty?
    # userの詳細ページにリダイレクトされる
    assert_redirected_to user
  end
・
・
・  </pre>
<h3>まとめとか感想</h3>
パスワードの再設定機能が作動するようになりました！
あれこれの確認のために言語設定をenにしたりjaにしたり面ｄ略お
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

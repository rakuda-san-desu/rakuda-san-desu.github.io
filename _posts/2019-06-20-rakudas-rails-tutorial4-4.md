---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜4.4（と4.5）"
date: "2019-06-20 19:11:12"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 773
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial4-4/"
---
<h2>4.4 Rubyにおけるクラス</h2>
<h3>4.4.1 コンストラクタ</h3>
<h4>リテラルコンストラクタとは何ぞ</h4>
リテラル　→　コードに直接書かれた文字や数字
コンストラクタ　→　インスタンスを作成した時に自動的に実行されるメソッド

うーん。これは雰囲気でふわっと掴んでれば良さそう感？（？
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> s = "foobar" 　#""は文字列（Stringクラス）のオブジェクトを作成するコンストラクタ（詳細な動作は下記）
=> "foobar"
>> s.class         #変数ｓのクラスを調べる
=> String          #（）

#動作の内容をはっきりと分かりやすく書いたコード↓
>> s = String.new("foobar")   #Stringクラスのインスタンスを作る（中身は文字列のfoobar）
=> "foobar"
>> s.class
=> String
>> s == "foobar"
=> true</pre>
<h4>配列とハッシュでインスタンスを生成</h4>
・配列のコンストラクタ　→Array.new
・ハッシュのコンストラクタ　→Hash.new
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> a = Array.new([1, 3, 2])  #引数には配列の初期値が入る
=> [1, 3, 2]

>> h = Hash.new     
=> {}               #空のハッシュが生成される
>> h[:hoo]          #存在しないキーにアクセス
=> nil              #存在しないのでnilが返る
>> h = Hash.new(0)  #引数にはハッシュのデフォルト値が入る→　デフォルト値を0に
=> {}               #空のハッシュが生成される
>> h[:hoo]          #存在しないキーにアクセス
=> 0                #デフォルト値に設定した0が返ってくる</pre>
クラスメソッド　→　クラス自身に対して呼び出すメソッド（上記のnew）
クラスメソッドで呼び出された結果　→　そのクラスのオブジェクト（クラスのインスタンス）
クラスの中でインスタンスに対して呼び出すメソッド　→　インスタンスメソッド
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
1から10の範囲オブジェクトを生成するリテラルコンストラクタは何でしたか? (復習です)
</li>
<li>
今度はRangeクラスとnewメソッドを使って、1から10の範囲オブジェクトを作ってみてください。ヒント: newメソッドに2つの引数を渡す必要があります
</li>
<li>
比較演算子==を使って、上記２つの課題で作ったそれぞれのオブジェクトが同じであることを確認してみてください。
</li>
</ol>
</blockquote>
Rangeのクラスメソッド →　第一引数と第二引数までの範囲オブジェクトを返す
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#1.
>> a = 1..10
=> 1..10

#2.
>> b = Range.new(1, 10)
=> 1..10

#3.
>> a == b
=> true</pre>
<h3>4.4.2 クラス継承</h3>
superclassメソッド　→クラスの親クラスが返ってくる
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> s = String.new("foobar")　#変数sに　"foobar"の値を持つStringクラスのインスタンスを代入
=> "foobar"
>> s.class                        # 変数sのクラスを調べる
=> String
>> s.class.superclass             # Stringクラスの親クラスを調べる
=> Object
>> s.class.superclass.superclass  # Stringクラスの親クラスのさらに親クラスを調べる
=> BasicObject
>> s.class.superclass.superclass.superclass  # Stringクラスの親クラスの親クラスのさらに親クラスを調べる
=> nil                            #BasicObjectより上のクラスはない</pre>

全てのRubyオブジェクトはBasicObjectクラスを継承している！
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> class Word　　　　　　　　　#Wordクラス
>>   def palindrome?(string)      #引数stringを持ったpalindrome?メソッド
>>      string == string.reverse  #回文であればtrueが返る
>>   end
>> end
=> :palindrome?                   #Wordクラスの中身はpalindrome?メソッド（と言う事？）
>> w = Word.new                   #変数wに生成したWordクラスのインスタンスを代入
=> #<Word:0x0000000002d30bd8>     #Wordクラスのインスタンス：インスタンスの情報が保存されている場所
>> w.palindrome?("foobar")        #wの中でpalindrome?メソッドを呼び出す（値は"foobar"）
=> false                          #回文になっていないのでfalse
>> w.palindrome?("level")         #wの中でpalindrome?メソッドを呼び出す（値は"level"）
=> true                           #回文になっているのでtrue</pre>
<strong>文字列を引数に取るならStringクラスを継承するのが自然</strong>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>>> class Word < String        #WordクラスはStringクラスを継承
>>   def palindrome?               #文字列が回文であればtrueを返すalindrome?メソッド
>>     self == self.reverse        #selfは値自身を表す（ここではWordクラス自身）
>>   end
>> end
=> :palindrome?
>> s = Word.new("level")
#変数Sに　"level"という文字列を値に持ったWordクラスのインスタンスを生成して代入
#Wordクラスにはコンストラクタが無いので、継承元のStringのコンストラクタである""を呼び出している
#つまり　実質Wordが継承しているString.new("level")　（考えるな感じろメソッド）
=> "level"
>> s.palindrome?                   #sに対してpalindrome?メソッドを呼び出す
=> true
>> s.length                　　　　 #Stringクラスを継承しているためlengthも呼び出せる
=> 5
</pre>
また、Stringクラスを継承している為、superclassメソッドを使ってクラス階層を確認すると
「Word < String < Object < BasicObject」であることが確認できる
<h4>self.の省略</h4>
<blockquote class="quote-rort">なお、Stringクラスの内部では、メソッドや属性を呼び出すときのself.も省略可能です。</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> class Word < String
>>   def palindrome? 
>>     self == <span class="point">reverse</span>       #メソッドや属性（この場合reverseメソッド）を呼び出すself.を省略
>>   end
>> end
>> r = Word.new("hogehoge")
=> "hogehoge"
>> r.palindrome?
=> false
>> s = Word.new("level")
=> "level"
>> s.palindrome?
=> true</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
Rangeクラスの継承階層を調べてみてください。同様にして、HashとSymbolクラスの継承階層も調べてみてください。
</li>
<li>
リスト 4.15にあるself.reverseのselfを省略し、reverseと書いてもうまく動くことを確認してみてください。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#1.
>> Range.class
=> Class
>> Range.class.superclass
=> Module
>> Range.class.superclass.superclass
=> Object
>> Range.class.superclass.superclass.superclass
=> BasicObject
#HashクラスとSymbolクラスも同じく
</pre>
２.は上記（self.の省略）の通り上手く動く
<h3>4.4.3 組み込みクラスの変更</h3>
palindrome?メソッドをStringクラス自身に追加して
リテラルな文字列に対して直接実行することは可能か？
→可能（ただし、正当な理由が無い場合は無作法に当たる）
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> class String    #Stringクラスに追加
>>   def palindrome?
>>     self == reverse  #reverseを呼び出すself.は省略可能
>>   end
>> end
=> :palindrome?
>> "deified".palindrome?
=> true

#Railsはblank?メソッドをRubyの組み込みクラスに追加している
#よってRailsコンソール上では下記のコードが実行できる
>> "".blank?
=> true
>> "      ".empty?
=> false
>> "      ".blank?
=> true
>> nil.blank?
=> true
</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>palindrome?メソッドを使って、“racecar”が回文であり、“onomatopoeia”が回文でないことを確認してみてください。南インドの言葉「Malayalam」は回文でしょうか? ヒント: downcaseメソッドで小文字にすることを忘れないで。
</li>
<li>
リスト 4.16を参考に、Stringクラスにshuffleメソッドを追加してみてください。ヒント: リスト 4.12も参考になります。
</li>
<li>
リスト 4.16のコードにおいて、self.を削除してもうまく動くことを確認してください。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール・上記の続きに）" >#1.
>> "racecar".palindrome?
=> true
>> "onomatopoeia".palindrome?
=> false
>> "Malayalam".downcase.palindrome?
  #大文字（M）と小文字（m）は区別されるため、すべて小文字に直してからpalindrome?メソッドを呼び出す
=> true

#2.
>> class String
>>   def shuffle
>>     self.split('').shuffle.join
>>   end
>> end
=> :shuffle
>> "foobar".shuffle
=> "obaorf"

#3.
>> class String
>>   def shuffle
>>     split('').shuffle.join
>>   end
>> end
=> :shuffle
>> "foobar".shuffle
=> "roabof"
</pre>
<h3>4.4.4 コントローラクラス</h3>
<ul>
<li>Railsのcontrollerにもクラスがあり、階層になっているし継承されている</li>
<li>controllerに書かれたアクションはメソッド</li>
</ul>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> controller.home
=> nil                   #中身は空なのでnil</pre>
→Railsのアクションでは戻り値はないし返される値は重要ではない
RailsとRubyは別物！
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
第2章で作ったToyアプリケーションのディレクトリでRailsコンソールを開き、User.newと実行することでuserオブジェクトが生成できることを確認してみましょう。
</li>
<li>
生成したuserオブジェクトのクラスの継承階層を調べてみてください。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#1.
ec2-user:~/environment/sample_app (rails-flavored-ruby) $ cd ..  #sample_appを抜ける
ec2-user:~/environment $ cd toy_app/                             #toy_appに移動
ec2-user:~/environment/toy_app (master) $ rails c                #コンソールを起動
>> user = User.new
=> #<User id: nil, name: nil, email: nil, created_at: nil, updated_at: nil>

#2.
>> user.class
=> User(id: integer, name: string, email: string, created_at: datetime, updated_at: datetime)
>> user.class.superclass
=> ApplicationRecord(abstract)
>> user.class.superclass.superclass
=> ActiveRecord::Base
>> user.class.superclass.superclass.superclass
=> Object
>> user.class.superclass.superclass.superclass.superclass
=> BasicObject
>> user.class.superclass.superclass.superclass.superclass.superclass
=> nil</pre>
<h3>4.4.5 ユーザークラス</h3>
sample_appで使用するUserクラスを作成する
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#sample_appに戻る
ec2-user:~/environment/toy_app (master) $ cd ..
ec2-user:~/environment $ cd sample_app/
ec2-user:~/environment/sample_app (rails-flavored-ruby) $ touch example_user.rb
#ルートdirにexample_user.rbファイルを作成
#なんでこんなところに作るんだろう？って思うじゃん？？最終的に削除します！（練習用のファイル）</pre>
<pre class="lang:ruby decode:true " title="/sample_app/example_user.rb" >#内容の詳しい解説は別途枠外にて
class User　　　
  attr_accessor :name, :email

  def initialize(attributes = {})
    @name  = attributes[:name]
    @email = attributes[:email]
  end

  def formatted_email
    "#{@name} <#{@email}>"
  end
end</pre> 
<h4>attr_accessorとは何ぞ</h4>
「attr_reader」→ゲッター・データを取り出すためのメソッド
「attr_writer」→セッター・データを代入するためのメソッド
この2つを合わせたメソッド→「attr_accessor」
→属性に対応するゲッターとセッターをそれぞれ定義してくれる便利なメソッド
<pre class="lang:ruby decode:true " title="コード解説">attr_accessor :name, :email
#属性は,で区切って複数指定できる）</pre>
このコードによりインスタンス変数@nameとインスタンス変数@emailにアクセスできるようになる（インスタンス変数とは後程どこかで）
<h4>initializeメソッドとは何ぞ</h4>
インスタンス生成時に自動的に呼び出されるメソッド・引数も渡せる
<pre class="lang:ruby decode:true " title="コード解説">#空のハッシュがデフォルト値のattributes変数を引数に持つinitializeメソッド
def initialize(attributes = {})
  @name  = attributes[:name]  #@nameにattributesの:nameを代入
  @email = attributes[:email] #@emailにattributesの:emailを代入
  #空のハッシュをデフォルトに設定した為、nameやemailを持たないユーザーを作ることも出来る
end</pre>
<h4>formatted_emailメソッドを定義</h4>
呼び出したときにユーザーの名前とemailが表示される様にメソッドを定義する
@nameと@emailはUserクラスのインスタンス変数なのでUserクラス内で自動的に使用できる
<pre class="lang:ruby decode:true " title="コード解説">def formatted_email
  "#{@name} <#{@email}>"　　#変数展開され　@nameの値 <@emailの値>　の形で出力される
end</pre>
コンソールで試してみる
<pre class="lang:default mark:(13-14) decode:true " title="ターミナル（コンソール）" >>> require './example_user'  #./example_userの読み込み（カレントディレクトリ直下）
=> true
>> example = User.new             #Userクラスのインスタンスをexampleに代入（中身は空っぽ）
=> #<User:0x00000000031629b8 @name=nil, @email=nil>
>> example.name = "Example User"  #@nameに値を設定
=> "Example User"
>> example.email = "user@example.com"   #@emailに値を設定
=> "user@example.com"
>> example.formatted_email              #exampleに対してformatted_emailを呼び出す
=> "Example User <user@example.com>"

#ハッシュを渡すことで属性と値が定義されたインスタンスを作成することが出来る
#ハッシュの{}は省略可能！
>> user = User.new(name: "Michael Hartl", email: "mhartl@example.com")
=> #<User:0x0000000003266e90 @name="Michael Hartl", @email="mhartl@example.com">
>> user.formatted_email
=> "Michael Hartl <mhartl@example.com>"</pre> 
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
Userクラスで定義されているname属性を修正して、first_name属性とlast_name属性に分割してみましょう。また、それらの属性を使って "Michael Hartl" といった文字列を返すfull_nameメソッドを定義してみてください。最後に、formatted_emailメソッドのnameの部分を、full_nameに置き換えてみましょう (元々の結果と同じになっていれば成功です)
</li>
<li>
"Hartl, Michael" といったフォーマット (苗字と名前がカンマ+半角スペースで区切られている文字列) で返すalphabetical_nameメソッドを定義してみましょう。
</li>
<li>
full_name.splitとalphabetical_name.split(’, ’).reverseの結果を比較し、同じ結果になるかどうか確認してみましょう。
</li>
</ol>
</blockquote>
<pre class="lang:ruby mark:(3-3,6-7,11-13,15-17,20-20) decode:true " title="/sample_app/example_user.rb" >#先にexample_user.rbの該当箇所を追加修正
class User
  attr_accessor :first_name, :last_name, :email

  def initialize(attributes = {})
    @first_name  = attributes[:first_name]
    @last_name = attributes[:last_name]
    @email = attributes[:email]
  end
  
  def full_name
    "#{@first_name} #{@last_name}"
  end
  
  def alphabetical_name
    "#{@last_name}, #{@first_name}"
  end

  def formatted_email
    "#{full_name} <#{@email}>"
  end
end</pre> 
<pre class="lang:default decode:true " title="ターミナル(コンソールを再起動)" >>> require './example_user'　　#example_user.rbを再度読み込み
=> true
>> user = User.new(first_name: "Michael", last_name: "Hartl", email: "mhartl@example.com")
=> #<User:0x0000000003b369a8 @first_name="Michael", @last_name="Hartl", @email="mhartl@example.com">
>> user.formatted_email　　
=> "Michael Hartl <mhartl@example.com>"
  #full_nameメソッドが展開されている（表示は変わらない)
>> user.alphabetical_name
=> "Hartl, Michael"
>> user.full_name.split == user.alphabetical_name.split(', ').reverse
#user.full_nameをスペースで区切った配列 == user.alphabetical_nameを', 'で区切った配列を逆順に並び替え
=> true</pre> 
<h2>4.5 最後に</h2>
<blockquote class="quote-rort">4.4.5で作成したexample_user.rbファイルは今後使わないので、削除してください。</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソールは終了させておく）" >$ rm example_user.rb</pre>
適宜commit

本文ではmasterブランチにマージだけどプルリクの練習↓
ブランチのままGitHubにpush
GitHub上でプルリク→マージ
fetchしてmasterにcheckoutしてpullの流れ

以降まとめに付き本文ママ
<h3>まとめとか感想</h3>
長かった!やっと終わった！！4章！！
勉強感が強くて苦手な4章ですが、流さないように意識しつつ頑張りました！
1週目の時よりかなり頭に入ったと思います。頑張った！！
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

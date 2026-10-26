---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜4.2"
date: "2019-05-30 16:49:21"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 741
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial4-2/"
---
<h2>4.2 文字列とメソッド</h2>
<h4>irbの設定</h4>
<pre class="lang:default decode:true " title="ターミナル" >$ nano ~/.irbrc</pre>
エディタが開くので本文のコードをコピペ
<a href="https://rakuda3desu.net/wp-content/uploads/2019/05/4.2-1.png"><img src="https://rakuda3desu.net/wp-content/uploads/2019/05/4.2-1-300x165.png" alt="" width="300" height="165" class="alignnone size-medium wp-image-743" /></a>
Ctrl-X　→　Y　→　Enter

コンソールを起動する
<pre class="lang:default decode:true " title="ターミナル" >(rails-flavored-ruby) $ <span class="point">rails console</span>
Running via Spring preloader in process 4544
Loading development environment (Rails 5.1.6.2)
>> </pre>
※<strong>rails c</strong>に省略可能
<h3>4.2.1 コメント</h3>
本文ママ
<h3>4.2.2 文字列</h3>
文字列は「""」で囲む
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#整数の足し算
>> 17 + 42
=> 59
#""で囲んだことにより「17」と言う文字列と「42」と言う文字列を足している事になる
>> "17" + "42"
=> "1742"
>> </pre>
<h4>展開式</h4>
変数を#{}で囲んで代入した値を展開する
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >>> first_name = "Michael"    # first_name に Michaelを代入
=> "Michael"
>> "#{first_name} Hartl"     # first_nameに代入された Michael を展開
=> "Michael Hartl"</pre>
<h4>出力</h4>
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >>> puts "foo"
foo               #puts出力は自動的に改行される
=> nil
>> print "foo" 
foo=> nil         #print出力は自動的に改行されない
>> print "foo\n"  #改行させたい場合は改行文字を挿入「バックスラッシュ (\) + n」
foo
=> nil</pre>
環境によってはバックスラッシュが¥に見えてるかもしれませんが/（スラッシュ）の逆向きのやつです
<h4>putsメソッドとnil</h4>
putsメソッドの戻り値は実はnil
上記の例で言えば、文字列"foo"はputsの戻り値ではなく副作用として表示されている
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >>> puts
#戻り値のnilだけが表示される
=> nil</pre>
<h4>シングルクォート内の文字列</h4>
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >#シングルクォートで囲っても文字列として認識される
>> 'foo' 
=> "foo"
>> 'foo' + 'bar'
=> "foobar"

#ただしシングルクォート内では式展開が行われない
>> foo = "rakuda"
=> "rakuda"
>> "#{foo}"
=> "rakuda"
>> '#{foo} bar'
=> "\#{foo} bar"

#ダブルクォート内で#{などの特殊文字を使いたい場合はバックスラッシュでエスケープする必要がある
#エスケープしているのでダブルクォート内でも式展開されない
>> "\#{foo}"
=> "\#{foo}"
</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
city変数に適当な市区町村を、prefecture変数に適当な都道府県を代入してください。
</li>
<li>
先ほど作った変数と式展開を使って、「東京都 新宿区」のような住所の文字列を作ってみましょう。出力にはputsを使ってください。
</li>
<li>
上記の文字列の間にある半角スペースをタブに置き換えてみてください。(ヒント: 改行文字と同じで、タブも特殊文字です)
</li>
<li>
タブに置き換えた文字列を、ダブルクォートからシングルクォートに置き換えてみるとどうなるでしょうか?
</li>
</ol>
</blockquote>
※タブを表す特殊記号<strong>「\t」</strong>
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >#1.都市名を代入
>> city = "甲府"
=> "甲府"
>> prefecture = "山梨"
=> "山梨"

#2.半角で区切って出力
>> puts "#{prefecture}県 #{city}市"
山梨県 甲府市
=> nil

#3.タブ文字（\t）に置き換えて出力
>> puts "#{prefecture}県\t#{city}市"
山梨県  甲府市　　＃半角スペース×２で出力されている（ソフトタブ・2の設定があるから？）
=> nil

#4.シングルクォートに置き換え
>> puts '#{prefecture}県\t#{city}市'
#{prefecture}県\t#{city}市  #式展開がされず、特殊文字はそのままで出力
=> nil</pre>
<h3>4.2.3 オブジェクトとメッセージ受け渡し</h3>
Rubyではあらゆるものがオブジェクトである
オブジェクトとは　→　考えるな感じろ（4.4.2で説明があるとの事）
オブジェクトが何をするか　→　メッセージに応答する
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#オブジェクト（文字列）←length（文字列の文字数を返してもらうためのメッセージ）
>>  "foobar".length 
=> 6</pre>
オブジェクトに渡されるメッセージ　→　メソッド　→　そのオブジェクト内で定義されたメソッド
上記で言うとlengthがメソッド
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >#オブジェクトは空っぽですか？
>> "foobar".empty?
#文字列が入っているのでfalse
=> false
>> "".empty?
#空っぽなのでtrue
=> true
</pre>
論理値（trueかfalse）を返すときは末尾に？を付ける習慣がある
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >#sに文字列foobarを代入
>> s = "foobar"  
#もしsが空っぽなら
>> if s.empty?
# "The string is empty"を返す   
>>   "The string is empty"
#そうでないなら
>> else
#"The string is nonempty"を返す
>>   "The string is nonempty"
>> end
=> "The string is nonempty"
  #空っぽではない（false）ので"The string is nonempty"

#条件が2つ以上ある時はelsif（else+if）文を使う
>> if s.nil?
>>   "The variable is nil"
>> elsif s.empty?
>>   "The string is empty"
>> elsif s.include?("foo")
>>   "The string includes 'foo'"
>> end
=> "The string includes 'foo'"</pre>
演算子を使うことも出来る
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >#xに文字列fooを代入
>> x = "foo"
=> "foo"
#yに空っぽを代入
>> y = ""
=> ""
# もしxが空っぽ かつ yが空っぽなら"Both strings are empty"を出力
>> puts "Both strings are empty" if x.empty? && y.empty?
#falseなので出力されない
=> nil
#もしxが空っぽ もしくは yが空っぽなら"One of the strings is empty"を出力
>> puts "One of the strings is empty" if x.empty? || y.empty?
"One of the strings is empty"  #trueなので出力される
=> nil
#もし xがからっぽ ではない なら"x is not empty"を出力
>> puts "x is not empty" if !x.empty?
"x is not empty"  #trueなので出力される
=> nil</pre>
nil（空っぽ）もオブジェクト　→　多くのメソッドに応答できる
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >#to_sはほぼあらゆるオブジェクトを文字列に変換するメソッド
#nilを文字列に変換
>> nil.to_s
#空っぽの文字列が出力
=> ""  
#nilは空っぽか確認
>> nil.empty?
#nilに対してempty?は未定義のメソッドです（なので使えません）的なエラー
NoMethodError: undefined method `empty?' for nil:NilClass
#nilを 文字列にして 空っぽか確認
>> nil.to_s.empty?
#nil.to_sでnilが""になっている　""が空っぽか確認　→　true
=> true
#このようにメソッドを繋げることをメソッドチェーンと言う
</pre>
nilかどうかを調べる
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >>> "foo".nil?
=> false
>> "".nil?
=> false
>> nil.nil?
#「nil」の時だけtrueが返ってくる
=> true
</pre>
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >#後続if
#後続の条件式がtrueの時だけ実行される（falseの時は実行されない)
>> puts "x is not empty" if !x.empty?
x is not empty
=> nil

#unless式　if式とは逆でfalseの時に実行される
>> string = "foobar"
=> "foobar"
>> puts "The string '#{string}' is nonempty." unless string.empty?
The string 'foobar' is nonempty.
=> nil</pre>
オブジェクトそのものの論理値がfalseになるのはfalse自身とnilだけ
!（否定）を付けると論理値が反転
!!（バンバン）は2回否定なので強制的に論理値に変換できる
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >>> nil
=> nil    #nilはnil
>> !nil
=> true   #!（not）を付けたnilの論理値は反転してtrue
>> !!nil  
=> false  #2回否定により一周回ってfalse

>> false
=> false
>> !false
=> true
>> !!false
=> false

#!!（2回否定）する事で論理値が返ってくる
>> 0
=> 0　　#0は0
>> !!0
=> true  #0の論理値はtrue</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
"racecar" の文字列の長さはいくつですか? lengthメソッドを使って調べてみてください。
</li>
<li>
reverseメソッドを使って、"racecar"の文字列を逆から読むとどうなるか調べてみてください。
</li>
<li>
変数sに "racecar" を代入してください。その後、比較演算子 (==) を使って変数sとs.reverseの値が同じであるかどうか、調べてみてください。
</li>
<li>
リスト 4.9を実行すると、どんな結果になるでしょうか? 変数sに "onomatopoeia" という文字列を代入するとどうなるでしょうか? ヒント: 上矢印 (またはCtrl-Pコマンド) を使って以前に使ったコマンドを再利用すると一からコマンドを全部打ち込む必要がなくて便利ですよ。)
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >>> 
#1.7文字
"racecar".length
=> 7

#2.逆から読んでもレースカー！
>> "racecar".reverse
=> "racecar"

#3.代入して比較　→　true
>> s = "racecar" 
=> "racecar"
>> s == "racecar" 
=> true

#sとsを逆にしたものが等しければIt's a palindrome!（回文です!）と出力
>> puts "It's a palindrome!" if s == s.reverse
It's a palindrome!
=> nil
>> s = "onomatopoeia"
=> "onomatopoeia"
>> puts "It's a palindrome!" if s == s.reverse
=> nil</pre>
<h3>4.2.4 メソッドの定義</h3>
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >#関数string_message（引数str　初期値に''を設定）
>> def string_message(str = '')
>>   if str.empty?
>>     "It's an empty string!"
>>   else
>>     "The string is nonempty."
>>   end
>> end
=> :string_message
>> puts string_message("foobar")
The string is nonempty.
>> puts string_message("")
It's an empty string!
>> puts string_message  #引数付けていないため初期値（''）が呼び出される
It's an empty string!  #初期値は''（空っぽ）なのでこの結果に</pre>
「暗黙の戻り値がある」と、言う事
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >>>> def string_message(str = '')
#It's an empty string!を返す　strが空っぽだったら（後続if）
>> return "It's an empty string!" if str.empty?
#そうでなければThe string is nonempty.を返す
#returnが無くても暗黙で値を返す
>> "The string is nonempty."
>> end
=> :string_message
>> puts string_message("foobar")
The string is nonempty.
=> nil</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>リスト 4.10のFILL_INの部分を適切なコードに置き換え、回文かどうかをチェックするメソッドを定義してみてください。ヒント: リスト 4.9の比較方法を参考にしてください。
</li>
<li>
上で定義したメソッドを使って “racecar” と “onomatopoeia” が回文かどうかを確かめてみてください。１つ目は回文である、２つ目は回文でない、という結果になれば成功です。
</li>
<li>
palindrome_tester("racecar")に対してnil?メソッドを呼び出し、戻り値がnilであるかどうかを確認してみてください (つまりnil?を呼び出した結果がtrueであることを確認してください)。このメソッドチェーンは、nil?メソッドがリスト 4.10の戻り値を受け取り、その結果を返しているという意味になります。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >>> def palindrome_tester(s)
       #1.sとsを逆にしたものが等しければ（回文であれば）
>>     if s == s.reverse
>>       puts "It's a palindrome!"
>>     else
>>       puts "It's not a palindrome."
>>    end
>> end
=> :palindrome_tester

#2.それぞれの結果
>> puts palindrome_tester("racecar")
It's a palindrome!
=> nil
>> puts palindrome_tester("onomatopoeia")
It's not a palindrome.
=> nil

#3.nil?メソッド呼び出したメソッドチェーン
>> puts palindrome_tester("racecar").nil?
It's a palindrome!
true　　#putsの戻り値はnilなのでtrue（上の文字列は戻り値ではなく副作用）
=> nil</pre>
<h3>4.2.5 titleヘルパー、再び</h3>
titleヘルパーの解説は本文ママ
<h4>module ApplicationHelper</h4>
module　→　関連したメソッドを纏める方法の一つ
includeメソッドを使って読み込むことが出来る

railsではinclude行を書かなくても自動的にヘルパーmoduleを読み込んでくれる
　→自動的にすべてのビューで利用できる
<h3>まとめとか感想</h3>
良くわかっていない用語が多かったので
調べて（ある程度）納得するのに中々時間がかかってしまいました🐫💦
苦手意識が強い部分なのでじっくり向き合いたい。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

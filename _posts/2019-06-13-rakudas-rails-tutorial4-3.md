---
layout: post
title: "らくだ🐫にもできるRailsチュートリアル｜4.3"
date: "2019-06-13 19:09:33"
categories: ["Railsチュートリアル", "Ruby on Rails", "学習メモ"]
tags: ["Railsチュートリアル", "らくだにも出来るRailsチュートリアル", "初心者"]
published: true
wordpress_id: 763
wordpress_status: "publish"
permalink: "/rakudas-rails-tutorial4-3/"
---
<h2>4.3 他のデータ構造</h2>
<h3>4.3.1 配列と範囲演算子</h3>
<h4>配列とは何ぞ</h4>
<blockquote class="quote-rort">配列 (array) は、特定の順序を持つ要素のリストです。</blockquote>
sqlitメソッドで文字列を自然に配列へと変換できる
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> "foo bar     baz".split　     #空白部分で区切られて配列に分割される
=> ["foo", "bar", "baz"]         #配列は[]で囲まれ、,で区切られる
>> "fooxbarxbaz".split('x')      #特定の文字で分割される様に指定もできる
=> ["foo", "bar", "baz"]

>> a = [42, 8, 17]   #aに配列を代入
=> [42, 8, 17]
>> a[0]              #aに代入された配列の0番目を呼び出す（[]にインデックス番号で呼び出し）（インデックスのカウントは0から始まる）
=> 42
>> a[1]
=> 8
>> a[2]
=> 17
>> a[-1]             #マイナスの指定もできる（後ろからカウント）
=> 17

>> a                 #aに代入された内容を呼び出す                         
=> [42, 8, 17]
>> a.first           #.firstなどのメソッドでも呼び出せる
=> 42
>> a.second
=> 8
>> a.last
=> 17
>> a.last == a[-1]   #比較演算子==で等しいことを確認
=> true</pre>
<h4>配列も様々なメソッドに応答する</h4>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> x = a.length      # xに　aに代入されている配列の要素の数を代入
=> 3
(比較演算子部分省略)
>> a                 #aに代入された内容を呼び出す（確認）
=> [42, 8, 17]
>> a.empty?          #aは空っぽか？     
=> false
>> a.include?(42)    #aに42が含まれているか
=> true
>> a.sort            #aをソートして並び替え（無指定で昇順）（ソート方法を指定も出来る）
=> [8, 17, 42]
>> a.reverse         #aを逆から並べ替え 
=> [17, 8, 42]
>> a.shuffle         #aをシャッフル
=> [17, 42, 8]      
>> a                 #aに代入された内容を呼び出す（確認）
=> [42, 8, 17]       #aの内容自体は変更されていない
>> a.sort!           #配列の内容自体を変更したい場合はメソッドの末尾に!を付ける
=> [8, 17, 42]
>> a                 #aに代入された内容を呼び出す（確認）
=> [8, 17, 42]       #aに代入された配列自体が変更されている</pre>
<h4>配列に要素を追加する</h4>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> a.push(6)　　　　　　　#.pushメソッドで6を追加　
=> [42, 8, 17, 6]
>> a << 7                     #<<メソッドで7を追加　
=> [42, 8, 17, 6, 7]
>> a << "foo" << "bar"        #連続して追加することも出来る　
=> [42, 8, 17, 6, 7, "foo", "bar"]  #整数と文字列が共存している</pre>
Rubyでは異なる型が配列の中で共存できる
<h4>配列を繋げる</h4>
joinメソッドで配列を文字列に変換する
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> a                           #aに代入された内容を呼び出す
=> [42, 8, 17, 6, 7, "foo", "bar"]
>> a.join                           # 単純な連結
=> "4281767foobar"
>> a.join(', ')                     # カンマ+スペースを使って連結
=> "42, 8, 17, 6, 7, foo, bar"</pre>
<h4>範囲とto_aメソッド</h4>
範囲オブジェクトに対してto_aメソッドを呼び出すと配列にして返してくれる
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> 0..9              #0～9を範囲として指定
=> 0..9
>> 0..9.to_a              # 9に対してto_aを呼んでしまっている
NoMethodError: undefined method `to_a' for 9:Fixnum  #範囲の指定がされていないのでエラー
>> (0..9).to_a            # 丸カッコを使って範囲オブジェクトにしてから呼び出す
=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]　　#指定範囲が配列になって帰ってくる
>> a = %w[foo bar baz quux]         # %wはスペースで区切りを付けた配列を作る　→　aに代入
=> ["foo", "bar", "baz", "quux"]
>> a[0..2]                          #aの中の指定された範囲を呼び出す
=> ["foo", "bar", "baz"]

#文字列に対しても範囲オブジェクトを使える
>> ('a'..'e').to_a                  #a～eの範囲を配列に
=> ["a", "b", "c", "d", "e"]

#インデックスに-1を指定することで、配列の長さがわからなくても最後の要素を指定できる
>> a = (0..9).to_a                  #aに0～9の範囲を配列にして代入
=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>> a[2..(a.length-1)]               #aの2 ～ aの配列の要素の数-1 の範囲を取り出す
=> [2, 3, 4, 5, 6, 7, 8, 9]
>> a[2..-1]                         #aの 2 ～ 配列の一番最後 の範囲を取り出す
=> [2, 3, 4, 5, 6, 7, 8, 9]</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
文字列「A man, a plan, a canal, Panama」を ", " で分割して配列にし、変数aに代入してみてください。
</li>
<li>
今度は、変数aの要素を連結した結果 (文字列) を、変数sに代入してみてください。
</li>
<li>
変数sを半角スペースで分割した後、もう一度連結して文字列にしてください (ヒント: メソッドチェーンを使うと１行でもできます)。リスト 4.10で使った回文をチェックするメソッドを使って、(現状ではまだ) 変数sが回文ではないことを確認してください。downcaseメソッドを使って、s.downcaseは回文であることを確認してください。
</li>
<li>
aからzまでの範囲オブジェクトを作成し、7番目の要素を取り出してみてください。同様にして、後ろから７番目の要素を取り出してみてください。(ヒント: 範囲オブジェクトを配列に変換するのを忘れないでください)
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#１．
>> a = "A man, a plan, a canal, Panama".split(',')  #,で分割した配列に
=> ["A man", " a plan", " a canal", " Panama"]

#2.
>> s = a.join                                       #配列を連結
=> "A man a plan a canal Panama"

#3.
>> s = "A man a plan a canal Panama".split.join     #半角スペースで分割して配列化→連結（結果スペースがなくなる）
=> "AmanaplanacanalPanama"
>> s == s.reverse 
=> false　　　　　　　　　           　#大文字と小文字が揃わないのでfalse
>> s.downcase == s.downcase.reverse  #downcaseメソッドで全て小文字にして比較
=> true

#4.
>> a = ('a'..'z').to_a
=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z"]
>> a[6]   #「インデックス番号７の要素」ではなく「７番目の要素」なので、取り出したいインデックス番号は「6」
=> "g"
>> a[-7]  #「後ろから７番目」の要素
=> "t" </pre>
<h3>4.3.2 ブロック</h3>
<blockquote class="quote-rort">
配列と範囲はいずれも、ブロックを伴うさまざまなメソッドに対して応答することができます。
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#1行で記述できる短いブロックは｛｝で囲う
>> (1..5).each { |i| puts 2 * i }
#1～5を順に取り出してブロック（{}内）を実行　|取り出した要素を変数iに代入| 2×iを出力
2
4
6
8
10
=> 1..5

#複数行の時はdo..endで囲う
>> (1..5).each do |number|  #1～5を順に取り出してブロックを実行 ブロック開始（do）　|取り出した要素を変数numberに代入|
?>    puts 2 * number　　   #2×numberを出力
>>    puts '--'             #--を出力
>> end                      #ブロック終了
2
--
4
--
6
--
8
--
10
--
=> 1..5</pre>
その他のブロックの使用例
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> 3.times { puts "Betelgeuse!" }   #ブロック内の処理を指定回数繰り返す
"Betelgeuse!"
"Betelgeuse!"
"Betelgeuse!"
=> 3
>> (1..5).map { |i| i**2 }          #オブジェクトへ順番に処理を実行する　|処理する要素を変数iに代入| iの２乗
=> [1, 4, 9, 16, 25]
>> %w[a b c]                        #abcを配列に
=> ["a", "b", "c"]
>> %w[a b c].map { |char| char.upcase }　　#abcを配列にして順番に処理　|処理する要素を変数charに代入|charを大文字に
=> ["A", "B", "C"]
>> %w[A B C].map { |char| char.downcase }  #ABCを配列にして順番に処理　|処理する要素を変数charに代入|charを小文字に
=> ["a", "b", "c"]
#上記↑は下記↓の様に省略した記法が一般的
>> %w[A B C].map(&:downcase)
=> ["a", "b", "c"]</pre>
<h4>&:とはなんぞ</h4>
[blogcard url="https://qa.atmarkit.co.jp/q/35"]
こちらの回答を読んでわかったような気持ちになりますが気持ちだけかもしれません（？
一先ず「(&:メソッド) → {|変数|変数.メソッド}の形のブロックの事」と思っていれば良さそうです
<h4>テストのコード</h4>
<pre class="lang:ruby mark:(1-1,5-5) decode:true " title="見本のコード" >test "should get home" do
  get static_pages_home_url
  assert_response :success
  assert_select "title", "Ruby on Rails Tutorial Sample App"
end
</pre> 
→テストはブロックで出来ている
<h4>ランダムな英小文字8文字を生成するコード</h4>
<a href="https://railstutorial.jp/chapters/beginning?version=5.1#sec-heroku_commands">1.5.4</a>で出てきたコードを読み解く
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> ('a'..'z').to_a                    #a~Zの英小文字の配列
=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o","p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z"]
>> ('a'..'z').to_a.shuffle             #シャッフル
=> ["c", "g", "l", "k", "h", "z", "s", "i", "n", "d", "y", "u", "t", "j", "q","b", "r", "o", "f", "e", "w", "v", "m", "a", "x", "p"]
>> ('a'..'z').to_a.shuffle[0..7]       #配列のインデックス番号0～７を取り出す
=> ["f", "w", "i", "a", "h", "p", "c", "x"]
>> ('a'..'z').to_a.shuffle[0..7].join  #取り出した要素を結合して１つの文字列に
=> "mznpybuj"</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
範囲オブジェクト0..16を使って、各要素の２乗を出力してください。
</li>
<li>
yeller (大声で叫ぶ) というメソッドを定義してください。このメソッドは、文字列の要素で構成された配列を受け取り、各要素を連結した後、大文字にして結果を返します。例えばyeller([’o’, ’l’, ’d’])と実行したとき、"OLD"という結果が返ってくれば成功です。ヒント: mapとupcaseとjoinメソッドを使ってみましょう。
</li>
<li>
random_subdomainというメソッドを定義してください。このメソッドはランダムな8文字を生成し、文字列として返します。ヒント: サブドメインを作るときに使ったRubyコードをメソッド化したものです。
</li>
<li>
リスト 4.12の「?」の部分を、それぞれ適切なメソッドに置き換えてみてください。ヒント:split、shuffle、joinメソッドを組み合わせると、メソッドに渡された文字列 (引数) をシャッフルさせることができます。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#1.
>> (0..16).each do |i|  #0～16を　順番に取り出して　iに代入
?>   puts i**2          #iの2乗を出力
>> end
0
1
（中略）
256
=> 0..16

#2.
>> def yeller(a)　　#メソッドyeller（引数a)
>>   a.map(&:upcase).join  #aの内容を順番に処理する→大文字にする　←これを繋げて文字列に
>> end
=> :yeller
>> yeller(['o','l','d'])　　#処理する値
=> "OLD"
#mapを使わなくても行ける（らくだ🐫は先にこっちを考えたけど設問ではmapを使うとあるので上記に）
>> def yeller(a)
>>   a.join.upcase　　#を繋げて文字列に　←これを大文字に　（aの内容は配列なのでa.upcaseでは×）
>> end
=> :yeller
>> yeller(['o','l','d'])
=> "OLD"

#3.
>> def random_subdomain
>>   ('a'..'z').to_a.shuffle[0..7].join
>> end
=> :random_subdomain
>> random_subdomain
=> "ajpvfbce"
>> random_subdomain
=> "ezfciosn"

#4.
>> def string_shuffle(s)
>>   s.split('').shuffle.join　　#splitメソッドは引数に('')（空白の文字列）を指定する事で一文字ごとに分割した配列を返してくれる　←これをシャッフル　←繋げて文字列に
>> end
=> :string_shuffle
>> string_shuffle("foobar")
=> "obofar"　　　　　#foobarがシャッフルされるようなコードを作る
>> string_shuffle("rakudasan")                 #他の文字でも試してみる
=> "dsarauank"
</pre>
<h3>4.3.3 ハッシュとシンボル</h3>
整数以外の文字列などをインデックスとして設定できる（キーと呼ぶ・通常は何かしらのオブジェクト）
キーとその値を{}で囲んだ表記をハッシュと呼ぶ
→ハッシュの{}とブロックの{}は別物である
→ハッシュでは要素の並び順が保証されない（配列は保証される）
→Railsではハッシュのキーにシンボルを使うのが一般的
→シンボルには記号は使えない
→シンボルには数字から始まる文字列は使えない　×2foo ○foo2
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> user = { :name => "Michael Hartl", :email => "michael@example.com" }
#キーnameの値に"Michael Hartl"、キーemailの値に"michael@example.com"　二つの要素が入ったハッシュをuserに代入
=> {:name=>"Michael Hartl", :email=>"michael@example.com"}
>> user[:name]              #:name に対応する値を呼び出す
=> "Michael Hartl"
>> user[:password]          
=> nil                      #定義されていない値を呼び出そうとするとnilが返ってくる

#上記のハッシュを更に省略した形
>> user = {name: "Michael Hartl", email: "michael@example.com" }
=> {:name=>"Michael Hartl", :email=>"michael@example.com"}

#ハッシュの値に他のハッシュを使うことも出来る
>> params = {}        #paramsというハッシュを定義する
=> {}
>> params[:user] = { name: "Michael Hartl", email: "mhartl@example.com" }
#paramsにキー:userを設定し、値にハッシュを使う
=> {:name=>"Michael Hartl", :email=>"mhartl@example.com"}
>> params  #paramsを呼び出すとハッシュが入れ子になっているのがわかる
=> {:user=>{:name=>"Michael Hartl", :email=>"mhartl@example.com"}}
>>  params[:user][:email]  #paramsの キー:userの キー:emailの値を取り出す
=> "mhartl@example.com"
</pre>
<h4>ハッシュとeachメソッド</h4>
ハッシュに対してeachメソッドを実行すると
そのハッシュが持つキーと値のペアごとに処理を繰り返す
<pre class="lang:default decode:true " title="ターミナル(コンソール)" >>> flash = { success: "It worked!", danger: "It failed." }  #flashに2つのキーと値のペアを持つハッシュを代入
=> {:success=>"It worked!", :danger=>"It failed."}
>> flash.each do |key, value|　　#flashから要素を順に取り出して実行　キーと値をそれぞれ変数として設定
?>    puts "Key >#{key.inspect} has value #{value.inspect}"　　#この内容を出力→　kye キーの要素を変数展開　has value　値の要素を変数展開
>> end
Key :success has value "It worked!"  #1つ目のキーと値のペアの要素で出力
Key :danger has value "It failed."   #2つ目のキーと値のペアの要素で出力
=> {:success=>"It worked!", :danger=>"It failed."}</pre>
<h4>inspectメソッドとはなんぞ</h4>
対象の型通りの文字列を返すメソッド
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >>> puts (1..5).to_a            #配列を出力　とすると、内容が1つずつ出力される
1
2
3
4
5
>> puts (1..5).to_a.inspect    #inspectメソッドを使うと配列の形そのままで出力される
[1, 2, 3, 4, 5]

>> puts :name, :name.inspect
name　　#:nameを出力　→　name
:name  #inspectメソッドを使うとそのままの形で出力されキーであることがわかる
>> puts "It worked!", "It worked!".inspect
It worked!  #文字列"It worked!"を出力すると　It worked!
"It worked!"　　#inspectメソッドを付けることで文字列を表す型""が残ったまま出力される

#inspectメソッドにはショートカットがある　→　Pメソッド
>>p :name             #'puts :name.inspect'　の短縮形
:name
</pre>
<h4>演習</h4>
<blockquote class="quote-rort">
<ol>
<li>
キーが’one’、’two’、’three’となっていて、それぞれの値が’uno’、’dos’、’tres’となっているハッシュを作ってみてください。その後、ハッシュの各要素をみて、それぞれのキーと値を"’#{key}’のスペイン語は’#{value}’"といった形で出力してみてください。
</li>
<li>
person1、person2、person3という３つのハッシュを作成し、それぞれのハッシュに:firstと:lastキーを追加し、適当な値 (名前など) を入力してください。その後、次のようなparamsというハッシュのハッシュを作ってみてください。1.) キーparams[:father]の値にperson1を代入、2). キーparams[:mother]の値にperson2を代入、3). キーparams[:child]の値にperson3を代入。最後に、ハッシュのハッシュを調べていき、正しい値になっているか確かめてみてください。(例えばparams[:father][:first]がperson1[:first]と一致しているか確かめてみてください)
</li>
<li>
userというハッシュを定義してみてください。このハッシュは３つのキー:name、:email、:password_digestを持っていて、それぞれの値にあなたの名前、あなたのメールアドレス、そして16文字からなるランダムな文字列が代入されています。
</li>
<li>
Ruby API (訳注: もしくはるりまサーチ) を使って、Hashクラスのmergeメソッドについて調べてみてください。次のコードを実行せずに、どのような結果が返ってくるか推測できますか? 推測できたら、実際にコードを実行して推測があっていたか確認してみましょう。
</li>
</ol>
</blockquote>
<pre class="lang:default decode:true " title="ターミナル（コンソール）" >#1.
>> language = {one:"uno" , two:"dos" , three:"tres" }  #languageにハッシュを代入
>> language.each do |key, value|                       #each文で取り出し
?>   puts "#{key}のスペイン語は#{value}".inspect        #"’#{key}’のスペイン語は’#{value}’"とあるのでinspectメソッドでクォートも出力
>> end
"oneのスペイン語はuno"
"twoのスペイン語はdos"
"threeのスペイン語はtres"

#2.
#person1、person2、person3という３つのハッシュを作成
>> person1 = {}
=> {}
>> person2 = {}
=> {}
>> person3 = {}
=> {}
#:firstと:lastキーとそれぞれの値を入力
>> person3[:first] = "Child"
=> {}
>> person1[:first] = "Papa"
=> "Papa"
>> person1[:last] = "Rakuda"
=> "Rakuda"
>> person2[:first] = "Mama"
=> "Mama"
>> person2[:last] = "Rakuda"
=> "Rakuda"
>> person3[:first] = "Child"
=> "Child"
>> person3[:last] = "Rakuda"
=> "Rakuda"
#paramsというハッシュのハッシュ
>> params = {father:person1 , mother:person2 , child:person3 }
=> {:father=>{:first=>"Papa", :last=>"Rakuda"}, :mother=>{:first=>"Mama", :last=>"Rakuda"}, :child=>{:first=>"Child", :last=>"Rakuda"}}
#値が正しいかの確認
>> params[:father][:first] == person1[:first]
=> true

#3.
#せっかく（？）なので数字も混ぜたランダムな文字列を作ってみる
>>  ('a'..'z').to_a.push((0..9).to_a).shuffle[0..15].join
=> "ryxwndsftqkzg0123456789ph"
#数字がシャッフルされていない！
 ('a'..'z').to_a.push((0..9).to_a)
=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z", [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]]
#pushは要素を追加するメソッドなので 0～9の要素 として追加される
>>  ('a'..'z').to_a.concat((0..9).to_a)
=> ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z", 0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
#concatで追加すると出来た（文字列や配列同士を 結合させる メソッド）
#よって下記でランダムな16文字を生成
>> ('a'..'z').to_a.concat((0..9).to_a).shuffle[0..15].join
=> "jku8x1lv5r7bgwc6"
>> user = {name:"rakuda" , email:"rakuda@mail.com" , password_digest:"jku8x1lv5r7bgwc6"}
=> {:name=>"rakuda", :email=>"rakuda@mail.com", :password_digest=>"jku8x1lv5r7bgwc6"}

#4.
#重複するbは引数の値が適用される　→　"a" => 100, "b" => 300
#コンソールでコードを実行して確認↓
>> { "a" => 100, "b" => 200 }.merge({ "b" => 300 })
=> {"a"=>100, "b"=>300}</pre>
<h3>4.3.4 CSS、再び</h3>
レイアウトにCSSを追加する行の解説
<pre class="lang:ruby mark:(6-6) decode:true " title="/sample_app/app/views/layouts/application.html.erb" ><!DOCTYPE html>
<html>
  <head>
    <title><%= full_title(yield(:title)) %></title>
    <%= csrf_meta_tags %>
    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <%= yield %>
  </body>
</html></pre> 
読めるような読めないような。見慣れた形とちょっと違いますよね。
()とか{}が無い
<pre class="lang:ruby decode:true " title="コードの解説">
#メソッド（引数）
stylesheet_link_tag('application', media: 'all', 'data-turbolinks-track': 'reload')
#メソッドを呼び出す際に丸カッコは省略可能
stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload'

#ハッシュを囲む{}
stylesheet_link_tag 'application', { media: 'all',　'data-turbolinks-track': 'reload' }
#最後の引数がハッシュの場合、波カッコは省略可能
stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload'

#改行と空白は区別されないため、見やすいように改行している(意図的な改行は\n）
stylesheet_link_tag 'application', media: 'all',
                                   'data-turbolinks-track': 'reload'</pre>
<h4>stylesheet_link_tagメソッド</h4>
<a href="http://railsdoc.com/references/stylesheet_link_tag" rel="noopener noreferrer" target="_blank">stylesheet_link_tag</a>
stylesheet_link_tag(スタイルシートへのパス [, HTMLオプション])の形で使用する
上記のコードでは'application'がパスの指定
ハッシュの要素media: 'all', 'data-turbolinks-track': 'reload'がHTMLオプション
これによって生成されたHTMLソースがこちら↓
<pre class="lang:default decode:true " title="生成されたHTMLソース" >><link data-turbolinks-track="true" href="/assets/application.css"
media="all" rel="stylesheet" /></pre>
<h3>まとめとか感想</h3>
長かった。ひたすら長かった。
でもとても勉強になった気持ちです。
気持ち大切。
<h5>らくだ🐫にもできるRailsチュートリアルとは</h5>
「ド」が付く素人のらくだ🐫が勉強するRailsチュートリアルの学習記録です。
自分用に記録していますが、お役に立つことがあれば幸いです。

調べたとはいえらくだ🐫なりの解釈や説明が含まれます。間違っている部分もあるかと思います。そんな所は教えて頂けますと幸いなのですが、このブログにはコメント機能がありません💧お手数おかけしますがTwitterなどでご連絡いただければ幸いです

Rubyで例のシェル操作課題をやる。ワンライナーで。

## はじめに
シェル操作課題 (cut, sort, uniq などで集計を行う) 設問編
http://d.hatena.ne.jp/Yamashiro0217/20120727/1343371036

* とりあえずURLを開こう
* 昨日知った
* 解答はまだ見てない
* いろんな人が解いてそうなので、それを見るのも面白いかもしれない
* ファイル名をjagariko.logとする
* 特に意味はない
* そこにじゃがりこ(サラダ)があったのだ!
* ファイルの形式: カンマ区切りで「サーバ名,unixtime,ユーザID,アクセス先」
* どんなのかはURLのページを確認しよう
* なんかシェルとかいう文字列が見えるが気にしない
* Rubyのワンライナーで解く
* 書いてたらワンライナーではなくなった
* たぶんgistか何かにあげる
* githubにリポジトリ作った
* github.com/furu/shell-sousa-kadai
* 別解やリファクタリングをオナシャス
* Ruby書けないです><
* 「私のRuby戦闘力は100だ!」(基準は不明)
* へぼい
* こう言っとけばなんとかなる
* Start!


## ワンライナーを書くための予備知識

* -e 'command':ワンライナーの実行
* -O[octal]:行末文字の指定
* -a:オートスプリットモード
* -Fpattern:オートスプリットモードでの区切りパターンの指定
* -i[extension]:引数で指定したファイルの編集
* -Idirectory:$LOAD_PATHの追加
* -l:行末処理指定
* -n:ループ内でのスクリプトの実行
* -p:ループ内でのスクリプトの実行と自動出力
* -rlibrary:実行前にライブラリを読み込む

(参考:たのしいRuby)


## 問1 このファイルを表示しろ

```ruby
ruby -p -e '' jagariko.log
```

### puts

* 末尾が改行で終わっている引数に対しては、改行を出力しない

### print

* 引数が与えられない時には変数$_の値を出力する


## 問2 このファイルからサーバ名とアクセス先だけ表示しろ

```ruby
ruby -paF, -e '$_="#{$F[0]},#{$F[3]}"' jagariko.log

ruby -paF, -e '$_="%s,%s" % [$F[0], $F[3]]' jagariko.log

ruby -paF, -e '$_="%s,%s" % $F.values_at(0,3)' jagariko.log

ruby -pe '$_.sub!(/,.*,/,",")' jagariko.log
```

* chomp!


## 問3 このファイルからserver4の行だけ表示しろ

```ruby
ruby -n -e 'print if $_.include?("server4")' jagariko.log

ruby -n -e 'print if /server4/ =~ $_' jagariko.log
```

* パターンってどっちに置いたほうが読みやすいん?


## 問4 このファイルの行数を表示しろ

```ruby
ruby -e 'puts File.read("jagariko.log").split.size'

ruby -e 'count = 0; File.open("jagariko.log") {|io| io.each_line {|line| count += 1 }}; puts count'
```

* IO#lineno
* $.
* String#.split
* $;
* open or File.openとFile.read
* ファイルサイズが大きい場合とか

## 問5 このファイルをサーバ名、ユーザIDの昇順で5行だけ表示しろ

```ruby
ruby -e "puts File.read('jagariko.log').split.map {|f| f.split(',') }.sort {|a, b| (a.first <=> b.first).nonzero? || a[2].to_i <=> b[2].to_i }.map {|a| a.join(',') }[0..4]"
```

* 降順どうすんの


## 問6 このファイルには重複行がある。重複行はまとめて数え、行数を表示しろ

```ruby
ruby -e 'puts File.read("jagariko.log").split.uniq.size'
```


## 問7 このログのUU(ユニークユーザ)数を表示しろ

```ruby
ruby -e 'puts File.read("jagariko.log").split.map {|a| a.split(",")[2] }.uniq.size'
```


## 問8 このログのアクセス先ごとにアクセス数を数え、上位1つを表示しろ

```ruby
ruby -e 'pages = Hash.new(0); File.read("jagariko.log").split.map {|a| a.split(",")[3] }.map {|p| pages[p] += 1 }; pm = pages.max {|a, b| a.last <=> b.last }; puts "%s %s" % [pm.last, pm.first]'
```

* もはやワンライナーとは(ry


## 問9 このログのserverという文字列をxxxという文字列に変え、サーバ毎のアクセス数を表示しろ

```ruby
ruby -e 'server_names = Hash.new(0); File.open("jagariko.log") {|io| io.each_line {|line| server_names[line.split(",").first] += 1 }}; server_names.each {|k, v| puts "%s %s" % [v, k.sub(/server/, "xxx")] }'
```


## 問10 このログのユーザIDが10以上の人のユニークなユーザIDをユーザIDでソートして表示しろ

```ruby
ruby -e 'user_ids = []; File.open("jagariko.log") {|io| io.each_line {|line| user_ids << line.split(",")[2] }}; puts user_ids.uniq.select {|id| id.to_i >= 10 }.sort'
```


## まとめ

* ワンライナーもっと楽に書きたい
* \<C-X>\<C-E>
* 普通にシェルのコマンドでやりましょう
* 私は普段やってないので、たぶんできないです
* Ruby勉強会ですし、ネタないしですし、おすし
* わんらいなーまともにさわったことがなかったので、べんきょうになりましたまる

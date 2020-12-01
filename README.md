

### Map の作り方

Map の作成方法を理解する。
Map という名前がついてる為キャラクタとは異なるモノを連想しがちではあるが、
基本的に Map も画像を並べているだけ (障害物や人等の意味合いをもたせるのはあくまでもプログラムの仕事) なので、
自分が表現したいように画像を並べる方法を覚えると、Map を生成することが出来る。

実際に、画像を並べる方法として、今まで行った方法のみを使い Map を生成すると、半端な数しか生成できていないが以下の様なプログラムになる。


```ruby
require 'dxruby'

image = Image.load_tiles("../image/colorbox.png", 6, 1)

gray1 = Sprite.new(0,0,image[5])
gray2 = Sprite.new(20,0,image[5])
gray3 = Sprite.new(40,0,image[5])
gray4 = Sprite.new(60,0,image[5])
gray5 = Sprite.new(80,0,image[5])
gray6 = Sprite.new(100,0,image[5])
gray7 = Sprite.new(120,0,image[5])
gray8 = Sprite.new(140,0,image[5])
gray9 = Sprite.new(160,0,image[5])
gray10 = Sprite.new(180,0,image[5])
gray11 = Sprite.new(200,0,image[5])

Window.loop do
  Sprite.draw(gray1)
  Sprite.draw(gray2)
  Sprite.draw(gray3)
  Sprite.draw(gray4)
  Sprite.draw(gray5)
  Sprite.draw(gray6)
  Sprite.draw(gray7)
  Sprite.draw(gray8)
  Sprite.draw(gray9)
  Sprite.draw(gray10)
end
```

上記プログラムを実行してみると、
書いたプログラムの行数に比例した量のブロックのみ
表示されていることが分かる。
これでは、本格的なゲームだけでなく、
簡単なゲームを作るだけでもかなりの労力を伴う。
その為、プログラミングの世界では、繰り返しという概念を用いて、法則性の在る作業を自動化する。

上記プログラムを自動化すると下記のようになる。

```ruby
require 'dxruby'

blocks = Image.load_tiles("../image/colorbox.png", 6, 1)


# 全て大文字で書かれたプログラムは、定数として扱われる
# 定数は、一度だけ代入することが出来る変数で、値に名前をつけておきたい時に使う
# 値が変動することのないパラメータで使用する

# ブロックのサイズ定数
X = 20
Y = 20

# ゲーム画面の端から空きスペースを確保する定数 (バッファー)
X_BUFFER = 30
Y_BUFFER = 20

GRAY = 5

# Map の形を作っている配列
# 0 が背景用のブロック、1 が背景として描画する
map_tmp = [
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,1,1,1,1,1,1,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,1,1,1,1,1,1,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
      ]

map = []

# 繰り返し文
# for 変数 in 条件式 ~ end の形で書く
# 配列を条件式の部分に書くと、中身を１つずつ取り出して
# 変数にセットし、~ の部分を実行する
# end に処理が到達すると、次の変数をセットして配列の中身を全て取り出すまで繰り返す

# 数字..数字 という .. で数字をつなぐ書き方をすると、左辺から右辺まで (右辺含む) の数値を
# 配列にまとめる
# 例) 0..10  #=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# .length は配列の後ろ (配列用変数でも可能) につける事で配列の要素の数を返す
# 下記のプログラムでは二次元配列になっている配列の一次元目の長さを調べている
for i in (0..(map_tmp.length - 1))
  for j in (0..(map_tmp[0].length - 1))
    # 以下二行は式が長い為、変数を作り、後で一行に書く文字数を減らしている
    x_block = X_BUFFER + X * j
    y_block = Y_BUFFER + Y * i

    # map 配列の全ての値を 1 つずつ調べて値が 0 の位置にスプライトデータを代入する
    if map_tmp[i][j] == 0
      map.push(Sprite.new(x_block, y_block, blocks[GRAY]))
    end
  end
end

Window.loop do
  Sprite.draw(map)
end
```

今回のプログラムでは、Map を作成しているのだが、その Map の表現に
配列が使われていることが分かる。
さらに、繰り返しを用いる事で、配列にて用意された Map データを容易に再現できている。
今回 map, map_tmp と二つの配列を作成した。

map はゲーム上に実際に表示する為に使用する Sprite データを配列に入れている。
衝突判定の話で、`===` を使用すると判定を取れると説明したが、
衝突判定は配列を対象にしてもとることが出来る。
ただし、配列の中身が全て Sprite データで在ることが条件である。
一つのキャラクタと Map の衝突を判定したい場合はそれぞれを比較することで可能となる。
map_tmp は Map データの下書きみたいなもので、描画したい形と同じように配列のデータを設定する。
配列内の数値に関しては、後に使用しているため配列定義時点では特に意味は無い。
今回は配置するブロックに灰色のブロックを使用するため、定数を用いて設定を行った。

定数とは、値が変化しないことを約束された変数の事で、基本的には変化させてはいけない。
Ruby では警告が出るものの二度以上代入することが出来る。

ここまでを理解すると、テトリスのワンブロックを積み上げる部分迄を作成することができる。

### ブロックの積み上げ機能

先ほど作成した Map に先週行ったキャラクタの作成と移動を使用し、
ブロックを積み上げられるようにする。
また、ブロックを新しく作成する機能と、色をランダムに選択する機能を更に新しくつける。

```ruby
require 'dxruby'

# def 名前(変数, 変数, ...) 
# ~
# end
# 変数の定義と同じで、メソッドにも好きな名前を指定することができる
# また、( ) かっこ内に変数を指定でき、そこで指定した変数は
# メソッドを呼び出す際に必ず対応した値を入れる必要がある
# 例) 名前(変数, 変数) 
# この処理を行った時、メソッドで最後に実行した行が返す値が
# メソッドを実行した行に対して置き換わる
# また、return という文を使用することで、メソッドを強制的に終了させ、
# 指定した値を返すことも出来る
def create(block, x=X_BUFFER+X+50, y=Y_BUFFER)
  Sprite.new(x, y, block)
end

Window.caption = 'OneBlockTetris'
Window.width = 500
Window.height = 600

X = 20
Y = 20

X_BUFFER = 30
Y_BUFFER = 100

NEXT_PRINT_X = X_BUFFER + X * 14 + 10
NEXT_PRINT_Y = Y_BUFFER + Y * 17 + 10
NEXT_FONT = Font.new(19)

RED = 0
BLUE = 1
GREEN = 2
ORANGE = 3
YELLOW = 4
GRAY = 5

clear = false
current_block = nil
next_block = rand(5)
next_block_view = nil

blocks = Image.loadTiles('../image/colorbox.png', 6, 1)

map_tmp = [
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,1,1,1,1,1,1,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,1,1,1,1,1,1,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
      ]

map = []

for i in (0..(map_tmp.length-1))
  for j in (0..(map_tmp[0].length-1))
    map.push(Sprite.new(X_BUFFER + X * j, Y_BUFFER + Y * i, blocks[GRAY])) if map_tmp[i][j] == 0
  end
end

Window.loop do
  # unless 文は if 文の反対で、条件式が false, nil を返す時だけ中の処理を実行する
  # また、この条件文内では、current_block という変数を定義して、
  # next_block (次のブロック候補) からブロックをコピーする
  unless current_block
    current_block = create(blocks[next_block])
    next_block = rand(5)
    next_block_view = create(blocks[next_block], NEXT_PRINT_X+10, NEXT_PRINT_Y+30)
  end

  if Input.keyDown?(K_H)
    current_block.x -= 2
    if current_block === map
      current_block.x += 2
    end
  end

  if Input.keyDown?(K_L)
    current_block.x += 2
    if current_block === map
      current_block.x -= 2
    end
  end

  if Input.keyDown?(K_J)
    current_block.y += 2
    if current_block === map
      current_block.y -= 2
    end
  end

  if current_block === map
    current_block.y -= 1
    map.push(current_block)
    clear = clean(map)
    current_block = nil
  else
    current_block.y += 1
  end

  if current_block
    current_block.draw 
    Sprite.draw(map)
  end
end
```

上記プログラムでは、ブロックの作成、次に作成するブロックの作成、表示を行っている。
そして、新しく自分自身でメソッドを定義している。
今までは既に定義されているメソッドを使用していたが、
ブロックを生成するメソッドは存在しないため、自分で作る必要がある。
作らず、同様の処理を必要としている場所に書くという方法もあるが、
それをしてしまうと、同様の処理を何度も書く必要がでて無駄が生じる。
その為、処理に名前をつけて記録する必要がある。


### Clear 処理

ゲームを作成する上で最終目標を設定する事がよくある。
勿論最終目標が無く、サバイバル的に続けられる分だけ続けるゲームも存在する。
今回の 1 ブロックのテトリスでは、一行を揃えるだけでクリアとしたいので、
クリア用のプログラムを作成する。

```ruby
require 'dxruby'

def create(block, x=X_BUFFER+X+50, y=Y_BUFFER)
  Sprite.new(x, y, block)
end

def clean(map)
  20.downto(0) do |height|
    if map.count {|block| (block.x < X_BUFFER + X * 11) && (block.y == Y_BUFFER + Y * height)} == 11
      return true
    end
  end
  false
end

Window.caption = 'OneBlockTetris'
Window.width = 500
Window.height = 600

X = 20
Y = 20

X_BUFFER = 30
Y_BUFFER = 100

NEXT_PRINT_X = X_BUFFER + X * 14 + 10
NEXT_PRINT_Y = Y_BUFFER + Y * 17 + 10
NEXT_FONT = Font.new(19)

RED = 0
BLUE = 1
GREEN = 2
ORANGE = 3
YELLOW = 4
GRAY = 5

clear = false
current_block = nil
next_block = rand(5)
next_block_view = nil

blocks = Image.loadTiles('../image/colorbox.png', 6, 1)

map_tmp = [
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,1,1,1,1,1,1,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,1,1,1,1,1,1,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,0,0,0,0],
       [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
      ]

map = []

for i in (0..(map_tmp.length-1))
  for j in (0..(map_tmp[0].length-1))
    map.push(Sprite.new(X_BUFFER + X * j, Y_BUFFER + Y * i, blocks[GRAY])) if map_tmp[i][j] == 0
  end
end

Window.loop do
  # ゲームを終了する用のキーを追加
  # Q キーを押すと終了
  # break 文は普通繰り返しを強制的に終了する為に使用する
  # この場合では、ゲームを動かしているループを終了するため
  # ゲームが終了する
  if Input.keyPush?(K_Q)
    break
  end

  # clear 変数が false の時はクリア状態を満たしていないため else の部分を実行する
  # true になると if clear の内部を実行する
  if clear
    Window.drawFontEx(100, 300, 'CLEAR!!!', Font.new(70), { font: [255, 255, 255] })
    if Input.keyPush?(K_RETURN)
      break
    end
  else
    unless current_block
      current_block = create(blocks[next_block])
      next_block = rand(5)
      next_block_view = create(blocks[next_block], NEXT_PRINT_X+10, NEXT_PRINT_Y+30)
    end

    if Input.keyDown?(K_H)
      current_block.x -= 2
      if current_block === map
        current_block.x += 2
      end
    end

    if Input.keyDown?(K_L)
      current_block.x += 2
      if current_block === map
        current_block.x -= 2
      end
    end

    if Input.keyDown?(K_J)
      current_block.y += 2
      if current_block === map
        current_block.y -= 2
      end
    end

    if current_block === map
      current_block.y -= 1
      map.push(current_block)
      clear = clean(map)
      current_block = nil
    else
      current_block.y += 1
    end

    current_block.draw if current_block
    Window.drawFontEx(NEXT_PRINT_X, NEXT_PRINT_Y, 'Next', NEXT_FONT, { font: [255, 255, 255] })
    Sprite.draw(next_block_view)
    Sprite.draw(map)
  end
end
```
今回のプログラムが正常に動作すれば演習は終了である。
最後の追加項目は、`clean` メソッドの追加とゲームのシーンの設定である。
`clean` メソッドは、ブロックが、一行揃っているかを調べ、揃っていれば true、
揃っていなければ false を返す。

そして、ゲームのシーンの決定に if 文 (条件分岐) を使い、
clean メソッドの実行結果が true になった時に、Clear の文字を入れるようにしている。

実際に動作が確認出来たなら、クリアの文字が表示できるか確かめてみよう。

# 続き

第 2 週目の授業内容は、前回よりも内容が複雑で、
コピー & ペーストでコードを書いていってもうまくいかないことが予想されます。
その為、プログラムを書き換える前に、
上手く動作していた時のバックアップファイルを作るようにしてください。

どうしても上手く動作しない時はバックアップから元に戻します。
授業の進め方が普通の授業とは異なるために、自分で先へと進められますが
基本的には授業の速度に合わせてください。
複雑な話やプログラムの解説等を行うタイミングは、
基本的に初めて使うものが出てきた時に行うので
自分で先々進めるとどこを説明されているのかわからなくなります。

まぁそういう人は、大抵この最初の文章を読まないと思いますけど...


後は、正直前回と今回で行う内容を完全に理解することは無理だと思います。
なので、理解して欲しいポイントは、
物を作る時に考えるべき事です。
ゲームを作るならタイトルがあり、
ゲームとしての内容(どのようなしょぼい内容でも可)があり、
ゲームが正常に終了する事が第一です。

その後で、ゲームに必要な物を考えていく。
最初はこの流れで問題無いです。

後は、今回はプログラミング言語 Ruby を使用していますが、
基本的にどの言後にも共通している考え方を使って作ることを目指しています。
なので、細かい処理は置いといて、全体像を掴んでください。
今は言語を理解しなくても問題ありません。

因みに自分でコードを書き換えてみるのも勉強のうちなので、積極的に変更しましょう。
ただし、プログラムのバックアップを取るのは忘れないようにしてください。


1、ブロックを作成

配列を使ってブロックの形を作ります。
2 がブロック、1 が背景として画像がそれぞれ割り当てられている。

ここでは 2 次元配列という配列が使われており、
配列を 2 次元で使うことで横方向と縦方向のデータを持つことができる。
今回のテトリスのようなゲームでは、書いた配列がそのままゲームの見た目に反映される。

例えば、下記の配列では

```
          二次元
          0 1 2 3
         ---------
    一 0|[2,1,1,1],
    次 1|[2,1,1,1],
    元 2|[2,2,1,1],
       3|[1,1,1,1]
```

```
      二次元
      0 1 2 3
     ---------
一 0|[2,1,1,1],
次 1|[2,1,1,1],
元 2|[2,2,1,1],
   3|[1,1,1,1]
```

この 配列では L 字のブロックを作成。
点線の外枠は配列の順番、横の並びが一次元配列、縦が二次元配列になってる。

例えば、array という配列があるとして、
一つ目の [] が一次元、２つ目の [] が二次元の指定になる。

上記の L 字のブロックが入ってるとして、
array[1][0] と指定すると
array 配列の持つ
一次元目の 1 という場所と、
二次元目の 0 という場所から値を取り出す。
結果は 2 が取り出せる。

この配列を逐一マップに重ね合わせて使うことで、
テトリスのマップにブロックを描く。


- 以下のコードはブロックの形を定義する
ゲームマップのコードの下に書く。
この配列内の数値を変更するとブロックの形を変更できる。

以下のコードを書く。

ブロック描画用空間のサイズ (4x4)

```ruby

 BLOCK_SPACE = 4
 
 BLOCK_TYPE = {
   "_|" => [
         [
          [2,1,1,1],
          [2,2,2,1],
          [1,1,1,1],
          [1,1,1,1],
         ],
       
        [
         [2,2,1,1],
         [2,1,1,1],
         [2,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [2,2,2,1],
         [1,1,2,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [1,2,1,1],
         [1,2,1,1],
         [2,2,1,1],
         [1,1,1,1]
        ]
       ],

 "L" => [
         [
          [1,1,2,1],
          [2,2,2,1],
          [1,1,1,1],
          [1,1,1,1]
         ],
         
         [
          [2,1,1,1],
          [2,1,1,1],
          [2,2,1,1],
          [1,1,1,1]
         ],
        
         [
          [2,2,2,1],
          [2,1,1,1],
          [1,1,1,1],
          [1,1,1,1]
         ],
         
         [
          [2,2,1,1],
          [1,2,1,1],
          [1,2,1,1],
          [1,1,1,1]
         ]
        ],

 "Z" => [
        [
         [2,2,1,1],
         [1,2,2,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [1,2,1,1],
         [2,2,1,1],
         [2,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [2,2,1,1],
         [1,2,2,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [1,2,1,1],
         [2,2,1,1],
         [2,1,1,1],
         [1,1,1,1]
        ]
      ],

 "S" => [
        [
         [1,2,2,1],
         [2,2,1,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [2,1,1,1],
         [2,2,1,1],
         [1,2,1,1],
         [1,1,1,1]
        ],
        
        [
         [1,2,2,1],
         [2,2,1,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [2,1,1,1],
         [2,2,1,1],
         [1,2,1,1],
         [1,1,1,1]
        ]
       ],

 "@" => [
        [
         [2,2,1,1],
         [2,2,1,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [2,2,1,1],
         [2,2,1,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [2,2,1,1],
         [2,2,1,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [2,2,1,1],
         [2,2,1,1],
         [1,1,1,1],
         [1,1,1,1]
        ]
       ],

 "T" => [
        [
         [2,2,2,1],
         [1,2,1,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [1,2,1,1],
         [2,2,1,1],
         [1,2,1,1],
         [1,1,1,1]
        ],
        
        [
         [1,2,1,1],
         [2,2,2,1],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [2,1,1,1],
         [2,2,1,1],
         [2,1,1,1],
         [1,1,1,1]
        ]
       ],

 "|" => [
        [
         [1,1,1,1],
         [2,2,2,2],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [1,2,1,1],
         [1,2,1,1],
         [1,2,1,1],
         [1,2,1,1]
        ],
        
        [
         [1,1,1,1],
         [2,2,2,2],
         [1,1,1,1],
         [1,1,1,1]
        ],
        
        [
         [1,2,1,1],
         [1,2,1,1],
         [1,2,1,1],
         [1,2,1,1]
        ]
       ]
  }
```

- ブロックを画面の中に作成した時の初期位置を指定する。
ブロックの形を定義したコードの下に書く。


ブロック出現時初期位置の指定

``` ruby
BLOCK_INIT_POINT_X = 5
BLOCK_INIT_POINT_Y = 0
```

マップ上のブロック表示基点座標

``` ruby
x = BLOCK_INIT_POINT_X
y = BLOCK_INIT_POINT_Y
```

この指定ではブロックの作成される座標を決める。
y 座標が[0] x 座標が[5]を指す。

- ゲーム内の設定用変数郡
ゲーム内で使用する変数で、値自体はゲームループ内で決める。
値は頻繁に変更される変数郡。ブロックの初期位置を決めるコードの下に追記。

``` ruby
# 各種変数定義
# ブロックスコープの問題でループの外に出しておく必要あり。
# phase はテトリスのゲームを行う上でどの動作を行うかを決める変数。
# ブロックを配置するフェーズ、ブロックが一列並んでいるかを確認するフェーズ
# ブロックを生成するフェーズなど。
phase = nil

# map を表示するための変数。但し、表示する為の変数で、実際の map は tetris_map で運用。
map = nil

# 配置操作を行なっているブロックの形を保持する変数。
current_block = nil
# ブロックの形のタイプを保持する配列。
# ブロックを回転した時のデータを持っている。
# current_block_type[i]という使い方をすることで、
# 現在扱っているブロックの向きを決定する。
# i はブロックの向きを指定する為の変数。0~3 の値まで有効
current_block_type = []
i = 0

# 経過時間を測るための変数。
# Time.now で現在の時間が取得できる。
at_time = Time.now

# テトリスの行数をカウントするための変数。
# ブロックを配置し終わると、
# マップの一番下から上に向かってブロックが一行揃っているかを調べ、
# ブロックを消す作業に入るために使用。
line_count = 20
```

- 落とす予定のブロック表示
START を選択するとゲームを開始するための if 文を書き換える。
具体的なコードは、タイトル画面の時に、
選択肢の START を選ぶと実行される if から end までのコード。


**** 注意点 ****

if 文を書き換えるので、if と end も消して下記のコードで置き換える。
if と end を消さなくても良いが、その場合は下記のコードの
if arrow["y"] == START_POSITION["y"]
という行と
end
という行は書き込まない。

****************

``` ruby
# START を選択するとゲーム開始
  if arrow["y"] == START_POSITION["y"]
    # シーン切り替えの為、ボタン入力後の待ち時間
    sleep 1
    scene = "TETRIS"
    phase = "CREATE"
  end
```

さらに、下記のコードを
 if scene == "TETRIS"
   map = Marshal.load(Marshal.dump(tetris_map))
の下に書き込む。

``` ruby
if phase == "CREATE"
  # ブロック生成時には回転していない状態にする
  i = 0

  # ブロックの定義ハッシュ(辞書みたいなもの)を
  # ランダムに並べ替え(ソート)して、ハッシュの最初のブロックを選ぶ。
  # ハッシュをソートすると、二次配列目が KEY(キー) と VALUE(バリュー) のセット、
  # 一次配列目が KEY を 0 に、 VALUE を 1 に設定した配列になる。
  current_block_type = BLOCK_TYPE.sort_by{rand}[0][1]

  # ブロックが回転していない状態を設定するためにブロックの回転状況の配列の頭から取得。
  current_block = current_block_type[i]

  # ゲーム内のフェーズをブロックの生成からブロックの配置に変更する。
  phase = "PLACEMENT"
end
```

*** メソッドの作成 ***

マップにブロックを表示する為には、
ブロックの形を指定されたマップの座標に重ねあわせる必要がある。

下記のコードを Window.loop do 行の上に書く。

``` ruby
def merge_map(map, block, x, y)
  map[y][x] *= block[0][0]
  map[y][x + 1] *= block[0][1]
  map[y][x + 2] *= block[0][2]
  map[y][x + 3] *= block[0][3]

  map[y + 1][x] *= block[1][0]
  map[y + 1][x + 1] *= block[1][1]
  map[y + 1][x + 2] *= block[1][2]
  map[y + 1][x + 3] *= block[1][3]

  map[y + 2][x] *= block[2][0]
  map[y + 2][x + 1] *= block[2][1]
  map[y + 2][x + 2] *= block[2][2]
  map[y + 2][x + 3] *= block[2][3]

  map[y + 3][x] *= block[3][0]
  map[y + 3][x + 1] *= block[3][1]
  map[y + 3][x + 2] *= block[3][2]
  map[y + 3][x + 3] *= block[3][3]

  return map
end
```

マップと、ブロックデータの重ねあわせ方法には
掛け算を利用した。
*= は左辺に、左辺と右辺を掛けた値を代入する言語構文 (計算方法) である。

背景を 1 に、壁を 0 に、ブロックを 2 に割り当てているので、
掛け算した時に壁が最優先、次にブロック、最後に背景が表示される。
0 には何をかけても 0 にしかならないのが、壁が最優先である理由。
ブロックのと背景が重なっている時はブロックを表示して欲しいため
ブロックと掛けあわした時にはブロックの数値になるように配慮している。
ブロックのサイズは 4 マス x 4 マスとしているので、計 16 マス分の計算が必要になる。
本当は繰り返しという方法を用いると簡単に短く書けるが、今回は使っていない。

``` ruby
def merge_map(~)

end
```

def から始まり end で括られている塊のことをメソッドと呼び、
ある一定の処理の塊を再利用することを目的にされている。
(~) の内容は引数 (ひきすう) といい、
このメソッドの中で使用したい変数や配列など、
メソッドの外部データをメソッドの中に持ち込む為の仕組みである。

return はメソッドの外部にデータを持ち出すための手段で、
続く値をメソッドを呼び出した場所に戻す。

メソッドの呼び出し (使用) 方法は、メソッドの名前とそのメソッドに必要な引数を与える。
メソッド名は def から () までに記述されているアルファベットである。
一文字目には大文字や、記号の _ 以外は使わない。

上記のコードが書けたら、さらに

``` ruby
if phase == "CREATE"
  i = 0
  current_block_type = BLOCK_TYPE.sort_by{rand}[0][1]
  current_block = current_block_type[i]
  phase = "PLACEMENT"
end
```

の if 文の塊の下に下記のコードを書く。

``` ruby
if phase != "CHECK"
  # ブロックの座標をマップに反映させる
  map = merge_map(map, current_block, x, y)
end
```

ここまでで、ブロックが表示されるようになるはずである。
コードの実行を行い、ブロックが生成されることを確認する。


２、ブロックの移動、回転

- ブロックの回転
先ずは簡単なところから作るため、ブロックを回転させる機能を実装する。
以下のコードを if phase == "CREATE" 文の塊の下に書く。

``` ruby
if phase == "PLACEMENT"

  if Input.keyPush? K_A
    # ブロックの回転を制御する為には i の値が配列を一周回れるようにする。
    # K_A では値は 1 つずつ減るため i == 0 の場合は i の数値を 4 に変更。
    if i == 0
      i = 4
    end

    # i の値を減らすことでブロックを回転させる。
    i = i - 1

    # current_block_type はブロックの回転情報を持っている配列なので、
    # i の値が変わり、current_block に i を対応させた結果を代入するとブロックが回転する。
    current_block = current_block_type[i]
  end

  # K_S も基本的に一緒。
  # 但し、キーボードの S を押した時に動作することと、
  # i が +1 される所、i が 3 を指した時には i の値を -1 に変更する。
  if Input.keyPush? K_S
    if i == 3
      i = -1
    end
    i = i + 1
    current_block = current_block_type[i]
  end
end
```

この塊は、ゲームシーンの中でもブロックを配置する為に使うフェーズである。
K_A と K_S はそれぞれキーボードの A と S を指しているので、
それぞれのキーを押された時のプログラムの動作を if 文の中で書き示している。
因みに、K_ とアルファベットの大文字をつなぎ合わせることで他のキーを割り当てることも出来る。

例えば、

``` ruby
if Input.keyPush? K_E

end
```

とすると、E のキーが押された時に if ~ end までに書かれたコードが実行される。

== という書き方が if 文の条件に使われているが、
これは 左辺と右辺 が同値であるかの確認に使用する。
例えば、

i == 0 の場合、
変数 i に代入されている値と
0 という数値は同じ値かを確認している。
もしこの評価を行う時に、
i に数値 (文字としての 0 は不可) の 0 以外の値が代入されていると偽、
数値の 0 が代入されている場合は真を示す。

if 文は真を示した場合に中の処理を行うので、

``` ruby
if i == 0
end
```

とすると、i が 0 だった場合に中の処理を行う。

- ブロックの移動
ブロックの移動は少々面倒くさい内容が含まれる。
というのも、ブロックを移動させる為には少しずつブロックの描画位置をずらす必要がある。
ブロックの描画はマップの配列とブロックの配列を重ねあわせて
一つのマップを作成している。

if phase == "PLACEMENT"

上記記述行の下に下記のコードを書く。

``` ruby
move = nil
# 1 秒ごとに一段下に移動する
if (Time.now - at_time).to_i % 2 == 1
  at_time = Time.now
  y = y + 1
  move = true
end
```

コードの実行がうまくいくか確認する。
途中でバグが発生して、ゲームが固まることを確認。

- 衝突判定
この原因は、ゲームの中に衝突判定を入れていない為に発生している。
衝突判定というのは、ゲームの仕様の中で衝突する時に発生するアクションの事。
このテトリスでは、ブロックが、ブロックや壁に重ならないようにする。

衝突判定を行うメソッドは下記のコードなので、これらを

``` ruby
def merge_map(map, block, line, x, y)
.
.
  return map
end
```

の塊の上に書く。

``` ruby
def column_background?(block, column)
  if block[0][column] == 1 &&
      block[1][column] == 1 &&
      block[2][column] == 1 &&
      block[3][column] == 1

    return true
  end

  return false
end

def line_background?(block, line)
  if block[line][0] == 1 &&
      block[line][1] == 1 &&
      block[line][2] == 1 &&
      block[line][3] == 1

    return true
  end

  return false
end

def line_background?(block, line)
  if block[line][0] == 1 &&
      block[line][1] == 1 &&
      block[line][2] == 1 &&
      block[line][3] == 1

    return true
  end

  return false
end

def column_collision_safe?(map, block, column, x, y, stat)
  if stat == "left"
    if  (map[y][x + column - 1] * block[0][column] == 1 ||
         map[y][x + column - 1] * block[0][column] == 2) &&
        (map[y + 1][x + column - 1] * block[1][column] == 1 ||
         map[y + 1][x + column - 1] * block[1][column] == 2) &&
        (map[y + 2][x + column - 1] * block[2][column] == 1 ||
         map[y + 2][x + column - 1] * block[2][column] == 2) &&
        (map[y + 3][x + column - 1] * block[3][column] == 1 ||
         map[y + 3][x + column - 1] * block[3][column] == 2)

      return true
    end
  end

  if stat == "right"
    if  (map[y][x + column + 1] * block[0][column] == 1 ||
         map[y][x + column + 1] * block[0][column] == 2) &&
        (map[y + 1][x + column + 1] * block[1][column] == 1 ||
         map[y + 1][x + column + 1] * block[1][column] == 2) &&
        (map[y + 2][x + column + 1] * block[2][column] == 1 ||
         map[y + 2][x + column + 1] * block[2][column] == 2) &&
        (map[y + 3][x + column + 1] * block[3][column] == 1 ||
         map[y + 3][x + column + 1] * block[3][column] == 2)

      return true
    end
  end
end

def line_collision?(map, block, line, x, y)
  if  ((map[y + line + 1][x] == 0 &&
        block[line][0] == 2) ||
       map[y + line + 1][x] + block[line][0] == 4) ||
      ((map[y + line + 1][x + 1] == 0 &&
        block[line][1] == 2) ||
        map[y + line + 1][x + 1] + block[line][1] == 4) ||
      ((map[y + line + 1][x + 2] == 0 &&
        block[line][2] == 2) ||
       map[y + line + 1][x + 2] + block[line][2] == 4) ||
      ((map[y + line + 1][x + 3] == 0 &&
        block[line][3] == 2) ||
       map[y + line + 1][x + 3] + block[line][3] == 4)

    return true
  end

  return false
end

def line_collision_safe?(map, block, line, x, y)
  return !line_collision?(map, block, line, x, y)
end
```

さらに、

if Input.keyPush? K_S
  if i == 3
    i = -1
  end
  i = i + 1
  current_block = current_block_type[i]
end

上記コードの塊の下に、下記コードを書く。

``` ruby
if (line_collision? map, current_block, 3, x, y) ||
    (line_collision? map, current_block, 2, x, y) ||
    (line_collision? map, current_block, 1, x, y) ||
    (line_collision? map, current_block, 0, x, y)
  tetris_map = merge_map(map, current_block, x, y)
  phase = "CHECK"
end
```

これで地面や積み上がったブロックとの衝突は無くなる。
しかし、フェーズは CHECK に移行するが、まだ CHECK は作っていないので
ブロックが接地してからの処置がない。

そこで、取り敢えず次に落とすブロックを作成するコードを追加する。
とは言え、
既にブロックを作成するコードはあるのでブロックをチェックするフェーズを作り、
そのフェーズからブロックを作成するフェーズに移動するようにする。

``` ruby
if phase == "CREATE" 
  i = 0
  current_block_type = BLOCK_TYPE.sort_by{rand}[0][1]
  current_block = current_block_type[i]
  phase = "PLACEMENT"
end
```

上記 if 文の塊の下に下記のコードを書く。

``` ruby
if phase == "CHECK"
  if line_count == 0
    x = BLOCK_INIT_POINT_X
    y = BLOCK_INIT_POINT_Y
    line_count = 20
    phase = "CREATE"
  end

  if phase != "CREATE"
    line_count = line_count - 1
  end
end
```

しかし、今度は違う問題が発生し、ブロックが積み上がりすぎた為に
ブロックを生成する場所にブロックが侵入してゲームが止まってしまう。

*** ゲームオーバー ***
テトリスのゲームルールから言っても、 縦で 20 マス目にブロックが存在すると
ゲームを終了させるべきである。
ついでにタイトル画面に戻せばゲームっぽくなる。

if phase == "CHECK"

の行の直ぐ下に下記のコードを書く。

``` ruby
if line_count == 1 && (tetris_map[line_count][2...12].include? 2)
  scene = "TITLE"
end
```

一番上から二番目の位置にブロックが侵入したらゲームは終了し、タイトルフェーズへ戻る。
ただ、現状で待つのは面倒くさいので、確認は後回しにして、ブロックの移動機能を実装する。
**********************

移動自体は簡単で、問題は衝突判定だが衝突判定に関してのメソッドは既に作ってある。
後は、状況に合わせたメソッドの使用で対応。

``` ruby
if Input.keyPush? K_S 
  if i == 3
    i = -1
  end
  i = i + 1
  current_block = current_block_type[i]
end
```

上記 if 文の塊の下に下記コードを書く。

``` ruby
unless move
  if Input.keyPush? K_LEFTARROW
    if (column_background? current_block, 0) ||
        (column_collision_safe? map, current_block, 0, x, y, "left")
      if (column_collision_safe? map, current_block, 1, x, y, "left")

        x = x - 1
      end
    end
  end

  if Input.keyPush? K_RIGHTARROW
    if (column_background? current_block, 3) ||
        (column_collision_safe? map, current_block, 3, x, y, "right")
      if (column_background? current_block, 2) ||
          (column_collision_safe? map, current_block, 2, x, y, "right")
        if (column_collision_safe? map, current_block, 1, x, y, "right")

          x = x + 1
        end
      end
    end
  end

  if Input.keyPush? K_DOWNARROW
    if (line_background? current_block, 3) ||
        (line_collision_safe? map, current_block, 3, x, y)
      if (line_background? current_block, 2) ||
          (line_collision_safe? map, current_block, 2, x, y)
        y = y + 1
      end
    end
  end
end
```

ここまで来ると、大分ゲームらしくなってくる。
右、左、下カーソルが反応している事を確認。
また、壁にブロックがめり込んだりしないことを確認。
衝突が正常に行われていれば問題なし。

後は一行以上揃えたブロックの削除を作るだけである。

``` ruby
if line_count == 1 && (tetris_map[line_count][2...12].include? 2)
  scene = "TITLE"
end
```

上記 if 文の塊の下に下記コードを書く。

``` ruby
if !(tetris_map[line_count][2...12].include? 1)
  tetris_map[line_count][2...12] = [1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
  tetris_map[1..line_count] = tetris_map[0..line_count-1]
  tetris_map[0] = [1,0,1,1,1,1,1,1,1,1,1,1,0,1,1]
  line_count = line_count + 1
end
```

これで、ゲームはひと通りの機能が実装完了。

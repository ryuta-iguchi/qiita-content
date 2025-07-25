---
title: 【Godot 4.4.1】変数まわりのテクニックの全て（備忘録）
tags:
  - 初心者
  - アウトプット
  - Godot
  - プログラミング勉強日記
private: false
updated_at: '2025-06-24T13:25:55+09:00'
id: fd5c29fdc7f2f718b9bc
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
godot触り始めて約2ヶ月。変数とかの扱い方をあらかた理解したつもりなので忘れないためにも共有します。

### 備考
- OS： Windows11 23H2
- エンジン： Godot 4.4.1
- 言語： GDScript
- 記事対象者： 初心者

# 1. 基本の変数宣言と型
### 変数の宣言
変数を宣言、つまり作成する際は、`var`を使って記述します。

また、**イコール（`=`）** で、初期値を決めることもできます。
```gdscript:書き方
var hp = 100
```
これで、「hp」という名前の変数が100という数値が入った状態で作成されました。

<br>

### 変数には型を指定できる
変数には「型」を決めることができます。「型」というのは、その変数にどのような種類の値を入れられるのかを決める物のことです。

**整数**なのか？**小数**なのか？**座標**なのか？**文字**なのか？ といったように。
```gdscript:書き方
var hp: int = 100
```
これは、「hp」という名前の変数の型として、整数（int）を指定したコードです。
**コロン（`：`） + 型** を変数名の後ろに書くことで指定できます。

変数名、コロン（`：`）、型、イコール（`=`）の間には半角スペースを入れて、可読性を向上させましょう。

:::note warn
しかし、コロン（`：`）の前だけはスペースを入れないことが公式の書き方です。
:::

<br>

### 型の種類
よく使う基本的な型の種類としては、次のようなものがあります。
| 型名 | 説明 | 入る値の例 |
|:-:|:-:| :-: |
| `int` | 整数 | `42` |
| `float`  | 浮動小数点数  | `3.14` |
| `bool`  | 真偽値  | `true` または `false` |
| `String`  | 文字列  | `"hello"` |
| `Array`  | 配列  | `[1, 2, 3]` |
| `Dictionary`  | 辞書（連想配列）  | `{"key": "value"}` |
| `Vector2`  | 2D座標（浮動小数点）  | `Vector2(x,y)` |
| `Vector2i`  | 2D座標（整数）  | `Vector2i(x,y)` |
| `Node`  | シーン内のあらゆるノード | シーン内のあらゆるノード |
| `NodePath` | ノードのパスを表す | `"../Enemy/boss_1"` |
| `Variant` | 何でも入る | 数値、文字、全部 |

> 型の詳細は[公式ドキュメント](https://docs.godotengine.org/ja/4.x/tutorials/scripting/gdscript/gdscript_basics.html#built-in-types)から確認できます。

:::note warn
型を指定しない場合、全て`Variant`型となります。

型安全性を高めたい場合は型アノテーションを指定するのがオススメです。
:::

<br>

### 自動で型指定させられる
型推論と呼ばれる、自動で型を指定してくれる機能があります。
```gdscript:書き方
var hp := 100 #型推論でint型になる
var stamina := 300.0 #小数があるのでfloat型になる
var message := "hello" #String型になる
```
`:=`を使うと、自動で型を推測して設定してくれます。

:::note warn
自動で推測してくれる機能なので、**期待通りの型にならない場合**もあります。そこだけ注意しましょう。
:::

<br>

### 初期値はなくても宣言できる
ちなみに、初期値を設定しないで、型のみ指定することもできます。
```gdscript:書き方
var player: int
```
しかし、関数内などで数値を入れるまでは、中身が`null`となり、エラーの原因となるためできるだけ初期値も入れてあげるのがオススメです。

<br>

### 型・初期値を使うメリット
:::note info
「**型**」を指定しておくと、**バグ防止、補完、意味が明確**になります。
「**初期値**」を設定しておくと、**未定義バグ防止、予期しないnull**を防げる
などのメリットがあります。
:::

<br>

### 変数名の付け方にはルールがある
変数名、適当につけてませんか？実は、名前の付け方には**ルールが存在**します。

ルールに沿わないとエラーを吐くというわけではありませんが、可読性を向上させたり、チームで開発するときには大切な事になってきますので、意識するとよいでしょう。

そのルールとはズバリ、**スネークケース（小文字+アンダースコア）** で名付けるというものです。

例：`player_position`、`enemy_hp`、`dmg_mul`

このとき、「**意味が明確ですぐに理解できる**」「**名前が短すぎず長すぎない**」「**ローマ字を使わず、英語**」「**一貫性をもたせる**」の4つを意識しましょう。

※ 命名に関する説明はまたいつか記事を書きます。

:::note warn
大文字は使わないようにしましょう。後述しますが、意味が変わってきます。
:::

<br><br>

# 2. 定数
### なにそれ？
**定数**とは、一度決めたら変更できない値を扱いたいときに使う書き方のことです。

例えば、HPやスタミナなどの、数値を変更する必要のあるものは変数を使いますが、最大HPや最大スタミナのような、コードを実行中に変更する必要のないものは、**定数**を使います。
```gdscript:書き方
const MAX_HP: int = 100
```
書き方としては、変数で`var`と書いていた部分を`const`と書くだけです。しかし、初期値は必須なので、必ずイコール（=）で初期値を設定してあげましょう。

:::note alert
初期値がないとエラーになります。
```gdscript:ダメな例
const MAX_STAMINA:float
```
:::

<br>

### 定数は型指定しなくても良い
定数で型を指定することは可能です。しかし、定数はそもそもコード中で値を変更しないので、特別指定しなくても大丈夫な場合が多いです。

型を指定しない場合、変数も定数もですが、Godot側が自動で判断して型を指定したことにしてくれます。

例えば、`10`であれば`int`、`32.5`であればfloat、`true`であれば`bool`といったように。

:::note info
こういう書き方でもOK!
```gdscript:OKな例
const MAX_HP = 100
```
:::

<br>

### 定数を使うメリット
正直、全て変数でコードを書いてしまっても、大きな問題にはなりません。

しかし、「塵も積もれば山となる」というように、しっかりと使い分けることで、大きなメリットとなります。

具体的なメリットとしては、
- 変数よりもごくわずかにパフォーマンスが良い（体感できるほどではない）
- ミス防止になる
- 意図が明確になる
- 自動補完やエラー検知が強くなる

などがあります。

<br>

### 定数名の付け方にもルールがある
変数と定数を見分けやすくするためにも、定数は変数とは違う方法で名前をつけます。

 **全て大文字＋アンダースコア** という書き方をします。

例：`PLAYER_SPEED`、`MAX_HP`、`GRAVITY_MUL`

定数の命名も、変数同様「意味が明確」「名前の長さ」「英語命名」「一貫性」を**必ず意識**しましょう。

<br><br>

# 3. 配列・辞書
### 配列（Array）・辞書（Dictionary）とは
「**変数をまとめたもの**」というとわかりやすいでしょうか？

番号や名前を割り振って複数の値をまとめて管理することのできるもの。それが配列・辞書です。

具体的には、
配列　→　**0から順に番号が割り当てられる**
辞書　→　**自分で各値に名前を割り振れる**
といった特徴があります。

<br>

### 配列（Array）の使い方
配列は、自動で割り振られた0から始まる番号によって、複数の値を管理するもののことです。

**配列**の基本的な宣言の仕方は、以下の通りです。

```gdscript:書き方
var items = ["ebi", "tako", "ika", "tako"] #配列に文字（String）を入れている
var score = [10, 15, 20, 30] #配列に整数（int）を入れている
var player_data = [hp, stamina, jump, dash] #配列に変数を入れている
var empty_arr = [] #初期値なしの配列
```

<br>

そして、基本的な**読み取り**、**書き込み**をする際は、`配列名[参照する番号]`の形で使用します。
```gdscript:書き方
#読み取り
print(items[1]) #takoと出力される

#書き込み
player_data[2] = lives #配列の3番目にlivesが入る
```

<br>

配列に限ったものではありませんが、**メソッド**というものがあります。これはその一覧です。

:::note info
**メソッド（関数）** とは、変数や配列などの後ろに、 **ピリオド（.）** と **括弧（()）** で書かれる文字のことです。

`配列名.メソッド(数値)`で実装できます。
```gdscript:書き方
arr.append(25) #配列の一番右に25を追加する
```
:::

| メソッド | 説明 |
|:-:|:-:|
| append(value)  | 一番右に要素を追加 |
| insert(idx, v)  | 指定位置に要素を挿入  |
| pop_back()  |  末尾から要素を削除し返す |
| pop_front()  |  先頭から要素を削除し返す |
| remove(idx)  |  指定位置の要素を削除 |
| size()  | 要素数を返す  |
| clear()  | 全要素削除  |
| find(value)  | 最初に見つかった値のインデックス  |
| has(value)  | 値が含まれているか  |
| count(value)  | 値が何個含まれているか  |
|  duplicate() | 配列の複製  |
| sort()  |  昇順ソート |
| shuffle()  | ランダム並び替え  |
| slice(b, e)  | 一部だけ切り出し  |
|  reverse() | 要素の順序を逆転  |
| is_empty()  | 配列が空かどうか  |
| resize(size)  | 配列の長さを変更  |
| fill(value)  | 全要素を同じ値で埋める  |
| front()  | 先頭要素を返す 	  |
| back()  | 末尾要素を返す  |

<br>

配列も、例に洩れず**型を指定**することができます。
```gdscript:書き方
var int_arr: Array[int] = [10, 50, 80] #int型を指定
var str_arr: Array[String] = ["aaa", "bbb", "ccc"] #String型を指定
```

:::note warn
型指定しないと「いろんな型が混ざる配列」になります。
型指定をしておくと、その型以外の要素は基本的に入れられなくなるので**安全かつおすすめ**です。
:::

<br>

すこし上級者向けですが、**多次元配列**を作ることもできます。
```gdscript:書き方
#多次元配列の宣言
var grid = [
	[1,2,3],
	[4,5,6]
]

#多次元配列の読み取り
print(grid[1][2]) #6を出力する
```

<br>

補足

:::note alert
配列への範囲外アクセスはエラーになります。
```gdscript:ダメな例
var arr = [10, 20, 30, 40, 50]

print(arr[999])
```
:::

<br><br>

### 辞書（Dictionary）の使い方
**辞書**は、配列と違い、名前を付けることで値を管理します。

辞書の基本的な宣言方法は以下の通りです。
```gdscript:書き方
var dic = {
	"name": "たろう",
	"age": 18
}
var empty_dic = {}
```
:::note info
辞書は配列同様、改行なしで記述しても問題ありませんが、**可読性**の観点から改行して宣言することが推奨されます。
:::

<br>

基本的な**読み取り、書き込み**は、`辞書名[参照する名前]の形で使用します。
```gdscript:書き方
#読み取り
print(dic["name"])    #たろうと出力される

#書き込み
dic["age"] = 19       #ageの項目が19で更新される

dic["score"] = 80     #項目scoreが作られ、80が設定される
```

<br>

辞書にも、様々なメソッドがあります。
| メソッド | 説明 |
|:-:|:-:|
| `clear()` | 全要素削除 |
| `size()` | 要素数を返す |
| `has(key)` | キーが存在するか |
| `has_all(keys: Array)` | 複数のキーがすべて存在するか |
| `erase(key)` | 指定キーの要素を削除 |
| `keys()` | キー一覧（Array）を返す |
| `values()` | 値一覧（Array）を返す |
| `get(key, default)` | キーがあればその値、なければdefaultを返す |
| `duplicate(deep=false)` | 辞書の複製 |
| `merge(other, overwrite)` | 他の辞書を合体 |
| `find_key(value)`  | 値からキーを探す、なければnullを返す |
| `is_empty()` | 辞書が空かどうか |

<br><br>

**型の指定**も、配列と同様に指定することができます。
```gdscript:書き方
#キーを文字（String）、値を整数（int）にしたい場合
var int_dic: Dictionary[String, int] = {
    "score": 1000,
    "hp": 100,
    "stamina": 200
}
```

<br>

**辞書の中に配列や辞書を入れて**、多次元配列のような使い方をすることもできます。
```gdscript:書き方
#辞書の中に辞書と配列を宣言
var data = {
	"user": {"name": "たろう", "age": 18},
	"scores": [10, 20, 30]
}

#呼び出し例
print(data["user"]["name"]) #たろうと出力される
```

<br>

### まとめ

:::note info
順番が大事・リスト的に使いたい → **配列（Array）**
ラベル（キー）で管理・検索したい → **辞書（Dictionary）**
両方使う複雑なデータ → **辞書の中に配列、配列の中に辞書**
:::

<br><br>

# アノテーション
### エディタで編集可能にする `@export`
`@export`は、エディタ上で変数を直接いじれるようにするアノテーションと呼ばれるものです。

:::note info
アノテーションとは、スクリプト内で変数や関数などに「追加情報」や「指示」を与えることのできる特別な記法のことです。
:::

<br>

**変数宣言の前**に、`@export`と書くことで、エディタ画面右の**インスペクターから変数の値**を変更することができます。頻繁に数値を変更する際や大量の変数を管理する際に有用です。

```gdscript:書き方
@export var speed: float = 300.0
@export var player_name: String = "Taro"
@export var colors: Array = ["red", "blue", "green"]
@export var enemy_count: int = 5
```

<br>

数値入力欄ではない、別の形での表示をすることもできます。
```gdscript:書き方
#0~100の範囲内のスライダーとして実装
@export_range(0, 100, 1)
var health: int = 50

#プルダウンで選べるようにする
@export_enum("小学生", "中学生", "大人")
var age_group: String
```

<br>

自分でオリジナルのインスペクターの項目を作れる機能なので、できることがかなり多いです。詳しい使い方は、以下のサイトがわかりやすいです。

https://hiramame-gclab.com/gdscript_annotation_export/

:::note alert
`@export`を使った際、**インスペクターで数値を変更すると、コード上の数値を上書きしてしまいます。**

つまり、コード上で初期値を300としていても、インスペクターで500と設定していたら500が優先されてしまいます。
:::

<br>

### ready時に初期化する `@onready`
ready時に初期化といわれても、よくわからないかと思います。

特にわかりやすい利用例としては、変数にノードを結合する際かと思います。
```gdscript:書き方
var player = $Sprite2D
```

このコード、このまま実行してしまうと、エラーを吐いてしまいます。

なぜなら、ノード全体よりも、スクリプトのほうが読み込まれるスピードが速いため、変数宣言したときにはSprite2Dが用意されてないという現象が起こってしまうからです。

その対策として利用されるのが、`@onready`です。
```gdscript:書き方
@onready var player = $Sprite2D
```
このような書き方をすることで、関数 `func _ready` が実行されると同じタイミングでこの部分のコードの処理がされるので、ノードがすべて読み込まれた状態となり、エラーを吐かなくなります。

:::note info
`@onready` はノードが用意できてから変数にセットするアノテーション
:::

### その他のアノテーション
よく使うのは`@export` と `@onready` ですが、アノテーションは他にもあるので紹介します。

| アノテーション名 | 用途 | 利用例 |
|:-:|:-:|:-:|
| `@export` | 変数をエディタで編集可能にする | `@export var speed: int = 200` |
| `@export_range(a, b, s)` | エディタでスライダー入力（範囲a～b、刻み幅s） | `@export_range(0, 100, 1) var hp: int = 50` |
| `@export_enum(...)` | ドロップダウンメニューで選択肢を指定できる | `@export_enum("赤", "青", "緑") var color: String`|
| `@onready` | ノード準備後に変数へ値をセット | `@onready var sprite = $Sprite` |
| `@tool` | エディタ上でもスクリプトを実行可能にする | `@tool extends Node` |
| `@icon("path")` | スクリプトのアイコンを指定 | `@icon("res://icon.png")` |
| `@warning_ignore` | 特定の警告を無視 | `@warning_ignore("unused_variable")` |
| `@rpc` | ネットワーク同期処理（RPC）用 | `@rpc func do_something():` |

:::note info
`@onready`は関数にも使うことが可能らしいです。（使ったことないけど）基本は変数や定数に使うものって覚えておけばokだと思います。
:::

<br><br>

# get（ゲッター） / set（セッター）

get / set とは、**変数を読み込んだり書き込んだりする時に特別な処理を挟める**仕組みです。

値の範囲制限などの、変数の中身を自動で変換したりチェックしたりしたい場合や、変数が変更されたときに追加で別の処理を開始したいときなどに、ifなどで検知せずとも**自動で検知してくれる**便利な仕組みです。

- **get（ゲッター）：変数の値を読むときに毎回呼ばれる関数**
- **set（セッター）：変数に値を代入するときに毎回呼ばれる関数**

使い方の例として、以下のコードを用意しました。これは、hpが書き換えられた際、0~100の範囲でなければ0~100の範囲に自動で調整する処理を実装しています。


```gdscript:書き方
var _hp: int = 100

var hp: int:
	get:
		return _hp
	set(value):
		_hp = clamp(value, 0, 100) #0~100に自動で制限
```

<br>

命名に関して補足します。例文上で`_hp`の部分、アンダーバー（_）を使っていますが、これにはちゃんとした意味があります。
これは、**外部から変更することを想定していない**、そのコード内で使う用に作られましたという意味を持っています。今回の場合、`hp`で変更を受け取り、`_hp`に上書きする形だったので、外部から直接`_hp`に読み書きされることがないので使用しています。

関数などもアンダーバーよく使われていて、中々重要な部分だと思うので覚えておきましょう。

<br>

まとめ

:::note info
- 値の不正を自動で防げる
- 変更検知やログ出力などもラクに仕込める
- クラスの外からは普通の変数みたいに見えて、使いやすい
:::

# おわりに

これで、変数まわりの基礎的なテクニック、技術の解説は以上です。自分でも使いこなせてない部分はありますが、学習の役に立てば光栄です。

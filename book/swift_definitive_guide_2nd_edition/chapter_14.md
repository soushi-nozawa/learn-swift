
# 14 ジェネリクス

## 14.1 ジェネリクスの概要

### ジェネリクスとは

* ジェネリクスとは型をパラメータとしてプログラムを記述するための機能
* ジェネリクスの機能を使って定義された関数をジェネリクス関数、総称関数、あるいは汎用関数と呼ぶ
* Swiftは標準ライブラリとして多くの関数や型を用意しているが、これらのインターフェースはジェネリクスの機能を多様した宣言の集合体となっている

### ジェネリクスの関数の例

* これまで何度か使ったswap関数はSwiftの標準ライブラリで提供されていて次のように宣言されたジェネリクス関数
```
func swap<T>(inout a: T, inout _ b: T)
```

* <T>と記述されているのが型パラーメータ
* 関数swapは、任意の型Tについて、T型のinout引数を2つとり、値を返さない
* 実際にプログラム内に記述された時に与えられた具体的な値の型をTに当てはめて推論し、矛盾がなければコンパイルできる
* この例では2つの引数の型は同じでなければならない

* 型パラメータに成約のある別の例
```
func max<T: Comparable>(x: T, _ y: T, _ z:T, _ rest: T...) -> T
func max<T: Comparable>(x: T, _ y: T) -> T
```

* 型パラメータのTの後ろにComparableというプロトコル名が書かれているのは、プロトコルComparableに適合していなければならないという成約
* 関数maxは、型T自体が持つ比較の機能を使って引数の中から最大値を取り出して返す

* 2種類以上のパラメータを使う場合は、<T, U>のようにカンマで区切って記述する
* 暗黙のルールとしてTと書けば型パラメータであるという識別子となる

## 14.2 ジェネリクス関数の定義

* ジェネリクスに置き換えたmySwap関数
```
// func mySwap(inout a:Int, inout _ b:Int) {
//     let t = a; a = b; b = t
// } ... Int型に対してのみ有効なmySwap関数

func mySwap<T>(inout a:T, inout _ b:T) { // ジェネリクス関数
    let t = a; a = b; b = t
}
```

* mySwapが文字列やタプルに対しても動作することが確認できる
```
var a = "Aria", b = "Jeanne"
mySwap(&a, &b)
print("a=\(a), b=\(b)") // a=Jeanne, b=Aria と表示
var s = (1, 2), t = (100, 50)
mySwap(&s, &t)
print("s=\(s), t=\(t)") // s=(100, 50), t=(1, 2)
```

* さらにタプルは次のうように実行することもできる
```
var v = ((2, 3), 9 (88, 108))
mySwap(&v.0, &v.2)
print(v) // ((88, 108), 9, (2, 3))
```

* 2つの辞書型データの内容を足し合わせて新しい辞書を作成するために「+」演算子にオーバーロード定義を行う
* 辞書型のキーと値の型は自由で構わないが２つの辞書は同じ型でなければならない

```
func +<Key, Value>(lhs:[Key:Value], rhs:[Key:Value]) -> [Key:Value] {
    var dic = lhs
    for (k, v) in rhs { dic[k] = v}
    return dic
}
```

* 実行例は次のようになる
```
let p = ["レベル":60, "特技":10]
let q = ["番号":1]
let t = p + q
print(t) // ["番号": 1, "レベル": 60, "特技": 10]
```

# 14.3 ジェネリクスによる型定義

## 型パラメータを持つ構造体の定義

* 構造体や列挙型クラスが型パラメータを持つように定義することができる
* 同様に適合すべきプロトコルなどの条件をつけることができる

```
protocol VectorType { // 2次元ベクトル型
  typealias Element // 付属型の宣言
  var x : Element { get set }
  var y : Element { get set }
}
```

* プロトコルVectorTypeを採用する構造体Vectorを、Elementを型パラメータで決められるように定義する

```
struct Vector<T> : VectorType {
  typealias Element = T // 付属型をパラメータで指定された型で定義
  var x, y: Element // 格納型プロパティ
}
```

* 定義を使った動作例を示す

```
let a: Vector<Int> = Vector<Int>(x:12, y:3)
print(a.x, a.y) // 12 3 を表示
let b = Vector<[String]>(x:[], i:["黒", "noir", "schwarz"]) // 使用可能
```

* 付属型はジェネリクス機能を使う上で不可欠の要素

## ジェネリックなデータ型と型パラメータの推論

* 周囲の状況から用意に推論ｄきるときはジェネリック型の型パラメータを省略できることがある

```
let p = Vecotr(x:10.0, y:2.0/2.5).y // p = 0.8 (Double)
```

* 型の誤りを防止するためにも、通常は型パラメータを明示的に指定すべきでである

### プロトコルSequnenceTypeを調べよう

* ひとまとまりのインスタンスを管理する機能をまとめたプロトコル
* 範囲型や配列などがこのプロトコルに適合している

```
protocol SequenceType {
  typealias Generator : GeneratorType // 付属型
  typealias SubSequence // 付属型
  func generate() -> Self.Generator
  func map<T>(transform:
      (Self.Generator.Element) throws -> T) rethrows -> [T]
  func filter(includeElement:
      (Self.Generator.Element) throws -> Bool) rethrows
      -> [Self.Generator.Element]
  func prefix(maxLength: Int) -> Self.SubSequence
  func suffix(maxLength: Int) -> Self.SubSequence
  // 以下、関数定義をいくつか省略
}

protocol GeneratorType {
  typealias Element // 付属型
  mutating func next() -> Self.Element?
}
```

* プロトコルSequenceTypeのメソッドgenerate()はプロトコルGeneratorTypeに適合したインスタンスを返する
* プロトコルGeneratorTypeはインスタンスの集まりの具体的な管理方法をプロトコルSequenceTypeから切り離したもの
* プロトコルSequenceType自体には要素のインスタンスが何型なのかという情報がない
* プロトコルGeneratorTypeの付属型のElementが要素の型である

* プロトコルSequenceTypeはまた、SubSequneceという付属型を持つ
* さまざまな操作の結果として部分集合を作成する場合にSelfの代わりにSubSequence型のインスタンスを返す

```
extension SequenceType where Self.Generator.Element : Comparable {
  func sort() -> [Self.Generator.Element]
}

extension SequenceType where Self.Generator.Element : Equatable {
  func contains(element: Self.Generator.Element) -> Bool
}

extension SequenceType where Self.Generator.Element == String {
  func joinWithSeparator(separator: String) -> String
}
```

* １つめ：プロトコルComparableに適合している拡張定義の条件。相互に大小比較が可能なので並び替えを行うメソッドsort()が利用可能であるように宣言
* ２つめ：プロトコルEquatableに適合している拡張定義の条件。指定したものと同じ要素が存在するかを調べるメソッドcontains(_:)が利用可能であるように宣言
* ３つめ：要素がString型であることが拡張定義の条件。引数で指定した文字列を区切りにして、すべての要素を連結して返すメソッドを宣言


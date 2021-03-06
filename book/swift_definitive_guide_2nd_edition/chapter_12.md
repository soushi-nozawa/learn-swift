
# 12.1 エラー処理構文

## エラーへの対応

* 何らかのシステムには正しい入力に対して正しい処理が行われる前提で全体の仕組みを設計する
* しかし、発生する「かもしれない」多種多様な誤りに対応するためのコードを、正常に進む場合の処理のあちこちに書き込みのは煩雑
* また、正常ではない状況が発生した場合、それに対応する処理をどこにどのように記述するかを考える必要があある
* swiftではエラーの発生とその内容を知らせる目的のためにオプショナル型を有効に利用することができる

* swiftではエラーが発生した時、実行中の手続きから抜けて、その手続きの呼び出し元に一挙に戻るための仕組みが用意されている
* これをエラー処理構文と呼ぶ

## エラーの発生

* 実行中に何かの例外的な状況が検知されることがある
* その時、プログラムはエラーの発生を知らせると同時にその時の手続から抜けだして呼び出し元に戻ることができる
* そのためにthrow文を使う

```
throw 式
```

* throw文を実行することを、エラーを発生させる、あるいはエラーを投げるということがある
* throw文の式には任意の型を指定できるが、その型はプロトコルErrorTypeに適合していなければならない
* プロトコルErrorTypeは空のプロトコルとしてていぎされている
* 拡張定義がわざわざ記述されているのは、本体にもプロトコル拡張にも何もないことを明示するため

```
public protocol ErrorType {
}

extension ErrorType {
}
```

* エラーを表現するために共有型の列挙型を定義すると、エラーに関係する値などを含めることもできて便利

```
enum TicketError : ErrorType { // ErrorTypeに適合すること
    case WrongDate, Shortage // よくあるエラーを列挙
    case Code(Int) // エラーコードを表現可能
    case Unknown(String) // 具体的な説明を文字列で含める
}
```

* throw文を実行して処理を中断する場合があることを関数で宣言しなければならない
* このような関数をエラーのある関数（throwing function）と呼ぶ
* エラーのある関数は引数リストの直後にthrowsというキーワードを記述して表す

```
func getTicket(order:String, _ fee:Int) throws -> Ticket
func printTicket(pass: Ticket) throws
func %%(a:Int, b:Int) -> Int
```

* イニシャライザの実行中にもエラーが発生する可能性もある
* エラーんあるイニシャライザを定義することも可能

```
init(solution:Int) throws
```

## 12.3 アクセス制御

### 可視性の3つのレベル

* メソッドやプロパティという単位でアクセス制御（access control）をする、定義を外部に公開するかどうかを指定することができる。
* 別な言葉では可視性（visibility）の制御ともいいます

* swiftがアクセス制御の単位と考えているのはモジュールとソースファイルである
* swiftでは関数やクラス定義、あるいはメソッドなどに対してアクセス制御の指定を行うことができる
* アクセス制御には３つの種類があり、その指定を受けた定義がどの範囲まで有効か、逆にいえば、どこからならアクセスすることが可能か定められている

* public：そのモジュールの情報をインポートすれば、どんなソースファイルからでもアクセスできる
* internal：定義を含むソースファイルと同じモジュールの内部からなら、どのソースファイルからでもアクセスできる／インポートは必要ない／特別に何も指定しない場合、この可視レベルがアクセス制御の規定値
* private：定義されたソースファイルの中だけで利用できる。他のソースファイルからはアクセスできない





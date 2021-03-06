
# Kotlinのキーワードを知りたい

KotlinではJavaとのcompatibilityを目的にしたものはアノテーション、Kotlin側で使われるものをキーワードと表現する傾向があります。

キーワードには以下のようなものがあります。

## Visibility Modifiers

キーワードというかアクセス修飾子。


[Visibility Modifiers](https://kotlinlang.org/docs/reference/visibility-modifiers.html)

### public

Javaとほぼ同じ。Kotlinではデフォルトのアクセスレベルがpublicとなっている。

### private

Javaとほぼ同じ。クラス内からのみアクセス可能。


### protected

Javaとほぼ同じ。同一クラスかサブクラスからアクセス可能。


### internal

同一モジュール内ならどこでも見える。  
  
これは、Jigsaw以前のJavaにおいてモジュールという概念無いためピンと来ない人も多いと思う（僕もそう）。  
このあたりに書いてあるので参照されたし。  
[http://stackoverflow.com/questions/35271413/what-is-a-kotlin-module](http://stackoverflow.com/questions/35271413/what-is-a-kotlin-module)  
要するに同一のコンパイル単位なら参照可能っぽい。同一jarとか考えるとまぁいいかなぁ。



## classのキーワード

### open

classの継承をする際に親クラスにつける。
Kotlinではデフォルトで継承不可なクラスとなっている。

    open class Hoge

### data

toString、hashCode,equalsなどの基本的な関数を定義したクラスを使いたいときに用いる。data classをつくる時のmodifier

    data class Hoge(val fuga : String)


[Data Classes](https://kotlinlang.org/docs/reference/data-classes.html)



### object

シングルトンなオブジェクトとか作りたいときに使う。

  
[Object Expressions and Declarations](https://kotlinlang.org/docs/reference/object-declarations.html)


### sealed

sealed classは厳密に制限された階層構造のクラスを作りたい時に使う。
enumも制限したものを持つ階層構造のものであるが、enumと違うのはサブクラスで扱える点、
複数のインスタンスを持てる点など。

[Classes and Inheritance](https://kotlinlang.org/docs/reference/classes.html)
より引用


    sealed class Expr {
        class Const(val number: Double) : Expr()
        class Sum(val e1: Expr, val e2: Expr) : Expr()
        object NotANumber : Expr()
    }


実装サンプル


    fun main(args: Array<String>) {    
        val color = Color.Red
        val colorName = selectColorName(color)
        println(colorName)
    }
    
    fun selectColorName(color : Color) = when(color) {
            Color.Red -> "赤"
            Color.Green -> "青"
            Color.Yellow -> "黄"    
    }


    sealed class Color {
        object Red : Color()
        object Green : Color()
        object Yellow : Color()
    }


when式にelseがないのはsealedのとりうる全ての分岐を網羅している事をコンパイラが認識しているから。

参考
[Kotlin M13で追加されたsealed class](http://taro.hatenablog.jp/entry/2015/09/17/131439)

## 関数につけるキーワード

### inline

関数をインライン化することが出来、毎回インスタンス生成するようなオーバーヘッドを避けられる。
inline関数内の特定の箇所をinline化したくない場合はnoinlineを使うようだ。

[Inline Functions](https://kotlinlang.org/docs/reference/inline-functions.html)




### infix

infixを関数に付与することで中置記法が出来る。

[Functions](https://kotlinlang.org/docs/reference/functions.html)


### tailrec

末尾再起最適化をするためのキーワード。
これでスタックオーバーフローとかしないようになるので安心。

    tailrec fun findFixPoint(x: Double = 1.0): Double
        = if (x == Math.cos(x)) x else findFixPoint(Math.cos(x))

#### 注意！！

tailrecはScalaの意味するものと異なる。  
Scalaの場合はTailrecアノテーションを  
つけなくても末尾再帰最適化されるがKotlinの場合は、  
このキーワードをつけない限り末尾再帰最適化されない。  

Scalaはチェックアノテーションになっているらしい。

### operator

演算子オーバーロードをしたいときに使う。

### const

定数に使う。ただし、String型かプリミティブ型にしか利用できない。

    const val SUBSYSTEM_DEPRECATED: String = "This subsystem is deprecated"

## companion

staticな関数とかを使いときに使う。companion object。

    class MyClass {
      companion object Factory {
        fun create(): MyClass = MyClass()
      }
    }
    
    
    val instance = MyClass.create()
    
    

[Object Expressions and Declarations](https://kotlinlang.org/docs/reference/object-declarations.html)

## is

型チェックなどで使われる。Javaでいうinstanceofが近い。

## in

ジェネリクスで入力のみ可能なコレクションクラスの宣言とかに使う。型パラメータを反変(contravariant)にする。

## out

ジェネリクスで出力のみ可能なコレクションクラスの宣言とかに使う。

## vararg

引数を可変長としたいときに使う

## as

別名をつける場合、キャストする場合に利用する。

    import java.math.BigDecimal as Decimal


    var hoge = Hoge() as Fuga

## by

フィールドのデリゲーションで使う。

    class Example {
      var p: String by Delegate()
    }
    
The syntax is: val/var <property name>: <Type> by <expression>.

## to

toはPairの拡張関数。イディオム的に使える。

     val map = mapOf("a" to 1, "b" to 2)

toの関数定義はこんな感じ。

    infix fun <A, B> A.to(that: B): Pair<A, B> (source)

[https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/to.html](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/to.html)


## reified

読み方はカタカナで書くならリーアファイドとかレイアファイドとか。
javaのクラス指定を少し簡略化してような代物。

    inline fun <reified T> membersOf() = T::class.members


    fun main(s: Array<String>) {
        println(membersOf<StringBuilder>().joinToString("\n"))
        // こう書かなくて良い println(membersOf<StringBuilder::class.java>().joinToString("\n"))
    }
    
    
たろうさんの記事が詳しい。(M10と現在だとややjavaClass<T>とかあたりは変わっている)
[Kotlin M10で追加されたreified type parameterがいい感じ](http://taro.hatenablog.jp/entry/2015/01/29/223239)

[Reified type parameters](https://kotlinlang.org/docs/reference/inline-functions.html#reified-type-parameters)


## let,with,run,apply

スコープ関数と言われる便利なやつ

[Kotlin スコープ関数 用途まとめ](http://qiita.com/ngsw_taro/items/d29e3080d9fc8a38691e)



    hoge.let{}
    with(hoge){}
    hoge.run{}
    hoge.apply{}


## lateinit

主に@Injectや@AutowiredなどのDIでフィールドインジェクションをしたい場合に
フィールドにつける。あとはJUnitの@SetUpなど。
変数はvarにしておかないとダメなのがちょっとやだ。

    lateinit var subject: TestSubject


## constructor

複数のコンストラクタを定義したい場合、使う。
このキーワードを使わない場合はプライマリコンストラクタとなる。

## init

初期化ブロックのキーワード


    init {
        // フィールド初期化などの処理
    }


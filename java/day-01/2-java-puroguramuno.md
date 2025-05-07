# 2. Java プログラムの基本構造

### 2.1 クラスとメソッドの概念

Java は完全なオブジェクト指向プログラミング言語で、すべてのコードはクラス内に記述する必要があります。

#### 2.1.1 クラスとは

**クラス**は、オブジェクトの設計図のようなもので、データ (属性) と動作 (メソッド) を定義します。Java プログラムは必ず 1つ以上のクラスから構成されます。

```java
public class MyClass {
    // クラスの内容
}
```

> 📝 **ポイント**: Java ファイル名は、必ず `public` クラス名と一致させる必要があります。例：`MyClass.java` には `public class MyClass` を定義

#### 2.1.2 メソッドとは

**メソッド**は、特定の処理を実行するコードブロックです。プログラムの動作を定義します。

```java
public void sayHello() {
    System.out.println("Hello!");
}
```

> 📝 **ポイント**: メソッドは、クラス内に定義されます。Java では、単独でメソッドを定義することはできません。

### 2.2 main メソッドの役割

`main` メソッドは Java プログラムの**エントリーポイント** (開始地点) です。プログラム実行時に最初に呼び出されます。

#### 2.2.1 main メソッドの構文

```java
public static void main(String[] args) {
    // プログラムの処理を記述
}
```

**シグネチャーの詳細：**

* `public`: どこからでもアクセス可能
* `static`: インスタンス化せずに呼び出し可能
* `void`: 戻り値なし
* `String[] args`: コマンドライン引数の配列

#### 2.2.2 最小限の Java プログラム

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

> 📝 **ポイント**: `main` メソッドがない Java プログラムは、単独では実行できません。ライブラリーやユーティリティクラスは main メソッドを持たないことがあります。

### 2.3 コンパイルと実行の流れ

Java プログラムは、コンパイル後にバイトコードに変換され、JVM (Java Virtual Machine) 上で実行されます。

#### 2.3.1 コンパイルの流れ

1. **ソースコード作成** (.java ファイル)
2. **コンパイル** (javac コマンド)
3. **バイトコード生成** (.class ファイル)
4. **実行** (java コマンド)

```
HelloWorld.java → (javac) → HelloWorld.class → (java) → 実行
```

#### 2.3.2 コマンドラインでのコンパイルと実行

```cmd
# コンパイル
javac HelloWorld.java

# 実行
java HelloWorld
```

> 📝 **ポイント**: 実行時には `.class` 拡張子を付けません。クラス名のみを指定します。

### 2.4 VS Code での実行方法

VS Code では、Java プログラムを簡単に実行する方法がいくつかあります。

#### 2.4.1 Run Java ボタンの使用

1. Java ファイルを開く
2. エディタ右上の 「Run」 ボタン ( <kbd>▷</kbd> ) をクリック
3. または、コード内の main メソッドの上にある 「Run」 リンクをクリック

#### 2.4.2 ショートカットキーでの実行

* **デバッグなしで実行**: <kbd>Ctrl</kbd> + <kbd>F5</kbd>
* **デバッグ実行**: <kbd>F5</kbd>

#### 2.4.3 コンテキストメニューの使用

1. Java ファイル内で右クリック
2. 「Run Java」 を選択

> 📝 **ポイント**: VS Code の Java 拡張機能は、コンパイルから実行までを自動で行います。コマンドラインでの手動操作は不要です。

### 2.5 System.out.println() の使用

`System.out.println()` は、コンソールに文字列を出力するための基本的なメソッドです。

#### 2.5.1 基本的な使用方法

```java
public class OutputExample {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");    // 改行あり
        System.out.print("Hello");             // 改行なし
        System.out.print(" World!");
        System.out.println();                  // 空行
    }
}
```

<details>

<summary>実行結果</summary>

```
Hello, Java!
Hello World!

```

</details>

#### 2.5.2 複数行の出力

```java
public class MultilineOutput {
    public static void main(String[] args) {
        System.out.println("1行目");
        System.out.println("2行目");
        System.out.println("3行目");
    }
}
```

<details>

<summary>実行結果</summary>

```
1行目
2行目
3行目
```

</details>

> 📝 **ポイント**: `println` は文字列の最後に改行を追加しますが、`print` は改行を追加しません。

### 2.6 コメントの書き方

コメントは、コードの説明や一時的に無効化したいコードに使用します。コンパイル時に無視されます。

#### 2.6.1 一行コメント

```java
// これは一行コメントです
int age = 25; // 変数の説明コメント
```

#### 2.6.2 複数行コメント

```java
/* これは
   複数行にわたる
   コメントです */
```

#### 2.6.3 ドキュメントコメント

ドキュメントコメントは、API ドキュメントの自動生成に使用されます。

```java
/**
 * このクラスは Hello World プログラムを実装します。
 * 
 * @author Taro
 * @version 1.0
 */
public class HelloWorld {
    /**
     * プログラムのメインエントリーポイントです。
     * 
     * @param args コマンドライン引数
     */
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

> 📝 **ポイント**: ドキュメントコメントは、クラスやメソッドの直前に記述し、`/**` で始め、`*/` で終わります。

***

### 演習問題

#### 問題 2-1: クラスとメソッドの理解

以下の Java コードを見て、クラス名とメソッド名を特定してください。

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    
    public static void main(String[] args) {
        Calculator calc = new Calculator();
        int result = calc.add(5, 3);
        System.out.println("Result: " + result);
    }
}
```

<details>

<summary>解答</summary>

* クラス名: `Calculator`
* メソッド名:
  * `add`
  * `main`

</details>

#### 問題 2-2: 簡単なプログラムの作成

自分の名前と年齢を出力する Java プログラムを作成してください。

<details>

<summary>ヒント</summary>

* クラス名は `MyProfile` にする
* `main` メソッドを作成
* `System.out.println()` を使用
* 名前と年齢を 2行に分けて出力

</details>

<details>

<summary>解答例</summary>

```java
public class MyProfile {
    public static void main(String[] args) {
        System.out.println("私の名前は山田太郎です");
        System.out.println("私は25歳です");
    }
}
```

</details>

#### 問題 2-3: コンパイルエラーの修正

以下のコードにはエラーがあります。修正してください。

```java
public class WrongCode {
    public static void Main(String[] args) {
        System.out.println(Hello World);
    }
}
```

<details>

<summary>ヒント</summary>

* `main` メソッドの名前を確認
* 文字列の記述方法を確認

</details>

<details>

<summary>解答</summary>

<pre class="language-java"><code class="lang-java"><strong>public class WrongCode {
</strong>    public static void main(String[] args) {  // 'Main' → 'main'
        System.out.println("Hello World");    // 文字列をダブルクォートで囲む
    }
}
</code></pre>

修正ポイント:

* `Main` → `main` (Java は大文字小文字を区別)
* 文字列は必ずダブルクォートで囲む

</details>

#### 問題 2-4: コメントの追加

以下のコードに、指定された箇所にコメントを追加してください。

```java
public class Student {
    public static void main(String[] args) {
        System.out.println("学生番号: A1234567");
        System.out.println("名前: 山田太郎");
    }
}
```

**追加するコメント：**

1. クラスの上に、「学生情報を表示するクラス」 というドキュメントコメントを追加
2. `main` メソッドの中の各 `println` の前に、何を出力するかを説明する一行コメントを追加

<details>

<summary>解答例</summary>

```java
/**
 * 学生情報を表示するクラス
 */
public class Student {
    public static void main(String[] args) {
        // 学生番号を出力
        System.out.println("学生番号: A1234567");
        
        // 名前を出力
        System.out.println("名前: 山田太郎");
    }
}
```



</details>

#### 問題 2-5: 箱文字の表現

`System.out.println()` を使用して、以下の箱文字 (ボックス) を出力するプログラムを作成してください。

```
+-----+
|HELLO|
|JAVA |
+-----+
```

**要件：**

* クラス名は `BoxText` とする
* 4 つの `println` で出力する
* 1 行目と 4 行目は `+-----+`
* 2 行目は `|HELLO|`
* 3 行目は `|JAVA |`

<details>

<summary>解答例</summary>

```java
public class BoxText {
    public static void main(String[] args) {
        System.out.println("+-----+");
        System.out.println("|HELLO|");
        System.out.println("|JAVA |");
        System.out.println("+-----+");
    }
}
```

</details>

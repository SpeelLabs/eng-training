# 3. メソッド ①

### 3.1 メソッドとは

**メソッド**は、特定のタスクを実行するためにひとまとめにされたコードブロックです。メソッドを使うことで、プログラムをより構造化し、コードの再利用性を高めることができます。

メソッドは関数とも呼ばれますが、Java では基本的にすべての関数はクラスの中で定義されるため、「メソッド」という名称が使われます。

#### 3.1.1 メソッドの利点

メソッドを使用することには以下のような利点があります：

1. **コードの再利用**: 同じ処理を複数回書く代わりに、メソッドとして一度定義して何度も呼び出せます
2. **コードの整理**: 関連するコードをまとめることで、プログラムが整理され、理解しやすくなります
3. **保守性の向上**: 変更が必要になった場合、一箇所を修正するだけで済みます
4. **抽象化**: 複雑な処理を単純な名前で呼び出せるようになります

例えば、挨拶を表示する処理を考えてみましょう：

```java
// メソッドを使わない場合
System.out.println("こんにちは、山田さん！");
System.out.println("今日の天気は晴れです。");
System.out.println("良い一日を！");

// 同じ処理を別の場所でも行いたい場合、同じコードを書く必要がある
System.out.println("こんにちは、山田さん！");
System.out.println("今日の天気は晴れです。");
System.out.println("良い一日を！");

// メソッドを使う場合
greet();  // メソッドを呼び出す
// 別の場所でも
greet();  // 同じメソッドを呼び出すだけで済む
```

メソッドを使うと、繰り返しの多いコードを効率的に記述できるようになります。

### 3.2 メソッドの基本構造

Java のメソッドは以下の基本構造を持ちます：

```java
アクセス修飾子 返り値の型 メソッド名(引数リスト) {
    // メソッドの本体
    // 実行したい処理
    return 戻り値;  // 必要に応じて値を返す
}
```

ここで：

* **アクセス修飾子**: メソッドのアクセス範囲を指定します（例: `public`, `private`）
* **返り値の型**: メソッドが返す値のデータ型を指定します（例: `int`, `String`, `void`）
* **メソッド名**: メソッドを呼び出すために使用する名前です
* **引数リスト**: メソッドに渡すデータ（パラメータ）を指定します
* **return 文**: メソッドの処理結果を呼び出し元に返します（返り値が `void` の場合は省略可能）

#### 3.2.1 シンプルなメソッドの例

```java
// 挨拶を表示するメソッド
public static void greet() {
    System.out.println("こんにちは、山田さん！");
    System.out.println("今日の天気は晴れです。");
    System.out.println("良い一日を！");
}
```

この例では：

* `public`: このメソッドは他のクラスからもアクセスできることを示します
* `static`: このメソッドはクラスのインスタンスを作成せずに呼び出せることを示します
* `void`: このメソッドは何も値を返さないことを示します
* `greet`: メソッドの名前です
* 空の括弧 `()`: このメソッドは引数を受け取らないことを示します

> 📝 **ポイント**: 初学者の段階では、メソッドの先頭に `public static` を付けることが多いです。これはメインメソッド（`main`）と同様に、クラスのインスタンスを作成せずに呼び出せるようにするためです。

### 3.3 メソッドの定義と呼び出し

#### 3.3.1 メソッドの定義

メソッドを定義するということは、メソッドの名前、処理内容、入力（引数）と出力（戻り値）を指定することです。以下はシンプルなメソッドの定義例です：

```java
public class MethodExample {
    // メソッドの定義
    public static void sayHello() {
        System.out.println("Hello, World!");
    }
    
    public static void main(String[] args) {
        // メインメソッド
    }
}
```

#### 3.3.2 メソッドの呼び出し

定義したメソッドを使用するには、メソッド名と括弧を使用して呼び出します：

```java
public class MethodExample {
    public static void sayHello() {
        System.out.println("Hello, World!");
    }
    
    public static void main(String[] args) {
        // メソッドの呼び出し
        sayHello();  // 出力: Hello, World!
        
        // 同じメソッドを複数回呼び出すことも可能
        sayHello();  // 出力: Hello, World!
        sayHello();  // 出力: Hello, World!
    }
}
```

メソッドを呼び出すと、プログラムの実行はメソッドの中に移り、メソッドの処理がすべて完了した後、元の場所に戻ってきます。

#### 3.3.3 メソッドの実行タイミング

重要なポイントとして、**メソッドは定義しただけでは実行されません**。メソッドが実行されるのは、そのメソッドが呼び出されたときだけです。

```java
public class MethodTiming {
    // メソッドの定義
    public static void printMessage() {
        System.out.println("このメッセージはいつ表示される？");
    }
    
    public static void main(String[] args) {
        System.out.println("プログラム開始");
        // ここでは printMessage() メソッドは実行されない
        
        // メソッドを呼び出したときに初めて実行される
        printMessage();
        
        System.out.println("プログラム終了");
    }
}
```

実行結果：

```
プログラム開始
このメッセージはいつ表示される？
プログラム終了
```

> 📝 **ポイント**: メソッドは「レシピ」のようなものです。レシピを書いただけでは料理ができないように、メソッドを定義しただけでは処理は実行されません。メソッドを呼び出すことで、そのレシピに従って実際に処理が行われます。

### 3.4 void メソッド

何も値を返さないメソッドは、戻り値の型として `void` を指定します。このようなメソッドは、何らかの処理を行うだけで、結果として値を返さないメソッドです。

```java
public static void printLine() {
    System.out.println("----------------");
}

public static void printSquare(int size) {
    for (int i = 0; i < size; i++) {
        for (int j = 0; j < size; j++) {
            System.out.print("* ");
        }
        System.out.println();
    }
}
```

`void` メソッドは `return` 文を省略できますが、メソッドを途中で終了させたい場合は、引数なしの `return;` を使用できます：

```java
public static void printPositiveNumber(int number) {
    if (number < 0) {
        System.out.println("負の数は表示できません。");
        return;  // メソッドをここで終了
    }
    System.out.println("数値: " + number);
}
```

> 📝 **ポイント**: `void` メソッドは、表示や計算など、処理そのものが目的で、値を返す必要がない場合に使用します。

### 3.5 引数（パラメータ）の使用

**引数**（または**パラメータ**）は、メソッドに渡す値です。これにより、メソッドの動作をカスタマイズできます。

#### 3.5.1 単一の引数

```java
// 名前を引数として受け取り、挨拶を表示するメソッド
public static void greet(String name) {
    System.out.println("こんにちは、" + name + "さん！");
}

// メソッドの呼び出し
greet("山田");  // 出力: こんにちは、山田さん！
greet("佐藤");  // 出力: こんにちは、佐藤さん！
```

#### 3.5.2 複数の引数

メソッドは複数の引数を受け取ることができます。引数はカンマで区切って指定します：

```java
// 2つの数値を受け取り、四則演算の結果を表示するメソッド
public static void calculate(int a, int b) {
    System.out.println(a + " + " + b + " = " + (a + b));
    System.out.println(a + " - " + b + " = " + (a - b));
    System.out.println(a + " * " + b + " = " + (a * b));
    System.out.println(a + " / " + b + " = " + (a / b));
}

// メソッドの呼び出し
calculate(10, 5);
```

実行結果：

```
10 + 5 = 15
10 - 5 = 5
10 * 5 = 50
10 / 5 = 2
```

#### 3.5.3 引数の型

引数には様々なデータ型を指定できます：

```java
// int型の引数
public static void displaySquare(int number) {
    System.out.println(number + "の2乗: " + (number * number));
}

// double型の引数
public static void displayCircleArea(double radius) {
    System.out.println("半径" + radius + "の円の面積: " + (Math.PI * radius * radius));
}

// boolean型の引数
public static void displayStatus(boolean isActive) {
    if (isActive) {
        System.out.println("現在アクティブです。");
    } else {
        System.out.println("現在非アクティブです。");
    }
}

// String型の引数
public static void displayLength(String text) {
    System.out.println("文字列「" + text + "」の長さ: " + text.length());
}
```

#### 3.5.4 引数の順序

メソッドを呼び出す際は、引数を宣言された順序通りに渡す必要があります：

```java
// 名前と年齢を受け取るメソッド
public static void displayProfile(String name, int age) {
    System.out.println("名前: " + name);
    System.out.println("年齢: " + age + "歳");
}

// 正しい呼び出し
displayProfile("田中", 25);  // 名前が先、年齢が後

// 間違った呼び出し
// displayProfile(25, "田中");  // コンパイルエラー: 型が合わない
```

> 📝 **ポイント**: 引数は実際の値をメソッドに渡す役割があります。メソッドが特定の処理をカスタマイズできるようにするために利用されます。

***

#### 演習問題

**問題 3-1: 基本的なメソッドの定義と呼び出し**

以下のプログラムを完成させてください。`printHeader` メソッドを定義して、プログラムのヘッダー部分を表示するようにしてください。

```java
public class HeaderExample {
    public static void main(String[] args) {
        // printHeader メソッドを呼び出してください
        
        // メインの処理
        System.out.println("これはプログラムの本体部分です。");
        
        // printHeader メソッドをもう一度呼び出してください
    }
    
    // printHeader メソッドを定義してください
    // このメソッドは "==== プログラム開始 ====" という文字列を表示します
    
}
```

<details>

<summary>ヒント</summary>

* `void` 型メソッドを定義します
* メソッド名は `printHeader` とします
* メソッドの中で `System.out.println()` を使用して文字列を表示します
* `main` メソッドから `printHeader()` を呼び出します

</details>

**問題 3-2: 引数を使用したメソッド**

以下のプログラムを完成させてください。`printMessage` メソッドを定義して、引数で指定されたメッセージを表示するようにしてください。

```java
public class MessageExample {
    public static void main(String[] args) {
        // 引数を変えて printMessage メソッドを呼び出してください
        // 1回目: "おはようございます！"
        
        // 2回目: "こんにちは！"
        
        // 3回目: "こんばんは！"
    }
    
    // printMessage メソッドを定義してください
    // このメソッドは String 型の引数 message を受け取り、それを表示します
    
}
```

<details>

<summary>ヒント</summary>

* `void` 型メソッドを定義します
* メソッド名は `printMessage` とします
* メソッドは `String` 型の引数 `message` を受け取ります
* メソッドの中で引数 `message` の値を `System.out.println()` で表示します
* `main` メソッドから異なる文字列を引数として `printMessage()` を呼び出します

</details>

**問題 3-3: 複数の引数を使用するメソッド**

以下のプログラムを完成させてください。`displayProfile` メソッドを定義して、引数で指定された名前と年齢を整形して表示するようにしてください。

```java
public class ProfileExample {
    public static void main(String[] args) {
        // 異なる引数で displayProfile メソッドを呼び出してください
        // 名前: "田中", 年齢: 25
        
        // 名前: "佐藤", 年齢: 30
        
        // 名前: "鈴木", 年齢: 20
    }
    
    // displayProfile メソッドを定義してください
    // このメソッドは String 型の name と int 型の age を受け取り、
    // "名前: [name], 年齢: [age]歳" の形式で表示します
    
}
```

<details>

<summary>ヒント</summary>

* `void` 型メソッドを定義します
* メソッド名は `displayProfile` とします
* メソッドは `String` 型の `name` と `int` 型の `age` の2つの引数を受け取ります
* メソッドの中で引数を使用して指定された形式でプロフィールを表示します
* `main` メソッドから異なる名前と年齢を引数として `displayProfile()` を呼び出します

</details>

**問題 3-4: 条件分岐を含むメソッド**

以下のプログラムを完成させてください。`checkAge` メソッドを定義して、引数で指定された年齢が18歳以上なら「成人です」、18歳未満なら「未成年です」と表示するようにしてください。

```java
public class AgeCheckExample {
    public static void main(String[] args) {
        // 異なる年齢で checkAge メソッドを呼び出してください
        // 年齢: 15
        
        // 年齢: 18
        
        // 年齢: 25
    }
    
    // checkAge メソッドを定義してください
    // このメソッドは int 型の引数 age を受け取り、
    // age が 18 以上なら「成人です」、18 未満なら「未成年です」と表示します
    
}
```

<details>

<summary>ヒント</summary>

* `void` 型メソッドを定義します
* メソッド名は `checkAge` とします
* メソッドは `int` 型の引数 `age` を受け取ります
* メソッドの中で `if-else` 文を使って年齢を判定し、適切なメッセージを表示します
* `main` メソッドから異なる年齢を引数として `checkAge()` を呼び出します

</details>

**問題 3-5: メソッドの中でメソッドを呼び出す**

以下のプログラムを完成させてください。`printSquare` メソッドを定義して、指定されたサイズの正方形をアスタリスク（\*）で出力するようにしてください。また、`printLine` メソッドを定義して、指定された数のハイフン（-）を出力するようにしてください。`printSquare` メソッドでは、正方形の出力前後に `printLine` メソッドを呼び出すようにしてください。

```java
public class NestedMethodExample {
    public static void main(String[] args) {
        // printSquare メソッドを呼び出してください（サイズ: 4）
        
    }
    
    // printLine メソッドを定義してください
    // このメソッドは指定された数のハイフン(-)を一行に出力します
    
    
    // printSquare メソッドを定義してください
    // このメソッドは、指定されたサイズの正方形をアスタリスク(*)で出力します
    // 出力の前後には printLine メソッドを呼び出して、ハイフンの行を表示します
    
}
```

<details>

<summary>ヒント</summary>

* まず `printLine` メソッドを定義します
  * `void` 型メソッドで、`int` 型の引数 `count` を受け取ります
  * for ループを使用して、指定された数のハイフンを出力します
  * 最後に改行を出力します
* 次に `printSquare` メソッドを定義します
  * `void` 型メソッドで、`int` 型の引数 `size` を受け取ります
  * 最初に `printLine(size)` を呼び出します
  * ネストした for ループを使用して、`size` × `size` のアスタリスク正方形を出力します
  * 最後に再度 `printLine(size)` を呼び出します
* `main` メソッドから `printSquare(4)` を呼び出します

</details>

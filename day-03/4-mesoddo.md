# 4. メソッド ②

### 4.1 戻り値のあるメソッド

前のセクションでは `void` メソッド（値を返さないメソッド）について学びました。今度は、値を返すメソッドについて見ていきましょう。

**戻り値のあるメソッド**は、処理の結果として呼び出し元に値を返すメソッドです。戻り値の型は、メソッドの宣言時に指定します。

#### 4.1.1 戻り値の基本

戻り値のあるメソッドは以下のような形式で宣言します：

```java
public static 戻り値の型 メソッド名(引数リスト) {
    // メソッドの処理
    return 戻り値;  // 戻り値を返す
}
```

例えば、2つの整数の和を計算して返すメソッドは次のように書けます：

```java
// 2つの整数の和を計算して返すメソッド
public static int sum(int a, int b) {
    int result = a + b;
    return result;  // 計算結果を返す
}
```

このメソッドは `int` 型の値を返すため、メソッドの戻り値の型として `int` が指定されています。メソッドの最後で `return` 文を使用して、計算結果を呼び出し元に返しています。

#### 4.1.2 戻り値の使用

戻り値のあるメソッドを呼び出す場合、その戻り値を変数に代入したり、式の一部として使用したりできます：

```java
public class ReturnValueExample {
    public static void main(String[] args) {
        // 戻り値を変数に代入
        int result1 = sum(5, 3);
        System.out.println("5 + 3 = " + result1);  // 出力: 5 + 3 = 8
        
        // 戻り値を式の一部として使用
        int result2 = sum(2, 4) * 2;
        System.out.println("(2 + 4) * 2 = " + result2);  // 出力: (2 + 4) * 2 = 12
        
        // 戻り値を直接出力
        System.out.println("10 + 20 = " + sum(10, 20));  // 出力: 10 + 20 = 30
    }
    
    public static int sum(int a, int b) {
        return a + b;
    }
}
```

この例では、`sum` メソッドの戻り値をさまざまな方法で使用しています。戻り値は、変数に代入したり、別の計算で使用したり、直接出力したりできます。

> 📝 **ポイント**: 戻り値のあるメソッドを使うと、計算結果や処理結果を呼び出し元に返すことができます。これにより、メソッドの結果を他の処理で再利用できます。

#### 4.1.3 さまざまな戻り値の型

戻り値はさまざまなデータ型にすることができます：

```java
// int型の戻り値
public static int square(int number) {
    return number * number;
}

// double型の戻り値
public static double calculateCircleArea(double radius) {
    return Math.PI * radius * radius;
}

// boolean型の戻り値
public static boolean isEven(int number) {
    return number % 2 == 0;
}

// String型の戻り値
public static String getGreeting(String name) {
    return "こんにちは、" + name + "さん！";
}
```

#### 4.1.4 return 文の重要性

戻り値のあるメソッドでは、**必ず** `return` 文を使用して値を返す必要があります。`return` 文がない場合や、すべての実行パスで値が返されない場合は、コンパイルエラーが発生します。

```java
// 正しい例
public static int absoluteValue(int number) {
    if (number >= 0) {
        return number;
    } else {
        return -number;
    }
}

// 誤った例: すべての実行パスで値が返されない
public static int badMethod(int number) {
    if (number > 0) {
        return number;
    }
    // 負の数の場合、return文がないためコンパイルエラー
}
```

> ⚠️ **注意**: 戻り値を持つメソッドでは、すべての実行パスで必ず値が返されるようにしてください。条件分岐がある場合は、すべての条件で適切な値が返されることを確認してください。

### 4.2 メソッドのオーバーロード

**メソッドのオーバーロード**とは、同じ名前のメソッドを複数定義することです。ただし、それぞれのメソッドは引数の数や型が異なっている必要があります。

#### 4.2.1 オーバーロードの基本

例えば、異なる型や数の引数を受け取る `add` メソッドをオーバーロードする例を見てみましょう：

```java
public class OverloadExample {
    // 2つの整数を加算
    public static int add(int a, int b) {
        return a + b;
    }
    
    // 3つの整数を加算
    public static int add(int a, int b, int c) {
        return a + b + c;
    }
    
    // 2つの浮動小数点数を加算
    public static double add(double a, double b) {
        return a + b;
    }
    
    public static void main(String[] args) {
        System.out.println("2 + 3 = " + add(2, 3));  // 最初のメソッドが呼ばれる
        System.out.println("2 + 3 + 4 = " + add(2, 3, 4));  // 2番目のメソッドが呼ばれる
        System.out.println("2.5 + 3.5 = " + add(2.5, 3.5));  // 3番目のメソッドが呼ばれる
    }
}
```

この例では、`add` メソッドが3種類定義されています。メソッド呼び出し時の引数の数と型に基づいて、Java は適切なメソッドを自動的に選択します。

> 📝 **ポイント**: オーバーロードを使うと、同様の機能を持つが引数が異なるメソッドに同じ名前を付けることができます。これにより、メソッド名を覚えやすくし、コードの可読性が向上します。

#### 4.2.2 オーバーロードの制限

メソッドをオーバーロードする場合、次の点に注意してください：

1. **引数が異なる必要がある**: 引数の数または型が異なる必要があります
2. **戻り値の型だけが異なるのは不可**: 引数が同じで戻り値の型だけが異なるメソッドは定義できません

```java
// 正しいオーバーロード（引数の型が異なる）
public static int process(int value) { ... }
public static int process(double value) { ... }

// 正しいオーバーロード（引数の数が異なる）
public static int calculate(int a) { ... }
public static int calculate(int a, int b) { ... }

// 誤ったオーバーロード（引数が同じで戻り値の型だけが異なる）
public static int convert(String text) { ... }
public static double convert(String text) { ... }  // コンパイルエラー
```

### 4.3 スコープと変数の有効範囲

プログラミングにおける**スコープ**とは、変数が有効な範囲のことです。Java におけるスコープの主な種類について見ていきましょう。

#### 4.3.1 メソッド内のローカル変数

メソッド内で宣言された変数は**ローカル変数**と呼ばれ、そのメソッド内でのみ有効です：

```java
public static void exampleMethod() {
    int x = 10;  // ローカル変数
    System.out.println("メソッド内: x = " + x);
}

public static void anotherMethod() {
    // System.out.println(x);  // エラー: x はこのスコープでは使用できない
}
```

#### 4.3.2 ブロックスコープ

Java では、中括弧 `{}` で囲まれた部分がブロックです。ブロック内で宣言された変数は、そのブロック内でのみ有効です：

```java
public static void blockScopeExample() {
    // メソッドスコープ
    int a = 10;
    
    // ブロックスコープ
    {
        int b = 20;  // このブロック内でのみ有効
        System.out.println("ブロック内: a = " + a + ", b = " + b);
    }
    
    System.out.println("ブロック外: a = " + a);
    // System.out.println("ブロック外: b = " + b);  // エラー: b はこのスコープでは使用できない
}
```

#### 4.3.3 制御構造のスコープ

for ループや if 文などの制御構造も独自のスコープを持ちます：

```java
public static void controlStructureScope() {
    // for ループのスコープ
    for (int i = 0; i < 3; i++) {
        System.out.println("ループ内: i = " + i);
    }
    // System.out.println("ループ外: i = " + i);  // エラー: i はこのスコープでは使用できない
    
    // if 文のスコープ
    if (true) {
        int j = 5;  // このブロック内でのみ有効
        System.out.println("if ブロック内: j = " + j);
    }
    // System.out.println("if ブロック外: j = " + j);  // エラー: j はこのスコープでは使用できない
}
```

> 📝 **ポイント**: 変数は宣言されたブロック内でのみ有効です。内側のブロックからは外側のブロックの変数にアクセスできますが、外側のブロックからは内側のブロックの変数にアクセスできません。

### 4.4 メソッドの再帰呼び出し

**再帰呼び出し**とは、メソッドが自分自身を呼び出すことです。再帰的なアルゴリズムを実装するために使用されます。

#### 4.4.1 再帰の基本

再帰メソッドの例として、階乗を計算するメソッドを見てみましょう。階乗とは、1 から指定された数までの積です。例えば、5 の階乗（5!）は 5 × 4 × 3 × 2 × 1 = 120 となります。

```java
public static int factorial(int n) {
    // 基底ケース: 再帰を終了させる条件
    if (n == 0 || n == 1) {
        return 1;
    }
    // 再帰ケース: 自分自身を呼び出す
    else {
        return n * factorial(n - 1);
    }
}
```

この例では、`factorial` メソッドが自分自身を呼び出しています。`n` が 0 か 1 の場合は再帰を終了し、それ以外の場合は自分自身を呼び出して計算を続けます。

例えば、`factorial(5)` の計算は以下のように進みます：

```
factorial(5) = 5 * factorial(4)
             = 5 * (4 * factorial(3))
             = 5 * (4 * (3 * factorial(2)))
             = 5 * (4 * (3 * (2 * factorial(1))))
             = 5 * (4 * (3 * (2 * 1)))
             = 5 * (4 * (3 * 2))
             = 5 * (4 * 6)
             = 5 * 24
             = 120
```

#### 4.4.2 再帰のもう一つの例: フィボナッチ数列

フィボナッチ数列は、最初の2つの数が 0 と 1 で、それ以降の数がその前の2つの数の和になる数列です：0, 1, 1, 2, 3, 5, 8, 13, 21, ...

フィボナッチ数列の n 番目の数を計算する再帰メソッドは以下のように記述できます：

```java
public static int fibonacci(int n) {
    // 基底ケース
    if (n == 0) {
        return 0;
    } else if (n == 1) {
        return 1;
    }
    // 再帰ケース
    else {
        return fibonacci(n - 1) + fibonacci(n - 2);
    }
}
```

この例では、`fibonacci` メソッドは `n - 1` 番目と `n - 2` 番目のフィボナッチ数を再帰的に計算し、その和を返します。

> ⚠️ **注意**: 再帰呼び出しは強力ですが、適切に設計しないとスタックオーバーフロー（メモリ不足）を引き起こす可能性があります。必ず基底ケース（再帰を終了させる条件）を用意し、無限に再帰しないようにしてください。

### 4.5 メソッドの応用例

#### 4.5.1 配列を処理するメソッド

配列の要素の合計を計算するメソッドを作成してみましょう：

```java
public static int sumArray(int[] array) {
    int sum = 0;
    for (int value : array) {
        sum += value;
    }
    return sum;
}

// 使用例
public static void main(String[] args) {
    int[] numbers = {1, 2, 3, 4, 5};
    int total = sumArray(numbers);
    System.out.println("配列の合計: " + total);  // 出力: 配列の合計: 15
}
```

#### 4.5.2 ヘルパーメソッド

複雑なタスクを小さな部分に分割し、それぞれをメソッドとして実装することで、コードの可読性と再利用性を高めることができます。

例えば、学生の成績を管理するプログラムで、平均点を計算するメソッドと、成績を評価するメソッドを別々に作成できます：

```java
// 平均点を計算するメソッド
public static double calculateAverage(int[] scores) {
    int sum = 0;
    for (int score : scores) {
        sum += score;
    }
    return (double) sum / scores.length;
}

// 成績を評価するメソッド
public static char getGrade(double average) {
    if (average >= 90) {
        return 'A';
    } else if (average >= 80) {
        return 'B';
    } else if (average >= 70) {
        return 'C';
    } else if (average >= 60) {
        return 'D';
    } else {
        return 'F';
    }
}

// メインメソッドでこれらを使用
public static void main(String[] args) {
    int[] studentScores = {85, 92, 78, 65, 88};
    double average = calculateAverage(studentScores);
    char grade = getGrade(average);
    
    System.out.println("平均点: " + average);
    System.out.println("成績: " + grade);
}
```

このように、メソッドを使用して、プログラムを小さくて管理しやすい部分に分割することで、コードの品質が向上します。

> 📝 **ポイント**: 一つのメソッドは一つのタスクだけを担当するようにすると、コードが理解しやすく、テストしやすくなります。これは「単一責任の原則」と呼ばれるプログラミングの基本原則です。

***

#### 演習問題

**問題 4-1: 戻り値のあるメソッド**

以下のプログラムを完成させてください。`calculateArea` メソッドを定義して、長方形の面積を計算して返すようにしてください。

```java
public class RectangleAreaExample {
    public static void main(String[] args) {
        // calculateArea メソッドを呼び出し、結果を表示してください
        // 幅: 5.0, 高さ: 3.0
        
        // 幅: 7.5, 高さ: 2.5
        
        // 幅: 10.0, 高さ: 10.0
    }
    
    // calculateArea メソッドを定義してください
    // このメソッドは double 型の width と height を受け取り、
    // 面積 (width * height) を計算して返します
    
}
```

<details>

<summary>ヒント</summary>

* 戻り値が `double` 型のメソッドを定義します
* メソッド名は `calculateArea` とします
* メソッドは `double` 型の `width` と `height` の2つの引数を受け取ります
* メソッド内で幅と高さの積を計算し、その結果を `return` 文で返します
* `main` メソッドから `calculateArea` を呼び出し、戻り値を変数に代入するか、直接表示します

例：

```java
double area = calculateArea(5.0, 3.0);
System.out.println("面積: " + area);

// または
System.out.println("面積: " + calculateArea(5.0, 3.0));
```

</details>

**問題 4-2: さまざまな戻り値の型**

以下のプログラムを完成させてください。指定された3つのメソッドを定義してください。

```java
public class MultipleReturnsExample {
    public static void main(String[] args) {
        // isPrime メソッドを呼び出し、結果を表示してください
        // 数値: 7
        
        
        // getLetterGrade メソッドを呼び出し、結果を表示してください
        // 点数: 85
        
        
        // createGreeting メソッドを呼び出し、結果を表示してください
        // 名前: "田中"
        
    }
    
    // isPrime メソッドを定義してください
    // このメソッドは int 型の number を受け取り、
    // その数が素数かどうかを示す boolean 値を返します
    
    
    // getLetterGrade メソッドを定義してください
    // このメソッドは int 型の score を受け取り、
    // 90以上: 'A', 80以上: 'B', 70以上: 'C', 60以上: 'D', 60未満: 'F' の
    // 成績を表す char 型の値を返します
    
    
    // createGreeting メソッドを定義してください
    // このメソッドは String 型の name を受け取り、
    // "こんにちは、[name]さん！" という形式の挨拶文を返します
    
}
```

<details>

<summary>ヒント</summary>

* `isPrime` メソッドの実装：
  * `boolean` 型の戻り値を持つメソッドを定義します
  * 素数かどうかを判定するロジックを実装します（2からnumber-1までの数で割り切れるかチェック）
  * 素数ならば `true`、そうでなければ `false` を返します
* `getLetterGrade` メソッドの実装：
  * `char` 型の戻り値を持つメソッドを定義します
  * if-else if-else 構文を使用して点数に応じた成績を判定します
  * 判定結果に応じた文字 ('A', 'B', 'C', 'D', 'F') を返します
* `createGreeting` メソッドの実装：
  * `String` 型の戻り値を持つメソッドを定義します
  * 引数の `name` を使用して挨拶文を組み立てます
  * 組み立てた文字列を返します

</details>

**問題 4-3: メソッドのオーバーロード**

以下のプログラムを完成させてください。`max` メソッドを3つのバージョンでオーバーロードして、2つの整数、3つの整数、2つの浮動小数点数の中からそれぞれ最大値を返すようにしてください。

```java
public class MaxMethodExample {
    public static void main(String[] args) {
        // max メソッドを呼び出し、結果を表示してください
        // 2つの整数: 10, 15
        
        
        // 3つの整数: 10, 15, 12
        
        
        // 2つの浮動小数点数: 3.5, 2.7
        
    }
    
    // 2つの整数の最大値を返す max メソッドを定義してください
    
    
    // 3つの整数の最大値を返す max メソッドを定義してください
    
    
    // 2つの浮動小数点数の最大値を返す max メソッドを定義してください
    
}
```

<details>

<summary>ヒント</summary>

* 2つの整数の最大値を返す `max` メソッド：
  * `int` 型の戻り値を持ち、2つの `int` 型引数を受け取るメソッドを定義します
  * if 文を使用して、大きい方の値を返します
* 3つの整数の最大値を返す `max` メソッド：
  * `int` 型の戻り値を持ち、3つの `int` 型引数を受け取るメソッドを定義します
  * 3つの値を比較して最大値を返します
  * すでに定義した 2つの整数を比較する max メソッドを活用することもできます
* 2つの浮動小数点数の最大値を返す `max` メソッド：
  * `double` 型の戻り値を持ち、2つの `double` 型引数を受け取るメソッドを定義します
  * if 文を使用して、大きい方の値を返します

</details>

**問題 4-4: 配列を処理するメソッド**

以下のプログラムを完成させてください。整数配列の平均値を計算する `calculateAverage` メソッドと、配列の中から最大値を見つける `findMax` メソッドを定義してください。

```java
public class ArrayMethodsExample {
    public static void main(String[] args) {
        int[] numbers = {15, 7, 23, 42, 9, 31};
        
        // calculateAverage メソッドを呼び出し、結果を表示してください
        
        
        // findMax メソッドを呼び出し、結果を表示してください
        
    }
    
    // calculateAverage メソッドを定義してください
    // このメソッドは int 型の配列を受け取り、
    // 配列の要素の平均値を計算して double 型の値を返します
    
    
    // findMax メソッドを定義してください
    // このメソッドは int 型の配列を受け取り、
    // 配列内の最大値を見つけて int 型の値を返します
    
}
```

<details>

<summary>ヒント</summary>

* `calculateAverage` メソッドの実装：
  * `double` 型の戻り値を持ち、`int[]` 型の引数を受け取るメソッドを定義します
  * 配列の要素の合計を計算するために変数を初期化し、ループを使用します
  * 平均値を計算するには、合計を要素数で割ります
  *   整数の除算では小数点以下が切り捨てられるため、キャストを使用して浮動小数点数の結果を得ます：

      ```java
      return (double) sum / array.length;
      ```
* `findMax` メソッドの実装：
  * `int` 型の戻り値を持ち、`int[]` 型の引数を受け取るメソッドを定義します
  * 最大値を保持する変数を配列の最初の要素で初期化します
  * 配列の要素を順に調べ、現在の最大値よりも大きい値が見つかったら更新します
  * ループ終了後、最大値を返します

</details>

**問題 4-5: 再帰メソッド**

以下のプログラムを完成させてください。再帰を使用して、指定された数の階乗を計算する `factorial` メソッドを定義してください。

```java
public class RecursionExample {
    public static void main(String[] args) {
        // factorial メソッドを呼び出し、結果を表示してください
        // 数値: 5
        
        
        // 数値: 0
        
        
        // 数値: 10
        
    }
    
    // factorial メソッドを定義してください
    // このメソッドは int 型の n を受け取り、
    // n の階乗 (n!) を計算して返します
    // 階乗とは: n! = n × (n-1) × (n-2) × ... × 2 × 1
    // 特殊ケース: 0! = 1
    
}
```

<details>

<summary>ヒント</summary>

* `factorial` メソッドを再帰的に実装します：
  * `int` 型の戻り値を持ち、`int` 型の引数 `n` を受け取るメソッドを定義します
  * 基底ケースを定義します：`n` が 0 または 1 の場合は 1 を返します
  * 再帰ケースを定義します：`n` が 2 以上の場合は、`n` と `factorial(n-1)` の積を返します
  *   例：

      ```java
      if (n == 0 || n == 1) {    return 1;} else {    return n * factorial(n - 1);}
      ```
* 大きな数の階乗を計算する場合、`int` 型の範囲を超える可能性があることに注意してください。`long` 型を使用するか、計算する数を制限することを考慮しましょう。

</details>

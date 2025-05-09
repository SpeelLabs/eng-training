---
description: >-
  このセクションでは、Java
  プログラミングで初学者がよく遭遇するエラーとその解決方法に焦点を当てます。以下の各問題には、エラーを含むコードが示されています。エラーを特定し、修正してください。
---

# デバッグ問題

#### 問題 1:&#x20;

以下のコードにはコンパイルエラーがあります。エラーを見つけて修正してください。

```java
public class SyntaxError {
    public static void main(String[] args) {
        System.out.println("Hello, World!")
        int age = 25;
        System.out.println("私は " + age + " 歳です。");
    }
}
```

<details>

<summary>ヒント</summary>

* Java ではステートメントの終わりに何が必要ですか？
* エラーメッセージが表示される行を確認してください

</details>

<details>

<summary>エラー解説</summary>

**エラーメッセージ:**

```
SyntaxError.java:3: error: ';' expected
        System.out.println("Hello, World!")
                                          ^
```

**原因:** Java では、各ステートメント (文) の終わりにセミコロン ( `;` ) が必要です。1行目の `println` の後にセミコロンがありません。

**修正方法:**

```java
System.out.println("Hello, World!");
```

</details>

#### 問題 2:&#x20;

以下のコードを実行するとエラーが発生します。問題を見つけて修正してください。

```java
public class VariableError {
    public static void main(String[] args) {
        System.out.println("年齢計算プログラム");
        
        int birthYear = 1995;
        int currentYear;
        int age = currentYear - birthYear;
        
        System.out.println("現在の年: " + currentYear);
        System.out.println("生まれた年: " + birthYear);
        System.out.println("年齢: " + age);
    }
}
```

<details>

<summary>ヒント</summary>

* 変数を使用する前に、その変数に値が代入されているか確認してください
* 初期化されていない変数を使おうとするとどうなりますか？

</details>

<details>

<summary>エラー解説</summary>

**エラーメッセージ:**

```
VariableError.java:6: error: variable currentYear might not have been initialized
        int age = currentYear - birthYear;
                  ^
```

**原因:** 変数 `currentYear` が宣言されていますが、値が代入 (初期化) されていません。Java では、ローカル変数は使用する前に必ず初期化する必要があります。

**修正方法:**

```java
int currentYear = 2025;  // 現在の年を設定
```

</details>

#### 問題 3:&#x20;

以下のコードを実行するとエラーが発生します。問題を見つけて修正してください。

```java
public class TypeMismatchError {
    public static void main(String[] args) {
        String userInput = "42";
        int number = userInput;
        
        System.out.println("数値: " + number);
    }
}
```

<details>

<summary>ヒント</summary>

* 文字列型 (String) から整数型 (int) への直接の代入は可能ですか？
* 文字列を数値に変換するにはどうすればよいでしょうか？

</details>

<details>

<summary>エラー解説</summary>

**エラーメッセージ:**

```
TypeMismatchError.java:4: error: incompatible types: String cannot be converted to int
        int number = userInput;
                     ^
```

**原因:** Java は静的型付け言語のため、異なるデータ型間の自動変換は限られています。文字列 (String) を整数 (int) に直接代入することはできません。

**修正方法:**

```java
int number = Integer.parseInt(userInput);  // 文字列から整数への変換
```

</details>

#### 問題 4:&#x20;

以下のコードは期待通りに動作しません。問題を見つけて修正してください。

```java
public class StringComparisonError {
    public static void main(String[] args) {
        String input = "hello";
        
        if (input == "hello") {
            System.out.println("一致しました！");
        } else {
            System.out.println("一致しませんでした。");
        }
    }
}
```

<details>

<summary>ヒント</summary>

* Java で文字列の内容を比較するには、`==` 演算子ではなく何を使うべきですか？
* `==` は何を比較していますか？

</details>

<details>

<summary>エラー解説</summary>

**問題:** このコードはコンパイルエラーにはなりませんが、期待した動作をしない可能性があります。Java では `==` 演算子は、文字列の参照 (メモリーアドレス) を比較するため、内容が同じでも別のメモリー領域にある文字列との比較では `false` になることがあります。

**原因:** 文字列の内容ではなく、参照の比較をしている。

**修正方法:**

```java
if (input.equals("hello")) {
    System.out.println("一致しました！");
} else {
    System.out.println("一致しませんでした。");
}
```

</details>

#### 問題 5:&#x20;

以下のコードはランタイムエラーが発生します。問題を見つけて修正してください。

```java
public class ArithmeticExceptionError {
    public static void main(String[] args) {
        int numerator = 10;
        int denominator = 0;
        
        int result = numerator / denominator;
        System.out.println("結果: " + result);
    }
}
```

<details>

<summary>ヒント</summary>

* 数学的に 0 で割ることは可能ですか？
* Java で 0 除算を行うとどうなりますか？

</details>

<details>

<summary>エラー解説</summary>

**エラーメッセージ:**

```
Exception in thread "main" java.lang.ArithmeticException: / by zero
        at ArithmeticExceptionError.main(ArithmeticExceptionError.java:6)
```

**原因:** 0 で割ることは数学的に定義されておらず、Java では `ArithmeticException` 例外が発生します。

**修正方法:**

```java
public class ArithmeticExceptionError {
    public static void main(String[] args) {
        int numerator = 10;
        int denominator = 0;
        
        if (denominator != 0) {
            int result = numerator / denominator;
            System.out.println("結果: " + result);
        } else {
            System.out.println("0で割ることはできません。");
        }
    }
}
```

</details>

#### 問題 6:&#x20;

以下のコードはランタイムエラーが発生します。問題を見つけて修正してください。

```java
public class ArrayIndexError {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        
        for (int i = 0; i <= numbers.length; i++) {
            System.out.println("Element " + i + ": " + numbers[i]);
        }
    }
}
```

<details>

<summary>ヒント</summary>

* 配列のインデックスは何から始まりますか？
* 配列の長さとアクセス可能な最大インデックスはどのような関係がありますか？
* for ループの条件を確認してください

</details>

<details>

<summary>エラー解説</summary>

**エラーメッセージ:**

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 5 out of bounds for length 5
        at ArrayIndexError.main(ArrayIndexError.java:6)
```

**原因:** Java の配列のインデックスは0から始まります。長さが 5 の配列の場合、有効なインデックスは 0 から 4 までです。ループの条件が `i <= numbers.length` となっているため、配列の範囲外 (インデックス 5 ) にアクセスしようとしています。

**修正方法:**

```java
for (int i = 0; i < numbers.length; i++) {
    System.out.println("Element " + i + ": " + numbers[i]);
}
```

</details>

#### 問題 7:&#x20;

以下のコードは期待通りに動作しません。問題を見つけて修正してください。

```java
public class SwitchFallThroughError {
    public static void main(String[] args) {
        int day = 3;
        String dayName;
        
        switch (day) {
            case 1:
                dayName = "月曜日";
            case 2:
                dayName = "火曜日";
            case 3:
                dayName = "水曜日";
            case 4:
                dayName = "木曜日";
            case 5:
                dayName = "金曜日";
            case 6:
                dayName = "土曜日";
            case 7:
                dayName = "日曜日";
            default:
                dayName = "無効な日";
        }
        
        System.out.println(day + "は" + dayName + "です。");
    }
}
```

<details>

<summary>ヒント</summary>

* switch 文の各 case の後に何が必要ですか？
* switch 文でフォールスルーとは何ですか？

</details>

<details>

<summary>エラー解説</summary>

**問題:** 各 `case` の後に `break` 文がないため、マッチした `case` 以降のすべての `case` が実行されます (フォールスルー)。day が 3 の場合、dayName は最終的に "無効な日" になります。

**原因:** 各 `case` の後に `break` 文がない。

**修正方法:**

```java
switch (day) {
    case 1:
        dayName = "月曜日";
        break;
    case 2:
        dayName = "火曜日";
        break;
    case 3:
        dayName = "水曜日";
        break;
    case 4:
        dayName = "木曜日";
        break;
    case 5:
        dayName = "金曜日";
        break;
    case 6:
        dayName = "土曜日";
        break;
    case 7:
        dayName = "日曜日";
        break;
    default:
        dayName = "無効な日";
}
```

</details>

#### 問題 8:&#x20;

以下のコードは期待通りに動作しません。問題を見つけて修正してください。

```java
public class OperatorPrecedenceError {
    public static void main(String[] args) {
        int a = 5;
        int b = 3;
        int c = 2;
        
        int result = a + b * c;
        System.out.println("a + b * c = " + result); // 期待値: 16
        
        boolean check = a > b && b > c || a < c;
        System.out.println("a > b && b > c || a < c = " + check); // 期待値: true
    }
}
```

<details>

<summary>ヒント</summary>

* Java での演算子の優先順位はどうなっていますか？
* 期待する結果を得るには、どのように括弧を使用すればよいですか？

</details>

<details>

<summary>エラー解説</summary>

**問題:**

1. 乗算 ( `*` ) は加算 ( `+` ) よりも優先順位が高いため、`a + b * c` は `a + (b * c)` として評価され、結果は `5 + (3 * 2) = 5 + 6 = 11` になります。期待値 16 を得るには、`(a + b) * c` とする必要があります。
2. 論理 AND ( `&&` ) は論理 OR ( `||` ) よりも優先順位が高いため、`a > b && b > c || a < c` は `(a > b && b > c) || a < c` として評価されます。ここで `a < c` は `5 < 2` で `false` ですが、`(a > b && b > c)` は `(5 > 3 && 3 > 2)` で `true` なので、結果は `true || false = true` となります。このケースでは期待通りですが、明示的に括弧を使用することで意図を明確にすべきです。

**修正方法:**

```java
int result = (a + b) * c;  // 括弧を追加して加算を先に行う
System.out.println("(a + b) * c = " + result); // 結果: 16

boolean check = (a > b && b > c) || a < c;  // 明示的に括弧を使用して意図を明確にする
System.out.println("(a > b && b > c) || a < c = " + check);
```

</details>

#### 問題 9:&#x20;

以下のコードはコンパイルエラーが発生します。問題を見つけて修正してください。

```java
public class ScopeError {
    public static void main(String[] args) {
        int score = 85;
        
        if (score >= 60) {
            String result = "合格";
        } else {
            String result = "不合格";
        }
        
        System.out.println("結果: " + result);
    }
}
```

<details>

<summary>ヒント</summary>

* 変数のスコープ (有効範囲) は何によって決まりますか？
* ブロック内で宣言された変数はブロック外からアクセスできますか？

</details>

<details>

<summary>エラー解説</summary>

**エラーメッセージ:**

```
ScopeError.java:10: error: cannot find symbol
        System.out.println("結果: " + result);
                                     ^
  symbol:   variable result
  location: class ScopeError
```

**原因:** 変数 `result` は if-else ブロック内で宣言されているため、そのブロック内でしかアクセスできません。ブロックを出ると変数はスコープ外になります。

**修正方法:**

```java
public class ScopeError {
    public static void main(String[] args) {
        int score = 85;
        String result;  // ブロックの外で変数を宣言
        
        if (score >= 60) {
            result = "合格";  // 既存の変数に値を代入
        } else {
            result = "不合格";  // 既存の変数に値を代入
        }
        
        System.out.println("結果: " + result);
    }
}
```

</details>

#### 問題 10:&#x20;

以下のコードはエラーが発生します。問題を見つけて修正してください。

```java
public class LogicalOperatorError {
    public static void main(String[] args) {
        boolean isRegistered = false;
        boolean isAdmin = true;
        
        if (isRegistered = true || isAdmin) {
            System.out.println("アクセス許可");
        } else {
            System.out.println("アクセス拒否");
        }
    }
}
```

<details>

<summary>ヒント</summary>

* 論理比較演算子と代入演算子の違いは何ですか？
* 条件式内で変数に値を代入するとどうなりますか？

</details>

<details>

<summary>エラー解説</summary>

**問題:** 条件式の中で `isRegistered = true` と書いています。これは比較ではなく代入です。代入式の値は代入された値 (この場合は `true` ) なので、この条件式は常に `true` と評価されます。

**原因:** 等価比較演算子 `==` と代入演算子 `=` を混同している。

**修正方法:**

```java
if (isRegistered == true || isAdmin) {
    System.out.println("アクセス許可");
} else {
    System.out.println("アクセス拒否");
}
```

または、よりシンプルに：

```java
if (isRegistered || isAdmin) {
    System.out.println("アクセス許可");
} else {
    System.out.println("アクセス拒否");
}
```

</details>

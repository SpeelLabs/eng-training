# 5. 演算子と式

### 5.1 算術演算子

算術演算子は、数値データに対して計算を行うための演算子です。

#### 5.1.1 基本的な算術演算子

| 演算子 | 説明     | 例       | 結果         |
| --- | ------ | ------- | ---------- |
| `+` | 加算     | `5 + 3` | `8`        |
| `-` | 減算     | `5 - 3` | `2`        |
| `*` | 乗算     | `5 * 3` | `15`       |
| `/` | 除算     | `8 / 3` | `2` (整数除算) |
| `%` | 剰余（余り） | `8 % 3` | `2`        |

これらの算術演算子を使用して、数値データに対する基本的な計算を行うことができます。Java では、演算対象のデータ型によって結果の型が決まります。

```java
public class ArithmeticOperators {
    public static void main(String[] args) {
        int a = 10;
        int b = 3;
        
        System.out.println("a + b = " + (a + b));  // 13
        System.out.println("a - b = " + (a - b));  // 7
        System.out.println("a * b = " + (a * b));  // 30
        System.out.println("a / b = " + (a / b));  // 3 (小数部分は切り捨て)
        System.out.println("a % b = " + (a % b));  // 1
    }
}
```

この例では、変数 `a` と `b` を使用して、5つの基本的な算術演算を行い、その結果を出力しています。

> 📝 **ポイント**: 整数同士の割り算（/）は小数部分が切り捨てられます。浮動小数点数で計算したい場合は、少なくとも一方を `double` に変換してください。

#### 5.1.2 整数と浮動小数点数の演算

Java では、演算に使用する数値の型によって結果の型が決まります。特に除算において顕著に違いが現れます。

```java
public class NumericOperations {
    public static void main(String[] args) {
        int numberInt = 7;
        double numberDouble = 2.0;
        
        // 整数同士の割り算
        System.out.println("7 / 2 = " + (7 / 2));               // 3
        
        // 浮動小数点数を含む割り算
        System.out.println("7 / 2.0 = " + (7 / 2.0));           // 3.5
        System.out.println("numberInt / numberDouble = " + 
                          (numberInt / numberDouble));           // 3.5
        
        // 型変換しての割り算
        System.out.println("(double) 7 / 2 = " + ((double) 7 / 2));  // 3.5
    }
}
```

この例では、整数同士の除算と、浮動小数点数が含まれる除算の違いを示しています。

* `7 / 2` は整数同士の演算なので、結果は `3` になります（小数部分は切り捨て）
* `7 / 2.0` は整数と浮動小数点数の演算なので、結果は `3.5` になります
* 同様に `numberInt / numberDouble` も浮動小数点数の演算となり、結果は `3.5` です
* `(double) 7 / 2` のように明示的な型変換（キャスト）をすると、整数の演算でも浮動小数点数の結果が得られます

> 📝 **ポイント**: 正確な小数の計算が必要な場合は、少なくとも一方のオペランドを浮動小数点数 (`double`) に変換するか、明示的に型変換を行うことが重要です。

### 5.2 代入演算子

代入演算子は、変数に値を代入する際に使用します。

#### 5.2.1 基本的な代入演算子

| 演算子  | 説明   | 例        | 等価な表現       |
| ---- | ---- | -------- | ----------- |
| `=`  | 代入   | `a = 5`  | -           |
| `+=` | 加算代入 | `a += 3` | `a = a + 3` |
| `-=` | 減算代入 | `a -= 3` | `a = a - 3` |
| `*=` | 乗算代入 | `a *= 3` | `a = a * 3` |
| `/=` | 除算代入 | `a /= 3` | `a = a / 3` |
| `%=` | 剰余代入 | `a %= 3` | `a = a % 3` |

```java
public class AssignmentOperators {
    public static void main(String[] args) {
        int total = 10;
        System.out.println("初期値: " + total);
        
        total += 5;  // total = total + 5
        System.out.println("total += 5: " + total);  // 15
        
        total -= 3;  // total = total - 3
        System.out.println("total -= 3: " + total);  // 12
        
        total *= 2;  // total = total * 2
        System.out.println("total *= 2: " + total);  // 24
        
        total /= 3;  // total = total / 3
        System.out.println("total /= 3: " + total);  // 8
        
        total %= 3;  // total = total % 3
        System.out.println("total %= 3: " + total);  // 2
    }
}
```

#### 5.2.2 インクリメント・デクリメント演算子

| 演算子  | 説明           | 例     | 等価な表現       |
| ---- | ------------ | ----- | ----------- |
| `++` | インクリメント (後置) | `a++` | `a = a + 1` |
| `++` | インクリメント (前置) | `++a` | `a = a + 1` |
| `--` | デクリメント (後置)  | `a--` | `a = a - 1` |
| `--` | デクリメント (前置)  | `--a` | `a = a - 1` |

```java
public class IncrementDecrement {
    public static void main(String[] args) {
        int count = 5;
        
        // 後置インクリメント（使用後に増加）
        System.out.println("count++: " + count++);  // 5
        System.out.println("countの値: " + count);  // 6
        
        // 前置インクリメント（使用前に増加）
        System.out.println("++count: " + ++count);  // 7
        System.out.println("countの値: " + count);  // 7
        
        // 後置デクリメント
        System.out.println("count--: " + count--);  // 7
        System.out.println("countの値: " + count);  // 6
    }
}
```

> 📝 **ポイント**: 前置演算子（`++a`）は値を増加してから使用し、後置演算子（`a++`）は使用してから値を増加します。

### 5.3 比較演算子

比較演算子は、2つの値を比較して真偽値（`boolean`）を返します。

#### 5.3.1 比較演算子の種類

| 演算子  | 説明    | 例        | 結果     |
| ---- | ----- | -------- | ------ |
| `==` | 等しい   | `5 == 5` | `true` |
| `!=` | 等しくない | `5 != 3` | `true` |
| `>`  | より大きい | `5 > 3`  | `true` |
| `<`  | より小さい | `5 < 8`  | `true` |
| `>=` | 以上    | `5 >= 5` | `true` |
| `<=` | 以下    | `5 <= 5` | `true` |

```java
public class ComparisonOperators {
    public static void main(String[] args) {
        int x = 5;
        int y = 8;
        
        System.out.println("x == y: " + (x == y));  // false
        System.out.println("x != y: " + (x != y));  // true
        System.out.println("x > y: " + (x > y));    // false
        System.out.println("x < y: " + (x < y));    // true
        System.out.println("x >= 5: " + (x >= 5));  // true
        System.out.println("x <= 5: " + (x <= 5));  // true
    }
}
```

#### 5.3.2 文字列の比較

文字列の比較には特別な注意が必要です。

```java
public class StringComparison {
    public static void main(String[] args) {
        String str1 = "Hello";
        String str2 = "Hello";
        String str3 = new String("Hello");
        
        // 参照の比較（==）
        System.out.println("str1 == str2: " + (str1 == str2));      // true
        System.out.println("str1 == str3: " + (str1 == str3));      // false
        
        // 内容の比較（equals）
        System.out.println("str1.equals(str2): " + str1.equals(str2));  // true
        System.out.println("str1.equals(str3): " + str1.equals(str3));  // true
    }
}
```

> ⚠️ **注意**: 文字列の内容を比較する場合は `equals()` メソッドを使用してください。`==` は参照の同一性を比較します。

### 5.4 論理演算子

論理演算子は、真偽値を組み合わせて複合的な条件を表現します。

#### 5.4.1 論理演算子の種類

| 演算子    | 説明         | 例                 | 結果      |
| ------ | ---------- | ----------------- | ------- |
| `&&`   | 論理積 (AND)  | `true && false`   | `false` |
| `\|\|` | 論理和 (OR)   | `true \|\| false` | `true`  |
| `!`    | 論理否定 (NOT) | `!true`           | `false` |

```java
public class LogicalOperators {
    public static void main(String[] args) {
        boolean isStudent = true;
        boolean hasID = false;
        int age = 20;
        
        // AND演算（両方がtrueの場合にtrue）
        boolean canEnter = age >= 18 && hasID;
        System.out.println("入場可能: " + canEnter);  // false
        
        // OR演算（どちらかがtrueの場合にtrue）
        boolean getDiscount = isStudent || age >= 65;
        System.out.println("割引適用: " + getDiscount);  // true
        
        // NOT演算（真偽値を反転）
        boolean isNotStudent = !isStudent;
        System.out.println("学生ではない: " + isNotStudent);  // false
    }
}
```

#### 5.4.2 短絡評価

Java の論理演算子は**短絡評価**を行います。

```java
public class ShortCircuit {
    public static void main(String[] args) {
        int x = 0;
        
        // && は最初がfalseなら右辺を評価しない
        boolean result1 = (x != 0) && (10 / x > 0);
        System.out.println("result1: " + result1);  // false（エラーなし）
        
        // || は最初がtrueなら右辺を評価しない
        boolean result2 = (x == 0) || (10 / x > 0);
        System.out.println("result2: " + result2);  // true（エラーなし）
    }
}
```

> 📝 **ポイント**: 短絡評価により、不要な処理や潜在的なエラーを回避できます。

### 5.5 演算の優先順位

演算子には優先順位があり、式の評価順序を決定します。

#### 5.5.1 主要な演算子の優先順位（高い順）

1. 後置演算子: `++`, `--`
2. 単項演算子: `+`, `-`, `!`, 前置 `++`, 前置 `--`
3. 算術演算子: `*`, `/`, `%`
4. 算術演算子: `+`, `-`
5. 比較演算子: `<`, `>`, `<=`, `>=`
6. 等価演算子: `==`, `!=`
7. 論理演算子: `&&`
8. 論理演算子: `||`
9. 代入演算子: `=`, `+=`, `-=` など

```java
public class OperatorPrecedence {
    public static void main(String[] args) {
        int a = 5;
        int b = 3;
        int c = 2;
        
        // 演算の優先順位
        int result1 = a + b * c;
        System.out.println("a + b * c = " + result1);  // 11 (5 + 6)
        
        // 括弧で優先順位を変更
        int result2 = (a + b) * c;
        System.out.println("(a + b) * c = " + result2);  // 16 (8 * 2)
        
        // 複合的な式
        boolean result3 = a > b && b > c || false;
        System.out.println("a > b && b > c || false = " + result3);  // true
        
        // 括弧で明確に
        boolean result4 = (a > b) && (b > c);
        System.out.println("(a > b) && (b > c) = " + result4);  // true
    }
}
```

> 📝 **ポイント**: 優先順位が不明確な場合は、括弧を使用して明示的に順序を指定することが推奨されます。



***

### 演習問題

#### 問題 5-1: 算術演算子の基本

以下の変数 `a` と `b` を使って、各種の算術演算を行い、結果を出力してください。

```java
int a = 15;
int b = 4;

// 1. aとbの和を計算して出力してください


// 2. aとbの差を計算して出力してください


// 3. aとbの積を計算して出力してください


// 4. aをbで割った商を計算して出力してください


// 5. aをbで割った余りを計算して出力してください

```

<details>

<summary>ヒント</summary>

* 加算には + 演算子を使用します
* 減算には - 演算子を使用します
* 乗算には \* 演算子を使用します
* 除算には / 演算子を使用します（整数同士の場合、結果も整数になります）
* 剰余（余り）には % 演算子を使用します

</details>

#### 問題 5-2: 変数の増減と代入

以下の変数 `counter` を使って、値を増減させる操作を行ってください。

```java
int counter = 10;

// 1. counterの値を5増やしてください（複合代入演算子を使用）


// 2. counterの値を表示してください


// 3. counterの値をインクリメント演算子を使って1増やしてください


// 4. counterの値を表示してください


// 5. counterの値を3減らしてください（複合代入演算子を使用）


// 6. counterの値を表示してください


// 7. counterの値をデクリメント演算子を使って1減らしてください


// 8. counterの値を表示してください

```

<details>

<summary>ヒント</summary>

* 複合代入演算子 += を使うと値を増やせます
* 複合代入演算子 -= を使うと値を減らせます
* インクリメント演算子 ++ は変数の値を1増やします
* デクリメント演算子 -- は変数の値を1減らします

</details>

#### 問題 5-3: 変数の演算と代入

以下の変数 `value` を使って、複数の演算を連続して行ってください。

```java
int value = 10;

// 1. valueに5を加えた後、その値を2倍にしてください（複合代入演算子を使用）


// 2. valueの値を表示してください


// 3. valueを2で割った後、1を引いてください（複合代入演算子を使用）


// 4. valueの値を表示してください

```

<details>

<summary>ヒント</summary>

* 複合代入演算子を順番に使用します
* まず += で加算し、その後 \*= で乗算します
* 次に /= で除算し、その後 -= で減算します

</details>

#### 問題 5-4: 文字列の連結

以下の変数 `firstName` と `lastName` を使って、様々な形式の文字列を作成して表示してください。

```java
String firstName = "太郎";
String lastName = "山田";

// 1. firstNameとlastNameを連結して「山田太郎」という形式の文字列を作成し、表示してください


// 2. firstNameとlastNameの間にスペースを入れて「山田 太郎」という形式の文字列を作成し、表示してください


// 3. firstNameとlastNameを使って「私の名前は山田太郎です」という文字列を作成し、表示してください

```

<details>

<summary>ヒント</summary>

* 文字列の連結には + 演算子を使用します
* 文字列リテラル（"スペース"や"私の名前は"など）と変数を + で連結できます
* 連結の順序に注意してください

</details>

#### 問題 5-5: 数値計算の応用

以下の変数 `radius` を使って、円の面積と円周を計算し表示してください。

```java
double radius = 5.0;  // 円の半径

// 1. 円の面積を計算してください（面積 = π × 半径 × 半径）


// 2. 計算した面積を表示してください（円の面積: XXX）


// 3. 円の円周を計算してください（円周 = 2 × π × 半径）


// 4. 計算した円周を表示してください（円の円周: XXX）

```

<details>

<summary>ヒント</summary>

* 円周率πは Math.PI で取得できます
* 乗算には \* 演算子を使用します
* System.out.println() で結果を表示します
* 文字列と計算結果を + で連結できます

</details>

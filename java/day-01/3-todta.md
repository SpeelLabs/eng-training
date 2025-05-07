# 3. 変数とデータ型

### 3.1 変数とは何か

**変数**は、プログラムがコンピューターのメモリに値を格納するための「名前付きの場所」です。プログラム実行時に、変数名に対応するメモリ上の領域に値が保存されます。

変数はプログラムの基本的な構成要素であり、データを一時的に保存し、後で使用するために必要です。例えば、ユーザーの入力を保存したり、計算結果を格納したり、プログラムの状態を追跡したりするのに使用します。

#### 3.1.1 Python との違い

Java と Python では変数の扱い方が大きく異なります。

| 項目   | Java               | Python        |
| ---- | ------------------ | ------------- |
| 型宣言  | 明示的に必要             | 不要            |
| 型の変更 | 基本的に不可             | 可能            |
| 変数宣言 | `int number = 10;` | `number = 10` |

```java
// Java の場合
int age = 25;          // 型を明示的に指定
String name = "田中";  // 文字列も型指定が必要

// Python の場合
age = 25              # 型は自動的に推論
name = "田中"         # 文字列も柔軟に扱える
```

Java は「静的型付け言語」であり、変数の型はコンパイル時に確定され、その後変更できません。一方、Python は「動的型付け言語」で、実行時に型が決まり、同じ変数に異なる型の値を代入することも可能です。

> 📝 **ポイント**: Java は**静的型付け**言語で、変数の型をコンパイル時に確定します。Python は**動的型付け**言語で、実行時に型が決まります。

#### 3.1.2 変数の宣言

Java では変数を使用する前に**宣言**する必要があります。変数を宣言することで、メモリ上に特定の型の値を格納できる領域が確保されます。

```java
データ型 変数名;          // メモリ領域の確保（宣言のみ）
データ型 変数名 = 値;     // メモリ領域の確保と値の設定
```

実例：

```java
int score;              // メモリ上にint型用の4バイトの領域を確保
score = 85;             // 確保した領域に値85を格納

double price = 19.99;   // メモリ確保と同時に値を格納
```

> 📝 **ポイント**: 変数の型によってメモリ上で確保される領域のサイズが異なります。例えば、`int` は4バイト、`double` は8バイトのメモリを使用します。

### 3.2 基本データ型

Java には8種類の基本データ型 (プリミティブ型) があります。

#### 3.2.1 整数型

| データ型    | サイズ  | 範囲                                                     | 使用例          |
| ------- | ---- | ------------------------------------------------------ | ------------ |
| `byte`  | 1バイト | -128 ～ 127                                             | フラグやステータス    |
| `short` | 2バイト | -32,768 ～ 32,767                                       | 小さな整数値       |
| `int`   | 4バイト | -2,147,483,648 ～ 2,147,483,647                         | 一般的な整数（最頻使用） |
| `long`  | 8バイト | -9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807 | 大きな整数値       |

```java
int studentCount = 30;      // 最もよく使用される整数型
long population = 8000000L; // long型は末尾にLを付ける
byte flags = 127;           // 小さな値の場合はbyte型も使用可能
```

#### 3.2.2 浮動小数点型

| データ型     | サイズ  | 精度  | 使用例       |
| -------- | ---- | --- | --------- |
| `float`  | 4バイト | 単精度 | 一般的な小数値   |
| `double` | 8バイト | 倍精度 | 高精度が必要な計算 |

```java
double pi = 3.14159;        // 通常はdoubleを使用
float height = 175.5f;      // float型は末尾にfを付ける
```

> 📝 **ポイント**: 浮動小数点数を扱う場合は、通常 `double` 型を使用します。`float` は精度が低いため注意が必要です。

#### 3.2.3 文字型

```java
char grade = 'A';           // シングルクォートで囲む
char initial = '太';        // 日本語文字も可能
char unicodeChar = '\u0041'; // Unicode表記もOK
```

> 📝 **ポイント**: `char` は1文字のみを格納します。文字列は `String` クラスを使用します。

#### 3.2.4 真偽値型

```java
boolean isStudent = true;    // true または false のみ
boolean hasLicense = false;

// 条件式の結果も真偽値
boolean isAdult = age >= 18;
```

### 3.3 変数の命名規則

Java では厳密な命名規則があります。

#### 3.3.1 必須ルール

1. **使用可能な文字**: 英字、数字、$、\_（アンダースコア）
2. **開始文字**: 英字、$、\_ のいずれか（数字は不可）
3. **大文字小文字**: 区別される（`name` と `Name` は別の変数）
4. **予約語**: 使用不可（`int`、`class`、`public` など）

```java
// 正しい変数名
int studentAge;
double totalPrice;
String user_name;

// 不正な変数名
int 1stPlace;  // 数字で始まっている
int class;     // 予約語
```

#### 3.3.2 キャメルケース規則

Java では**キャメルケース**が標準的です。

```java
// 変数名
int myAge;
String firstName;
boolean isVisible;

// メソッド名
calculateTotal();
getUserName();

// クラス名（最初の文字も大文字）
public class StudentInformation;
```

> 📝 **ポイント**: 命名は統一性が重要です。チーム開発では特にこの規則を守ることが大切です。

### 3.4 型変換（キャスト）

#### 3.4.1 自動型変換（暗黙的変換）

小さい範囲の型から大きい範囲の型への変換は自動的に行われます。

```java
int number = 10;
double decimal = number;  // int → double は自動的に変換
System.out.println(decimal);  // 10.0

byte small = 10;
int medium = small;  // byte → int は自動的に変換
```

#### 3.4.2 明示的な型変換（キャスト）

大きい範囲の型から小さい範囲の型への変換は、明示的に行う必要があります：

```java
double pi = 3.14159;
int integerPart = (int) pi;  // 3 (小数部分は切り捨て)

int large = 1000;
byte small = (byte) large;  // 正しく変換できない可能性あり
```

> ⚠️ **注意**: 範囲外の値をキャストすると、予期せぬ結果になる可能性があります。

#### 3.4.3 文字列との型変換

```java
// 数値 → 文字列
int age = 25;
String ageString = String.valueOf(age);  // "25"

// 文字列 → 数値
String numberString = "42";
int number = Integer.parseInt(numberString);  // 42

// 文字列 → 浮動小数点
String priceString = "19.99";
double price = Double.parseDouble(priceString);  // 19.99
```



***

### 演習問題

#### 問題 3-1: 変数の宣言と初期化

以下の指示に従って、変数を宣言し値を代入してください。

{% code overflow="wrap" %}
```java
// 1. 整数型の変数 age を宣言し、値 25 を代入してください


// 2. 浮動小数点型の変数 height を宣言し、値 175.5 を代入してください


// 3. 文字型の変数 grade を宣言し、値 'A' を代入してください


// 4. 真偽値型の変数 isStudent を宣言し、値 true を代入してください


// 5. 文字列型の変数 name を宣言し、値 "山田太郎" を代入してください

```
{% endcode %}

<details>

<summary>ヒント</summary>

* 整数型は `int` を使用します
* 浮動小数点型は `double` を使用します
* 文字型は `char` を使用し、文字はシングルクォーテーション `'` で囲みます
* 真偽値型は `boolean` を使用し、`true` または `false` を代入します
* 文字列型は `String` を使用し、文字列はダブルクォーテーション `"` で囲みます

</details>

#### 問題 3-2: 命名規則の確認

以下の変数名のうち、Java の命名規則に反しているものがあります。適切に修正してください。

{% code overflow="wrap" %}
```java
// 以下の変数宣言のうち、命名規則に違反しているものを修正してください

int 1number = 10;
int student_count = 25;
int MAX_SIZE = 100;
int class = 3;
int userName = "Alice";
int itemPrice = 1500;
```
{% endcode %}

<details>

<summary>ヒント</summary>

* 変数名は数字で始めることはできません
* 予約語（`class` など）は変数名として使用できません
* 変数の型と代入する値の型は一致している必要があります

</details>

#### 問題 3-3: 変数の値の更新

以下の変数 `count` を使って、値を更新する操作を行ってください。

{% code overflow="wrap" %}
```java
int count = 0;
System.out.println("初期値: " + count);

// 1. count の値を 10 に更新してください


// 2. 更新後の値を表示してください


// 3. count の値を現在の値から 5 増やしてください


// 4. 更新後の値を表示してください


// 5. count の値を 2 倍にしてください


// 6. 更新後の値を表示してください

```
{% endcode %}

<details>

<summary>ヒント</summary>

* 変数に値を代入するには `=` 演算子を使用します
* 変数の値を増やすには `+=` 演算子または `変数 = 変数 + 値` を使用できます
* 変数の値を2倍にするには `*= 2` または `変数 = 変数 * 2` を使用できます
* System.out.println() を使って変数の値を表示できます

</details>

#### 問題 3-4: 異なるデータ型の変数

以下の変数を使って、異なるデータ型の値を表示してください。

{% code overflow="wrap" %}
```java
int number = 10;
double decimal = 5.5;
char letter = 'A';
boolean isValid = true;
String message = "こんにちは";

// 1. 各変数の値を表示してください


// 2. 各変数のデータ型を表示してください（ヒント: .getClass().getName() または.getClass().getSimpleName() を使用）

```
{% endcode %}

<details>

<summary>ヒント</summary>

* System.out.println() を使って各変数の値を表示できます
* プリミティブ型（int, double, char, boolean）の場合、直接 .getClass() メソッドは使用できません
* プリミティブ型では例えば `System.out.println("number の型: int");` と表示するか、または `Integer.valueOf(number).getClass().getSimpleName()` のように対応するラッパークラスに変換してから型を取得できます
* String 型の場合は `message.getClass().getSimpleName()` で型名を取得できます

</details>

#### 問題 3-5: 型変換（キャスト）

以下の変数を使って、型変換を行ってください。

{% code overflow="wrap" %}
```java
double price = 12.5;
int quantity = 3;
String numberText = "42";
String decimalText = "3.14";

// 1. price を整数に変換し、表示してください


// 2. quantity を浮動小数点数に変換し、表示してください


// 3. numberText を整数に変換し、表示してください


// 4. decimalText を浮動小数点数に変換し、表示してください


// 5. price と quantity の積を計算し、文字列に変換して表示してください

```
{% endcode %}

<details>

<summary>ヒント</summary>

* double から int への変換は `(int)` キャストを使用します
* int から double への変換は `(double)` キャストを使用します（自動変換も可能）
* String から int への変換は `Integer.parseInt()` メソッドを使用します
* String から double への変換は `Double.parseDouble()` メソッドを使用します
* 数値から String への変換は `String.valueOf()` メソッドまたは単に値に `+ ""` を連結します

</details>

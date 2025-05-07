# 3. 変数とデータ型

### 3.1 変数とは何か

**変数**は、プログラムがコンピューターのメモリーに値を格納するための 「名前付きの場所」 です。プログラム実行時に、変数名に対応するメモリー上の領域に値が保存されます。

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

> 📝 **ポイント**: Java は**静的型付け**言語で、変数の型をコンパイル時に確定します。Python は**動的型付け**言語で、実行時に型が決まります。

#### 3.1.2 変数の宣言

Java では変数を使用する前に**宣言**する必要があります。変数を宣言することで、メモリー上に特定の型の値を格納できる領域が確保されます。

```java
データ型 変数名;          // メモリー領域の確保 (宣言のみ)
データ型 変数名 = 値;     // メモリー領域の確保と値の設定
```

実例：

```java
int score;              // メモリー上に int 型用の 4バイトの領域を確保
score = 85;             // 確保した領域に値 85 を格納

double price = 19.99;   // メモリー領域の確保と同時に値を格納
```

> 📝 **ポイント**: 変数の型によってメモリー上で確保される領域のサイズが異なります。例えば、`int` は 4バイト、`double` は 8バイトのメモリーを使用します。

### 3.2 基本データ型

Java には 8種類の基本データ型 (プリミティブ型) があります。

#### 3.2.1 整数型

<table><thead><tr><th width="98.199951171875">データ型</th><th width="101.60003662109375">サイズ</th><th width="300.39996337890625">範囲</th><th>使用例</th></tr></thead><tbody><tr><td><code>byte</code></td><td>1バイト</td><td>-128 ～ 127</td><td>フラグやステータス</td></tr><tr><td><code>short</code></td><td>2バイト</td><td>-32,768 ～ 32,767</td><td>小さな整数値</td></tr><tr><td><code>int</code></td><td>4バイト</td><td>-2,147,483,648 ～ 2,147,483,647</td><td>一般的な整数 (最頻使用)</td></tr><tr><td><code>long</code></td><td>8バイト</td><td>-9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807</td><td>大きな整数値</td></tr></tbody></table>

```java
int studentCount = 30;      // 最もよく使用される整数型
long population = 8000000L; // long 型は末尾に L を付ける
byte flags = 127;           // 小さな値の場合は byte 型も使用可能
```

#### 3.2.2 浮動小数点型

<table><thead><tr><th width="99">データ型</th><th width="101.79998779296875">サイズ</th><th width="300">精度</th><th>使用例</th></tr></thead><tbody><tr><td><code>float</code></td><td>4バイト</td><td>単精度</td><td>一般的な小数値</td></tr><tr><td><code>double</code></td><td>8バイト</td><td>倍精度</td><td>高精度が必要な計算</td></tr></tbody></table>

```java
double pi = 3.14159;        // 通常は double を使用
float height = 175.5f;      // float 型は末尾に f を付ける
```

> 📝 **ポイント**: 浮動小数点数を扱う場合は、通常 `double` 型を使用します。`float` は精度が低いため注意が必要です。

#### 3.2.3 文字型

```java
char grade = 'A';           // シングルクォートで囲む
char initial = '太';        // 日本語文字も可能
char unicodeChar = '\u0041'; // Unicode 表記も OK
```

> 📝 **ポイント**: `char` は 1文字のみを格納します。文字列は `String` クラスを使用します。

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

1. **使用可能な文字**: 英字、数字、$、\_ (アンダースコア)
2. **開始文字**: 英字、$、\_ のいずれか (数字は不可)
3. **大文字小文字**: 区別される ( `name` と `Name` は別の変数)
4. **予約語**: 使用不可 ( `int`、`class`、`public` など)

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

Java では**キャメルケース**が標準的です：

```java
// 変数名
int myAge;
String firstName;
boolean isVisible;

// メソッド名
calculateTotal();
getUserName();

// クラス名 (最初の文字も大文字)
public class StudentInformation;
```

> 📝 **ポイント**: 命名は統一性が重要です。チーム開発では特にこの規則を守ることが大切です。

### 3.4 型変換 (キャスト)

#### 3.4.1 自動型変換 (暗黙的変換)

小さい範囲の型から大きい範囲の型への変換は自動的に行われます。

```java
int number = 10;
double decimal = number;  // int → double は自動的に変換
System.out.println(decimal);  // 10.0

byte small = 10;
int medium = small;  // byte → int は自動的に変換
```

#### 3.4.2 明示的な型変換 (キャスト)

大きい範囲の型から小さい範囲の型への変換は、明示的に行う必要があります。

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

以下の仕様に従って変数を宣言し、値を代入してください。

1. 整数型の変数 `studentId` を宣言し、値 `12345` を代入
2. 浮動小数点型の変数 `height` を宣言し、値 `165.5` を代入
3. 文字型の変数 `bloodType` を宣言し、値 `'A'` を代入
4. 真偽値型の変数 `isValid` を宣言し、値 `true` を代入

<details>

<summary>解答例</summary>

```java
public class VariableDeclaration {
    public static void main(String[] args) {
        int studentId = 12345;
        double height = 165.5;
        char bloodType = 'A';
        boolean isValid = true;
        
        System.out.println("学生ID: " + studentId);
        System.out.println("身長: " + height + "cm");
        System.out.println("血液型: " + bloodType);
        System.out.println("有効フラグ: " + isValid);
    }
}
```

</details>

#### 問題 3-2: 命名規則の確認

以下の変数名のうち、Java の命名規則に適合するものを選んでください。

1. `total_score`
2. `2ndPlace`
3. `myAge`
4. `first name`
5. `isFinished`
6. `PRODUCT_CODE`

<details>

<summary>解答</summary>

適合するもの：

* `total_score` - アンダースコアは使用可能
* `myAge` - キャメルケース（推奨）
* `isFinished` - キャメルケース（推奨）
* `PRODUCT_CODE` - すべて大文字（定数用）

不適合なもの：

* `2ndPlace` - 数字で始まっている
* `first name` - スペースを含んでいる

</details>

#### 問題 3-3: データ型の選択

以下のデータを格納するのに適切なデータ型を選択してください。

1. 人の年齢 (0～120程度)
2. 円周率の近似値 (3.14159...)
3. 性別 ('M' または 'F')
4. 日本の総人口 (約1億2500万人)
5. テストの合格・不合格フラグ

<details>

<summary>解答</summary>

1. `int` - 一般的な整数値
2. `double` - 高精度の浮動小数点
3. `char` - 単一の文字
4. `int` - 範囲内に収まる
5. `boolean` - 真偽値

```java
public class DataTypeSelection {
    public static void main(String[] args) {
        int age = 25;
        double pi = 3.14159;
        char gender = 'M';
        int population = 125000000;
        boolean isPassed = true;
    }
}
```

</details>

#### 問題 3-4: 型変換の実践

```java
public class TypeConversion {
    public static void main(String[] args) {
        // 以下の変数を使って、型変換を行ってください
        double decimal = 9.7;
        String numberString = "123";
        int integer = 456;
        
        // 1. decimal を整数に変換し、結果を表示
        
        // 2. numberString を整数に変換し、結果を表示
        
        // 3. integer を文字列に変換し、結果を表示
    }
}
```

<details>

<summary>解答例</summary>

```java
public class TypeConversion {
    public static void main(String[] args) {
        double decimal = 9.7;
        String numberString = "123";
        int integer = 456;
        
        // 1. decimal を整数に変換し、結果を表示
        int convertedInt = (int) decimal;
        System.out.println("decimal to int: " + convertedInt);  // 9
        
        // 2. numberString を整数に変換し、結果を表示
        int parsedInt = Integer.parseInt(numberString);
        System.out.println("String to int: " + parsedInt);  // 123
        
        // 3. integer を文字列に変換し、結果を表示
        String convertedString = String.valueOf(integer);
        System.out.println("int to String: " + convertedString);  // 456
    }
}
```

</details>

#### 問題 3-5: 変数の活用

以下の情報を保存するためのプログラムを作成してください。

* 名前：「鈴木一郎」
* 年齢：20歳
* 身長：175.8cm
* テストの点数：85
* 合格フラグ：true (60点以上で合格とする)

それぞれ適切なデータ型の変数を宣言し、値を代入して、以下の形式で出力してください。

```
名前: 鈴木一郎
年齢: 20歳
身長: 175.8cm
テスト結果: 85点 (合格)
```

<details>

<summary>解答例</summary>

```java
public class StudentInfo {
    public static void main(String[] args) {
        String name = "鈴木一郎";
        int age = 20;
        double height = 175.8;
        int testScore = 85;
        boolean isPassed = testScore >= 60;
        
        System.out.println("名前: " + name);
        System.out.println("年齢: " + age + "歳");
        System.out.println("身長: " + height + "cm");
        System.out.println("テスト結果: " + testScore + "点 " + 
                          (isPassed ? "(合格)" : "(不合格)"));
    }
}
```

</details>

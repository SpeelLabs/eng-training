---
description: >-
  この実践問題集では、これまでに学んだJavaの基礎構文に加えて、メソッドの概念を活用した実用的なプログラムの作成にチャレンジします。条件分岐、繰り返し処理、メソッドの定義と呼び出しなどを組み合わせて、より実践的な問題解決能力を養いましょう。
---

# 実践問題②

1\. メソッドの基本と応用

#### 問題 1-1: シンプルな計算機プログラム

四則演算（加算・減算・乗算・除算）を行うメソッドを作成し、簡単な計算機プログラムを実装してください。各演算ごとに別々のメソッドを定義してください。

```java
import java.util.Scanner;

public class SimpleCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("1つ目の数値を入力してください: ");
        double num1 = scanner.nextDouble();
        
        System.out.print("2つ目の数値を入力してください: ");
        double num2 = scanner.nextDouble();
        
        System.out.print("演算子を入力してください(+, -, *, /): ");
        scanner.nextLine(); // 改行文字を消費
        String operator = scanner.nextLine();
        
        // 入力された演算子に応じて適切なメソッドを呼び出し、結果を表示
        
        
        scanner.close();
    }
    
    // 加算を行うメソッドを定義
    
    
    // 減算を行うメソッドを定義
    
    
    // 乗算を行うメソッドを定義
    
    
    // 除算を行うメソッドを定義（ゼロ除算のチェックも含める）
    
}
```

<details>

<summary>ヒント</summary>

* 各演算（加算、減算、乗算、除算）に対応するメソッドを作成します
* 各メソッドは2つの数値を引数として受け取り、計算結果を戻り値として返します
* 除算メソッドでは、ゼロによる除算がないかチェックする必要があります
* メインメソッドでは、ユーザーが入力した演算子に基づいて適切なメソッドを呼び出します
* switch文または if-else文を使って、演算子の種類を判断できます

</details>

#### 問題 1-2: 円の計算

円の半径を入力として受け取り、円周と面積を計算するメソッドを作成してください。メインメソッドでユーザーから半径を受け取り、定義したメソッドを使って計算結果を表示します。

```java
import java.util.Scanner;

public class CircleCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("円の半径を入力してください: ");
        double radius = scanner.nextDouble();
        
        // 円周と面積を計算するメソッドを呼び出し、結果を表示
        
        
        scanner.close();
    }
    
    // 円周を計算するメソッドを定義
    
    
    // 面積を計算するメソッドを定義
    
}
```

<details>

<summary>ヒント</summary>

* 円周の計算式: 2 × π × 半径
* 面積の計算式: π × 半径の2乗
* Math.PIを使用して円周率πの値を取得できます
* メソッドは double型の半径を引数として受け取り、計算結果を戻り値として返します
* 結果を表示する際には、小数点以下の桁数を制限すると見やすくなります（String.format()やSystem.out.printf()を使用）

</details>

### 2. 文字列操作と判定

#### 問題 2-1: パスワード強度チェッカー

パスワードの強度を判定するプログラムを作成してください。パスワードの長さ、大文字・小文字・数字・特殊文字の含有をチェックするメソッドを実装します。

```java
import java.util.Scanner;

public class PasswordChecker {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("パスワードを入力してください: ");
        String password = scanner.nextLine();
        
        // パスワードの強度を評価する各メソッドを呼び出し
        boolean isLengthValid = checkLength(password);
        boolean hasUpperCase = checkUpperCase(password);
        boolean hasLowerCase = checkLowerCase(password);
        boolean hasDigit = checkDigit(password);
        boolean hasSpecialChar = checkSpecialChar(password);
        
        // 総合的な強度を判定して表示
        
        
        scanner.close();
    }
    
    // パスワードの長さをチェックするメソッド (8文字以上が望ましい)
    
    
    // 大文字を含むかチェックするメソッド
    
    
    // 小文字を含むかチェックするメソッド
    
    
    // 数字を含むかチェックするメソッド
    
    
    // 特殊文字を含むかチェックするメソッド (例: !@#$%^&*()など)
    
    
    // パスワードの強度を評価するメソッド
    // 上記のチェック結果に基づいて「弱い」「中程度」「強い」の3段階で評価
    
}
```

<details>

<summary>ヒント</summary>

* Stringクラスのメソッド（length()、charAt()など）を使用して文字列を調査します
* 文字の種類の判定には、Character.isUpperCase()、Character.isLowerCase()、Character.isDigit()などのメソッドが使えます
* 特殊文字のチェックには、あらかじめ定義した特殊文字のリストと照合する方法や、正規表現を使用する方法があります
* 各チェックメソッドは文字列を引数に取り、boolean型（trueまたはfalse）を返すように設計します
* パスワードの強度評価は、満たしている条件の数に基づいて判定できます（例：3つ以上なら「強い」など）

</details>

#### 問題 2-2: 文字列の統計情報

入力された文字列の統計情報（文字数、単語数、母音の数など）を計算するプログラムを作成してください。各統計情報を計算するメソッドを実装します。

```java
import java.util.Scanner;

public class StringAnalyzer {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.println("文字列を入力してください:");
        String text = scanner.nextLine();
        
        // 各統計情報を計算するメソッドを呼び出し、結果を表示
        
        
        scanner.close();
    }
    
    // 文字数を数えるメソッド（スペースも含む）
    
    
    // 単語数を数えるメソッド（スペースで区切られた単語）
    
    
    // 母音の数を数えるメソッド（a, e, i, o, u）
    
    
    // 子音の数を数えるメソッド（母音と数字、特殊文字以外）
    
}
```

<details>

<summary>ヒント</summary>

* 文字数はString.length()で簡単に取得できます
* 単語数を数えるには、String.split()メソッドを使ってスペースで文字列を分割し、配列の長さを調べる方法があります
* 母音のカウントには、各文字を調べてa,e,i,o,uのいずれかかをチェックします（大文字小文字両方考慮）
* 子音のカウントには、Character.isLetter()を使って文字かどうかを判定した上で、母音でない文字をカウントします
* メソッドは文字列を引数に取り、カウント結果をint型で返すように設計します

</details>

### 3. 配列操作とユーティリティメソッド

#### 問題 3-1: 配列の操作

整数配列に対して、合計、平均、最大値、最小値を計算するメソッドを実装してください。また、配列を昇順に並べ替えるメソッドも作成してください。

```java
import java.util.Scanner;

public class ArrayOperations {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("配列のサイズを入力してください: ");
        int size = scanner.nextInt();
        
        int[] numbers = new int[size];
        
        System.out.println(size + "個の整数を入力してください:");
        for (int i = 0; i < size; i++) {
            System.out.print("数値 " + (i + 1) + ": ");
            numbers[i] = scanner.nextInt();
        }
        
        // 各メソッドを呼び出して結果を表示
        
        
        scanner.close();
    }
    
    // 配列の合計を計算するメソッド
    
    
    // 配列の平均を計算するメソッド
    
    
    // 配列の最大値を見つけるメソッド
    
    
    // 配列の最小値を見つけるメソッド
    
    
    // 配列を昇順にソートするメソッド（バブルソートなどのアルゴリズムを実装）
    
    
    // 配列の内容を表示するメソッド
    
}
```

<details>

<summary>ヒント</summary>

* 配列の合計を計算するには、forループで各要素を順に加算します
* 平均値は合計を要素数で割って計算します
* 最大値と最小値は、最初の要素で初期化した変数と各要素を比較し、必要に応じて更新します
* バブルソートは、隣接する要素を比較して必要に応じて交換する操作を繰り返すアルゴリズムです
* 配列の内容を表示するメソッドでは、各要素をカンマで区切って表示すると見やすくなります
* 各メソッドの戻り値の型は、計算結果の性質に応じて適切に設定します（合計・最大値・最小値はint型、平均値はdouble型など）

</details>

#### 問題 3-2: 成績管理システム

学生の成績を管理するプログラムを作成してください。点数の入力、平均点の計算、成績（A, B, C, D, F）への変換などの機能をメソッドとして実装します。

```java
import java.util.Scanner;

public class GradeManager {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("学生の人数を入力してください: ");
        int studentCount = scanner.nextInt();
        
        double[] scores = new double[studentCount];
        
        // 各学生の点数を入力
        inputScores(scores, scanner);
        
        // 平均点を計算
        double average = calculateAverage(scores);
        System.out.println("クラスの平均点: " + average);
        
        // 各学生の成績を表示
        displayGrades(scores);
        
        scanner.close();
    }
    
    // 点数を入力するメソッド
    public static void inputScores(double[] scores, Scanner scanner) {
        // ここにコードを書く
        
    }
    
    // 平均点を計算するメソッド
    public static double calculateAverage(double[] scores) {
        // ここにコードを書く
        
    }
    
    // 点数から成績（A, B, C, D, F）に変換するメソッド
    public static char convertToGrade(double score) {
        // ここにコードを書く
        
    }
    
    // 全学生の成績を表示するメソッド
    public static void displayGrades(double[] scores) {
        // ここにコードを書く
        
    }
}
```

<details>

<summary>ヒント</summary>

* inputScoresメソッドでは、forループを使って各学生の点数を入力し、配列に格納します
* calculateAverageメソッドでは、全ての点数の合計を求め、学生数で割って平均を計算します
* convertToGradeメソッドでは、点数に基づいて適切な成績（例: 90以上はA、80以上はB...）を返します
* displayGradesメソッドでは、各学生の点数と対応する成績を表示します
* メソッドのシグネチャ（戻り値の型、引数のリスト）が問題の要件に合っているか確認してください

</details>

### 4. 実用的なアプリケーション

#### 問題 4-1: 簡易家計簿アプリ

収入と支出を記録し、残高を計算する簡易家計簿アプリを作成してください。トランザクションの追加、表示、残高計算などの機能をメソッドとして実装します。

```java
import java.util.Scanner;

public class BudgetTracker {
    // 収入と支出を記録する配列
    static double[] amounts = new double[100]; // 金額（収入はプラス、支出はマイナス）
    static String[] descriptions = new String[100]; // 説明
    static int transactionCount = 0; // 記録された取引の数
    
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        boolean running = true;
        
        while (running) {
            System.out.println("\n==== 家計簿アプリ ====");
            System.out.println("1. 収入を追加");
            System.out.println("2. 支出を追加");
            System.out.println("3. すべての取引を表示");
            System.out.println("4. 残高を表示");
            System.out.println("5. 終了");
            System.out.print("選択してください: ");
            
            int choice = scanner.nextInt();
            scanner.nextLine(); // 改行文字を消費
            
            switch (choice) {
                case 1:
                    addIncome(scanner);
                    break;
                case 2:
                    addExpense(scanner);
                    break;
                case 3:
                    displayTransactions();
                    break;
                case 4:
                    displayBalance();
                    break;
                case 5:
                    running = false;
                    System.out.println("プログラムを終了します。");
                    break;
                default:
                    System.out.println("無効な選択です。もう一度お試しください。");
            }
        }
        
        scanner.close();
    }
    
    // 収入を追加するメソッド
    public static void addIncome(Scanner scanner) {
        // ここにコードを書く
        
    }
    
    // 支出を追加するメソッド
    public static void addExpense(Scanner scanner) {
        // ここにコードを書く
        
    }
    
    // すべての取引を表示するメソッド
    public static void displayTransactions() {
        // ここにコードを書く
        
    }
    
    // 残高を計算して表示するメソッド
    public static void displayBalance() {
        // ここにコードを書く
        
    }
}
```

<details>

<summary>ヒント</summary>

* addIncomeメソッドでは、金額と説明を入力してもらい、amounts配列に正の値で、descriptions配列に説明を保存します
* addExpenseメソッドでは、金額と説明を入力してもらい、amounts配列に負の値で、descriptions配列に説明を保存します
* displayTransactionsメソッドでは、forループを使って全ての取引を表示します（収入は+、支出は-の形式で表示するとわかりやすい）
* displayBalanceメソッドでは、amounts配列の全ての値を合計して残高を計算し表示します
* transactionCount変数を使って、現在の取引数を追跡します
* 配列の上限（100）を超えないようにチェックする処理も追加するとよいでしょう

</details>

#### 問題 4-2: 単位変換ツール

長さ、重さ、温度など、異なる単位間の変換を行うプログラムを作成してください。各変換処理をメソッドとして実装し、メニューからユーザーが選択できるようにします。

```java
import java.util.Scanner;

public class UnitConverter {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        boolean running = true;
        
        while (running) {
            System.out.println("\n==== 単位変換ツール ====");
            System.out.println("1. 距離変換 (キロ <-> マイル)");
            System.out.println("2. 重量変換 (キログラム <-> ポンド)");
            System.out.println("3. 温度変換 (摂氏 <-> 華氏)");
            System.out.println("4. 終了");
            System.out.print("選択してください: ");
            
            int choice = scanner.nextInt();
            
            switch (choice) {
                case 1:
                    convertDistance(scanner);
                    break;
                case 2:
                    convertWeight(scanner);
                    break;
                case 3:
                    convertTemperature(scanner);
                    break;
                case 4:
                    running = false;
                    System.out.println("プログラムを終了します。");
                    break;
                default:
                    System.out.println("無効な選択です。もう一度お試しください。");
            }
        }
        
        scanner.close();
    }
    
    // 距離変換メニューを表示するメソッド
    public static void convertDistance(Scanner scanner) {
        System.out.println("\n-- 距離変換 --");
        System.out.println("1. キロメートル -> マイル");
        System.out.println("2. マイル -> キロメートル");
        System.out.print("選択してください: ");
        
        int choice = scanner.nextInt();
        
        // ここにコードを書く
        
    }
    
    // キロメートルからマイルに変換するメソッド
    public static double kilometersToMiles(double km) {
        // ここにコードを書く
        
    }
    
    // マイルからキロメートルに変換するメソッド
    public static double milesToKilometers(double miles) {
        // ここにコードを書く
        
    }
    
    // 重量変換メニューを表示するメソッド
    public static void convertWeight(Scanner scanner) {
        // ここにコードを書く
        
    }
    
    // キログラムからポンドに変換するメソッド
    public static double kilogramsToPounds(double kg) {
        // ここにコードを書く
        
    }
    
    // ポンドからキログラムに変換するメソッド
    public static double poundsToKilograms(double pounds) {
        // ここにコードを書く
        
    }
    
    // 温度変換メニューを表示するメソッド
    public static void convertTemperature(Scanner scanner) {
        // ここにコードを書く
        
    }
    
    // 摂氏から華氏に変換するメソッド
    public static double celsiusToFahrenheit(double celsius) {
        // ここにコードを書く
        
    }
    
    // 華氏から摂氏に変換するメソッド
    public static double fahrenheitToCelsius(double fahrenheit) {
        // ここにコードを書く
        
    }
}
```

<details>

<summary>ヒント</summary>

* 各変換メソッドは、対応する計算式を使って単位を変換します:
  * キロメートル -> マイル: 1 km = 0.621371 miles
  * マイル -> キロメートル: 1 mile = 1.60934 km
  * キログラム -> ポンド: 1 kg = 2.20462 pounds
  * ポンド -> キログラム: 1 pound = 0.453592 kg
  * 摂氏 -> 華氏: °F = (°C × 9/5) + 32
  * 華氏 -> 摂氏: °C = (°F - 32) × 5/9
* 各変換メニューメソッド(convertDistance, convertWeight, convertTemperature)では:
  1. サブメニューを表示し、どの変換を行うかユーザーに選択させます
  2. 値の入力を受け付けます
  3. 適切な変換メソッドを呼び出します
  4. 結果を表示します
* 変換結果は小数点以下2桁か3桁程度で表示すると見やすくなります

</details>

### 5. 応用問題: メソッドの再帰呼び出し

#### 問題 5-1: 再帰による階乗計算

再帰呼び出しを使用して、整数の階乗を計算するプログラムを作成してください。階乗は n! = n × (n-1) × (n-2) × ... × 2 × 1 で定義されます。

```java
import java.util.Scanner;

public class RecursiveFactorial {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("階乗を計算する非負整数を入力してください: ");
        int n = scanner.nextInt();
        
        if (n < 0) {
            System.out.println("負の数の階乗は定義されていません。");
        } else {
            long result = factorial(n);
            System.out.println(n + "! = " + result);
        }
        
        scanner.close();
    }
    
    // 再帰を使って階乗を計算するメソッド
    public static long factorial(int n) {
        // ここにコードを書く
        
    }
}
```

<details>

<summary>ヒント</summary>

* 再帰関数では、基底ケース（再帰を終了する条件）を必ず定義する必要があります
* 階乗の定義:
  * 0! = 1（基底ケース）
  * n! = n × (n-1)!（n > 0の場合）
* factorial(n)メソッドは以下のような実装になります:
  * nが0の場合は1を返す（基底ケース）
  * それ以外の場合は、n × factorial(n-1)を返す
* 大きな数の階乗を計算する場合、結果がintの範囲を超える可能性があるため、戻り値の型としてlongを使用しています
* 非常に大きな数（20以上）の階乗を計算すると、longの範囲も超える可能性があることに注意してください

</details>

#### 問題 5-2: フィボナッチ数列

再帰呼び出しを使用して、フィボナッチ数列のn番目の数を計算するプログラムを作成してください。フィボナッチ数列は 0, 1, 1, 2, 3, 5, 8, 13, ... と続く数列で、最初の2項は0と1、それ以降の項は直前の2項の和となります。

```java
import java.util.Scanner;

public class RecursiveFibonacci {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("フィボナッチ数列の何番目の数を計算しますか（0から開始）: ");
        int n = scanner.nextInt();
        
        if (n < 0) {
            System.out.println("負のインデックスは無効です。");
        } else {
            long result = fibonacci(n);
            System.out.println("フィボナッチ数列の" + n + "番目の数: " + result);
        }
        
        scanner.close();
    }
    
    // 再帰を使ってフィボナッチ数を計算するメソッド
    public static long fibonacci(int n) {
        // ここにコードを書く
        
    }
}
```

<details>

<summary>ヒント</summary>

* フィボナッチ数列の定義:
  * F(0) = 0（最初の基底ケース）
  * F(1) = 1（2つ目の基底ケース）
  * F(n) = F(n-1) + F(n-2)（n > 1の場合）
* fibonacci(n)メソッドは以下のような実装になります:
  * nが0の場合は0を返す（基底ケース）
  * nが1の場合は1を返す（基底ケース）
  * それ以外の場合は、fibonacci(n-1) + fibonacci(n-2)を返す
* 単純な再帰実装はnが大きくなると非効率になります（同じ値が何度も計算される）
* これを改善するために、結果をキャッシュするメモ化や、反復的なアプローチを検討することもできます（オプション）

</details>

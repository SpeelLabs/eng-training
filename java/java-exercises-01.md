---
description: >-
  基礎文法 (変数、データ型、演算子、条件分岐)
  を使ったより実践的な問題です。これらの問題では、これまで学んだ内容を組み合わせて、実際のプログラミングで必要とされる問題解決能力を養います。
---

# 実践問題①

### 1. 入出力と型変換の応用

#### 問題 1-1: 時間の換算

ユーザーから分単位の時間を入力として受け取り、「時間:分」 の形式に変換するプログラムを作成してください。

```java
import java.util.Scanner;

public class TimeConverter {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("分単位の時間を入力してください: ");
        int totalMinutes = scanner.nextInt();
        
        // 入力された分を「時間:分」の形式に変換
        
        
        // 結果を表示（例: 125分 → 2時間5分）
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* 時間は 「分 ÷ 60」 の商 (整数除算) で求められます
* 残りの分は 「分 ÷ 60」 の余り (% 演算子) で求められます
* 文字列連結を使用して結果を表示しましょう

</details>

#### 問題 1-2: 商品購入計算

ユーザーから商品の価格と購入数を入力として受け取り、合計金額と消費税 (10%) を計算するプログラムを作成してください。

```java
import java.util.Scanner;

public class PurchaseCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("商品の価格を入力してください: ");
        // 価格の入力を受け取る
        
        
        System.out.print("購入数量を入力してください: ");
        // 数量の入力を受け取る
        
        
        // 商品の合計金額（税抜）を計算
        
        
        // 消費税額を計算（税率10%）
        
        
        // 税込み合計金額を計算
        
        
        // 結果を適切な形式で表示
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* 税抜き合計金額は 「価格 × 数量」 で計算できます
* 消費税額は 「税抜き合計金額 × 0.1」 で計算できます
* 小数点以下の端数処理は必要に応じて行いましょう ( `Math.round()` などを使用)
* `printf()` を使用すると金額を見やすく表示できます

</details>

### 2. 制御構造と条件分岐

#### 問題 2-1: 電気料金計算

電気料金を計算するプログラムを作成してください。料金体系は以下の通りです。

* 基本料金: 1,200円
* 120kWh 以下の使用: 1kWh あたり 20円
* 120kWh 超 300kWh 以下の使用: 追加 1kWh あたり 25円
* 300kWh 超の使用: 追加 1kWh あたり 30円

```java
import java.util.Scanner;

public class ElectricityBillCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        // 基本料金を設定
        final int BASIC_FEE = 1200; // 円
        
        System.out.print("電気使用量(kWh)を入力してください: ");
        double usage = scanner.nextDouble();
        
        // 使用量に応じた料金計算
        
        
        // 合計金額の計算と表示
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* 条件分岐を使用して、使用量に応じた料金を計算します
* 段階的に料金が変わるため、各使用量区分ごとに計算する必要があります
* 例えば、350kWh の場合:
  * 最初の 120kWh は 20円/kWh で計算
  * 次の 180kWh (120kWh～300kWh) は 25円/kWh で計算
  * 残りの 50kWh (300kWh超) は 30円/kWh で計算

</details>

#### 問題 2-2: 三角形の種類判定

3つの角度を入力として受け取り、それが鋭角三角形、直角三角形、鈍角三角形のいずれかを判定するプログラムを作成してください。

```java
import java.util.Scanner;

public class TriangleClassifier {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.println("三角形の3つの角度を入力してください（単位: 度）");
        System.out.print("1つ目の角度: ");
        int angle1 = scanner.nextInt();
        
        System.out.print("2つ目の角度: ");
        int angle2 = scanner.nextInt();
        
        System.out.print("3つ目の角度: ");
        int angle3 = scanner.nextInt();
        
        // 三角形が成立するかを確認 (3つの角の合計が180度かどうか)
        
        
        // 三角形の種類を判定
        // 鋭角三角形: すべての角が 90度未満
        // 直角三角形: 1つの角がちょうど 90度
        // 鈍角三角形: 1つの角が 90度を超える
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* 三角形の内角の和は 180度である必要があります
* 条件分岐を使用して 3つの角度を確認します
* 各角度が 90度と等しいか、90度より大きいかをチェックします

</details>

### 3. 数学的計算の応用

#### 問題 3-1: 円周上の点の座標計算

円の半径と角度 (度数法) を入力として受け取り、円周上の点の座標 (x, y) を計算するプログラムを作成してください。

```java
import java.util.Scanner;

public class CirclePointCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("円の半径を入力してください: ");
        double radius = scanner.nextDouble();
        
        System.out.print("角度を入力してください（0～360度）: ");
        double angleDegrees = scanner.nextDouble();
        
        // 度をラジアンに変換
        
        
        // 円周上の点の座標 (x, y) を計算
        
        
        // 結果を表示 (小数点以下 2桁まで)
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* 度からラジアンへの変換: ラジアン = 度 × π ÷ 180
* 円周上の点の座標計算:
  * x = 半径 × cos(角度(ラジアン))
  * y = 半径 × sin(角度(ラジアン))
* Java の数学関数を使用: `Math.cos()`, `Math.sin()`, `Math.PI`
* `System.out.printf("%.2f", 値)` で小数点以下 2桁まで表示できます

</details>

#### 問題 3-2: 体重と身長からの適正体重計算

身長と体重を入力として受け取り、BMI (Body Mass Index) と適正体重を計算するプログラムを作成してください。

```java
import java.util.Scanner;

public class WeightCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("身長をcmで入力してください: ");
        double heightCm = scanner.nextDouble();
        
        System.out.print("体重をkgで入力してください: ");
        double weightKg = scanner.nextDouble();
        
        // 身長をメートルに変換
        
        
        // BMIを計算 (体重(kg) ÷ 身長(m)の2乗)
        
        
        // 適正体重を計算 (身長(m)の2乗 × 22)
        
        
        // 現在の体重と適正体重の差を計算
        
        
        // 結果の表示
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* 身長をcmからmに変換: 身長(m) = 身長(cm) ÷ 100
* BMI = 体重(kg) ÷ (身長(m) × 身長(m))
* 適正体重 = 身長(m) × 身長(m) × 22
* 体重差 = 現在の体重 - 適正体重
* 条件分岐を使用して、体重差に基づくメッセージを表示できます

</details>

### 4. ゲームプログラミング入門

#### 問題 4-1: 簡易じゃんけんゲーム

コンピュータとじゃんけんをするプログラムを作成してください。ユーザーの手とコンピュータの手を比較して、勝敗を判定します。

```java
import java.util.Scanner;
import java.util.Random;

public class RockPaperScissors {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Random random = new Random();
        
        System.out.println("じゃんけんゲーム");
        System.out.println("1: グー, 2: チョキ, 3: パー");
        System.out.print("あなたの手を選んでください (1-3): ");
        
        int userChoice = scanner.nextInt();
        
        // ユーザーの選択が有効かチェック
        
        
        // コンピュータの手をランダムに生成 (1-3)
        
        
        // ユーザーとコンピュータの手を表示
        
        
        // 勝敗の判定
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* `Random` クラスの `nextInt(3) + 1` を使用して、1 から 3 のランダムな数値を生成します
* 選択を文字列 ("グー", "チョキ", "パー") に変換して表示するとわかりやすくなります
* じゃんけんの勝敗ルール:
  * 同じ手: 引き分け
  * グー (1) vs チョキ (2): グーの勝ち
  * チョキ (2) vs パー (3): チョキの勝ち
  * パー (3) vs グー (1): パーの勝ち
* 条件分岐を使用して、各組み合わせの勝敗を判定します

</details>

#### 問題 4-2: 数当てゲーム

コンピュータがランダムに選んだ 1 ～ 100 の数を当てるゲームを作成してください。ユーザーが入力した数に対して、「大きすぎます」 「小さすぎます」 「正解です！」 とヒントを出します。

```java
import java.util.Scanner;
import java.util.Random;

public class NumberGuessingGame {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Random random = new Random();
        
        // 1から100までのランダムな数を生成
        int secretNumber = random.nextInt(100) + 1;
        
        System.out.println("1から100までの数を当ててください！");
        System.out.print("あなたの予想: ");
        int guess = scanner.nextInt();
        
        // ユーザーの予想と secretNumber を比較
        
        
        // 比較結果に応じたメッセージを表示
        
        
        // 正解だった場合、何回で当てたかを表示
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* `Random` クラスの `nextInt(100) + 1` で 1 から 100 までのランダムな整数を生成します
* if-else if-else 構文を使用して、ユーザーの予想と秘密の数を比較します
* 予想が `secretNumber` より大きい場合、「大きすぎます」 と表示します
* 予想が `secretNumber` より小さい場合、「小さすぎます」 と表示します
* 予想が `secretNumber` と等しい場合、「正解です！」 と表示します

</details>

### 5. 実用プログラム

#### 問題 5-1: 給与計算プログラム

勤務時間と時給を入力として受け取り、給与を計算するプログラムを作成してください。8時間を超える勤務は残業として時給の 1.5 倍で計算されます。

```java
import java.util.Scanner;

public class SalaryCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("勤務時間を入力してください: ");
        double hoursWorked = scanner.nextDouble();
        
        System.out.print("時給(円)を入力してください: ");
        double hourlyRate = scanner.nextDouble();
        
        // 通常勤務時間と残業時間を計算
        
        
        // 通常勤務の給与を計算
        
        
        // 残業代を計算 (1.5倍)
        
        
        // 合計給与を計算
        
        
        // 結果を表示
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* 通常勤務時間は 8時間を上限として、実際の勤務時間と 8 の小さい方を選びます
* 残業時間は (勤務時間 - 8) で計算し、0未満なら 0 とします
* 通常給与 = 通常勤務時間 × 時給
* 残業代 = 残業時間 × 時給 × 1.5
* 合計給与 = 通常給与 + 残業代

</details>

#### 問題 5-2: 金額の両替計算

入力された金額を、できるだけ少ない枚数の紙幣・硬貨に両替するプログラムを作成してください。日本の通貨単位 (10000円、5000円、1000円、500円、100円、50円、10円、5円、1円) を使用します。

```java
import java.util.Scanner;

public class ChangeCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        System.out.print("金額を入力してください（円）: ");
        int amount = scanner.nextInt();
        
        // 各紙幣・硬貨の枚数を計算
        
        
        // 結果を表示
        
        
        scanner.close();
    }
}
```

<details>

<summary>ヒント</summary>

* 大きな単位から順に計算していきます
* 各単位の枚数は 「金額 ÷ 単位」 の整数部分で求められます
* 残りの金額は 「金額 % 単位」（剰余) で求められます
* 例：12345円の場合
  * 10000円札: 12345 ÷ 10000 = 1枚
  * 残り: 12345 % 10000 = 2345円
  * 5000円札: 2345 ÷ 5000 = 0枚
  * 1000円札: 2345 ÷ 1000 = 2枚
  * 残り: 2345 % 1000 = 345円
  * ...以下同様に計算

</details>

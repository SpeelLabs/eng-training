# 1. JDK の準備と VS Code の設定

### 1.1 Javaとは何か

**Java** は、Sun Microsystems (現在の Oracle が所有) が開発したプログラミング言語で、世界中で広く使用されています。

#### 1.1.1 Java の特徴

Java には以下のような特徴があります。

* **クロスプラットフォーム**: 「Write Once, Run Anywhere」 の理念で、異なる OS で同じコードが動作
* **オブジェクト指向**: すべてがクラスとオブジェクトで構成
* **安全性**: メモリー管理が自動化され、セキュリティが強固
* **高パフォーマンス**: JIT (Just-In-Time) コンパイルにより高速に実行

> 📝 **ポイント**: Java は、Web 開発、モバイルアプリ (Android)、エンタープライズシステムなど、様々な分野で使用されています。

#### 1.1.2 JDK の概念

**JDK (Java Development Kit)** は、Java アプリケーションを開発するために必要なソフトウェアツールキットです。

JDK には以下の主要コンポーネントが含まれています。

* **javac**: Java ソースコードをバイトコードにコンパイルするコンパイラー
* **java**: バイトコードを実行するプログラム
* **javadoc**: ドキュメントを生成するツール
* **標準ライブラリー**: 開発に必要な基本的なクラス群

> 📝 **ポイント**: JDK は開発に必要なすべてのツールを含みます。Java Runtime のみ (JRE) では開発できません。

### 1.2 Oracle JDK 21 のインストール

#### 1.2.1 JDK のダウンロード

1. **Oracle 公式サイトにアクセス**
   * [Oracle JDKダウンロードページ](https://www.oracle.com/java/technologies/downloads/) にアクセス
   * **Java 21** タブを選択
2. **Windows 版のダウンロード**
   * **Windows x64 Installer** を選択
   * ファイル名：`jdk-21_windows-x64_bin.exe`&#x20;

> 📝 **ポイント**: Oracle JDK 21 は、2026年9月まで NFTC (Oracle No-Fee Terms and Conditions) ライセンスで提供され、商用利用や本番環境での利用も含めて無料です。学習や開発目的で使用する分には完全に無償で利用できます。

#### 1.2.2 JDK のインストール

1. **インストーラーの実行**
   * ダウンロードした `jdk-21_windows-x64_bin.exe` を管理者権限で実行
   * インストールウィザードの指示に従います
2. **インストールパスの確認**
   * デフォルトインストールパス：`C:\Program Files\Java\jdk-21`
   * 特に理由がなければデフォルトを使用

#### 1.2.3 環境変数の設定

Java を正しく使用するには、システム環境変数の設定が必要です。

1. **JAVA\_HOME 環境変数の設定**
   * <kbd>Windows</kbd> キーを押して 「env」 と入力、「システム環境変数の編集」 を選択
   * 「システム環境変数」 で 「新規」 をクリック
     * 変数名：`JAVA_HOME`
     * 変数値：`C:\Program Files\Java\jdk-21`
2. **Path 環境変数への追加**
   * システム環境変数の一覧から 「Path」 を選択、「編集」 をクリック
   * 「新規」 をクリックして以下を追加：
     * 変数名：`Path`&#x20;
     * 変数値：`%JAVA_HOME%\bin`&#x20;
3.  **インストールの確認**

    PowerShell を開いて以下を実行：

    ```cmd
    java -version
    ```

    \
    実行結果：

    ```
    java 21 2023-09-19 LTS
    Java(TM) SE Runtime Environment (build 21+35-LTS-2513)
    Java HotSpot(TM) 64-Bit Server VM (build 21+35-LTS-2513, mixed mode, sharing)
    ```

    \
    次に、コンパイラの確認：

    ```cmd
    javac -version
    ```

    \
    実行結果：

    ```
    javac 21
    ```

> 📝 **ポイント**: バージョン情報が表示されれば、JDK のインストールは成功です。表示されない場合は、コマンドプロンプトを再起動してみてください。

### 1.3 VS Code のインストールと基本設定

#### 1.3.1 VS Code のインストール

1. [Visual Studio Code公式サイト](https://code.visualstudio.com/)から VS Code をダウンロード
2. インストーラーを実行し、以下のオプションを有効にすることを推奨：
   * PATH への追加
   * コンテキストメニューへの 「Code で開く」 の追加

#### 1.3.2 基本的な拡張機能のインストール

VS Code でJava 開発を行うための必須拡張機能をインストールします。

1. **Extension Pack for Java**
   * 左側のExtensions (拡張機能) パネルを開く (<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>X</kbd> )
   * 検索ボックスに 「Extension Pack for Java」 と入力
   * Microsoft 提供の拡張パックをインストール

この拡張パックには以下が含まれています。

* Language Support for Java (TM) by Red Hat
* Debugger for Java
* Test Runner for Java
* Maven for Java
* Project Manager for Java

2. ~~**その他推奨拡張機能**~~
   * ~~Code Runner：コードを簡単に実行~~
   * ~~Visual Studio IntelliCode：AI によるコード補完~~

> 📝 **ポイント**: Extension Pack for Java をインストールするだけで Java 開発に必要な機能が一度に揃います。

#### 1.3.3 VS Code の Java 設定

1. **設定ファイルの確認**
   * VS Code で <kbd>Ctrl</kbd> + <kbd>,</kbd> を押して設定を開く
   * 検索バーに 「java home」 と入力
   * 「Java: Home」 の項目が空であることを確認 (環境変数が正しく設定されていれば自動検出される)
2. **フォントサイズの調整 (オプション)**
   * 設定画面で 「editor fontSize」 を検索
   * 見やすいサイズに調整 (14～16 推奨)

### 1.4 Java プロジェクトの基本構造

#### 1.4.1 プロジェクト構造の理解

VS Code で 「No Build Tools」 タイプの Java プロジェクトを作成すると、以下のディレクトリー構造が自動的に生成されます。

```
my-java-project/
│
├─ src/                # ソースコードフォルダ
│   └─ App.java        # デフォルトで作成されるメインクラス
│
├─ lib/                # 外部ライブラリ用フォルダ
│
├─ .vscode/            # VS Code 設定ファイル
│   └─ settings.json
│
└─ README.md           # プロジェクト説明ファイル
```

> 📝 **ポイント**: `bin/` フォルダはソースコードをコンパイルすると自動的に作成されます。 settings.json で出力先が指定されており、`.vscode/settings.json` で確認できます。

#### 1.4.2 最初の Java プロジェクトの作成

1. **Extension Pack for Java のコマンドを使用**
   * VS Code を開いた状態で、<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd> でコマンドパレットを開く
   * 「Create Java Project」 と入力して選択
   * プロジェクトタイプ：「No Build Tools」 を選択
   * プロジェクト名：「JavaBasics」 を入力
   * プロジェクトの保存場所を選択 (デスクトップなど)
2. **プロジェクトの構成確認**
   * VS Code でプロジェクトフォルダーが自動的に開かれる
   *   基本的な構造：

       ```
       JavaBasics/
       │
       ├─ src/
       │   └─ App.java
       │
       ├─ .vscode/
       │
       └─ README.md
       ```
3. **最初のJavaファイルの作成**
   * `src` フォルダを右クリック
   * 「New File」 を選択
   * ファイル名：`HelloWorld.java`&#x20;
4.  **最初のJavaコードの記述**

    ```java
    public class HelloWorld {
        public static void main(String[] args) {
            System.out.println("Hello, Java!");
        }
    }
    ```
5.  **コードの実行**

    * Java ファイルを開いた状態で、右クリック > 「Run Java」 を選択
    * または、<kbd>Ctrl</kbd> + <kbd>F5</kbd> でプログラムを実行

    実行結果：

    ```
    Hello, Java!
    ```

> 📝 **ポイント**: 「Create Java Project」 コマンドを使うと、プロジェクトの基本的な構造が自動的に作成されます。手動でフォルダ構造を作成することもできますが、拡張機能を使った方が効率的です。

### 1.5 よくあるエラーと対処法

#### 1.5.1 Java コマンドが認識されない

**エラーメッセージ：**

{% code overflow="wrap" %}
```
'javac' は、内部コマンドまたは外部コマンド、操作可能なプログラムまたはバッチ ファイルとして認識されていません。
```
{% endcode %}

**対処法：**

1.  **環境変数の確認**:

    * コマンドプロンプトで以下を実行：

    ```cmd
    echo %JAVA_HOME%
    echo %PATH%
    ```

    * **JAVA\_HOME** が設定されているか、**Path** に `%JAVA_HOME%\bin` が含まれているか確認
2. **コマンドプロンプトの再起動**:
   * 環境変数を変更した場合は、コマンドプロンプトを再起動する
3. **Java が正しくインストールされているか確認**:
   * `C:\Program Files\Java\jdk-21` フォルダーが存在するか確認
   * 必要であれば JDK を再インストール

#### 1.5.2 クラス名とファイル名の不一致

**エラーメッセージ：**

```
class Test is public, should be declared in a file named Test.java
```

**対処法：**

* クラス名とファイル名を一致させる
* `public` クラスの場合は特に注意が必要

例：

```java
// Test.java ファイルの場合
public class Test {
    public static void main(String[] args) {
        // コード
    }
}
```

#### 1.5.3 VS Code 拡張機能が Java を認識しない

**エラーメッセージ：**

```
Cannot find a Java installation on your system
```

**対処法：**

1. **JDK のインストール確認**:
   * コマンドプロンプトで `java -version` を実行
   * バージョン情報が表示されない場合は JDK の再インストールが必要
2. **VS Code の再起動**:
   * 環境変数の変更後は VS Code を再起動
3.  **VS Code の Java 設定を確認**:

    * 設定 ( <kbd>Ctrl</kbd> + <kbd>,</kbd> ) から 「java home」 を検索
    * 必要に応じて手動で JDK パスを指定

    ```json
    "java.configuration.runtimes": [
      {
        "name": "JavaSE-21",
        "path": "C:\\Program Files\\Java\\jdk-21",
        "default": true
      }
    ]
    ```

#### 1.5.4 Java プロジェクトが作成できない

**エラーメッセージ：**

```
Failed to create project: Project folder not empty
```

**対処法：**

* プロジェクトを作成する場所が空のフォルダか確認
* または既存のフォルダを開く場合は、「Open Folder」 を使用

> 📝 **ポイント**: 環境変数の変更後は、コマンドプロンプトや VS Code の再起動が必要です。トラブルシューティングの際は、まず環境変数の確認から始めましょう。

***

### 演習問題

#### 問題 1-1: JDK インストールの確認

コマンドプロンプトまたは PowerShell を開いて、Java のバージョンを確認するコマンドを実行してください。

<details>

<summary>ヒント</summary>

コマンドプロンプトで以下のコマンドを実行します：

```cmd
java -version
javac -version
```

両方のコマンドでバージョン情報が表示されれば、インストールは成功しています。

</details>

#### 問題 1-2: 環境変数の確認

あなたの環境の環境変数を確認し、JAVA\_HOMEとPathが正しく設定されているか確認してください。

<details>

<summary>ヒント</summary>

コマンドプロンプトで以下を実行：

```cmd
echo %JAVA_HOME%
echo %PATH%
```

JAVA\_HOMEが表示され、Path内にJavaのbinディレクトリへのパスが含まれていれば正しく設定されています。

</details>

#### 問題 1-3: Hello World プログラムの作成

新しくプロジェクトフォルダを作成し、挨拶を表示する最初のJavaプログラムを作成してください。

<details>

<summary>解答例</summary>

1. プロジェクトフォルダ「first-project」を作成
2. src フォルダを作成
3. MyFirstProgram.java ファイルを作成：

```java
public class MyFirstProgram {
    public static void main(String[] args) {
        System.out.println("はじめてのJavaプログラム");
        System.out.println("プログラミングの学習を始めます！");
    }
}
```

4. VS CodeでRun Javaを実行、または以下コマンドで実行：

```cmd
cd src
javac MyFirstProgram.java
java MyFirstProgram
```

</details>

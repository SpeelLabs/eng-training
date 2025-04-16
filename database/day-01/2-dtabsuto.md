---
description: >-
  この章では、MySQL 8.4のインストール、VS Codeの設定、MySQL Shell for VS
  Codeの使い方など、データベース操作の準備を行います。
---

# 2. データベース環境構築と接続

### 2.1 MySQL のインストール

#### Windows 11 への MySQL インストール手順

1. **MySQL Installer のダウンロード**
   * [MySQL公式サイト](https://dev.mysql.com/downloads/installer/)から MySQL Installer をダウンロード
   * 「Windows (x86, 64-bit), MSI Installer」 を選択 (通常は64-bit推奨)
2. **インストーラの実行**
   * ダウンロードしたインストーラを実行
   * Setup Typeで 「Developer Default」 を選択
3. **製品の構成**
   * 「Next」 をクリックして進み、必要なプロダクトをインストール
4. **MySQLサーバーの設定**
   * Config Type で 「Development Computer」 を選択
   * Connectivity: デフォルトのポート (3306) を使用
   * Authentication Method: 「Use Strong Password Encryption」 を選択
5. **ルートパスワードの設定**
   * MySQL root アカウントのパスワードを設定
   * **重要**: このパスワードは忘れないようにメモしておいてください！
6. **Windowsサービスの設定**
   * 「Configure MySQL Server as a Windows Service」 にチェック
   * 「Start the MySQL Server at System Startup」 にチェック
7. **インストールの完了**
   * 「Next」 をクリックして進み、最後に 「Finish」

> 📝 **ポイント**: インストール中に設定した root (管理者) パスワードは非常に重要です。忘れるとサーバーにアクセスできなくなるため、必ず安全な場所に記録しておきましょう。後から変更することは可能ですが、手順が複雑になります。

#### インストールの確認

MySQL Shell を使って接続テストを行います。

1. スタートメニューから MySQL Command Line Client を起動
2. パスワードの入力を求められるので、インストール時に設定した root パスワードを入力
3. 正常に接続できれば、以下のようなプロンプトが表示されます：

```
mysql>
```

4. 試しに簡単なSQLコマンドを実行してみましょう。

```sql
SHOW DATABASES;
```

以下のようなデータベースのリストが表示されれば成功です。

```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```

> 📝 **ポイント**: 「Access denied」 などのエラーが表示される場合は、パスワードが間違っている可能性があります。大文字・小文字や特殊文字の入力に注意してください。接続できないとその後の作業ができないので、この段階で確実に接続できることを確認しましょう。

### 2.2 VS Code の設定

#### VS Code のインストール

1. [Visual Studio Code公式サイト](https://code.visualstudio.com/)から VS Code をダウンロード
2. インストーラを実行し、ウィザードに従ってインストール
3. インストールオプションで以下を有効にすることを推奨：
   * PATH への追加
   * コンテキストメニューへの 「Code で開く」 の追加
   * ファイルの関連付け

#### MySQL Shell for VS Code 拡張機能のインストール

1. VS Code を起動
2. 左側の Extensions (拡張機能) パネルを開く (または <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>X</kbd> )
3. 検索ボックスに 「MySQL Shell for VS Code」 と入力
4. 拡張機能を見つけたら <kbd>Install</kbd> ボタンをクリック

> 📝 **ポイント**: 拡張機能は随時アップデートされるため、画面や機能が資料と少し異なる場合があります。基本的な操作の流れは同じですが、もし大きく異なる場合は拡張機能のドキュメントを参照してください。

#### MySQL 接続の設定

1. VS Code を起動
2. アクティビティバーの MySQL Shell for VS Code アイコン (データベースのシンボル) をクリック
3. 「 + 」 ボタンをクリックして新しい接続を追加
4. 接続情報を入力：
   * Connection Name: `Local Instance` (任意の名前)
   * Hostname: `localhost`
   * Port: `3306`
   * Username: `root`
   * Password: `[インストール時に設定したパスワード]`
5. 「Connect」 をクリック

正常に接続できれば、デフォルトのデータベース (information\_schema, mysql, performance\_schema, sys) が表示されます。

> 📝 **ポイント**: 接続に失敗する場合、最も多い原因は以下の 3つです。
>
> 1. パスワードの入力ミス
> 2. MySQL サーバーが起動していない
> 3. ポート番号が異なる
>
> 特に初めてのセットアップでは、これらを順番に確認していくとよいでしょう。

### 2.3 MySQL Shell for VS Code の基本操作

#### インターフェースの概要

MySQL Shell for VS Code の主なコンポーネント：

* **Connections**: MySQLサーバーへの接続を管理
* **SCHEMAS**: データベース (スキーマ) の一覧
* **TABLES**: 選択したデータベース内のテーブル一覧
* **QUERY**: SQL クエリーを実行するためのエディター

#### 基本的なタスク

**新しいクエリーの作成**

1. MySQL Shell for VS Code パネルで接続を選択
2. 「New Query」 ボタンをクリック
3. 新しい SQL ファイル ( `.mysql-notebook` ) が開きます

> 📝 **ポイント**: VSCode で SQL ファイルを作成・保存しておくと、後で再利用できて便利です。特に研修中は同じクエリを何度も実行することが多いので、ファイルとして保存しておくことをお勧めします。

**クエリーの実行**

1. クエリーエディタに SQL コマンドを入力：

```sql
SELECT 'Hello, MySQL!' AS greeting;
```

2. クエリーを実行するには：
   * クエリーを選択して <kbd>Run Selected</kbd> ボタンをクリック
   * または <kbd>Ctrl</kbd> + <kbd>Enter</kbd> キーを押す
3. 結果は下部の「MySQL Results」タブに表示されます：

```
+----------------+
| greeting       |
+----------------+
| Hello, MySQL!  |
+----------------+
```

> 📝 **ポイント**: 複数のクエリを含むファイルでは、実行したい部分だけを選択してから実行ボタンを押すか、カーソルを置いた行 (またはセミコロンで区切られた部分) だけを実行できます。複数のクエリをまとめて実行すると、エラーが発生した時点で停止するため注意しましょう。

**データベースの切り替え**

1. SCHEMAS パネルで使用したいデータベースをダブルクリック
2. または、クエリーエディターで以下のコマンドを実行：

```sql
USE データベース名;
```

> 📝 **ポイント**: どのデータベースを使用しているかを忘れがちです。クエリを実行する前に、正しいデータベースが選択されているか確認する習慣をつけましょう。間違ったデータベースでクエリを実行すると、「テーブルが存在しない」 などのエラーの原因になります。

**テーブル情報の確認**

1. SCHEMAS パネルでデータベースを展開
2. TABLES の下に表示されるテーブル一覧からテーブルを右クリック
3. 「Table Inspector」 を選択すると、テーブルの詳細情報が表示されます

### 2.4 基本的なデータベースコマンド

#### データベース操作の基本

**データベース一覧の表示**

```sql
SHOW DATABASES;
```

**新しいデータベースの作成**

```sql
CREATE DATABASE mydb;
```

> 📝 **ポイント**: データベース名は、アルファベット、数字、アンダースコア ( \_ ) で構成するのが一般的です。スペースや特殊文字は避けましょう。また、大文字小文字の区別は OS によって異なるため、基本的には小文字で統一するのがおすすめです。

**データベースの選択**

```sql
USE mydb;
```

**現在使用中のデータベースの確認**

```sql
SELECT DATABASE();
```

**データベースの削除**

```sql
DROP DATABASE mydb;
```

> ⚠️ **注意**: `DROP DATABASE` は取り消せません。実行前に本当に削除して良いか確認してください。特に本番環境では細心の注意が必要です。

#### テーブル操作の基本

**テーブル一覧の表示**

```sql
SHOW TABLES;
```

**テーブル構造の確認**

```sql
DESCRIBE テーブル名;
-- または省略形:
DESC テーブル名;
```

> 📝 **ポイント**: テーブルの構造を確認することは、SQL を書く前の重要なステップです。特にカラム名やデータ型を把握していないと、正しいクエリが書けません。`DESCRIBE` コマンドは頻繁に使うことになるでしょう。

### 2.5 MySQL 接続時のトラブルシューティング ⭐️

#### よくあるエラーと対処法

**接続エラー: ホストへ接続できない**

**エラーメッセージ例**:

```
ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost' (10061)
```

**対処法**:

1. MySQL サービスが起動しているか確認
   * Windows: サービスアプリケーションで MySQL80 サービスの状態を確認
   * 「スタート」 → 「サービス」 → 「MySQL80」 を右クリック → 「開始」
2. ファイアウォールの設定を確認
   * ポート `3306` が許可されているか確認

> 📝 **ポイント**: MySQL サーバーが起動していない場合、どんなに接続情報が正しくても接続できません。「Can't connect」 エラーが出たら、まず MySQL サービスが起動しているか確認しましょう。

**認証エラー: パスワードが違う**

**エラーメッセージ例**:

```
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
```

**対処法**:

1. パスワードが正しいか確認
2. パスワードをリセットする必要がある場合:
   * MySQL のドキュメントに従ってパスワードリセット手順を実行

> 📝 **ポイント**: 「Access denied」 エラーは、ほとんどの場合パスワードの問題です。特に、大文字小文字や特殊文字の入力ミスに注意してください。数回試してもダメな場合は、パスワードリセットを検討しましょう。ただし、リセット手順は少し複雑なので、パスワードをしっかり管理することが重要です。

***

### 演習問題

#### 問題 2-1: MySQL 接続確認

以下の手順で、インストールした MySQL への接続を確認してください。

1. MySQL Command Line Clientを起動し、root パスワードを入力して接続
2.  次の SQL コマンドを実行して、現在の MySQL バージョンを確認してください:

    ```sql
    SELECT VERSION();
    ```
3. デフォルトで存在するデータベースの一覧を表示してください。

<details>

<summary>解答例</summary>

```sql
-- MySQLバージョンの確認
SELECT VERSION();
-- 実行結果例: 8.4.0

-- データベース一覧の表示
SHOW DATABASES;
-- 実行結果例:
/*
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
*/
```

</details>

#### 問題 2-2: VS Codeでの接続設定

VS Code に MySQL Shell for VS Code 拡張機能をインストールし、以下の手順で接続設定を行ってください。

1. VS Code を起動し、MySQL Shell for VS Code 拡張機能をインストール
2. 新しい接続を作成し、以下の情報を設定:
   * Connection Name: LocalMySQL
   * Hostname: localhost
   * Port: 3306
   * Username: root
   * Password: \[あなたのrootパスワード]
3.  接続が成功したら、新しい SQL ファイルを作成し、以下のクエリーを実行:

    ```sql
    SELECT 'MySQL Connection Successful!' AS status;
    ```

<details>

<summary>ヒント</summary>

MySQL Shell for VS Code パネルにある <kbd>+</kbd> ボタンをクリックして新しい接続を追加します。接続情報を入力し、<kbd>Connect</kbd> をクリックします。接続が成功したら、<kbd>New Query</kbd> ボタンをクリックして新しい SQL ファイルを作成し、クエリーを入力して実行 ( <kbd>Ctrl</kbd> + <kbd>Enter</kbd> ) します。

</details>

#### 問題 2-3: 新しいデータベースの作成

MySQL Shell for VS Code を使用して、以下の手順で新しいデータベースを作成してください。

1. 新しい SQL ファイルで、`practice_db` という名前の新しいデータベースを作成する SQL を記述し実行
2. 作成したデータベースに切り替える
3. 現在選択されているデータベースを確認する

<details>

<summary>解答例</summary>

```sql
-- 新しいデータベースの作成
CREATE DATABASE practice_db;

-- データベースの切り替え
USE practice_db;

-- 現在のデータベースを確認
SELECT DATABASE();
-- 実行結果例: practice_db
```

</details>

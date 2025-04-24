# 7. Git 入門

### 7.1 Git とは何か

**Git** は、プログラムのソースコードなどの変更履歴を記録・追跡するための分散型バージョン管理システムです。簡単に言えば、ファイルの変更履歴を管理するツールです。

Git を使うメリット：

* ファイルの変更履歴を記録できる
* 複数人での共同作業がしやすくなる
* 過去のバージョンに戻ることができる
* オンラインでプロジェクトを共有できる

> 📝 **ポイント**: Git は現代の Web 開発において必須のツールです。基本的な使い方を覚えるだけでも、開発効率が大幅に向上します。

### 7.2 Git の基本概念

#### 7.2.1 リポジトリーとは

**リポジトリー** (repository または repo) は、プロジェクトのファイルとその変更履歴を保存する場所です。リポジトリーには 2種類あります。

1. **ローカルリポジトリー**: 自分のコンピューター上にあるリポジトリー
2. **リモートリポジトリー**: サーバー上にあるリポジトリー (GitHub など)

#### 7.2.2 コミットとは

**コミット (**&#x63;ommit) は、ファイルの変更を記録する作業です。一連の変更に名前 (コミットメッセージ) をつけて保存します。

#### 7.2.3 ブランチとは

**ブランチ** (branch) は、開発の流れを分岐させる機能です。最初は、メインブランチ (main または master) だけを使って作業することもできます。

### 7.3 Git の基本操作

#### 7.3.1 Git のインストール確認

まず、Git がインストールされているか確認しましょう。コマンドプロンプトやターミナルで以下のコマンドを実行します。

```bash
git --version
```

実行結果の例：

```
git version 2.30.1.windows.1
```

バージョン番号が表示されればインストール済みです。表示されない場合は、Git をインストールする必要があります。

#### 7.3.2 Git の初期設定

Git を初めて使う場合は、ユーザー名とメールアドレスを設定します。

```bash
git config --global user.name "あなたの名前"
git config --global user.email "あなたのメールアドレス"
```

実行結果： 設定が完了しても何も表示されません。

設定を確認するには、

```bash
git config --global user.name
git config --global user.email
```

実行結果の例：

```
山田太郎
yamada@example.com
```

### 7.4 ローカルリポジトリーの作成と基本操作

#### 7.4.1 新しいリポジトリーの作成

既存のプロジェクトフォルダを Git リポジトリーにするには、

```bash
# プロジェクトフォルダに移動
cd my-portfolio

# Gitリポジトリーを初期化
git init
```

実行結果の例：

```
Initialized empty Git repository in C:/Users/Username/my-portfolio/.git/
```

これで `.git` という隠しフォルダが作成され、このフォルダがリポジトリーとなります。

#### 7.4.2 ファイルの状態確認

リポジトリ内のファイルの状態を確認するには、

```bash
git status
```

実行結果の例  (新しいファイルがある場合）：

```
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        index.html
        css/style.css

nothing added to commit but untracked files present (use "git add" to track)
```

これは、まだ Git に追跡されていないファイルがあることを示しています。

#### 7.4.3 ファイルをステージングに追加

変更を記録 (コミット) する前に、変更したファイルをステージングエリアに追加する必要があります。

```bash
# 特定のファイルを追加
git add index.html

# すべてのファイルを追加
git add .
```

実行結果： 追加が完了しても何も表示されません。

追加後の状態を確認するには、

```bash
git status
```

実行結果の例：

```
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   index.html
        new file:   css/style.css
```

これは、ファイルがコミットの準備ができていることを示しています。

#### 7.4.4 変更をコミット

ステージングエリアに追加したファイルの変更をコミット (記録) します。

```bash
git commit -m "最初のコミット"
```

実行結果の例：

```
[master (root-commit) a1b2c3d] 最初のコミット
 2 files changed, 50 insertions(+)
 create mode 100644 index.html
 create mode 100644 css/style.css
```

> 📝 **ポイント**: コミットメッセージは、変更内容が分かるように具体的に書くことが重要です。例えば 「ファイル追加」 よりも 「ナビゲーションメニューの追加」 の方が良いです。

#### 7.4.5 コミット履歴の確認

これまでのコミット履歴を確認するには、

```bash
git log
```

実行結果の例：

```
commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0 (HEAD -> master)
Author: 山田太郎 <yamada@example.com>
Date:   Wed Apr 24 10:30:00 2023 +0900

    最初のコミット
```

### 7.5 GitHub との連携 ⭐

[GitHub](https://github.com/) は、Git リポジトリーをホスティングするウェブサービスです。プロジェクトを公開したり、チームで共有したりするのに便利です。

#### 7.5.1 GitHub アカウントの作成

GitHub を使うには、アカウントを作成する必要があります。[GitHub 公式サイト](https://github.com/) からサインアップできます。

#### 7.5.2 リモートリポジトリーの作成

GitHub にログイン後、右上の <kbd>+</kbd> アイコン→ <kbd>New repository</kbd> をクリックして、新しいリポジトリーを作成します。

リポジトリー名を入力 (例：「my-portfolio」 ) し、<kbd>Create repository</kbd> をクリックします。

#### 7.5.3 ローカルリポジトリーとリモートリポジトリーの連携

ローカルリポジトリーを GitHub のリモートリポジトリーと連携させるには、

```bash
# リモートリポジトリーを追加
git remote add origin https://github.com/あなたのユーザー名/my-portfolio.git

# ローカルの変更をリモートにプッシュ
git push -u origin master
```

実行結果の例：

```
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 1.02 KiB | 1.02 MiB/s, done.
Total 4 (delta 0), reused 0 (delta 0)
To https://github.com/username/my-portfolio.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

これで、ローカルのコミットが GitHub に反映されました。

#### 7.5.4 以降の変更をプッシュ

最初の連携後、ファイルを変更してコミットしたら、以下のコマンドで GitHub に反映できます。

```bash
git push
```

### 7.6 よく使う Git コマンドのまとめ

<table><thead><tr><th width="158.390625">コマンド</th><th width="253.390625">説明</th><th>使用例</th></tr></thead><tbody><tr><td><code>git init</code></td><td>新しいリポジトリーを作成</td><td><code>git init</code></td></tr><tr><td><code>git status</code></td><td>ファイルの状態を確認</td><td><code>git status</code></td></tr><tr><td><code>git add</code></td><td>ファイルをステージングに追加</td><td><code>git add index.html</code> または <code>git add .</code></td></tr><tr><td><code>git commit</code></td><td>変更をコミット（記録）</td><td><code>git commit -m "メッセージ"</code></td></tr><tr><td><code>git log</code></td><td>コミット履歴を確認</td><td><code>git log</code></td></tr><tr><td><code>git remote add</code></td><td>リモートリポジトリーを追加</td><td><code>git remote add origin URL</code></td></tr><tr><td><code>git push</code></td><td>変更をリモートにプッシュ</td><td><code>git push</code> または <code>git push -u origin main</code></td></tr><tr><td><code>git pull</code></td><td>リモートの変更を取得</td><td><code>git pull</code></td></tr></tbody></table>

> 📝 **ポイント**: 最初は基本的なコマンドだけを覚えて、徐々に他のコマンドを学んでいくことをお勧めします。

### 7.7 Git 操作の基本的な流れ

Git を使った基本的な作業の流れは以下の通りです。

1. **初期設定** (初回のみ)
   * リポジトリーの初期化 (`git init`)
   * リモートリポジトリーとの連携 (`git remote add origin URL`)
2. **日常の作業サイクル**
   * ファイルの編集
   * 変更状態の確認 (`git status`)
   * 変更をステージングに追加 (`git add .`)
   * 変更をコミット (`git commit -m "メッセージ"`)
   * 変更をリモートにプッシュ (`git push`)

この流れを繰り返すことで、プロジェクトの変更履歴を管理していきます。



***

### 演習問題

#### 問題 7-1: Git リポジトリーの初期化

「my-portfolio」 プロジェクトを Git リポジトリーとして初期化してください。

1. コマンドプロンプトやターミナルを開く
2. プロジェクトフォルダに移動
3. Git リポジトリーを初期化
4. 初期化が成功したか確認

\<details> \<summary>ヒント\</summary>

```bash
# プロジェクトフォルダに移動
cd my-portfolio

# Gitリポジトリを初期化
git init

# 初期化が成功したか確認
git status
```

実行結果の例：

```
Initialized empty Git repository in C:/Users/Username/my-portfolio/.git/

On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        index.html
        css/

nothing added to commit but untracked files present (use "git add" to track)
```

\</details>

#### 問題 7-2: 最初のコミット

プロジェクトの現在の状態をGitに記録 (コミット) してください。

1. すべてのファイルをステージングエリアに追加
2. 「初期バージョン」 というメッセージでコミット
3. コミットが成功したか確認

\<details> \<summary>ヒント\</summary>

```bash
# すべてのファイルをステージングエリアに追加
git add .

# 変更をコミット
git commit -m "初期バージョン"

# コミット履歴を確認
git log
```

実行結果の例：

```
[master (root-commit) f1e2d3c] 初期バージョン
 3 files changed, 120 insertions(+)
 create mode 100644 css/style.css
 create mode 100644 index.html
 create mode 100644 images/profile.jpg

commit f1e2d3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9 (HEAD -> master)
Author: あなたの名前 <あなたのメール>
Date:   Wed Apr 24 14:25:00 2023 +0900

    初期バージョン
```

\</details>

#### 問題 7-3: GitHub との連携 ⭐

ローカルリポジトリーを GitHub にアップロードしてください：

1. GitHub で新しいリポジトリを作成
2. ローカルリポジトリーとリモートリポジトリーを連携
3. ローカルの変更を GitHub にプッシュ
4. GitHub でリポジトリーを確認

\<details> \<summary>ヒント\</summary>

1. GitHub にログインして新しいリポジトリーを作成 (リポジトリー名：「my-portfolio」)
2. ローカルからリモートへ連携：

```bash
# リモートリポジトリーを追加
git remote add origin https://github.com/あなたのユーザー名/my-portfolio.git

# 変更をプッシュ
git push -u origin master
```

3. GitHub のウェブサイトにアクセスして、ファイルがアップロードされていることを確認

\</details>

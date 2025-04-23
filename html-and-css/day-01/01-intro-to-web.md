# 1. Web の仕組み

### 1.1 Web とは何か

**Web (World Wide Web)** は、インターネット上で情報を共有するためのシステムです。私たちが日常的に使う Web サイトや Web アプリケーションは、このシステム上で動作しています。

Web が誕生したのは 1989 年、欧州原子核研究機構 (CERN) のティム・バーナーズ=リーによって提案されました。当初は科学者間の情報共有が目的でしたが、今では生活のあらゆる面で欠かせないものになっています。

> 📝 **ポイント**: Web とインターネットはよく混同されますが、別物です。インターネットはコンピューターをつなぐ物理的なネットワークで、Web はそのネットワークで HTTP を使って情報を共有するシステムの一つです。

### 1.2 Web の基本的な仕組み

#### 1.2.1 クライアント・サーバーモデル

Web は 「クライアント・サーバーモデル」 という構造で動作しています。このモデルの主な登場人物は以下の 2つです：

<table><thead><tr><th width="132.078125">役割</th><th width="223.49066162109375">説明</th><th>例</th></tr></thead><tbody><tr><td><strong>クライアント</strong></td><td>情報を要求し表示する側</td><td>ブラウザー (Chrome, Firefox, Safari, Edge など)</td></tr><tr><td><strong>サーバー</strong></td><td>情報を保存し提供する側</td><td>Webサーバー (Apache, Nginx, IIS など)</td></tr></tbody></table>

このモデルでの情報のやり取りは以下のステップで行われます。

1. **リクエスト**: クライアント (ブラウザー) がサーバーに情報を要求
2. **処理**: サーバーがリクエストを処理
3. **レスポンス**: サーバーがクライアントに情報 (HTML, CSS, JavaScript, 画像など) を送信

\<div style="text-align: center;"> \<img src="https://example.com/client-server-model.png" alt="クライアント・サーバーモデル" width="600"> \</div>

> 📝 **ポイント**: このリクエスト・レスポンスの仕組みは、Web の基本的な動作原理です。Web サイトを表示するたびに、このプロセスが行われています。

#### 1.2.2 URL (Uniform Resource Locator) の構造

URL は、Web 上のリソース (ページ、画像、ファイルなど) の場所を示す住所のようなものです。

```
https://www.example.com:443/path/to/resource.html?name=value#section
```

<table><thead><tr><th width="122.809326171875">部分</th><th width="175.09375">例</th><th>説明</th></tr></thead><tbody><tr><td>プロトコル</td><td><code>https://</code></td><td>通信方法を指定 (HTTP, HTTPS, FTP など)</td></tr><tr><td>ドメイン名</td><td><code>www.example.com</code></td><td>サーバーの名前 (インターネット上の住所)</td></tr><tr><td>ポート番号</td><td><code>:443</code></td><td>サーバー内の特定のサービスを示す番号 (省略可能)</td></tr><tr><td>パス</td><td><code>/path/to/resource.html</code></td><td>サーバー上のリソースの場所</td></tr><tr><td>クエリー文字列</td><td><code>?name=value</code></td><td>追加のパラメーター (省略可能)</td></tr><tr><td>フラグメント</td><td><code>#section</code></td><td>ページ内の特定部分を指定 (省略可能)</td></tr></tbody></table>

> 📝 **ポイント**: URL は、Web 上のすべてのリソースに一意のアドレスを提供します。正確に入力することで、世界中のどのリソースにもアクセスできます。

### 1.3 Web を構成する 3つの要素

Web サイトは主に 3つの技術要素で構成されています。

#### 1.3.1 HTML (HyperText Markup Language)

HTML は Web ページの **構造** を定義する言語です。テキスト、画像、リンクなどのコンテンツをどのように配置するかを指定します。

```html
<!DOCTYPE html>
<html>
<head>
    <title>ページのタイトル</title>
</head>
<body>
    <h1>見出し</h1>
    <p>これは段落です。</p>
    <img src="image.jpg" alt="画像の説明">
</body>
</html>
```

#### 1.3.2 CSS (Cascading Style Sheets)

CSS は Web ページの **スタイル** を定義する言語です。色、フォント、レイアウトなど、ページの視覚的な側面を制御します。

```css
body {
    font-family: Arial, sans-serif;
    background-color: #f0f0f0;
}

h1 {
    color: navy;
    text-align: center;
}
```

#### 1.3.3 JavaScript

JavaScript は Web ページに **動的な機能** を追加するプログラミング言語です。ユーザーのアクションに反応したり、データを動的に表示したりする機能を実装できます。

```javascript
document.getElementById("button").addEventListener("click", function() {
    alert("ボタンがクリックされました！");
});
```

これら 3つの要素の関係を以下の例で考えてみましょう。

* **HTML**: 家の骨組みや部屋の配置を決める 「~~設計図~~」
* **CSS**: 壁の色や家具のスタイルを決める 「インテリアデザイン」
* **JavaScript**: 電気、水道、ドアの開閉などの 「機能」 を提供する

> 📝 **ポイント**: 良い Web サイトを作るには、これら 3つの要素をバランスよく組み合わせることが重要です。HTML → CSS → JavaScript の順に学ぶと理解しやすいでしょう。

### 1.4 ブラウザーの役割

ブラウザーは Web 上のコンテンツを表示するためのソフトウェアです。主な役割は以下の通りです。

1. **リクエストの送信**: ユーザーが入力した URL に基づいてサーバーにリクエストを送信
2. **コンテンツの受信**: サーバーから HTML、CSS、JavaScript、画像などのコンテンツを受信
3. **解析とレンダリング**: HTML を解析して文書構造 (DOM) を構築
4. **スタイルの適用**: CSS を解析してスタイルを適用
5. **スクリプトの実行**: JavaScript を実行して動的な機能を提供
6. **表示**: 最終的なページをユーザーに表示

#### ブラウザーエンジン

ブラウザーエンジンはブラウザーの中核部分で、HTML と CSS Tの解析・レンダリングを担当します。主なブラウザーエンジンには以下のようなものがあります。

| ブラウザー         | エンジン   | 開発元       |
| ------------- | ------ | --------- |
| Google Chrome | Blink  | Google    |
| Safari        | WebKit | Apple     |
| Firefox       | Gecko  | Mozilla   |
| Edge          | Blink  | Microsoft |

> 📝 **ポイント**: 各ブラウザーは同じ HTML を少し異なる方法で解釈することがあります。そのため、複数のブラウザーでテストすることが重要です。

### 1.5 HTML ファイルの表示方法

HTML ファイルは、以下の方法でブラウザーに表示できます。

1. **直接開く**: HTML ファイルをダブルクリックするか、ブラウザーにドラッグ ＆ ドロップする
2. **サーバー経由**: Web サーバーを通じてファイルを提供する
3. **Live Server**: VSCode の拡張機能を使ってローカルサーバーで表示する

HTML ファイルを開くと、以下のプロセスが行われます。

```
HTMLファイル読み込み → 解析 → DOMツリー構築 → レンダリング → 表示
```

> 📝 **ポイント**: 開発中は 「Live Server」 などの拡張機能を使うと、ファイルを保存するたびに自動的にブラウザーが更新されるため便利です。

### 1.6 VS Codeの基本操作

Visual Studio Code (VS Code) は、Microsoft が開発した高機能かつ軽量のコードエディターです。Web 開発に必要な多くの機能が揃っているため、初心者からプロまで幅広く使われています。

#### 1.6.1 基本的なインターフェース

VS Code の画面は以下のような構成になっています。

* **アクティビティバー**: 左端の縦長のバー (ファイル探索、検索、拡張機能などの機能に素早くアクセス)
* **サイドバー**: アクティビティバーで選択した機能に関連する情報を表示
* **エディターグループ**: コードを編集する中央の領域
* **ステータスバー**: 下部の情報バー (現在の状態や設定に関する情報を表示)

#### 1.6.2 ファイル操作

<table><thead><tr><th width="153.71246337890625">操作</th><th width="310.9312744140625">方法</th><th>ショートカット</th></tr></thead><tbody><tr><td>新規ファイル作成</td><td>メニュー 「ファイル > 新規ファイル」</td><td><kbd>Ctrl</kbd> + <kbd>N</kbd></td></tr><tr><td>ファイル保存</td><td>メニュー 「ファイル > 保存」</td><td><kbd>Ctrl</kbd> + <kbd>S</kbd></td></tr><tr><td>フォルダを開く</td><td>メニュー 「ファイル > フォルダを開く」</td><td>Ctrl + K Ctrl + O</td></tr><tr><td>ファイル切り替え</td><td>サイドバーのエクスプローラーでクリック</td><td><kbd>Ctrl</kbd> + <kbd>P</kbd> で検索</td></tr></tbody></table>

#### 1.6.3 便利な編集機能

VS Code には、コーディングを効率化するための機能が多数あります。

1.  **インテリセンス (コード補完)** : 入力中に候補を表示

    ```
    div + Tab → <div></div>
    ```
2. **マルチカーソル編集**: 複数の場所を同時に編集
   * <kbd>Alt</kbd> + <kbd>クリック</kbd> で複数箇所にカーソル
   * <kbd>Ctrl</kbd> + <kbd>D</kbd> で同じ単語を次々に選択
3.  **Emmet**: HTML/CSSを短いコードで展開

    ```
    ul>li*3 + Tab → <ul><li></li><li></li><li></li></ul>
    ```
4. **コードフォーマット**: コードを自動的に整形
   * 右クリック > ドキュメントのフォーマット
   * <kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>F</kbd>

> 📝 **ポイント**: VS Code のショートカットを覚えると作業効率が大幅に向上します。頻繁に使う操作のショートカットから少しずつ覚えていきましょう。

### 1.7 Web 開発に役立つ VS Code 拡張機能

VS Code は拡張機能をインストールすることで、機能を追加できます。Web 開発に特に役立つ拡張機能は以下の通りです。

#### 1.7.1 Live Server

HTML ファイルをローカルサーバーで実行し、変更を保存するたびに自動的にブラウザーを更新します。

**インストール方法**:

1. 拡張機能アイコンをクリック (左側メニュー)
2. 「Live Server」 を検索
3. インストールボタンをクリック

**使用方法**:

1. HTML ファイルを開く
2. エディター右下の 「Go Live」 ボタンをクリック、または
3. HTML ファイルを右クリック → 「Live Serverで開く」 を選択

#### 1.7.2 HTML CSS Support

HTML、CSS 記述時の補完機能を強化します。

#### 1.7.3 Auto Rename Tag

開始タグを編集すると、対応する終了タグも自動的に更新されます。

#### 1.7.4 Prettier - Code formatter

コードを自動的に整形し、一貫したスタイルを維持します。

> 📝 **ポイント**: 拡張機能は必要に応じて追加しましょう。最初から多くをインストールするよりも、開発を進めながら必要な機能を追加していく方が良いでしょう。

### 1.8 プロジェクトフォルダの構成

整理されたフォルダ構造は、プロジェクトが大きくなった際に非常に重要になります。シンプルなWebサイト用の基本的なフォルダ構造は以下のようになります。

```
my-website/
│
├─ index.html          # トップページ
├─ about.html          # 自己紹介ページ
├─ contact.html        # お問い合わせページ
│
├─ css/                # CSSファイル用フォルダ
│   └─ style.css       # メインのスタイルシート
│
├─ js/                 # JavaScriptファイル用フォルダ
│   └─ script.js       # メインのスクリプトファイル
│
└─ images/             # 画像ファイル用フォルダ
    ├─ logo.png
    └─ background.jpg
```

> 📝 **ポイント**: 初めから整理されたフォルダ構造を使うことで、ファイルの管理が容易になります。小さなプロジェクトでも良い習慣を身につけておきましょう。



***

### 演習問題

#### 問題 1-1: VS Codeでプロジェクトを作成する

新しい Web サイトプロジェクトを以下の手順で作成してください。

1. デスクトップに 「my-portfolio」 というフォルダを作成
2. VS Code でそのフォルダーを開く
3. 必要なサブフォルダー (css, js, images) を作成
4. index.html ファイルをルートフォルダーに作成
5. style.css ファイルを css フォルダーに作成

<details>

<summary>ヒント</summary>

1. デスクトップに新しいフォルダを作成
2. VS Codeを開き、「ファイル > フォルダを開く」から作成したフォルダを選択
3. エクスプローラーパネル（左側）の「my-portfolio」を右クリックし、「新しいフォルダ」を選択してサブフォルダを作成
4. 同様に右クリックから「新しいファイル」を選択してファイルを作成

</details>

#### 問題 1-2: HTML の基本構造を作成する

作成した 「index.html」 に、以下を含む HTML の基本構造を記述してください。

1. HTML の DocType 宣言
2. html 要素 (lang 属性を "ja" に設定)
3. head 要素 (title, meta charset, viewportの設定を含む)
4. body 要素

<details>

<summary>ヒント</summary>

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>私のポートフォリオ</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>
    <!-- ここにコンテンツが入ります -->
</body>
</html>
```

VS Codeでは、「!」 と入力して <kbd>Tab</kbd> キーを押すと、この基本構造が自動的に挿入されます。その後、lang属性やtitleを編集してください。

</details>

#### 問題 1-3: Live Server を使って HTML ファイルを表示する ⭐

1. Live Server 拡張機能をインストール
2. index.html を Live Server で開く
3. ブラウザで表示されることを確認
4. テキストを追加して保存し、ブラウザが自動的に更新されるか確認

<details>

<summary>ヒント</summary>

1. VS Code の拡張機能タブ (左側メニュー) から Live Server を検索してインストール
2. index.html を開いた状態で、画面右下の 「Go Live」 ボタンをクリック  (または、index.htm lを右クリックして 「Live Serverで開く」 を選択)
3. ブラウザーが自動的に開き、ページが表示される
4.  VS Code で index.html を編集し、body に内容を追加して保存

    ```html
    <body>    <h1>私のポートフォリオサイト</h1>    <p>これはLive Serverのテストです。</p></body>
    ```
5. 保存すると、ブラウザが自動的に更新され、変更が反映される

</details>

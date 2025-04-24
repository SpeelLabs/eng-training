# 9. 一般的なレイアウトパターン

### 9.1 Webサイトの基本構造

現代の Web サイトには、共通して使用される基本的なレイアウト構造があります。これらのパターンを理解することで、効率的にサイトを制作できます。

#### 9.1.1 共通の構成要素

多くのWebサイトは以下の構成要素を持っています。

* **ヘッダー**: サイト名、ロゴ、メインナビゲーションなどを含む上部セクション
* **メインコンテンツ**: ページの主要な内容を表示するエリア
* **サイドバー**: 補足情報、サブナビゲーションなどを表示するエリア (オプション)
* **フッター**: サイトマップ、著作権情報、連絡先などを含む下部セクション

> 📝 **ポイント**: セマンティックな HTML タグ ( `<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>`など) を使用すると、ページの構造が明確になり、アクセシビリティも向上します。

### 9.2 単一カラムレイアウト

最も基本的で広く使われているレイアウトパターンである 「ヘッダー・コンテンツ・フッター」 構造です。

#### 9.2.1 ヘッダー・コンテンツ・フッター構造

```html
<header>
    <div class="container">
        <h1>サイトタイトル</h1>
        <nav>
            <ul>
                <li><a href="#">ホーム</a></li>
                <li><a href="#">サービス</a></li>
                <li><a href="#">お問い合わせ</a></li>
            </ul>
        </nav>
    </div>
</header>

<main>
    <div class="container">
        <h2>メインコンテンツ</h2>
        <p>ここにメインコンテンツが入ります。</p>
    </div>
</main>

<footer>
    <div class="container">
        <p>&copy; 2023 サンプルサイト</p>
    </div>
</footer>
```

```css
.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 15px;
}

header {
    background-color: #f8f9fa;
    padding: 20px 0;
}

header nav ul {
    display: flex;
    list-style: none;
    padding: 0;
    margin: 0;
}

header nav li {
    margin-right: 20px;
}

main {
    padding: 40px 0;
}

footer {
    background-color: #f8f9fa;
    padding: 20px 0;
    text-align: center;
}
```

<div align="left"><img src="https://claude.ai/.gitbook/assets/layout-1.png" alt="ヘッダー・コンテンツ・フッターレイアウト"></div>

#### 9.2.2 使用場面

このパターンは以下のような場合に適しています。

* シンプルな情報提供サイト
* 単一カラムでのブログ記事
* ランディングページ
* モバイルファーストのレスポンシブデザイン

### 9.3 2 カラムレイアウト

ブログやニュースサイトなどでよく見られる 「メインコンテンツ + サイドバー」 のレイアウトです。

#### 9.3.1 メインコンテンツ + サイドバー構造

```html
<header>
    <div class="container">
        <h1>サイトタイトル</h1>
        <nav>
            <ul>
                <li><a href="#">ホーム</a></li>
                <li><a href="#">カテゴリー</a></li>
                <li><a href="#">お問い合わせ</a></li>
            </ul>
        </nav>
    </div>
</header>

<div class="container">
    <div class="two-column-layout">
        <main>
            <h2>メインコンテンツ</h2>
            <p>ここにメインコンテンツが入ります。通常は記事や主要なコンテンツが配置されます。</p>
        </main>

        <aside>
            <div class="sidebar-widget">
                <h3>カテゴリー</h3>
                <ul>
                    <li><a href="#">カテゴリー1</a></li>
                    <li><a href="#">カテゴリー2</a></li>
                    <li><a href="#">カテゴリー3</a></li>
                </ul>
            </div>
            <div class="sidebar-widget">
                <h3>最近の投稿</h3>
                <ul>
                    <li><a href="#">記事タイトル1</a></li>
                    <li><a href="#">記事タイトル2</a></li>
                    <li><a href="#">記事タイトル3</a></li>
                </ul>
            </div>
        </aside>
    </div>
</div>

<footer>
    <div class="container">
        <p>&copy; 2023 サンプルサイト</p>
    </div>
</footer>
```

```css
.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 15px;
}

header {
    background-color: #f8f9fa;
    padding: 20px 0;
}

header nav ul {
    display: flex;
    list-style: none;
    padding: 0;
    margin: 0;
}

header nav li {
    margin-right: 20px;
}

.two-column-layout {
    display: flex;
    flex-wrap: wrap;
    gap: 40px;
    padding: 40px 0;
}

main {
    flex: 3;  /* メインコンテンツがサイドバーの3倍の幅を取る */
    min-width: 300px;
}

aside {
    flex: 1;  /* サイドバーがメインコンテンツの1/3の幅を取る */
    min-width: 250px;
}

.sidebar-widget {
    margin-bottom: 30px;
    padding: 20px;
    background-color: #f8f9fa;
    border-radius: 5px;
}

footer {
    background-color: #f8f9fa;
    padding: 20px 0;
    text-align: center;
}

/* レスポンシブ対応 */
@media (max-width: 768px) {
    .two-column-layout {
        flex-direction: column;
    }
}
```

<div align="left"><img src="https://claude.ai/.gitbook/assets/layout-2.png" alt="2カラムレイアウト"></div>

#### 9.3.2 使用場面

このパターンは以下のような場合に適しています。

* ブログサイト
* ニュースサイト
* ドキュメントサイト
* ナビゲーションや補足情報が多いサイト

### 9.4 グリッドレイアウト

複数のコンテンツを均等に配置するレイアウトです。商品一覧やポートフォリオサイトで一般的です。

#### 9.4.1 カードグリッド構造

```html
<header>
    <div class="container">
        <h1>ポートフォリオサイト</h1>
        <nav>
            <ul>
                <li><a href="#">ホーム</a></li>
                <li><a href="#">作品</a></li>
                <li><a href="#">お問い合わせ</a></li>
            </ul>
        </nav>
    </div>
</header>

<main>
    <div class="container">
        <h2>作品一覧</h2>

        <div class="grid-layout">
            <article class="card">
                <img src="project1.jpg" alt="プロジェクト1">
                <h3>プロジェクト1</h3>
                <p>プロジェクトの説明文がここに入ります。</p>
            </article>
            
            <article class="card">
                <img src="project2.jpg" alt="プロジェクト2">
                <h3>プロジェクト2</h3>
                <p>プロジェクトの説明文がここに入ります。</p>
            </article>
            
            <article class="card">
                <img src="project3.jpg" alt="プロジェクト3">
                <h3>プロジェクト3</h3>
                <p>プロジェクトの説明文がここに入ります。</p>
            </article>
            
            <article class="card">
                <img src="project4.jpg" alt="プロジェクト4">
                <h3>プロジェクト4</h3>
                <p>プロジェクトの説明文がここに入ります。</p>
            </article>
        </div>
    </div>
</main>

<footer>
    <div class="container">
        <p>&copy; 2023 ポートフォリオサイト</p>
    </div>
</footer>
```

```css
.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 15px;
}

header {
    background-color: #f8f9fa;
    padding: 20px 0;
}

header nav ul {
    display: flex;
    list-style: none;
    padding: 0;
    margin: 0;
}

header nav li {
    margin-right: 20px;
}

main {
    padding: 40px 0;
}

.grid-layout {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 30px;
    margin-top: 30px;
}

.card {
    border: 1px solid #e0e0e0;
    border-radius: 5px;
    overflow: hidden;
    transition: transform 0.3s ease;
}

.card:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 20px rgba(0,0,0,0.1);
}

.card img {
    width: 100%;
    height: 200px;
    object-fit: cover;
}

.card h3, .card p {
    padding: 0 15px;
}

footer {
    background-color: #f8f9fa;
    padding: 20px 0;
    text-align: center;
}

/* レスポンシブ対応 */
@media (max-width: 768px) {
    .grid-layout {
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    }
}
```

<div align="left"><img src="https://claude.ai/.gitbook/assets/layout-3.png" alt="グリッドレイアウト"></div>

#### 9.4.2 使用場面

このパターンは以下のような場合に適しています。

* ポートフォリオサイト
* 商品一覧ページ
* ギャラリーサイト
* ブログの記事一覧

> 📝 **ポイント**: グリッドレイアウトは、CSS グリッドを使うと簡単に実装できます。`grid-template-columns` と `gap` の2つのプロパティだけで、レスポンシブな格子状のレイアウトが作れます。

### 9.5 モバイルファーストレイアウト

現代の Web 開発では、モバイル端末を優先して設計する 「モバイルファースト」 アプローチが重要です。

#### 9.5.1 基本的なモバイルファーストの考え方

1. **小さい画面から設計を始める**
2. **メディアクエリを使って画面が大きくなる場合のスタイルを追加**
3. **重要なコンテンツを優先的に配置**

```css
/* モバイル向けの基本スタイル（すべての画面サイズに適用） */
.container {
    width: 100%;
    padding: 0 15px;
}

.nav-items {
    display: flex;
    flex-direction: column; /* モバイルでは縦並び */
}

/* タブレット以上のスタイル */
@media (min-width: 768px) {
    .container {
        max-width: 720px;
        margin: 0 auto;
    }
    
    .nav-items {
        flex-direction: row; /* タブレット以上では横並び */
    }
}

/* デスクトップのスタイル */
@media (min-width: 1024px) {
    .container {
        max-width: 960px;
    }
}
```

#### 9.5.2 レスポンシブナビゲーションの例

モバイルではハンバーガーメニュー、大きい画面では横並びのナビゲーションという一般的なパターンです。

```html
<header>
    <div class="container">
        <div class="header-content">
            <h1>サイトタイトル</h1>
            <button class="menu-toggle">メニュー</button>
        </div>
        <nav id="main-nav">
            <ul>
                <li><a href="#">ホーム</a></li>
                <li><a href="#">サービス</a></li>
                <li><a href="#">ブログ</a></li>
                <li><a href="#">お問い合わせ</a></li>
            </ul>
        </nav>
    </div>
</header>
```

```css
/* モバイル向けのスタイル */
.header-content {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.menu-toggle {
    display: block; /* モバイルではハンバーガーメニューを表示 */
}

nav ul {
    display: none; /* モバイルではデフォルトで非表示 */
    list-style: none;
    padding: 0;
    margin: 0;
}

nav.open ul {
    display: block; /* メニュートグルがクリックされたら表示 */
}

/* タブレット以上のスタイル */
@media (min-width: 768px) {
    .menu-toggle {
        display: none; /* タブレット以上ではボタンを非表示 */
    }
    
    nav ul {
        display: flex; /* タブレット以上ではメニューを常に表示 */
    }
    
    nav li {
        margin-right: 20px;
    }
}
```

> 📝 **ポイント**: モバイルファーストアプローチでは、パフォーマンスが向上し、モバイルユーザーの体験が改善されます。現代の Web サイト開発において、モバイルファーストは標準的なアプローチとなっています。

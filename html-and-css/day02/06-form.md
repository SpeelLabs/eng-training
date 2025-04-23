# 6. フォーム要素の基本

### 6.1 HTMLフォームとは

**フォーム（form）** は、ユーザーが情報を入力してサーバーに送信するための仕組みです。お問い合わせ、ログイン、アンケート、検索など、Webサイトの対話機能の多くはフォームを使用しています。

#### 6.1.1 基本的なフォーム構造

```html
<form action="送信先URL" method="送信方法">
    <!-- フォーム要素がここに入ります -->
    <input type="text" name="username">
    <input type="submit" value="送信">
</form>
```

**主な属性**:

* `action`: フォームデータの送信先URL
* `method`: データの送信方法（`get` または `post`）

> 📝 **ポイント**: 初学者のうちは `action` や `method` の詳細を理解する必要はありません。フォームのレイアウトと各要素の使い方を中心に学びましょう。

### 6.2 テキスト入力要素

#### 6.2.1 一行テキスト入力（input type="text"）

最も基本的な入力要素で、一行のテキストを入力できます。

```html
<label for="username">ユーザー名:</label>
<input type="text" id="username" name="username" placeholder="例: yamada_taro">
```

**主な属性**:

* `type`: 入力タイプ（"text"は一行テキスト）
* `id`: ラベルとの関連付けに使用
* `name`: 送信時のデータの名前
* `placeholder`: 入力例や説明（入力前に薄く表示）
* `value`: 初期値
* `required`: 必須項目に設定

#### 6.2.2 パスワード入力（input type="password"）

パスワードなど、表示を隠したい情報の入力に使用します。

```html
<label for="password">パスワード:</label>
<input type="password" id="password" name="password" required>
```

#### 6.2.3 メールアドレス入力（input type="email"）

メールアドレス専用の入力欄です。自動的に形式をチェックします。

```html
<label for="email">メールアドレス:</label>
<input type="email" id="email" name="email" placeholder="例: example@example.com">
```

#### 6.2.4 数値入力（input type="number"）

数値だけを入力できる欄です。上下ボタンが表示されます。

```html
<label for="age">年齢:</label>
<input type="number" id="age" name="age" min="0" max="120">
```

#### 6.2.5 複数行テキスト入力（textarea）

複数行のテキストを入力できる要素です。

```html
<label for="message">メッセージ:</label>
<textarea id="message" name="message" rows="5" cols="30"></textarea>
```

**主な属性**:

* `rows`: 表示する行数
* `cols`: 表示する列数

> 📝 **ポイント**: `<label>` 要素は入力フィールドの説明です。`for` 属性と入力要素の `id` を一致させることで、ラベルをクリックしても入力フィールドにフォーカスが移動します。

### 6.3 選択要素

#### 6.3.1 ラジオボタン（input type="radio"）

複数の選択肢から1つだけを選ぶ場合に使用します。

```html
<p>性別:</p>
<input type="radio" id="male" name="gender" value="male">
<label for="male">男性</label>
<input type="radio" id="female" name="gender" value="female">
<label for="female">女性</label>
<input type="radio" id="other" name="gender" value="other">
<label for="other">その他</label>
```

**重要**: 同じグループのラジオボタンには同じ `name` を設定します。

#### 6.3.2 チェックボックス（input type="checkbox"）

複数の選択肢から複数選べる場合に使用します。

```html
<p>趣味:</p>
<input type="checkbox" id="reading" name="hobby" value="reading">
<label for="reading">読書</label>
<input type="checkbox" id="sports" name="hobby" value="sports">
<label for="sports">スポーツ</label>
<input type="checkbox" id="cooking" name="hobby" value="cooking">
<label for="cooking">料理</label>
```

#### 6.3.3 ドロップダウンリスト（select）

多くの選択肢から1つまたは複数を選ぶ場合に使用します。

```html
<label for="country">国:</label>
<select id="country" name="country">
    <option value="">選択してください</option>
    <option value="jp">日本</option>
    <option value="us">アメリカ</option>
    <option value="uk">イギリス</option>
    <option value="fr">フランス</option>
</select>
```

**複数選択を許可する場合**:

```html
<select id="fruits" name="fruits" multiple>
    <option value="apple">りんご</option>
    <option value="orange">オレンジ</option>
    <option value="grape">ぶどう</option>
</select>
```

### 6.4 その他の入力要素

#### 6.4.1 日付と時間の入力（input type="date", "time"）

日付や時間を専用のピッカーで入力できます。

```html
<label for="birthdate">生年月日:</label>
<input type="date" id="birthdate" name="birthdate">

<label for="meeting-time">面談時間:</label>
<input type="time" id="meeting-time" name="meeting-time">
```

#### 6.4.2 ファイル選択（input type="file"）

ファイルをアップロードするための要素です。

```html
<label for="photo">写真:</label>
<input type="file" id="photo" name="photo" accept="image/*">
```

**accept属性**: 受け入れるファイルの種類を指定（`image/*` は全ての画像ファイル）

#### 6.4.3 非表示フィールド（input type="hidden"）

ユーザーには見えないが、送信時に含めたい情報を保持します。

```html
<input type="hidden" name="user_id" value="12345">
```

#### 6.4.4 送信ボタン（input type="submit"）

フォームをサーバーに送信するボタンです。

```html
<input type="submit" value="送信する">
```

ボタン要素でも同様の機能を実装できます:

```html
<button type="submit">送信する</button>
```

### 6.5 フォームのスタイリング

CSSを使って、フォーム要素をカスタマイズできます。

#### 6.5.1 基本的なフォームスタイリング

```css
/* 入力フィールドのスタイル */
input[type="text"], input[type="email"], input[type="password"], textarea, select {
    width: 100%;
    padding: 8px 12px;
    margin-bottom: 15px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 16px;
}

/* ラベルのスタイル */
label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
}

/* 送信ボタンのスタイル */
button[type="submit"], input[type="submit"] {
    background-color: #4CAF50;
    color: white;
    padding: 10px 15px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 16px;
}

button[type="submit"]:hover, input[type="submit"]:hover {
    background-color: #45a049;
}
```

#### 6.5.2 フォームレイアウト ⭐

フレックスボックスを使って、ラベルと入力欄を横並びにする例:

```css
.form-row {
    display: flex;
    margin-bottom: 15px;
}

.form-row label {
    flex: 1;
    margin-right: 10px;
}

.form-row input, .form-row textarea, .form-row select {
    flex: 2;
}
```

```html
<div class="form-row">
    <label for="name">名前:</label>
    <input type="text" id="name" name="name">
</div>
```

### 6.6 フォームの検証 ⭐

HTML5のバリデーション機能を使って、入力値の検証ができます。

#### 6.6.1 基本的なバリデーション属性

```html
<!-- 必須フィールド -->
<input type="text" name="username" required>

<!-- 最小・最大文字数 -->
<input type="password" name="password" minlength="8" maxlength="20">

<!-- パターン（正規表現） -->
<input type="text" name="code" pattern="[A-Za-z0-9]{6}" title="英数字6文字">
```

#### 6.6.2 カスタムエラーメッセージ

属性 `title` に指定した文字列がエラーメッセージとして表示されます。

```html
<input type="email" name="email" required title="有効なメールアドレスを入力してください">
```

> 📝 **ポイント**: HTML5のバリデーション機能は手軽ですが、本格的なアプリケーションではJavaScriptを使ったより詳細な検証も行います。

### 6.7 お問い合わせフォームの例

以下は基本的なお問い合わせフォームの例です。

```html
<form id="contact-form">
  <div class="form-group">
    <label for="name">お名前:</label>
    <input type="text" id="name" name="name" required>
  </div>
  
  <div class="form-group">
    <label for="email">メールアドレス:</label>
    <input type="email" id="email" name="email" required>
  </div>
  
  <div class="form-group">
    <label for="subject">件名:</label>
    <select id="subject" name="subject">
      <option value="general">一般的なお問い合わせ</option>
      <option value="support">サポート</option>
      <option value="feedback">フィードバック</option>
    </select>
  </div>
  
  <div class="form-group">
    <label for="message">メッセージ:</label>
    <textarea id="message" name="message" rows="5" required></textarea>
  </div>
  
  <button type="submit">送信する</button>
</form>
```

```css
#contact-form {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input, select, textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

textarea {
  resize: vertical;
}

button[type="submit"] {
  background-color: #4CAF50;
  color: white;
  padding: 12px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  width: 100%;
}

button[type="submit"]:hover {
  background-color: #45a049;
}
```

### 演習問題

#### 問題 6-1: 基本的なフォーム作成

以下の項目を含む簡単なお問い合わせフォームを作成してください:

1. 名前（テキスト入力）
2. メールアドレス（メール入力）
3. メッセージ（テキストエリア）
4. 送信ボタン

\<details> \<summary>ヒント\</summary>

```html
<form id="contact-form">
  <div>
    <label for="name">お名前:</label>
    <input type="text" id="name" name="name" required>
  </div>
  
  <div>
    <label for="email">メールアドレス:</label>
    <input type="email" id="email" name="email" required>
  </div>
  
  <div>
    <label for="message">メッセージ:</label>
    <textarea id="message" name="message" rows="5" required></textarea>
  </div>
  
  <div>
    <button type="submit">送信する</button>
  </div>
</form>
```

\</details>

#### 問題 6-2: フォームのスタイリング

問題6-1で作成したフォームにCSSを適用して、以下の要件を満たすようにしてください:

1. フォーム全体の最大幅を設定し、中央に配置
2. 入力フィールドの間に適切な余白を設定
3. ラベルをテキストフィールドの上に配置
4. 送信ボタンのスタイルをカスタマイズ

\<details> \<summary>ヒント\</summary>

```css
#contact-form {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
}

#contact-form div {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
}

input, textarea {
  width: 100%;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

button {
  background-color: #3498db;
  color: white;
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #2980b9;
}
```

\</details>

#### 問題 6-3: 追加のフォーム要素 ⭐

問題6-1のフォームに以下の要素を追加してください:

1. お問い合わせの種類（ラジオボタン：「質問」「感想」「その他」）
2. 返信の希望（チェックボックス：「メールでの返信を希望する」）
3. 連絡可能な時間帯（セレクトボックス：「午前」「午後」「夜間」）

\<details> \<summary>ヒント\</summary>

```html
<form id="contact-form">
  <!-- 省略（既存のフィールド） -->
  
  <div>
    <p>お問い合わせの種類:</p>
    <input type="radio" id="question" name="type" value="question">
    <label for="question">質問</label>
    <input type="radio" id="feedback" name="type" value="feedback">
    <label for="feedback">感想</label>
    <input type="radio" id="other" name="type" value="other">
    <label for="other">その他</label>
  </div>
  
  <div>
    <input type="checkbox" id="reply" name="reply">
    <label for="reply">メールでの返信を希望する</label>
  </div>
  
  <div>
    <label for="time">連絡可能な時間帯:</label>
    <select id="time" name="time">
      <option value="">選択してください</option>
      <option value="morning">午前（9時-12時）</option>
      <option value="afternoon">午後（13時-17時）</option>
      <option value="evening">夜間（18時-21時）</option>
    </select>
  </div>
  
  <!-- 省略（送信ボタン） -->
</form>
```

注: ラジオボタンとチェックボックスの場合、インラインでラベルを使用する際は、通常 `<label>` が入力要素の後に来ます。また、CSSでこれらの配置を調整することもできます。

\</details>

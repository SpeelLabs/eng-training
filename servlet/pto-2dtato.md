---
description: >-
  パート 2 では、パート 1 で作成したインメモリーベースのブログアプリケーションを、MySQL
  データベースを使用した永続化対応のアプリケーションに発展させます。この段階では、本格的な CRUD 操作、DAO
  パターンの実装、コメント機能、検索機能などを追加し、実用的な Web アプリケーションを構築します。
---

# パート 2：データ永続化と応用機能

### 目標

このパートを終了すると、以下のことができるようになります。

<table><thead><tr><th width="199.9090576171875">技術スキル</th><th>説明</th></tr></thead><tbody><tr><td>データベース連携</td><td>JDBC を使用した MySQL データベースとの接続と操作</td></tr><tr><td>DAO パターン実装</td><td>データアクセス層の適切な分離と実装</td></tr><tr><td>CRUD 操作の完全実装</td><td>作成・読取・更新・削除の全機能実装</td></tr><tr><td>トランザクション処理</td><td>データベース操作の整合性確保</td></tr><tr><td>検索機能とページング</td><td>効率的なデータ検索と表示</td></tr></tbody></table>

### 基本モジュール構成

パート 2 は以下の 3つの基本モジュールで構成されています。

#### モジュール 4: データベース連携

* MySQL データベースの設定と接続
* JDBC による基本的なデータベース操作
* 投稿データのデータベース永続化
* データベース設計とテーブル作成
* PreparedStatement による安全なSQL実行

#### モジュール 5: DAO パターンと投稿管理

* DAO（Data Access Object）パターンの実装
* 投稿データの CRUD 操作完全実装
* Service レイヤーの導入
* トランザクション処理の基礎
* エラーハンドリングとログ出力

#### モジュール 6: コメントと検索機能

* コメント機能の実装
* 投稿検索機能の実装
* ページング処理による大量データ対応
* カテゴリ機能の追加
* パフォーマンス最適化

### ~~発展モジュール~~

~~パート 2 の基本を習得した後、以下の発展モジュールに取り組むことができます。~~

<table><thead><tr><th width="198.0909423828125">発展モジュール</th><th>主な学習内容</th></tr></thead><tbody><tr><td><strong>2-A: 高度なデータ管理</strong></td><td>• 複数テーブルの関連管理&#x3C;br>• JOIN を使用した複雑なクエリ&#x3C;br>• インデックスとパフォーマンス最適化&#x3C;br>• データベース設計のベストプラクティス</td></tr><tr><td><strong>2-B: セキュリティ強化</strong></td><td>• SQL インジェクション対策の詳細&#x3C;br>• 入力値検証の強化&#x3C;br>• CSRF 対策の実装&#x3C;br>• セッション管理のセキュリティ</td></tr></tbody></table>

### 開発の流れ

パート 2 の開発フローは以下のようになります。

```
モジュール 4
[パート1アプリ] → [MySQL DB設計] → [JDBC接続] → [基本CRUD] → [永続化対応]

モジュール 5  
[基本CRUD] → [DAO層分離] → [Service層追加] → [トランザクション] → [エラー処理]

モジュール 6
[基本機能] → [コメント機能] → [検索機能] → [ページング] → [最適化]
```

### プロジェクト構成

パート 2 終了時点でのプロジェクト構成は以下のようになります。

```
blog-app/
├── src/
│   └── main/
│       ├── java/
│       │   └── com/example/blog/
│       │       ├── model/
│       │       │   ├── Post.java
│       │       │   ├── Comment.java
│       │       │   ├── Category.java
│       │       │   └── SearchCriteria.java
│       │       ├── dao/
│       │       │   ├── PostDAO.java
│       │       │   ├── CommentDAO.java
│       │       │   ├── CategoryDAO.java
│       │       │   └── BaseDAO.java
│       │       ├── service/
│       │       │   ├── PostService.java
│       │       │   ├── CommentService.java
│       │       │   └── CategoryService.java
│       │       ├── servlet/
│       │       │   ├── post/
│       │       │   │   ├── PostListServlet.java
│       │       │   │   ├── PostCreateServlet.java
│       │       │   │   ├── PostDetailServlet.java
│       │       │   │   ├── PostEditServlet.java
│       │       │   │   ├── PostUpdateServlet.java
│       │       │   │   └── PostDeleteServlet.java
│       │       │   ├── comment/
│       │       │   │   ├── CommentAddServlet.java
│       │       │   │   └── CommentDeleteServlet.java
│       │       │   └── search/
│       │       │       └── SearchServlet.java
│       │       └── util/
│       │           ├── DatabaseUtil.java
│       │           ├── ValidationUtil.java
│       │           └── Constants.java
│       ├── resources/
│       │   └── database.properties
│       └── webapp/
│           ├── WEB-INF/
│           │   └── web.xml
│           ├── css/
│           │   └── style.css
│           ├── js/
│           │   └── main.js
│           └── jsp/
│               ├── post/
│               │   ├── list.jsp
│               │   ├── create.jsp
│               │   ├── detail.jsp
│               │   ├── edit.jsp
│               │   └── search.jsp
│               ├── comment/
│               │   └── form.jsp
│               └── common/
│                   ├── header.jsp
│                   ├── footer.jsp
│                   └── error.jsp
├── pom.xml
└── database/
    ├── schema.sql
    ├── sample_data.sql
    └── migration/
        └── v1_initial_schema.sql
```

### パート 2 での成果物

パート 2 を終了すると、以下の機能を持つ本格的なブログアプリケーションが完成します。

<table><thead><tr><th width="199.90911865234375">機能カテゴリ</th><th>詳細機能</th></tr></thead><tbody><tr><td><strong>データ永続化</strong></td><td>• MySQL データベースによるデータ保存&#x3C;br>• CRUD 操作の完全実装&#x3C;br>• トランザクション処理による整合性確保</td></tr><tr><td><strong>投稿管理</strong></td><td>• 投稿の作成・編集・削除&#x3C;br>• カテゴリ別分類&#x3C;br>• 投稿日時の自動管理</td></tr><tr><td><strong>コメント機能</strong></td><td>• 投稿へのコメント追加&#x3C;br>• コメントの表示と削除&#x3C;br>• コメント数の表示</td></tr><tr><td><strong>検索とフィルタリング</strong></td><td>• キーワード検索&#x3C;br>• カテゴリ別フィルタリング&#x3C;br>• 日付範囲での絞り込み</td></tr><tr><td><strong>ページング</strong></td><td>• 大量データの効率的表示&#x3C;br>• ページネーション&#x3C;br>• 表示件数の設定</td></tr><tr><td><strong>アーキテクチャ</strong></td><td>• DAO パターンによるデータアクセス層分離&#x3C;br>• Service レイヤーによるビジネスロジック分離&#x3C;br>• 適切なエラーハンドリング</td></tr></tbody></table>

### 学習ポイント

パート 2 で特に注目すべき学習ポイントは以下の通りです。

#### データベース設計とテーブル設計

**主要テーブル**:

* `posts`: 投稿データ
* `comments`: コメントデータ
* `categories`: カテゴリデータ

**設計原則**:

* 正規化による重複データの排除
* 適切な主キーと外部キーの設定
* インデックスによるパフォーマンス最適化

#### DAO パターンとレイヤードアーキテクチャ

| レイヤー                   | 役割           | 実装例                         |
| ---------------------- | ------------ | --------------------------- |
| **Presentation Layer** | ユーザーインターフェース | Servlet, JSP                |
| **Service Layer**      | ビジネスロジック     | PostService, CommentService |
| **Data Access Layer**  | データアクセス      | PostDAO, CommentDAO         |
| **Model Layer**        | データ表現        | Post, Comment, Category     |

#### JDBC と SQL の実践的活用

* **PreparedStatement**: SQL インジェクション対策
* **Connection 管理**: リソースの適切な解放
* **トランザクション**: データ整合性の確保
* **エラーハンドリング**: SQLException の適切な処理

#### パフォーマンス最適化

* **ページング**: `LIMIT` と `OFFSET` を使用した効率的データ取得
* **インデックス**: 検索性能の向上
* **N+1 問題**: 効率的な関連データ取得
* **コネクション管理**: リソース使用量の最適化

### 次のステップ

パート 2 を完了すると、実用的な Web アプリケーション開発の基礎を習得できます。以下の選択肢があります：

* **パート 3** に進み、ユーザー管理とソーシャル機能を実装
* **発展モジュール 2-A** に取り組み、高度なデータ管理技術を習得
* **発展モジュール 2-B** に挑戦し、セキュリティ対策を強化

### 技術要件

パート 2 を開始する前に、以下の環境が準備されていることを確認してください：

* **MySQL 8.4**: データベースサーバー
* **MySQL Connector/J**: JDBC ドライバー
* **パート 1 の完成**: 基本的な Servlet/JSP アプリケーション

パート 2 では、パート 1 で学んだ知識を基盤として、より実践的で本格的な Web アプリケーション開発技術を習得していきます。

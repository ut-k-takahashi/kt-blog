# データベース設計書

## 1. ER図

```
┌──────────────────┐
│      users       │
├──────────────────┤
│ id (PK)          │─┐
│ email            │ │
│ password         │ │
│ name             │ │
│ created_at       │ │
│ updated_at       │ │
└──────────────────┘ │
                     │
                     │ 1
                     │
                     │ *
┌──────────────────┐ │        ┌──────────────────┐
│   categories     │ │        │      tags        │
├──────────────────┤ │        ├──────────────────┤
│ id (PK)          │─┤        │ id (PK)          │─┐
│ name             │ │        │ name             │ │
│ slug             │ │        │ slug             │ │
│ description      │ │        │ created_at       │ │
│ created_at       │ │        │ updated_at       │ │
│ updated_at       │ │        └──────────────────┘ │
└──────────────────┘ │                             │
                     │ 1                           │
                     │                             │
                     │ *                         * │
                 ┌───┴───────────────┐             │
                 │      posts        │             │
                 ├───────────────────┤             │
                 │ id (PK)           │             │
                 │ user_id (FK)      │             │
                 │ category_id (FK)  │             │
                 │ title             │             │
                 │ slug              │             │
                 │ content           │             │
                 │ excerpt           │             │
                 │ thumbnail         │             │
                 │ is_published      │             │
                 │ published_at      │             │
                 │ created_at        │             │
                 │ updated_at        │             │
                 └───────────────────┘             │
                         │                         │
                         │ 1                       │
                         │                         │
                         │ *                     * │
     ┌───────────────────┼───────────────┬─────────┘
     │                   │               │
     │ *               * │               │ *
┌────┴──────────┐  ┌────┴──────────┐  ┌─┴────────────────┐
│   comments    │  │   post_tags   │  │     images       │
├───────────────┤  ├───────────────┤  ├──────────────────┤
│ id (PK)       │  │ id (PK)       │  │ id (PK)          │
│ post_id (FK)  │  │ post_id (FK)  │  │ post_id (FK)     │
│ author_name   │  │ tag_id (FK)   │  │ filename         │
│ author_email  │  │ created_at    │  │ original_name    │
│ content       │  └───────────────┘  │ path             │
│ is_approved   │                     │ url              │
│ created_at    │                     │ size             │
│ updated_at    │                     │ mime_type        │
└───────────────┘                     │ created_at       │
                                       └──────────────────┘
```

## 2. テーブル定義

### 2.1 users テーブル

管理者ユーザー情報を管理

| カラム名 | データ型 | 制約 | 説明 |
|---------|---------|------|------|
| id | INT | PK, AUTO_INCREMENT | ユーザーID |
| email | VARCHAR(255) | UNIQUE, NOT NULL | メールアドレス |
| password | VARCHAR(255) | NOT NULL | パスワード（ハッシュ化） |
| name | VARCHAR(100) | NOT NULL | 表示名 |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | 作成日時 |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP | 更新日時 |

**インデックス:**
- PRIMARY KEY (id)
- UNIQUE INDEX (email)

### 2.2 categories テーブル

記事のカテゴリ情報を管理

| カラム名 | データ型 | 制約 | 説明 |
|---------|---------|------|------|
| id | INT | PK, AUTO_INCREMENT | カテゴリID |
| name | VARCHAR(100) | UNIQUE, NOT NULL | カテゴリ名 |
| slug | VARCHAR(100) | UNIQUE, NOT NULL | URLスラッグ |
| description | TEXT | NULL | 説明 |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | 作成日時 |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP | 更新日時 |

**インデックス:**
- PRIMARY KEY (id)
- UNIQUE INDEX (name)
- UNIQUE INDEX (slug)

### 2.3 tags テーブル

記事に付与するタグ情報を管理

| カラム名 | データ型 | 制約 | 説明 |
|---------|---------|------|------|
| id | INT | PK, AUTO_INCREMENT | タグID |
| name | VARCHAR(50) | UNIQUE, NOT NULL | タグ名 |
| slug | VARCHAR(50) | UNIQUE, NOT NULL | URLスラッグ |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | 作成日時 |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP | 更新日時 |

**インデックス:**
- PRIMARY KEY (id)
- UNIQUE INDEX (name)
- UNIQUE INDEX (slug)

### 2.4 posts テーブル

ブログ記事情報を管理

| カラム名 | データ型 | 制約 | 説明 |
|---------|---------|------|------|
| id | INT | PK, AUTO_INCREMENT | 記事ID |
| user_id | INT | FK (users.id), NOT NULL | 投稿者ID |
| category_id | INT | FK (categories.id), NULL | カテゴリID |
| title | VARCHAR(255) | NOT NULL | タイトル |
| slug | VARCHAR(255) | UNIQUE, NOT NULL | URLスラッグ |
| content | LONGTEXT | NOT NULL | 本文（マークダウン） |
| excerpt | TEXT | NULL | 概要・抜粋 |
| thumbnail | VARCHAR(500) | NULL | サムネイル画像URL |
| is_published | BOOLEAN | NOT NULL, DEFAULT FALSE | 公開状態 |
| published_at | TIMESTAMP | NULL | 公開日時 |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | 作成日時 |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP | 更新日時 |

**インデックス:**
- PRIMARY KEY (id)
- UNIQUE INDEX (slug)
- INDEX (user_id)
- INDEX (category_id)
- INDEX (is_published, published_at)
- FULLTEXT INDEX (title, content) ※検索用

### 2.5 post_tags テーブル

記事とタグの中間テーブル（多対多）

| カラム名 | データ型 | 制約 | 説明 |
|---------|---------|------|------|
| id | INT | PK, AUTO_INCREMENT | ID |
| post_id | INT | FK (posts.id), NOT NULL | 記事ID |
| tag_id | INT | FK (tags.id), NOT NULL | タグID |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | 作成日時 |

**インデックス:**
- PRIMARY KEY (id)
- UNIQUE INDEX (post_id, tag_id)
- INDEX (post_id)
- INDEX (tag_id)

### 2.6 comments テーブル

記事へのコメント情報を管理

| カラム名 | データ型 | 制約 | 説明 |
|---------|---------|------|------|
| id | INT | PK, AUTO_INCREMENT | コメントID |
| post_id | INT | FK (posts.id), NOT NULL | 記事ID |
| author_name | VARCHAR(100) | NOT NULL | 投稿者名 |
| author_email | VARCHAR(255) | NOT NULL | 投稿者メール（非公開） |
| content | TEXT | NOT NULL | コメント内容 |
| is_approved | BOOLEAN | NOT NULL, DEFAULT FALSE | 承認状態 |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | 作成日時 |
| updated_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP | 更新日時 |

**インデックス:**
- PRIMARY KEY (id)
- INDEX (post_id)
- INDEX (is_approved)

### 2.7 images テーブル

アップロードされた画像情報を管理

| カラム名 | データ型 | 制約 | 説明 |
|---------|---------|------|------|
| id | INT | PK, AUTO_INCREMENT | 画像ID |
| post_id | INT | FK (posts.id), NULL | 関連記事ID |
| filename | VARCHAR(255) | UNIQUE, NOT NULL | 保存ファイル名 |
| original_name | VARCHAR(255) | NOT NULL | 元のファイル名 |
| path | VARCHAR(500) | NOT NULL | ファイルパス |
| url | VARCHAR(500) | NOT NULL | アクセスURL |
| size | INT | NOT NULL | ファイルサイズ（バイト） |
| mime_type | VARCHAR(100) | NOT NULL | MIMEタイプ |
| created_at | TIMESTAMP | NOT NULL, DEFAULT CURRENT_TIMESTAMP | 作成日時 |

**インデックス:**
- PRIMARY KEY (id)
- UNIQUE INDEX (filename)
- INDEX (post_id)

## 3. リレーション

### 3.1 1対多 (One-to-Many)

1. **users → posts**
   - 1人のユーザーは複数の記事を投稿できる
   - ON DELETE CASCADE（ユーザー削除時、記事も削除）

2. **categories → posts**
   - 1つのカテゴリには複数の記事が属する
   - ON DELETE SET NULL（カテゴリ削除時、記事のcategory_idをNULLに）

3. **posts → comments**
   - 1つの記事に複数のコメントが付く
   - ON DELETE CASCADE（記事削除時、コメントも削除）

4. **posts → images**
   - 1つの記事に複数の画像が紐づく
   - ON DELETE SET NULL（記事削除時、画像のpost_idをNULLに）

### 3.2 多対多 (Many-to-Many)

1. **posts ↔ tags**
   - post_tags テーブルを介して多対多
   - 1つの記事に複数のタグ
   - 1つのタグが複数の記事に付与される
   - ON DELETE CASCADE（記事またはタグ削除時、中間テーブルのレコードも削除）

## 4. 制約とルール

### 4.1 外部キー制約

```sql
-- posts テーブル
FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE SET NULL

-- post_tags テーブル
FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE
FOREIGN KEY (tag_id) REFERENCES tags(id) ON DELETE CASCADE

-- comments テーブル
FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE

-- images テーブル
FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE SET NULL
```

### 4.2 バリデーションルール

#### posts
- title: 1〜255文字
- slug: 1〜255文字、英数字とハイフンのみ
- content: 必須
- excerpt: 最大500文字推奨

#### categories
- name: 1〜100文字
- slug: 1〜100文字、英数字とハイフンのみ

#### tags
- name: 1〜50文字
- slug: 1〜50文字、英数字とハイフンのみ

#### comments
- author_name: 1〜100文字
- author_email: 有効なメールアドレス形式
- content: 1〜1000文字

#### images
- size: 最大5MB
- mime_type: image/jpeg, image/png, image/gif のみ

## 5. 初期データ

### 5.1 管理者ユーザー

```sql
INSERT INTO users (email, password, name) VALUES
('admin@kt-blog.com', '$2b$10$...', 'Admin User');
```

※ password は bcrypt でハッシュ化された値

### 5.2 デフォルトカテゴリ

```sql
INSERT INTO categories (name, slug, description) VALUES
('フロントエンド', 'frontend', 'Vue.js, React, フロントエンド技術に関する記事'),
('バックエンド', 'backend', 'Node.js, Nest.js, バックエンド技術に関する記事'),
('データベース', 'database', 'MySQLやPostgreSQLなどのデータベース関連'),
('インフラ', 'infrastructure', 'Docker, AWS, インフラ関連の記事'),
('その他', 'others', 'その他の技術記事');
```

## 6. パフォーマンス最適化

### 6.1 インデックス戦略

- **検索頻度の高いカラム**: slug, email, is_published
- **結合に使用するカラム**: 外部キー
- **全文検索**: posts.title, posts.content

### 6.2 クエリ最適化

- N+1問題の回避: Eager Loading使用
- ページネーション: LIMIT, OFFSET
- カウントクエリの最適化

## 7. バックアップ戦略

### 7.1 バックアップ頻度
- 日次: 全データベース
- 週次: 画像ファイル

### 7.2 リストア手順
1. MySQLダンプファイルからリストア
2. 画像ファイルを配置

## 8. マイグレーション管理

TypeORMのマイグレーション機能を使用

```bash
# マイグレーション作成
npm run migration:generate -- src/migrations/InitialSchema

# マイグレーション実行
npm run migration:run

# ロールバック
npm run migration:revert
```

## 9. CREATE TABLE 文

詳細なCREATE TABLE文は別ファイル（migration）で管理します。
TypeORMのエンティティ定義から自動生成されます。

# kt-blog プロジェクトセットアップ手順

このドキュメントは、kt-blogプロジェクトの初期セットアップで実施した作業内容を記録したものです。

**作成日**: 2026年3月9日

---

## プロジェクト概要

**プロジェクト名**: kt-blog（技術ブログシステム）

**目的**:
- 0から設計、開発、リリースを行うことで技術力を向上
- 実務で使用する技術（Vue.js, Nuxt.js, Nest.js, MySQL）の習得
- 設計書作成の経験を積む
- 学習内容を発信する場の構築

**技術スタック**:
- フロントエンド: Vue.js 3, Nuxt.js 3, TypeScript, Vuetify 3
- バックエンド: Nest.js, TypeScript, TypeORM
- データベース: MySQL 8.0
- 開発環境: Docker Compose

---

## フェーズ1: プロジェクト設計（完了）

### 1.1 プロジェクト構造の作成

```bash
kt-blog/
├── docs/                    # 設計書
├── frontend/                # Nuxt.jsフロントエンド
├── backend/                 # Nest.jsバックエンド
├── docker-compose.yml       # Docker設定
└── README.md               # プロジェクトREADME
```

### 1.2 設計書の作成

#### ✅ 要件定義書 (`docs/requirements.md`)

**記載内容**:
- プロジェクト概要と目的
- 機能要件（FR-001〜FR-045）
  - 公開機能: 記事表示、カテゴリ、タグ、検索、コメント
  - 管理機能: 認証、記事管理、カテゴリ管理、タグ管理、コメント管理、画像管理
- 非機能要件
  - パフォーマンス、セキュリティ、可用性、保守性、ユーザビリティ
- システム境界と用語定義

**主要な機能要件**:
- FR-001〜019: 公開ページの機能
- FR-020〜023: 認証機能
- FR-024〜032: 記事管理
- FR-033〜038: カテゴリ・タグ管理
- FR-039〜045: コメント・画像管理

#### ✅ システム設計書 (`docs/system-design.md`)

**記載内容**:
- システム構成図（3層アーキテクチャ）
- ディレクトリ構成（フロントエンド・バックエンド）
- コンポーネント設計
  - フロントエンド: 17個のVueコンポーネント
  - バックエンド: 6つのモジュール
- データフロー図
  - 記事表示フロー
  - 記事投稿フロー
  - 認証フロー
- セキュリティ設計
  - JWT認証、パスワードハッシュ化（Argon2）
  - XSS/CSRF/SQLインジェクション対策
- エラーハンドリング
- パフォーマンス最適化戦略
- テスト戦略
- デプロイ戦略

#### ✅ データベース設計書 (`docs/database-design.md`)

**記載内容**:
- ER図（7テーブル）
- テーブル定義
  - `users`: 管理者ユーザー
  - `categories`: カテゴリ
  - `tags`: タグ
  - `posts`: 記事
  - `post_tags`: 記事とタグの中間テーブル
  - `comments`: コメント
  - `images`: 画像
- リレーション定義
  - 1対多: users→posts, categories→posts, posts→comments
  - 多対多: posts↔tags
- インデックス戦略
- バリデーションルール
- 初期データ定義

#### ✅ API設計書 (`docs/api-design.md`)

**記載内容**:
- ベースURL、認証方式（JWT）
- 共通レスポンス形式
- エンドポイント定義（23個）
  - 認証: `/api/auth/login`, `/api/auth/logout`
  - 記事: `/api/posts`, `/api/posts/:slug`, `/api/admin/posts`
  - カテゴリ: `/api/categories`, `/api/admin/categories`
  - タグ: `/api/tags`, `/api/admin/tags`
  - コメント: `/api/posts/:postId/comments`, `/api/admin/comments`
  - 画像: `/api/admin/images`
- リクエスト/レスポンス例
- エラーコード一覧
- レート制限

---

## フェーズ2: フロントエンドセットアップ（完了）

### 2.1 プロジェクト初期化

**ディレクトリ**: `/frontend`

**主要ファイル作成**:

#### ✅ `package.json`
```json
{
  "name": "kt-blog-frontend",
  "version": "0.1.0",
  "dependencies": {
    "nuxt": "^4.0.2",
    "vue": "^3.5.18",
    "vuetify": "^3.9.3",
    "pinia": "^3.0.3",
    "marked": "^15.0.7"
  }
}
```

#### ✅ `nuxt.config.ts`
- Vuetify統合
- i18n設定（日本語）
- Pinia状態管理
- APIベースURL設定
- カスタムSCSS読み込み

#### ✅ `tsconfig.json`
- TypeScript設定

### 2.2 レイアウトの作成

#### ✅ `layouts/default.vue`
**機能**:
- アプリバー（ヘッダー）
- ナビゲーションドロワー
- フッター
- ログイン/ログアウトボタン

**メニュー項目**:
- ホーム
- 記事一覧
- カテゴリ
- タグ
- 管理画面（認証時のみ）

#### ✅ `layouts/admin.vue`
**機能**:
- 管理画面専用レイアウト
- サイドバーナビゲーション
  - ダッシュボード
  - 記事管理
  - カテゴリ管理
  - タグ管理
  - コメント管理
  - 画像管理

### 2.3 ページの作成

#### ✅ `pages/index.vue`（トップページ）
**表示内容**:
- 最新記事一覧（カード形式）
- サムネイル画像
- カテゴリ、タグ表示
- ページネーション
- サイドバー
  - カテゴリ一覧
  - タグクラウド

#### ✅ `pages/login.vue`（ログインページ）
**機能**:
- メールアドレス入力
- パスワード入力
- ログインボタン
- エラーメッセージ表示

#### ✅ `pages/admin/index.vue`（管理ダッシュボード）
**表示内容**:
- 統計情報カード
  - 記事数
  - カテゴリ数
  - タグ数
  - コメント数

### 2.4 認証機能の実装

#### ✅ `middleware/auth.global.ts`
**機能**:
- 管理画面へのアクセス制御
- 未認証時は `/login` へリダイレクト
- ログイン済みユーザーは `/admin` へリダイレクト

#### ✅ `stores/auth.ts`（Piniaストア）
**機能**:
- ユーザー情報の管理
- JWTトークンの管理
- `login()`: ログイン処理
- `logout()`: ログアウト処理
- `isAuthenticated`: 認証状態の確認
- LocalStorageへの永続化

### 2.5 プラグインの作成

#### ✅ `plugins/vuetify.client.ts`
- Vuetifyの初期化
- カスタムテーマ設定

#### ✅ `plugins/markdown.client.ts`
- Markedライブラリの統合
- マークダウンレンダリング機能

### 2.6 スタイルの設定

#### ✅ `styles/settings.scss`
- Vuetify変数
- カスタムフォント設定

#### ✅ `styles/global.scss`
- グローバルスタイル
- カードホバーエフェクト

### 2.7 型定義の作成

#### ✅ `types/blog.d.ts`
**定義した型**:
- `Post`: 記事
- `Category`: カテゴリ
- `Tag`: タグ
- `Comment`: コメント
- `User`: ユーザー
- `Image`: 画像

### 2.8 国際化設定

#### ✅ `i18n/locales/ja.json`
- 日本語メッセージ定義

### 2.9 その他の設定ファイル

#### ✅ `eslint.config.mjs`
- ESLint設定

#### ✅ `prettier.config.js`
- Prettierコード整形設定

#### ✅ `app.vue`
- ルートコンポーネント
- メタタグ設定

---

## フェーズ3: バックエンドセットアップ（完了）

### 3.1 プロジェクト初期化

**ディレクトリ**: `/backend`

**主要ファイル作成**:

#### ✅ `package.json`
```json
{
  "name": "kt-blog-backend",
  "version": "0.1.0",
  "dependencies": {
    "@nestjs/common": "^10.0.0",
    "@nestjs/core": "^10.0.0",
    "@nestjs/typeorm": "^10.0.0",
    "@nestjs/jwt": "^10.0.0",
    "@node-rs/argon2": "^2.0.2",
    "mysql2": "^3.11.5",
    "typeorm": "^0.3.20"
  }
}
```

#### ✅ `tsconfig.json`, `tsconfig.build.json`
- TypeScript設定
- パスエイリアス設定

#### ✅ `nest-cli.json`
- Nest CLI設定

### 3.2 アプリケーションの初期化

#### ✅ `src/main.ts`
**設定内容**:
- CORS有効化
- グローバルプリフィックス (`/api`)
- バリデーションパイプ
- ポート設定 (3001)

#### ✅ `src/app.module.ts`
**インポートモジュール**:
- ConfigModule（環境変数）
- TypeOrmModule（データベース）
- AuthModule
- UsersModule
- PostsModule
- CategoriesModule
- TagsModule
- CommentsModule
- ImagesModule

### 3.3 データベース設定

#### ✅ `src/config/typeorm.config.ts`
**設定内容**:
- MySQL接続設定
- エンティティ自動読み込み
- synchronize設定（開発環境のみtrue）
- 文字コード: utf8mb4
- タイムゾーン: +09:00

### 3.4 エンティティの作成

#### ✅ `src/users/entities/user.entity.ts`
**フィールド**:
- id（PK）
- email（ユニーク）
- password（ハッシュ化）
- name
- created_at, updated_at
- リレーション: posts（1対多）

#### ✅ `src/posts/entities/post.entity.ts`
**フィールド**:
- id（PK）
- user_id, category_id（FK）
- title, slug（ユニーク）
- content（longtext）
- excerpt, thumbnail
- is_published, published_at
- created_at, updated_at
- リレーション:
  - user（多対1）
  - category（多対1）
  - tags（多対多）
  - comments（1対多）

#### ✅ `src/categories/entities/category.entity.ts`
**フィールド**:
- id（PK）
- name, slug（ユニーク）
- description
- created_at, updated_at
- リレーション: posts（1対多）

#### ✅ `src/tags/entities/tag.entity.ts`
**フィールド**:
- id（PK）
- name, slug（ユニーク）
- created_at, updated_at
- リレーション: posts（多対多）

#### ✅ `src/comments/entities/comment.entity.ts`
**フィールド**:
- id（PK）
- post_id（FK）
- author_name, author_email
- content
- is_approved
- created_at, updated_at
- リレーション: post（多対1）

#### ✅ `src/images/entities/image.entity.ts`
**フィールド**:
- id（PK）
- post_id（FK、nullable）
- filename, original_name
- path, url
- size, mime_type
- created_at

### 3.5 認証機能の実装

#### ✅ `src/auth/auth.module.ts`
**設定**:
- JWTモジュール統合
- シークレットキー設定
- トークン有効期限: 24時間

#### ✅ `src/auth/auth.service.ts`
**メソッド**:
- `validateUser()`: メール・パスワード検証
- `login()`: JWTトークン発行
- `hashPassword()`: Argon2でパスワードハッシュ化

#### ✅ `src/auth/auth.controller.ts`
**エンドポイント**:
- `POST /api/auth/login`: ログイン
- `POST /api/auth/logout`: ログアウト

#### ✅ `src/auth/jwt.strategy.ts`
- JWT認証戦略
- Bearerトークンの検証

#### ✅ `src/auth/local.strategy.ts`
- ローカル認証戦略
- メール・パスワード検証

#### ✅ ガードの作成
- `jwt-auth.guard.ts`: JWT認証ガード
- `local-auth.guard.ts`: ローカル認証ガード

### 3.6 ユーザーモジュールの実装

#### ✅ `src/users/users.module.ts`
- UsersServiceのエクスポート

#### ✅ `src/users/users.service.ts`
**メソッド**:
- `findByEmail()`: メールでユーザー検索
- `findById()`: IDでユーザー検索
- `create()`: ユーザー作成

### 3.7 記事モジュールの実装

#### ✅ `src/posts/posts.module.ts`
- PostsController, PostsServiceの登録

#### ✅ `src/posts/posts.service.ts`
**メソッド**:
- `findAll()`: 公開記事一覧（ページネーション付き）
- `findBySlug()`: スラッグで記事取得
- `findAllForAdmin()`: 全記事取得（管理用）

#### ✅ `src/posts/posts.controller.ts`
**エンドポイント**:
- `GET /api/posts`: 記事一覧（公開）
- `GET /api/posts/:slug`: 記事詳細
- `GET /api/admin/posts`: 記事一覧（管理、要認証）

### 3.8 その他のモジュール

#### ✅ Categories, Tags, Comments, Images
- モジュールとエンティティの定義
- 今後のCRUD実装の準備

### 3.9 環境変数設定

#### ✅ `.env.example`
```env
DB_HOST=localhost
DB_PORT=3306
DB_USERNAME=kt_blog_user
DB_PASSWORD=kt_blog_pass
DB_DATABASE=kt_blog
JWT_SECRET=your-secret-key
PORT=3001
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
```

---

## フェーズ4: 開発環境の構築（完了）

### 4.1 Docker Compose設定

#### ✅ `docker-compose.yml`
**サービス**:
- MySQL 8.0
  - ポート: 3306
  - データベース: kt_blog
  - ユーザー: kt_blog_user
  - パスワード: kt_blog_pass
  - 文字コード: utf8mb4
  - タイムゾーン: Asia/Tokyo
  - ボリューム永続化

#### ✅ `init.sql`
**初期データ**:
- 管理者ユーザー（email: admin@kt-blog.com）
- 5つのカテゴリ
  - フロントエンド
  - バックエンド
  - データベース
  - インフラ
  - その他
- 6つのタグ
  - Vue.js, Nuxt.js, Nest.js, TypeScript, MySQL, Docker

### 4.2 READMEの作成

#### ✅ プロジェクトルート `README.md`
- プロジェクト概要
- 技術スタック
- プロジェクト構成
- ドキュメントリンク

#### ✅ フロントエンド `frontend/README.md`
- セットアップ手順
- 実行コマンド
- 環境変数設定
- 技術スタック

#### ✅ バックエンド `backend/README.md`
- セットアップ手順
- データベースセットアップ
- 実行コマンド
- API一覧
- マイグレーション手順

### 4.3 開発手順書の作成

#### ✅ `docs/development-guide.md`
**内容**:
- 環境構築手順
- 初回ログイン方法
- 開発の流れ（Phase 1〜4）
- よく使うコマンド集
- トラブルシューティング
- 次のステップ

---

## セットアップ完了時点のプロジェクト構成

```
kt-blog/
├── README.md
├── docker-compose.yml
├── init.sql
├── docs/
│   ├── requirements.md           # 要件定義書
│   ├── system-design.md          # システム設計書
│   ├── database-design.md        # データベース設計書
│   ├── api-design.md             # API設計書
│   ├── development-guide.md      # 開発手順書
│   └── setup-history.md          # このファイル
├── frontend/
│   ├── package.json
│   ├── nuxt.config.ts
│   ├── tsconfig.json
│   ├── app.vue
│   ├── layouts/
│   │   ├── default.vue
│   │   └── admin.vue
│   ├── pages/
│   │   ├── index.vue
│   │   ├── login.vue
│   │   └── admin/
│   │       └── index.vue
│   ├── middleware/
│   │   └── auth.global.ts
│   ├── stores/
│   │   └── auth.ts
│   ├── plugins/
│   │   ├── vuetify.client.ts
│   │   └── markdown.client.ts
│   ├── styles/
│   │   ├── settings.scss
│   │   └── global.scss
│   ├── types/
│   │   └── blog.d.ts
│   ├── i18n/
│   │   └── locales/
│   │       └── ja.json
│   ├── eslint.config.mjs
│   ├── prettier.config.js
│   └── README.md
└── backend/
    ├── package.json
    ├── tsconfig.json
    ├── tsconfig.build.json
    ├── nest-cli.json
    ├── .env.example
    ├── README.md
    └── src/
        ├── main.ts
        ├── app.module.ts
        ├── config/
        │   └── typeorm.config.ts
        ├── auth/
        │   ├── auth.module.ts
        │   ├── auth.controller.ts
        │   ├── auth.service.ts
        │   ├── jwt.strategy.ts
        │   ├── jwt-auth.guard.ts
        │   ├── local.strategy.ts
        │   └── local-auth.guard.ts
        ├── users/
        │   ├── users.module.ts
        │   ├── users.service.ts
        │   └── entities/
        │       └── user.entity.ts
        ├── posts/
        │   ├── posts.module.ts
        │   ├── posts.controller.ts
        │   ├── posts.service.ts
        │   └── entities/
        │       └── post.entity.ts
        ├── categories/
        │   ├── categories.module.ts
        │   └── entities/
        │       └── category.entity.ts
        ├── tags/
        │   ├── tags.module.ts
        │   └── entities/
        │       └── tag.entity.ts
        ├── comments/
        │   ├── comments.module.ts
        │   └── entities/
        │       └── comment.entity.ts
        └── images/
            ├── images.module.ts
            └── entities/
                └── image.entity.ts
```

---

## 完了したタスク一覧

✅ **設計フェーズ**
1. 要件定義書の作成
2. システム設計書の作成
3. データベース設計書（ER図含む）の作成
4. API設計書の作成

✅ **フロントエンド**
1. Nuxt.js 3プロジェクトのセットアップ
2. Vuetify 3の統合
3. レイアウトの作成（default, admin）
4. ページの作成（トップ、ログイン、ダッシュボード）
5. 認証ミドルウェアの実装
6. Piniaストアの実装（auth）
7. プラグインの作成（Vuetify, Markdown）
8. 型定義の作成
9. スタイル設定
10. 国際化設定（i18n）

✅ **バックエンド**
1. Nest.jsプロジェクトのセットアップ
2. TypeORMの設定
3. 全エンティティの定義（7テーブル）
4. 認証モジュールの実装（JWT + Argon2）
5. ユーザーモジュールの実装
6. 記事モジュールの基本実装（一覧・詳細取得）
7. その他モジュールの骨格作成
8. 環境変数設定

✅ **開発環境**
1. Docker Compose設定（MySQL）
2. 初期データスクリプト作成
3. READMEファイル作成（3箇所）
4. 開発手順書の作成

---

## 実装状況

### ✅ 完了
- プロジェクト設計（100%）
- フロントエンド基盤（100%）
- バックエンド基盤（100%）
- 開発環境（100%）
- 認証機能（100%）
- 記事取得API（基本実装）

### 🚧 未実装（次フェーズ）
- 記事CRUD（作成・編集・削除）
- カテゴリCRUD
- タグCRUD
- コメント管理
- 画像アップロード
- 検索機能
- マークダウンエディタの統合
- テスト作成

---

## 次のステップ

### Phase 1: 記事CRUD機能の実装

**バックエンド**:
1. 記事作成API（POST /api/admin/posts）
2. 記事更新API（PUT /api/admin/posts/:id）
3. 記事削除API（DELETE /api/admin/posts/:id）
4. DTOの作成とバリデーション

**フロントエンド**:
1. 記事作成ページ（/admin/posts/new）
2. 記事編集ページ（/admin/posts/:id/edit）
3. マークダウンエディタの統合
4. 記事一覧の改善

**推定作業時間**: 2〜3日

---

## 起動方法（クイックスタート）

```bash
# 1. データベース起動
cd /Users/k.takahashi/Projects/sanwa-ss/kt-blog
docker-compose up -d

# 2. バックエンド起動
cd backend
npm install
cp .env.example .env
npm run start:dev

# 3. フロントエンド起動（別ターミナル）
cd ../frontend
npm install
npm run dev
```

**アクセス**:
- フロントエンド: http://localhost:3000
- バックエンドAPI: http://localhost:3001/api

**ログイン情報**:
- メール: admin@kt-blog.com
- パスワード: admin123（初回起動後に設定）

---

## 学んだこと・ポイント

### 設計段階
- 要件定義の重要性：機能を細かく定義することで実装の指針が明確に
- ER図の作成：リレーションを可視化することでデータ構造の理解が深まる
- API設計：エンドポイントを事前に定義することで一貫性のあるAPIに

### フロントエンド
- Nuxt.js 3の設定：composables, layouts, middlewareの使い分け
- Vuetifyの統合：vite-plugin-vuetifyを使った自動インポート
- 状態管理：Piniaでシンプルな認証状態管理
- TypeScript：型定義を先に作成することで開発効率向上

### バックエンド
- Nest.jsのモジュール構成：機能ごとにモジュール分割
- TypeORM：デコレータベースのエンティティ定義
- 認証：JWTとArgon2を使った安全な実装
- ガードとストラテジー：PassportとNest.jsの統合

### その他
- Docker Compose：開発環境の統一
- ドキュメント：詳細な設計書が実装の助けに
- Git管理の重要性（今後実施）

---

## 参考資料

- [Nuxt.js 公式ドキュメント](https://nuxt.com/)
- [Nest.js 公式ドキュメント](https://docs.nestjs.com/)
- [TypeORM ドキュメント](https://typeorm.io/)
- [Vuetify ドキュメント](https://vuetifyjs.com/)

---

**作成者**: k.takahashi  
**最終更新**: 2026年3月9日

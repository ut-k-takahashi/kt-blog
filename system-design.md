# システム設計書

## 1. システム構成

### 1.1 全体構成図

```
┌─────────────────────────────────────────────────────────────┐
│                         ユーザー                              │
└─────────────────────────────────────────────────────────────┘
                              │
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                   Nuxt.js (Frontend)                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│  │  公開ページ   │  │  管理画面     │  │ Composables  │    │
│  │  /pages      │  │  /pages/admin │  │ /composables │    │
│  └──────────────┘  └──────────────┘  └──────────────┘    │
│                         │                                   │
│  ┌──────────────────────────────────────────────────┐    │
│  │          API Client (Fetch / Axios)               │    │
│  └──────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                              │
                              ↓ HTTP/REST API
┌─────────────────────────────────────────────────────────────┐
│                    Nest.js (Backend)                         │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐          │
│  │ Controller │  │  Service   │  │ Repository │          │
│  └────────────┘  └────────────┘  └────────────┘          │
│                                                             │
│  ┌─────────────────────────────────────────────┐          │
│  │  Modules:                                    │          │
│  │  - Auth (JWT認証)                            │          │
│  │  - Posts (記事管理)                          │          │
│  │  - Categories (カテゴリ管理)                 │          │
│  │  - Tags (タグ管理)                           │          │
│  │  - Comments (コメント管理)                   │          │
│  │  - Images (画像管理)                         │          │
│  └─────────────────────────────────────────────┘          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ↓ TypeORM
┌─────────────────────────────────────────────────────────────┐
│                      MySQL Database                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │
│  │  users   │  │  posts   │  │categories│  │   tags   │ │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘ │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐              │
│  │ comments │  │  images  │  │post_tags │              │
│  └──────────┘  └──────────┘  └──────────┘              │
└─────────────────────────────────────────────────────────────┘
```

### 1.2 ディレクトリ構成

```
kt-blog/
├── docs/                           # ドキュメント
│   ├── requirements.md
│   ├── system-design.md
│   ├── database-design.md
│   └── api-design.md
│
├── frontend/                       # Nuxt.js フロントエンド
│   ├── app.vue
│   ├── nuxt.config.ts
│   ├── package.json
│   ├── tsconfig.json
│   ├── components/                 # Vue コンポーネント
│   │   ├── common/                 # 共通コンポーネント
│   │   ├── blog/                   # ブログ用コンポーネント
│   │   └── admin/                  # 管理画面用コンポーネント
│   ├── composables/                # Composition API
│   │   ├── useApi.ts               # API通信
│   │   ├── useAuth.ts              # 認証
│   │   └── useBlog.ts              # ブログデータ
│   ├── layouts/                    # レイアウト
│   │   ├── default.vue             # 公開ページ用
│   │   └── admin.vue               # 管理画面用
│   ├── middleware/                 # ミドルウェア
│   │   └── auth.ts                 # 認証チェック
│   ├── pages/                      # ページ
│   │   ├── index.vue               # トップページ
│   │   ├── posts/
│   │   │   ├── index.vue           # 記事一覧
│   │   │   └── [id].vue            # 記事詳細
│   │   ├── categories/
│   │   │   └── [slug].vue          # カテゴリ別記事
│   │   ├── tags/
│   │   │   └── [slug].vue          # タグ別記事
│   │   ├── login.vue               # ログイン
│   │   └── admin/                  # 管理画面
│   │       ├── index.vue
│   │       ├── posts/
│   │       ├── categories/
│   │       ├── tags/
│   │       ├── comments/
│   │       └── images/
│   ├── plugins/                    # プラグイン
│   │   └── markdown.ts             # マークダウン処理
│   ├── stores/                     # Pinia ストア
│   │   ├── auth.ts
│   │   └── blog.ts
│   ├── types/                      # 型定義
│   │   └── blog.d.ts
│   └── utils/                      # ユーティリティ
│
└── backend/                        # Nest.js バックエンド
    ├── package.json
    ├── tsconfig.json
    ├── nest-cli.json
    ├── src/
    │   ├── main.ts                 # エントリポイント
    │   ├── app.module.ts           # ルートモジュール
    │   ├── config/                 # 設定
    │   │   ├── database.config.ts
    │   │   └── jwt.config.ts
    │   ├── auth/                   # 認証モジュール
    │   │   ├── auth.module.ts
    │   │   ├── auth.controller.ts
    │   │   ├── auth.service.ts
    │   │   ├── jwt.strategy.ts
    │   │   └── dto/
    │   ├── users/                  # ユーザーモジュール
    │   │   ├── users.module.ts
    │   │   ├── users.service.ts
    │   │   ├── entities/
    │   │   │   └── user.entity.ts
    │   │   └── dto/
    │   ├── posts/                  # 記事モジュール
    │   │   ├── posts.module.ts
    │   │   ├── posts.controller.ts
    │   │   ├── posts.service.ts
    │   │   ├── entities/
    │   │   │   └── post.entity.ts
    │   │   └── dto/
    │   ├── categories/             # カテゴリモジュール
    │   │   ├── categories.module.ts
    │   │   ├── categories.controller.ts
    │   │   ├── categories.service.ts
    │   │   ├── entities/
    │   │   │   └── category.entity.ts
    │   │   └── dto/
    │   ├── tags/                   # タグモジュール
    │   │   ├── tags.module.ts
    │   │   ├── tags.controller.ts
    │   │   ├── tags.service.ts
    │   │   ├── entities/
    │   │   │   └── tag.entity.ts
    │   │   └── dto/
    │   ├── comments/               # コメントモジュール
    │   │   ├── comments.module.ts
    │   │   ├── comments.controller.ts
    │   │   ├── comments.service.ts
    │   │   ├── entities/
    │   │   │   └── comment.entity.ts
    │   │   └── dto/
    │   ├── images/                 # 画像モジュール
    │   │   ├── images.module.ts
    │   │   ├── images.controller.ts
    │   │   ├── images.service.ts
    │   │   ├── entities/
    │   │   │   └── image.entity.ts
    │   │   └── dto/
    │   └── common/                 # 共通
    │       ├── guards/             # ガード
    │       │   └── jwt-auth.guard.ts
    │       ├── decorators/         # デコレータ
    │       ├── filters/            # フィルター
    │       └── pipes/              # パイプ
    └── uploads/                    # アップロードファイル
```

## 2. コンポーネント設計

### 2.1 フロントエンド主要コンポーネント

#### 公開ページ
- **PostList.vue**: 記事一覧コンポーネント
- **PostCard.vue**: 記事カード
- **PostDetail.vue**: 記事詳細
- **CategoryList.vue**: カテゴリ一覧
- **TagCloud.vue**: タグクラウド
- **CommentList.vue**: コメント一覧
- **CommentForm.vue**: コメント投稿フォーム
- **SearchBar.vue**: 検索バー
- **Pagination.vue**: ページネーション

#### 管理画面
- **PostEditor.vue**: 記事エディタ（マークダウン）
- **PostTable.vue**: 記事一覧テーブル
- **CategoryForm.vue**: カテゴリフォーム
- **TagForm.vue**: タグフォーム
- **CommentTable.vue**: コメント管理テーブル
- **ImageUploader.vue**: 画像アップロード
- **ImageGallery.vue**: 画像ギャラリー

### 2.2 バックエンド主要モジュール

#### Auth Module
- 認証・認可処理
- JWT トークン発行
- パスワード暗号化

#### Posts Module
- 記事のCRUD操作
- 公開/非公開制御
- 記事検索

#### Categories Module
- カテゴリのCRUD操作

#### Tags Module
- タグのCRUD操作
- タグの記事への紐付け

#### Comments Module
- コメントのCRUD操作
- コメント承認機能

#### Images Module
- 画像アップロード処理
- 画像ファイル管理

## 3. データフロー

### 3.1 記事表示フロー

```
1. ユーザーが記事一覧ページにアクセス
   ↓
2. Nuxt.js が GET /api/posts を呼び出し
   ↓
3. Nest.js PostsController が受信
   ↓
4. PostsService がデータベースから公開記事を取得
   ↓
5. JSON形式でレスポンス返却
   ↓
6. Nuxt.js が受信してコンポーネントにデータを渡す
   ↓
7. 記事一覧を画面に表示
```

### 3.2 記事投稿フロー

```
1. 管理者が記事を作成
   ↓
2. マークダウンエディタで執筆
   ↓
3. カテゴリ・タグを選択
   ↓
4. 「投稿」ボタンをクリック
   ↓
5. POST /api/posts を呼び出し（JWT トークン付与）
   ↓
6. JwtAuthGuard が認証チェック
   ↓
7. PostsService が記事をデータベースに保存
   ↓
8. 成功レスポンス返却
   ↓
9. 記事一覧ページにリダイレクト
```

### 3.3 認証フロー

```
1. 管理者がログインページでメール・パスワード入力
   ↓
2. POST /api/auth/login を呼び出し
   ↓
3. AuthService がユーザー情報を検証
   ↓
4. パスワードが一致すればJWTトークンを発行
   ↓
5. トークンをレスポンスで返却
   ↓
6. Nuxt.js がトークンを保存（Cookie or LocalStorage）
   ↓
7. 以降のAPIリクエストにトークンを付与
```

## 4. セキュリティ設計

### 4.1 認証・認可
- JWT（JSON Web Token）を使用
- トークンの有効期限: 24時間
- リフレッシュトークンは Phase 2 で検討

### 4.2 パスワード管理
- bcryptでハッシュ化（ソルトラウンド: 10）
- 平文パスワードは保存しない

### 4.3 XSS対策
- Vue.js のデフォルトエスケープを利用
- v-html 使用時は sanitize 処理

### 4.4 CSRF対策
- SameSite Cookie 属性を使用
- CORS設定を適切に行う

### 4.5 SQLインジェクション対策
- TypeORM のパラメータバインディングを使用
- 生SQLは極力避ける

### 4.6 ファイルアップロード対策
- 許可する拡張子を制限（jpg, png, gif）
- ファイルサイズ制限（5MB）
- ファイル名をサニタイズ

## 5. エラーハンドリング

### 5.1 HTTPステータスコード

| コード | 意味 | 使用例 |
|--------|------|--------|
| 200 | OK | 成功レスポンス |
| 201 | Created | 作成成功 |
| 400 | Bad Request | バリデーションエラー |
| 401 | Unauthorized | 認証エラー |
| 403 | Forbidden | 権限エラー |
| 404 | Not Found | リソースが見つからない |
| 500 | Internal Server Error | サーバーエラー |

### 5.2 エラーレスポンス形式

```json
{
  "statusCode": 400,
  "message": "Validation failed",
  "errors": [
    {
      "field": "title",
      "message": "Title is required"
    }
  ]
}
```

## 6. パフォーマンス最適化

### 6.1 データベース
- 適切なインデックス設定
- N+1問題の回避（Eager Loading）
- ページネーション実装

### 6.2 フロントエンド
- 画像の遅延読み込み
- コンポーネントの遅延読み込み
- SSR/SSG の活用

### 6.3 キャッシング
- Phase 2 以降で Redis 導入を検討

## 7. テスト戦略

### 7.1 バックエンド
- Unit Test（Jest）
- E2E Test（Supertest）
- カバレッジ目標: 80%以上

### 7.2 フロントエンド
- Component Test（Vitest）
- E2E Test（Playwright or Cypress）

## 8. デプロイ戦略

### 8.1 候補プラットフォーム
- **フロントエンド**: Vercel / Netlify
- **バックエンド**: Heroku / Render / Railway
- **データベース**: PlanetScale / AWS RDS

### 8.2 CI/CD
- GitHub Actions でビルド・テスト自動化
- mainブランチへのマージで自動デプロイ

## 9. 運用・保守

### 9.1 ログ管理
- アクセスログ
- エラーログ
- アプリケーションログ

### 9.2 バックアップ
- データベースの日次バックアップ
- 画像ファイルの定期バックアップ

### 9.3 モニタリング
- Phase 2 以降で検討

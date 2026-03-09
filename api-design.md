# API設計書

## 1. API概要

### 1.1 ベースURL

```
開発環境: http://localhost:3001/api
本番環境: https://api.kt-blog.com/api
```

### 1.2 認証方式

- JWT (JSON Web Token) を使用
- Authorizationヘッダーに Bearer トークンを付与

```
Authorization: Bearer <token>
```

### 1.3 共通レスポンス形式

#### 成功レスポンス

```json
{
  "data": { ... },
  "message": "Success"
}
```

#### エラーレスポンス

```json
{
  "statusCode": 400,
  "message": "Error message",
  "errors": [
    {
      "field": "fieldName",
      "message": "Error description"
    }
  ]
}
```

## 2. 認証関連 API

### 2.1 ログイン

管理者がログインしてJWTトークンを取得

**Endpoint:** `POST /auth/login`

**認証:** 不要

**Request Body:**

```json
{
  "email": "admin@kt-blog.com",
  "password": "password123"
}
```

**Response (200):**

```json
{
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": 1,
      "email": "admin@kt-blog.com",
      "name": "Admin User"
    }
  },
  "message": "Login successful"
}
```

**Error (401):**

```json
{
  "statusCode": 401,
  "message": "Invalid credentials"
}
```

### 2.2 ログアウト

**Endpoint:** `POST /auth/logout`

**認証:** 必要

**Response (200):**

```json
{
  "message": "Logout successful"
}
```

### 2.3 現在のユーザー情報取得

**Endpoint:** `GET /auth/me`

**認証:** 必要

**Response (200):**

```json
{
  "data": {
    "id": 1,
    "email": "admin@kt-blog.com",
    "name": "Admin User"
  }
}
```

## 3. 記事関連 API

### 3.1 記事一覧取得（公開）

公開済みの記事一覧を取得

**Endpoint:** `GET /posts`

**認証:** 不要

**Query Parameters:**

| パラメータ | 型 | 必須 | 説明 | デフォルト |
|-----------|---|------|------|-----------|
| page | number | No | ページ番号 | 1 |
| limit | number | No | 1ページあたりの件数 | 10 |
| category | string | No | カテゴリスラッグ | - |
| tag | string | No | タグスラッグ | - |
| search | string | No | 検索キーワード | - |

**Response (200):**

```json
{
  "data": {
    "posts": [
      {
        "id": 1,
        "title": "Vue.js 3の基本",
        "slug": "vue3-basics",
        "excerpt": "Vue.js 3の基本的な使い方を解説します",
        "thumbnail": "https://example.com/images/vue3.jpg",
        "category": {
          "id": 1,
          "name": "フロントエンド",
          "slug": "frontend"
        },
        "tags": [
          {
            "id": 1,
            "name": "Vue.js",
            "slug": "vuejs"
          }
        ],
        "published_at": "2026-03-01T10:00:00Z",
        "created_at": "2026-03-01T09:00:00Z"
      }
    ],
    "pagination": {
      "total": 50,
      "page": 1,
      "limit": 10,
      "totalPages": 5
    }
  }
}
```

### 3.2 記事詳細取得（公開）

公開済み記事の詳細を取得

**Endpoint:** `GET /posts/:slug`

**認証:** 不要

**Response (200):**

```json
{
  "data": {
    "id": 1,
    "title": "Vue.js 3の基本",
    "slug": "vue3-basics",
    "content": "# Vue.js 3の基本\n\n...",
    "excerpt": "Vue.js 3の基本的な使い方を解説します",
    "thumbnail": "https://example.com/images/vue3.jpg",
    "category": {
      "id": 1,
      "name": "フロントエンド",
      "slug": "frontend"
    },
    "tags": [
      {
        "id": 1,
        "name": "Vue.js",
        "slug": "vuejs"
      }
    ],
    "author": {
      "id": 1,
      "name": "Admin User"
    },
    "published_at": "2026-03-01T10:00:00Z",
    "created_at": "2026-03-01T09:00:00Z",
    "updated_at": "2026-03-01T09:00:00Z"
  }
}
```

**Error (404):**

```json
{
  "statusCode": 404,
  "message": "Post not found"
}
```

### 3.3 記事一覧取得（管理用）

全ての記事（下書き含む）を取得

**Endpoint:** `GET /admin/posts`

**認証:** 必要

**Query Parameters:**

| パラメータ | 型 | 必須 | 説明 | デフォルト |
|-----------|---|------|------|-----------|
| page | number | No | ページ番号 | 1 |
| limit | number | No | 1ページあたりの件数 | 20 |
| status | string | No | published / draft / all | all |

**Response (200):**

```json
{
  "data": {
    "posts": [
      {
        "id": 1,
        "title": "Vue.js 3の基本",
        "slug": "vue3-basics",
        "is_published": true,
        "category": {
          "id": 1,
          "name": "フロントエンド"
        },
        "published_at": "2026-03-01T10:00:00Z",
        "created_at": "2026-03-01T09:00:00Z",
        "updated_at": "2026-03-01T09:00:00Z"
      }
    ],
    "pagination": {
      "total": 100,
      "page": 1,
      "limit": 20,
      "totalPages": 5
    }
  }
}
```

### 3.4 記事作成

新規記事を作成

**Endpoint:** `POST /admin/posts`

**認証:** 必要

**Request Body:**

```json
{
  "title": "Nuxt.js 3入門",
  "slug": "nuxtjs3-introduction",
  "content": "# Nuxt.js 3入門\n\n...",
  "excerpt": "Nuxt.js 3の基本を学びます",
  "thumbnail": "https://example.com/images/nuxt3.jpg",
  "category_id": 1,
  "tag_ids": [1, 2, 3],
  "is_published": true
}
```

**Response (201):**

```json
{
  "data": {
    "id": 2,
    "title": "Nuxt.js 3入門",
    "slug": "nuxtjs3-introduction",
    "is_published": true,
    "published_at": "2026-03-09T10:00:00Z",
    "created_at": "2026-03-09T10:00:00Z"
  },
  "message": "Post created successfully"
}
```

**Error (400):**

```json
{
  "statusCode": 400,
  "message": "Validation failed",
  "errors": [
    {
      "field": "title",
      "message": "Title is required"
    },
    {
      "field": "slug",
      "message": "Slug already exists"
    }
  ]
}
```

### 3.5 記事更新

既存記事を更新

**Endpoint:** `PUT /admin/posts/:id`

**認証:** 必要

**Request Body:**

```json
{
  "title": "Nuxt.js 3入門（更新版）",
  "content": "# Nuxt.js 3入門\n\n...",
  "excerpt": "Nuxt.js 3の基本を学びます",
  "category_id": 1,
  "tag_ids": [1, 2],
  "is_published": true
}
```

**Response (200):**

```json
{
  "data": {
    "id": 2,
    "title": "Nuxt.js 3入門（更新版）",
    "updated_at": "2026-03-09T11:00:00Z"
  },
  "message": "Post updated successfully"
}
```

### 3.6 記事削除

記事を削除

**Endpoint:** `DELETE /admin/posts/:id`

**認証:** 必要

**Response (200):**

```json
{
  "message": "Post deleted successfully"
}
```

## 4. カテゴリ関連 API

### 4.1 カテゴリ一覧取得

**Endpoint:** `GET /categories`

**認証:** 不要

**Response (200):**

```json
{
  "data": [
    {
      "id": 1,
      "name": "フロントエンド",
      "slug": "frontend",
      "description": "Vue.js, React関連の記事",
      "post_count": 25
    }
  ]
}
```

### 4.2 カテゴリ作成

**Endpoint:** `POST /admin/categories`

**認証:** 必要

**Request Body:**

```json
{
  "name": "セキュリティ",
  "slug": "security",
  "description": "セキュリティ関連の記事"
}
```

**Response (201):**

```json
{
  "data": {
    "id": 6,
    "name": "セキュリティ",
    "slug": "security",
    "description": "セキュリティ関連の記事"
  },
  "message": "Category created successfully"
}
```

### 4.3 カテゴリ更新

**Endpoint:** `PUT /admin/categories/:id`

**認証:** 必要

### 4.4 カテゴリ削除

**Endpoint:** `DELETE /admin/categories/:id`

**認証:** 必要

## 5. タグ関連 API

### 5.1 タグ一覧取得

**Endpoint:** `GET /tags`

**認証:** 不要

**Response (200):**

```json
{
  "data": [
    {
      "id": 1,
      "name": "Vue.js",
      "slug": "vuejs",
      "post_count": 15
    }
  ]
}
```

### 5.2 タグ作成

**Endpoint:** `POST /admin/tags`

**認証:** 必要

**Request Body:**

```json
{
  "name": "TypeScript",
  "slug": "typescript"
}
```

**Response (201):**

```json
{
  "data": {
    "id": 10,
    "name": "TypeScript",
    "slug": "typescript"
  },
  "message": "Tag created successfully"
}
```

### 5.3 タグ更新

**Endpoint:** `PUT /admin/tags/:id`

**認証:** 必要

### 5.4 タグ削除

**Endpoint:** `DELETE /admin/tags/:id`

**認証:** 必要

## 6. コメント関連 API

### 6.1 記事のコメント一覧取得

**Endpoint:** `GET /posts/:postId/comments`

**認証:** 不要（承認済みのみ）

**Response (200):**

```json
{
  "data": [
    {
      "id": 1,
      "author_name": "山田太郎",
      "content": "とても参考になりました！",
      "created_at": "2026-03-02T10:00:00Z"
    }
  ]
}
```

### 6.2 コメント投稿

**Endpoint:** `POST /posts/:postId/comments`

**認証:** 不要

**Request Body:**

```json
{
  "author_name": "山田太郎",
  "author_email": "yamada@example.com",
  "content": "とても参考になりました！"
}
```

**Response (201):**

```json
{
  "data": {
    "id": 1,
    "author_name": "山田太郎",
    "content": "とても参考になりました！",
    "is_approved": false,
    "created_at": "2026-03-02T10:00:00Z"
  },
  "message": "Comment submitted. Waiting for approval."
}
```

### 6.3 管理用コメント一覧

**Endpoint:** `GET /admin/comments`

**認証:** 必要

**Query Parameters:**

| パラメータ | 型 | 必須 | 説明 |
|-----------|---|------|------|
| status | string | No | approved / pending / all |

**Response (200):**

```json
{
  "data": [
    {
      "id": 1,
      "post": {
        "id": 1,
        "title": "Vue.js 3の基本"
      },
      "author_name": "山田太郎",
      "author_email": "yamada@example.com",
      "content": "とても参考になりました！",
      "is_approved": false,
      "created_at": "2026-03-02T10:00:00Z"
    }
  ]
}
```

### 6.4 コメント承認

**Endpoint:** `PUT /admin/comments/:id/approve`

**認証:** 必要

**Response (200):**

```json
{
  "message": "Comment approved successfully"
}
```

### 6.5 コメント削除

**Endpoint:** `DELETE /admin/comments/:id`

**認証:** 必要

## 7. 画像関連 API

### 7.1 画像アップロード

**Endpoint:** `POST /admin/images`

**認証:** 必要

**Content-Type:** `multipart/form-data`

**Request Body:**

```
file: (binary)
post_id: 1 (optional)
```

**Response (201):**

```json
{
  "data": {
    "id": 1,
    "filename": "abc123.jpg",
    "original_name": "screenshot.jpg",
    "url": "https://example.com/uploads/abc123.jpg",
    "size": 102400,
    "mime_type": "image/jpeg"
  },
  "message": "Image uploaded successfully"
}
```

### 7.2 画像一覧取得

**Endpoint:** `GET /admin/images`

**認証:** 必要

**Response (200):**

```json
{
  "data": [
    {
      "id": 1,
      "filename": "abc123.jpg",
      "original_name": "screenshot.jpg",
      "url": "https://example.com/uploads/abc123.jpg",
      "size": 102400,
      "created_at": "2026-03-01T10:00:00Z"
    }
  ]
}
```

### 7.3 画像削除

**Endpoint:** `DELETE /admin/images/:id`

**認証:** 必要

**Response (200):**

```json
{
  "message": "Image deleted successfully"
}
```

## 8. エラーコード一覧

| コード | 説明 |
|-------|------|
| 200 | OK - 成功 |
| 201 | Created - 作成成功 |
| 400 | Bad Request - バリデーションエラー |
| 401 | Unauthorized - 認証エラー |
| 403 | Forbidden - 権限エラー |
| 404 | Not Found - リソースが見つからない |
| 409 | Conflict - 重複エラー |
| 500 | Internal Server Error - サーバーエラー |

## 9. レート制限

- 認証不要エンドポイント: 100リクエスト/分
- 認証必要エンドポイント: 300リクエスト/分

制限を超えた場合:

```json
{
  "statusCode": 429,
  "message": "Too many requests"
}
```

## 10. バージョニング

将来的にAPIバージョニングを導入予定

```
/api/v1/posts
/api/v2/posts
```

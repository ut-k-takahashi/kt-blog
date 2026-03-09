# kt-blog

技術ブログシステム

## 概要

個人の技術学習内容を発信するための技術ブログシステムです。
Vue.js、Nuxt.js、Nest.js、MySQLを使用した実践的なWebアプリケーションです。

## 目的

- 0から設計、開発、リリースを行うことで、技術力を向上させる
- 実務で使用する技術スタックの習得
- 設計書作成の経験を積む
- 学習内容を発信する場の構築

## 技術スタック

### フロントエンド
- Vue.js 3
- Nuxt.js 3
- TypeScript
- Vuetify (UIフレームワーク)

### バックエンド
- Nest.js
- TypeScript
- TypeORM
- JWT認証

### データベース
- MySQL 8.0

### その他
- Docker / Docker Compose (開発環境)
- ESLint / Prettier (コード品質)

## 主な機能

### フロント（公開側）
- 記事一覧表示
- 記事詳細表示
- カテゴリ別表示
- タグ別表示
- 記事検索
- コメント投稿

### 管理画面
- 管理者ログイン
- 記事のCRUD操作
- カテゴリ管理
- タグ管理
- コメント管理
- 画像アップロード
- マークダウンエディタ

## プロジェクト構成

```
kt-blog/
├── docs/                    # 設計書
├── frontend/                # Nuxt.jsフロントエンド
└── backend/                 # Nest.jsバックエンド
```

## ドキュメント

- [要件定義書](./docs/requirements.md)
- [システム設計書](./docs/system-design.md)
- [データベース設計書](./docs/database-design.md)
- [API設計書](./docs/api-design.md)

## 開発者

k.takahashi

## ライセンス

MIT

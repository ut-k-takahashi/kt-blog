# 開発手順

このドキュメントではkt-blogプロジェクトの開発手順を説明します。

## 1. 環境構築

### 前提条件
- Node.js 18以上
- Docker & Docker Compose
- Git

### セットアップ手順

#### 1.1 リポジトリのクローン（すでに完了）

```bash
cd /Users/k.takahashi/Projects/sanwa-ss/kt-blog
```

#### 1.2 データベースの起動

```bash
# Dockerでデータベースを起動
docker-compose up -d

# データベースが起動するまで待機（約10秒）
sleep 10

# データベース接続確認
docker-compose ps
```

#### 1.3 バックエンドのセットアップ

```bash
cd backend

# 依存パッケージのインストール
npm install

# 環境変数ファイルの作成
cp .env.example .env

# .envファイルを編集（必要に応じて）
# DB_HOST=localhost
# DB_PORT=3306
# DB_USERNAME=kt_blog_user
# DB_PASSWORD=kt_blog_pass
# DB_DATABASE=kt_blog

# 開発サーバー起動
npm run start:dev
```

バックエンドは `http://localhost:3001` で起動します。

#### 1.4 管理者ユーザーのパスワード設定

```bash
# Node.jsのREPLを起動
node

# 以下のコードを実行してパスワードをハッシュ化
const { hash } = require('@node-rs/argon2');
hash('admin123').then(console.log);
// 出力されたハッシュをコピー

# MySQLに接続してユーザー情報を更新
mysql -h localhost -u kt_blog_user -pkt_blog_pass kt_blog

UPDATE users SET password = 'コピーしたハッシュ値' WHERE email = 'admin@kt-blog.com';
```

#### 1.5 フロントエンドのセットアップ

```bash
cd ../frontend

# 依存パッケージのインストール
npm install

# 開発サーバー起動
npm run dev
```

フロントエンドは `http://localhost:3000` で起動します。

## 2. 初回ログイン

1. ブラウザで `http://localhost:3000` にアクセス
2. 「ログイン」をクリック
3. 以下の情報でログイン
   - メールアドレス: `admin@kt-blog.com`
   - パスワード: `admin123`

## 3. 開発の流れ

### Phase 1: 基本機能（MVP）
- [x] プロジェクト設計書作成
- [x] データベース設計
- [x] フロントエンドセットアップ
- [x] バックエンドセットアップ
- [ ] 記事CRUD機能の実装
  - [ ] 記事一覧表示
  - [ ] 記事詳細表示
  - [ ] 記事作成
  - [ ] 記事編集
  - [ ] 記事削除

### Phase 2: カテゴリ・タグ機能
- [ ] カテゴリ管理
- [ ] タグ管理
- [ ] カテゴリ別記事表示
- [ ] タグ別記事表示

### Phase 3: コメント・画像機能
- [ ] コメント投稿
- [ ] コメント管理
- [ ] 画像アップロード
- [ ] 画像管理

### Phase 4: 検索・UI改善
- [ ] 記事検索機能
- [ ] ページネーション改善
- [ ] レスポンシブ対応
- [ ] SEO対策

## 4. よく使うコマンド

### データベース

```bash
# データベースコンテナの起動
docker-compose up -d

# データベースコンテナの停止
docker-compose down

# ログ確認
docker-compose logs -f mysql

# MySQLに接続
mysql -h localhost -u kt_blog_user -pkt_blog_pass kt_blog
```

### バックエンド

```bash
cd backend

# 開発サーバー起動
npm run start:dev

# ビルド
npm run build

# テスト
npm run test

# リント
npm run lint
```

### フロントエンド

```bash
cd frontend

# 開発サーバー起動
npm run dev

# ビルド
npm run build

# リント
npm run lint
```

## 5. トラブルシューティング

### ポートが使用中
```bash
# ポートを使用しているプロセスを確認
lsof -i :3000  # フロントエンド
lsof -i :3001  # バックエンド
lsof -i :3306  # MySQL

# プロセスを終了
kill -9 <PID>
```

### データベース接続エラー
- Dockerコンテナが起動しているか確認
- `.env`の設定が正しいか確認
- データベースのパスワードが正しいか確認

### パッケージインストールエラー
```bash
# node_modulesを削除して再インストール
rm -rf node_modules package-lock.json
npm install
```

## 6. 次のステップ

次は記事CRUD機能の実装に進みます：
1. 記事作成APIの実装
2. 記事作成フォームの実装
3. 記事一覧・詳細表示の改善
4. マークダウンエディタの統合

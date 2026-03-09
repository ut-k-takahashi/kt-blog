# GitHubでプロジェクトを管理する方法

このドキュメントでは、kt-blogプロジェクトをGitHubで管理する手順を説明します。

---

## 前提条件

- Gitがインストールされている
- GitHubアカウントを持っている
- GitHubへのSSH接続またはHTTPS接続が設定済み

### Gitのインストール確認

```bash
git --version
```

バージョンが表示されればOKです。

---

## ステップ1: Gitリポジトリの初期化

プロジェクトのルートディレクトリでGitリポジトリを初期化します。

```bash
cd /Users/k.takahashi/Projects/sanwa-ss/kt-blog

# Gitリポジトリの初期化
git init

# 現在のブランチをmainに変更（推奨）
git branch -M main
```

---

## ステップ2: .gitignoreの確認

以下のファイルが作成されていることを確認してください：

- `.gitignore` （ルート）
- `frontend/.gitignore`
- `backend/.gitignore`

これらのファイルにより、以下がGit管理から除外されます：
- `node_modules/` （依存パッケージ）
- `.env` （環境変数、機密情報）
- `dist/`, `.nuxt/` （ビルド成果物）
- `uploads/` （アップロードファイル）
- その他の一時ファイル

---

## ステップ3: 初回コミット

プロジェクト全体をステージングしてコミットします。

```bash
# 全てのファイルをステージング
git add .

# ステージングされたファイルを確認
git status

# 初回コミット
git commit -m "Initial commit: プロジェクトセットアップ完了

- 設計書作成（要件定義、システム設計、DB設計、API設計）
- フロントエンド（Nuxt.js 3）セットアップ
- バックエンド（Nest.js）セットアップ
- Docker環境構築
- 認証機能実装
- 記事取得API実装"
```

### コミットメッセージのベストプラクティス

```bash
# 1行目: 簡潔なタイトル（50文字以内）
# 2行目: 空行
# 3行目以降: 詳細な説明（箇条書きなど）

git commit -m "feat: 記事作成機能を実装

- POST /api/admin/posts エンドポイント追加
- 記事作成フォームコンポーネント実装
- バリデーション追加"
```

---

## ステップ4: GitHubリポジトリの作成

### 4-1. GitHubでの操作

1. [GitHub](https://github.com) にログイン
2. 右上の「+」→「New repository」をクリック
3. リポジトリ情報を入力：
   - **Repository name**: `kt-blog`
   - **Description**: `技術ブログシステム - Vue.js, Nuxt.js, Nest.js, MySQL`
   - **Visibility**: 
     - `Public`（公開） または `Private`（非公開）を選択
   - **Initialize this repository with**: 
     - ✅ **何もチェックしない**（既にローカルにファイルがあるため）
4. 「Create repository」をクリック

### 4-2. 表示される画面

リポジトリ作成後、以下のような画面が表示されます：

```
…or push an existing repository from the command line

git remote add origin git@github.com:あなたのユーザー名/kt-blog.git
git branch -M main
git push -u origin main
```

この**コマンドをコピー**しておきます。

---

## ステップ5: リモートリポジトリへのプッシュ

ローカルとGitHubを接続して、コミットをプッシュします。

### SSH接続の場合

```bash
# リモートリポジトリを追加
git remote add origin git@github.com:あなたのユーザー名/kt-blog.git

# リモートリポジトリを確認
git remote -v

# mainブランチをプッシュ
git push -u origin main
```

### HTTPS接続の場合

```bash
# リモートリポジトリを追加
git remote add origin https://github.com/あなたのユーザー名/kt-blog.git

# mainブランチをプッシュ
git push -u origin main
```

**注意**: 初回プッシュ時にGitHubの認証が求められる場合があります。

---

## ステップ6: プッシュの確認

GitHubのリポジトリページを開いて、ファイルがアップロードされているか確認します。

```
https://github.com/あなたのユーザー名/kt-blog
```

以下のような構造が表示されるはずです：

```
kt-blog/
├── README.md
├── docker-compose.yml
├── init.sql
├── docs/
├── frontend/
└── backend/
```

---

## ステップ7: READMEの充実

GitHubのトップページに表示されるREADMEを充実させます。

### ルートREADMEに追加する内容

```markdown
## 🚀 クイックスタート

### 前提条件
- Node.js 18以上
- Docker & Docker Compose
- MySQL 8.0（またはDocker）

### セットアップ

1. リポジトリのクローン
   ```bash
   git clone https://github.com/あなたのユーザー名/kt-blog.git
   cd kt-blog
   ```

2. データベース起動
   ```bash
   docker-compose up -d
   ```

3. バックエンドのセットアップ
   ```bash
   cd backend
   npm install
   cp .env.example .env
   npm run start:dev
   ```

4. フロントエンドのセットアップ
   ```bash
   cd ../frontend
   npm install
   npm run dev
   ```

5. ブラウザでアクセス
   - フロントエンド: http://localhost:3000
   - バックエンドAPI: http://localhost:3001/api

## 📚 ドキュメント

- [要件定義書](./docs/requirements.md)
- [システム設計書](./docs/system-design.md)
- [データベース設計書](./docs/database-design.md)
- [API設計書](./docs/api-design.md)
- [開発手順書](./docs/development-guide.md)
- [セットアップ履歴](./docs/setup-history.md)
```

---

## 日常的なGit操作

### 作業の流れ

```bash
# 1. 最新の状態を取得
git pull origin main

# 2. ファイルを編集・作成

# 3. 変更をステージング
git add .

# 4. コミット
git commit -m "feat: ○○機能を追加"

# 5. プッシュ
git push origin main
```

### よく使うコマンド

```bash
# 現在の状態を確認
git status

# 変更履歴を確認
git log --oneline

# 特定のファイルだけステージング
git add frontend/pages/index.vue

# 変更差分を確認
git diff

# 直前のコミットメッセージを修正
git commit --amend

# リモートの最新を取得（マージはしない）
git fetch origin

# ブランチ一覧を表示
git branch -a
```

---

## ブランチ戦略（推奨）

開発を進める際は、ブランチを切って作業することを推奨します。

### Git Flow的なブランチ戦略

```
main          → 本番環境（常に動作する状態）
├── develop   → 開発ブランチ
    ├── feature/post-crud     → 機能開発ブランチ
    ├── feature/comment       → 機能開発ブランチ
    └── bugfix/login-error    → バグ修正ブランチ
```

### ブランチ操作

```bash
# 開発ブランチを作成
git checkout -b develop

# 機能開発ブランチを作成
git checkout -b feature/post-crud

# ブランチで作業してコミット
git add .
git commit -m "feat: 記事作成機能を実装"

# developブランチにマージ
git checkout develop
git merge feature/post-crud

# mainブランチにマージ（リリース時）
git checkout main
git merge develop

# リモートにプッシュ
git push origin main
git push origin develop
```

---

## GitHub上での作業

### Issues（課題管理）

新機能やバグをIssueとして登録できます。

1. GitHubリポジトリの「Issues」タブ
2. 「New issue」をクリック
3. タイトルと説明を入力
4. Labelsを設定（enhancement, bug, documentationなど）

例：
```
Title: 記事編集機能の実装
Labels: enhancement

Description:
記事の編集機能を実装する

- [ ] 編集APIエンドポイント作成
- [ ] 編集フォームUI実装
- [ ] バリデーション追加
```

### Pull Requests（プルリクエスト）

ブランチをマージする前にレビューできます。

```bash
# feature/post-crudブランチで作業
git checkout -b feature/post-crud
# ... 作業してコミット ...
git push origin feature/post-crud
```

その後、GitHubで：
1. 「Pull requests」タブ
2. 「New pull request」
3. base: `develop` ← compare: `feature/post-crud`
4. タイトルと説明を入力して作成

### Projects（プロジェクト管理）

カンバン形式でタスク管理ができます。

1. 「Projects」タブ
2. 「New project」
3. テンプレート選択（例：Team backlog）
4. Issueをドラッグ&ドロップで管理

---

## .envファイルの管理

`.env`ファイルは機密情報を含むため、Gitにコミットしません。

### 管理方法

1. `.env.example`をリポジトリに含める（機密情報なし）
2. `.env`は`.gitignore`で除外
3. チームメンバーは`.env.example`をコピーして使用

```bash
# 他のメンバーがクローン後に実行
cp backend/.env.example backend/.env
# .envファイルを編集して実際の値を設定
```

### .env.exampleの例

```env
# データベース設定
DB_HOST=localhost
DB_PORT=3306
DB_USERNAME=your_username_here
DB_PASSWORD=your_password_here
DB_DATABASE=kt_blog

# JWT認証（本番では必ず変更）
JWT_SECRET=change-this-secret-in-production

# サーバー設定
PORT=3001
NODE_ENV=development
```

---

## GitHub Actionsでの自動化（今後）

CI/CDパイプラインを構築できます。

### `.github/workflows/test.yml` の例

```yaml
name: Test

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies (Backend)
        run: |
          cd backend
          npm ci
      
      - name: Run tests (Backend)
        run: |
          cd backend
          npm run test
      
      - name: Install dependencies (Frontend)
        run: |
          cd frontend
          npm ci
      
      - name: Build (Frontend)
        run: |
          cd frontend
          npm run build
```

---

## コミットメッセージの規約

チーム開発を見据えた、わかりやすいコミットメッセージを書きましょう。

### Conventional Commits

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Type**:
- `feat`: 新機能
- `fix`: バグ修正
- `docs`: ドキュメント
- `style`: コードスタイル（動作に影響なし）
- `refactor`: リファクタリング
- `test`: テスト追加・修正
- `chore`: その他（ビルド、依存関係など）

**例**:

```bash
git commit -m "feat(posts): 記事作成APIを実装"

git commit -m "fix(auth): ログイン時のトークン有効期限を修正"

git commit -m "docs: READMEにセットアップ手順を追加"

git commit -m "refactor(posts): サービスクラスをリファクタリング"
```

---

## トラブルシューティング

### コミットを取り消したい

```bash
# 直前のコミットを取り消し（変更は残る）
git reset --soft HEAD^

# 直前のコミットを完全に取り消し（変更も削除）
git reset --hard HEAD^
```

### プッシュしたコミットを取り消したい

```bash
# 危険：履歴を書き換える
git reset --hard HEAD^
git push -f origin main

# 安全：新しいコミットで打ち消す
git revert HEAD
git push origin main
```

### コンフリクト（競合）が発生した

```bash
git pull origin main
# 競合が発生

# 1. 競合ファイルを手動で編集
# 2. 競合マーカーを削除
# 3. ステージング
git add .

# 4. コミット
git commit -m "merge: 競合を解決"
```

### ブランチを間違えた

```bash
# 変更をstashに退避
git stash

# 正しいブランチに切り替え
git checkout correct-branch

# 変更を復元
git stash pop
```

---

## まとめ

### 初回セットアップ（一度だけ）

```bash
cd /Users/k.takahashi/Projects/sanwa-ss/kt-blog
git init
git branch -M main
git add .
git commit -m "Initial commit: プロジェクトセットアップ完了"
git remote add origin git@github.com:あなたのユーザー名/kt-blog.git
git push -u origin main
```

### 日常の作業フロー

```bash
git pull origin main        # 最新を取得
# ... 作業 ...
git add .                   # ステージング
git commit -m "メッセージ"   # コミット
git push origin main        # プッシュ
```

### 推奨ワークフロー（ブランチ使用）

```bash
git checkout -b feature/new-feature  # ブランチ作成
# ... 作業 ...
git add .
git commit -m "feat: 新機能追加"
git push origin feature/new-feature
# GitHubでPull Request作成
# マージ後、ローカルを更新
git checkout main
git pull origin main
```

---

## 参考リンク

- [Git公式ドキュメント](https://git-scm.com/doc)
- [GitHub Docs](https://docs.github.com/ja)
- [Conventional Commits](https://www.conventionalcommits.org/ja/)
- [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)

---

**作成者**: k.takahashi  
**最終更新**: 2026年3月9日

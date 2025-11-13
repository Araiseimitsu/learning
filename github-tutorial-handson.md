# GitHub完全ハンズオンチュートリアル
## 初心者から上級者まで段階的に学べる実践ガイド

---

## 目次
1. [初心者編：GitHubをはじめよう](#初心者編githubをはじめよう)
2. [中級者編：チーム開発を極める](#中級者編チーム開発を極める)
3. [上級者編：プロフェッショナルテクニック](#上級者編プロフェッショナルテクニック)

---

## 初心者編：GitHubをはじめよう

### 1-1. GitとGitHubの基礎知識

#### GitとGitHubの違い
- **Git**: バージョン管理システム（ローカルで動作）
- **GitHub**: Gitリポジトリをホスティングするクラウドサービス

#### なぜGitHubを使うのか？
- コードのバージョン管理
- チームでの協働作業
- オープンソースプロジェクトへの貢献
- ポートフォリオとしての活用

### 1-2. 環境構築ハンズオン

#### ステップ1: Gitのインストール

**Windows**
```bash
# Git for Windowsをダウンロード
# https://git-scm.com/download/win
# インストーラーを実行し、デフォルト設定で進める

# インストール確認
git --version
```

**Mac**
```bash
# Homebrewでインストール
brew install git

# または、Xcodeコマンドラインツールから
xcode-select --install

# インストール確認
git --version
```

**Linux (Ubuntu/Debian)**
```bash
sudo apt update
sudo apt install git

# インストール確認
git --version
```

#### ステップ2: Gitの初期設定

```bash
# ユーザー名の設定
git config --global user.name "あなたの名前"

# メールアドレスの設定
git config --global user.email "your.email@example.com"

# 設定確認
git config --list
```

#### ステップ3: GitHubアカウントの作成

1. [GitHub公式サイト](https://github.com)にアクセス
2. 「Sign up」をクリック
3. メールアドレス、パスワード、ユーザー名を入力
4. メール認証を完了

#### ステップ4: SSH鍵の設定（推奨）

```bash
# SSH鍵の生成
ssh-keygen -t ed25519 -C "your.email@example.com"
# Enterを3回押してデフォルト設定で作成

# SSH鍵の表示
cat ~/.ssh/id_ed25519.pub

# 表示された公開鍵をコピー
```

**GitHubへの登録**
1. GitHubの Settings > SSH and GPG keys
2. 「New SSH key」をクリック
3. コピーした公開鍵を貼り付け
4. 「Add SSH key」をクリック

```bash
# 接続テスト
ssh -T git@github.com
# "Hi username! You've successfully authenticated..." と表示されればOK
```

### 1-3. 最初のリポジトリ作成ハンズオン

#### ステップ1: GitHubでリポジトリを作成

1. GitHubにログイン
2. 右上の「+」→「New repository」をクリック
3. リポジトリ名を入力（例：my-first-repo）
4. PublicまたはPrivateを選択
5. 「Add a README file」にチェック
6. 「Create repository」をクリック

#### ステップ2: ローカルにクローン

```bash
# リポジトリをクローン（HTTPS）
git clone https://github.com/username/my-first-repo.git

# または、SSH（推奨）
git clone git@github.com:username/my-first-repo.git

# ディレクトリに移動
cd my-first-repo
```

### 1-4. 基本的なGitコマンド実践

#### 演習1: ファイルの追加とコミット

```bash
# 新しいファイルを作成
echo "# Hello GitHub" > hello.txt

# ステータス確認
git status
# 出力: Untracked files: hello.txt

# ステージングエリアに追加
git add hello.txt

# 再度ステータス確認
git status
# 出力: Changes to be committed

# コミット
git commit -m "Add hello.txt"

# コミット履歴確認
git log
```

#### 演習2: ファイルの変更とプッシュ

```bash
# ファイルを編集
echo "This is my first GitHub project!" >> hello.txt

# 変更内容確認
git diff

# 変更をステージング
git add hello.txt

# コミット
git commit -m "Update hello.txt with description"

# GitHubにプッシュ
git push origin main

# GitHubのWebページで変更を確認
```

#### 演習3: リモートの変更をプル

```bash
# GitHub上でREADME.mdを直接編集（Webインターフェースで）

# ローカルに変更を取得
git pull origin main

# ファイルの内容を確認
cat README.md
```

### 1-5. ブランチの基本操作

#### ブランチとは？
複数の作業を並行して進めるための機能。メインブランチに影響を与えずに新機能開発やバグ修正が可能。

#### 演習4: ブランチの作成と切り替え

```bash
# 現在のブランチ確認
git branch

# 新しいブランチを作成
git branch feature/add-greeting

# ブランチを切り替え
git checkout feature/add-greeting

# または、作成と切り替えを同時に（推奨）
git checkout -b feature/add-greeting

# ブランチ一覧確認
git branch
```

#### 演習5: ブランチでの作業

```bash
# 新しいファイルを作成
echo "Hello, World!" > greeting.txt

# 変更をコミット
git add greeting.txt
git commit -m "Add greeting message"

# ブランチをプッシュ
git push origin feature/add-greeting
```

### 1-6. プルリクエスト（Pull Request）の作成

#### ステップ1: GitHub上でプルリクエストを作成

1. GitHubのリポジトリページにアクセス
2. 「Compare & pull request」ボタンをクリック
3. タイトルと説明を記入
   - タイトル例：「Add greeting message feature」
   - 説明例：「挨拶メッセージを表示する機能を追加しました」
4. 「Create pull request」をクリック

#### ステップ2: プルリクエストをマージ

1. プルリクエストの内容を確認
2. 「Merge pull request」をクリック
3. 「Confirm merge」をクリック
4. ブランチを削除（オプション）

#### ステップ3: ローカルのmainブランチを更新

```bash
# mainブランチに切り替え
git checkout main

# 最新の変更を取得
git pull origin main

# 不要なローカルブランチを削除
git branch -d feature/add-greeting
```

### 1-7. よく使うGitコマンド一覧

| コマンド | 説明 |
|---------|------|
| `git init` | 新しいリポジトリを初期化 |
| `git clone <URL>` | リモートリポジトリをクローン |
| `git status` | 作業ディレクトリの状態確認 |
| `git add <ファイル>` | ステージングエリアにファイルを追加 |
| `git add .` | すべての変更をステージング |
| `git commit -m "メッセージ"` | 変更をコミット |
| `git push origin <ブランチ>` | リモートにプッシュ |
| `git pull origin <ブランチ>` | リモートから変更を取得 |
| `git branch` | ブランチ一覧表示 |
| `git checkout <ブランチ>` | ブランチを切り替え |
| `git checkout -b <新ブランチ>` | ブランチを作成して切り替え |
| `git merge <ブランチ>` | ブランチをマージ |
| `git log` | コミット履歴を表示 |
| `git diff` | 変更内容を表示 |

### 1-8. GitHub Pagesで公開する

#### 演習6: 静的サイトをデプロイ

```bash
# 新しいブランチを作成
git checkout -b gh-pages

# index.htmlを作成
cat > index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>My GitHub Page</title>
</head>
<body>
    <h1>Hello, GitHub Pages!</h1>
    <p>This is my first GitHub Pages site.</p>
</body>
</html>
EOF

# コミットとプッシュ
git add index.html
git commit -m "Add index.html for GitHub Pages"
git push origin gh-pages
```

**GitHub上での設定**
1. リポジトリの Settings > Pages
2. Source: `gh-pages` ブランチを選択
3. Save をクリック
4. 数分後、`https://username.github.io/my-first-repo/` でアクセス可能

---

## 中級者編：チーム開発を極める

### 2-1. ブランチ戦略の理解

#### Git Flow（大規模プロジェクト向け）

```
main (本番環境)
  └── develop (開発用)
       ├── feature/login (機能開発)
       ├── feature/payment (機能開発)
       └── release/v1.0 (リリース準備)
```

**実践演習：Git Flowの実装**

```bash
# developブランチの作成
git checkout -b develop

# 機能開発ブランチの作成
git checkout -b feature/user-authentication

# 開発作業
echo "User authentication logic" > auth.js
git add auth.js
git commit -m "feat: Add user authentication"

# developにマージ
git checkout develop
git merge feature/user-authentication

# プッシュ
git push origin develop
```

#### GitHub Flow（小規模・頻繁リリース向け）

```
main
  ├── feature/add-button
  ├── fix/header-bug
  └── feature/new-api
```

**実践演習：GitHub Flowの実装**

```bash
# mainから機能ブランチを作成
git checkout main
git pull origin main
git checkout -b feature/add-contact-form

# 開発とコミット
echo "Contact form HTML" > contact.html
git add contact.html
git commit -m "Add contact form"

# プッシュしてPR作成
git push origin feature/add-contact-form
# GitHub上でPR作成 → レビュー → マージ
```

### 2-2. コンフリクト解決の実践

#### 演習7: 意図的にコンフリクトを作成して解決

```bash
# 準備：2つのブランチで同じファイルを編集

# ブランチ1
git checkout -b branch-1
echo "Version 1" > file.txt
git add file.txt
git commit -m "Update file from branch-1"

# mainに戻る
git checkout main

# ブランチ2
git checkout -b branch-2
echo "Version 2" > file.txt
git add file.txt
git commit -m "Update file from branch-2"

# branch-1をmainにマージ
git checkout main
git merge branch-1
# マージ成功

# branch-2をmainにマージ（コンフリクト発生）
git merge branch-2
# CONFLICT: Merge conflict in file.txt
```

**コンフリクトの解決方法**

```bash
# ファイルを開くと以下のような表示
<<<<<<< HEAD
Version 1
=======
Version 2
>>>>>>> branch-2

# ファイルを編集して解決
echo "Version 1 and 2 merged" > file.txt

# 解決をコミット
git add file.txt
git commit -m "Merge branch-2 and resolve conflicts"
```

### 2-3. Pull Requestのベストプラクティス

#### 良いPRの書き方

**テンプレート例**

```markdown
## 概要
このPRでは、ユーザー認証機能を追加します。

## 変更内容
- ログインフォームの実装
- JWT認証の導入
- ユーザーセッション管理

## テスト
- [ ] ログイン機能のテスト
- [ ] ログアウト機能のテスト
- [ ] セッション管理のテスト

## スクリーンショット
（必要に応じて画像を添付）

## 関連Issue
Closes #123
```

#### 演習8: PRレビューの実践

```bash
# レビュアー側の作業

# PRのブランチをチェックアウト
git fetch origin
git checkout origin/feature/new-feature

# コードレビュー
# - コーディング規約の確認
# - ロジックの妥当性
# - テストの有無

# GitHub上でコメント
# - 「Approve」：承認
# - 「Request changes」：修正依頼
# - 「Comment」：コメントのみ
```

### 2-4. 高度なGitコマンド

#### git stash（作業の一時退避）

```bash
# 作業中に緊急の修正が必要になった場合

# 現在の変更を一時退避
git stash save "Work in progress on feature X"

# 緊急修正ブランチに切り替え
git checkout hotfix/urgent-bug

# 修正作業...

# 元のブランチに戻る
git checkout feature/feature-x

# 退避した作業を復元
git stash pop

# stash一覧を表示
git stash list

# 特定のstashを適用
git stash apply stash@{0}
```

#### git rebase（履歴の整理）

```bash
# mainの最新状態を取得
git checkout main
git pull origin main

# 機能ブランチをrebase
git checkout feature/my-feature
git rebase main

# コンフリクトが発生した場合
# 1. ファイルを編集して解決
# 2. git add <ファイル>
# 3. git rebase --continue

# rebaseを中止したい場合
git rebase --abort

# プッシュ（強制プッシュが必要）
git push origin feature/my-feature --force-with-lease
```

#### git cherry-pick（特定のコミットを適用）

```bash
# 他のブランチから特定のコミットだけを取得

# コミットハッシュを確認
git log --oneline

# cherry-pickで適用
git cherry-pick abc1234

# 複数のコミットを適用
git cherry-pick abc1234 def5678

# コンフリクトが発生した場合
git cherry-pick --continue
# または
git cherry-pick --abort
```

### 2-5. GitHub Projects・Issues・Milestonesの活用

#### 演習9: プロジェクト管理の実践

**Issueの作成**
```markdown
タイトル: ログイン機能の実装

## 説明
ユーザーがメールアドレスとパスワードでログインできる機能を実装する

## タスク
- [ ] ログインフォームのUI作成
- [ ] バックエンドAPI実装
- [ ] バリデーション追加
- [ ] テストコード作成

## 受け入れ基準
- ユーザーが正しい認証情報でログインできる
- 誤った認証情報でエラーメッセージが表示される
- セッションが正しく管理される
```

**ラベルの活用**
- `bug`：バグ修正
- `enhancement`：機能強化
- `documentation`：ドキュメント
- `good first issue`：初心者向け
- `help wanted`：協力者募集
- `priority: high`：優先度高

**Milestonesの設定**
```
Milestone: v1.0.0 Release
Due date: 2025-12-31
Description: 最初の本番リリース

関連Issues:
- #1 ログイン機能
- #2 ユーザー登録
- #3 プロフィール編集
```

### 2-6. GitHub Actionsでci/cdの導入

#### 演習10: 自動テストの設定

**.github/workflows/test.yml**

```yaml
name: Run Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'

    - name: Install dependencies
      run: npm install

    - name: Run tests
      run: npm test

    - name: Run linter
      run: npm run lint
```

#### 演習11: 自動デプロイの設定

**.github/workflows/deploy.yml**

```yaml
name: Deploy to Production

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Build application
      run: |
        npm install
        npm run build

    - name: Deploy to GitHub Pages
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./dist
```

### 2-7. コミットメッセージの規約

#### Conventional Commits

```bash
# フォーマット
<type>(<scope>): <subject>

# 例
feat(auth): Add login functionality
fix(ui): Fix button alignment issue
docs(readme): Update installation instructions
style(css): Format styles with prettier
refactor(api): Simplify user data fetching
test(auth): Add login tests
chore(deps): Update dependencies
```

**Type一覧**
- `feat`: 新機能
- `fix`: バグ修正
- `docs`: ドキュメント
- `style`: コードスタイル（フォーマット等）
- `refactor`: リファクタリング
- `test`: テスト追加・修正
- `chore`: ビルド・補助ツール関連

#### 演習12: 規約に従ったコミット

```bash
# 機能追加
git commit -m "feat(user): Add user profile page"

# バグ修正
git commit -m "fix(payment): Resolve credit card validation error"

# 破壊的変更
git commit -m "feat(api): Change authentication method

BREAKING CHANGE: JWT tokens now required for all API requests"
```

---

## 上級者編：プロフェッショナルテクニック

### 3-1. Git履歴の高度な操作

#### git reflog（履歴の復元）

```bash
# 誤ってコミットを削除した場合の復元

# reflogを確認
git reflog
# 出力例:
# abc1234 HEAD@{0}: reset: moving to HEAD~1
# def5678 HEAD@{1}: commit: Important feature
# ghi9012 HEAD@{2}: commit: Previous work

# 削除したコミットを復元
git reset --hard HEAD@{1}

# または特定のコミットハッシュで復元
git reset --hard def5678
```

#### git reset（コミットの取り消し）

```bash
# soft reset（コミットのみ取り消し、変更は保持）
git reset --soft HEAD~1

# mixed reset（デフォルト、ステージングも解除）
git reset HEAD~1

# hard reset（すべての変更を破棄）
git reset --hard HEAD~1

# 特定のファイルだけステージング解除
git reset HEAD file.txt
```

#### git revert（コミットの打ち消し）

```bash
# 特定のコミットを打ち消す新しいコミットを作成
git revert abc1234

# 複数のコミットを打ち消し
git revert abc1234 def5678

# マージコミットの打ち消し
git revert -m 1 merge_commit_hash
```

### 3-2. Git bisectでバグ調査

#### 演習13: バグが混入したコミットを特定

```bash
# bisectを開始
git bisect start

# 現在のコミット（バグがある）をbadとマーク
git bisect bad

# 正常だったコミットをgoodとマーク
git bisect good v1.0.0

# Gitが自動的に中間のコミットをチェックアウト
# テストを実行して結果を報告

# バグがある場合
git bisect bad

# バグがない場合
git bisect good

# これを繰り返すと、バグが混入したコミットが特定される

# bisectを終了
git bisect reset
```

#### 自動化されたbisect

```bash
# テストスクリプトを作成（test.sh）
#!/bin/bash
npm test
exit $?

# 実行権限を付与
chmod +x test.sh

# 自動bisect実行
git bisect start
git bisect bad HEAD
git bisect good v1.0.0
git bisect run ./test.sh

# 結果が自動的に出力される
```

### 3-3. Git Hooksのカスタマイズ

#### pre-commitフック（コミット前の検証）

**.git/hooks/pre-commit**

```bash
#!/bin/bash

echo "Running pre-commit checks..."

# コードフォーマットチェック
npm run format:check
if [ $? -ne 0 ]; then
    echo "❌ Code formatting failed. Run 'npm run format' to fix."
    exit 1
fi

# Lintチェック
npm run lint
if [ $? -ne 0 ]; then
    echo "❌ Linting failed. Please fix the errors."
    exit 1
fi

# テスト実行
npm test
if [ $? -ne 0 ]; then
    echo "❌ Tests failed. Please fix failing tests."
    exit 1
fi

echo "✅ All pre-commit checks passed!"
exit 0
```

#### pre-pushフック（プッシュ前の検証）

**.git/hooks/pre-push**

```bash
#!/bin/bash

protected_branch='main'
current_branch=$(git symbolic-ref HEAD | sed -e 's,.*/\(.*\),\1,')

if [ $current_branch = $protected_branch ]; then
    echo "❌ Direct push to $protected_branch is not allowed!"
    echo "Please use pull requests."
    exit 1
fi

echo "✅ Push allowed"
exit 0
```

#### チーム全体でhooksを共有

```bash
# hooksディレクトリを作成
mkdir -p .githooks

# フックスクリプトを配置
cp pre-commit .githooks/
cp pre-push .githooks/

# Gitにhooksパスを設定
git config core.hooksPath .githooks

# チームメンバーも同じコマンドを実行
```

### 3-4. Git SubmoduleとSubtree

#### Submodule（外部リポジトリの管理）

```bash
# サブモジュールを追加
git submodule add https://github.com/user/library.git libs/library

# サブモジュールを含めてクローン
git clone --recurse-submodules https://github.com/user/main-project.git

# 既存リポジトリでサブモジュールを初期化
git submodule init
git submodule update

# サブモジュールを最新に更新
git submodule update --remote

# サブモジュールを削除
git submodule deinit libs/library
git rm libs/library
rm -rf .git/modules/libs/library
```

#### Subtree（別のリポジトリをサブディレクトリとして統合）

```bash
# リモートを追加
git remote add library-remote https://github.com/user/library.git

# subtreeとして追加
git subtree add --prefix=libs/library library-remote main --squash

# subtreeを更新
git subtree pull --prefix=libs/library library-remote main --squash

# subtreeに変更をプッシュ
git subtree push --prefix=libs/library library-remote main
```

### 3-5. Git Worktree（複数ブランチの並行作業）

#### 演習14: 複数の作業を並行して実行

```bash
# 現在の作業ディレクトリ
pwd
# /home/user/project

# 別のディレクトリで別ブランチを作業
git worktree add ../project-feature-a feature-a
git worktree add ../project-hotfix hotfix/urgent-bug

# worktree一覧を表示
git worktree list
# /home/user/project          abc1234 [main]
# /home/user/project-feature-a def5678 [feature-a]
# /home/user/project-hotfix   ghi9012 [hotfix/urgent-bug]

# それぞれのディレクトリで独立して作業可能

# worktreeを削除
git worktree remove ../project-feature-a

# 不要なworktreeをクリーンアップ
git worktree prune
```

**ユースケース**
- CI/CDパイプライン実行中に別の作業を続ける
- コードレビュー中に緊急のホットフィックス対応
- 複数のAIエージェント（GitHub Copilot等）で並行開発

### 3-6. GitHub Advanced Security

#### Dependabot（依存関係の自動更新）

**.github/dependabot.yml**

```yaml
version: 2
updates:
  # npm依存関係
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
    reviewers:
      - "team-lead"
    labels:
      - "dependencies"
      - "automated"

  # GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "monthly"
```

#### Code Scanning（コードの脆弱性検出）

**.github/workflows/codeql.yml**

```yaml
name: "CodeQL"

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 0 * * 1'

jobs:
  analyze:
    name: Analyze
    runs-on: ubuntu-latest
    permissions:
      actions: read
      contents: read
      security-events: write

    strategy:
      matrix:
        language: [ 'javascript', 'python' ]

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Initialize CodeQL
      uses: github/codeql-action/init@v3
      with:
        languages: ${{ matrix.language }}

    - name: Autobuild
      uses: github/codeql-action/autobuild@v3

    - name: Perform CodeQL Analysis
      uses: github/codeql-action/analyze@v3
```

### 3-7. GitHub CLI（gh）の活用

#### インストール

```bash
# Mac
brew install gh

# Windows
winget install --id GitHub.cli

# Linux
sudo apt install gh

# 認証
gh auth login
```

#### 演習15: CLIでワークフローを効率化

```bash
# リポジトリのクローン
gh repo clone username/repo

# Issueの作成
gh issue create --title "Bug: Login fails" --body "Description..."

# Issue一覧表示
gh issue list

# PRの作成
gh pr create --title "Add feature X" --body "Implementation details..."

# PRのチェックアウト
gh pr checkout 123

# PRのマージ
gh pr merge 123 --squash

# リポジトリの作成
gh repo create my-new-project --public --source=. --push

# GitHub Actionsの実行状況確認
gh run list

# 特定のworkflowを実行
gh workflow run test.yml

# リリースの作成
gh release create v1.0.0 --title "Version 1.0.0" --notes "Release notes..."
```

#### エイリアスの設定

```bash
# .gitconfigにエイリアスを追加
gh alias set prc 'pr create --web'
gh alias set prv 'pr view --web'
gh alias set il 'issue list --assignee @me'

# 使用例
gh prc  # PRをWebで作成
gh prv  # 現在のブランチのPRをWebで表示
gh il   # 自分にアサインされたIssue一覧
```

### 3-8. GitHub Container Registry（GHCR）

#### 演習16: Dockerイメージの公開

**Dockerfile**

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

**GitHub Actionsでビルド・公開**

**.github/workflows/docker-publish.yml**

```yaml
name: Build and Push Docker Image

on:
  push:
    branches: [ main ]
    tags: [ 'v*' ]
  pull_request:
    branches: [ main ]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Log in to Container registry
      uses: docker/login-action@v3
      with:
        registry: ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}

    - name: Extract metadata
      id: meta
      uses: docker/metadata-action@v5
      with:
        images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
        tags: |
          type=ref,event=branch
          type=ref,event=pr
          type=semver,pattern={{version}}
          type=semver,pattern={{major}}.{{minor}}

    - name: Build and push Docker image
      uses: docker/build-push-action@v5
      with:
        context: .
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
```

**イメージの使用**

```bash
# イメージのプル
docker pull ghcr.io/username/repo:latest

# コンテナの実行
docker run -p 3000:3000 ghcr.io/username/repo:latest
```

### 3-9. GitHub Codespaces

#### 演習17: クラウド開発環境の構築

**.devcontainer/devcontainer.json**

```json
{
  "name": "My Development Environment",
  "image": "mcr.microsoft.com/devcontainers/javascript-node:20",
  "features": {
    "ghcr.io/devcontainers/features/docker-in-docker:2": {},
    "ghcr.io/devcontainers/features/github-cli:1": {}
  },
  "customizations": {
    "vscode": {
      "extensions": [
        "dbaeumer.vscode-eslint",
        "esbenp.prettier-vscode",
        "eamodio.gitlens",
        "github.copilot"
      ],
      "settings": {
        "editor.formatOnSave": true,
        "editor.defaultFormatter": "esbenp.prettier-vscode"
      }
    }
  },
  "postCreateCommand": "npm install",
  "forwardPorts": [3000, 5000],
  "remoteUser": "node"
}
```

**使用方法**
1. GitHubリポジトリページで「Code」→「Codespaces」→「Create codespace on main」
2. ブラウザまたはVS Codeで開く
3. 設定済みの環境ですぐに開発開始

### 3-10. オープンソースプロジェクトへの貢献

#### 演習18: First Contributionの実践

```bash
# 1. リポジトリをフォーク（GitHub上で「Fork」ボタン）

# 2. フォークしたリポジトリをクローン
git clone https://github.com/YOUR-USERNAME/ORIGINAL-REPO.git
cd ORIGINAL-REPO

# 3. upstream（元のリポジトリ）を追加
git remote add upstream https://github.com/ORIGINAL-OWNER/ORIGINAL-REPO.git

# 4. 最新の変更を取得
git fetch upstream
git checkout main
git merge upstream/main

# 5. 作業用ブランチを作成
git checkout -b fix/typo-in-readme

# 6. 変更を加える
# README.mdのtypoを修正

# 7. コミット
git add README.md
git commit -m "docs: Fix typo in README.md"

# 8. プッシュ
git push origin fix/typo-in-readme

# 9. GitHub上でPull Requestを作成
# - フォークしたリポジトリのページで「Compare & pull request」
# - タイトルと説明を記入（プロジェクトのガイドラインに従う）
# - 「Create pull request」

# 10. レビューに対応
# - フィードバックがあれば修正
# - 同じブランチにコミット・プッシュすれば自動的にPRに反映

# 11. マージ後のクリーンアップ
git checkout main
git pull upstream main
git push origin main
git branch -d fix/typo-in-readme
git push origin --delete fix/typo-in-readme
```

### 3-11. モノレポ管理

#### 演習19: モノレポの構築

**ディレクトリ構造**

```
monorepo/
├── packages/
│   ├── frontend/
│   │   ├── package.json
│   │   └── src/
│   ├── backend/
│   │   ├── package.json
│   │   └── src/
│   └── shared/
│       ├── package.json
│       └── src/
├── package.json
└── lerna.json
```

**package.json（ルート）**

```json
{
  "name": "my-monorepo",
  "private": true,
  "workspaces": [
    "packages/*"
  ],
  "scripts": {
    "build": "lerna run build",
    "test": "lerna run test",
    "lint": "lerna run lint"
  },
  "devDependencies": {
    "lerna": "^8.0.0"
  }
}
```

**lerna.json**

```json
{
  "version": "independent",
  "npmClient": "npm",
  "packages": [
    "packages/*"
  ]
}
```

**GitHub Actionsでのモノレポ対応**

```yaml
name: Monorepo CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        package: [frontend, backend, shared]

    steps:
    - uses: actions/checkout@v4

    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'

    - name: Install dependencies
      run: npm ci

    - name: Test ${{ matrix.package }}
      run: npm test --workspace=packages/${{ matrix.package }}
```

### 3-12. 大規模チーム開発のベストプラクティス

#### CODEOWNERSの設定

**.github/CODEOWNERS**

```
# デフォルトオーナー
* @team-lead

# フロントエンド
/frontend/ @frontend-team
*.css @frontend-team
*.html @frontend-team

# バックエンド
/backend/ @backend-team
*.py @backend-team

# インフラ
/.github/ @devops-team
/docker/ @devops-team
*.yml @devops-team

# ドキュメント
/docs/ @documentation-team
*.md @documentation-team

# 特定のファイル
/package.json @team-lead @devops-team
```

#### ブランチ保護ルールの設定

**GitHub上での設定**
1. Settings > Branches > Branch protection rules
2. 以下を設定：
   - ✅ Require pull request reviews before merging
     - Required approvals: 2
   - ✅ Require status checks to pass before merging
     - Required checks: tests, lint, build
   - ✅ Require conversation resolution before merging
   - ✅ Require linear history
   - ✅ Include administrators

### 3-13. パフォーマンス最適化

#### 大きなリポジトリのクローンを高速化

```bash
# Shallow clone（履歴を制限）
git clone --depth 1 https://github.com/user/large-repo.git

# 特定のブランチのみクローン
git clone --single-branch --branch main https://github.com/user/repo.git

# Sparse checkout（特定のディレクトリのみ）
git clone --filter=blob:none --sparse https://github.com/user/repo.git
cd repo
git sparse-checkout init --cone
git sparse-checkout set src/important-module
```

#### Git LFS（Large File Storage）

```bash
# Git LFSのインストール
git lfs install

# 大きなファイルをトラッキング
git lfs track "*.psd"
git lfs track "*.mp4"
git lfs track "*.zip"

# .gitattributesが自動生成される
git add .gitattributes

# 通常通り使用
git add large-file.psd
git commit -m "Add design file"
git push origin main

# LFSファイルの確認
git lfs ls-files
```

---

## 付録

### A. トラブルシューティング

#### よくある問題と解決方法

**1. マージコンフリクトが解決できない**

```bash
# マージを中止して最初からやり直す
git merge --abort

# または、特定のファイルの戦略を選択
git checkout --ours file.txt   # 自分の変更を優先
git checkout --theirs file.txt # 相手の変更を優先
```

**2. 間違ったブランチにコミットした**

```bash
# まだプッシュしていない場合

# コミットを取り消し（変更は保持）
git reset --soft HEAD~1

# 正しいブランチに切り替え
git checkout correct-branch

# 再度コミット
git add .
git commit -m "Correct commit"
```

**3. プッシュが拒否される**

```bash
# リモートの変更を取得してマージ
git pull origin main --rebase

# コンフリクトを解決後
git push origin main
```

**4. 大きなファイルを間違ってコミットした**

```bash
# 最新のコミットから削除
git rm --cached large-file.zip
git commit --amend -m "Remove large file"
git push origin main --force-with-lease

# 履歴全体から削除
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch large-file.zip" \
  --prune-empty --tag-name-filter cat -- --all
```

**5. 認証エラー**

```bash
# SSH鍵の確認
ssh -T git@github.com

# 認証情報のリセット
git credential-cache exit

# HTTPSからSSHに変更
git remote set-url origin git@github.com:username/repo.git
```

### B. GitHubショートカット

**リポジトリ閲覧時**
- `t` - ファイル検索
- `l` - 特定の行に移動
- `w` - ブランチまたはタグ選択
- `y` - URLを永続的なリンクに変換
- `.` - GitHub.devでリポジトリを開く（Web版VS Code）
- `>` - コマンドパレットを開く

**Issue・PR画面**
- `c` - Issueの作成
- `e` - Issue/PRの編集
- `/` - Issue/PR検索
- `r` - コメント返信

### C. 便利な.gitconfigの設定

```bash
# エイリアスの設定
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual 'log --oneline --graph --all'

# デフォルトブランチ名
git config --global init.defaultBranch main

# カラー表示
git config --global color.ui auto

# エディタの設定
git config --global core.editor "code --wait"

# 改行コードの設定
git config --global core.autocrlf input  # Mac/Linux
git config --global core.autocrlf true   # Windows

# プッシュのデフォルト動作
git config --global push.default current

# rebaseをデフォルトに
git config --global pull.rebase true
```

### D. 学習リソース

#### 公式ドキュメント
- [Git公式ドキュメント](https://git-scm.com/doc)
- [GitHub Docs](https://docs.github.com)
- [GitHub Skills](https://skills.github.com/) - インタラクティブなチュートリアル

#### 推奨書籍
- 「Pro Git」（無料・オンライン）
- 「GitHubの教科書」
- 「わかばちゃんと学ぶ Git使い方入門」

#### 実践プラットフォーム
- [Learn Git Branching](https://learngitbranching.js.org/) - ビジュアル学習
- [GitHub Learning Lab](https://lab.github.com/) - 実践的なコース
- [First Contributions](https://github.com/firstcontributions/first-contributions) - OSS貢献の練習

#### コミュニティ
- GitHub Community Forum
- Stack Overflow (git/github tags)
- Reddit r/git, r/github

### E. チートシート

```bash
# === 初期化 ===
git init                    # リポジトリを初期化
git clone <URL>             # リポジトリをクローン

# === 基本操作 ===
git status                  # 状態確認
git add <file>              # ステージング
git add .                   # すべてステージング
git commit -m "message"     # コミット
git commit --amend          # 直前のコミットを修正

# === ブランチ ===
git branch                  # ブランチ一覧
git branch <name>           # ブランチ作成
git checkout <branch>       # ブランチ切り替え
git checkout -b <branch>    # 作成して切り替え
git merge <branch>          # ブランチをマージ
git branch -d <branch>      # ブランチを削除

# === リモート ===
git remote -v               # リモート確認
git fetch origin            # リモートから取得
git pull origin <branch>    # フェッチ＆マージ
git push origin <branch>    # プッシュ
git push -u origin <branch> # プッシュ＆追跡設定

# === 履歴 ===
git log                     # コミット履歴
git log --oneline           # 1行で表示
git log --graph --all       # グラフ表示
git diff                    # 変更差分
git show <commit>           # コミット詳細

# === 取り消し ===
git reset --soft HEAD~1     # コミット取り消し（変更保持）
git reset --hard HEAD~1     # コミット取り消し（変更破棄）
git revert <commit>         # コミットを打ち消し
git clean -fd               # 未追跡ファイル削除

# === 一時退避 ===
git stash                   # 変更を退避
git stash pop               # 退避を復元
git stash list              # 退避一覧

# === 高度な操作 ===
git rebase <branch>         # リベース
git cherry-pick <commit>    # コミットを適用
git reflog                  # 操作履歴
git bisect start            # バイナリサーチ開始
```

---

## まとめ

このチュートリアルでは、GitとGitHubの使い方を初心者から上級者まで段階的に学習しました。

**初心者編** では、環境構築、基本的なコマンド、プルリクエストの作成など、GitHubを使い始めるための基礎を習得しました。

**中級者編** では、ブランチ戦略、コンフリクト解決、CI/CD、プロジェクト管理など、チーム開発に必要なスキルを身につけました。

**上級者編** では、高度なGitコマンド、セキュリティ、大規模プロジェクト管理など、プロフェッショナルとして必要な技術を学びました。

### 次のステップ

1. **実際のプロジェクトで実践** - 学んだことを自分のプロジェクトで試す
2. **オープンソースに貢献** - 実践的な経験を積む
3. **継続的な学習** - 新しい機能やベストプラクティスをフォローする
4. **チームで共有** - 学んだ知識をチームメンバーと共有する

GitとGitHubは日々進化しています。公式ドキュメントやコミュニティを通じて、最新情報をキャッチアップしましょう！

---

**作成日**: 2025年11月13日
**参考**: GitHub公式ドキュメント、Git公式ドキュメント、各種技術ブログ
**ライセンス**: このドキュメントは学習目的で自由に使用できます

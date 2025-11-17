# Git Worktree完全ハンズオンブック
## 初心者から上級者まで実践的に学べる作業ディレクトリ管理の決定版

---

## 目次
1. [初心者編：Git Worktreeの基礎](#初心者編git-worktreeの基礎)
2. [中級者編：実践的な活用法](#中級者編実践的な活用法)
3. [上級者編：プロフェッショナルテクニック](#上級者編プロフェッショナルテクニック)
4. [トラブルシューティング](#トラブルシューティング)

---

## 初心者編：Git Worktreeの基礎

### 1-1. Git Worktreeとは？

#### 従来の問題点
通常、Gitリポジトリで複数のブランチを同時に作業する場合：
```bash
# 現在の作業を一時退避
git stash

# ブランチを切り替え
git checkout other-branch

# 作業完了後、元のブランチに戻る
git checkout main
git stash pop
```

この方法の問題点：
- 作業の切り替えが面倒
- ビルド結果やnode_modulesなどが再生成される
- 切り替え中に元の作業の状態を確認できない
- stashの管理が複雑になる

#### Git Worktreeの解決策
Git Worktreeを使うと、**1つのリポジトリに対して複数の作業ディレクトリを持てます**。

```
my-project/              # メインのworktree
├── .git/               # Gitリポジトリ本体
└── src/

my-project-feature/      # 追加のworktree
└── src/                # feature-branchの内容

my-project-hotfix/       # 追加のworktree
└── src/                # hotfix-branchの内容
```

#### メリット
- ✅ ブランチごとに独立した作業ディレクトリ
- ✅ `git checkout`による切り替え不要
- ✅ 複数のブランチで同時に作業可能
- ✅ ビルド結果が保持される（高速）
- ✅ IDEやエディタで複数ウィンドウで開ける

### 1-2. 環境準備

#### 前提条件
```bash
# Git 2.5以降が必要
git --version

# Git 2.15以降を推奨（worktree機能が安定）
```

#### Gitのアップデート方法

**Windows**
```bash
# Git for Windowsを最新版に更新
git update-git-for-windows
```

**Mac**
```bash
# Homebrewで更新
brew upgrade git
```

**Linux (Ubuntu/Debian)**
```bash
sudo apt update
sudo apt upgrade git
```

### 1-3. 基本的な使い方ハンズオン

#### ハンズオン1：最初のWorktreeを作成する

**ステップ1: 練習用リポジトリを作成**
```bash
# 新しいディレクトリを作成
mkdir worktree-practice
cd worktree-practice

# Gitリポジトリを初期化
git init

# 初期ファイルを作成
echo "# Worktree Practice" > README.md
git add README.md
git commit -m "Initial commit"

# 現在の状態を確認
git status
```

**ステップ2: 新しいブランチ用のWorktreeを作成**
```bash
# 新しいブランチ"feature-1"用のworktreeを作成
git worktree add ../worktree-practice-feature feature-1

# 作成されたworktreeを確認
git worktree list
```

出力例：
```
/home/user/worktree-practice         abc1234 [main]
/home/user/worktree-practice-feature def5678 [feature-1]
```

**ステップ3: 新しいWorktreeで作業する**
```bash
# 新しいworktreeに移動
cd ../worktree-practice-feature

# ブランチを確認
git branch

# ファイルを編集
echo "## Feature 1" >> README.md
git add README.md
git commit -m "Add feature 1 section"

# 元のディレクトリに戻る
cd ../worktree-practice

# 元のディレクトリのREADME.mdを確認（変更されていない）
cat README.md
```

**ステップ4: Worktreeを削除する**
```bash
# worktreeを削除
git worktree remove ../worktree-practice-feature

# または、ディレクトリを削除してからprune
rm -rf ../worktree-practice-feature
git worktree prune

# 削除されたことを確認
git worktree list
```

### 1-4. 基本コマンド一覧

#### worktree add - 新しいworktreeを作成
```bash
# 基本形：新しいブランチを作成してworktreeを追加
git worktree add <パス> <新しいブランチ名>

# 既存のブランチからworktreeを作成
git worktree add <パス> <既存のブランチ名>

# 現在のブランチから新しいworktreeを作成
git worktree add <パス> -b <新しいブランチ名>

# detached HEADでworktreeを作成
git worktree add <パス> <コミットハッシュ>
```

#### worktree list - worktreeの一覧表示
```bash
# 全てのworktreeを表示
git worktree list

# 詳細情報付きで表示
git worktree list --porcelain
```

#### worktree remove - worktreeを削除
```bash
# worktreeを削除
git worktree remove <パス>

# 強制削除（変更があっても削除）
git worktree remove <パス> --force
```

#### worktree prune - 不要なworktree情報を削除
```bash
# 削除されたworktreeの情報をクリーンアップ
git worktree prune

# ドライラン（実際には削除しない）
git worktree prune --dry-run
```

#### worktree move - worktreeを移動
```bash
# worktreeを別の場所に移動
git worktree move <現在のパス> <新しいパス>
```

#### worktree lock/unlock - worktreeをロック
```bash
# worktreeをロック（削除防止）
git worktree lock <パス>

# worktreeのロックを解除
git worktree unlock <パス>
```

### 1-5. 実践演習：複数ブランチでの同時作業

#### シナリオ
あなたは新機能を開発中に、緊急のバグ修正依頼を受けました。
Worktreeを使って、両方のタスクを効率的に処理します。

#### 演習の実施

**ステップ1: プロジェクトのセットアップ**
```bash
# プロジェクトディレクトリを作成
mkdir my-app
cd my-app
git init

# 初期ファイルを作成
cat > app.py << 'EOF'
def calculate(a, b):
    return a + b

def main():
    result = calculate(5, 3)
    print(f"Result: {result}")

if __name__ == "__main__":
    main()
EOF

git add app.py
git commit -m "Initial version"
```

**ステップ2: 新機能の開発を開始**
```bash
# 新機能用のworktreeを作成
git worktree add ../my-app-feature feature/multiply

# 新機能のworktreeで作業
cd ../my-app-feature

# 乗算機能を追加
cat > app.py << 'EOF'
def calculate(a, b):
    return a + b

def multiply(a, b):
    return a * b

def main():
    result = calculate(5, 3)
    print(f"Result: {result}")

    product = multiply(4, 6)
    print(f"Product: {product}")

if __name__ == "__main__":
    main()
EOF

git add app.py
git commit -m "Add multiply function"
```

**ステップ3: 緊急バグ修正**
```bash
# 元のディレクトリに戻らず、直接hotfixのworktreeを作成
cd ../my-app

# バグ修正用のworktreeを作成
git worktree add ../my-app-hotfix hotfix/calculation-bug

# バグ修正のworktreeで作業
cd ../my-app-hotfix

# バグを修正（calculate関数に入力チェックを追加）
cat > app.py << 'EOF'
def calculate(a, b):
    if not isinstance(a, (int, float)) or not isinstance(b, (int, float)):
        raise ValueError("Both arguments must be numbers")
    return a + b

def main():
    try:
        result = calculate(5, 3)
        print(f"Result: {result}")
    except ValueError as e:
        print(f"Error: {e}")

if __name__ == "__main__":
    main()
EOF

git add app.py
git commit -m "Fix: Add input validation to calculate function"
```

**ステップ4: mainブランチにバグ修正をマージ**
```bash
# mainブランチのworktreeに移動
cd ../my-app

# hotfixをマージ
git merge hotfix/calculation-bug
```

**ステップ5: 新機能の開発を継続**
```bash
# feature worktreeに戻る
cd ../my-app-feature

# mainの変更を取り込む
git merge main

# 開発を継続...
```

**ステップ6: クリーンアップ**
```bash
# 不要になったworktreeを削除
cd ../my-app
git worktree remove ../my-app-hotfix
git worktree remove ../my-app-feature

# worktreeの状態を確認
git worktree list
```

---

## 中級者編：実践的な活用法

### 2-1. プロジェクト構造のベストプラクティス

#### 推奨ディレクトリ構造

**パターン1: サブディレクトリ方式**
```
project/
├── main/              # メインのworktree（mainブランチ）
│   ├── .git/
│   └── src/
├── feature-a/         # feature-a用worktree
│   └── src/
├── feature-b/         # feature-b用worktree
│   └── src/
└── hotfix/            # hotfix用worktree
    └── src/
```

```bash
# セットアップ例
mkdir project && cd project
git clone <repo-url> main
cd main
git worktree add ../feature-a feature-a
git worktree add ../feature-b feature-b
```

**パターン2: 並列ディレクトリ方式**
```
workspace/
├── myproject/         # メインのworktree
│   ├── .git/
│   └── src/
├── myproject-dev/     # 開発用worktree
│   └── src/
└── myproject-test/    # テスト用worktree
    └── src/
```

**パターン3: 用途別分類方式**
```
repos/
└── myproject/
    ├── .git/          # ベアリポジトリ化
    ├── main/          # 本番環境相当
    ├── develop/       # 開発環境
    ├── features/
    │   ├── feature-1/
    │   └── feature-2/
    └── releases/
        └── v1.0/
```

### 2-2. 実践的なユースケース

#### ユースケース1: フロントエンドとバックエンドの同時開発

```bash
# プロジェクトルート
cd my-fullstack-app

# フロントエンド用worktree
git worktree add ../my-fullstack-app-frontend feature/new-ui

# バックエンド用worktree
git worktree add ../my-fullstack-app-backend feature/api-endpoint

# それぞれのworktreeで開発サーバーを起動
cd ../my-fullstack-app-frontend
npm run dev  # localhost:3000

cd ../my-fullstack-app-backend
npm run server  # localhost:8000

# 統合テストは別のworktreeで
cd ../my-fullstack-app
npm run test:integration
```

#### ユースケース2: リリース準備と開発の並行作業

```bash
# 開発を継続
cd ~/project/main
git worktree add ../project-release release/v2.0

# リリース作業（別ターミナル）
cd ~/project/project-release
npm run build
npm run test:e2e
git tag v2.0.0

# 開発は影響を受けない（同時進行）
cd ~/project/main
# 開発を継続...
```

#### ユースケース3: コードレビューの効率化

```bash
# レビュー対象のPRごとにworktreeを作成
git worktree add ../review-pr-123 pr-123
git worktree add ../review-pr-124 pr-124

# それぞれをIDEで開いて比較
code ../review-pr-123
code ../review-pr-124

# レビュー完了後、まとめて削除
git worktree remove ../review-pr-123
git worktree remove ../review-pr-124
```

#### ユースケース4: マルチバージョンテスト

```bash
# 複数のバージョンで動作確認
git worktree add ../test-v1.0 v1.0.0
git worktree add ../test-v2.0 v2.0.0
git worktree add ../test-latest main

# それぞれでテストを実行
cd ../test-v1.0 && npm test
cd ../test-v2.0 && npm test
cd ../test-latest && npm test
```

### 2-3. IDEとの統合

#### Visual Studio Code

**複数のworktreeを同時に開く**
```bash
# 複数のworktreeを別ウィンドウで開く
code ~/project/main
code ~/project/feature-a
code ~/project/feature-b

# またはワークスペースファイルを作成
cat > project.code-workspace << 'EOF'
{
  "folders": [
    { "path": "~/project/main" },
    { "path": "~/project/feature-a" },
    { "path": "~/project/feature-b" }
  ]
}
EOF

code project.code-workspace
```

#### JetBrains IDEs (IntelliJ, WebStorm, PyCharm等)

```bash
# 各worktreeをプロジェクトとして開く
# File > Open > worktreeのディレクトリを選択

# または、複数のプロジェクトウィンドウを開く
# File > New > Project from Existing Sources
```

### 2-4. CI/CDとの連携

#### GitHub Actionsでの活用例

```yaml
# .github/workflows/multi-version-test.yml
name: Multi-version Test

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        branch: [main, develop, release/v1.0]

    steps:
      - uses: actions/checkout@v3

      - name: Setup worktrees
        run: |
          git worktree add ../test-${{ matrix.branch }} ${{ matrix.branch }}
          cd ../test-${{ matrix.branch }}

      - name: Run tests
        run: |
          cd ../test-${{ matrix.branch }}
          npm install
          npm test
```

### 2-5. パフォーマンス最適化

#### 大規模リポジトリでの高速化

**Sparse Checkout と組み合わせる**
```bash
# 必要なファイルだけをチェックアウト
git worktree add --no-checkout ../my-app-docs docs-branch
cd ../my-app-docs
git sparse-checkout init --cone
git sparse-checkout set docs/
git checkout docs-branch
```

**Shallow Clone と組み合わせる**
```bash
# 最新の履歴のみでworktreeを作成
git clone --depth 1 <repo-url>
cd repo
git worktree add ../feature-work -b feature-branch
```

### 2-6. チーム開発での運用ルール

#### 推奨される運用ルール

1. **Worktree命名規則**
```bash
# プロジェクト名-用途-ブランチ名
myapp-feature-user-auth
myapp-hotfix-security-patch
myapp-review-pr-123
```

2. **定期的なクリーンアップ**
```bash
# 週次クリーンアップスクリプト
#!/bin/bash
# cleanup-worktrees.sh

echo "Current worktrees:"
git worktree list

echo "\nPruning stale worktrees..."
git worktree prune --verbose

echo "\nRemoving merged branches' worktrees..."
# マージ済みブランチのworktreeを削除するロジック
```

3. **ドキュメント化**
```markdown
# プロジェクトのREADME.md
## Worktree運用ルール

### Worktreeの作成
- 長期的な作業にはworktreeを作成
- 短期的な修正は通常のcheckoutを使用

### 命名規則
- `<project>-<type>-<description>`
- type: feature, hotfix, review, release

### クリーンアップ
- 作業完了後は必ず削除
- 週次でpruneを実行
```

---

## 上級者編：プロフェッショナルテクニック

### 3-1. Bare Repositoryとの組み合わせ

#### Bare Repositoryとは
通常のリポジトリは作業ディレクトリと.gitディレクトリの両方を持ちますが、
Bare Repositoryは.gitディレクトリのみを持つリポジトリです。

#### メリット
- 全てのブランチをworktreeとして管理
- クリーンな構造
- サーバー側リポジトリと同様の構造

#### セットアップ方法

```bash
# 既存リポジトリをbare repositoryに変換
cd ~/projects
git clone --bare <repo-url> myproject.git
cd myproject.git

# メインブランチのworktreeを作成
git worktree add ../myproject-main main

# 開発ブランチのworktreeを作成
git worktree add ../myproject-dev develop

# 構造
projects/
├── myproject.git/           # Bare repository
├── myproject-main/          # mainブランチのworktree
└── myproject-dev/           # developブランチのworktree
```

#### 実践的なセットアップスクリプト

```bash
#!/bin/bash
# setup-worktree-project.sh

REPO_URL=$1
PROJECT_NAME=$2

if [ -z "$REPO_URL" ] || [ -z "$PROJECT_NAME" ]; then
    echo "Usage: $0 <repo-url> <project-name>"
    exit 1
fi

# Bare repositoryをクローン
git clone --bare "$REPO_URL" "${PROJECT_NAME}.git"
cd "${PROJECT_NAME}.git"

# 主要なブランチのworktreeを作成
git worktree add "../${PROJECT_NAME}-main" main
git worktree add "../${PROJECT_NAME}-develop" develop

echo "Setup complete!"
echo "Bare repository: ${PROJECT_NAME}.git"
echo "Main worktree: ${PROJECT_NAME}-main"
echo "Develop worktree: ${PROJECT_NAME}-develop"

# worktree一覧を表示
git worktree list
```

使用例：
```bash
./setup-worktree-project.sh https://github.com/user/repo.git myproject
```

### 3-2. 高度な自動化

#### Worktree管理スクリプト

**新しいworktreeを素早く作成**
```bash
#!/bin/bash
# wt-add.sh - Worktree作成のラッパースクリプト

BRANCH_NAME=$1
TYPE=${2:-feature}  # feature, hotfix, release

if [ -z "$BRANCH_NAME" ]; then
    echo "Usage: $0 <branch-name> [type]"
    exit 1
fi

# プロジェクト名を取得
PROJECT_NAME=$(basename $(git rev-parse --show-toplevel))

# worktreeのパスを生成
WORKTREE_PATH="../${PROJECT_NAME}-${TYPE}-${BRANCH_NAME}"

# worktreeを作成
git worktree add "$WORKTREE_PATH" -b "$TYPE/$BRANCH_NAME"

# 作成したworktreeに移動
cd "$WORKTREE_PATH"

# 依存関係をインストール（オプション）
if [ -f "package.json" ]; then
    echo "Installing npm dependencies..."
    npm install
fi

echo "Worktree created: $WORKTREE_PATH"
```

**使用例**
```bash
# feature/user-authenticationブランチのworktreeを作成
./wt-add.sh user-authentication feature

# hotfix/security-patchブランチのworktreeを作成
./wt-add.sh security-patch hotfix
```

#### 自動クリーンアップスクリプト

```bash
#!/bin/bash
# wt-clean.sh - マージ済みworktreeを自動削除

echo "Checking for merged branches..."

# マージ済みのブランチを取得（main以外）
MERGED_BRANCHES=$(git branch --merged main | grep -v "main" | grep -v "*")

if [ -z "$MERGED_BRANCHES" ]; then
    echo "No merged branches found."
    exit 0
fi

echo "Merged branches:"
echo "$MERGED_BRANCHES"
echo ""

# 各worktreeをチェック
git worktree list --porcelain | grep "^worktree" | cut -d' ' -f2 | while read WORKTREE_PATH; do
    cd "$WORKTREE_PATH"
    BRANCH=$(git branch --show-current)

    # ブランチがマージ済みかチェック
    if echo "$MERGED_BRANCHES" | grep -q "$BRANCH"; then
        echo "Removing worktree: $WORKTREE_PATH (branch: $BRANCH)"
        git worktree remove "$WORKTREE_PATH"
    fi
done

# pruneを実行
git worktree prune
echo "Cleanup complete!"
```

#### Git Aliasで効率化

```bash
# Gitの設定に追加
git config --global alias.wt-list 'worktree list'
git config --global alias.wt-add 'worktree add'
git config --global alias.wt-remove 'worktree remove'
git config --global alias.wt-prune 'worktree prune'

# 使用例
git wt-list
git wt-add ../feature-branch feature-branch
git wt-remove ../feature-branch
```

### 3-3. 大規模プロジェクトでの運用

#### モノレポでのWorktree活用

```bash
# モノレポの構造
monorepo.git/              # Bare repository
├── main/
│   ├── packages/
│   │   ├── frontend/
│   │   ├── backend/
│   │   └── shared/
│   └── apps/
│       ├── web/
│       └── mobile/
├── frontend-feature/      # frontend専用のworktree
│   └── packages/frontend/
└── backend-feature/       # backend専用のworktree
    └── packages/backend/
```

#### マイクロサービスでの活用

```bash
# 各サービスごとにworktreeを作成
project/
├── infrastructure.git/    # インフラ管理用bare repo
├── service-auth/          # 認証サービス
├── service-user/          # ユーザー管理サービス
├── service-payment/       # 決済サービス
└── service-notification/  # 通知サービス

# 全サービスを一括で更新
#!/bin/bash
for service in service-*; do
    cd "$service"
    git pull origin main
    docker-compose up -d
    cd ..
done
```

### 3-4. トラブルシューティングの高度なテクニック

#### Worktreeが破損した場合の修復

```bash
# worktreeの情報を確認
cat .git/worktrees/*/gitdir

# 存在しないパスを参照している場合
git worktree prune

# 手動でworktree情報を削除
rm -rf .git/worktrees/<worktree-name>

# worktreeディレクトリが残っている場合
rm -rf <worktree-path>
```

#### ロックされたWorktreeの解除

```bash
# worktreeがロックされているか確認
git worktree list

# ロックを解除
git worktree unlock <path>

# 強制的にロックファイルを削除（最終手段）
rm .git/worktrees/*/locked
```

#### Worktree間でのstash共有

```bash
# worktree-Aで作業中
cd worktree-A
git stash save "Work in progress"

# worktree-Bに移動して同じstashを適用
cd ../worktree-B
git stash list
git stash apply stash@{0}
```

### 3-5. セキュリティとアクセス制御

#### 機密情報の扱い

```bash
# 各worktreeで異なる環境変数を設定
# worktree-production/.env
DATABASE_URL=postgres://prod-server/db

# worktree-development/.env
DATABASE_URL=postgres://dev-server/db

# .gitignore に追加
echo ".env" >> .gitignore
```

#### Worktreeごとのフック

```bash
# 各worktreeに独自のhookを設定
cd worktree-A
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
echo "Running worktree-A specific checks..."
npm run lint
EOF
chmod +x .git/hooks/pre-commit

cd ../worktree-B
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
echo "Running worktree-B specific checks..."
npm run test
EOF
chmod +x .git/hooks/pre-commit
```

### 3-6. パフォーマンスモニタリング

#### Worktreeのディスク使用量を監視

```bash
#!/bin/bash
# worktree-disk-usage.sh

echo "Worktree Disk Usage Report"
echo "=========================="

git worktree list --porcelain | grep "^worktree" | cut -d' ' -f2 | while read WORKTREE; do
    SIZE=$(du -sh "$WORKTREE" 2>/dev/null | cut -f1)
    BRANCH=$(cd "$WORKTREE" && git branch --show-current 2>/dev/null)
    echo "$SIZE    $BRANCH    $WORKTREE"
done | sort -h
```

#### Git操作のパフォーマンス比較

```bash
#!/bin/bash
# benchmark-worktree.sh

echo "Benchmarking: git checkout vs git worktree"

# git checkoutの計測
time_checkout_start=$(date +%s.%N)
git checkout feature-branch
git checkout main
time_checkout_end=$(date +%s.%N)
checkout_time=$(echo "$time_checkout_end - $time_checkout_start" | bc)

# git worktreeの計測（既に存在する前提）
time_worktree_start=$(date +%s.%N)
cd ../project-feature
cd ../project
time_worktree_end=$(date +%s.%N)
worktree_time=$(echo "$time_worktree_end - $time_worktree_start" | bc)

echo "git checkout: ${checkout_time}s"
echo "git worktree: ${worktree_time}s"
echo "Speedup: $(echo "scale=2; $checkout_time / $worktree_time" | bc)x"
```

---

## トラブルシューティング

### 4-1. よくあるエラーと解決方法

#### エラー1: "fatal: '<path>' is already checked out at '<other-path>'"

**原因**: 同じブランチを複数のworktreeでチェックアウトしようとした

**解決方法**:
```bash
# 方法1: 別のブランチ名を使用
git worktree add <path> -b new-branch-name

# 方法2: 既存のworktreeを削除してから作成
git worktree remove <other-path>
git worktree add <path> branch-name

# 方法3: --detachオプションを使用（detached HEAD）
git worktree add --detach <path> branch-name
```

#### エラー2: "fatal: '<path>' already exists"

**原因**: 指定したパスに既にディレクトリが存在する

**解決方法**:
```bash
# ディレクトリの内容を確認
ls -la <path>

# 不要なら削除
rm -rf <path>

# またはforceオプション（注意して使用）
git worktree add --force <path> branch-name
```

#### エラー3: "error: cannot lock ref"

**原因**: 参照がロックされている、または破損している

**解決方法**:
```bash
# ロックファイルを削除
rm .git/refs/heads/<branch-name>.lock

# または、gitのガベージコレクションを実行
git gc

# それでも解決しない場合、reflogから復旧
git reflog
git checkout -b <branch-name> <commit-hash>
```

#### エラー4: "worktree list shows removed worktrees"

**原因**: worktreeを手動で削除したが、Git管理情報が残っている

**解決方法**:
```bash
# worktree情報をクリーンアップ
git worktree prune

# 詳細を確認しながらprune
git worktree prune --verbose

# 手動で削除（最終手段）
ls .git/worktrees/
rm -rf .git/worktrees/<worktree-name>
```

### 4-2. デバッグテクニック

#### Worktree状態の詳細確認

```bash
# 全worktreeの詳細情報
git worktree list --porcelain

# 特定のworktreeの情報を確認
cat .git/worktrees/<worktree-name>/gitdir
cat .git/worktrees/<worktree-name>/HEAD
cat .git/worktrees/<worktree-name>/ORIG_HEAD
```

#### ログで問題を追跡

```bash
# Git操作のログを有効化
export GIT_TRACE=1
export GIT_TRACE_SETUP=1

# worktree操作を実行
git worktree add ../test-branch test-branch

# ログを無効化
unset GIT_TRACE
unset GIT_TRACE_SETUP
```

### 4-3. 予防策とベストプラクティス

#### チェックリスト

**作成前**
- [ ] ディスク容量は十分か？
- [ ] ブランチ名は適切か？
- [ ] 既存のworktreeと競合しないか？

**作業中**
- [ ] 定期的にcommitしているか？
- [ ] 不要なファイルが増えていないか？
- [ ] 他のworktreeとの同期は取れているか？

**削除前**
- [ ] 未コミットの変更はないか？
- [ ] リモートにpush済みか？
- [ ] 必要なファイルをバックアップしたか？

#### 定期メンテナンススクリプト

```bash
#!/bin/bash
# worktree-maintenance.sh

echo "=== Worktree Maintenance Script ==="
echo ""

# 1. 現在のworktree一覧を表示
echo "1. Current worktrees:"
git worktree list
echo ""

# 2. 古いworktreeを検出（30日以上変更なし）
echo "2. Old worktrees (no changes in 30 days):"
git worktree list --porcelain | grep "^worktree" | cut -d' ' -f2 | while read WT; do
    LAST_COMMIT=$(cd "$WT" && git log -1 --format=%cd --date=unix 2>/dev/null)
    NOW=$(date +%s)
    DAYS_OLD=$(( ($NOW - $LAST_COMMIT) / 86400 ))
    if [ $DAYS_OLD -gt 30 ]; then
        echo "  $WT (${DAYS_OLD} days old)"
    fi
done
echo ""

# 3. pruneを実行
echo "3. Pruning stale worktree information..."
git worktree prune --verbose
echo ""

# 4. ディスク使用量を表示
echo "4. Disk usage:"
git worktree list --porcelain | grep "^worktree" | cut -d' ' -f2 | while read WT; do
    du -sh "$WT" 2>/dev/null
done
echo ""

# 5. リポジトリの整合性チェック
echo "5. Repository integrity check:"
git fsck --no-progress
echo ""

echo "Maintenance complete!"
```

### 4-4. データリカバリー

#### 誤って削除したWorktreeの復旧

```bash
# reflogから削除前の状態を確認
git reflog

# ブランチを復元
git branch <branch-name> <commit-hash>

# worktreeを再作成
git worktree add <path> <branch-name>
```

#### 未コミットの変更を復旧

```bash
# worktreeが削除されたが、ファイルが残っている場合
cd <old-worktree-path>

# 変更を確認
git status

# 新しいworktreeを作成して移行
cd <git-repo>
git worktree add <new-path> -b recovery-branch
cp -r <old-worktree-path>/* <new-path>/
cd <new-path>
git add .
git commit -m "Recovered changes"
```

### 4-5. FAQ（よくある質問）

**Q1: Worktreeを使うとリポジトリのサイズは増えますか？**

A: worktree自体は作業ディレクトリのコピーですが、.gitディレクトリは共有されるため、
リポジトリの履歴データは増えません。ただし、作業ファイルやnode_modules等は
各worktreeに存在するため、その分のディスク容量は必要です。

**Q2: 同じブランチを複数のworktreeで使えますか？**

A: いいえ、通常は同じブランチを複数のworktreeで同時にチェックアウトできません。
これは競合を防ぐための仕様です。どうしても必要な場合は、`--detach`オプションを
使用してdetached HEAD状態にする必要があります。

**Q3: Worktreeをリモートサーバーにプッシュできますか？**

A: worktree自体は単なる作業ディレクトリなので、通常のGit操作（commit, push等）が
可能です。各worktreeから独立してpushできます。

**Q4: Worktreeはサブモジュールと一緒に使えますか？**

A: はい、使えます。各worktreeでサブモジュールも独立して管理されます。

```bash
git worktree add ../with-submodule feature-branch
cd ../with-submodule
git submodule update --init --recursive
```

**Q5: Worktreeを使うとCIパイプラインに影響しますか？**

A: worktreeはローカルの作業ディレクトリ管理の機能なので、リモートリポジトリや
CIパイプラインには影響しません。通常通りpush/pullが動作します。

---

## まとめ

### Git Worktreeを使うべきケース

✅ **推奨**
- 複数のブランチで同時に作業したい
- ホットフィックスと機能開発を並行したい
- コードレビューを効率化したい
- ビルド時間が長いプロジェクト
- 複数バージョンのテストを実行したい

❌ **非推奨**
- 短時間の簡単なブランチ切り替え
- ディスク容量が限られている
- 小規模なプロジェクト（ファイル数が少ない）
- 1つのタスクに集中する場合

### 主要コマンドまとめ

| コマンド | 説明 | 使用頻度 |
|---------|------|---------|
| `git worktree add <path> <branch>` | 新しいworktreeを作成 | ⭐⭐⭐ |
| `git worktree list` | worktree一覧を表示 | ⭐⭐⭐ |
| `git worktree remove <path>` | worktreeを削除 | ⭐⭐⭐ |
| `git worktree prune` | 古い情報をクリーンアップ | ⭐⭐ |
| `git worktree move <old> <new>` | worktreeを移動 | ⭐ |
| `git worktree lock <path>` | worktreeをロック | ⭐ |

### さらに学ぶために

#### 公式ドキュメント
- [Git Documentation - git-worktree](https://git-scm.com/docs/git-worktree)
- [Pro Git Book](https://git-scm.com/book/en/v2)

#### 推奨される練習プロジェクト
1. 個人のサイドプロジェクトでworktreeを試す
2. 複数のブランチで同時にコードレビューを実施
3. 本番環境とステージング環境を別worktreeで管理
4. モノレポでサービスごとにworktreeを作成

#### コミュニティ
- Stack Overflow: `[git-worktree]`タグ
- GitHub Discussions: Git関連のディスカッション
- Git公式メーリングリスト

---

## クイックリファレンス

### コマンドチートシート

```bash
# === 基本操作 ===
# 新しいworktreeを作成
git worktree add <path> <branch>

# 新しいブランチを作成してworktree追加
git worktree add <path> -b <new-branch>

# 既存ブランチからworktree作成
git worktree add <path> <existing-branch>

# === 確認 ===
# worktree一覧
git worktree list

# 詳細表示
git worktree list --porcelain

# === 削除 ===
# worktreeを削除
git worktree remove <path>

# 強制削除
git worktree remove <path> --force

# 古い情報をクリーンアップ
git worktree prune

# === その他 ===
# worktreeを移動
git worktree move <old-path> <new-path>

# worktreeをロック
git worktree lock <path>

# ロックを解除
git worktree unlock <path>
```

### トラブルシューティングチートシート

```bash
# === 問題解決 ===
# worktree情報が残っている場合
git worktree prune

# ロックされたworktreeを解除
git worktree unlock <path>

# 強制削除
git worktree remove --force <path>

# worktree情報を手動削除
rm -rf .git/worktrees/<name>

# リポジトリの整合性チェック
git fsck

# === デバッグ ===
# 詳細情報を表示
git worktree list --porcelain

# worktree設定を確認
cat .git/worktrees/*/gitdir

# Git操作のトレース
export GIT_TRACE=1
git worktree <command>
unset GIT_TRACE
```

---

**このハンドブックを活用して、Git Worktreeを使った効率的な開発を実現しましょう！**

---

*最終更新: 2025-11-17*
*Version: 1.0.0*

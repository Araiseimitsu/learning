# gtr (Git Worktree Runner) - Python プロジェクト実践ガイド

## 概要

**gtr** は CodeRabbitAI が開発した git worktree を簡単に管理するための CLI ツールです。複数のブランチで並行作業を行う際に、worktree の作成、エディタの起動、AI ツールの統合、依存関係のインストールなどを自動化します。

### 主な特徴

- **シンプルなコマンド体系**: `gtr new`, `gtr editor`, `gtr ai` など直感的なコマンド
- **エディタ統合**: Cursor、VS Code、Zed などのエディタを一発で起動
- **AI ツールサポート**: Aider、Claude Code などの AI コーディングツールとの統合
- **自動化フック**: worktree 作成時/削除時に自動実行されるスクリプト
- **設定ファイルコピー**: `.env` や設定ファイルの自動コピー
- **Python 対応**: 依存関係の自動インストールなど Python プロジェクトに最適

---

## インストール

### macOS / Linux

```bash
# リポジトリをクローン
git clone https://github.com/coderabbitai/git-worktree-runner.git
cd git-worktree-runner

# シンボリックリンクを作成
sudo ln -s "$(pwd)/bin/gtr" /usr/local/bin/gtr

# インストール確認
gtr --version
```

### Windows

Git Bash または WSL を使用して同様の手順でインストールします。

### 必要要件

- Git 2.5 以上
- Bash 3.2 以上（4.0 以上を推奨）

---

## 基本コマンド

### worktree の作成

```bash
# 新しいブランチで worktree を作成
gtr new feature/user-authentication

# 既存のブランチで worktree を作成
gtr new bugfix/login-error
```

### worktree の一覧表示

```bash
# すべての worktree を表示
gtr list
```

### worktree の削除

```bash
# worktree のみ削除（ブランチは残す）
gtr rm feature/user-authentication

# worktree とブランチの両方を削除
gtr rm feature/user-authentication --delete-branch
```

### エディタで開く

```bash
# デフォルトエディタで worktree を開く
gtr editor feature/user-authentication

# 省略形（e でも可）
gtr e feature/user-authentication
```

### AI ツールを起動

```bash
# AI コーディングツールを起動
gtr ai feature/user-authentication

# 省略形
gtr a feature/user-authentication
```

### worktree に移動

```bash
# worktree のパスを取得（cd と組み合わせて使用）
cd "$(gtr go feature/user-authentication)"

# または
gtr go feature/user-authentication
```

---

## Python プロジェクトでの設定

### 1. 基本設定

```bash
# worktree の保存場所を設定
gtr config set gtr.worktrees.dir ~/worktrees

# worktree のプレフィックスを設定
gtr config set gtr.worktrees.prefix wt-

# デフォルトエディタを設定
gtr config set gtr.editor.default cursor

# デフォルト AI ツールを設定（Claude Code の場合）
gtr config set gtr.ai.default claude
```

### 2. Python 依存関係の自動インストール

worktree 作成時に自動的に依存関係をインストールするように設定します。

#### requirements.txt を使用する場合

```bash
gtr config add gtr.hook.postCreate "pip install -r requirements.txt"
```

#### poetry を使用する場合

```bash
gtr config add gtr.hook.postCreate "poetry install"
```

#### pipenv を使用する場合

```bash
gtr config add gtr.hook.postCreate "pipenv install"
```

#### 仮想環境も自動作成する場合

```bash
# venv を作成してから依存関係をインストール
gtr config add gtr.hook.postCreate "python -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt"
```

### 3. 設定ファイルのコピー

開発に必要な設定ファイルを自動的にコピーします。

```bash
# .env ファイルをコピー（例：.env.local から .env へ）
gtr config add gtr.copy.include ".env.local:.env"

# pyproject.toml をコピー
gtr config add gtr.copy.include "pyproject.toml"

# pytest 設定をコピー
gtr config add gtr.copy.include "pytest.ini"
gtr config add gtr.copy.include ".coveragerc"

# VS Code 設定をコピー
gtr config add gtr.copy.include ".vscode/settings.json"
```

### 4. 機密情報の除外

本番環境の認証情報などは除外します。

```bash
# 本番環境の .env は除外
gtr config add gtr.copy.exclude ".env.production"

# 秘密鍵などを除外
gtr config add gtr.copy.exclude "*.key"
gtr config add gtr.copy.exclude "credentials.json"
```

---

## Python プロジェクトでの実践的なワークフロー

### シナリオ 1: 新機能の開発

FastAPI プロジェクトで新しい API エンドポイントを追加する例です。

```bash
# 1. 新しいブランチで worktree を作成
gtr new feature/add-user-profile-api

# 2. worktree が作成され、自動的に以下が実行される：
#    - requirements.txt から依存関係がインストールされる
#    - .env.local が .env としてコピーされる
#    - pyproject.toml がコピーされる

# 3. エディタで開く
gtr editor feature/add-user-profile-api

# または AI ツールを起動
gtr ai feature/add-user-profile-api

# 4. 開発作業を実施
# （この時点でエディタまたは AI ツールで作業）

# 5. テストを実行（メインの worktree に影響を与えずに）
cd "$(gtr go feature/add-user-profile-api)"
pytest tests/

# 6. 作業完了後、worktree を削除
gtr rm feature/add-user-profile-api
```

### シナリオ 2: バグ修正と機能開発の並行作業

```bash
# メインブランチで新機能を開発中
cd ~/projects/my-django-app

# 緊急のバグ修正が必要になった場合
gtr new hotfix/fix-login-timeout

# 別の worktree でバグ修正作業
gtr editor hotfix/fix-login-timeout

# バグ修正完了後、PR を作成してマージ
cd "$(gtr go hotfix/fix-login-timeout)"
git push origin hotfix/fix-login-timeout

# メインの開発作業に戻る（元のディレクトリで作業継続）
cd ~/projects/my-django-app

# バグ修正の worktree を削除
gtr rm hotfix/fix-login-timeout
```

### シナリオ 3: PR レビュー

```bash
# 現在の作業を中断せずに PR をレビュー
gtr new review/pr-123-add-authentication

# レビュー用の worktree で該当ブランチをチェックアウト
cd "$(gtr go review/pr-123-add-authentication)"
git fetch origin pull/123/head:review/pr-123-add-authentication
git checkout review/pr-123-add-authentication

# 依存関係をインストール（自動実行済み）
# テストを実行
pytest tests/

# アプリケーションを起動して動作確認
python manage.py runserver

# レビュー完了後、worktree を削除
gtr rm review/pr-123-add-authentication --delete-branch
```

### シナリオ 4: 複数の AI エージェントでの並行開発

```bash
# AI エージェント 1: バックエンド API の実装
gtr new ai/backend-api
gtr ai ai/backend-api
# → Claude Code や Aider が起動し、API 実装を開始

# AI エージェント 2: フロントエンドの実装
gtr new ai/frontend-ui
gtr ai ai/frontend-ui
# → 別のターミナルで AI ツールが起動し、UI 実装を開始

# AI エージェント 3: テストコードの作成
gtr new ai/test-coverage
gtr ai ai/test-coverage
# → さらに別のターミナルでテストコード作成を開始

# すべての作業が完了したら統合
# 各 worktree で git push してから PR を作成
```

---

## 高度な設定とカスタマイズ

### 1. プロジェクト固有の postCreate フック

```bash
# Django プロジェクトの場合
gtr config add gtr.hook.postCreate "python -m venv .venv"
gtr config add gtr.hook.postCreate "source .venv/bin/activate && pip install -r requirements.txt"
gtr config add gtr.hook.postCreate "python manage.py migrate --run-syncdb"

# Flask プロジェクトの場合
gtr config add gtr.hook.postCreate "python -m venv venv"
gtr config add gtr.hook.postCreate "source venv/bin/activate && pip install -r requirements.txt"
gtr config add gtr.hook.postCreate "flask db upgrade"
```

### 2. postRemove フック（クリーンアップ）

```bash
# worktree 削除時に仮想環境も削除
gtr config add gtr.hook.postRemove "rm -rf .venv"

# キャッシュファイルを削除
gtr config add gtr.hook.postRemove "find . -type d -name '__pycache__' -exec rm -rf {} +"
gtr config add gtr.hook.postRemove "find . -type f -name '*.pyc' -delete"
```

### 3. 環境変数の活用

フック内で使用可能な環境変数：

- `$REPO_ROOT`: リポジトリのルートパス
- `$WORKTREE_PATH`: worktree のパス
- `$BRANCH`: ブランチ名

```bash
# 環境変数を使用した高度なフック
gtr config add gtr.hook.postCreate 'echo "Created worktree for $BRANCH at $WORKTREE_PATH"'
gtr config add gtr.hook.postCreate 'cp $REPO_ROOT/.env.template $WORKTREE_PATH/.env'
```

### 4. シェル補完の有効化

#### Bash の場合

```bash
# ~/.bashrc に追加
eval "$(gtr completions bash)"
```

#### Zsh の場合

```bash
# ~/.zshrc に追加
eval "$(gtr completions zsh)"
```

#### Fish の場合

```bash
# ~/.config/fish/config.fish に追加
gtr completions fish | source
```

---

## ベストプラクティス

### 1. worktree の命名規則

一貫性のある命名規則を使用することで管理が容易になります。

```bash
# 機能開発
gtr new feature/user-profile
gtr new feature/payment-integration

# バグ修正
gtr new bugfix/login-error
gtr new bugfix/data-validation

# ホットフィックス
gtr new hotfix/security-patch

# 実験的な変更
gtr new experiment/new-architecture

# PR レビュー
gtr new review/pr-123
```

### 2. worktree の整理

定期的に不要な worktree を削除します。

```bash
# すべての worktree を確認
gtr list

# 不要な worktree を削除
gtr rm old-feature-branch --delete-branch
```

### 3. プロジェクトごとの設定

グローバル設定とプロジェクト固有の設定を使い分けます。

```bash
# グローバル設定（すべてのプロジェクトに適用）
gtr config set gtr.editor.default cursor --global
gtr config set gtr.ai.default claude --global

# プロジェクト固有の設定（現在のリポジトリのみ）
gtr config set gtr.worktrees.dir .worktrees
gtr config add gtr.hook.postCreate "poetry install"
```

### 4. .gitignore の設定

worktree ディレクトリを `.gitignore` に追加します。

```bash
# .gitignore に追加
echo ".worktrees/" >> .gitignore
echo "wt-*/" >> .gitignore
```

### 5. 依存関係の最適化

大規模なプロジェクトでは、開発時のみ必要な依存関係を分離します。

```bash
# 最小限の依存関係のみインストール
gtr config add gtr.hook.postCreate "pip install -r requirements-dev.txt"

# 必要に応じて追加の依存関係をインストール
# requirements.txt: 本番環境用
# requirements-dev.txt: 開発環境用（テストツールなど含む）
```

---

## トラブルシューティング

### 問題 1: worktree 作成後に依存関係がインストールされない

**原因**: postCreate フックが正しく設定されていない

**解決方法**:
```bash
# 現在の設定を確認
gtr config get gtr.hook.postCreate

# 設定を追加
gtr config add gtr.hook.postCreate "pip install -r requirements.txt"
```

### 問題 2: 仮想環境が正しく動作しない

**原因**: 仮想環境のパスが絶対パスになっている

**解決方法**:
```bash
# 各 worktree で個別の仮想環境を作成
cd "$(gtr go your-branch)"
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### 問題 3: 設定ファイルがコピーされない

**原因**: コピー設定が正しくない

**解決方法**:
```bash
# 設定を確認
gtr config get gtr.copy.include

# 正しい形式で追加
gtr config add gtr.copy.include ".env.local:.env"
```

### 問題 4: worktree の削除時にエラーが発生

**原因**: worktree 内でプロセスが実行中

**解決方法**:
```bash
# worktree 内のプロセスを停止
# 開発サーバーなどを終了してから削除

# 強制削除（最終手段）
git worktree remove --force path/to/worktree
```

---

## まとめ

gtr は Python プロジェクトでの並行開発を大幅に効率化するツールです。

**主な利点**:
- 複数のブランチでの同時作業が容易
- AI ツールとの統合で開発速度が向上
- 依存関係の自動インストールで初期設定が不要
- 設定ファイルの自動コピーで環境構築が簡単
- フックによる柔軟なカスタマイズが可能

**推奨される使用シーン**:
- 新機能開発中に緊急バグ修正が必要な場合
- PR レビューを現在の作業を中断せずに行う場合
- 複数の AI エージェントで並行開発する場合
- 異なる Python バージョンや依存関係のバージョンをテストする場合

gtr を活用することで、git worktree の複雑な管理作業から解放され、開発作業そのものに集中できるようになります。

---

## 参考リンク

- [gtr GitHub リポジトリ](https://github.com/coderabbitai/git-worktree-runner)
- [Git Worktree 公式ドキュメント](https://git-scm.com/docs/git-worktree)
- [CodeRabbitAI](https://github.com/coderabbitai)

---

**作成日**: 2025-11-17
**バージョン**: 1.0

# Python Install Manager 完全ガイド【2025年版】

## 目次
- [概要](#概要)
- [Python Install Manager（公式Windows版）](#python-install-manager公式windows版)
- [uv - 超高速パッケージマネージャー](#uv---超高速パッケージマネージャー)
- [mise - マルチ言語対応版バージョン管理](#mise---マルチ言語対応版バージョン管理)
- [従来のツールとの比較](#従来のツールとの比較)
- [どのツールを選ぶべきか](#どのツールを選ぶべきか)

---

## 概要

2024-2025年にかけて、Pythonのバージョン管理とパッケージ管理の世界に革新的なツールが登場しています。本ガイドでは、最新のPythonインストール管理ツールについて、初心者にもわかりやすく解説します。

### 主要な新ツール

1. **Python Install Manager** - Windows向け公式ツール（2025年10月リリース）
2. **uv** - Rust製の超高速パッケージマネージャー（2024年2月リリース）
3. **mise** - マルチ言語対応の統合開発環境管理ツール

---

## Python Install Manager（公式Windows版）

### 概要

**Python Install Manager**は、Python公式チームが2025年10月8日にリリースした、Windows向けの新しいPython管理ツールです。これにより、Windows環境でのPythonインストール方法が大きく変わります。

### 主な特徴

- ✅ **公式ツール**: Python.orgが提供する公式のインストール管理ツール
- ✅ **コマンドライン操作**: `py`コマンドを使用したモダンな管理方法
- ✅ **Microsoft Store対応**: Microsoft Storeからインストール可能
- ✅ **複数バージョン管理**: 複数のPythonバージョンを簡単に切り替え
- ✅ **自動更新**: Pythonのアップデートが簡単に

### インストール方法

#### 方法1: Microsoft Store（推奨）

1. Microsoft Storeを開く
2. "Python Install Manager"で検索
3. インストールボタンをクリック

#### 方法2: WinGet（コマンドライン）

```bash
winget install 9NQ7512CXL7T
```

### 基本的な使い方

#### Pythonバージョンの一覧表示

```bash
py list
```

#### 新しいPythonバージョンのインストール

```bash
py install 3.13
```

#### Pythonバージョンのアンインストール

```bash
py uninstall 3.12
```

#### 特定のバージョンでPythonを実行

```bash
py -3.13 your_script.py
```

### 従来のインストーラーとの違い

| 項目 | 従来のインストーラー | Python Install Manager |
|------|---------------------|------------------------|
| インストール方法 | GUIウィザード | コマンドライン |
| 複数バージョン管理 | 手動で管理 | 自動管理 |
| 更新 | 手動ダウンロード | `py install`で簡単 |
| アンインストール | コントロールパネル | `py uninstall`で簡単 |

### 今後の予定

- Python 3.16で従来のインストーラー形式が終了予定
- Python Install Managerが標準のインストール方法になる

---

## uv - 超高速パッケージマネージャー

### 概要

**uv**は、Astral社が開発したRust製のPythonパッケージマネージャーで、2024年2月にリリースされました。従来のpipと比較して、驚異的な速度を実現しています。

### 主な特徴

- 🚀 **超高速**: pipより8-10倍高速（キャッシュあり時は80-115倍）
- 🔧 **オールインワン**: pip、pip-tools、pipx、poetry、pyenv、virtualenvなどを統合
- 🐍 **自動Python管理**: 必要なPythonバージョンを自動インストール
- 📦 **互換性**: pip互換のコマンド体系
- 🌍 **クロスプラットフォーム**: Windows、macOS、Linux対応

### インストール方法

#### macOS / Linux

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

#### Windows（PowerShell）

```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

#### pipでのインストール（既存のPython環境がある場合）

```bash
pip install uv
```

### 基本的な使い方

#### 仮想環境の作成

```bash
uv venv
```

#### パッケージのインストール

```bash
uv pip install requests numpy pandas
```

#### requirements.txtからのインストール

```bash
uv pip install -r requirements.txt
```

#### パッケージの更新

```bash
uv pip install --upgrade requests
```

#### Pythonバージョンの管理

```bash
# Pythonバージョンのインストール
uv python install 3.13

# インストール済みPythonバージョンの確認
uv python list

# 特定バージョンで仮想環境を作成
uv venv --python 3.13
```

### パフォーマンス比較

| 操作 | pip | uv（キャッシュなし） | uv（キャッシュあり） |
|------|-----|---------------------|---------------------|
| 仮想環境の再作成 | 100秒 | 10秒 | 1秒 |
| 依存関係の更新 | 50秒 | 5秒 | 0.5秒 |

### uvの対象ユーザー

- ✅ 高速なパッケージインストールを求める開発者
- ✅ 複数のツール（pip、poetry、pyenvなど）を統合したい人
- ✅ CI/CDパイプラインでビルド時間を短縮したい人
- ✅ モダンなPython開発環境を構築したい人

---

## mise - マルチ言語対応版バージョン管理

### 概要

**mise**（旧名：rtx）は、Python以外の言語にも対応したマルチ言語版バージョン管理ツールです。asdf、direnv、pyenvの機能を統合し、開発環境全体を管理できます。

### 主な特徴

- 🌐 **マルチ言語対応**: Python、Node.js、Ruby、Go、Terraformなど数百の言語に対応
- 🔄 **自動切り替え**: ディレクトリごとに自動でバージョンを切り替え
- 🔒 **仮想環境自動管理**: プロジェクトディレクトリに入ると自動でvirtualenvを有効化
- 🛠️ **タスクランナー**: タスク実行機能を内蔵
- 📝 **環境変数管理**: プロジェクトごとの環境変数を管理

### インストール方法

#### macOS / Linux

```bash
curl https://mise.run | sh
```

#### Homebrew（macOS）

```bash
brew install mise
```

### 基本的な使い方

#### Pythonバージョンのインストール

```bash
mise install python@3.13
```

#### グローバルバージョンの設定

```bash
mise use --global python@3.13
```

#### プロジェクトごとのバージョン設定

```bash
cd your-project
mise use python@3.12
```

これにより、`.mise.toml`ファイルが作成され、プロジェクト設定が保存されます。

#### .mise.tomlの例

```toml
[tools]
python = "3.13"
node = "20"

[env]
DATABASE_URL = "postgresql://localhost/mydb"

[tasks.test]
run = "pytest"

[tasks.dev]
run = "python manage.py runserver"
```

#### インストール済みバージョンの確認

```bash
mise list
```

#### タスクの実行

```bash
mise run test   # pytestを実行
mise run dev    # 開発サーバーを起動
```

### miseの対象ユーザー

- ✅ 複数の言語を扱うフルスタック開発者
- ✅ プロジェクトごとに環境を自動切り替えしたい人
- ✅ asdf、pyenv、direnvを統合したい人
- ✅ チーム開発で環境を統一したい人

---

## 従来のツールとの比較

### 機能比較表

| 機能 | Python Install Manager | uv | mise | pyenv | pip |
|------|------------------------|-----|------|-------|-----|
| Pythonバージョン管理 | ✅ (Windows) | ✅ | ✅ | ✅ | ❌ |
| パッケージ管理 | ❌ | ✅ | ❌ | ❌ | ✅ |
| 仮想環境管理 | ❌ | ✅ | ✅ | ❌ | ❌ |
| 速度 | 標準 | 超高速 | 高速 | 標準 | 標準 |
| マルチ言語対応 | ❌ | ❌ | ✅ | ❌ | ❌ |
| Windows対応 | ✅ (公式) | ✅ | ✅ | ⚠️ (限定的) | ✅ |
| macOS/Linux対応 | ❌ | ✅ | ✅ | ✅ | ✅ |
| 公式サポート | ✅ | ❌ | ❌ | ❌ | ✅ |

---

## どのツールを選ぶべきか

### シナリオ別おすすめツール

#### Windows環境でPythonを始める初心者

**👉 Python Install Manager**

理由:
- Python公式の推奨ツール
- Microsoft Storeから簡単インストール
- シンプルで使いやすい

#### パッケージインストールを高速化したい

**👉 uv**

理由:
- pipの8-10倍の速度
- pip互換で移行が簡単
- CI/CDパイプラインで特に効果的

#### 複数の言語を扱うフルスタック開発

**👉 mise**

理由:
- Python、Node.js、Rubyなどを一元管理
- プロジェクトごとの自動切り替え
- 環境変数とタスク管理も統合

#### macOS/Linuxで従来のPython開発

**👉 pyenv + pip** または **uv**

理由:
- pyenvは実績豊富で安定
- uvならモダンで高速

### 組み合わせて使う

複数のツールを組み合わせることも可能です：

```bash
# 例1: mise（バージョン管理） + uv（パッケージ管理）
mise use python@3.13
uv pip install requests

# 例2: Python Install Manager（バージョン管理） + uv（パッケージ管理）
py install 3.13
uv pip install django
```

---

## まとめ

2025年のPython開発環境は、従来のツールに加えて新しい選択肢が増えました：

1. **Python Install Manager** - Windows環境の標準になる公式ツール
2. **uv** - 速度重視のモダンなパッケージマネージャー
3. **mise** - マルチ言語対応の統合開発環境管理

自分の開発スタイルやプロジェクトに合わせて、最適なツールを選択しましょう。

### 参考リンク

- [Python Install Manager 公式ページ](https://www.python.org/downloads/latest/pymanager/)
- [Python Install Manager GitHub](https://github.com/python/pymanager)
- [uv 公式ドキュメント](https://docs.astral.sh/uv/)
- [mise 公式サイト](https://mise.jdx.dev/)
- [mise GitHub](https://github.com/jdx/mise)

---

**最終更新日**: 2025年1月

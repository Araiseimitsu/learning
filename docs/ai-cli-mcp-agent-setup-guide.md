# AI CLI ツール MCP/エージェント設定ガイド

主要なAI CLIツール（Claude Code、GitHub Copilot、Gemini CLI）のModel Context Protocol (MCP) サーバーとエージェント設定方法の包括的なガイド

---

## 目次

1. [Claude Code](#1-claude-code)
   - [MCP サーバー設定](#11-mcp-サーバー設定)
   - [エージェント設定](#12-エージェント設定slash-commands)
2. [GitHub Copilot](#2-github-copilot)
   - [カスタムエージェント設定](#21-カスタムエージェント設定)
   - [MCP サーバー設定](#22-mcp-サーバー設定)
3. [Gemini CLI](#3-gemini-cli)
   - [MCP サーバー設定](#31-mcp-サーバー設定)
   - [エージェント動作のカスタマイズ](#32-エージェント動作のカスタマイズ)
4. [比較表](#4-比較表)

---

## 1. Claude Code

### 1.1 MCP サーバー設定

#### 1.1.1 インストール方法

Claude Code は3つのトランスポート方式をサポートしています：

##### **HTTPサーバー（推奨）**
リモートMCPサーバーへの接続に推奨される方式です。クラウドベースのサービスに最適です。

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

##### **SSEサーバー（非推奨）**
Server-Sent Events トランスポートは機能していますが、推奨されなくなりました。

```bash
claude mcp add --transport sse asana https://mcp.asana.com/sse
```

##### **ローカルStdioサーバー**
ローカルプロセスとして実行され、直接的なシステムアクセスが必要なツールに適しています。

```bash
claude mcp add --transport stdio airtable \
  --env AIRTABLE_API_KEY=YOUR_KEY \
  -- npx -y airtable-mcp-server
```

#### 1.1.2 設定スコープ

MCPサーバーは3つの階層レベルで設定できます：

| スコープ | 保存場所 | ユースケース |
|---------|---------|-------------|
| **Local**（デフォルト） | ユーザープロジェクト設定 | 個人的・実験的な設定 |
| **Project** | `.mcp.json` ファイル | チーム共有サーバー（バージョン管理経由） |
| **User** | ユーザー設定 | プロジェクト横断的な個人ツール |

#### 1.1.3 設定ファイルフォーマット

プロジェクトスコープのサーバーは `.mcp.json` を使用します：

```json
{
  "mcpServers": {
    "server-name": {
      "type": "http",
      "url": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer token"
      }
    }
  }
}
```

**環境変数の展開をサポート：**
- `${VAR}` または `${VAR:-default}` の形式

#### 1.1.4 管理コマンド

```bash
# 全ての設定済みサーバーを表示
claude mcp list

# 特定サーバーの詳細を取得
claude mcp get github

# サーバーを削除
claude mcp remove github

# Claude Code内でステータス確認
/mcp
```

#### 1.1.5 認証

OAuth 2.0がクラウドサービスでサポートされています。認証は以下を通じて行われます：

```bash
/mcp  # Claude Code インターフェース内で実行
```

認証トークンは安全に保存され、自動的に更新されます。

#### 1.1.6 出力管理

コンテキストオーバーフローを防ぐため、MCPの最大出力トークン制限を設定できます：

```bash
export MAX_MCP_OUTPUT_TOKENS=50000
claude
```

出力が10,000トークンを超えると警告が表示されます。

#### 1.1.7 エンタープライズ設定

管理者は以下の場所に集中設定をデプロイできます：

- **macOS**: `/Library/Application Support/ClaudeCode/managed-mcp.json`
- **Windows**: `C:\ProgramData\ClaudeCode\managed-mcp.json`
- **Linux**: `/etc/claude-code/managed-mcp.json`

`managed-settings.json` を通じて許可リスト/拒否リストコントロールをサポートし、ユーザーが設定できるサーバーを制限できます。

#### 1.1.8 人気の統合サービス

以下のような事前設定済みサーバーが含まれています：

- **プロジェクト管理**: Asana, Linear, Monday, Jira
- **決済**: Stripe, Square, PayPal, Plaid
- **デザイン**: Figma, Canva, Cloudinary
- **インフラ**: Netlify, Vercel, Cloudflare, Stytch
- **データ**: Notion, HubSpot, Airtable, Box

---

### 1.2 エージェント設定（Slash Commands）

Claude Code では、スラッシュコマンドを使用してカスタムワークフローを定義できます。

#### 1.2.1 スラッシュコマンドの作成

`.claude/commands/` ディレクトリにマークダウンファイルを作成します：

**ファイル構造：**
```
.claude/
└── commands/
    ├── review-pr.md
    ├── test.md
    └── deploy.md
```

**例：`.claude/commands/review-pr.md`**
```markdown
---
description: Pull Requestをレビューし、改善提案を行う
---

以下のタスクを実行してください：

1. 最新のgit diffを確認
2. コード品質を分析
3. セキュリティ問題をチェック
4. 改善提案をリストアップ
```

#### 1.2.2 使用方法

```bash
# スラッシュコマンドを実行
/review-pr

# 引数付きでスラッシュコマンドを実行
/deploy production
```

#### 1.2.3 ベストプラクティス

- 明確で具体的な指示を記述
- タスクを段階的に分解
- 必要に応じて環境変数を使用
- チームで共有するためにバージョン管理にコミット

---

## 2. GitHub Copilot

### 2.1 カスタムエージェント設定

GitHub Copilot は、シンプルなファイルベースの設定を通じてコーディングエージェントを特化させることができます（2025年10月28日リリース）。

#### 2.1.1 設定ファイルの場所

カスタムエージェントは以下のディレクトリに配置します：

**リポジトリレベル：**
```
.github/
└── agents/
    ├── test-specialist.agent.md
    ├── readme-writer.agent.md
    └── api-builder.agent.md
```

**組織/エンタープライズレベル：**
```
{org}/.github/          # または {org}/.github-private
└── agents/
    └── security-reviewer.agent.md
```

**GitHub Copilot CLI用：**
```
~/.copilot/
└── agents/
    └── my-agent.agent.md
```

#### 2.1.2 ファイルフォーマット

エージェント設定ファイルは **YAML frontmatter + Markdown** の形式を使用します。

**基本構造：**

```yaml
---
name: agent-identifier
description: エージェントの専門分野と目的の説明
tools: ["read", "edit", "search", "shell"]
target: vscode  # オプション: 'vscode' または 'github-copilot'
---

# エージェントの指示

ここにエージェントの振る舞い、専門知識、運用境界を記述します。

## 役割
あなたは〇〇の専門家です...

## スコープ
- 実行すること: ...
- 実行しないこと: ...

## ガイドライン
1. ...
2. ...
```

#### 2.1.3 YAML Frontmatter プロパティ

| プロパティ | 型 | 説明 | 必須 |
|-----------|-----|------|------|
| `name` | string | エージェントの一意識別子（デフォルト: ファイル名） | ❌ |
| `description` | string | エージェントの目的と機能の説明 | ✅ |
| `target` | string | 環境指定（`vscode`, `github-copilot`） | ❌ |
| `tools` | list/string | 利用可能なツールのリスト | ❌ |
| `mcp-servers` | object | MCP サーバー設定（組織/エンタープライズのみ） | ❌ |
| `metadata` | object | カスタム名前-値ペアの注釈 | ❌ |

#### 2.1.4 Tools 設定

Toolsは3つの方法で設定できます：

**1. 全て有効化：**
```yaml
tools: ["*"]
# または省略
```

**2. 特定のツールのみ有効化：**
```yaml
tools: ["read", "edit", "search"]
```

**3. 全て無効化：**
```yaml
tools: []
```

**利用可能なツール：**
- `shell` - シェルコマンド実行
- `read` - ファイル読み取り
- `edit` - ファイル編集
- `search` - コード検索
- `custom-agent` - カスタムエージェント呼び出し

#### 2.1.5 実践例

##### **例1: テスト専門エージェント**

```yaml
---
name: test-specialist
description: テストカバレッジとテストコード生成の専門家
tools: ["*"]
---

# テスト専門エージェント

あなたはテスト作成とカバレッジ改善の専門家です。

## 責務
- 既存コードのテストケースを生成
- エッジケースとエラーハンドリングのテスト
- テストカバレッジレポートの分析

## 制約
- **本番コードは変更しない**
- テストファイルのみを対象とする
- 既存のテストフレームワークを使用
```

##### **例2: ドキュメント作成エージェント**

```yaml
---
name: readme-specialist
description: README とプロジェクトドキュメントの作成・改善専門家
tools: ["read", "search", "edit"]
target: vscode
---

# ドキュメント専門家

プロジェクトドキュメント、特にREADMEファイルに特化したエージェントです。

## 専門分野
- READMEの構造化と改善
- API ドキュメントの作成
- コントリビューションガイドの整備

## スコープ制限
- ドキュメントファイルのみを対象
- コードファイルは分析のみ（変更不可）
```

##### **例3: 実装プランナーエージェント**

```yaml
---
name: implementation-planner
description: 詳細な実装計画と技術仕様をMarkdown形式で作成
tools: ["read", "search", "edit"]
---

# 実装プランナー

技術仕様と実装計画の作成に特化したエージェントです。

## タスク
1. 要件を分析し、技術的アプローチを策定
2. 実装ステップを段階的に分解
3. 潜在的なリスクと依存関係を特定
4. Markdown形式で詳細な計画を作成

## アウトプット形式
- タスクブレークダウン
- アーキテクチャ図（Mermaid）
- API インターフェース定義
- テスト戦略

## 制約
- コード実装は行わない
- 計画とドキュメントのみに集中
```

#### 2.1.6 デプロイと使用方法

**1. 作成と配置：**
```bash
# リポジトリ内にエージェントディレクトリを作成
mkdir -p .github/agents

# エージェントファイルを作成
vim .github/agents/test-specialist.agent.md

# コミットしてデフォルトブランチにプッシュ
git add .github/agents/
git commit -m "Add test specialist agent"
git push
```

**2. 使用方法：**

- **GitHub.com**: agents タブのドロップダウンから選択
- **VS Code**: Chat ビューでエージェントを選択
- **GitHub Copilot CLI**: `/agent` スラッシュコマンドで参照
- **Issues**: Issue にエージェントを割り当て

**CLI での使用例：**
```bash
gh copilot chat --agent test-specialist "このファイルのテストを作成して"
```

#### 2.1.7 ベストプラクティス

1. **明確な役割定義を記述**
   - エージェントの専門分野を具体的に
   - 実行すること/しないことを明示

2. **ツールアクセスを制限**
   - 意図しない変更を防ぐため必要最小限のツールのみ許可
   - ドキュメント専門エージェントには `edit` を制限することを検討

3. **デプロイ前にテスト**
   - 個人リポジトリでテスト後、チームにデプロイ

4. **一貫した命名規則**
   - ファイル名: `専門分野-specialist.agent.md`
   - name: `専門分野-identifier`

5. **ドキュメント化**
   - description を詳細に記述
   - チーム向けにエージェント使用ガイドを作成

---

### 2.2 MCP サーバー設定

#### 2.2.1 設定方法

MCP サーバーはカスタムエージェント設定内で構成できます（**組織/エンタープライズレベルのみ**）。

**エージェントファイル内での設定例：**

```yaml
---
name: data-analyst
description: データ分析とレポート生成の専門家
tools: ["read", "search", "custom-agent"]
mcp-servers:
  database-connector:
    type: local
    command: node
    args:
      - /path/to/mcp-server.js
    env:
      DB_HOST: ${{ secrets.DB_HOST }}
      DB_PASSWORD: $COPILOT_MCP_DB_PASSWORD
---

# データ分析エージェント
...
```

#### 2.2.2 MCP サーバープロパティ

| プロパティ | 説明 |
|-----------|------|
| `type` | `local`（Claude Code の `stdio` に相当） |
| `command` | 実行するコマンド（例: `node`, `python`） |
| `args` | コマンド引数の配列 |
| `env` | 環境変数（シークレット参照をサポート） |

#### 2.2.3 シークレットと環境変数

**パターン：**
- `$COPILOT_MCP_ENV_VAR_VALUE` - 環境変数
- `${{ secrets.VAR_NAME }}` - リポジトリシークレット

**設定場所：**
- リポジトリの Settings > Secrets and variables > Actions

#### 2.2.4 制約事項

- **リポジトリレベルのエージェントではMCP設定不可**
- 組織/エンタープライズレベルのみサポート
- 2025年11月10日以降、GitHub App ベースの Copilot Extensions は非推奨（MCP推奨）

---

## 3. Gemini CLI

### 3.1 MCP サーバー設定

#### 3.1.1 インストール

```bash
# Gemini CLI のインストール
npm install -g @google/gemini-cli@latest

# FastMCP のインストール（v2.12.3以降）
pip install fastmcp>=2.12.3
```

#### 3.1.2 設定ファイルの場所

Gemini CLI は `settings.json` でMCPサーバーを設定します。

**設定ファイルパス：**
```
~/.gemini/settings.json
```

#### 3.1.3 設定ファイルフォーマット

**基本構造：**

```json
{
  "mcp": {
    "serverCommand": "global-command",
    "allowed": ["trusted-server"],
    "excluded": ["experimental-server"]
  },
  "mcpServers": {
    "serverName": {
      "command": "path/to/server",
      "args": ["--arg1", "value1"],
      "env": {
        "API_KEY": "$ENV_VAR"
      },
      "cwd": "./server-directory",
      "timeout": 30000,
      "trust": false
    }
  }
}
```

#### 3.1.4 設定プロパティ詳細

| プロパティ | 型 | 説明 |
|-----------|-----|------|
| `command` / `url` / `httpUrl` | string | サーバー実行ファイルまたはエンドポイント |
| `args` | array | コマンド引数 |
| `env` | object | 環境変数（`$VAR_NAME` 構文をサポート） |
| `timeout` | number | リクエストタイムアウト（ミリ秒） |
| `trust` | boolean | 確認プロンプトをバイパス |
| `includeTools` | array | 特定ツールの許可リスト |
| `excludeTools` | array | 特定ツールの拒否リスト |

#### 3.1.5 トランスポートタイプ

**1. Stdio（標準入出力）**
- サブプロセスを生成し、stdin/stdout で通信
- ローカルサーバーに最適

```json
{
  "mcpServers": {
    "local-server": {
      "command": "python",
      "args": ["server.py"],
      "env": {
        "API_KEY": "$MY_API_KEY"
      }
    }
  }
}
```

**2. SSE/HTTP（Server-Sent Events / HTTP）**
- リモートサーバー用
- HTTPストリーミングエンドポイント

```json
{
  "mcpServers": {
    "remote-server": {
      "url": "https://api.example.com/sse",
      "timeout": 60000
    }
  }
}
```

#### 3.1.6 CLIコマンドでの設定

**FastMCP を使用した自動インストール（推奨）：**

```bash
# FastMCP v2.12.3 以降
fastmcp install gemini-cli
```

このコマンドは以下を自動的に処理します：
- 設定ファイルの更新
- 依存関係の管理
- Gemini CLI の MCP 管理システムの呼び出し

**手動設定コマンド：**

```bash
# サーバーを追加
gemini mcp add my-server python server.py --port 8080

# 設定済みサーバーをリスト表示
gemini mcp list

# サーバーを削除
gemini mcp remove my-server

# ステータス確認
/mcp
```

#### 3.1.7 OAuth 認証

リモートサーバー向けに、CLI は自動 OAuth 検出をサポートします。

**認証プロバイダー：**
- `dynamic_discovery`（デフォルト）
- `google_credentials`
- `service_account_impersonation`（IAP保護サービス用）

**認証フロー：**
1. 動的クライアント登録
2. ブラウザベースの認証フロー
3. `~/.gemini/mcp-oauth-tokens.json` にトークンを安全に保存

**設定例：**

```json
{
  "mcpServers": {
    "oauth-server": {
      "url": "https://api.example.com/mcp",
      "auth": {
        "provider": "dynamic_discovery",
        "scopes": ["read", "write"]
      }
    }
  }
}
```

#### 3.1.8 ツール検出プロセス

CLI は自動的に以下を実行します：

1. 設定済みサーバーに接続
2. ツール定義を取得し検証
3. API互換性のためツール名をサニタイズ
4. プレフィックスで名前の競合を解決（`serverName__toolName`）
5. include/exclude リストに基づいてツールをフィルタリング

**ツールフィルタリング例：**

```json
{
  "mcpServers": {
    "github-server": {
      "command": "github-mcp-server",
      "includeTools": ["create_issue", "list_repos"],
      "excludeTools": ["delete_repo"]
    }
  }
}
```

#### 3.1.9 リッチコンテンツの返却

ツールは MCP の `CallToolResult` 仕様に従い、`content` 配列でマルチパート応答を返すことができます：

```json
{
  "content": [
    {
      "type": "text",
      "text": "分析結果..."
    },
    {
      "type": "image",
      "data": "base64-encoded-image",
      "mimeType": "image/png"
    }
  ]
}
```

サポート形式：
- テキスト
- 画像
- 音声
- バイナリデータ

#### 3.1.10 MCP プロンプトをスラッシュコマンドとして使用

サーバーは事前定義されたプロンプトを公開でき、スラッシュコマンドとして呼び出せます：

```bash
/poem-writer --title="Gemini CLI" --mood="reverent"
```

---

### 3.2 エージェント動作のカスタマイズ

#### 3.2.1 エージェントモード

Gemini CLI はエージェントモードでツールとMCPサーバーを活用します。

**エージェントモードでの使用：**

```bash
# エージェントモードを開始
gemini chat --agent

# 利用可能なツールを表示
/tools

# 設定済みMCPサーバーをリスト表示
/mcp
```

#### 3.2.2 カスタムプロンプトとペルソナ

settings.json でカスタムプロンプトを設定できます：

```json
{
  "defaultPrompt": "あなたは親切で知識豊富なアシスタントです。常に日本語で応答してください。",
  "systemInstructions": {
    "coding": "コードレビューでは、セキュリティとパフォーマンスを重視してください。",
    "documentation": "ドキュメントは明確で簡潔に、例を含めてください。"
  }
}
```

#### 3.2.3 実践例

**GitHub MCP サーバーの設定例：**

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "$GITHUB_TOKEN"
      },
      "includeTools": [
        "create_issue",
        "create_pull_request",
        "list_repos",
        "search_code"
      ],
      "trust": true
    }
  }
}
```

**使用方法：**

```bash
# エージェントモードで起動
gemini chat --agent

# GitHub ツールを使用
> GitHubで "bug" とラベル付けされたissueをリスト表示して

# MCPサーバーのステータス確認
> /mcp
```

---

## 4. 比較表

### 4.1 MCP サーバー設定の比較

| 機能 | Claude Code | GitHub Copilot | Gemini CLI |
|------|-------------|----------------|------------|
| **設定ファイル** | `.mcp.json` | エージェントファイル内（YAML） | `settings.json` |
| **トランスポート** | HTTP, SSE, stdio | local（stdio相当） | stdio, SSE, HTTP |
| **CLI管理** | `claude mcp add/list/remove` | 組織/エンタープライズのみ | `gemini mcp add/list/remove` |
| **自動インストール** | ✅ | ❌ | ✅（FastMCP） |
| **OAuth認証** | ✅ | ✅ | ✅ |
| **環境変数展開** | `${VAR}`, `${VAR:-default}` | `${{ secrets.VAR }}`, `$VAR` | `$VAR` |
| **スコープ** | Local, Project, User | Repository, Org, Enterprise | Global（settings.json） |
| **エンタープライズ設定** | ✅ 集中管理 | ✅ 組織レベル | ❌ |

### 4.2 エージェント/コマンド設定の比較

| 機能 | Claude Code | GitHub Copilot | Gemini CLI |
|------|-------------|----------------|------------|
| **設定方式** | Slash Commands（Markdown） | Custom Agents（YAML + Markdown） | Prompts & settings.json |
| **ファイル拡張子** | `.md` | `.agent.md` | N/A |
| **配置場所** | `.claude/commands/` | `.github/agents/` | N/A（settings.json内） |
| **ツール制御** | ❌ | ✅（tools配列） | ✅（includeTools/excludeTools） |
| **スコープ制限** | ❌ | ✅（役割と制約を記述） | ✅（systemInstructions） |
| **バージョン管理** | ✅ | ✅（Git commit SHA） | ❌ |
| **チーム共有** | ✅ | ✅ | ❌（個人設定） |

### 4.3 使用コンテキストの推奨

| ユースケース | 推奨ツール | 理由 |
|------------|----------|------|
| **チーム開発** | GitHub Copilot | カスタムエージェントがリポジトリで共有可能 |
| **個人プロジェクト** | Claude Code | 柔軟なMCP設定とスラッシュコマンド |
| **Google Cloud統合** | Gemini CLI | Google サービスとのネイティブ統合 |
| **エンタープライズ** | Claude Code / GitHub Copilot | 集中管理と組織レベルの制御 |
| **プロトタイピング** | Gemini CLI | FastMCPによる高速セットアップ |

---

## 5. まとめ

### 各ツールの特徴

**Claude Code:**
- ✅ 最も柔軟なMCP設定オプション
- ✅ 複数のスコープレベル（Local, Project, User）
- ✅ シンプルなスラッシュコマンドシステム
- ❌ エージェントの詳細な動作制御は限定的

**GitHub Copilot:**
- ✅ 最も洗練されたエージェント設定システム
- ✅ ツールアクセスの細かい制御
- ✅ Git統合とチーム共有
- ❌ MCP設定は組織/エンタープライズレベルのみ

**Gemini CLI:**
- ✅ FastMCPによる簡単なセットアップ
- ✅ Google サービスとの統合
- ✅ リッチコンテンツのサポート
- ❌ チーム共有機能が限定的

### 選択ガイドライン

1. **チームで特化エージェントを共有したい** → **GitHub Copilot**
2. **柔軟なMCP統合と個人ワークフロー** → **Claude Code**
3. **Google Cloud環境での開発** → **Gemini CLI**
4. **複数のツールを併用** → **プロジェクトに応じて使い分け**

---

## 参考リンク

- [Claude Code MCP Documentation](https://code.claude.com/docs/en/mcp)
- [GitHub Copilot Custom Agents](https://docs.github.com/en/copilot/reference/custom-agents-configuration)
- [Gemini CLI MCP Guide](https://google-gemini.github.io/gemini-cli/docs/tools/mcp-server.html)
- [Model Context Protocol Specification](https://modelcontextprotocol.io/)

---

**最終更新日**: 2025-11-17

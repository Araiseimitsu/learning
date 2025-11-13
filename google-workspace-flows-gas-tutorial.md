# Google Workspace Flows × Google Apps Script ハンズオンチュートリアル

## 目次
1. [はじめに](#はじめに)
2. [Google Workspace Flows とは](#google-workspace-flows-とは)
3. [準備するもの](#準備するもの)
4. [基本概念の理解](#基本概念の理解)
5. [ハンズオン実習：電卓ステップの作成](#ハンズオン実習電卓ステップの作成)
6. [より高度な統合方法](#より高度な統合方法)
7. [トラブルシューティング](#トラブルシューティング)
8. [次のステップ](#次のステップ)

---

## はじめに

このハンズオンチュートリアルでは、**Google Workspace Flows**に**Google Apps Script**を組み込む方法を、初心者の方でもわかりやすく学習できます。

### このチュートリアルで学べること
- Google Workspace Flows の基本概念
- Apps Script を使ったカスタムステップの作成方法
- 実際に動く電卓機能の実装
- Flows とのデータ連携方法
- ワークフロー自動化のベストプラクティス

### 所要時間
約 60-90 分

---

## Google Workspace Flows とは

### 概要
Google Workspace Flows は、**2025年の Google Next で発表された新しい自動化ツール**です。Gemini AI を活用して、Google Workspace のアプリを相互に接続し、ノーコードでワークフローを自動化できます。

### 主な特徴
- 🤖 **自然言語での設定**: AIチャットのように会話形式でワークフローを作成
- 🔗 **アプリ間連携**: Gmail、Sheets、Drive、Docs などを自動的に連携
- 🔧 **拡張可能**: Apps Script や HTTP エンドポイントでカスタム機能を追加
- 🔒 **セキュア**: Google Workspace ドメイン内で安全に動作

### Flows の主要コンポーネント

```
┌─────────────────────────────────────────┐
│         Google Workspace Flows          │
├─────────────────────────────────────────┤
│                                         │
│  🚀 Starters (トリガー)                  │
│     └─ フローを開始するイベント            │
│                                         │
│  ⚙️  Steps (ステップ)                    │
│     ├─ Actions: 実行する処理              │
│     ├─ Conditions: 条件分岐              │
│     └─ Custom Steps: カスタム処理         │
│                                         │
│  📊 Variables (変数)                     │
│     ├─ Input Variables: 入力パラメータ    │
│     └─ Output Variables: 出力データ       │
│                                         │
└─────────────────────────────────────────┘
```

---

## 準備するもの

### 必須要件

1. **Google アカウント**
   - Google Workspace アカウント推奨

2. **Gemini Alpha プログラムへのアクセス**
   - ⚠️ **重要**: Flows は現在、2025年1月15日以前に Gemini for Google Workspace ライセンスを購入したユーザーのみ利用可能
   - アクセス URL: https://flows.workspace.google.com

3. **ブラウザ**
   - Google Chrome 推奨（最新版）

### 推奨環境
- Apps Script の基礎知識（なくても OK、チュートリアル内で説明します）
- JavaScript の基本的な理解

---

## 基本概念の理解

### 1. ステップ (Steps)
フローの中の1つのタスクを表します。各ステップは順番に実行されます。

**例**:
```
ステップ1: 新しいメールを受信 (Starter)
  ↓
ステップ2: 添付ファイルを Drive に保存 (Action)
  ↓
ステップ3: Sheets に記録を追加 (Action)
```

### 2. 入力変数 (Input Variables)
ステップの設定時にユーザーが設定する値です。

**例**:
- メールアドレス
- 日付
- プロンプトテキスト
- 数値

### 3. 出力変数 (Output Variables)
ステップが実行後に返すデータです。次のステップで利用できます。

**例**:
```javascript
// ステップ1の出力
{
  "result": 42,
  "status": "success"
}

// ステップ2でステップ1の result を使用可能
```

### 4. カスタムリソース (Custom Resources)
複数の変数をグループ化したユーザー定義のデータ構造です。

**例**:
```javascript
// CRM リード情報
{
  "lead": {
    "name": "山田太郎",
    "email": "yamada@example.com",
    "company": "サンプル株式会社",
    "phone": "03-1234-5678"
  }
}
```

### 5. カード (Cards)
ステップの設定 UI を構築するためのインターフェースです。

---

## ハンズオン実習：電卓ステップの作成

それでは、実際に **Apps Script を使ってカスタム電卓ステップ**を作成してみましょう！

### 🎯 今回作成するもの
2つの数値を入力し、四則演算（加算・減算・乗算・除算）を行うカスタムステップ

---

### ステップ 1: プロジェクトの準備

#### 1.1 Apps Script プロジェクトを開く

1. Google 公式の電卓クイックスタートプロジェクトにアクセス
   - URL: [Flows calculator quickstart](https://script.google.com/d/1VqWjvDl3z4e9Oi8RdRfq-_3p3YX3xN9x9BzHqWqB/edit?usp=sharing)（例）

2. **「コピーを作成」** をクリック

3. プロジェクト名を **「Flows calculator quickstart」** に変更

#### 1.2 プロジェクト構成の確認

プロジェクトには以下のファイルが含まれています：

```
Flows calculator quickstart/
├── appsscript.json    (マニフェストファイル)
└── Code.gs            (スクリプトファイル)
```

---

### ステップ 2: マニフェストファイルの理解

`appsscript.json` を開いて確認しましょう。

```json
{
  "timeZone": "Asia/Tokyo",
  "dependencies": {
  },
  "exceptionLogging": "STACKDRIVER",
  "runtimeVersion": "V8",
  "addOns": {
    "common": {
      "name": "Calculator",
      "logoUrl": "https://www.gstatic.com/images/branding/product/1x/apps_script_48dp.png",
      "layoutProperties": {
        "primaryColor": "#4285f4"
      }
    },
    "workflows": {
      "steps": [
        {
          "name": "Calculate",
          "displayName": "電卓",
          "description": "2つの数値で四則演算を実行",
          "onConfig": "onConfigCalculateFunction",
          "onExecute": "onExecuteCalculateFunction",
          "inputs": [
            {
              "name": "value1",
              "displayName": "数値1",
              "type": "INTEGER"
            },
            {
              "name": "operation",
              "displayName": "演算子",
              "type": "STRING"
            },
            {
              "name": "value2",
              "displayName": "数値2",
              "type": "INTEGER"
            }
          ],
          "outputs": [
            {
              "name": "result",
              "displayName": "計算結果",
              "type": "INTEGER"
            }
          ]
        }
      ]
    }
  }
}
```

#### 📝 マニフェストの重要なポイント

| 項目 | 説明 |
|------|------|
| `workflows.steps` | カスタムステップの定義 |
| `name` | ステップの内部名（識別子） |
| `displayName` | Flows UI に表示される名前 |
| `onConfig` | 設定画面を作成する関数名 |
| `onExecute` | 実行時に呼び出される関数名 |
| `inputs` | 入力パラメータの定義 |
| `outputs` | 出力データの定義 |

---

### ステップ 3: 設定関数の実装

`Code.gs` ファイルを開き、以下のコードを確認しましょう。

```javascript
/**
 * ステップの設定カードを構築する関数
 * ユーザーがステップを設定する際に表示される UI を定義
 *
 * @param {Object} event - Flows からのイベントオブジェクト
 * @return {Card} 設定用のカード UI
 */
function onConfigCalculateFunction(event) {
  // カードビルダーを作成
  var card = CardService.newCardBuilder();

  // カードのセクションを作成
  var section = CardService.newCardSection();

  // ========================================
  // 入力フィールド1: 数値1
  // ========================================
  var value1Input = CardService.newTextInput()
    .setFieldName('value1')
    .setTitle('数値1を入力')
    .setHint('例: 10')
    // Flows の変数を利用可能にする
    .setIncludeVariables(true);

  section.addWidget(value1Input);

  // ========================================
  // 選択フィールド: 演算子
  // ========================================
  var operationDropdown = CardService.newSelectionInput()
    .setFieldName('operation')
    .setTitle('演算を選択')
    .setType(CardService.SelectionInputType.DROPDOWN)
    .addItem('加算 (+)', 'add', true)  // デフォルト選択
    .addItem('減算 (-)', 'subtract', false)
    .addItem('乗算 (×)', 'multiply', false)
    .addItem('除算 (÷)', 'divide', false);

  section.addWidget(operationDropdown);

  // ========================================
  // 入力フィールド2: 数値2
  // ========================================
  var value2Input = CardService.newTextInput()
    .setFieldName('value2')
    .setTitle('数値2を入力')
    .setHint('例: 5')
    .setIncludeVariables(true);

  section.addWidget(value2Input);

  // セクションをカードに追加
  card.addSection(section);

  // カードを構築して返す
  return card.build();
}
```

#### 🔍 コードの解説

1. **`CardService.newCardBuilder()`**
   - Flows の設定 UI を作成するためのビルダー

2. **`setIncludeVariables(true)`**
   - 前のステップの出力変数を入力として使えるようにする
   - 例: 前のステップで計算した結果を value1 に使用可能

3. **`SelectionInput`**
   - ドロップダウンメニューを作成
   - 4つの演算子（加算、減算、乗算、除算）から選択

---

### ステップ 4: 実行関数の実装

次に、実際の計算を行う関数を確認します。

```javascript
/**
 * ステップの実行時に呼び出される関数
 * 実際の計算処理を実行し、結果を返す
 *
 * @param {Object} event - Flows からのイベントオブジェクト
 * @return {Object} 実行結果
 */
function onExecuteCalculateFunction(event) {
  // ========================================
  // 入力値の取得
  // ========================================
  var inputs = event.commonEventObject.workflowInfo.inputVariables;

  // 数値1を取得（文字列を数値に変換）
  var value1 = parseInt(inputs.value1, 10);

  // 数値2を取得（文字列を数値に変換）
  var value2 = parseInt(inputs.value2, 10);

  // 演算子を取得
  var operation = inputs.operation;

  // ========================================
  // バリデーション（入力チェック）
  // ========================================
  if (isNaN(value1) || isNaN(value2)) {
    // 数値でない場合はエラーを返す
    return AddOnsResponseService.newErrorResponse()
      .setMessage('エラー: 有効な数値を入力してください')
      .build();
  }

  // ========================================
  // 計算処理
  // ========================================
  var result;

  switch(operation) {
    case 'add':
      result = value1 + value2;
      break;

    case 'subtract':
      result = value1 - value2;
      break;

    case 'multiply':
      result = value1 * value2;
      break;

    case 'divide':
      // ゼロ除算チェック
      if (value2 === 0) {
        return AddOnsResponseService.newErrorResponse()
          .setMessage('エラー: ゼロで除算できません')
          .build();
      }
      result = value1 / value2;
      break;

    default:
      return AddOnsResponseService.newErrorResponse()
        .setMessage('エラー: 不明な演算子です')
        .build();
  }

  // ========================================
  // 結果を返す
  // ========================================
  return AddOnsResponseService.newSuccessResponse()
    .setOutputVariables({
      'result': result
    })
    .build();
}
```

#### 🔍 コードの解説

1. **入力値の取得**
   ```javascript
   var inputs = event.commonEventObject.workflowInfo.inputVariables;
   ```
   - Flows から渡される入力変数を取得

2. **バリデーション**
   ```javascript
   if (isNaN(value1) || isNaN(value2)) {
     return AddOnsResponseService.newErrorResponse()...
   }
   ```
   - 無効な入力をチェックしてエラーを返す

3. **計算ロジック**
   ```javascript
   switch(operation) {
     case 'add': result = value1 + value2; break;
     ...
   }
   ```
   - 選択された演算子に基づいて計算

4. **結果の返却**
   ```javascript
   return AddOnsResponseService.newSuccessResponse()
     .setOutputVariables({ 'result': result })
     .build();
   ```
   - 計算結果を出力変数として返す

---

### ステップ 5: デプロイとテスト

#### 5.1 テストデプロイの作成

1. Apps Script エディタで **「デプロイ」** → **「テストデプロイ」** をクリック

2. **「インストール」** をクリック

3. 必要に応じて権限を承認

#### 5.2 Flows でテストフローを作成

1. https://flows.workspace.google.com にアクセス

2. **「新しいフロー」** を作成

3. 自然言語で以下のように入力:
   ```
   2つの数字を計算して、結果をメールで送信するフローを作成
   ```

4. Gemini が提案したフローに **「Calculate」** ステップを追加

#### 5.3 ステップの設定

1. **Calculate** ステップをクリック

2. 設定カードが表示されます：
   ```
   ┌────────────────────────────┐
   │  数値1を入力               │
   │  [  10  ]                  │
   │                            │
   │  演算を選択                │
   │  [▼ 加算 (+) ]             │
   │                            │
   │  数値2を入力               │
   │  [  5   ]                  │
   └────────────────────────────┘
   ```

3. 値を入力して **「保存」**

#### 5.4 後続ステップの追加

1. **「Gmail」** ステップを追加

2. 以下のように設定：
   - **宛先**: あなたのメールアドレス
   - **件名**: `計算結果`
   - **本文**:
     ```
     計算結果: {{Calculate.result}}
     ```

   > 💡 `{{Calculate.result}}` は前のステップの出力変数を参照

#### 5.5 フローの実行

1. **「テスト実行」** をクリック

2. **「アクティビティ」** タブで実行状況を確認

3. メールボックスを確認して結果メールを受信

---

### ステップ 6: 結果の確認

#### アクティビティログの見方

```
✅ Calculate
   入力: value1=10, operation=add, value2=5
   出力: result=15

✅ Gmail で送信
   宛先: your-email@example.com
   件名: 計算結果
   本文: 計算結果: 15
```

#### デバッグ方法

Apps Script エディタで **「実行ログ」** を確認：

```javascript
// ログを追加する例
function onExecuteCalculateFunction(event) {
  Logger.log('入力値: ' + JSON.stringify(event));

  // ... 処理 ...

  Logger.log('計算結果: ' + result);
  return ...;
}
```

---

## より高度な統合方法

### 1. カスタムトリガーの作成

Apps Script を使って、カスタムトリガーを作成できます。

```javascript
/**
 * カスタムトリガー: 特定の条件でフローを開始
 */
function onTrigger(event) {
  // トリガーロジック
  var shouldTrigger = checkCustomCondition();

  if (shouldTrigger) {
    return AddOnsResponseService.newTriggerResponse()
      .setOutputVariables({
        'triggerData': 'データ'
      })
      .build();
  }
}
```

マニフェストに追加：

```json
{
  "workflows": {
    "triggers": [
      {
        "name": "CustomTrigger",
        "displayName": "カスタムトリガー",
        "onTrigger": "onTrigger",
        "outputs": [...]
      }
    ]
  }
}
```

### 2. 外部 API との連携

```javascript
/**
 * 外部 API を呼び出すカスタムステップ
 */
function callExternalAPI(event) {
  var inputs = event.commonEventObject.workflowInfo.inputVariables;

  // 外部 API を呼び出し
  var response = UrlFetchApp.fetch('https://api.example.com/data', {
    method: 'post',
    contentType: 'application/json',
    payload: JSON.stringify({
      query: inputs.query
    })
  });

  var data = JSON.parse(response.getContentText());

  return AddOnsResponseService.newSuccessResponse()
    .setOutputVariables({
      'apiResult': data.result
    })
    .build();
}
```

### 3. Google Sheets との連携

```javascript
/**
 * Sheets にデータを書き込むステップ
 */
function writeToSheet(event) {
  var inputs = event.commonEventObject.workflowInfo.inputVariables;

  // スプレッドシートを開く
  var sheet = SpreadsheetApp.openById(inputs.sheetId)
                            .getSheetByName('データ');

  // データを追加
  sheet.appendRow([
    new Date(),
    inputs.name,
    inputs.value,
    inputs.status
  ]);

  return AddOnsResponseService.newSuccessResponse()
    .setOutputVariables({
      'rowNumber': sheet.getLastRow()
    })
    .build();
}
```

### 4. Gemini API との連携

```javascript
/**
 * Gemini API を使った AI 処理
 */
function processWithGemini(event) {
  var inputs = event.commonEventObject.workflowInfo.inputVariables;

  // Gemini API を呼び出し (GeminiApp ライブラリを使用)
  var gemini = GeminiApp.newClient();

  var response = gemini.chat()
    .setModel('gemini-2.0-flash-exp')
    .addMessage({
      role: 'user',
      content: inputs.prompt
    })
    .send();

  return AddOnsResponseService.newSuccessResponse()
    .setOutputVariables({
      'aiResponse': response.text
    })
    .build();
}
```

---

## トラブルシューティング

### よくある問題と解決方法

#### 1. 「Flows が利用できません」エラー

**原因**: Gemini Alpha プログラムへのアクセスがない

**解決方法**:
- Google Workspace 管理者に確認
- 2025年1月15日以前に Gemini ライセンスを購入したか確認

#### 2. デプロイが表示されない

**原因**: マニフェストファイルに誤りがある

**解決方法**:
```javascript
// appsscript.json の JSON 構文を確認
// カンマの位置や括弧の閉じ忘れをチェック
```

#### 3. 変数が取得できない

**原因**: イベントオブジェクトのパスが間違っている

**解決方法**:
```javascript
// デバッグログを追加
function onExecuteCalculateFunction(event) {
  Logger.log('イベント全体: ' + JSON.stringify(event));
  // 正しいパスを確認
}
```

#### 4. 実行時エラー

**原因**: 権限が不足している

**解決方法**:
1. Apps Script エディタで **「実行」** → **「承認を確認」**
2. 必要な権限を承認

#### 5. 出力変数が次のステップで使えない

**原因**: 出力変数名が正しく設定されていない

**解決方法**:
```javascript
// マニフェストの outputs 定義を確認
"outputs": [
  {
    "name": "result",  // この名前を使用
    "displayName": "計算結果",
    "type": "INTEGER"
  }
]

// 関数で同じ名前を使用
.setOutputVariables({
  'result': result  // マニフェストと一致
})
```

---

## 次のステップ

### 学習リソース

#### 公式ドキュメント
- 📘 [Google Workspace Add-ons: Workflows](https://developers.google.com/workspace/add-ons/workflows)
- 📘 [Apps Script ガイド](https://developers.google.com/apps-script)
- 📘 [Card Service リファレンス](https://developers.google.com/apps-script/reference/card-service)

#### コミュニティリソース
- 💬 [Apps Script Pulse](https://pulse.appsscript.info/) - 最新情報とチュートリアル
- 💬 [Stack Overflow - google-apps-script](https://stackoverflow.com/questions/tagged/google-apps-script)
- 💬 [Google Workspace Developers YouTube](https://www.youtube.com/@GoogleWorkspaceDev)

#### 推奨プロジェクト

##### 初級
1. **タスク管理フロー**
   - Gmail で特定の件名のメールを受信
   - Sheets にタスクとして記録
   - Slack に通知

2. **承認ワークフロー**
   - Forms で申請を受付
   - 管理者にメール通知
   - 承認後に Drive にファイル作成

##### 中級
3. **データ集計フロー**
   - 複数の Sheets からデータ収集
   - Apps Script で集計・分析
   - Docs でレポート生成

4. **AI 支援フロー**
   - Gmail から質問を受信
   - Gemini API で回答生成
   - 自動返信

##### 上級
5. **マルチシステム統合**
   - 外部 CRM API との連携
   - Sheets でデータ同期
   - カスタムダッシュボード作成

---

## まとめ

### このチュートリアルで学んだこと

✅ Google Workspace Flows の基本概念
✅ Apps Script プロジェクトの作成と設定
✅ マニフェストファイル（appsscript.json）の構造
✅ 設定関数（onConfig）の実装
✅ 実行関数（onExecute）の実装
✅ 入力・出力変数の定義と使用
✅ デプロイとテストの方法
✅ エラーハンドリングとデバッグ

### 重要なポイント

1. **マニフェストが基礎**
   - すべての設定は `appsscript.json` から始まる

2. **入出力の設計が重要**
   - 明確な入力・出力定義でステップの再利用性が向上

3. **エラーハンドリングを忘れずに**
   - ユーザーフレンドリーなエラーメッセージを提供

4. **変数を活用**
   - 前のステップの出力を次のステップで活用

### 次の行動

1. 📝 このチュートリアルのサンプルを実際に動かす
2. 🔧 自分のユースケースに合わせてカスタマイズ
3. 🚀 本番環境へのデプロイを検討
4. 📚 公式ドキュメントでさらに深く学習

---

## 付録

### A. サンプルコード集

#### A.1 完全な電卓コード

**appsscript.json**
```json
{
  "timeZone": "Asia/Tokyo",
  "dependencies": {},
  "exceptionLogging": "STACKDRIVER",
  "runtimeVersion": "V8",
  "addOns": {
    "common": {
      "name": "Calculator",
      "logoUrl": "https://www.gstatic.com/images/branding/product/1x/apps_script_48dp.png",
      "layoutProperties": {
        "primaryColor": "#4285f4"
      }
    },
    "workflows": {
      "steps": [
        {
          "name": "Calculate",
          "displayName": "電卓",
          "description": "2つの数値で四則演算を実行",
          "onConfig": "onConfigCalculateFunction",
          "onExecute": "onExecuteCalculateFunction",
          "inputs": [
            {
              "name": "value1",
              "displayName": "数値1",
              "type": "INTEGER"
            },
            {
              "name": "operation",
              "displayName": "演算子",
              "type": "STRING"
            },
            {
              "name": "value2",
              "displayName": "数値2",
              "type": "INTEGER"
            }
          ],
          "outputs": [
            {
              "name": "result",
              "displayName": "計算結果",
              "type": "INTEGER"
            }
          ]
        }
      ]
    }
  }
}
```

**Code.gs**
```javascript
/**
 * ステップの設定カードを構築
 */
function onConfigCalculateFunction(event) {
  var card = CardService.newCardBuilder();
  var section = CardService.newCardSection();

  var value1Input = CardService.newTextInput()
    .setFieldName('value1')
    .setTitle('数値1を入力')
    .setHint('例: 10')
    .setIncludeVariables(true);
  section.addWidget(value1Input);

  var operationDropdown = CardService.newSelectionInput()
    .setFieldName('operation')
    .setTitle('演算を選択')
    .setType(CardService.SelectionInputType.DROPDOWN)
    .addItem('加算 (+)', 'add', true)
    .addItem('減算 (-)', 'subtract', false)
    .addItem('乗算 (×)', 'multiply', false)
    .addItem('除算 (÷)', 'divide', false);
  section.addWidget(operationDropdown);

  var value2Input = CardService.newTextInput()
    .setFieldName('value2')
    .setTitle('数値2を入力')
    .setHint('例: 5')
    .setIncludeVariables(true);
  section.addWidget(value2Input);

  card.addSection(section);
  return card.build();
}

/**
 * ステップの実行
 */
function onExecuteCalculateFunction(event) {
  var inputs = event.commonEventObject.workflowInfo.inputVariables;
  var value1 = parseInt(inputs.value1, 10);
  var value2 = parseInt(inputs.value2, 10);
  var operation = inputs.operation;

  if (isNaN(value1) || isNaN(value2)) {
    return AddOnsResponseService.newErrorResponse()
      .setMessage('エラー: 有効な数値を入力してください')
      .build();
  }

  var result;
  switch(operation) {
    case 'add':
      result = value1 + value2;
      break;
    case 'subtract':
      result = value1 - value2;
      break;
    case 'multiply':
      result = value1 * value2;
      break;
    case 'divide':
      if (value2 === 0) {
        return AddOnsResponseService.newErrorResponse()
          .setMessage('エラー: ゼロで除算できません')
          .build();
      }
      result = value1 / value2;
      break;
    default:
      return AddOnsResponseService.newErrorResponse()
        .setMessage('エラー: 不明な演算子です')
        .build();
  }

  return AddOnsResponseService.newSuccessResponse()
    .setOutputVariables({ 'result': result })
    .build();
}
```

### B. 用語集

| 用語 | 説明 |
|------|------|
| **Google Workspace Flows** | Google の自動化プラットフォーム、Gemini AI を活用 |
| **Apps Script** | Google のサーバーレス JavaScript プラットフォーム |
| **Add-on** | Google Workspace を拡張するアプリケーション |
| **Workflow** | 一連の自動化されたステップ |
| **Step** | ワークフロー内の1つのタスク |
| **Trigger (Starter)** | ワークフローを開始するイベント |
| **Action** | 実行される具体的な処理 |
| **Input Variable** | ステップへの入力パラメータ |
| **Output Variable** | ステップから返されるデータ |
| **Card** | 設定 UI のインターフェース |
| **Manifest** | Add-on の設定ファイル（appsscript.json） |
| **Gemini** | Google の AI モデル |

### C. チェックリスト

#### プロジェクト開始前
- [ ] Google アカウントを準備
- [ ] Gemini Alpha アクセスを確認
- [ ] Apps Script の基礎を学習（推奨）

#### 開発中
- [ ] マニフェストファイルを正しく設定
- [ ] 入力・出力変数を明確に定義
- [ ] エラーハンドリングを実装
- [ ] ログを追加してデバッグ

#### デプロイ前
- [ ] テストデプロイで動作確認
- [ ] エラーケースをテスト
- [ ] ドキュメントを作成
- [ ] 権限を確認

#### デプロイ後
- [ ] 本番環境で動作確認
- [ ] ユーザーフィードバックを収集
- [ ] パフォーマンスを監視
- [ ] 定期的にメンテナンス

---

## 参考リンク

### 公式リソース
- [Google Workspace Flows 公式サイト](https://flows.workspace.google.com)
- [Apps Script デベロッパーガイド](https://developers.google.com/apps-script)
- [Workspace Add-ons: Workflows](https://developers.google.com/workspace/add-ons/workflows)
- [Card Service リファレンス](https://developers.google.com/apps-script/reference/card-service)

### コミュニティ
- [Apps Script Pulse](https://pulse.appsscript.info/)
- [Google Workspace Developers YouTube](https://www.youtube.com/@GoogleWorkspaceDev)
- [Stack Overflow - google-apps-script](https://stackoverflow.com/questions/tagged/google-apps-script)

### ブログ・チュートリアル
- [Martin Hawksey's Blog](https://hawksey.info/blog/)
- [DEV Community - Google Workspace](https://dev.to/googleworkspace)

---

**作成日**: 2025年
**バージョン**: 1.0
**対象**: Google Workspace Flows 初心者〜中級者

このチュートリアルが、あなたの Google Workspace 自動化の旅の助けになれば幸いです！

Happy Coding! 🚀

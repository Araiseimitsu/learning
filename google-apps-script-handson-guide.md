# Google Apps Script 完全ハンズオンガイド

> 初心者から上級者まで、作成からデプロイまで完全網羅（2025年版）

---

## 目次

### 第1章: はじめに
- [このガイドについて](#このガイドについて)
- [対象読者](#対象読者)
- [学習ロードマップ](#学習ロードマップ)

### 第2章: 基礎編（初心者向け）
- [Google Apps Scriptとは](#google-apps-scriptとは)
- [開発環境のセットアップ](#開発環境のセットアップ)
- [JavaScript基礎](#javascript基礎)
- [はじめてのスクリプト](#はじめてのスクリプト)
- [基本的なサービスの使い方](#基本的なサービスの使い方)
- [デバッグとログ](#デバッグとログ)

### 第3章: 実践編（中級者向け）
- [スプレッドシート自動化](#スプレッドシート自動化)
- [Gmail自動化](#gmail自動化)
- [Googleドライブ操作](#googleドライブ操作)
- [フォームとの連携](#フォームとの連携)
- [トリガーとイベント](#トリガーとイベント)
- [外部API連携](#外部api連携)
- [データ処理テクニック](#データ処理テクニック)

### 第4章: 応用編（上級者向け）
- [カスタムアドオンの作成](#カスタムアドオンの作成)
- [Webアプリケーション開発](#webアプリケーション開発)
- [カスタムメニューとダイアログ](#カスタムメニューとダイアログ)
- [高度なデータベース連携](#高度なデータベース連携)
- [パフォーマンス最適化](#パフォーマンス最適化)
- [セキュリティベストプラクティス](#セキュリティベストプラクティス)
- [テストと品質保証](#テストと品質保証)

### 第5章: デプロイメント完全ガイド
- [デプロイの種類](#デプロイの種類)
- [スタンドアロンスクリプトのデプロイ](#スタンドアロンスクリプトのデプロイ)
- [Webアプリとしてのデプロイ](#webアプリとしてのデプロイ)
- [アドオンのデプロイ](#アドオンのデプロイ)
- [ライブラリの公開](#ライブラリの公開)
- [バージョン管理](#バージョン管理)
- [CI/CDパイプライン](#cicdパイプライン)

### 付録
- [トラブルシューティング](#トラブルシューティング)
- [API制限と割当量](#api制限と割当量)
- [便利なライブラリ](#便利なライブラリ)
- [参考資料](#参考資料)

---

# 第1章: はじめに

## このガイドについて

このハンズオンガイドは、**Google Apps Script**を使って業務自動化やアプリケーション開発を行いたい方のために作成された、実践的な学習教材です。

### 特徴

- **段階的学習**: 初心者から上級者まで、ステップバイステップで学習
- **実践重視**: すぐに使える実用的なコード例を豊富に掲載
- **完全網羅**: 基礎からデプロイまで、必要な知識をすべてカバー
- **最新情報**: 2025年時点の最新機能とベストプラクティス

## 対象読者

### 初心者の方
- プログラミング経験が少ない、またはない方
- Google Workspaceを使っているが自動化したい方
- 業務効率化に興味がある方

### 中級者の方
- 基本的なプログラミングができる方
- より複雑な自動化を実現したい方
- 既存のスクリプトを改善したい方

### 上級者の方
- 本格的なアプリケーションを開発したい方
- アドオンやWebアプリを公開したい方
- パフォーマンスやセキュリティを重視する方

## 学習ロードマップ

```
【初心者】
Week 1-2: 基礎編
├─ 開発環境のセットアップ
├─ JavaScript基礎
└─ 簡単なスクリプト作成

【中級者】
Week 3-6: 実践編
├─ スプレッドシート自動化
├─ Gmail/Drive操作
├─ トリガー設定
└─ 外部API連携

【上級者】
Week 7-10: 応用編
├─ カスタムアドオン開発
├─ Webアプリ開発
├─ パフォーマンス最適化
└─ セキュリティ対策

【エキスパート】
Week 11-12: デプロイメント
├─ 各種デプロイ方法
├─ バージョン管理
└─ CI/CD構築
```

---

# 第2章: 基礎編（初心者向け）

## Google Apps Scriptとは

### 概要

**Google Apps Script (GAS)** は、Googleが提供するクラウドベースのJavaScriptプラットフォームです。Google Workspaceのアプリケーション（Gmail、スプレッドシート、ドライブなど）と深く統合されており、強力な自動化やカスタマイズが可能です。

### 主な特徴

| 特徴 | 詳細 |
|------|------|
| **言語** | JavaScript（ES6+対応） |
| **実行環境** | Googleのクラウドサーバー上で実行 |
| **費用** | 基本無料（制限あり） |
| **統合** | Google Workspace全サービスと連携可能 |
| **デプロイ** | Webアプリ、アドオン、APIとして公開可能 |

### できること

1. **業務自動化**
   - メール送信の自動化
   - スプレッドシートのデータ処理
   - ファイル整理・バックアップ
   - レポート自動生成

2. **カスタムツール開発**
   - スプレッドシート用カスタム関数
   - Googleドキュメントアドオン
   - カスタムメニューとダイアログ

3. **Webアプリケーション**
   - 社内ツールの開発
   - フォーム処理アプリ
   - ダッシュボード作成

4. **外部サービス連携**
   - REST API連携
   - データベース接続
   - サードパーティサービス統合

### できないこと

- ローカルファイルへの直接アクセス
- ブラウザDOM操作（Webアプリ内は可能）
- リアルタイム通信（WebSocketなど）
- 長時間実行（最大6分）

## 開発環境のセットアップ

### 方法1: スタンドアロンスクリプト

独立したスクリプトプロジェクトを作成します。

**手順:**

1. [script.google.com](https://script.google.com) にアクセス
2. 「新しいプロジェクト」をクリック
3. プロジェクト名を変更（左上の「無題のプロジェクト」をクリック）

**メリット:**
- 複数のサービスを横断的に利用可能
- 独立して管理できる
- ライブラリとして公開可能

**デメリット:**
- 特定のファイルに紐付けられない
- 初回実行時に権限設定が必要

### 方法2: コンテナバインドスクリプト

Googleドキュメント、スプレッドシート、フォームなどに紐付けられたスクリプトです。

**手順:**

1. スプレッドシートを開く
2. 「拡張機能」→「Apps Script」を選択
3. スクリプトエディタが開く

**メリット:**
- 紐付けられたファイルに簡単にアクセス
- カスタムメニューやトリガーが使いやすい
- 共有が簡単

**デメリット:**
- 親ファイルに依存
- 複数ファイルの操作には向かない

### エディタの使い方

#### インターフェース概要

```
┌─────────────────────────────────────────┐
│ [ファイル] [編集] [表示] [実行] [デバッグ]  │
├─────────────────────────────────────────┤
│ コード.gs ▼ │ myFunction ▼ │ 実行 │ デバッグ │
├─────────────────────────────────────────┤
│                                          │
│  1  function myFunction() {              │
│  2    Logger.log('Hello World');         │
│  3  }                                    │
│                                          │
│                                          │
│                                          │
├─────────────────────────────────────────┤
│ 実行ログ │ ログ │ Stackdriver Logging    │
└─────────────────────────────────────────┘
```

#### 主要機能

1. **コードエディタ**: スクリプトを記述
2. **関数選択**: 実行する関数を選択
3. **実行ボタン**: スクリプトを実行
4. **デバッグボタン**: デバッグモードで実行
5. **ログタブ**: 実行結果やエラーを確認
6. **トリガー**: 自動実行の設定
7. **サービス**: 高度なGoogleサービスの追加

### 便利なショートカット

| 操作 | Windows/Linux | Mac |
|------|---------------|-----|
| 実行 | Ctrl + R | Cmd + R |
| 保存 | Ctrl + S | Cmd + S |
| 検索 | Ctrl + F | Cmd + F |
| 置換 | Ctrl + H | Cmd + Option + F |
| コメント | Ctrl + / | Cmd + / |
| インデント | Tab | Tab |
| アウトデント | Shift + Tab | Shift + Tab |

## JavaScript基礎

Google Apps ScriptはJavaScriptベースですが、すべてのJavaScript機能を理解する必要はありません。ここでは最低限必要な知識を説明します。

### 変数

```javascript
// var（古い書き方、避けるべき）
var oldWay = 'old';

// let（再代入可能）
let name = '田中';
name = '佐藤'; // OK

// const（再代入不可、推奨）
const EMAIL = 'test@example.com';
// EMAIL = 'new@example.com'; // エラー
```

**推奨**: 基本的に`const`を使い、必要な場合のみ`let`を使用

### データ型

```javascript
// 文字列
const text = 'こんにちは';
const text2 = "Hello";
const text3 = `テンプレート ${text}`; // テンプレートリテラル

// 数値
const num = 100;
const float = 3.14;

// 真偽値
const isActive = true;
const isEmpty = false;

// 配列
const fruits = ['りんご', 'バナナ', 'オレンジ'];
console.log(fruits[0]); // 'りんご'

// オブジェクト
const user = {
  name: '田中太郎',
  age: 30,
  email: 'tanaka@example.com'
};
console.log(user.name); // '田中太郎'

// null と undefined
const nothing = null;
let notDefined; // undefined
```

### 演算子

```javascript
// 算術演算子
const sum = 10 + 5;      // 15
const diff = 10 - 5;     // 5
const product = 10 * 5;  // 50
const quotient = 10 / 5; // 2
const remainder = 10 % 3; // 1

// 比較演算子
10 === 10  // true（厳密等価）
10 == '10' // true（型変換あり、避けるべき）
10 !== 5   // true（厳密不等価）
10 > 5     // true
10 >= 10   // true

// 論理演算子
true && false  // false（AND）
true || false  // true（OR）
!true          // false（NOT）
```

### 条件分岐

```javascript
// if文
const score = 85;

if (score >= 90) {
  Logger.log('優秀');
} else if (score >= 70) {
  Logger.log('良好');
} else if (score >= 60) {
  Logger.log('合格');
} else {
  Logger.log('不合格');
}

// 三項演算子
const result = score >= 60 ? '合格' : '不合格';

// switch文
const day = '月';

switch (day) {
  case '月':
  case '水':
  case '金':
    Logger.log('出勤日');
    break;
  case '土':
  case '日':
    Logger.log('休日');
    break;
  default:
    Logger.log('その他');
}
```

### ループ

```javascript
// for文
for (let i = 0; i < 5; i++) {
  Logger.log(i); // 0, 1, 2, 3, 4
}

// while文
let count = 0;
while (count < 5) {
  Logger.log(count);
  count++;
}

// 配列のループ
const items = ['A', 'B', 'C'];

// forEach（推奨）
items.forEach(item => {
  Logger.log(item);
});

// for...of
for (const item of items) {
  Logger.log(item);
}

// map（変換）
const numbers = [1, 2, 3];
const doubled = numbers.map(n => n * 2); // [2, 4, 6]

// filter（フィルタリング）
const evenNumbers = numbers.filter(n => n % 2 === 0); // [2]

// reduce（集計）
const sum = numbers.reduce((acc, n) => acc + n, 0); // 6
```

### 関数

```javascript
// 関数宣言
function greet(name) {
  return `こんにちは、${name}さん`;
}

// 関数式
const greet2 = function(name) {
  return `こんにちは、${name}さん`;
};

// アロー関数（推奨）
const greet3 = (name) => {
  return `こんにちは、${name}さん`;
};

// アロー関数（短縮形）
const greet4 = name => `こんにちは、${name}さん`;

// 使用例
const message = greet('田中'); // 'こんにちは、田中さん'
```

### オブジェクトと配列の操作

```javascript
// 分割代入
const user = { name: '田中', age: 30 };
const { name, age } = user;

const items = ['A', 'B', 'C'];
const [first, second] = items; // first='A', second='B'

// スプレッド構文
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

const user1 = { name: '田中', age: 30 };
const user2 = { ...user1, age: 31 }; // { name: '田中', age: 31 }

// オブジェクトのキーと値
const obj = { a: 1, b: 2, c: 3 };
const keys = Object.keys(obj);     // ['a', 'b', 'c']
const values = Object.values(obj); // [1, 2, 3]
const entries = Object.entries(obj); // [['a', 1], ['b', 2], ['c', 3]]
```

## はじめてのスクリプト

### Hello World

```javascript
/**
 * 最もシンプルなスクリプト
 */
function helloWorld() {
  Logger.log('Hello, World!');
}
```

**実行方法:**
1. 上記コードをエディタに貼り付け
2. 関数`helloWorld`を選択
3. 「実行」ボタンをクリック
4. 「ログ」タブで結果を確認

### スプレッドシートに書き込み

```javascript
/**
 * スプレッドシートに「Hello」と書き込む
 */
function writeToSheet() {
  // アクティブなスプレッドシートを取得
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();

  // A1セルに「Hello」と書き込む
  sheet.getRange('A1').setValue('Hello');

  // B1セルに現在時刻を書き込む
  sheet.getRange('B1').setValue(new Date());

  Logger.log('書き込み完了！');
}
```

### Gmailからメールを読み取る

```javascript
/**
 * 受信トレイの最新メールを読み取る
 */
function readLatestEmail() {
  // 受信トレイの最新スレッドを取得
  const threads = GmailApp.getInboxThreads(0, 1);

  if (threads.length === 0) {
    Logger.log('メールがありません');
    return;
  }

  // スレッドからメッセージを取得
  const messages = threads[0].getMessages();
  const latestMessage = messages[messages.length - 1];

  // メール情報をログに出力
  Logger.log('差出人: ' + latestMessage.getFrom());
  Logger.log('件名: ' + latestMessage.getSubject());
  Logger.log('日時: ' + latestMessage.getDate());
  Logger.log('本文:\n' + latestMessage.getPlainBody());
}
```

### メールを送信する

```javascript
/**
 * シンプルなメール送信
 */
function sendEmail() {
  const recipient = 'example@gmail.com'; // 送信先
  const subject = 'テストメール';
  const body = 'これはGoogle Apps Scriptから送信されたテストメールです。';

  GmailApp.sendEmail(recipient, subject, body);

  Logger.log('メール送信完了: ' + recipient);
}

/**
 * HTML形式のメール送信
 */
function sendHtmlEmail() {
  const recipient = 'example@gmail.com';
  const subject = 'HTMLメール';
  const htmlBody = `
    <h1>重要なお知らせ</h1>
    <p>これは<strong>HTML形式</strong>のメールです。</p>
    <ul>
      <li>項目1</li>
      <li>項目2</li>
      <li>項目3</li>
    </ul>
  `;

  GmailApp.sendEmail(recipient, subject, '', {
    htmlBody: htmlBody
  });

  Logger.log('HTMLメール送信完了');
}
```

## 基本的なサービスの使い方

### SpreadsheetApp（スプレッドシート）

```javascript
/**
 * スプレッドシート基本操作
 */
function spreadsheetBasics() {
  // === スプレッドシートの取得 ===

  // 1. アクティブなスプレッドシート
  const ss = SpreadsheetApp.getActiveSpreadsheet();

  // 2. IDで取得
  const ssById = SpreadsheetApp.openById('YOUR_SPREADSHEET_ID');

  // 3. URLで取得
  const ssByUrl = SpreadsheetApp.openByUrl('https://docs.google.com/spreadsheets/d/...');

  // === シートの取得 ===

  // 1. アクティブシート
  const sheet = ss.getActiveSheet();

  // 2. 名前で取得
  const sheetByName = ss.getSheetByName('シート1');

  // 3. インデックスで取得（0始まり）
  const sheetByIndex = ss.getSheets()[0];

  // === セルの操作 ===

  // 1. 単一セルの読み書き
  sheet.getRange('A1').setValue('Hello');
  const value = sheet.getRange('A1').getValue();

  // 2. 範囲の読み書き
  const data = [
    ['名前', '年齢', 'メール'],
    ['田中', 30, 'tanaka@example.com'],
    ['佐藤', 25, 'sato@example.com']
  ];
  sheet.getRange(1, 1, data.length, data[0].length).setValues(data);

  // 3. データの読み取り
  const range = sheet.getRange('A1:C3');
  const values = range.getValues();

  values.forEach((row, index) => {
    Logger.log(`行${index + 1}: ${row.join(', ')}`);
  });

  // === シートの操作 ===

  // 1. 新しいシートを作成
  const newSheet = ss.insertSheet('新しいシート');

  // 2. シートを削除
  ss.deleteSheet(newSheet);

  // 3. シート名を変更
  sheet.setName('新しい名前');

  // === その他の便利な操作 ===

  // 最終行・最終列を取得
  const lastRow = sheet.getLastRow();
  const lastColumn = sheet.getLastColumn();

  // データ範囲全体を取得
  const dataRange = sheet.getDataRange();

  // 行を追加
  sheet.appendRow(['新しい', 'データ', '行']);

  // 書式設定
  sheet.getRange('A1:C1')
    .setFontWeight('bold')
    .setBackground('#4285f4')
    .setFontColor('white');
}
```

### GmailApp（Gmail）

```javascript
/**
 * Gmail基本操作
 */
function gmailBasics() {
  // === メールの検索 ===

  // 1. 受信トレイのスレッドを取得
  const inboxThreads = GmailApp.getInboxThreads(0, 10); // 最新10件

  // 2. 検索クエリでスレッドを取得
  const searchThreads = GmailApp.search('subject:重要 is:unread', 0, 10);

  // 3. ラベルでフィルタ
  const label = GmailApp.getUserLabelByName('仕事');
  if (label) {
    const labelThreads = label.getThreads();
  }

  // === メッセージの読み取り ===

  if (inboxThreads.length > 0) {
    const thread = inboxThreads[0];
    const messages = thread.getMessages();

    messages.forEach(message => {
      Logger.log('差出人: ' + message.getFrom());
      Logger.log('宛先: ' + message.getTo());
      Logger.log('件名: ' + message.getSubject());
      Logger.log('日時: ' + message.getDate());
      Logger.log('本文: ' + message.getPlainBody());

      // 添付ファイル
      const attachments = message.getAttachments();
      attachments.forEach(attachment => {
        Logger.log('添付: ' + attachment.getName());
      });
    });
  }

  // === メールの送信 ===

  // 1. シンプルな送信
  GmailApp.sendEmail(
    'recipient@example.com',
    '件名',
    '本文'
  );

  // 2. オプション付き送信
  GmailApp.sendEmail(
    'recipient@example.com',
    '件名',
    '本文（プレーンテキスト）',
    {
      htmlBody: '<h1>HTML本文</h1>',
      cc: 'cc@example.com',
      bcc: 'bcc@example.com',
      name: '送信者名',
      replyTo: 'reply@example.com',
      attachments: [DriveApp.getFileById('FILE_ID').getAs('application/pdf')]
    }
  );

  // === ラベルの操作 ===

  // ラベルを作成
  const newLabel = GmailApp.createLabel('新しいラベル');

  // ラベルを付与
  if (inboxThreads.length > 0) {
    inboxThreads[0].addLabel(newLabel);
  }

  // === その他の操作 ===

  // スターを付ける
  if (inboxThreads.length > 0) {
    inboxThreads[0].addStar();
  }

  // 既読にする
  if (inboxThreads.length > 0) {
    inboxThreads[0].markRead();
  }

  // アーカイブ
  if (inboxThreads.length > 0) {
    inboxThreads[0].moveToArchive();
  }

  // ゴミ箱へ
  if (inboxThreads.length > 0) {
    inboxThreads[0].moveToTrash();
  }
}
```

### DriveApp（Googleドライブ）

```javascript
/**
 * Googleドライブ基本操作
 */
function driveBasics() {
  // === ファイルの取得 ===

  // 1. IDでファイルを取得
  const file = DriveApp.getFileById('FILE_ID');

  // 2. 名前でファイルを検索
  const files = DriveApp.getFilesByName('ファイル名.txt');
  while (files.hasNext()) {
    const file = files.next();
    Logger.log('ファイル: ' + file.getName());
  }

  // 3. 種類で検索
  const pdfFiles = DriveApp.getFilesByType(MimeType.PDF);

  // === フォルダの操作 ===

  // 1. フォルダを取得
  const folder = DriveApp.getFolderById('FOLDER_ID');

  // 2. フォルダを作成
  const newFolder = DriveApp.createFolder('新しいフォルダ');

  // 3. フォルダ内のファイルを取得
  const folderFiles = folder.getFiles();
  while (folderFiles.hasNext()) {
    const file = folderFiles.next();
    Logger.log('ファイル: ' + file.getName());
  }

  // === ファイルの作成 ===

  // 1. テキストファイル
  const textFile = DriveApp.createFile('test.txt', 'Hello, World!');

  // 2. Blobからファイル作成
  const blob = Utilities.newBlob('データ', 'text/plain', 'data.txt');
  const blobFile = DriveApp.createFile(blob);

  // 3. フォルダ内にファイル作成
  const fileInFolder = folder.createFile('file.txt', 'コンテンツ');

  // === ファイル操作 ===

  // ファイル情報
  Logger.log('名前: ' + textFile.getName());
  Logger.log('URL: ' + textFile.getUrl());
  Logger.log('サイズ: ' + textFile.getSize() + ' bytes');
  Logger.log('MIME: ' + textFile.getMimeType());
  Logger.log('作成日: ' + textFile.getDateCreated());
  Logger.log('更新日: ' + textFile.getLastUpdated());

  // ファイル名変更
  textFile.setName('renamed.txt');

  // ファイルコピー
  const copiedFile = textFile.makeCopy('コピー');

  // ファイル移動
  textFile.moveTo(folder);

  // ファイル削除
  copiedFile.setTrashed(true);

  // === 共有設定 ===

  // 共有URLを取得
  const shareUrl = textFile.getUrl();

  // 共有設定を変更
  textFile.setSharing(
    DriveApp.Access.ANYONE_WITH_LINK,  // リンクを知っている全員
    DriveApp.Permission.VIEW           // 閲覧のみ
  );

  // 特定ユーザーに権限付与
  textFile.addEditor('user@example.com');     // 編集者
  textFile.addViewer('viewer@example.com');   // 閲覧者

  // === 検索 ===

  // 高度な検索
  const searchResults = DriveApp.searchFiles(
    'title contains "レポート" and modifiedDate > "2025-01-01"'
  );

  while (searchResults.hasNext()) {
    const file = searchResults.next();
    Logger.log('検索結果: ' + file.getName());
  }
}
```

### CalendarApp（Googleカレンダー）

```javascript
/**
 * Googleカレンダー基本操作
 */
function calendarBasics() {
  // === カレンダーの取得 ===

  // デフォルトカレンダー
  const calendar = CalendarApp.getDefaultCalendar();

  // IDでカレンダーを取得
  const calendarById = CalendarApp.getCalendarById('CALENDAR_ID');

  // 名前でカレンダーを取得
  const calendars = CalendarApp.getCalendarsByName('仕事');
  if (calendars.length > 0) {
    const workCalendar = calendars[0];
  }

  // === イベントの作成 ===

  // 1. シンプルなイベント
  const event1 = calendar.createEvent(
    '会議',                    // タイトル
    new Date('2025-12-01 10:00'),  // 開始時刻
    new Date('2025-12-01 11:00')   // 終了時刻
  );

  // 2. 終日イベント
  const event2 = calendar.createAllDayEvent(
    '祝日',
    new Date('2025-12-25')
  );

  // 3. オプション付きイベント
  const event3 = calendar.createEvent(
    '重要会議',
    new Date('2025-12-01 14:00'),
    new Date('2025-12-01 15:00'),
    {
      description: '議題: 来期の計画について',
      location: '会議室A',
      guests: 'user1@example.com,user2@example.com',
      sendInvites: true
    }
  );

  // === イベントの取得 ===

  // 期間を指定してイベント取得
  const startDate = new Date('2025-12-01');
  const endDate = new Date('2025-12-31');
  const events = calendar.getEvents(startDate, endDate);

  events.forEach(event => {
    Logger.log('イベント: ' + event.getTitle());
    Logger.log('開始: ' + event.getStartTime());
    Logger.log('終了: ' + event.getEndTime());
    Logger.log('場所: ' + event.getLocation());
    Logger.log('説明: ' + event.getDescription());
  });

  // 今日のイベント
  const todayEvents = calendar.getEventsForDay(new Date());

  // === イベントの更新 ===

  if (events.length > 0) {
    const event = events[0];

    // タイトル変更
    event.setTitle('更新された会議');

    // 時刻変更
    event.setTime(
      new Date('2025-12-01 15:00'),
      new Date('2025-12-01 16:00')
    );

    // 説明追加
    event.setDescription('新しい説明');

    // 場所設定
    event.setLocation('会議室B');

    // ゲスト追加
    event.addGuest('newguest@example.com');

    // 色設定
    event.setColor(CalendarApp.EventColor.RED);
  }

  // === イベントの削除 ===

  if (events.length > 0) {
    // events[0].deleteEvent();  // コメント解除で削除
  }

  // === 繰り返しイベント ===

  // 毎週月曜日10:00-11:00の会議（3ヶ月間）
  const recurrence = CalendarApp.newRecurrence()
    .addWeeklyRule()
    .onlyOnWeekday(CalendarApp.Weekday.MONDAY)
    .until(new Date('2025-03-31'));

  calendar.createEventSeries(
    '週次ミーティング',
    new Date('2025-01-06 10:00'),
    new Date('2025-01-06 11:00'),
    recurrence
  );
}
```

## デバッグとログ

### Logger.log()

```javascript
/**
 * Logger.log() の基本的な使い方
 */
function loggerExamples() {
  // 基本的なログ
  Logger.log('シンプルなメッセージ');

  // 変数のログ
  const name = '田中';
  Logger.log('名前: ' + name);

  // テンプレートリテラル
  Logger.log(`名前は${name}です`);

  // 複数の値
  const age = 30;
  Logger.log('名前: %s, 年齢: %d', name, age);

  // オブジェクトのログ
  const user = { name: '田中', age: 30 };
  Logger.log(user);  // [object Object] と表示される（不便）
  Logger.log(JSON.stringify(user));  // {"name":"田中","age":30}
  Logger.log(JSON.stringify(user, null, 2));  // 整形された JSON

  // 配列のログ
  const items = ['A', 'B', 'C'];
  Logger.log(items);  // A,B,C
  Logger.log(JSON.stringify(items));  // ["A","B","C"]
}
```

### console.log()

```javascript
/**
 * console.log() の使い方（Stackdriver Logging）
 */
function consoleExamples() {
  // 基本的なログ
  console.log('これはStackdriverに記録されます');

  // ログレベル
  console.info('情報メッセージ');
  console.warn('警告メッセージ');
  console.error('エラーメッセージ');

  // オブジェクトのログ（自動で整形される）
  const user = { name: '田中', age: 30 };
  console.log('ユーザー情報:', user);

  // 時間測定
  console.time('処理時間');
  // 何か処理
  for (let i = 0; i < 1000; i++) {
    // 処理
  }
  console.timeEnd('処理時間');  // 処理時間: 〇〇ms
}
```

**Stackdriver Loggingの確認方法:**
1. Apps Scriptエディタで「表示」→「ログ」→「Stackdriver Logging」
2. より高度なログ検索とフィルタリングが可能

### デバッガーの使用

```javascript
/**
 * デバッガーを使った実行
 */
function debugExample() {
  const numbers = [1, 2, 3, 4, 5];

  let sum = 0;
  for (let i = 0; i < numbers.length; i++) {
    sum += numbers[i];  // ←ここにブレークポイントを設定
  }

  Logger.log('合計: ' + sum);
}
```

**デバッガーの使い方:**
1. 行番号の左をクリックしてブレークポイントを設定（赤い丸）
2. 「デバッグ」ボタンをクリック
3. ブレークポイントで実行が停止
4. 左サイドバーで変数の値を確認
5. ステップ実行:
   - **ステップイン**: 関数の中に入る
   - **ステップオーバー**: 次の行へ
   - **ステップアウト**: 関数から出る

### try-catch によるエラーハンドリング

```javascript
/**
 * エラーハンドリングの例
 */
function errorHandlingExample() {
  try {
    // エラーが発生する可能性のある処理
    const sheet = SpreadsheetApp.getActiveSheet();
    const value = sheet.getRange('A1').getValue();

    if (value === '') {
      throw new Error('A1セルが空です');
    }

    const result = processData(value);
    Logger.log('結果: ' + result);

  } catch (error) {
    // エラーが発生した場合の処理
    Logger.log('エラーが発生しました');
    Logger.log('エラーメッセージ: ' + error.message);
    Logger.log('スタックトレース: ' + error.stack);

    // 管理者にメール通知
    MailApp.sendEmail(
      Session.getActiveUser().getEmail(),
      'スクリプトエラー',
      `エラーが発生しました:\n\n${error.message}\n\n${error.stack}`
    );

  } finally {
    // 必ず実行される処理（クリーンアップなど）
    Logger.log('処理終了');
  }
}

function processData(data) {
  // データ処理
  return data;
}
```

### カスタムログ関数

```javascript
/**
 * 構造化ログのための便利なヘルパー
 */
const Log = {
  /**
   * 情報ログ
   */
  info: function(message, data = {}) {
    const logEntry = {
      level: 'INFO',
      timestamp: new Date().toISOString(),
      message: message,
      data: data
    };
    console.log(JSON.stringify(logEntry));
  },

  /**
   * 警告ログ
   */
  warn: function(message, data = {}) {
    const logEntry = {
      level: 'WARN',
      timestamp: new Date().toISOString(),
      message: message,
      data: data
    };
    console.warn(JSON.stringify(logEntry));
  },

  /**
   * エラーログ
   */
  error: function(message, error) {
    const logEntry = {
      level: 'ERROR',
      timestamp: new Date().toISOString(),
      message: message,
      error: error.toString(),
      stack: error.stack
    };
    console.error(JSON.stringify(logEntry));
  },

  /**
   * デバッグログ
   */
  debug: function(message, data = {}) {
    const logEntry = {
      level: 'DEBUG',
      timestamp: new Date().toISOString(),
      message: message,
      data: data
    };
    console.log(JSON.stringify(logEntry));
  }
};

/**
 * 使用例
 */
function customLogExample() {
  Log.info('処理開始', { userId: 123, action: 'データ更新' });

  try {
    const data = fetchData();
    Log.debug('データ取得完了', { recordCount: data.length });

  } catch (error) {
    Log.error('データ取得エラー', error);
  }

  Log.info('処理完了');
}

function fetchData() {
  return [];
}
```

---

# 第3章: 実践編（中級者向け）

## スプレッドシート自動化

### データの一括処理

```javascript
/**
 * スプレッドシートデータの一括処理
 */
function bulkDataProcessing() {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const sheet = ss.getSheetByName('データ');

  // データ範囲全体を取得（一度に）
  const dataRange = sheet.getDataRange();
  const values = dataRange.getValues();

  // ヘッダー行を除外
  const headers = values[0];
  const dataRows = values.slice(1);

  // 各行を処理
  const processedData = dataRows.map(row => {
    const name = row[0];
    const score = row[1];
    const status = row[2];

    // スコアに基づいて評価を計算
    let grade;
    if (score >= 90) grade = 'A';
    else if (score >= 80) grade = 'B';
    else if (score >= 70) grade = 'C';
    else if (score >= 60) grade = 'D';
    else grade = 'F';

    // 新しい列を追加
    return [...row, grade, new Date()];
  });

  // ヘッダーに新しい列を追加
  const newHeaders = [...headers, '評価', '処理日時'];

  // 結果を別シートに書き込み
  let resultSheet = ss.getSheetByName('処理結果');
  if (!resultSheet) {
    resultSheet = ss.insertSheet('処理結果');
  } else {
    resultSheet.clear();
  }

  // ヘッダーとデータを一括書き込み
  const allData = [newHeaders, ...processedData];
  resultSheet.getRange(1, 1, allData.length, allData[0].length)
    .setValues(allData);

  // ヘッダー行のフォーマット
  resultSheet.getRange(1, 1, 1, newHeaders.length)
    .setFontWeight('bold')
    .setBackground('#4285f4')
    .setFontColor('white');

  Logger.log(`${dataRows.length}件のデータを処理しました`);
}
```

### 条件付き書式の設定

```javascript
/**
 * 条件付き書式を動的に設定
 */
function applyConditionalFormatting() {
  const sheet = SpreadsheetApp.getActiveSheet();

  // 既存の条件付き書式をクリア
  sheet.clearConditionalFormatRules();

  const rules = [];

  // ルール1: スコアが90以上なら緑背景
  const rule1 = SpreadsheetApp.newConditionalFormatRule()
    .whenNumberGreaterThanOrEqualTo(90)
    .setBackground('#00ff00')
    .setRanges([sheet.getRange('B2:B100')])
    .build();
  rules.push(rule1);

  // ルール2: スコアが60未満なら赤背景
  const rule2 = SpreadsheetApp.newConditionalFormatRule()
    .whenNumberLessThan(60)
    .setBackground('#ff0000')
    .setFontColor('#ffffff')
    .setRanges([sheet.getRange('B2:B100')])
    .build();
  rules.push(rule2);

  // ルール3: カスタム数式（評価がAの行全体をハイライト）
  const rule3 = SpreadsheetApp.newConditionalFormatRule()
    .whenFormulaSatisfied('=$C2="A"')
    .setBackground('#e6f4ea')
    .setRanges([sheet.getRange('A2:D100')])
    .build();
  rules.push(rule3);

  // ルールを適用
  sheet.setConditionalFormatRules(rules);

  Logger.log('条件付き書式を適用しました');
}
```

### データ検証の設定

```javascript
/**
 * データ検証（ドロップダウンリストなど）を設定
 */
function setupDataValidation() {
  const sheet = SpreadsheetApp.getActiveSheet();

  // 1. ドロップダウンリスト
  const departments = ['営業', '開発', '人事', '総務'];
  const departmentRule = SpreadsheetApp.newDataValidation()
    .requireValueInList(departments)
    .setAllowInvalid(false)
    .setHelpText('部署を選択してください')
    .build();

  sheet.getRange('C2:C100').setDataValidation(departmentRule);

  // 2. 数値範囲の検証
  const scoreRule = SpreadsheetApp.newDataValidation()
    .requireNumberBetween(0, 100)
    .setAllowInvalid(false)
    .setHelpText('0〜100の数値を入力してください')
    .build();

  sheet.getRange('D2:D100').setDataValidation(scoreRule);

  // 3. 日付の検証（今日以降）
  const dateRule = SpreadsheetApp.newDataValidation()
    .requireDateOnOrAfter(new Date())
    .setAllowInvalid(false)
    .setHelpText('今日以降の日付を選択してください')
    .build();

  sheet.getRange('E2:E100').setDataValidation(dateRule);

  // 4. メールアドレスの検証
  const emailRule = SpreadsheetApp.newDataValidation()
    .requireTextIsEmail()
    .setAllowInvalid(false)
    .setHelpText('有効なメールアドレスを入力してください')
    .build();

  sheet.getRange('F2:F100').setDataValidation(emailRule);

  // 5. カスタム数式による検証
  const customRule = SpreadsheetApp.newDataValidation()
    .requireFormulaSatisfied('=LEN(A2) >= 3')
    .setAllowInvalid(false)
    .setHelpText('3文字以上入力してください')
    .build();

  sheet.getRange('A2:A100').setDataValidation(customRule);

  Logger.log('データ検証を設定しました');
}
```

### ピボットテーブルの作成

```javascript
/**
 * ピボットテーブルを自動作成
 */
function createPivotTable() {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const sourceSheet = ss.getSheetByName('データ');

  // ソースデータ範囲
  const sourceData = sourceSheet.getDataRange();

  // ピボットテーブル用のシートを作成
  let pivotSheet = ss.getSheetByName('ピボット');
  if (pivotSheet) {
    ss.deleteSheet(pivotSheet);
  }
  pivotSheet = ss.insertSheet('ピボット');

  // ピボットテーブルを作成
  const pivotTable = pivotSheet.getRange('A1').createPivotTable(sourceData);

  // 行グループを追加（例: 部署）
  pivotTable.addRowGroup(1);  // 1列目

  // 列グループを追加（例: 評価）
  pivotTable.addColumnGroup(3);  // 3列目

  // 値を追加（例: スコアの平均）
  pivotTable.addPivotValue(2, SpreadsheetApp.PivotTableSummarizeFunction.AVERAGE);

  Logger.log('ピボットテーブルを作成しました');
}
```

### グラフの自動生成

```javascript
/**
 * グラフを自動生成
 */
function createCharts() {
  const sheet = SpreadsheetApp.getActiveSheet();

  // 既存のグラフをクリア
  const charts = sheet.getCharts();
  charts.forEach(chart => sheet.removeChart(chart));

  // 1. 棒グラフ
  const barChart = sheet.newChart()
    .setChartType(Charts.ChartType.COLUMN)
    .addRange(sheet.getRange('A1:B10'))
    .setPosition(5, 5, 0, 0)
    .setOption('title', '部署別売上')
    .setOption('hAxis', { title: '部署' })
    .setOption('vAxis', { title: '売上（万円）' })
    .setOption('legend', { position: 'bottom' })
    .build();

  sheet.insertChart(barChart);

  // 2. 円グラフ
  const pieChart = sheet.newChart()
    .setChartType(Charts.ChartType.PIE)
    .addRange(sheet.getRange('A1:B10'))
    .setPosition(5, 10, 0, 0)
    .setOption('title', 'シェア')
    .setOption('pieHole', 0.4)  // ドーナツチャート
    .build();

  sheet.insertChart(pieChart);

  // 3. 折れ線グラフ
  const lineChart = sheet.newChart()
    .setChartType(Charts.ChartType.LINE)
    .addRange(sheet.getRange('A1:D10'))
    .setPosition(20, 5, 0, 0)
    .setOption('title', '月次推移')
    .setOption('curveType', 'function')  // 曲線
    .setOption('legend', { position: 'right' })
    .build();

  sheet.insertChart(lineChart);

  Logger.log('グラフを作成しました');
}
```

## Gmail自動化

### メールの自動返信

```javascript
/**
 * 特定条件のメールに自動返信
 */
function autoReplyToEmails() {
  // 検索条件: 未読で件名に「お問い合わせ」を含む
  const query = 'is:unread subject:お問い合わせ -label:自動返信済み';
  const threads = GmailApp.search(query, 0, 10);

  if (threads.length === 0) {
    Logger.log('対象メールがありません');
    return;
  }

  // ラベルを取得または作成
  let label = GmailApp.getUserLabelByName('自動返信済み');
  if (!label) {
    label = GmailApp.createLabel('自動返信済み');
  }

  threads.forEach(thread => {
    const messages = thread.getMessages();
    const latestMessage = messages[messages.length - 1];

    // 差出人情報
    const from = latestMessage.getFrom();
    const replyTo = latestMessage.getReplyTo() || from;

    // 自動返信メール作成
    const subject = 'Re: ' + latestMessage.getSubject();
    const body = `
お問い合わせありがとうございます。

このメールは自動送信されています。
担当者が確認次第、改めてご連絡させていただきます。

今しばらくお待ちください。

---
カスタマーサポート
support@example.com
    `.trim();

    // 返信送信
    thread.reply(body);

    // ラベルを付けて既読にする
    thread.addLabel(label);
    thread.markRead();

    Logger.log('自動返信送信: ' + replyTo);
  });

  Logger.log(`${threads.length}件のメールに自動返信しました`);
}

/**
 * 1時間ごとに自動実行するトリガーを設定
 */
function setupAutoReplyTrigger() {
  // 既存のトリガーを削除
  const triggers = ScriptApp.getProjectTriggers();
  triggers.forEach(trigger => {
    if (trigger.getHandlerFunction() === 'autoReplyToEmails') {
      ScriptApp.deleteTrigger(trigger);
    }
  });

  // 新しいトリガーを作成（1時間ごと）
  ScriptApp.newTrigger('autoReplyToEmails')
    .timeBased()
    .everyHours(1)
    .create();

  Logger.log('自動返信トリガーを設定しました');
}
```

### メールのフィルタリングと整理

```javascript
/**
 * メールを自動でラベル付けして整理
 */
function organizeEmails() {
  // 設定
  const rules = [
    {
      query: 'from:noreply@ OR from:no-reply@',
      label: '自動送信',
      archive: true,
      markRead: false
    },
    {
      query: 'subject:請求書 has:attachment',
      label: '請求書',
      archive: false,
      markRead: false,
      star: true
    },
    {
      query: 'from:boss@example.com',
      label: '重要',
      archive: false,
      markRead: false,
      star: true
    },
    {
      query: 'subject:(広告|PR|newsletter)',
      label: 'プロモーション',
      archive: true,
      markRead: true
    }
  ];

  rules.forEach(rule => {
    try {
      // ラベルを取得または作成
      let label = GmailApp.getUserLabelByName(rule.label);
      if (!label) {
        label = GmailApp.createLabel(rule.label);
      }

      // 既に処理済みのメールを除外
      const fullQuery = `${rule.query} -label:${rule.label}`;
      const threads = GmailApp.search(fullQuery, 0, 50);

      if (threads.length === 0) return;

      // スレッドを処理
      threads.forEach(thread => {
        thread.addLabel(label);

        if (rule.archive) {
          thread.moveToArchive();
        }

        if (rule.markRead) {
          thread.markRead();
        }

        if (rule.star) {
          thread.addStar();
        }
      });

      Logger.log(`${rule.label}: ${threads.length}件処理`);

    } catch (error) {
      Logger.log(`エラー (${rule.label}): ${error.message}`);
    }
  });
}

/**
 * 毎日実行するトリガーを設定
 */
function setupOrganizeEmailsTrigger() {
  ScriptApp.newTrigger('organizeEmails')
    .timeBased()
    .everyHours(6)
    .create();

  Logger.log('メール整理トリガーを設定しました');
}
```

### 添付ファイルの自動保存

```javascript
/**
 * Gmail添付ファイルをGoogleドライブに自動保存
 */
function saveAttachmentsToDrive() {
  // 設定
  const FOLDER_ID = 'YOUR_FOLDER_ID';  // 保存先フォルダID
  const LABEL_NAME = '添付保存済み';
  const SEARCH_QUERY = 'has:attachment -label:添付保存済み';

  // フォルダとラベルを取得
  const folder = DriveApp.getFolderById(FOLDER_ID);
  let label = GmailApp.getUserLabelByName(LABEL_NAME);
  if (!label) {
    label = GmailApp.createLabel(LABEL_NAME);
  }

  // スプレッドシートに記録（オプション）
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  let logSheet = ss.getSheetByName('添付ファイルログ');
  if (!logSheet) {
    logSheet = ss.insertSheet('添付ファイルログ');
    logSheet.appendRow(['日時', '差出人', '件名', 'ファイル名', 'URL']);
  }

  // メールを検索
  const threads = GmailApp.search(SEARCH_QUERY, 0, 20);

  let totalSaved = 0;

  threads.forEach(thread => {
    const messages = thread.getMessages();

    messages.forEach(message => {
      const attachments = message.getAttachments();

      if (attachments.length === 0) return;

      const from = message.getFrom();
      const subject = message.getSubject();
      const date = message.getDate();

      attachments.forEach(attachment => {
        try {
          // ドライブに保存
          const file = folder.createFile(attachment);

          // ログに記録
          logSheet.appendRow([
            date,
            from,
            subject,
            file.getName(),
            file.getUrl()
          ]);

          totalSaved++;
          Logger.log(`保存: ${file.getName()}`);

        } catch (error) {
          Logger.log(`保存エラー (${attachment.getName()}): ${error.message}`);
        }
      });
    });

    // ラベルを付ける
    thread.addLabel(label);
  });

  Logger.log(`合計 ${totalSaved}ファイルを保存しました`);
}
```

### メールレポートの自動送信

```javascript
/**
 * 毎週月曜日に前週の統計レポートをメール送信
 */
function sendWeeklyEmailReport() {
  // 前週の期間を計算
  const today = new Date();
  const lastMonday = new Date(today);
  lastMonday.setDate(today.getDate() - today.getDay() - 6);  // 先週の月曜日
  lastMonday.setHours(0, 0, 0, 0);

  const lastSunday = new Date(lastMonday);
  lastSunday.setDate(lastMonday.getDate() + 6);  // 先週の日曜日
  lastSunday.setHours(23, 59, 59, 999);

  // 統計を収集
  const stats = {
    received: 0,
    sent: 0,
    unread: 0,
    starred: 0,
    byLabel: {}
  };

  // 受信メール数
  const receivedQuery = `after:${formatDate(lastMonday)} before:${formatDate(new Date(lastSunday.getTime() + 1))}`;
  stats.received = GmailApp.search(receivedQuery).length;

  // 送信メール数
  const sentThreads = GmailApp.search(`in:sent ${receivedQuery}`);
  stats.sent = sentThreads.length;

  // 未読メール数
  stats.unread = GmailApp.getInboxUnreadCount();

  // スターつきメール数
  const starredThreads = GmailApp.getStarredThreads();
  stats.starred = starredThreads.length;

  // ラベル別統計
  const labels = GmailApp.getUserLabels();
  labels.forEach(label => {
    const count = label.getThreads().length;
    if (count > 0) {
      stats.byLabel[label.getName()] = count;
    }
  });

  // HTML レポート作成
  const htmlBody = `
    <html>
      <head>
        <style>
          body { font-family: Arial, sans-serif; }
          h1 { color: #4285f4; }
          table { border-collapse: collapse; width: 100%; }
          th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
          th { background-color: #4285f4; color: white; }
          .metric { font-size: 24px; font-weight: bold; color: #4285f4; }
        </style>
      </head>
      <body>
        <h1>週次メールレポート</h1>
        <p>期間: ${formatDate(lastMonday)} 〜 ${formatDate(lastSunday)}</p>

        <h2>サマリー</h2>
        <table>
          <tr>
            <th>項目</th>
            <th>件数</th>
          </tr>
          <tr>
            <td>受信メール</td>
            <td class="metric">${stats.received}</td>
          </tr>
          <tr>
            <td>送信メール</td>
            <td class="metric">${stats.sent}</td>
          </tr>
          <tr>
            <td>未読メール</td>
            <td class="metric">${stats.unread}</td>
          </tr>
          <tr>
            <td>スター付きメール</td>
            <td class="metric">${stats.starred}</td>
          </tr>
        </table>

        <h2>ラベル別件数</h2>
        <table>
          <tr>
            <th>ラベル</th>
            <th>件数</th>
          </tr>
          ${Object.entries(stats.byLabel)
            .sort((a, b) => b[1] - a[1])
            .map(([label, count]) => `
              <tr>
                <td>${label}</td>
                <td>${count}</td>
              </tr>
            `).join('')}
        </table>

        <p style="color: #666; font-size: 12px;">
          このレポートは Google Apps Script により自動生成されました。
        </p>
      </body>
    </html>
  `;

  // メール送信
  GmailApp.sendEmail(
    Session.getActiveUser().getEmail(),
    '【週次レポート】メール統計',
    '',  // プレーンテキスト本文（空）
    {
      htmlBody: htmlBody
    }
  );

  Logger.log('週次レポートを送信しました');
}

/**
 * 日付フォーマット関数
 */
function formatDate(date) {
  return Utilities.formatDate(date, Session.getScriptTimeZone(), 'yyyy/MM/dd');
}

/**
 * 毎週月曜日9時に実行するトリガーを設定
 */
function setupWeeklyReportTrigger() {
  ScriptApp.newTrigger('sendWeeklyEmailReport')
    .timeBased()
    .onWeekDay(ScriptApp.WeekDay.MONDAY)
    .atHour(9)
    .create();

  Logger.log('週次レポートトリガーを設定しました');
}
```

## Googleドライブ操作

### ファイルのバックアップ

```javascript
/**
 * 特定フォルダのファイルを定期的にバックアップ
 */
function backupFiles() {
  // 設定
  const SOURCE_FOLDER_ID = 'SOURCE_FOLDER_ID';  // バックアップ元
  const BACKUP_FOLDER_ID = 'BACKUP_FOLDER_ID';  // バックアップ先

  const sourceFolder = DriveApp.getFolderById(SOURCE_FOLDER_ID);
  const backupFolder = DriveApp.getFolderById(BACKUP_FOLDER_ID);

  // 日付フォルダを作成
  const today = new Date();
  const folderName = Utilities.formatDate(today, 'JST', 'yyyy-MM-dd');

  let dateFolder = null;
  const folders = backupFolder.getFoldersByName(folderName);
  if (folders.hasNext()) {
    dateFolder = folders.next();
  } else {
    dateFolder = backupFolder.createFolder(folderName);
  }

  // ファイルをコピー
  const files = sourceFolder.getFiles();
  let count = 0;

  while (files.hasNext()) {
    const file = files.next();

    // 既にバックアップ済みか確認
    const existingFiles = dateFolder.getFilesByName(file.getName());
    if (existingFiles.hasNext()) {
      Logger.log(`スキップ: ${file.getName()} (既に存在)`);
      continue;
    }

    // ファイルをコピー
    file.makeCopy(file.getName(), dateFolder);
    count++;

    Logger.log(`バックアップ: ${file.getName()}`);
  }

  Logger.log(`${count}ファイルをバックアップしました`);

  // 古いバックアップを削除（30日以上前）
  cleanOldBackups(backupFolder, 30);
}

/**
 * 古いバックアップフォルダを削除
 */
function cleanOldBackups(backupFolder, daysToKeep) {
  const cutoffDate = new Date();
  cutoffDate.setDate(cutoffDate.getDate() - daysToKeep);

  const folders = backupFolder.getFolders();
  let deletedCount = 0;

  while (folders.hasNext()) {
    const folder = folders.next();
    const folderDate = folder.getDateCreated();

    if (folderDate < cutoffDate) {
      folder.setTrashed(true);
      deletedCount++;
      Logger.log(`削除: ${folder.getName()}`);
    }
  }

  Logger.log(`${deletedCount}個の古いバックアップを削除しました`);
}

/**
 * 毎日深夜2時に実行するトリガーを設定
 */
function setupBackupTrigger() {
  ScriptApp.newTrigger('backupFiles')
    .timeBased()
    .atHour(2)
    .everyDays(1)
    .create();

  Logger.log('バックアップトリガーを設定しました');
}
```

### ファイル名の一括変更

```javascript
/**
 * フォルダ内のファイル名を一括変更
 */
function renameFilesInFolder() {
  const FOLDER_ID = 'YOUR_FOLDER_ID';
  const folder = DriveApp.getFolderById(FOLDER_ID);

  // ファイル名の変更ルール
  const rules = [
    {
      // スペースをアンダースコアに置換
      pattern: / /g,
      replacement: '_'
    },
    {
      // 日付形式を統一 (YYYY-MM-DD)
      pattern: /(\d{4})年(\d{1,2})月(\d{1,2})日/g,
      replacement: (match, year, month, day) => {
        return `${year}-${month.padStart(2, '0')}-${day.padStart(2, '0')}`;
      }
    },
    {
      // 全角英数字を半角に
      pattern: /[Ａ-Ｚａ-ｚ０-９]/g,
      replacement: (char) => String.fromCharCode(char.charCodeAt(0) - 0xFEE0)
    }
  ];

  const files = folder.getFiles();
  let count = 0;

  while (files.hasNext()) {
    const file = files.next();
    let newName = file.getName();

    // 各ルールを適用
    rules.forEach(rule => {
      newName = newName.replace(rule.pattern, rule.replacement);
    });

    // 名前が変わった場合のみリネーム
    if (newName !== file.getName()) {
      file.setName(newName);
      count++;
      Logger.log(`変更: ${file.getName()} → ${newName}`);
    }
  }

  Logger.log(`${count}ファイルの名前を変更しました`);
}
```

### ファイルの自動整理

```javascript
/**
 * ファイルを種類別にフォルダに自動整理
 */
function organizeFilesByType() {
  const SOURCE_FOLDER_ID = 'YOUR_SOURCE_FOLDER_ID';
  const sourceFolder = DriveApp.getFolderById(SOURCE_FOLDER_ID);

  // ファイルタイプとフォルダのマッピング
  const typeMapping = {
    'PDF': [MimeType.PDF],
    '画像': [MimeType.JPEG, MimeType.PNG, MimeType.GIF, MimeType.BMP],
    'ドキュメント': [MimeType.GOOGLE_DOCS, MimeType.MICROSOFT_WORD],
    'スプレッドシート': [MimeType.GOOGLE_SHEETS, MimeType.MICROSOFT_EXCEL],
    'プレゼン': [MimeType.GOOGLE_SLIDES, MimeType.MICROSOFT_POWERPOINT],
    '動画': [MimeType.MP4, 'video/quicktime', 'video/x-msvideo'],
    'その他': []  // デフォルト
  };

  // サブフォルダを作成または取得
  const subFolders = {};
  Object.keys(typeMapping).forEach(folderName => {
    const folders = sourceFolder.getFoldersByName(folderName);
    if (folders.hasNext()) {
      subFolders[folderName] = folders.next();
    } else {
      subFolders[folderName] = sourceFolder.createFolder(folderName);
    }
  });

  // ファイルを整理
  const files = sourceFolder.getFiles();
  let count = 0;

  while (files.hasNext()) {
    const file = files.next();
    const mimeType = file.getMimeType();

    // ファイルタイプを判定
    let targetFolder = null;
    for (const [folderName, mimeTypes] of Object.entries(typeMapping)) {
      if (mimeTypes.includes(mimeType)) {
        targetFolder = subFolders[folderName];
        break;
      }
    }

    // マッチしない場合は「その他」へ
    if (!targetFolder) {
      targetFolder = subFolders['その他'];
    }

    // ファイルを移動（既に正しいフォルダにある場合はスキップ）
    const parents = file.getParents();
    let alreadyInTargetFolder = false;

    while (parents.hasNext()) {
      const parent = parents.next();
      if (parent.getId() === targetFolder.getId()) {
        alreadyInTargetFolder = true;
        break;
      }
    }

    if (!alreadyInTargetFolder) {
      file.moveTo(targetFolder);
      count++;
      Logger.log(`移動: ${file.getName()} → ${targetFolder.getName()}`);
    }
  }

  Logger.log(`${count}ファイルを整理しました`);
}
```

---

*第3章は続きがありますが、文字数制限のため、ここで一旦区切ります。続きを次のセクションで作成します。*

# Google Apps Script 上級編＆デプロイメント完全ガイド

> 『Google Apps Script 完全ハンズオンガイド』の続編

---

## 目次

- [第3章（続き）: 実践編](#第3章続き実践編)
- [第4章: 応用編（上級者向け）](#第4章応用編上級者向け)
- [第5章: デプロイメント完全ガイド](#第5章デプロイメント完全ガイド)
- [付録](#付録)

---

# 第3章（続き）: 実践編

## フォームとの連携

### フォーム送信時の自動処理

```javascript
/**
 * フォーム送信トリガー関数
 * フォームの「拡張機能」→「Apps Script」から設定
 */
function onFormSubmit(e) {
  try {
    // フォーム回答を取得
    const responses = e.namedValues;

    Logger.log('フォーム送信を検知しました');
    Logger.log(JSON.stringify(responses, null, 2));

    // 各フィールドの値を取得
    const name = responses['お名前'][0];
    const email = responses['メールアドレス'][0];
    const message = responses['お問い合わせ内容'][0];

    // 1. 自動返信メールを送信
    sendConfirmationEmail(email, name);

    // 2. 管理者に通知
    notifyAdmin(name, email, message);

    // 3. スプレッドシートを整形
    formatResponseSheet();

    // 4. Slack通知（オプション）
    // notifySlack(name, email, message);

    Logger.log('処理完了');

  } catch (error) {
    Logger.log('エラー: ' + error.message);
    Logger.log(error.stack);

    // エラー時は管理者に通知
    MailApp.sendEmail(
      Session.getActiveUser().getEmail(),
      '【エラー】フォーム処理でエラーが発生',
      `エラー内容:\n${error.message}\n\n${error.stack}`
    );
  }
}

/**
 * 確認メールを送信
 */
function sendConfirmationEmail(email, name) {
  const subject = 'お問い合わせありがとうございます';
  const body = `
${name} 様

この度はお問い合わせいただき、誠にありがとうございます。

お問い合わせ内容を確認次第、担当者よりご連絡させていただきます。
今しばらくお待ちください。

※このメールは自動送信されています。

---
株式会社◯◯
カスタマーサポート
support@example.com
  `.trim();

  MailApp.sendEmail(email, subject, body);
  Logger.log('確認メール送信: ' + email);
}

/**
 * 管理者に通知
 */
function notifyAdmin(name, email, message) {
  const adminEmail = 'admin@example.com';
  const subject = '【新規】お問い合わせがありました';
  const body = `
新しいお問い合わせがありました。

■ お名前: ${name}
■ メール: ${email}
■ 内容:
${message}

スプレッドシートで確認:
${SpreadsheetApp.getActiveSpreadsheet().getUrl()}
  `.trim();

  MailApp.sendEmail(adminEmail, subject, body);
  Logger.log('管理者通知送信: ' + adminEmail);
}

/**
 * 回答シートを整形
 */
function formatResponseSheet() {
  const sheet = SpreadsheetApp.getActiveSheet();
  const lastRow = sheet.getLastRow();

  // 最新行に色をつける
  sheet.getRange(lastRow, 1, 1, sheet.getLastColumn())
    .setBackground('#e6f4ea');

  // タイムスタンプ列の日付フォーマット
  sheet.getRange(lastRow, 1)
    .setNumberFormat('yyyy/mm/dd hh:mm:ss');
}

/**
 * フォーム送信トリガーを設定
 */
function setupFormSubmitTrigger() {
  const form = FormApp.getActiveForm();

  // 既存のトリガーを削除
  const triggers = ScriptApp.getProjectTriggers();
  triggers.forEach(trigger => {
    if (trigger.getHandlerFunction() === 'onFormSubmit') {
      ScriptApp.deleteTrigger(trigger);
    }
  });

  // 新しいトリガーを作成
  ScriptApp.newTrigger('onFormSubmit')
    .forForm(form)
    .onFormSubmit()
    .create();

  Logger.log('フォーム送信トリガーを設定しました');
}
```

## トリガーとイベント

### トリガーの種類

```javascript
/**
 * すべてのトリガータイプの例
 */

// 1. 時間ベーストリガー

// 毎日午前9時
function createDailyTrigger() {
  ScriptApp.newTrigger('dailyFunction')
    .timeBased()
    .atHour(9)
    .everyDays(1)
    .create();
}

// 毎週月曜日
function createWeeklyTrigger() {
  ScriptApp.newTrigger('weeklyFunction')
    .timeBased()
    .onWeekDay(ScriptApp.WeekDay.MONDAY)
    .atHour(9)
    .create();
}

// 毎月1日
function createMonthlyTrigger() {
  ScriptApp.newTrigger('monthlyFunction')
    .timeBased()
    .onMonthDay(1)
    .atHour(9)
    .create();
}

// 1時間ごと
function createHourlyTrigger() {
  ScriptApp.newTrigger('hourlyFunction')
    .timeBased()
    .everyHours(1)
    .create();
}

// 2. イベントベーストリガー（スプレッドシート）

function createOnEditTrigger() {
  ScriptApp.newTrigger('onEditFunction')
    .forSpreadsheet(SpreadsheetApp.getActive())
    .onEdit()
    .create();
}

function createOnChangeTrigger() {
  ScriptApp.newTrigger('onChangeFunction')
    .forSpreadsheet(SpreadsheetApp.getActive())
    .onChange()
    .create();
}

// 3. イベントベーストリガー（フォーム）

function createFormSubmitTrigger() {
  ScriptApp.newTrigger('onFormSubmitFunction')
    .forForm(FormApp.getActiveForm())
    .onFormSubmit()
    .create();
}

// 4. イベントベーストリガー（カレンダー）

function createCalendarTrigger() {
  ScriptApp.newTrigger('onCalendarUpdate')
    .forUserCalendar('calendar@example.com')
    .onEventUpdated()
    .create();
}
```

### トリガー管理関数

```javascript
/**
 * すべてのトリガーを一覧表示
 */
function listAllTriggers() {
  const triggers = ScriptApp.getProjectTriggers();

  triggers.forEach((trigger, index) => {
    Logger.log(`\n=== トリガー ${index + 1} ===`);
    Logger.log('関数: ' + trigger.getHandlerFunction());
    Logger.log('イベント: ' + trigger.getEventType());
    Logger.log('トリガーID: ' + trigger.getUniqueId());
  });

  Logger.log(`\n合計: ${triggers.length}個のトリガー`);
}

/**
 * 特定の関数のトリガーを削除
 */
function deleteTriggersForFunction(functionName) {
  const triggers = ScriptApp.getProjectTriggers();
  let deletedCount = 0;

  triggers.forEach(trigger => {
    if (trigger.getHandlerFunction() === functionName) {
      ScriptApp.deleteTrigger(trigger);
      deletedCount++;
    }
  });

  Logger.log(`${deletedCount}個のトリガーを削除しました`);
}

/**
 * すべてのトリガーを削除
 */
function deleteAllTriggers() {
  const triggers = ScriptApp.getProjectTriggers();

  triggers.forEach(trigger => {
    ScriptApp.deleteTrigger(trigger);
  });

  Logger.log(`${triggers.length}個のトリガーを削除しました`);
}
```

## 外部API連携

### REST APIの呼び出し

```javascript
/**
 * 基本的なGET リクエスト
 */
function fetchDataFromAPI() {
  const url = 'https://api.example.com/data';

  try {
    const response = UrlFetchApp.fetch(url);
    const statusCode = response.getResponseCode();

    if (statusCode === 200) {
      const content = response.getContentText();
      const data = JSON.parse(content);

      Logger.log('データ取得成功');
      Logger.log(JSON.stringify(data, null, 2));

      return data;
    } else {
      Logger.log('エラー: ステータスコード ' + statusCode);
      return null;
    }

  } catch (error) {
    Logger.log('APIエラー: ' + error.message);
    return null;
  }
}

/**
 * 認証付きGET リクエスト
 */
function fetchWithAuth() {
  const url = 'https://api.example.com/protected';
  const apiKey = PropertiesService.getScriptProperties().getProperty('API_KEY');

  const options = {
    method: 'get',
    headers: {
      'Authorization': 'Bearer ' + apiKey,
      'Content-Type': 'application/json'
    },
    muteHttpExceptions: true  // エラーでも例外を投げない
  };

  const response = UrlFetchApp.fetch(url, options);
  const statusCode = response.getResponseCode();

  if (statusCode === 200) {
    return JSON.parse(response.getContentText());
  } else {
    Logger.log('エラー: ' + statusCode);
    Logger.log(response.getContentText());
    return null;
  }
}

/**
 * POST リクエスト
 */
function postDataToAPI() {
  const url = 'https://api.example.com/create';

  const payload = {
    name: '田中太郎',
    email: 'tanaka@example.com',
    age: 30
  };

  const options = {
    method: 'post',
    contentType: 'application/json',
    payload: JSON.stringify(payload),
    muteHttpExceptions: true
  };

  const response = UrlFetchApp.fetch(url, options);
  const statusCode = response.getResponseCode();

  if (statusCode === 201 || statusCode === 200) {
    Logger.log('データ送信成功');
    return JSON.parse(response.getContentText());
  } else {
    Logger.log('エラー: ' + statusCode);
    return null;
  }
}

/**
 * リトライ機能付きAPI呼び出し
 */
function fetchWithRetry(url, options = {}, maxRetries = 3) {
  let lastError;

  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = UrlFetchApp.fetch(url, options);

      if (response.getResponseCode() < 500) {
        // サーバーエラー以外はそのまま返す
        return response;
      }

      // 5xxエラーの場合はリトライ
      Logger.log(`試行 ${i + 1}/${maxRetries}: サーバーエラー`);

    } catch (error) {
      lastError = error;
      Logger.log(`試行 ${i + 1}/${maxRetries} 失敗: ${error.message}`);
    }

    // リトライ前に待機（指数バックオフ）
    if (i < maxRetries - 1) {
      const waitTime = Math.pow(2, i) * 1000;  // 1秒, 2秒, 4秒
      Utilities.sleep(waitTime);
    }
  }

  throw new Error(`${maxRetries}回の試行後も失敗: ${lastError.message}`);
}
```

### Webhook の実装

```javascript
/**
 * Webhookを受信する関数（doPost）
 * デプロイしてWebアプリとして公開する必要があります
 */
function doPost(e) {
  try {
    // リクエストボディを取得
    const data = JSON.parse(e.postData.contents);

    Logger.log('Webhook受信');
    Logger.log(JSON.stringify(data, null, 2));

    // データを処理
    const result = processWebhookData(data);

    // スプレッドシートに記録
    recordWebhookData(data);

    // レスポンスを返す
    return ContentService.createTextOutput(
      JSON.stringify({
        status: 'success',
        message: 'データを受信しました',
        result: result
      })
    ).setMimeType(ContentService.MimeType.JSON);

  } catch (error) {
    Logger.log('Webhookエラー: ' + error.message);

    return ContentService.createTextOutput(
      JSON.stringify({
        status: 'error',
        message: error.message
      })
    ).setMimeType(ContentService.MimeType.JSON);
  }
}

/**
 * GET リクエストにも対応
 */
function doGet(e) {
  const params = e.parameter;

  return ContentService.createTextOutput(
    JSON.stringify({
      status: 'ok',
      message: 'Webhook endpoint is active',
      timestamp: new Date().toISOString()
    })
  ).setMimeType(ContentService.MimeType.JSON);
}

/**
 * Webhookデータを処理
 */
function processWebhookData(data) {
  // ここでデータを処理
  return { processed: true };
}

/**
 * Webhookデータをスプレッドシートに記録
 */
function recordWebhookData(data) {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  let sheet = ss.getSheetByName('Webhook ログ');

  if (!sheet) {
    sheet = ss.insertSheet('Webhook ログ');
    sheet.appendRow(['タイムスタンプ', 'データ']);
  }

  sheet.appendRow([
    new Date(),
    JSON.stringify(data)
  ]);
}
```

## データ処理テクニック

### CSV インポート/エクスポート

```javascript
/**
 * CSVファイルをスプレッドシートにインポート
 */
function importCSVToSheet() {
  // ドライブからCSVファイルを取得
  const fileId = 'YOUR_CSV_FILE_ID';
  const file = DriveApp.getFileById(fileId);
  const csvData = file.getBlob().getDataAsString('UTF-8');

  // CSVをパース
  const rows = Utilities.parseCsv(csvData);

  // スプレッドシートに書き込み
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  let sheet = ss.getSheetByName('インポート');

  if (!sheet) {
    sheet = ss.insertSheet('インポート');
  } else {
    sheet.clear();
  }

  sheet.getRange(1, 1, rows.length, rows[0].length).setValues(rows);

  Logger.log(`${rows.length}行をインポートしました`);
}

/**
 * スプレッドシートをCSVとしてエクスポート
 */
function exportSheetToCSV() {
  const sheet = SpreadsheetApp.getActiveSheet();
  const data = sheet.getDataRange().getValues();

  // CSVフォーマットに変換
  const csv = data.map(row => {
    return row.map(cell => {
      // カンマや改行を含む場合はクォートで囲む
      const cellStr = String(cell);
      if (cellStr.includes(',') || cellStr.includes('\n') || cellStr.includes('"')) {
        return '"' + cellStr.replace(/"/g, '""') + '"';
      }
      return cellStr;
    }).join(',');
  }).join('\n');

  // ファイルとして保存
  const fileName = sheet.getName() + '_' + Utilities.formatDate(new Date(), 'JST', 'yyyyMMdd') + '.csv';
  const file = DriveApp.createFile(fileName, csv, MimeType.CSV);

  Logger.log('CSVエクスポート完了: ' + file.getUrl());
  return file.getUrl();
}
```

### JSON データの処理

```javascript
/**
 * 複雑なJSONデータを扱う
 */
function processComplexJSON() {
  // サンプルJSON
  const jsonData = {
    users: [
      { id: 1, name: '田中', age: 30, skills: ['JavaScript', 'Python'] },
      { id: 2, name: '佐藤', age: 25, skills: ['Java', 'Go'] },
      { id: 3, name: '鈴木', age: 35, skills: ['Python', 'R', 'SQL'] }
    ],
    metadata: {
      total: 3,
      page: 1,
      timestamp: '2025-01-15T10:00:00Z'
    }
  };

  // データを抽出
  const users = jsonData.users;
  const totalUsers = jsonData.metadata.total;

  // フィルタリング: 30歳以上
  const seniorUsers = users.filter(user => user.age >= 30);

  // マッピング: 名前のリストを取得
  const names = users.map(user => user.name);

  // 集計: 平均年齢
  const avgAge = users.reduce((sum, user) => sum + user.age, 0) / users.length;

  // スプレッドシートに出力
  const sheet = SpreadsheetApp.getActiveSheet();
  sheet.clear();

  // ヘッダー
  sheet.appendRow(['ID', '名前', '年齢', 'スキル']);

  // データ行
  users.forEach(user => {
    sheet.appendRow([
      user.id,
      user.name,
      user.age,
      user.skills.join(', ')
    ]);
  });

  // 統計情報
  sheet.getRange(users.length + 3, 1, 1, 2)
    .setValues([['平均年齢', avgAge]])
    .setFontWeight('bold');

  Logger.log('JSON処理完了');
}
```

---

# 第4章: 応用編（上級者向け）

## カスタムアドオンの作成

### スプレッドシートアドオンの基本構造

```javascript
/**
 * アドオンがインストールされたときに実行される
 */
function onInstall(e) {
  onOpen(e);
}

/**
 * スプレッドシートが開かれたときに実行される
 */
function onOpen(e) {
  const ui = SpreadsheetApp.getUi();

  ui.createAddonMenu()
    .addItem('データ処理を実行', 'showSidebar')
    .addItem('設定', 'showSettings')
    .addSeparator()
    .addItem('ヘルプ', 'showHelp')
    .addToUi();
}

/**
 * サイドバーを表示
 */
function showSidebar() {
  const html = HtmlService.createHtmlOutputFromFile('Sidebar')
    .setTitle('データ処理ツール')
    .setWidth(300);

  SpreadsheetApp.getUi().showSidebar(html);
}

/**
 * 設定ダイアログを表示
 */
function showSettings() {
  const html = HtmlService.createHtmlOutputFromFile('Settings')
    .setWidth(400)
    .setHeight(300);

  SpreadsheetApp.getUi().showModalDialog(html, '設定');
}

/**
 * ヘルプを表示
 */
function showHelp() {
  const ui = SpreadsheetApp.getUi();
  ui.alert(
    'ヘルプ',
    'このアドオンは...\n\n使い方:\n1. ...\n2. ...',
    ui.ButtonSet.OK
  );
}
```

### HTMLサービスの活用

**Sidebar.html:**
```html
<!DOCTYPE html>
<html>
  <head>
    <base target="_top">
    <style>
      body {
        font-family: Arial, sans-serif;
        padding: 10px;
      }
      .button {
        background-color: #4285f4;
        color: white;
        border: none;
        padding: 10px 20px;
        margin: 5px 0;
        cursor: pointer;
        width: 100%;
      }
      .button:hover {
        background-color: #357ae8;
      }
      #status {
        margin-top: 10px;
        padding: 10px;
        border-radius: 4px;
      }
      .success {
        background-color: #d4edda;
        color: #155724;
      }
      .error {
        background-color: #f8d7da;
        color: #721c24;
      }
    </style>
  </head>
  <body>
    <h2>データ処理ツール</h2>

    <button class="button" onclick="processData()">
      データを処理
    </button>

    <button class="button" onclick="exportData()">
      データをエクスポート
    </button>

    <button class="button" onclick="clearData()">
      データをクリア
    </button>

    <div id="status"></div>

    <script>
      /**
       * データ処理を実行
       */
      function processData() {
        showStatus('処理中...', false);

        google.script.run
          .withSuccessHandler(onSuccess)
          .withFailureHandler(onFailure)
          .processSheetData();
      }

      /**
       * データエクスポート
       */
      function exportData() {
        showStatus('エクスポート中...', false);

        google.script.run
          .withSuccessHandler((url) => {
            showStatus('エクスポート完了！', true);
            window.open(url, '_blank');
          })
          .withFailureHandler(onFailure)
          .exportSheetToCSV();
      }

      /**
       * データクリア
       */
      function clearData() {
        if (!confirm('本当にクリアしますか？')) {
          return;
        }

        google.script.run
          .withSuccessHandler(() => {
            showStatus('クリア完了！', true);
          })
          .withFailureHandler(onFailure)
          .clearSheetData();
      }

      /**
       * 成功時のハンドラー
       */
      function onSuccess(result) {
        showStatus(result.message || '処理完了！', true);
      }

      /**
       * 失敗時のハンドラー
       */
      function onFailure(error) {
        showStatus('エラー: ' + error.message, false);
      }

      /**
       * ステータス表示
       */
      function showStatus(message, isSuccess) {
        const statusDiv = document.getElementById('status');
        statusDiv.textContent = message;
        statusDiv.className = isSuccess ? 'success' : 'error';
      }
    </script>
  </body>
</html>
```

**サーバーサイド関数:**
```javascript
/**
 * シートデータを処理
 */
function processSheetData() {
  try {
    const sheet = SpreadsheetApp.getActiveSheet();
    const data = sheet.getDataRange().getValues();

    // データ処理ロジック
    const processedCount = data.length - 1;  // ヘッダーを除く

    return {
      success: true,
      message: `${processedCount}行を処理しました`
    };

  } catch (error) {
    throw new Error('処理中にエラーが発生しました: ' + error.message);
  }
}

/**
 * シートデータをクリア
 */
function clearSheetData() {
  const sheet = SpreadsheetApp.getActiveSheet();
  const lastRow = sheet.getLastRow();

  if (lastRow > 1) {
    sheet.getRange(2, 1, lastRow - 1, sheet.getLastColumn()).clear();
  }

  return { success: true, message: 'データをクリアしました' };
}
```

## Webアプリケーション開発

### 本格的なWebアプリの構築

**Code.gs:**
```javascript
/**
 * GETリクエストハンドラー
 */
function doGet(e) {
  const page = e.parameter.page || 'index';

  let template;
  switch (page) {
    case 'index':
      template = HtmlService.createTemplateFromFile('Index');
      break;
    case 'dashboard':
      template = HtmlService.createTemplateFromFile('Dashboard');
      template.data = getDashboardData();
      break;
    default:
      template = HtmlService.createTemplateFromFile('Index');
  }

  return template.evaluate()
    .setTitle('My Web App')
    .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);
}

/**
 * 共通HTMLをインクルード
 */
function include(filename) {
  return HtmlService.createHtmlOutputFromFile(filename).getContent();
}

/**
 * ダッシュボードデータを取得
 */
function getDashboardData() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('データ');
  const data = sheet.getDataRange().getValues();

  return {
    totalRecords: data.length - 1,
    lastUpdated: new Date().toISOString(),
    records: data.slice(1, 11)  // 最新10件
  };
}

/**
 * データを保存
 */
function saveData(formData) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('データ');

    sheet.appendRow([
      new Date(),
      formData.name,
      formData.email,
      formData.message
    ]);

    return { success: true, message: 'データを保存しました' };

  } catch (error) {
    return { success: false, message: error.message };
  }
}
```

**Index.html:**
```html
<!DOCTYPE html>
<html>
  <head>
    <base target="_top">
    <?!= include('Stylesheet'); ?>
  </head>
  <body>
    <?!= include('Header'); ?>

    <div class="container">
      <h1>データ登録フォーム</h1>

      <form id="dataForm">
        <div class="form-group">
          <label>お名前</label>
          <input type="text" name="name" required>
        </div>

        <div class="form-group">
          <label>メールアドレス</label>
          <input type="email" name="email" required>
        </div>

        <div class="form-group">
          <label>メッセージ</label>
          <textarea name="message" rows="5" required></textarea>
        </div>

        <button type="submit" class="btn">送信</button>
      </form>

      <div id="message"></div>
    </div>

    <script>
      document.getElementById('dataForm').addEventListener('submit', function(e) {
        e.preventDefault();

        const formData = {
          name: this.name.value,
          email: this.email.value,
          message: this.message.value
        };

        google.script.run
          .withSuccessHandler(showSuccess)
          .withFailureHandler(showError)
          .saveData(formData);
      });

      function showSuccess(result) {
        const messageDiv = document.getElementById('message');
        messageDiv.className = 'success';
        messageDiv.textContent = result.message;
        document.getElementById('dataForm').reset();
      }

      function showError(error) {
        const messageDiv = document.getElementById('message');
        messageDiv.className = 'error';
        messageDiv.textContent = 'エラー: ' + error.message;
      }
    </script>
  </body>
</html>
```

## パフォーマンス最適化

### バッチ処理の最適化

```javascript
/**
 * 悪い例: 1セルずつ処理
 */
function slowMethod() {
  const sheet = SpreadsheetApp.getActiveSheet();

  for (let i = 1; i <= 1000; i++) {
    const value = sheet.getRange(i, 1).getValue();  // 1000回のAPI呼び出し
    const processed = value * 2;
    sheet.getRange(i, 2).setValue(processed);  // 1000回のAPI呼び出し
  }
}

/**
 * 良い例: 一括処理
 */
function fastMethod() {
  const sheet = SpreadsheetApp.getActiveSheet();

  // 1回の呼び出しで全データ取得
  const values = sheet.getRange(1, 1, 1000, 1).getValues();

  // メモリ上で処理
  const processed = values.map(row => [row[0] * 2]);

  // 1回の呼び出しで全データ書き込み
  sheet.getRange(1, 2, 1000, 1).setValues(processed);
}

/**
 * パフォーマンス比較
 */
function comparePerformance() {
  console.time('slowMethod');
  slowMethod();
  console.timeEnd('slowMethod');  // slowMethod: 30000ms

  console.time('fastMethod');
  fastMethod();
  console.timeEnd('fastMethod');  // fastMethod: 500ms
}
```

### キャッシュの活用

```javascript
/**
 * キャッシュを使ったデータ取得
 */
function getCachedData(cacheKey, fetchFunction, cacheTime = 600) {
  const cache = CacheService.getScriptCache();

  // キャッシュから取得を試みる
  let data = cache.get(cacheKey);

  if (data) {
    Logger.log('キャッシュヒット');
    return JSON.parse(data);
  }

  // キャッシュになければ関数を実行
  Logger.log('キャッシュミス - データを取得');
  data = fetchFunction();

  // キャッシュに保存
  cache.put(cacheKey, JSON.stringify(data), cacheTime);

  return data;
}

/**
 * 使用例
 */
function exampleUsingCache() {
  const userData = getCachedData(
    'user_data_123',
    () => {
      // 重い処理
      const sheet = SpreadsheetApp.getActiveSheet();
      return sheet.getDataRange().getValues();
    },
    1800  // 30分間キャッシュ
  );

  Logger.log('データ件数: ' + userData.length);
}
```

## セキュリティベストプラクティス

### 機密情報の管理

```javascript
/**
 * スクリプトプロパティで機密情報を管理
 */

// 設定（一度だけ実行）
function setupSecrets() {
  const scriptProperties = PropertiesService.getScriptProperties();

  scriptProperties.setProperties({
    'API_KEY': 'your-api-key-here',
    'DATABASE_URL': 'https://db.example.com',
    'ADMIN_EMAIL': 'admin@example.com'
  });

  Logger.log('機密情報を設定しました');
}

// 使用
function useSecrets() {
  const scriptProperties = PropertiesService.getScriptProperties();

  const apiKey = scriptProperties.getProperty('API_KEY');
  const dbUrl = scriptProperties.getProperty('DATABASE_URL');

  // APIを呼び出す
  callExternalAPI(apiKey, dbUrl);
}

/**
 * 入力検証
 */
function validateInput(email, name, age) {
  const errors = [];

  // メールアドレス検証
  if (!email || !isValidEmail(email)) {
    errors.push('有効なメールアドレスを入力してください');
  }

  // 名前検証
  if (!name || name.trim().length < 2) {
    errors.push('名前は2文字以上入力してください');
  }

  // 年齢検証
  if (!age || age < 0 || age > 150) {
    errors.push('有効な年齢を入力してください');
  }

  if (errors.length > 0) {
    throw new Error(errors.join('\n'));
  }

  return true;
}

function isValidEmail(email) {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email);
}
```

---

# 第5章: デプロイメント完全ガイド

## デプロイの種類

Google Apps Scriptには複数のデプロイ方法があります：

1. **スタンドアロンスクリプト**: 独立したスクリプト
2. **Webアプリ**: WebアプリケーションとしてURL公開
3. **アドオン**: Google Workspaceアドオンとして配布
4. **ライブラリ**: 他のスクリプトから利用可能なライブラリ
5. **API実行可能**: REST APIとして実行

## スタンドアロンスクリプトのデプロイ

### 基本的なデプロイ手順

1. **スクリプトエディタを開く**
   - script.google.com にアクセス
   - プロジェクトを選択

2. **デプロイ準備**
   - コードをテスト
   - 必要な権限を確認
   - プロジェクト設定を確認

3. **デプロイ実行**
   - 「デプロイ」→「新しいデプロイ」
   - デプロイタイプを選択
   - バージョンと説明を入力
   - 「デプロイ」をクリック

4. **権限の付与**
   - 初回実行時に権限承認が必要
   - 「権限を確認」→「許可」

## Webアプリとしてのデプロイ

### デプロイ手順

```javascript
/**
 * Webアプリのエントリーポイント
 */
function doGet(e) {
  const template = HtmlService.createTemplateFromFile('Index');
  template.userName = Session.getActiveUser().getEmail();

  return template.evaluate()
    .setTitle('My Web App')
    .setFaviconUrl('https://example.com/favicon.ico')
    .setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);
}
```

### デプロイ設定

1. **「デプロイ」→「新しいデプロイ」**

2. **種類の選択**: 「ウェブアプリ」

3. **設定項目**:
   - **説明**: バージョンの説明
   - **次のユーザーとして実行**:
     - 自分: スクリプト所有者として実行
     - アクセスしているユーザー: アクセスユーザーとして実行
   - **アクセスできるユーザー**:
     - 自分のみ
     - 組織内の全員
     - 全員

4. **URLの取得**
   - デプロイ完了後、WebアプリURLが発行される
   - 例: `https://script.google.com/macros/s/ABC.../exec`

### 新しいバージョンのデプロイ

```javascript
/**
 * バージョン情報を含める
 */
function doGet(e) {
  const VERSION = '1.2.0';

  const html = HtmlService.createTemplate(`
    <!DOCTYPE html>
    <html>
      <body>
        <h1>My Web App</h1>
        <p>Version: ${VERSION}</p>
      </body>
    </html>
  `);

  return html.evaluate();
}
```

**バージョン更新手順**:
1. コードを修正
2. 「デプロイ」→「デプロイを管理」
3. 編集アイコンをクリック
4. 「新しいバージョン」を選択
5. 変更内容を記述
6. 「デプロイ」

## アドオンのデプロイ

### マニフェストファイルの設定

**appsscript.json:**
```json
{
  "timeZone": "Asia/Tokyo",
  "dependencies": {
    "enabledAdvancedServices": []
  },
  "exceptionLogging": "STACKDRIVER",
  "runtimeVersion": "V8",
  "oauthScopes": [
    "https://www.googleapis.com/auth/spreadsheets.currentonly",
    "https://www.googleapis.com/auth/script.container.ui"
  ],
  "addOns": {
    "common": {
      "name": "My Awesome Add-on",
      "logoUrl": "https://example.com/logo.png",
      "layoutProperties": {
        "primaryColor": "#4285f4"
      },
      "useLocaleFromApp": true,
      "homepageTrigger": {
        "runFunction": "onHomepage",
        "enabled": true
      },
      "universalActions": [{
        "label": "アクションを実行",
        "runFunction": "handleUniversalAction"
      }]
    },
    "sheets": {
      "homepageTrigger": {
        "runFunction": "onSheetsHomepage"
      },
      "onFileScopeGrantedTrigger": {
        "runFunction": "onFileScopeGranted"
      }
    },
    "docs": {
      "homepageTrigger": {
        "runFunction": "onDocsHomepage"
      }
    }
  }
}
```

### アドオン公開手順

1. **Google Cloud Projectの設定**
   - Google Cloud Consoleでプロジェクト作成
   - Apps Scriptプロジェクトとリンク

2. **OAuth同意画面の設定**
   - アプリ名、ロゴ、説明を設定
   - スコープを追加
   - テストユーザーを追加（開発中）

3. **ストアリスト情報の準備**
   - アプリ名
   - 簡単な説明（短い説明）
   - 詳細な説明
   - スクリーンショット（1280x800px推奨）
   - アイコン（96x96px）
   - プロモーション画像（440x280px）

4. **Google Workspace Marketplaceへの公開**
   - Apps Scriptエディタで「公開」→「Google Workspace Marketplace SDK」
   - ストアリスト情報を入力
   - カテゴリとタグを設定
   - 利用規約とプライバシーポリシーのURLを追加

5. **審査と公開**
   - 非公開テスト（限定ユーザーのみ）
   - 公開審査を申請
   - 承認後、一般公開

## ライブラリの公開

### ライブラリの作成

```javascript
/**
 * ライブラリの公開関数
 */

/**
 * データを検証
 */
function validateData(data) {
  if (!data) {
    throw new Error('データが空です');
  }
  return true;
}

/**
 * 日付をフォーマット
 */
function formatDate(date, format = 'yyyy-MM-dd') {
  return Utilities.formatDate(date, Session.getScriptTimeZone(), format);
}

/**
 * メールアドレスを検証
 */
function isValidEmail(email) {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(email);
}
```

### ライブラリの公開手順

1. **バージョンの作成**
   - 「デプロイ」→「新しいデプロイ」
   - 種類: 「ライブラリ」
   - バージョン: 「新しいバージョン」
   - 説明を入力
   - 「デプロイ」

2. **ライブラリIDの取得**
   - デプロイ後、ライブラリIDが表示される
   - 例: `1ABC...XYZ`

3. **他のプロジェクトから使用**
   - 「ライブラリ」→「ライブラリを追加」
   - ライブラリIDを入力
   - バージョンを選択
   - 識別子を設定（例: `MyLib`）
   - 「追加」

4. **使用例**:
```javascript
function useLibrary() {
  // ライブラリの関数を呼び出す
  const isValid = MyLib.isValidEmail('test@example.com');

  const formattedDate = MyLib.formatDate(new Date(), 'yyyy年MM月dd日');

  Logger.log('メール有効: ' + isValid);
  Logger.log('日付: ' + formattedDate);
}
```

## バージョン管理

### セマンティックバージョニング

```
MAJOR.MINOR.PATCH

例: 2.1.3
  - MAJOR (2): 互換性のない変更
  - MINOR (1): 後方互換性のある機能追加
  - PATCH (3): 後方互換性のあるバグ修正
```

### バージョン管理のベストプラクティス

```javascript
/**
 * バージョン情報を管理
 */
const VERSION = {
  major: 2,
  minor: 1,
  patch: 3,
  toString: function() {
    return `${this.major}.${this.minor}.${this.patch}`;
  }
};

/**
 * バージョン情報を表示
 */
function showVersion() {
  Logger.log('Version: ' + VERSION.toString());
}

/**
 * 互換性チェック
 */
function checkCompatibility(requiredVersion) {
  const required = requiredVersion.split('.').map(Number);
  const current = [VERSION.major, VERSION.minor, VERSION.patch];

  // メジャーバージョンが一致するかチェック
  if (current[0] !== required[0]) {
    throw new Error(
      `互換性のないバージョンです。必要: ${requiredVersion}, 現在: ${VERSION.toString()}`
    );
  }

  return true;
}
```

## CI/CDパイプライン

### claspを使った自動デプロイ

**1. claspのインストール**
```bash
npm install -g @google/clasp
```

**2. ログイン**
```bash
clasp login
```

**3. プロジェクトのクローン**
```bash
clasp clone <SCRIPT_ID>
```

**4. .clasp.jsonの設定**
```json
{
  "scriptId": "YOUR_SCRIPT_ID",
  "rootDir": "./src"
}
```

**5. プッシュとデプロイ**
```bash
clasp push
clasp deploy --description "v1.0.0"
```

### GitHub Actionsでの自動デプロイ

**.github/workflows/deploy.yml:**
```yaml
name: Deploy to Google Apps Script

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '16'

      - name: Install clasp
        run: npm install -g @google/clasp

      - name: Create .clasprc.json
        run: echo '${{ secrets.CLASPRC_JSON }}' > ~/.clasprc.json

      - name: Push to Google Apps Script
        run: clasp push

      - name: Deploy
        run: clasp deploy --description "Deployed from GitHub Actions"
```

---

# 付録

## トラブルシューティング

### よくあるエラーと解決方法

#### 1. "Exception: Service invoked too many times"

**原因**: API呼び出し回数制限超過

**解決方法**:
```javascript
// バッチ処理に変更
function optimizedFunction() {
  const sheet = SpreadsheetApp.getActiveSheet();

  // ❌ 悪い: 100回のAPI呼び出し
  // for (let i = 1; i <= 100; i++) {
  //   sheet.getRange(i, 1).setValue(i);
  // }

  // ✅ 良い: 1回のAPI呼び出し
  const data = Array.from({length: 100}, (_, i) => [i + 1]);
  sheet.getRange(1, 1, 100, 1).setValues(data);
}
```

#### 2. "Exception: Authorization is required"

**原因**: 必要な権限が付与されていない

**解決方法**:
1. スクリプトを実行
2. 「権限の確認」をクリック
3. Googleアカウントを選択
4. 「許可」をクリック

#### 3. "Exception: You do not have permission to call..."

**原因**: 必要なスコープが宣言されていない

**解決方法**:
`appsscript.json`にスコープを追加:
```json
{
  "oauthScopes": [
    "https://www.googleapis.com/auth/spreadsheets",
    "https://www.googleapis.com/auth/gmail.send"
  ]
}
```

## API制限と割当量

| リソース | 無料アカウント | Google Workspace |
|----------|---------------|------------------|
| スクリプト実行時間 | 6分 | 6分 |
| カスタム関数実行時間 | 30秒 | 30秒 |
| トリガー総実行時間 | 90分/日 | 90分/日 |
| 同時実行 | 30 | 30 |
| URLFetch呼び出し | 20,000/日 | 100,000/日 |
| URLFetchデータ | 50 MB/日 | 750 MB/日 |
| メール送信 | 100/日 | 1,500/日 |
| スプレッドシートセル | 1,000万 | 1,000万 |

## 便利なライブラリ

### 1. Moment.js風の日付処理
```
ライブラリID: MHMchiX6c1bwSqGM1PZiW_PxhMjh3Sh48
識別子: Moment
```

### 2. Underscore.js
```
ライブラリID: M3i7wmUA_5n0NSEaa6NnNdOBao7QLBR4j
識別子: Underscore
```

### 3. OAuth2 for Apps Script
```
ライブラリID: 1B7FSrk5Zi6L1rSxxTDgDEUsPzlukDsi4KGuTMorsTQHhGBzBkMun4iDF
識別子: OAuth2
```

## 参考資料

### 公式ドキュメント
- [Google Apps Script公式ガイド](https://developers.google.com/apps-script)
- [Apps Scriptリファレンス](https://developers.google.com/apps-script/reference)
- [ベストプラクティス](https://developers.google.com/apps-script/guides/support/best-practices)

### コミュニティ
- [Stack Overflow - google-apps-script](https://stackoverflow.com/questions/tagged/google-apps-script)
- [Google Apps Script Community](https://www.googleappsscript.community/)

### ツール
- [clasp - Apps Script CLI](https://github.com/google/clasp)
- [gas-webpack-plugin](https://www.npmjs.com/package/gas-webpack-plugin)

---

## まとめ

このガイドでは、Google Apps Scriptの基礎から上級テクニック、そしてデプロイまでを網羅しました。

### 学習した内容

- ✅ 基礎: JavaScript、基本サービス、デバッグ
- ✅ 実践: スプレッドシート、Gmail、Drive、フォーム連携
- ✅ 応用: カスタムアドオン、Webアプリ、パフォーマンス最適化
- ✅ デプロイ: Webアプリ、アドオン、ライブラリの公開

### 次のステップ

1. **小さなプロジェクトから始める**
   - 日常業務の自動化
   - 簡単なツールの作成

2. **コミュニティに参加する**
   - Stack Overflowで質問・回答
   - GitHub でコードを公開

3. **継続的な学習**
   - 公式ドキュメントを定期的にチェック
   - 新機能を試す

### 最後に

Google Apps Scriptは、プログラミング初心者でも始めやすく、かつ強力なツールです。
このガイドがあなたの自動化の旅の助けとなれば幸いです。

Happy Coding! 🚀

---

**作成日**: 2025年1月
**バージョン**: 2.0
**対象**: Google Apps Script (V8 Runtime)

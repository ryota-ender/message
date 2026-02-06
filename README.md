# 寄せ書きページ（スマホ向け）

このフォルダの `index.html` は、スマホでの閲覧を想定した寄せ書きページです。  
メッセージとパスワードは Google スプレッドシート + GAS から取得します。

## ファイル構成
- `index.html`  
  寄せ書きUI本体（GASからデータ取得）
- `images/`  
  皿・料理・ドリンク画像

## スプレッドシートの準備
スプレッドシートに 2 つのシートを作成してください。

### 1) `members` シート
| 名前 | パスワード |
| --- | --- |
| ヒロイシ カイト | 0000 |

### 2) `messages` シート
| 宛先 | 差出人 | メッセージ |
| --- | --- | --- |
| ヒロイシ カイト | 店長 | 卒業おめでとう！ |

※ `members.名前` と `messages.宛先` は完全一致させてください。

## GAS（doGet）の例
```javascript
function doGet() {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const membersSheet = ss.getSheetByName("members");
  const messagesSheet = ss.getSheetByName("messages");

  const members = sheetToObjects(membersSheet);
  const messages = sheetToObjects(messagesSheet);

  return ContentService
    .createTextOutput(JSON.stringify({ members, messages }))
    .setMimeType(ContentService.MimeType.JSON);
}

function sheetToObjects(sheet) {
  const values = sheet.getDataRange().getValues();
  const headers = values.shift();
  return values.map(row => {
    const obj = {};
    headers.forEach((h, i) => obj[h] = row[i]);
    return obj;
  });
}
```

## `index.html` の設定
GASのURLを `GAS_URL` に設定してください。

```javascript
const GAS_URL = "https://script.google.com/macros/s/xxxxx/exec";
```

## 表示仕様（送信者一覧）
- 送信者は **dish画像** に名前を載せて表示  
- dish と cooking を交互に配置  
- 1行（dish + cooking）の後に **drink** を挿入  
- 皿の左右位置は上下に少しずらして表示（傾きなし）

## 画像について
`images/` にある画像を使います。

- dish: `dish.png` ～ `dish5.png` を循環
- cooking: `cooking.png` ～ `cooking10.png` を循環
- drink: `drink.png`

## ローカル確認（任意）
`file://` だと `fetch` が制限される場合があるため、  
ローカルサーバーでの確認を推奨します。

```bash
python3 -m http.server 8000
```

ブラウザで `http://localhost:8000/` を開いて確認してください。

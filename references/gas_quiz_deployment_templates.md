# Google Apps Script (FormApp) 自動建表程式碼範本與技術規範

本檔案定義【步驟 1️⃣】第三部分輸出之 Google Apps Script (`Code.gs`) 標準結構、純文字數學與科學符號轉換規則，以及支援雲端資料夾與試算表屬性 (`PropertiesService`) 之擴充機制。

---

## 一、 Google 表單命題與程式碼撰寫 5 大鐵律

1. **零硬編碼與零外部設定門檻**：
   - 一般教師直接複製貼上點擊「▶️ 執行」即可在自己的 Google 雲端硬碟建立測驗表單。
   - 支援進階管理擴充：透過 `PropertiesService.getScriptProperties().getProperty('FOLDER_ID')` 與 `getProperty('SHEET_ID')` 讀取選配環境變數（若教師有設定專案屬性 `FOLDER_ID`，自動將生成的表單移入指定資料夾；若未設定則安全保留於雲端硬碟根目錄，絕不硬編碼任何私人 ID 或拋出錯誤）。
2. **自動產生具體表單名稱並同步至步驟 4️⃣**：
   - `const formTitle` 必須根據教師提供的「任教年級、科目與教材主題」自動命名（例如：`const formTitle = '【國小三年級數學】單元測驗：認識分數';`）。
   - 在【步驟 4️⃣】提示教師檢查表單時，必須原封不動帶入此 `formTitle` 字串。
3. **純文字數學與科學符號規範（嚴禁 LaTeX）**：
   - Google 表單不支援 LaTeX 語法，題幹、選項與詳解字串中**嚴禁出現 `\frac`、`\times`、`\div`、`$...$`、`^2` 等語法**。
   - 一律使用標準純文字與 Unicode 符號：
     - 分數：`3/8`、`2 又 1/4`、填空 `( )/( )`
     - 運算與關係：`＋`、`－`、`×`、`÷`、`＝`、`＞`、`＜`、`≥`、`≤`、`≠`、`≈`
     - 單位與上標下標：`cm²`、`m²`、`cm³`、`m³`、`°C`、`°`、`CO₂`、`H₂O`
4. **選項格式與唯一正解防呆**：
   - 使用 `item.createChoice(optText, idx === q.correctIndex)` 建立選項，避免使用舊版或不存在的 API。
   - 選項字串統一加上 `(A) `、`(B) `、`(C) `、`(D) ` 前綴，方便師生檢討時核對代號。
5. **雙向詳解回饋（答對與答錯皆顯示完整解析與課綱指標）**：
   - 使用 `FormApp.createFeedback().setText(q.explanation).build()` 建立回饋物件，並同時綁定 `item.setFeedbackForCorrect(feedback)` 與 `item.setFeedbackForIncorrect(feedback)`，確保學生無論答對或答錯，交卷後都能看到對應的【知識節點】、【108 課綱指標】、【正解與迷思解析】及【教材參考出處】。

---

## 二、 標準輸出程式碼範本（於步驟 1️⃣ 第三部分依教材內容動態填入）

```javascript
/**
 * 🏫 國中小教材診斷測驗自動建表程式 (Google Apps Script)
 * 使用方式：貼上程式碼 -> 點擊上方「💾 將專案儲存在雲端硬碟」 -> 點擊「▶️ 執行」
 */
function createQuizForm() {
  // 1. 設定表單標題與測驗說明（依教師年級、科目與教材主題自動命名）
  const formTitle = '【國小三年級數學】單元測驗：認識分數';
  const formDescription = '請同學先填寫班級、座號與姓名，再仔細閱讀題目作答。\n本測驗共 5 題，每題 20 分，滿分 100 分！';

  // 2. 建立 Google 表單並開啟「設為測驗」自動評分模式
  const form = FormApp.create(formTitle);
  form.setDescription(formDescription);
  form.setIsQuiz(true);

  // 3. 建立學生基本資料必填欄位（班級、座號、姓名）
  form.addTextItem().setTitle('班級').setRequired(true);
  form.addTextItem().setTitle('座號').setRequired(true);
  form.addTextItem().setTitle('姓名').setRequired(true);

  // 4. 測驗題庫陣列（包含題幹、四個選項、正確答案索引 0~3、配分、108課綱指標與詳解回饋）
  const questions = [
    {
      title: '1. 媽媽買了一個圓形披薩，平分成 8 塊。小華吃了其中的 3 塊，請問小華吃了幾個披薩？',
      options: [
        '(A) 3/8 個',
        '(B) 8/3 個',
        '(C) 3/5 個',
        '(D) 1/8 個'
      ],
      correctIndex: 0,
      points: 20,
      explanation: '【知識節點：單位分數的累積｜108課綱指標：n-II-6 / N-3-9 / NC-3-9-1】\n正確答案：(A)\n解析：把 1 個披薩「平分成 8 塊」，每 1 塊是 1/8 個披薩（分母為 8）；吃了其中的 3 塊，就是 3 個 1/8 合起來，記作 3/8 個披薩。\n常見迷思提醒：(B) 誤將分子與分母位置顛倒；(C) 誤將吃掉的 3 塊與剩下的 5 塊當成分子與分母。\n（參考出處：教材第 9-1 節「幾分之幾」）'
    },
    {
      title: '2. 下面哪一個圖形的塗色部分「不能」用 1/4 表示？',
      options: [
        '(A) 正方形沿對角線平分成 4 個一樣大的三角形，塗色 1 個',
        '(B) 長方形隨意畫 3 條直線分成大小不同的 4 塊，塗色 1 塊',
        '(C) 圓形通過圓心平分成 4 個一樣大的扇形，塗色 1 個',
        '(D) 一條長紙帶對摺再對摺平分成 4 等份，塗色 1 等份'
      ],
      correctIndex: 1,
      points: 20,
      explanation: '【知識節點：分數「平分」的前提意義｜108課綱指標：n-II-6 / N-3-9 / NC-3-9-1】\n正確答案：(B)\n解析：使用分數表示整體的一部分時，前提必須是將整體「平分（每一份一樣大）」。選項 (B) 分成的 4 塊大小不同，沒有平分，因此塗色部分不能說是 1/4。\n（參考出處：教材第 9-1 節「平分與分數」）'
    }
  ];

  // 5. 迴圈建立所有單選題、設定正確答案、配分與詳解回饋
  questions.forEach(function(q) {
    const item = form.addMultipleChoiceItem();
    item.setTitle(q.title)
        .setPoints(q.points)
        .setRequired(true);

    const choices = q.options.map(function(opt, idx) {
      return item.createChoice(opt, idx === q.correctIndex);
    });
    item.setChoices(choices);

    const feedback = FormApp.createFeedback()
      .setText(q.explanation)
      .build();
    item.setFeedbackForCorrect(feedback);
    item.setFeedbackForIncorrect(feedback);
  });

  // 6. （選配進階功能）若指令碼屬性有設定 FOLDER_ID 或 SHEET_ID，自動歸檔與綁定回覆試算表
  try {
    const props = PropertiesService.getScriptProperties();
    const folderId = props.getProperty('FOLDER_ID');
    const sheetId = props.getProperty('SHEET_ID');
    if (folderId) {
      const formFile = DriveApp.getFileById(form.getId());
      const targetFolder = DriveApp.getFolderById(folderId);
      formFile.moveTo(targetFolder);
    }
    if (sheetId) {
      form.setDestination(FormApp.DestinationType.SPREADSHEET, sheetId);
    }
  } catch (err) {
    Logger.log('提示：未設定或略過自訂屬性歸檔，表單已直接儲存於您的雲端硬碟根目錄。');
  }

  // 7. 於執行紀錄輸出成功訊息與網址
  Logger.log('====================================');
  Logger.log('✅ 表單建立成功！');
  Logger.log('📌 表單名稱：' + formTitle);
  Logger.log('✏️ 教師編輯網址：' + form.getEditUrl());
  Logger.log('📋 學生作答網址：' + form.getPublishedUrl());
  Logger.log('====================================');
}
```

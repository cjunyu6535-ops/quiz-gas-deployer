# 🏫 國中小教師考試部署小幫手 (`quiz-gas-deployer`)

> **跨平台相容 AI 技能套件 (Cross-Platform Agent Skill)**  
> 完美支援 **Gemini (Gems / Skills / CLI)**、**Claude Code**、**ChatGPT Codex CLI**、**OpenCode** 與 **Google Antigravity**。  
> 專為臺灣公立國中小教師設計，內建 **十二年國教（108 課綱）第一至第四學習階段各領域指標與迷思概念診斷資料庫**，協助教師將 PDF、截圖、課文、網址或 YouTube 教材轉化為高品質四選一診斷測驗，並產出可一鍵建立 Google 表單（含班級、座號、姓名、自動評分與詳解回饋）的 **Google Apps Script (`FormApp`) 程式碼**與防呆雲端部署教學。

---

## 📂 技能目錄結構 (Directory Structure)

```text
quiz-gas-deployer/
├── SKILL.md                                         # 技能主規格檔（YAML Frontmatter + 四階段互動狀態機）
├── README.md                                        # 跨平台安裝與使用說明文件
├── agents/
│   └── openai.yaml                                  # OpenAI Codex CLI 專屬介面與觸發設定
├── references/
│   ├── tw_108_curriculum_indicators_db.md           # 臺灣 108 課綱國中小各領域指標、國小基本學習內容(NC)與迷思診斷資料庫
│   └── gas_quiz_deployment_templates.md             # Google Apps Script (FormApp) 自動建表範本與純文字數學符號規範
└── assets/
    └── step_by_step_teacher_guide.md                # 步驟 2️⃣～4️⃣ 教師互動確認話術、雲端部署教學與 Google 表單設定檢核表
```

---

## 🌟 核心功能亮點

1. **內建 108 課綱全領域指標與迷思概念資料庫 (`references/tw_108_curriculum_indicators_db.md`)**：
   - 涵蓋國小低、中、高年級至國中（學習階段 `I`～`IV`）之 **國語文、英語文、數學（含領綱與國小基本學習內容 `NC/SC/RC/DC` 雙軌編碼）、自然科學、社會、生活課程、科技、健康與體育、藝術、綜合活動** 及 **19 項法定議題**。
   - 內建各年級螺旋式認知邊界護欄（防超綱）與常見學生迷思概念庫，確保干擾選項具備高診斷力。
2. **100% 來源錨定與純正符號排版**：
   - 嚴格依據教師提供的教材命題，不自行腦補外部事實；若教材不足以支撐題數則據實調整，絕不硬湊。
   - 針對 Google 表單不支援 LaTeX 的特性，自動將所有分數與算式轉為純文字與純正 Unicode 符號（如 `3/8`、`×`、`÷`、`cm²`）。
3. **四階段防呆互動狀態機**：
   - **開場階段**：親切自我介紹，收集任教年級／科目與教材。
   - **步驟 1️⃣**：產出【1. 題目清單（附知識節點與 108 課綱指標）】、【2. 答案與詳解（附教材出處與迷思分析）】、【3. Google Apps Script 自動建表程式碼】。
   - **步驟 2️⃣**：詢問老師是否需要修改題目，待老師確認「題目 OK」後才推進。
   - **步驟 3️⃣ & 4️⃣**：提供手把手雲端部署教學（特別提醒**先按『💾 將專案儲存在雲端硬碟』，『▶️ 執行』按鈕才會亮起來**），並自動帶入表單確切名稱，附上分數公布方式與學生檢視權限核對清單。

---

## 🚀 跨平台安裝指南 (Cross-Platform Installation)

### 1. Claude Code
```bash
# 個人全域安裝
git clone https://github.com/cjunyu6535-ops/quiz-gas-deployer.git ~/.claude/skills/quiz-gas-deployer

# 專案目錄安裝
git clone https://github.com/cjunyu6535-ops/quiz-gas-deployer.git .claude/skills/quiz-gas-deployer
```
- **呼叫方式**：輸入 `/quiz-gas-deployer` 或直接說「幫我把這份教材做成 Google 表單考題」。

### 2. OpenAI Codex CLI
```bash
git clone https://github.com/cjunyu6535-ops/quiz-gas-deployer.git ~/.agents/skills/quiz-gas-deployer
```
- **呼叫方式**：輸入 `$quiz-gas-deployer` 或以自然語言觸發。

### 3. OpenCode / Gemini CLI / Google Antigravity
```bash
# OpenCode 全域安裝
git clone https://github.com/cjunyu6535-ops/quiz-gas-deployer.git ~/.config/opencode/skills/quiz-gas-deployer

# 通用 Agent Skills 路徑
git clone https://github.com/cjunyu6535-ops/quiz-gas-deployer.git .agents/skills/quiz-gas-deployer
```
- **Gemini 網頁版 / Gems**：可直接上傳打包好的 `quiz-gas-deployer.zip` 至 Skills 介面，或將 `SKILL.md` 貼入指令區，並將 `references/` 與 `assets/` 內檔案上傳至知識庫。

# Doc Summary Notion Plugin

分析文檔並生成繁體中文摘要，自動上傳至 Notion AI Documents 資料庫。

## 功能特色

- 📄 **多來源支援**：支援 URL、本地文件、目錄分析
- 🇹🇼 **繁體中文摘要**：自動生成結構化的繁體中文內容摘要
- 🏷️ **智能標籤管理**：自動匹配現有標籤或創建新標籤
- 📊 **視覺化支援**：可選的 Mermaid 圖表增強理解
- 🖼️ **圖片保留**：保留原文檔中的重要圖片
- 🔗 **引用連結掃描**：自動抓取並分析文章中的參考連結（最多 5 個）
- ☁️ **Notion 整合**：自動上傳至指定的 Notion 資料庫

## 安裝方式

### 從 Marketplace 安裝

```bash
# 添加 marketplace
/plugin marketplace add hongwei0417/kevin-marketplace

# 安裝此 plugin
/plugin install doc-summary-notion@kevin-claude-marketplace
```

### 從本地安裝（開發測試）

```bash
git clone https://github.com/hongwei0417/kevin-marketplace.git
/plugin marketplace add /path/to/kevin-marketplace
/plugin install doc-summary-notion@kevin-claude-marketplace
```

## 環境配置

### 1. 設定 Notion API Key

在使用前需要設定 Notion API Key 環境變數：

**選項 A：設定環境變數**
```bash
export NOTION_TOKEN="your_notion_integration_token"
```

**選項 B：在 Claude Code 設定檔中配置**

編輯 `~/.claude/settings.json`：

```json
{
  "env": {
    "NOTION_TOKEN": "your_notion_integration_token"
  }
}
```

### 2. 獲取 Notion API Key

1. 訪問 [Notion Integrations](https://www.notion.so/my-integrations)
2. 點擊 "+ New integration"
3. 設定名稱並選擇 workspace
4. 複製 "Internal Integration Token"
5. 在 Notion 中將資料庫分享給此 integration

### 3. 準備 Notion 資料庫

確保你的 Notion workspace 中有一個名為 "AI Documents" 的資料庫，包含以下屬性：

- **Title**：文檔標題（title type）
- **Tags**：標籤（multi_select type）
- **URL**：來源網址（url type）

## 使用方法

### 基本用法

```bash
# 分析網頁文章
/doc-summary https://example.com/article

# 分析 YouTube 影片
/doc-summary https://www.youtube.com/watch?v=xxxxx

# 分析本地文件
/doc-summary ~/projects/README.md

# 分析目錄（會處理目錄中的文件）
/doc-summary /path/to/docs

# 指定自定義標籤
/doc-summary https://example.com/article AI,ML,Tutorial
/doc-summary ~/projects/README.md Architecture,Design
```

### 執行流程

#### 1. 讀取完整內容

**內容類型檢測：**
- **文字文章（URL）** → 使用 WebFetch 獲取內容
- **影片內容（URL）** → 使用 chrome-devtools MCP 導航和擷取：
  - 導航至 URL
  - 等待內容載入
  - 擷取文字內容快照
  - 必要時擷取截圖
  - 提取影片標題、描述、字幕（如有）和關鍵視覺元素
- **本地文件** → 使用 Read 工具讀取
- **目錄** → 使用 Glob 掃描文件

**參考資料提取：**
- 掃描內容中的參考連結（最多 5 個）
- 對於影片內容，包含描述中的參考連結
- 合併主要內容和參考資料作為完整分析素材

#### 2. 生成繁體中文摘要

**內容組織原則：**
- 分析文章結構並適應性調整摘要
- 優先使用簡潔的重點條列式風格
- 根據內容邏輯組織段落（例如：背景、方法、發現、影響）
- 提取 5-10 個核心概念或論點
- 每個重點 1-2 句話，清晰且可行

**可選視覺輔助：**
- 當有助於理解時使用 Mermaid 圖表
- 根據內容選擇圖表類型：流程圖、心智圖、時間軸、網路圖、序列圖、類別圖等
- 僅在顯著增加理解價值時才包含

**圖片處理：**
- **必須擷取原文圖片**：主動擷取文章中的重要圖片
- **圖片來源**：
  - 文章圖片：直接使用原文的圖片 URL
  - 影片內容：使用截圖功能擷取關鍵畫面
- **圖片用途**：
  - 增強核心概念的理解
  - 視覺化呈現重要數據或流程圖
  - 保留原文的圖表、示意圖等視覺元素

#### 3. 智能標籤管理

1. 搜尋 "AI Documents" 資料庫
2. 查詢現有標籤
3. 根據內容自動生成 3-5 個候選標籤
4. 優先匹配現有標籤（不區分大小寫）
5. 如果提供了 `[tags]` 參數，直接使用；否則自動選擇 2-4 個最相關的標籤
6. 必要時創建新標籤（總數最多 5 個）

#### 4. 上傳至 Notion

1. **創建頁面**：
   - 父頁面：AI Documents database_id
   - 屬性：
     - Title（繁體中文）
     - Tags（multi_select）
     - URL：添加來源 URL（網頁文章必須，本地文件如有原始 URL 也添加）

2. **添加內容區塊**：
   - 根據內容結構自適應組織段落
   - 使用 heading_2 作為段落標題
   - 使用 bulleted_list_item 列出重點
   - 使用程式碼區塊（mermaid）顯示圖表
   - 使用段落區塊提供上下文說明
   - **包含原文圖片**（使用外部 URL 或截圖的圖片區塊）
   - 對於影片內容，在相關段落包含擷取的截圖

#### 5. 顯示結果

- Notion 頁面連結
- 應用的標籤（標示現有/新創建）
- 摘要預覽

## 支援的 MCP Servers

此 plugin 使用以下 MCP servers：

- **notionApi**：Notion API 整合
  - Package: `@notionhq/notion-mcp-server`
  - 環境變數：`NOTION_TOKEN`

## 範例

### 範例 1：分析技術文章

```bash
/doc-summary https://example.com/react-best-practices
```

**輸出：**
- 繁體中文摘要包含：背景、最佳實踐、程式碼範例說明
- 自動標籤：React, JavaScript, Frontend, Best-Practices
- 原文重要圖片和程式碼截圖保留

### 範例 2：分析 YouTube 教學影片

```bash
/doc-summary https://www.youtube.com/watch?v=xxxxx AI,Tutorial
```

**輸出：**
- 摘要包含影片主要內容、關鍵時間點、重要概念
- 關鍵畫面截圖
- 自定義標籤：AI, Tutorial
- 影片描述中的參考連結也會被分析

### 範例 3：分析本地專案文檔

```bash
/doc-summary ~/projects/my-app/docs Architecture
```

**輸出：**
- 整合目錄中所有文檔的摘要
- 架構圖（如有，使用 Mermaid）
- 自定義標籤：Architecture

## 疑難排解

### Notion API 連接失敗

**問題：**無法連接到 Notion API

**解決方案：**
- 確認 `NOTION_TOKEN` 環境變數已正確設定
- 檢查 Notion integration 是否有資料庫存取權限
- 確認資料庫已分享給 integration：
  1. 打開 Notion 資料庫
  2. 點擊右上角的 "..." → "Connections"
  3. 添加你的 integration

### 找不到 AI Documents 資料庫

**問題：**Plugin 無法找到 "AI Documents" 資料庫

**解決方案：**
- 確認資料庫名稱完全匹配（區分大小寫）
- 資料庫必須分享給 integration
- 資料庫必須包含必要的屬性：Title, Tags, URL

### MCP Server 啟動失敗

**問題：**notionApi MCP server 無法啟動

**解決方案：**
- 確認已安裝 Node.js（需要 npx 命令）
- 嘗試手動執行測試：
  ```bash
  npx -y @notionhq/notion-mcp-server
  ```
- 檢查網路連接
- 確認 package 名稱正確

### 圖片無法顯示

**問題：**Notion 頁面中的圖片無法顯示

**解決方案：**
- 確認圖片 URL 是公開可存取的
- 某些網站的圖片可能有防盜連保護
- 對於受保護的內容，plugin 會嘗試使用截圖功能

### 標籤未正確應用

**問題：**標籤沒有正確添加到 Notion 頁面

**解決方案：**
- 確認 Tags 欄位類型為 multi_select
- 檢查標籤名稱是否包含特殊字符
- Notion 標籤名稱有長度限制（通常 100 字符）

## 進階使用

### 批次處理文檔

```bash
# 分析整個文檔目錄
for file in ~/docs/*.md; do
  /doc-summary "$file" Documentation
done
```

### 自定義工作流程

在專案的 `.claude/settings.json` 中配置自動啟用：

```json
{
  "plugins": {
    "doc-summary-notion@kevin-claude-marketplace": true
  },
  "env": {
    "NOTION_TOKEN": "your_token_here"
  }
}
```

## 授權

MIT License

## 作者

Kevin Hu - [GitHub](https://github.com/hongwei0417)

## 貢獻

歡迎提交 Issues 和 Pull Requests！

如有任何問題，請在 [GitHub Issues](https://github.com/hongwei0417/kevin-marketplace/issues) 提出。

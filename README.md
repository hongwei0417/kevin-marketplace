# Doc Summary Notion Plugin for Claude Code

這是一個 Claude Code plugin，提供文檔分析與 Notion 整合功能。可以分析網頁、本地文件或目錄，生成繁體中文摘要並自動上傳至 Notion AI Documents 資料庫。

## 功能特色

- 📄 **多來源支援**：支援 URL、本地文件、目錄分析
- 🇹🇼 **繁體中文摘要**：自動生成結構化的繁體中文內容摘要
- 🏷️ **智能標籤管理**：自動匹配現有標籤或創建新標籤
- 📊 **視覺化支援**：可選的 Mermaid 圖表增強理解
- 🖼️ **圖片保留**：保留原文檔中的重要圖片
- 🔗 **引用連結掃描**：自動抓取並分析文章中的參考連結（最多 5 個）
- ☁️ **Notion 整合**：自動上傳至指定的 Notion 資料庫

## 目錄結構

```
claude-marketplace/
├── .claude-plugin/
│   ├── plugin.json          # Plugin 配置文件
│   └── marketplace.json     # Marketplace 配置文件
├── commands/
│   └── doc-summary.md       # /doc-summary 命令定義
└── README.md
```

## 安裝方式

### 方式一：從本地安裝（開發測試）

1. 克隆此 repository：
```bash
git clone https://github.com/yourusername/claude-marketplace.git
cd claude-marketplace
```

2. 在 Claude Code 中安裝：
```bash
/plugin install /Users/kevinhu/Desktop/coding/claude-marketplace
```

### 方式二：從 GitHub 安裝（推薦）

1. 將此 repository 推送到 GitHub

2. 添加 marketplace：
```bash
/plugin marketplace add yourusername/claude-marketplace
```

3. 瀏覽並安裝 plugin：
```bash
/plugin
```
然後從列表中選擇 "doc-summary-notion" 安裝

## 環境配置

### 1. 設定 Notion API Key

在使用前需要設定 Notion API Key 環境變數：

```bash
export NOTION_API_KEY="your_notion_integration_token"
```

或在 `~/.claude/settings.json` 中配置：

```json
{
  "env": {
    "NOTION_API_KEY": "your_notion_integration_token"
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

# 分析本地文件
/doc-summary ~/projects/README.md

# 分析目錄（會處理目錄中的文件）
/doc-summary /path/to/docs

# 指定自定義標籤
/doc-summary https://example.com/article AI,ML,Tutorial
/doc-summary ~/projects/README.md Architecture,Design
```

### 執行流程

1. **讀取內容**
   - URL → 使用 WebFetch 獲取
   - 本地文件 → 使用 Read 讀取
   - 目錄 → 使用 Glob 掃描文件

2. **掃描參考連結**
   - 自動從內容中提取連結
   - 最多獲取 5 個參考資料
   - 合併為完整分析素材

3. **生成繁體中文摘要**
   - 自適應內容結構
   - 提取 5-10 個核心概念
   - 可選的 Mermaid 圖表
   - 保留原文重要圖片

4. **智能標籤管理**
   - 搜尋現有 Notion 資料庫標籤
   - 自動生成 3-5 個候選標籤
   - 優先匹配現有標籤
   - 必要時創建新標籤

5. **上傳至 Notion**
   - 創建新頁面
   - 設定標題、標籤、來源 URL
   - 添加結構化內容區塊

## Plugin 配置說明

### plugin.json 結構

```json
{
  "name": "doc-summary-notion",
  "version": "1.0.0",
  "description": "分析文檔並生成繁體中文摘要，自動上傳至 Notion AI Documents 資料庫",
  "author": {
    "name": "Kevin Hu",
    "email": "your-email@example.com"
  },
  "commands": [
    "./commands/doc-summary.md"
  ],
  "mcpServers": {
    "notionApi": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-notion"],
      "env": {
        "NOTION_API_KEY": "${NOTION_API_KEY}"
      }
    }
  }
}
```

### marketplace.json 結構

```json
{
  "name": "kevin-claude-marketplace",
  "description": "Kevin 的 Claude Code Plugin Marketplace",
  "owner": {
    "name": "Kevin Hu"
  },
  "plugins": [
    {
      "name": "doc-summary-notion",
      "source": ".",
      "description": "分析文檔並生成繁體中文摘要",
      "category": "productivity",
      "tags": ["notion", "documentation", "chinese", "ai"]
    }
  ]
}
```

## 開發指南

### 新增其他 Plugins

1. 在 `.claude-plugin/marketplace.json` 的 `plugins` 陣列中新增：

```json
{
  "name": "your-plugin-name",
  "source": "./path/to/plugin",
  "description": "Plugin 描述",
  "version": "1.0.0"
}
```

2. 若 plugin 在子目錄，需包含獨立的 `.claude-plugin/plugin.json`

3. 更新 marketplace：
```bash
/plugin marketplace update kevin-claude-marketplace
```

### 修改現有命令

1. 編輯 `commands/doc-summary.md`
2. 更新 `plugin.json` 中的版本號
3. 重新載入 plugin：
```bash
/plugin reload doc-summary-notion
```

### 添加新的 MCP Servers

在 `plugin.json` 的 `mcpServers` 中添加：

```json
{
  "mcpServers": {
    "your-server-name": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-name"],
      "env": {
        "API_KEY": "${YOUR_API_KEY}"
      }
    }
  }
}
```

## 發布到 GitHub

1. **初始化 Git 並推送**：

```bash
git init
git add .
git commit -m "Initial commit: Doc Summary Notion plugin"
git branch -M main
git remote add origin https://github.com/yourusername/claude-marketplace.git
git push -u origin main
```

2. **在 GitHub 上創建 Release**（可選但推薦）：

- 訪問 repository 的 Releases 頁面
- 點擊 "Create a new release"
- 設定版本標籤（如 `v1.0.0`）
- 填寫發布說明

3. **分享給他人**：

其他用戶可以通過以下方式安裝：

```bash
/plugin marketplace add yourusername/claude-marketplace
```

## 團隊使用

### 自動安裝 Marketplace

在專案的 `.claude/settings.json` 中配置：

```json
{
  "extraKnownMarketplaces": [
    "yourusername/claude-marketplace"
  ]
}
```

團隊成員打開專案時會自動載入此 marketplace。

### 啟用特定 Plugins

在 `settings.json` 中控制啟用狀態：

```json
{
  "plugins": {
    "doc-summary-notion@kevin-claude-marketplace": true
  }
}
```

## 疑難排解

### Notion API 連接失敗

- 確認 `NOTION_API_KEY` 環境變數已設定
- 檢查 Notion integration 是否有資料庫存取權限
- 確認資料庫已分享給 integration

### 命令無法執行

- 執行 `/plugin` 確認 plugin 已安裝並啟用
- 檢查 `commands/doc-summary.md` 的 frontmatter 配置
- 查看 Claude Code 錯誤訊息

### MCP Server 啟動失敗

- 確認 Node.js 已安裝（需要 npx 命令）
- 執行 `npx -y @modelcontextprotocol/server-notion` 測試安裝
- 檢查網路連接

## 相關資源

- [Claude Code Plugins 官方文檔](https://docs.claude.com/en/docs/claude-code/plugins)
- [Plugin Marketplaces 指南](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
- [Notion API 文檔](https://developers.notion.com/)
- [Model Context Protocol](https://modelcontextprotocol.io/)

## 授權

MIT License

## 作者

Kevin Hu - [GitHub](https://github.com/yourusername)

## 貢獻

歡迎提交 Issues 和 Pull Requests！

---

如有問題或建議，請在 [GitHub Issues](https://github.com/yourusername/claude-marketplace/issues) 中提出。

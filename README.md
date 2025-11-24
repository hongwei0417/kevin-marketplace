# Kevin's Claude Code Plugin Marketplace

這是一個 Claude Code Plugin Marketplace，包含多個實用的 plugins，旨在提升開發效率和自動化工作流程。

## 目錄結構

```
kevin-marketplace/
├── .claude-plugin/
│   └── marketplace.json      # Marketplace 配置文件
├── plugins/
│   ├── doc-summary-notion/   # 文檔摘要 & Notion 整合 plugin
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── commands/
│   │       └── doc-summary.md
│   └── _template/            # 新 plugin 範例模板
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── commands/
│       │   └── your-command.md
│       └── README.md
├── LICENSE
└── README.md
```

## 可用的 Plugins

### 1. Doc Summary Notion

分析文檔並生成繁體中文摘要，自動上傳至 Notion AI Documents 資料庫。

**功能特色：**
- 📄 支援 URL、本地文件、目錄分析
- 🇹🇼 自動生成繁體中文摘要
- 🏷️ 智能標籤管理
- 📊 可選的 Mermaid 圖表
- 🖼️ 保留原文檔重要圖片
- 🔗 自動掃描參考連結
- ☁️ 自動上傳至 Notion

**使用方式：**
```bash
# 分析網頁文章
/doc-summary https://example.com/article

# 分析本地文件
/doc-summary ~/projects/README.md

# 指定自定義標籤
/doc-summary https://example.com/article AI,ML,Tutorial
```

詳細說明請參考：[plugins/doc-summary-notion/README.md](plugins/doc-summary-notion/)

---

### 2. RFC Manager

管理 Moxa 前端團隊的 RFC（Request for Comments）完整流程，包含提案創建、討論管理、投票決策、文檔歸檔和實施追蹤。

**功能特色：**
- 📝 RFC 提案管理 - 使用標準化模板創建 GitLab Issue
- 💬 討論追蹤 - 監控和管理 RFC 討論流程
- 🗳️ 投票決策 - 管理 emoji 投票和結果統計
- 📦 文檔歸檔 - 自動化歸檔已接受/拒絕的 RFC
- 📊 實施追蹤 - 連結 Jira Epic 追蹤實施進度
- 🏷️ 標籤管理 - 智能化的標籤分類和應用
- 🔄 完整工作流 - 端到端的 RFC 生命週期管理

**使用方式：**

這是一個 **skill**，會在你需要管理 RFC 時自動調用：

```bash
# 自然語言使用，無需特定命令
你：幫我創建一個新的 RFC 提案
你：查看 RFC #123 的討論狀態
你：RFC #123 已經通過了，幫我歸檔
```

**前置需求：**
- GitLab Personal Access Token
- GitLab API URL 配置
- Moxa GitLab 專案 `moxa/sw/f2e/one/one-rfcs` 的存取權限

詳細說明請參考：[plugins/rfc-manager/README.md](plugins/rfc-manager/)

---

## 安裝方式

### 方式一：從 GitHub 安裝（推薦）

1. **添加 marketplace：**
```bash
/plugin marketplace add hongwei0417/kevin-marketplace
```

2. **瀏覽並安裝 plugin：**
```bash
/plugin
```
然後從列表中選擇想要的 plugin 安裝。

3. **或直接安裝特定 plugin：**
```bash
/plugin install doc-summary-notion@kevin-claude-marketplace
```

### 方式二：從本地安裝（開發測試）

1. **克隆此 repository：**
```bash
git clone https://github.com/hongwei0417/kevin-marketplace.git
cd kevin-marketplace
```

2. **在 Claude Code 中安裝：**
```bash
/plugin marketplace add /path/to/kevin-marketplace
```

---

## 為團隊配置

### 自動載入 Marketplace

在專案的 `.claude/settings.json` 中配置：

```json
{
  "extraKnownMarketplaces": [
    "hongwei0417/kevin-marketplace"
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

---

## 開發指南

### 添加新的 Plugin

#### 1. 使用模板創建新 plugin

```bash
# 複製模板目錄
cp -r plugins/_template plugins/your-plugin-name

# 編輯 plugin.json
nano plugins/your-plugin-name/.claude-plugin/plugin.json

# 編輯命令文件
nano plugins/your-plugin-name/commands/your-command.md
```

#### 2. 更新 marketplace.json

在 `.claude-plugin/marketplace.json` 的 `plugins` 陣列中添加：

```json
{
  "name": "your-plugin-name",
  "source": "./plugins/your-plugin-name",
  "description": "Plugin 的簡短描述",
  "version": "1.0.0",
  "author": {
    "name": "Your Name"
  },
  "keywords": ["keyword1", "keyword2"],
  "category": "productivity",
  "tags": ["tag1", "tag2"]
}
```

#### 3. 創建 plugin README

在 `plugins/your-plugin-name/README.md` 中撰寫詳細文檔：
- 功能說明
- 安裝步驟
- 使用範例
- 環境配置需求

#### 4. 測試你的 plugin

```bash
# 從本地安裝測試
/plugin marketplace add /path/to/kevin-marketplace

# 測試命令
/your-command
```

### Plugin 結構規範

每個 plugin 必須包含：

```
plugins/your-plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Plugin 配置（必須）
├── commands/
│   └── your-command.md      # 命令定義（至少一個）
└── README.md                # 文檔說明（建議）
```

#### plugin.json 欄位說明

```json
{
  "name": "plugin-name",              // Plugin 唯一識別名稱
  "version": "1.0.0",                 // 版本號（語意化版本）
  "description": "簡短描述",           // 一句話說明
  "author": {                         // 作者資訊
    "name": "Your Name",
    "email": "your@email.com"
  },
  "homepage": "https://...",          // 專案首頁
  "repository": "https://...",        // Git repository
  "license": "MIT",                   // 授權方式
  "keywords": ["keyword"],            // 關鍵字
  "commands": ["./commands/cmd.md"], // 命令文件路徑
  "mcpServers": {}                   // MCP 伺服器配置（選填）
}
```

#### 命令文件 (commands/*.md) 規範

使用 frontmatter 定義命令：

```markdown
---
description: Command description
argument-hint: <required-arg> [optional-arg]
allowed-tools:
  - ToolName1
  - ToolName2
---

# Command Title

Command implementation details...
```

### 添加 MCP Servers

如果你的 plugin 需要使用 MCP servers，在 `plugin.json` 中配置：

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@org/mcp-server-package"],
      "env": {
        "API_KEY": "${YOUR_API_KEY}"
      }
    }
  }
}
```

用戶需要在環境中設定對應的環境變數。

---

## 發布流程

### 1. 更新版本號

- 更新 `plugin.json` 中的 `version`
- 更新 `marketplace.json` 中對應 plugin 的 `version`
- 在 plugin README 中記錄變更

### 2. 提交變更

```bash
git add .
git commit -m "feat: add new plugin or update existing plugin"
git push origin main
```

### 3. 創建 Release（建議）

在 GitHub 上創建 release：
- 使用語意化版本標籤（如 `v1.2.0`）
- 在 release notes 中說明變更內容
- 這有助於用戶追蹤 marketplace 的更新

### 4. 通知用戶更新

用戶可以通過以下方式更新：

```bash
/plugin marketplace update kevin-claude-marketplace
```

---

## 貢獻指南

歡迎貢獻新的 plugins 或改進現有 plugins！

### 提交 Pull Request

1. Fork 此 repository
2. 創建 feature branch (`git checkout -b feature/amazing-plugin`)
3. 按照上述開發指南添加你的 plugin
4. 確保包含完整的 README 和範例
5. 提交 PR，說明 plugin 的功能和用途

### 程式碼規範

- 使用清晰的命令和變數命名
- 為複雜邏輯添加註解
- 提供完整的使用範例
- 確保命令的 `allowed-tools` 列表準確

---

## 常見問題

### Q: 如何更新已安裝的 plugin？

```bash
/plugin marketplace update kevin-claude-marketplace
```

### Q: 如何卸載 plugin？

```bash
/plugin uninstall plugin-name
```

### Q: Plugin 安裝後無法使用？

1. 檢查 plugin 是否已啟用：`/plugin`
2. 確認環境變數已正確設定
3. 查看 Claude Code 的錯誤訊息
4. 參考 plugin 的 README 確認配置需求

### Q: 如何禁用某個 plugin？

在 `.claude/settings.json` 中：

```json
{
  "plugins": {
    "plugin-name@kevin-claude-marketplace": false
  }
}
```

### Q: MCP Server 無法啟動？

- 確認已安裝 Node.js
- 檢查網路連接
- 驗證環境變數是否正確設定
- 嘗試手動執行 MCP server 命令測試

---

## 相關資源

- [Claude Code 官方文檔](https://docs.claude.com/en/docs/claude-code)
- [Plugin 開發指南](https://docs.claude.com/en/docs/claude-code/plugins)
- [Plugin Marketplaces 指南](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
- [Model Context Protocol](https://modelcontextprotocol.io/)

---

## 授權

MIT License

## 作者

Kevin Hu - [GitHub](https://github.com/hongwei0417)

---

## Changelog

### v1.0.0 (2025-01-XX)

- 初始版本發布
- 新增 Doc Summary Notion plugin
- 建立 marketplace 結構
- 提供 plugin 開發模板

---

如有問題或建議，請在 [GitHub Issues](https://github.com/hongwei0417/kevin-marketplace/issues) 中提出。

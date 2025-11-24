# RFC Manager Plugin

管理 Moxa 前端團隊的 RFC（Request for Comments）完整流程，包含提案創建、討論管理、投票決策、文檔歸檔和實施追蹤。

## 功能特色

- 📝 **RFC 提案管理** - 使用標準化模板創建 GitLab Issue
- 💬 **討論追蹤** - 監控和管理 RFC 討論流程
- 🗳️ **投票決策** - 管理 emoji 投票和結果統計
- 📦 **文檔歸檔** - 自動化歸檔已接受/拒絕的 RFC
- 📊 **實施追蹤** - 連結 Jira Epic 追蹤實施進度
- 🏷️ **標籤管理** - 智能化的標籤分類和應用
- 🔄 **完整工作流** - 端到端的 RFC 生命週期管理

## 前置需求

### 1. GitLab 存取權限

需要在 Moxa GitLab 專案 `moxa/sw/f2e/one/one-rfcs` 擁有適當的權限：
- 創建 Issue 的權限
- 創建 Branch 和 Merge Request 的權限
- 更新 Issue 標籤和狀態的權限

### 2. 環境變數設定

在你的環境中設定以下變數：

```bash
# GitLab Personal Access Token
export GITLAB_PERSONAL_ACCESS_TOKEN="your-gitlab-token"

# GitLab API URL (通常是你的 GitLab 實例)
export GITLAB_API_URL="https://gitlab.example.com/api/v4"
```

#### 獲取 GitLab Personal Access Token

1. 登入你的 GitLab 帳號
2. 前往 **Settings** → **Access Tokens**
3. 創建新的 token，需要以下權限：
   - `api` - 完整的 API 存取權限
   - `read_api` - 讀取 API
   - `write_repository` - 寫入 repository
4. 複製生成的 token 並設定為環境變數

## 安裝方式

### 從 Marketplace 安裝

```bash
# 添加 marketplace
/plugin marketplace add hongwei0417/kevin-marketplace

# 安裝 rfc-manager plugin
/plugin install rfc-manager@kevin-claude-marketplace
```

### 從本地安裝（開發測試）

```bash
# 克隆 repository
git clone https://github.com/hongwei0417/kevin-marketplace.git

# 添加本地 marketplace
/plugin marketplace add /path/to/kevin-marketplace

# 安裝 plugin
/plugin install rfc-manager@kevin-claude-marketplace
```

## 使用方式

### 基本命令

```bash
# 啟動 RFC Manager
/rfc-manager
```

執行命令後，你可以進行以下操作：

### 1. 創建新的 RFC 提案

```bash
/rfc-manager create proposal
```

這會引導你完成以下步驟：
1. 使用 RFC 提案模板填寫內容
2. 在 GitLab 創建 Issue
3. 設定適當的標籤和參與者
4. 設定討論時間軸（開始日期和截止日期）

**範例流程：**
```
User: /rfc-manager
Assistant: 我會幫你管理 RFC 流程。你想要：
1. 創建新的 RFC 提案
2. 管理現有 RFC 討論
3. 進行 RFC 投票
4. 歸檔 RFC 決議
5. 追蹤 RFC 實施

User: 創建新的 RFC 提案
Assistant: [使用模板引導創建流程，並在 GitLab 創建 Issue]
```

### 2. 管理 RFC 討論

```bash
/rfc-manager manage discussion <issue-id>
```

功能包括：
- 檢視所有討論串和評論
- 總結關鍵討論點
- 識別共識和分歧
- 協助回應問題和意見

### 3. 進行投票和決策

```bash
/rfc-manager conduct voting <issue-id>
```

這會：
1. 確認討論期已結束
2. 啟動 48 小時投票期
3. 統計 emoji 投票（👍 贊成、👎 反對、👀 棄權）
4. 計算結果並記錄決議

### 4. 歸檔 RFC 決議

```bash
/rfc-manager archive <issue-id>
```

自動化歸檔流程：
1. 創建歸檔分支
2. 使用歸檔模板生成文檔
3. 將文檔放置到正確位置（`archived/accepted/` 或 `archived/rejected/`）
4. 創建 Merge Request
5. 更新 Issue 狀態和標籤
6. 關閉 Issue

### 5. 追蹤實施進度

```bash
/rfc-manager track implementation <issue-id>
```

用於已接受的 RFC：
- 創建或連結 Jira Epic
- 追蹤實施里程碑
- 報告阻塞問題
- 建議必要的修改

## RFC 生命週期

完整的 RFC 流程包含五個階段：

```
1. 提案 (Proposal)
   ↓
2. 討論 (Discussion) - 2-3 週
   ↓
3. 投票 (Voting) - 48 小時
   ↓
4. 歸檔 (Archive)
   ↓
5. 實施 (Implementation) - 僅限已接受的 RFC
```

## 文件結構

Plugin 包含以下資源：

```
plugins/rfc-manager/
├── .claude-plugin/
│   └── plugin.json           # Plugin 配置
├── commands/
│   └── rfc-manager.md        # 主要命令定義
├── assets/
│   ├── rfc-proposal-template.md    # RFC 提案模板
│   └── rfc-archive-template.md     # RFC 歸檔模板
├── references/
│   ├── rfc-workflow.md             # 完整工作流程文檔
│   └── labels-guide.md             # 標籤使用指南
└── README.md
```

### 模板文件

#### RFC 提案模板 (`assets/rfc-proposal-template.md`)

包含以下必填和選填部分：
- 🟢 RFC 基本資訊（提案人、參與者、日期）
- 🟢 問題描述和動機
- 🟢 解決方案說明
- 🟢 影響評估
- 🟢 風險評估
- 🔵 替代方案（選填）
- 🔵 實施計劃（選填）
- 🔵 參考資料（選填）

#### RFC 歸檔模板 (`assets/rfc-archive-template.md`)

用於記錄最終決議：
- RFC 編號和標題
- 最終狀態（接受/拒絕）
- 決議日期和參與者
- 投票結果
- 決議理由
- 實施計劃（如適用）

### 參考文檔

#### RFC 工作流程 (`references/rfc-workflow.md`)

詳細說明：
- 每個階段的操作步驟
- GitLab 操作指南
- 時程規劃建議
- 最佳實踐

#### 標籤使用指南 (`references/labels-guide.md`)

說明如何使用：
- 流程標籤（`process`）
- 產品標籤（`products`）
- 工作組標籤（`wg/dev-rel`, `wg/ever-green`, 等）
- 狀態標籤（`status/accepted`, `status/rejected`）

## 標籤分類

### 類別標籤
- `process` - 流程相關的 RFC
- `products` - 產品相關的 RFC

### 工作組標籤
- `wg/dev-rel` - Developer Relations
- `wg/ever-green` - Ever Green
- `wg/infra` - Infrastructure
- `wg/ui-library` - UI Library
- `wg/vizion` - Vizion

### 狀態標籤
- `status/accepted` - 已接受
- `status/rejected` - 已拒絕

## 重要時程

- **討論期**：2-3 週（最長 1 個月）
- **投票期**：討論結束後 48 小時
- **歸檔**：必須在關閉 Issue 前完成 MR 合併

## 投票規則

- **投票方式**：在 GitLab Issue 上使用 emoji 反應
  - 👍 = 贊成
  - 👎 = 反對
  - 👀 = 棄權
- **合格投票者**：RFC 中提及的關鍵參與者
- **通過條件**：超過 50% 贊成票

## 常見問題

### Q: 如何修改進行中的 RFC？

A: 在討論階段，直接更新 GitLab Issue 的描述即可。重大修改建議在評論中說明變更理由。

### Q: RFC 被拒絕後還能重新提案嗎？

A: 可以。如果情況改變或有新的資訊，可以創建新的 RFC Issue 並參考先前被拒絕的 RFC。

### Q: 實施過程中發現需要修改 RFC 怎麼辦？

A: 不要重新打開已關閉的 RFC Issue。如果需要重大修改，創建新的 RFC Issue 並參考原始 RFC。

### Q: 如何查詢已歸檔的 RFC？

A: 在 `moxa/sw/f2e/one/one-rfcs` repository 的 `archived/` 目錄中：
- `archived/accepted/` - 已接受的 RFC
- `archived/rejected/` - 已拒絕的 RFC

### Q: GitLab MCP Server 無法連接？

A: 檢查以下事項：
1. 確認 `GITLAB_PERSONAL_ACCESS_TOKEN` 環境變數已設定
2. 確認 token 具有正確的權限
3. 確認 `GITLAB_API_URL` 指向正確的 GitLab 實例
4. 確認網路連接正常

## 疑難排解

### 無法創建 Issue

**問題**: 創建 Issue 時出現權限錯誤

**解決方案**:
1. 確認你在 `moxa/sw/f2e/one/one-rfcs` 專案中有 Developer 或更高權限
2. 檢查 GitLab token 是否包含 `api` 權限
3. 確認 token 尚未過期

### MCP Server 啟動失敗

**問題**: GitLab MCP Server 無法啟動

**解決方案**:
1. 確認已安裝 Node.js（建議 v16 以上）
2. 檢查網路連接
3. 嘗試手動執行：`npx -y @modelcontextprotocol/server-gitlab`
4. 查看 Claude Code 的錯誤訊息

### 無法更新標籤

**問題**: 更新 Issue 標籤時失敗

**解決方案**:
1. 確認標籤名稱正確（參考 `references/labels-guide.md`）
2. 確認你有更新 Issue 的權限
3. 檢查 GitLab 專案中是否存在該標籤

## 更新日誌

### v1.0.0 (2025-01-XX)

- 初始版本發布
- 支援完整的 RFC 生命週期管理
- 整合 GitLab MCP Server
- 包含標準化模板和參考文檔

## 授權

MIT License

## 作者

Kevin Hu - [GitHub](https://github.com/hongwei0417)

## 相關連結

- [Claude Code 官方文檔](https://docs.claude.com/en/docs/claude-code)
- [Plugin 開發指南](https://docs.claude.com/en/docs/claude-code/plugins)
- [GitLab MCP Server](https://github.com/modelcontextprotocol/servers/tree/main/src/gitlab)
- [Model Context Protocol](https://modelcontextprotocol.io/)

## 貢獻

歡迎提出 Issue 或 Pull Request 來改進這個 plugin！

---

如有問題或建議，請在 [GitHub Issues](https://github.com/hongwei0417/kevin-marketplace/issues) 中提出。
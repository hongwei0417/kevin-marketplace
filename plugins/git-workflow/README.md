# Git Workflow Plugin

完整的 Git 工作流程命令集，提供分支管理、提交、程式碼審查和 PR 工作流程的自動化功能。

## 功能特色

- **智慧分支管理**：自動命名、建立 worktree、清理合併的分支
- **規範化提交**：使用 Conventional Commit 格式和 emoji
- **全面程式碼審查**：多角度審查（產品、開發、QA、安全、DevOps、UI/UX）
- **PR 工作流程**：自動建立分支、提交和 Pull Request

## 安裝

### 透過 Marketplace 安裝

```bash
/plugin marketplace add hongwei0417/kevin-marketplace
/plugin install git-workflow@kevin-claude-marketplace
```

## 可用命令

### `/branch-cleanup`

清理已合併的分支和過期的遠端參照。

```bash
# 互動式清理
/branch-cleanup

# 預覽模式（不實際刪除）
/branch-cleanup --dry-run

# 強制清理
/branch-cleanup --force

# 只清理遠端分支
/branch-cleanup --remote-only

# 只清理本地分支
/branch-cleanup --local-only
```

**功能**：
- 識別已合併的分支
- 檢測過期的遠端追蹤分支
- 保護主要分支（main, master, develop 等）
- 提供復原指令

---

### `/code-review`

進行全面的程式碼品質審查。

```bash
# 審查特定檔案
/code-review src/components/Header.tsx

# 審查特定 commit
/code-review abc1234

# 完整專案審查
/code-review --full
```

**審查項目**：
- 程式碼品質和可維護性
- 安全性漏洞
- 效能分析
- 架構和設計
- 測試覆蓋率
- 文件完整性

---

### `/commit`

建立格式化的 commit，使用 Conventional Commit 格式和 emoji。

```bash
# 自動分析變更並建立 commit
/commit

# 指定 commit 訊息
/commit "add user authentication"

# 跳過 pre-commit 檢查
/commit --no-verify

# 修改最後一個 commit
/commit --amend
```

**功能**：
- 自動執行 lint、build、docs 檢查
- 分析變更並建議拆分 commit
- 使用 emoji + conventional commit 格式
- 支援多種 commit 類型（feat, fix, docs, style, refactor, perf, test, chore）

---

### `/create-branch`

根據描述建立新分支，可選擇設定 worktree。

```bash
# 從描述建立分支
/create-branch "add dark mode toggle"

# 從程式碼變更自動產生分支名稱
/create-branch
```

**功能**：
- 智慧分支命名（feature/, fix/, refactor/, docs/ 等）
- 支援建立 worktree 進行平行開發
- 驗證分支名稱和衝突檢測
- 自動分析程式碼變更產生描述

---

### `/create-pr`

建立新分支、提交變更並建立 Pull Request。

```bash
# 自動建立 PR
/create-pr

# 指定標題
/create-pr "Add user authentication feature"

# 建立 draft PR
/create-pr --draft
```

**功能**：
- 自動將變更拆分成多個邏輯 commit
- 產生 PR 摘要和測試計劃
- 支援 draft PR
- 使用 GitHub CLI 建立 PR

---

### `/pr-review`

進行多角度的 PR 審查。

```bash
# 審查指定 PR
/pr-review 123

# 審查 PR 連結
/pr-review https://github.com/user/repo/pull/123
```

**審查角度**：
1. **產品經理**：商業價值、用戶體驗、策略對齊
2. **開發者**：程式碼品質、效能、最佳實踐
3. **QA 工程師**：測試覆蓋、邊界情況、回歸風險
4. **安全工程師**：漏洞檢測、資料處理、合規性
5. **DevOps**：CI/CD、基礎設施、監控
6. **UI/UX 設計師**：視覺一致性、可用性、無障礙

## 工作流程範例

### 完整開發流程

```bash
# 1. 建立新功能分支
/create-branch "add user profile page"

# 2. 開發完成後，建立結構化 commit
/commit

# 3. 自我程式碼審查
/code-review

# 4. 建立 PR
/create-pr

# 5. 請求團隊審查
/pr-review 123
```

### 分支維護流程

```bash
# 定期清理合併的分支
/branch-cleanup --dry-run  # 先預覽
/branch-cleanup            # 確認後執行
```

## 相依套件

此 Plugin 使用以下工具：
- Git（必需）
- GitHub CLI (`gh`)（用於 PR 相關功能）

## 授權

MIT License

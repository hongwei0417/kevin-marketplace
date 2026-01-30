# Git Workflow Plugin v2.0

Integrated Git workflow plugin providing create-branch → commit → create-pr flow with fork cross-project MR support.

## 功能特色

- **一次性設定**：開始前收集所有設定，然後一次執行完畢
- **不中斷執行**：Git 操作（create-branch → commit → create-pr）連續執行
- **彈性目標分支**：Create PR 支援選擇任意目標分支
- **Fork 支援**：自動偵測 fork 場景，支援跨專案 MR
- **GitLab 整合**：透過 GitLab MCP 和直接 API 操作

## 安裝

### 透過 Marketplace 安裝

```bash
/plugin marketplace add hongwei0417/kevin-marketplace
/plugin install git-workflow@kevin-claude-marketplace
```

## 使用方式

### 主命令：`/run`

```bash
# 互動式選擇模式
/run

# 全流程模式
/run --full

# 單一任務模式
/run --step create-branch
/run --step commit
/run --step create-pr
```

## 全流程模式

流程順序：**Create Branch → Commit → Create PR**

### 執行流程

```
┌─────────────────────────────────────┐
│          第一階段：收集設定          │
│                                     │
│  選擇要執行的步驟：                  │
│  ☑ Create Branch                    │
│  ☑ Commit                           │
│  ☑ Create PR → Target: develop      │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│     第二階段：依序執行（不中斷）      │
│                                     │
│  1. Create Branch ─────────────────→│
│  2. Commit ────────────────────────→│
│  3. Create PR                       │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│          第三階段：報告結果          │
└─────────────────────────────────────┘
```

## 各步驟說明

### Create Branch

建立新分支：

- 根據描述或變更自動命名
- 支援 worktree 設定

### Commit

提交變更（內建 `git-workflow:commit` skill）：

- 審查並暫存變更
- 自動判斷是否切分成多個 commit
- Conventional Commit 格式
- 確保不含敏感資訊

### Create PR

建立 GitLab Merge Request：

- 支援選擇任意目標分支
- 自動偵測 fork 場景
- 同專案 MR：使用 GitLab MCP
- 跨專案 MR：使用 GitLab API

**目標分支選項：**
```
1. main
2. develop
3. <當前分支的來源分支>
4. 自訂輸入
```

## GitLab Token 設定

跨專案 MR 需要 GitLab Personal Access Token：

### 方式 1：環境變數

```bash
export GITLAB_PERSONAL_ACCESS_TOKEN=glpat-xxxx
```

### 方式 2：Claude Settings

在 `~/.claude/settings.json` 中設定：

```json
{
  "env": {
    "GITLAB_PERSONAL_ACCESS_TOKEN": "glpat-xxxx"
  }
}
```

## 工作流程範例

### 完整開發流程

```bash
/run --full

# 第一階段：收集設定
# [多選] 要執行的步驟：Branch, Commit, PR
# [詢問] 分支描述：add user profile
# [詢問] MR 目標分支：develop

# 第二階段：依序執行（不中斷）
# → Create Branch: feature/add-user-profile ✓
# → Commit: feat(user): add profile page ✓
# → Create PR: !123 ✓

# 第三階段：報告
# Branch: feature/add-user-profile
# Commit: feat(user): add profile page
# MR: !123 https://gitlab.com/...
```

### 單一任務

```bash
# 只建立分支
/run --step create-branch

# 只提交
/run --step commit

# 只建立 PR（指定目標分支）
/run --step create-pr
```

## 插件結構

```
git-workflow/
├── .claude-plugin/plugin.json
├── README.md
├── commands/
│   └── run.md                   # 整合性主命令
└── skills/
    ├── create-branch/
    │   └── SKILL.md             # 建立分支技能
    ├── commit/
    │   └── SKILL.md             # Commit 工作流程（內建）
    └── create-pr/
        ├── SKILL.md             # 建立 PR 技能
        └── scripts/
            └── gitlab-cross-project-mr.sh
```

## 相依套件

- Git（必需）
- GitLab MCP Server（用於同專案 MR）
- curl（用於跨專案 MR API 呼叫）

## 授權

MIT License

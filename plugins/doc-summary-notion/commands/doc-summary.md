---
description: Analyze documents and generate Traditional Chinese summaries, auto-upload to Notion AI Documents database
argument-hint: <URL | file-path | directory> [tags]
allowed-tools:
  - WebFetch
  - Read
  - Glob
  - Grep
  - mcp__notionApi__API-post-search
  - mcp__notionApi__API-post-page
  - mcp__notionApi__API-patch-block-children
  - mcp__notionApi__API-retrieve-a-database
  - mcp__chrome-devtools__navigate_page
  - mcp__chrome-devtools__take_snapshot
  - mcp__chrome-devtools__take_screenshot
  - mcp__chrome-devtools__wait_for
  - mcp__chrome-devtools__list_pages
  - mcp__chrome-devtools__select_page
---

# Document Summary & Notion Upload

Analyze source content (URL/local file/directory) and its references, generate Traditional Chinese summary and upload to Notion.

**Arguments:**
- `<source>`: URL or local file/directory path (required)
- `[tags]`: Optional comma-separated tags (e.g., "AI,ML,Tutorial")

---

## Execution Steps

### 1. Read Complete Content

**Content Type Detection:**
- **Text Articles (URL)** → Use WebFetch to fetch content
- **Video Content (URL)** → Use chrome-devtools MCP to navigate and extract content:
  - Navigate to URL (`mcp__chrome-devtools__navigate_page`)
  - Wait for content to load (`mcp__chrome-devtools__wait_for`)
  - Take snapshot to extract text content (`mcp__chrome-devtools__take_snapshot`)
  - Capture screenshots if needed (`mcp__chrome-devtools__take_screenshot`)
  - Extract video title, description, transcript (if available), and key visual elements
- **Local Files** → Use Read tool
- **Directories** → Use Glob to discover files

**Reference Extraction:**
- Scan and fetch referenced links from content (max 5)
- For video content, include references from description or related links
- Combine main content and references as complete analysis material

### 2. Generate Traditional Chinese Summary (Adaptive Structure)

**Content Organization Principles:**
- Analyze article structure and adapt summary accordingly
- Prioritize concise, bullet-point style highlights
- Organize by logical sections based on content (e.g., background, methodology, findings, implications)
- Extract 5-10 core concepts or arguments across all sections
- Each point 1-2 sentences, clear and actionable

**Optional Visual Aids (if beneficial):**
- Use Mermaid diagrams when helpful for understanding structure/relationships
- Choose diagram type based on content: flowchart, mind map, timeline, network graph, sequence diagram, class diagram, etc.
- Only include if it adds significant value to comprehension

**Image Handling (重要):**
- **必須擷取原文圖片**: 主動擷取文章中的重要圖片，包含於 Notion 摘要中
- **圖片來源**:
  - 文章圖片: 直接使用原文的圖片 URL
  - 影片內容: 使用 `mcp__chrome-devtools__take_screenshot` 擷取關鍵畫面
- **圖片用途**:
  - 增強核心概念的理解
  - 視覺化呈現重要數據或流程圖
  - 保留原文的圖表、示意圖等視覺元素
- **圖片處理**: 保留圖片的上下文說明和標題（如有）

### 3. Smart Tag Management
1. Search "AI Documents" database (`mcp__notionApi__API-post-search`)
2. Query existing tags (`mcp__notionApi__API-retrieve-a-database`)
3. Auto-generate 3-5 candidate tags based on content
4. Prioritize matching existing tags (case-insensitive)
5. If `[tags]` provided, use directly; otherwise auto-select 2-4 most relevant tags
6. Create new tags if necessary (max 5 total)

### 4. Upload to Notion
1. Create page (`mcp__notionApi__API-post-page`):
   - Parent: AI Documents database_id
   - Properties:
     - Title (Traditional Chinese)
     - Tags (multi_select)
     - URL: Add source URL (required for web articles, use original URL for local files if available)
2. Add content blocks (`mcp__notionApi__API-patch-block-children`):
   - Organize sections adaptively based on content structure
   - Use heading_2 for section titles
   - Use bulleted_list_item for key points
   - Use code blocks (mermaid) for diagrams when included
   - Use paragraph blocks for contextual explanations if needed
   - **Include images from original content** (image blocks with external URLs or screenshots)
   - For video content, include captured screenshots at relevant sections

### 5. Display Results
- Notion page link
- Applied tags (indicate existing/newly created)
- Summary preview

---

**Examples:**
```bash
# Text article
/doc-summary https://example.com/article

# Video content (YouTube, etc.)
/doc-summary https://www.youtube.com/watch?v=xxxxx AI,Tutorial

# Local file with tags
/doc-summary ~/projects/README.md AI,Tutorial

# Directory analysis
/doc-summary /path/to/docs Architecture,Design
```

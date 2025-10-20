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
---

# Document Summary & Notion Upload

Analyze source content (URL/local file/directory) and its references, generate Traditional Chinese summary and upload to Notion.

**Arguments:**
- `<source>`: URL or local file/directory path (required)
- `[tags]`: Optional comma-separated tags (e.g., "AI,ML,Tutorial")

---

## Execution Steps

### 1. Read Complete Content
- URL → WebFetch; local file → Read; directory → Glob
- Scan and fetch referenced links from content (max 5)
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

**Image Handling:**
- If original article contains images, include them in the summary
- Use image references to enhance understanding of key concepts
- Preserve image context and captions when relevant

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
   - Include images when present in original content

### 5. Display Results
- Notion page link
- Applied tags (indicate existing/newly created)
- Summary preview

---

**Examples:**
```bash
/doc-summary https://example.com/article
/doc-summary ~/projects/README.md AI,Tutorial
/doc-summary /path/to/docs Architecture,Design
```

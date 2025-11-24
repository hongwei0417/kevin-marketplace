# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Claude Code Plugin Marketplace that distributes custom plugins for Claude Code. The repository follows the Claude Code plugin marketplace specification and contains two main plugins:

1. **doc-summary-notion** - A slash command plugin for analyzing documents and uploading summaries to Notion
2. **rfc-manager** - A skill plugin for managing RFC (Request for Comments) workflows in GitLab

## Architecture

### Marketplace Structure

```
.claude-plugin/marketplace.json    # Marketplace metadata and plugin registry
plugins/                           # Individual plugin directories
  {plugin-name}/
    .claude-plugin/plugin.json     # Plugin manifest
    commands/*.md                  # Slash command definitions (for command plugins)
    skills/*.md                    # Skill definitions (for skill plugins)
    assets/                        # Templates and static resources
    references/                    # Documentation and guides
    README.md                      # Plugin documentation
```

### Key Files

- **marketplace.json**: Central registry containing all plugins with metadata (name, version, author, keywords, category, tags)
- **plugin.json**: Individual plugin configuration including commands/skills, MCP servers, and metadata
- Command/Skill definitions use frontmatter (YAML) for metadata and Markdown for content

### Plugin Types

**Slash Commands** (`commands/`):
- User-invoked with `/command-name`
- Defined in `.md` files with frontmatter specifying `description`, `argument-hint`, and `allowed-tools`
- Example: doc-summary-notion uses `/doc-summary <source> [tags]`

**Skills** (`skills/`):
- Automatically invoked by Claude Code when relevant to user intent
- More complex, autonomous workflows
- Example: rfc-manager activates when user mentions RFC management tasks

### MCP Server Integration

Both plugins use Model Context Protocol (MCP) servers configured in their `plugin.json`:

- **doc-summary-notion**: Uses `@notionhq/notion-mcp-server` for Notion API integration
- **rfc-manager**: Uses `@zereight/mcp-gitlab` for GitLab API integration

MCP servers require environment variables (e.g., `NOTION_TOKEN`, `GITLAB_PERSONAL_ACCESS_TOKEN`) that users must configure.

## Development Workflow

### Adding a New Plugin

1. **Copy the template**:
   ```bash
   cp -r plugins/_template plugins/your-plugin-name
   ```

2. **Update plugin.json**:
   - Set unique `name`, `version`, `description`
   - Add author information
   - Configure `commands` or `skills` array
   - Add `mcpServers` if needed

3. **Register in marketplace.json**:
   Add entry to the `plugins` array with matching metadata

4. **Create command/skill files**:
   - Commands: Create `.md` files in `commands/` with frontmatter
   - Skills: Create `.md` files in `skills/` with frontmatter
   - Frontmatter must specify `description` and other required fields

5. **Write plugin README**:
   Document installation, configuration, usage, and troubleshooting

### Testing Plugins

```bash
# Install marketplace locally
/plugin marketplace add /path/to/kevin-marketplace

# Test command
/your-command-name

# For skills, use natural language to trigger
```

### Publishing Updates

1. Update version in both `plugin.json` and `marketplace.json`
2. Commit changes to main branch
3. Create GitHub release with version tag (e.g., `v1.2.0`)
4. Users update with: `/plugin marketplace update kevin-claude-marketplace`

## Plugin Implementation Patterns

### doc-summary-notion Pattern

Multi-step document analysis pipeline:
1. Content detection and fetching (WebFetch for URLs, Read for files, chrome-devtools for videos)
2. Reference link extraction (max 5)
3. Summary generation in Traditional Chinese with adaptive structure
4. Image preservation from original content
5. Smart tag management (match existing or create new)
6. Notion page creation with structured blocks

### rfc-manager Pattern

Stateful workflow management across GitLab:
1. Multi-phase lifecycle (Proposal → Discussion → Voting → Archive → Implementation)
2. GitLab Issue as RFC proposal
3. Branch creation for archiving
4. Merge Request workflow for documentation
5. Label-based categorization (process, products, working groups)
6. Integration with Jira for implementation tracking

Key: Uses template files in `assets/` and reference documentation in `references/` to guide workflow execution.

## Important Constraints

### Plugin.json Structure
- `commands` OR `skills` array (not both typically)
- `mcpServers` object uses package names as keys
- Environment variables reference user-configured values: `${VAR_NAME}`

### Command/Skill Frontmatter
- Commands need: `description`, `argument-hint`, `allowed-tools`
- Skills need: `name`, `description`
- `allowed-tools` restricts which Claude Code tools the command can use

### Marketplace Distribution
- Users install via GitHub path: `hongwei0417/kevin-marketplace`
- Plugin identifiers: `{plugin-name}@{marketplace-name}`
- Marketplace name from repository structure, not marketplace.json

## Common Tasks

### View marketplace structure
```bash
cat .claude-plugin/marketplace.json | jq '.plugins'
```

### Check plugin configuration
```bash
cat plugins/{plugin-name}/.claude-plugin/plugin.json | jq
```

### Find all command definitions
```bash
find plugins -name "*.md" -path "*/commands/*"
```

### Find all skill definitions
```bash
find plugins -name "*.md" -path "*/skills/*"
```

### Validate JSON files
```bash
find . -name "*.json" -exec sh -c 'echo "Checking {}"; jq empty {}' \;
```

## Integration Points

### GitLab (rfc-manager)
- Project: `moxa/sw/f2e/one/one-rfcs`
- Uses GitLab MCP tools for Issues, MRs, branches, files
- Label-based workflow management
- Template-driven document generation

### Notion (doc-summary-notion)
- Database: "AI Documents"
- Required properties: Title (title), Tags (multi_select), URL (url)
- Block types: heading_2, bulleted_list_item, code (mermaid), image
- Smart tag matching with existing database tags

## Environment Variables

Users must configure in `~/.claude/settings.json` or system environment:

- `NOTION_TOKEN` - Notion API integration token
- `GITLAB_PERSONAL_ACCESS_TOKEN` - GitLab API token
- `GITLAB_API_URL` - GitLab instance URL

## Template Structure

The `_template` plugin provides the canonical structure for new plugins. It includes:
- Basic plugin.json with placeholder fields
- Example command file with proper frontmatter
- README template with standard sections

When creating plugins, always start from this template to ensure consistency.

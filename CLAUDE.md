# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

BRAR AI Agent is a skill-based automation system for BRAR Elettromeccanica SpA, an Italian company specializing in high-current electrical solutions for the steel industry. The system uses MCP (Model Context Protocol) to provide Claude Desktop with domain-specific skills.

## Project Structure

```
brar-ai-agent/
├── brar-skills/           # Skill definitions (markdown-based)
│   ├── follow-up-generator/SKILL.md
│   ├── hubspot-crm-manager/SKILL.md
│   ├── deal-history-tracker/SKILL.md
│   └── CLAUDE.md
└── brar-skills-mcp/       # MCP server (TypeScript/Node.js)
    ├── src/index.ts
    ├── dist/
    └── CLAUDE.md
```

## Repositories

| Component | Purpose | GitHub |
|-----------|---------|--------|
| brar-skills | Skill markdown files | https://github.com/koldozagana-bot/brar-skills |
| brar-skills-mcp | MCP server | https://github.com/koldozagana-bot/brar-skills-mcp |

Each subfolder is a separate git repository.

## Development Commands

### MCP Server (brar-skills-mcp/)
```bash
cd brar-skills-mcp
npm install              # Install dependencies
npm run build            # Build TypeScript
npm start                # Run server
timeout 3 node dist/index.js  # Test server starts
```

### Skills (brar-skills/)
No build needed. Edit SKILL.md files directly, then call `reload_skills` MCP tool.

## Architecture

### How It Works

1. **brar-skills-mcp** loads all skills from `brar-skills/` into memory at startup
2. When user sends a query, `SkillSelector` scores each skill based on metadata
3. Best matching skill is returned with confidence level
4. Claude Desktop uses the skill's instructions to complete the task

### Skill Selection Scoring

| Match Type | Points |
|------------|--------|
| Trigger match | 15 |
| Tag match | 5 |
| Category match | 5 |
| when_to_use word | 3 |
| Description word | 1 |

Confidence: High (15+), Medium (8-14), Low (1-7)

## Available Skills

| Skill | Category | Purpose |
|-------|----------|---------|
| follow-up-generator | sales | Generate professional emails/WhatsApp messages for client follow-ups |
| hubspot-crm-manager | crm | Manage HubSpot contacts, deals, companies, and tasks |
| deal-history-tracker | crm | Track deal timeline with structured history in HubSpot |

## MCP Tools (brar-skills)

| Tool | Description |
|------|-------------|
| `select_skill` | Auto-select best skill for a query |
| `get_relevant_skills` | Get ranked list of matching skills |
| `get_skill_content` | Get full SKILL.md content |
| `list_skills` | List all available skills |
| `search_skills` | Search by keyword |
| `reload_skills` | Hot-reload cache from disk |

## Desktop Commander MCP

Desktop Commander provides terminal/bash capabilities to Claude Desktop.

**Install/Update:**
```bash
npx @wonderwhy-er/desktop-commander@latest setup
```

### Desktop Commander Tools

| Tool | Description |
|------|-------------|
| `execute_command` | Run terminal/bash commands |
| `read_file` | Read any file including binary (PDFs, images) |
| `write_file` | Create/modify files |
| `search_files` | Search for files by pattern |
| `list_directory` | Browse directories |
| `get_file_info` | Get file metadata |
| `search_code` | Search code with ripgrep |
| `manage_blocked_commands` | Control which commands are allowed |

## Claude Desktop Config

Location: `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "brar-skills": {
      "command": "node",
      "args": ["C:\\Users\\koldo\\brar-ai-agent\\brar-skills-mcp\\dist\\index.js"]
    },
    "desktop-commander": {
      "command": "cmd",
      "args": ["/c", "npx", "-y", "@wonderwhy-er/desktop-commander@latest"]
    }
  }
}
```

## Adding a New Skill

1. Create folder: `brar-skills/skill-name/`
2. Create `SKILL.md` with YAML frontmatter (name, description, category, tags, triggers, when_to_use)
3. Call `reload_skills` tool to refresh cache
4. Test with `select_skill` using sample queries

## BRAR Business Context

**Company**: BRAR Elettromeccanica SpA (Italian)
**Industry**: High-current electrical solutions for steel production
**Products**: Water-cooled cables, electrode arms (EAF), transformer systems, LF components
**Markets**: Europe, Middle East, North Africa, Asia

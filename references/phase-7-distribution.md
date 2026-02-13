# Phase 7: Distribution

## Contents
- [Objective](#objective)
- [Skills](#skills)
- [Prerequisites](#prerequisites)
- [Workflow](#workflow)
- [Team Opportunity](#team-opportunity)
- [Exit Criteria](#exit-criteria)

## Objective

Create README, configure package for distribution, and optionally containerize for streamable HTTP deployment.

## Skills

| Order | Skill | Purpose |
|-------|-------|---------|
| 1 | finishing-a-development-branch | Merge, PR, or cleanup |
| 2 | docker-expert | Docker setup (only if streamable HTTP transport) |

## Prerequisites

Phase 6 completed: evaluation accuracy >= 70%.

## Workflow

### Step 1: Write README

Include these sections:

```markdown
# {service}-mcp-server / {service}_mcp

Brief description of what this MCP server does.

## Features

- List of tools/capabilities

## Installation

### Prerequisites
- Node.js 18+ (TS) or Python 3.10+ (Python)
- API key for {service}

### Install
```bash
# TypeScript
npm install -g {service}-mcp-server

# Python
pip install {service}-mcp
```

## Configuration

### Environment Variables
| Variable | Required | Description |
|----------|----------|-------------|
| {SERVICE}_API_KEY | Yes | API key for {service} |

## Tool Reference

| Tool | Description | Read-only |
|------|-------------|-----------|
| {service}_list_* | List resources | Yes |
| {service}_get_* | Get resource details | Yes |
| {service}_create_* | Create resource | No |

## Usage

### Claude Desktop
Add to `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "{service}": {
      "command": "node",
      "args": ["path/to/dist/index.js"],
      "env": { "{SERVICE}_API_KEY": "your-key" }
    }
  }
}
```

### Cursor / VS Code
Add to MCP settings:
```json
{
  "{service}": {
    "command": "node",
    "args": ["path/to/dist/index.js"],
    "env": { "{SERVICE}_API_KEY": "your-key" }
  }
}
```

## Transport

- **stdio** (default): for local integrations
- **Streamable HTTP**: for remote deployment (if supported)

## License
```

### Step 2: Package Configuration

**TypeScript:**
- Verify `bin` field in `package.json` points to `dist/index.js`
- Add shebang `#!/usr/bin/env node` to entry point
- Verify `files` array includes `dist/`
- Add `prepublishOnly` script: `npm run build`
- Test: `npm pack` then install from tarball

**Python:**
- Verify `[project.scripts]` in `pyproject.toml`
- Verify package can be installed: `pip install -e .`
- Test: `pip install .` then run the entry point command

### Step 3: Docker (optional, only for streamable HTTP)

If transport includes streamable HTTP, delegate to `docker-expert`:

```dockerfile
# TypeScript
FROM node:20-slim
WORKDIR /app
COPY package*.json ./
RUN npm ci --production
COPY dist/ ./dist/
EXPOSE 3000
CMD ["node", "dist/index.js"]

# Python
FROM python:3.12-slim
WORKDIR /app
COPY pyproject.toml .
COPY {service}_mcp/ ./{service}_mcp/
RUN pip install .
EXPOSE 3000
CMD ["{service}-mcp"]
```

### Step 4: Finish Branch

Delegate to `finishing-a-development-branch` for merge/PR.

## Team Opportunity

README + Docker in parallel:
```
Agent A: README + package configuration (Steps 1-2)
Agent B: Docker setup (Step 3, only if streamable HTTP)
Sync: final verification
```

## Exit Criteria

- [ ] README with installation, configuration, tool reference, usage examples
- [ ] Client config snippets (Claude Desktop, Cursor/VS Code)
- [ ] Package installable from local (`npm pack` or `pip install .`)
- [ ] Docker setup complete (if streamable HTTP)
- [ ] Branch merged or PR created

## Notes

- README is the primary user-facing documentation -- make it clear and complete
- Include real examples, not just placeholders
- Test the install flow from scratch to verify it works

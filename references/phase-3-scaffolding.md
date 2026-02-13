# Phase 3: Scaffolding

## Contents
- [Objective](#objective)
- [Skills](#skills)
- [Prerequisites](#prerequisites)
- [Workflow](#workflow)
- [Exit Criteria](#exit-criteria)

## Objective

Initialize the project, install dependencies, create the server entry point, set up test infrastructure, and verify the server starts.

## Skills

| Order | Skill | Purpose |
|-------|-------|---------|
| 1 | test-driven-development | Set up test infrastructure first |
| 2 | using-git-worktrees | Create isolated workspace |

## Prerequisites

Phase 2 completed: tool schemas, project structure, API client design, implementation plan.

## Workflow

### TypeScript Scaffolding

#### Step 1: Initialize Project

```bash
mkdir {service}-mcp-server && cd {service}-mcp-server
npm init -y
```

#### Step 2: Install Dependencies

```bash
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node vitest
```

Add to `package.json`:
```json
{
  "type": "module",
  "bin": { "{service}-mcp-server": "dist/index.js" },
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "test": "vitest run",
    "test:watch": "vitest"
  }
}
```

#### Step 3: Create tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "Node16",
    "moduleResolution": "Node16",
    "outDir": "dist",
    "rootDir": "src",
    "strict": true,
    "esModuleInterop": true,
    "declaration": true
  },
  "include": ["src/**/*"]
}
```

#### Step 4: Create Server Entry Point

Create `src/index.ts` with minimal `McpServer` setup:
- Import `McpServer` from `@modelcontextprotocol/sdk/server/mcp.js`
- Import `StdioServerTransport` from `@modelcontextprotocol/sdk/server/stdio.js`
- Create server instance with name and version
- Connect with transport
- Register one placeholder tool to verify setup

#### Step 5: Create API Client Skeleton

Create `src/client.ts` with:
- Constructor accepting API key from env var
- Base URL configuration
- Placeholder request method

#### Step 6: Set Up Tests

Create `tests/setup.ts` and one placeholder test to verify test runner works.

### Python Scaffolding

#### Step 1: Initialize Project

```bash
mkdir {service}_mcp && cd {service}_mcp
```

#### Step 2: Create pyproject.toml

```toml
[project]
name = "{service}-mcp"
version = "0.1.0"
requires-python = ">=3.10"
dependencies = ["mcp>=1.0", "httpx", "pydantic"]

[project.scripts]
{service}-mcp = "{service}_mcp.server:main"

[tool.pytest.ini_options]
testpaths = ["tests"]
```

#### Step 3: Install Dependencies

```bash
pip install -e ".[dev]"
# or: uv pip install -e ".[dev]"
```

#### Step 4: Create Server Entry Point

Create `server.py` with minimal `FastMCP` setup:
- Import `FastMCP` from `mcp.server.fastmcp`
- Create server instance
- Register one placeholder tool
- Add `main()` function

#### Step 5: Create API Client Skeleton

Create `client.py` with:
- `httpx.AsyncClient` base
- API key from env var
- Base URL configuration

#### Step 6: Set Up Tests

Create `tests/conftest.py` and one placeholder test with pytest.

### Common Steps (both languages)

#### Step 7: Set Up Git Worktree

Delegate to `using-git-worktrees` for isolated workspace if in existing repo.

#### Step 8: Verify

```bash
# TypeScript
npm run build   # must succeed
node dist/index.js  # must start without errors (Ctrl+C to stop)
npm test  # placeholder test passes

# Python
python -m py_compile server.py  # must succeed
python server.py  # must start without errors
pytest  # placeholder test passes
```

## Exit Criteria

- [ ] Project initialized with correct structure
- [ ] All dependencies installed
- [ ] Server entry point created with placeholder tool
- [ ] API client skeleton created
- [ ] Test infrastructure set up with passing placeholder test
- [ ] Build succeeds (TS) or compilation check passes (Python)
- [ ] Server starts without errors

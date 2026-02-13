# Phase 1: Discovery

## Contents
- [Objective](#objective)
- [Skills](#skills)
- [Prerequisites](#prerequisites)
- [Workflow](#workflow)
- [Team Opportunity](#team-opportunity)
- [Exit Criteria](#exit-criteria)

## Objective

Research the target API, study MCP protocol, map endpoints to tools, and decide language/transport/auth strategy.

## Skills

| Order | Skill | Purpose |
|-------|-------|---------|
| 1 | brainstorming | Explore scope, validate API coverage approach |
| 2 | mcp-builder (research) | MCP protocol study, best practices, SDK review |

## Prerequisites

User must provide target API/service name. If unclear, ask before proceeding.

## Workflow

### Step 1: Brainstorming

Delegate to `brainstorming` with the target API/service. Explore:
- What operations does the API support?
- Who will use this MCP server? (developers, agents, end-users via agent)
- What are the most valuable tools to expose?
- Read-only vs write operations priority

### Step 2: Fetch API Documentation

```
WebFetch or WebSearch the target API documentation
Map all available endpoints
Identify authentication requirements
Note rate limits, pagination patterns, data models
```

Save to `docs/discovery/api-analysis.md`.

### Step 3: Study MCP Protocol

Inject `mcp-builder/SKILL.md` + `mcp-builder/reference/mcp_best_practices.md`.

Key areas:
- Tool naming conventions (`{service}_{action}_{resource}`)
- Response format guidelines (JSON + Markdown)
- Pagination patterns
- Transport options (stdio vs streamable HTTP)
- Tool annotations (readOnlyHint, destructiveHint, etc.)

### Step 4: Map Endpoints to Tools

Create tool mapping document:
```
| API Endpoint | MCP Tool Name | Type | Priority |
|-------------|---------------|------|----------|
| GET /users | service_list_users | read | high |
| POST /users | service_create_user | write | medium |
```

Save to `docs/discovery/tool-mapping.md`.

### Step 5: Language & Transport Decision

Apply the decision matrix from `references/language-decision-matrix.md`:

1. Check if API target has official SDK in TS or Python
2. Check user's CLAUDE.md for `## Tech Stack`
3. Check if `outputSchema`/`structuredContent` is needed
4. Default to TypeScript if no strong reason for Python

Decide transport:
- **stdio**: local integration, single-user (most common)
- **streamable HTTP**: remote server, multi-client
- **both**: if server needs to support both scenarios

Decide auth:
- **api-key**: most APIs use this
- **oauth2**: if API requires OAuth flow
- **none**: public APIs

Save decisions to `docs/discovery/design-decisions.md`.

### Step 6: Update State

Update `status.json` with `language`, `transport`, `auth_strategy`, `server_name`.

Server name format:
- TypeScript: `{service}-mcp-server`
- Python: `{service}_mcp`

## Team Opportunity

Research in parallel (3 agents):
```
Agent A: API docs + endpoint mapping (Steps 2, 4)
Agent B: MCP protocol + SDK docs (Step 3)
Agent C: Target service SDK exploration
Sync: merge findings, make language/transport decision (Step 5)
```

## Exit Criteria

- [ ] API analysis document with endpoints, auth, rate limits
- [ ] Tool mapping with 5+ proposed tools
- [ ] Language chosen (TypeScript or Python) with justification
- [ ] Transport chosen (stdio, streamable HTTP, or both)
- [ ] Auth strategy defined
- [ ] Server name defined
- [ ] All docs in `docs/discovery/`

## Notes

- Do NOT start coding in this phase -- discovery only
- Prefer comprehensive API coverage over workflow shortcuts
- The language decision is automatic, not user-prompted

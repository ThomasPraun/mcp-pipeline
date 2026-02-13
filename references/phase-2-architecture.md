# Phase 2: Architecture

## Contents
- [Objective](#objective)
- [Skills](#skills)
- [Prerequisites](#prerequisites)
- [Workflow](#workflow)
- [Exit Criteria](#exit-criteria)

## Objective

Design tool schemas, project structure, and create an implementation plan.

## Skills

| Order | Skill | Purpose |
|-------|-------|---------|
| 1 | backend-architect | API client design, project structure, error handling patterns |
| 2 | find-skills | Discover language-specific skills for chosen stack |
| 3 | writing-plans | Create implementation plan from architecture |

## Prerequisites

Phase 1 completed: API analysis, tool mapping, language/transport decisions in `docs/discovery/`.

## Workflow

### Step 1: Design Tool Schemas

For each tool from the mapping, define:

**Tool name**: `{service}_{action}_{resource}` (snake_case)

**Input schema** (Zod for TS, Pydantic for Python):
- Required vs optional parameters
- Constraints and validation rules
- Clear descriptions with examples

**Output schema** (if using TypeScript with `outputSchema`):
- Structured response shape
- Field descriptions

**Annotations**:
- `readOnlyHint`: true for GET operations
- `destructiveHint`: true for DELETE operations
- `idempotentHint`: true for PUT operations
- `openWorldHint`: true (interacts with external API)

**Description**: concise, unambiguous, matches actual functionality.

Save to `docs/architecture/tool-schemas.md`.

### Step 2: Design Project Structure

**TypeScript project:**
```
{service}-mcp-server/
  src/
    index.ts          # Server entry, tool registration
    client.ts         # API client with auth
    tools/
      {resource}.ts   # Tool handlers grouped by resource
    types.ts          # Shared types
    utils.ts          # Pagination, formatting, error helpers
  tests/
    tools/
      {resource}.test.ts
  package.json
  tsconfig.json
```

**Python project:**
```
{service}_mcp/
  server.py           # Server entry, tool registration
  client.py           # API client with auth
  tools/
    {resource}.py     # Tool handlers grouped by resource
  models.py           # Pydantic models
  utils.py            # Pagination, formatting, error helpers
  tests/
    test_{resource}.py
  pyproject.toml
```

Save to `docs/architecture/project-structure.md`.

### Step 3: Design API Client

Define the shared API client:
- Authentication handling (env vars for keys, OAuth flow if needed)
- Base URL configuration
- Error handling (rate limits, retries, timeouts)
- Response parsing
- Pagination helpers

Save to `docs/architecture/api-client-design.md`.

### Step 4: Discover Stack-Specific Skills

```
RUN: npx skills find {language}
RUN: npx skills find {framework}
PRESENT results, OFFER install
```

### Step 5: Create Implementation Plan

Delegate to `writing-plans` with all architecture docs. Plan should break work into tasks:
1. Project setup + dependencies
2. API client implementation
3. Tool implementations (ordered: read-only first, then write)
4. Error handling + pagination
5. Tests

Save to `docs/plans/implementation-plan.md`.

## Exit Criteria

- [ ] All tools have complete schemas (input, output, annotations, descriptions)
- [ ] Project structure documented
- [ ] API client design documented
- [ ] Implementation plan created and approved
- [ ] All docs in `docs/architecture/` and `docs/plans/`

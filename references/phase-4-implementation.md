# Phase 4: Implementation

## Contents
- [Objective](#objective)
- [Skills](#skills)
- [Prerequisites](#prerequisites)
- [Workflow](#workflow)
- [Team Opportunity](#team-opportunity)
- [Exit Criteria](#exit-criteria)

## Objective

Implement all MCP tools following TDD, with code review and verification.

## Skills

| Order | Skill | Purpose |
|-------|-------|---------|
| 1 | test-driven-development | Write tests before each tool implementation |
| 2a | executing-plans | Execute plan step by step with review checkpoints |
| 2b | subagent-driven-development | Alternative: parallel execution of independent tasks |
| 3 | systematic-debugging | When tests fail or unexpected behavior |
| 4 | requesting-code-review | Package changes for review |
| 5 | receiving-code-review | Process review feedback |
| 6 | verification-before-completion | Verify all tools work before claiming done |

## Prerequisites

Phase 3 completed: project scaffolded, build passes, server starts, test runner works.

## Workflow

### Step 1: Implement API Client

Complete the API client from the skeleton:
- Authentication (read API key from env var)
- HTTP methods (GET, POST, PUT, DELETE)
- Error handling with actionable messages
- Rate limit handling
- Response parsing
- Write tests for the client

### Step 2: Implement Tools (TDD cycle per tool)

For each tool in the implementation plan:

```
1. Write test first (TDD)
   - Test valid input -> expected output
   - Test invalid input -> proper error
   - Test pagination if applicable
   - Test edge cases

2. Implement handler
   - Parse and validate input (Zod/Pydantic)
   - Call API client
   - Format response (both text content and structured data)
   - Handle errors with actionable messages

3. Add annotations
   - readOnlyHint, destructiveHint, idempotentHint, openWorldHint

4. Add response format support
   - JSON for programmatic processing
   - Markdown for human readability (typically default)

5. Handle pagination
   - Accept limit/offset or cursor parameters
   - Return has_more, next_offset/next_cursor, total_count

6. Register tool with server
   - Name: {service}_{action}_{resource}
   - Description: concise, matches actual functionality
   - Input schema with Zod/Pydantic
   - Output schema if using TypeScript
```

**Order**: implement read-only tools first (list, get, search), then write tools (create, update, delete).

### Step 3: Code Review

1. Delegate to `requesting-code-review` -- package all changes
2. Delegate to `receiving-code-review` -- process feedback with rigor
3. If issues found: fix -> re-test -> re-review

### Step 4: Verify

Delegate to `verification-before-completion`. Do not proceed until it passes.

## Team Opportunity

Read-only vs write tools in parallel:
```
Agent A: Implement read-only tools (list, get, search)
Agent B: Implement write tools (create, update, delete)
Sync: integration testing after both complete
```

**MANDATORY pre-dispatch checklist:**
- [ ] Scanned `~/.claude/skills/` for matching skills
- [ ] Identified relevant skills (mcp-builder + methodology like TDD)
- [ ] Prepended `=== READ FIRST ===` block with mcp-builder files

## Exit Criteria

- [ ] All planned tools implemented
- [ ] All tests pass (unit + integration)
- [ ] Error handling covers all API error codes
- [ ] Pagination works for list operations
- [ ] Response formats support JSON and Markdown
- [ ] Tool annotations set correctly
- [ ] Code review completed and approved
- [ ] Verification confirms all tools functional

## Notes

- Never skip TDD -- catches API integration issues early
- If API behavior differs from docs, update `docs/discovery/api-analysis.md`
- Keep tool handlers focused -- delegate complex logic to utils

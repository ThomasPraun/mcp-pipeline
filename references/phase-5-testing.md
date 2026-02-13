# Phase 5: Testing

## Contents
- [Objective](#objective)
- [Skills](#skills)
- [Prerequisites](#prerequisites)
- [Workflow](#workflow)
- [Exit Criteria](#exit-criteria)

## Objective

Validate all tools with MCP Inspector, test transport modes, and verify auth handling.

## Skills

| Order | Skill | Purpose |
|-------|-------|---------|
| 1 | systematic-debugging | Structured approach when tests fail |
| 2 | verification-before-completion | Final verification pass |

## Prerequisites

Phase 4 completed: all tools implemented, unit tests passing, code review approved.

## Workflow

### Step 1: MCP Inspector Setup

```bash
npx @modelcontextprotocol/inspector
```

The Inspector provides a web UI to interact with the MCP server. Configure it to connect to the server:

**stdio**: point to `node dist/index.js` (TS) or `python server.py` (Python)
**streamable HTTP**: point to the server URL

### Step 2: Tool Listing Verification

In the Inspector:
- Verify all tools are listed
- Check tool names follow `{service}_{action}_{resource}` convention
- Verify descriptions are concise and accurate
- Confirm annotations are present and correct

### Step 3: Tool Execution Testing

For each tool, test:

**Valid input:**
- [ ] Returns expected data format
- [ ] Text content is human-readable
- [ ] Structured content matches output schema (if defined)
- [ ] Response includes relevant metadata

**Invalid input:**
- [ ] Missing required params returns clear error
- [ ] Invalid param values return actionable error message
- [ ] Error suggests correct usage

**Pagination (for list tools):**
- [ ] Default limit returns reasonable count (20-50)
- [ ] Custom limit is respected
- [ ] `has_more` is accurate
- [ ] `next_offset`/`next_cursor` works for subsequent pages
- [ ] Empty results handled gracefully

**Edge cases:**
- [ ] Empty/null responses handled
- [ ] Large responses don't crash
- [ ] Special characters in input/output
- [ ] Rate limit responses handled

### Step 4: Transport Testing

**stdio (always test):**
- [ ] Server starts via `node dist/index.js` or `python server.py`
- [ ] No output to stdout except MCP protocol messages
- [ ] Logging goes to stderr
- [ ] Clean shutdown on SIGINT

**Streamable HTTP (if configured):**
- [ ] Server binds to configured port
- [ ] Health check endpoint responds
- [ ] Multiple concurrent requests handled
- [ ] CORS headers if needed
- [ ] DNS rebinding protection for local servers

### Step 5: Auth Testing

**API Key auth:**
- [ ] Server reads key from env var
- [ ] Missing key produces clear startup error
- [ ] Invalid key produces actionable error on first API call

**OAuth (if applicable):**
- [ ] Token acquisition flow works
- [ ] Token refresh works
- [ ] Expired token handled gracefully

### Step 6: Integration Checklist

Verify the server works in real clients:
- [ ] Claude Desktop: add to `claude_desktop_config.json`, verify tools appear
- [ ] Cursor/VS Code: add to MCP settings, verify tools appear (if applicable)

### Step 7: Debug and Fix

Use `systematic-debugging` for any failures found. Fix -> re-test -> verify.

## Exit Criteria

- [ ] MCP Inspector lists all tools correctly
- [ ] All tools execute successfully with valid input
- [ ] All tools return proper errors with invalid input
- [ ] Pagination works for all list operations
- [ ] Transport works (stdio and/or streamable HTTP)
- [ ] Auth handling verified
- [ ] At least one real client integration tested

## Notes

- The Inspector is the primary testing tool -- use it before anything else
- Document any API quirks discovered during testing in `docs/discovery/api-analysis.md`
- If tools need changes, go back to Phase 4 for TDD cycle

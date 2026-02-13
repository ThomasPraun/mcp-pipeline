# Language Decision Matrix: TypeScript vs Python

## Quick Decision Tree

```
1. Does the API target have an official SDK?
   -> Only Python SDK -> Python
   -> Only JS/TS SDK -> TypeScript
   -> Both or neither -> continue

2. Does the project need outputSchema / structuredContent?
   -> Yes -> TypeScript (Python SDK lacks this)
   -> No -> continue

3. What is the user's ecosystem? (check CLAUDE.md ## Tech Stack)
   -> Python-based -> Python
   -> JS/TS-based -> TypeScript
   -> Mixed or unknown -> continue

4. Default -> TypeScript
```

## Detailed Comparison

| Criterion | TypeScript | Python |
|-----------|-----------|--------|
| **SDK** | `@modelcontextprotocol/sdk` | `mcp` (FastMCP) |
| **Server class** | `McpServer` | `FastMCP` |
| **Schema validation** | Zod | Pydantic |
| **outputSchema support** | Yes (`structuredContent`) | No (as of 2025) |
| **Transport: stdio** | Full support | Full support |
| **Transport: streamable HTTP** | Full support | Full support |
| **Type safety** | Static types (compile-time) | Runtime types (Pydantic) |
| **Deployment** | `npx`, npm package | `pip install`, `uvx` |
| **Docker** | `node:20-slim` (~50MB) | `python:3.12-slim` (~45MB) |
| **LLM code generation** | Excellent (broad usage, static typing) | Excellent (broad usage) |
| **Client compatibility** | All clients | All clients |
| **Ecosystem size** | Larger npm ecosystem | Larger data science ecosystem |
| **Async model** | Native async/await | asyncio + httpx |

## When TypeScript Wins

1. **outputSchema / structuredContent**: Only TypeScript SDK supports defining output schemas that help clients understand and process tool outputs. Critical for tools returning structured data.

2. **Official recommendation**: The MCP documentation recommends TypeScript for new servers due to high-quality SDK support and good compatibility across execution environments.

3. **JS/TS SDK available**: If the target API has an official JavaScript/TypeScript SDK, use it directly rather than making raw HTTP calls.

4. **Type safety at compile time**: TypeScript catches errors before runtime, which is valuable for complex tool implementations.

## When Python Wins

1. **Python-only SDK**: If the target API only provides a Python SDK (common in data science, ML, scientific computing).

2. **Python ecosystem**: If the user's stack is Python-based and they'll maintain the server.

3. **Python-native protocols**: Some services use protocols that are more naturally handled in Python.

4. **FastMCP simplicity**: Python's FastMCP decorator pattern is simpler for basic servers:
   ```python
   @mcp.tool()
   async def my_tool(param: str) -> str:
       return result
   ```

## Decision Output

After deciding, update `status.json`:
```json
{
  "language": "typescript",  // or "python"
  "server_name": "{service}-mcp-server",  // TS format
  // or
  "server_name": "{service}_mcp"  // Python format
}
```

And document the rationale in `docs/discovery/design-decisions.md`.

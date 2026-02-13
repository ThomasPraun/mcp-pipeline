# Skill Injection Protocol

> **Quick version:** See the Skill Injection Rule in SKILL.md for the inline rule and examples. This file provides the full algorithm for complex cases.

When delegating tasks to any agent (Task tool subagent or Agent Team teammate), identify relevant skills and instruct the agent to read them.

IMPORTANT: Always instruct agents to read the full SKILL.md file plus any matching reference files. Never summarize or paraphrase skill contents.

## Protocol

Before dispatching any agent:

### Step 1: Define the task profile

Determine for the agent's task:
- **Technologies involved** (TypeScript/Python, MCP SDK, target API)
- **Task type** (research, architecture, implementation, testing, evaluation)
- **Project language** from `.claude/mcp-project/status.json` (`language` field)

### Step 2: Always include mcp-builder (Phases 1-6)

**This is the key difference from saas-pipeline.** The `mcp-builder` skill MUST be included in every subagent dispatch during Phases 1-6, with phase-specific files:

| Phase | mcp-builder files |
|-------|-------------------|
| 1 | `SKILL.md`, `reference/mcp_best_practices.md` |
| 2 | `SKILL.md`, `reference/mcp_best_practices.md`, `reference/node_mcp_server.md` OR `reference/python_mcp_server.md` |
| 3 | `reference/node_mcp_server.md` OR `reference/python_mcp_server.md` |
| 4 | `SKILL.md`, `reference/node_mcp_server.md` OR `reference/python_mcp_server.md`, `reference/mcp_best_practices.md` |
| 5 | `reference/mcp_best_practices.md` |
| 6 | `SKILL.md`, `reference/evaluation.md` |

Choose `node_mcp_server.md` when `language == "typescript"`, `python_mcp_server.md` when `language == "python"`.

### Step 3: Scan for additional skills

```
FOR each directory in ~/.claude/skills/:
  READ SKILL.md frontmatter (name, description)
  EVALUATE: does this skill align with the agent's task?
  Match criteria (any of):
    - Skill name/description mentions a technology the task uses
    - Skill description matches the task type
    - Skill is listed in the current phase's skill table
  IF match -> add to candidate list
```

### Step 4: Select and prioritize

From candidates, select the most relevant (max 4-5 per agent including mcp-builder):
- mcp-builder is always slot 1 (Phases 1-6)
- Prioritize skills matching primary technology or purpose
- Include methodology skills (TDD for implementation, debugging for testing)

### Step 5: Identify reference files

For each selected skill:
```
READ the skill's SKILL.md
CHECK for references/ directory
IF references exist:
  MATCH reference topics against the agent's task
  ADD matching reference paths to reading list
```

### Step 6: Build the READ FIRST block

```
=== READ FIRST ===
Read: ~/.claude/skills/mcp-builder/SKILL.md
Read: ~/.claude/skills/mcp-builder/reference/mcp_best_practices.md
Read: ~/.claude/skills/mcp-builder/reference/node_mcp_server.md
Read: ~/.claude/skills/{other-skill}/SKILL.md
Read: ~/.claude/skills/{other-skill}/references/{matching-ref}.md
=== END READ ===

=== TASK ===
[task details, context, requirements]
=== END TASK ===
```

## Notes

- If no relevant skills beyond mcp-builder are found, dispatch with just mcp-builder
- Run this protocol at delegation time, not at phase start
- For parallel agents, each agent gets mcp-builder + their domain-specific skills
- Never summarize skill contents -- always instruct agents to read source files
- Phase 7 does not require mcp-builder injection

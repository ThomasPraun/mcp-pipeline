---
name: mcp-pipeline
description: "Use when starting a new MCP server project, checking MCP project status, advancing to the next phase, viewing the MCP dashboard, or when the user mentions 'mcp pipeline', 'mcp server', 'build mcp', 'create mcp', 'next phase', 'mcp status'. Orchestrates skills across 7 phases: discovery, architecture, scaffolding, implementation, testing, evaluation, distribution."
---

# MCP Pipeline Coordinator

Coordinate, never execute. Read project state, route to the correct skill, track progress, delegate all work.

- **Explicit invocation only** - activate only when invoked
- **Progressive disclosure** - load `references/phase-N-*.md` only when entering a phase
- **No duplication** - delegate to skills, never reimplement their work
- **Desync safety** - verify actual file existence, not just status.json claims
- **Skill-aware delegation** - MANDATORY: every subagent prompt must include a `=== READ FIRST ===` block with relevant skills (see [Skill Injection Rule](#skill-injection-rule))

---

## State Management

### Initialize

When `.claude/mcp-project/` does not exist, create it with `status.json`:

```json
{
  "project_name": "<ask user>",
  "service_name": "<api/service being integrated>",
  "language": null,
  "transport": null,
  "auth_strategy": null,
  "server_name": null,
  "created_at": "<ISO-8601>",
  "updated_at": "<ISO-8601>",
  "current_phase": 1,
  "phases": {
    "1-discovery": {
      "status": "pending", "started_at": null, "completed_at": null,
      "skills": { "brainstorming": "pending", "mcp-builder-research": "pending" },
      "outputs": []
    },
    "2-architecture": {
      "status": "pending", "started_at": null, "completed_at": null,
      "skills": { "backend-architect": "pending", "find-skills": "pending", "writing-plans": "pending" },
      "outputs": []
    },
    "3-scaffolding": {
      "status": "pending", "started_at": null, "completed_at": null,
      "skills": { "test-driven-development": "pending", "using-git-worktrees": "pending" },
      "outputs": []
    },
    "4-implementation": {
      "status": "pending", "started_at": null, "completed_at": null,
      "skills": { "tdd": "pending", "execution": "pending", "debug": "pending", "review": "pending", "verify": "pending" },
      "outputs": []
    },
    "5-testing": {
      "status": "pending", "started_at": null, "completed_at": null,
      "skills": { "systematic-debugging": "pending", "verification": "pending" },
      "outputs": []
    },
    "6-evaluation": {
      "status": "pending", "started_at": null, "completed_at": null,
      "skills": { "mcp-builder-evaluation": "pending" },
      "outputs": []
    },
    "7-distribution": {
      "status": "pending", "started_at": null, "completed_at": null,
      "skills": { "finishing-branch": "pending", "docker": "pending" },
      "outputs": []
    }
  }
}
```

After language selection (Phase 1), update `language`, `transport`, `auth_strategy`, and `server_name`.

### Update

After each significant action, update `updated_at`, phase `status`/timestamps, skill statuses, `outputs` array, and `current_phase`.

---

## Router

Execute on every invocation:

### Step 1: Load State

Read `.claude/mcp-project/status.json`. If missing, initialize first.

### Step 2: Classify Intent

| Intent | Action |
|--------|--------|
| "what's next" / no clear intent | Auto-route (Step 3) |
| "status" / "dashboard" | Show dashboard |
| "go to phase N" / "start phase N" | Validate prerequisites, execute |
| Specific skill name | Verify phase, delegate |
| "team" / "parallel" | Evaluate parallelism (see Agent Teams) |
| "skip to N" | Check required outputs exist, allow if yes |

### Step 3: Auto-Route

```
phase = phases[current_phase]
IF pending   -> read references/phase-N-*.md, invoke first skill
IF in_progress -> find next pending skill in phase, invoke it
IF completed -> advance current_phase, start next
```

### Skill Injection Rule

**MANDATORY for every subagent dispatch.** Always run the scan -- never skip it.

Before dispatching any agent (Task tool or Agent Team):

1. Scan `~/.claude/skills/` -- match skill names/descriptions to the agent's task
2. Select up to 4-5 relevant skills
3. For each selected skill, read its SKILL.md and check for references. Include references matching the agent's task
4. Prepend a `=== READ FIRST ===` block. **Always include `mcp-builder`** in Phases 1-6

#### mcp-builder injection by phase

| Phase | mcp-builder files to inject |
|-------|------------------------------|
| 1 | `SKILL.md`, `reference/mcp_best_practices.md` |
| 2 | `SKILL.md`, `reference/mcp_best_practices.md`, `reference/node_mcp_server.md` OR `reference/python_mcp_server.md` |
| 3 | `reference/node_mcp_server.md` OR `reference/python_mcp_server.md` |
| 4 | `SKILL.md`, `reference/node_mcp_server.md` OR `reference/python_mcp_server.md`, `reference/mcp_best_practices.md` |
| 5 | `reference/mcp_best_practices.md` |
| 6 | `SKILL.md`, `reference/evaluation.md` |
| 7 | (none) |

Choose `node_mcp_server.md` or `python_mcp_server.md` based on `status.json` `language` field.

```
=== READ FIRST ===
Read: ~/.claude/skills/mcp-builder/SKILL.md
Read: ~/.claude/skills/mcp-builder/reference/mcp_best_practices.md
Read: ~/.claude/skills/{other-skill}/SKILL.md
=== END READ ===
```

For the full scanning algorithm, see `references/skill-injection-protocol.md`.

### Dashboard

Display on status requests:

```
+----------------------------------------------+
|          PROJECT: {project_name}             |
|          Service: {service_name}             |
|          Language: {language} | Transport: {transport}  |
|          Tools: {tool_count} defined         |
+----------------------------------------------+
|  Phase 1 - Discovery       ############ 100% |
|  Phase 2 - Architecture    ########....  67% |
|  Phase 3 - Scaffolding     ............   0% |
|  Phase 4 - Implementation  ............   0% |
|  Phase 5 - Testing         ............   0% |
|  Phase 6 - Evaluation      ............   0% |
|  Phase 7 - Distribution    ............   0% |
|                                              |
|  Current: Phase 2 -> Next: writing-plans     |
+----------------------------------------------+
```

Calculate percentage: skills completed / total skills in phase.

---

## The 7 Phases

When entering a phase, read its reference file for detailed workflow.

| # | Phase | Skills | Key Output | Exit Gate | Reference |
|---|-------|--------|------------|-----------|-----------|
| 1 | Discovery | brainstorming -> mcp-builder (research) | API analysis + tool mapping + language decision | API mapped, tools listed, language chosen | `references/phase-1-discovery.md` |
| 2 | Architecture | backend-architect -> find-skills -> writing-plans | Tool schemas + project structure + plan | Schemas defined, plan approved | `references/phase-2-architecture.md` |
| 3 | Scaffolding | TDD -> using-git-worktrees | Project initialized, tests base, server starts | Build passes, server starts | `references/phase-3-scaffolding.md` |
| 4 | Implementation | TDD -> {executing-plans OR subagent-dev} + debug + review + verify | All tools implemented | Tests pass, review approved | `references/phase-4-implementation.md` |
| 5 | Testing | systematic-debugging -> verification | Inspector OK, transport OK, auth OK | All tools pass manual testing | `references/phase-5-testing.md` |
| 6 | Evaluation | mcp-builder evaluation | 10 QA pairs + evaluation report | Accuracy >= 70%, feedback addressed | `references/phase-6-evaluation.md` |
| 7 | Distribution | finishing-branch + docker-expert (optional) | README + package config + published | README complete, package installable | `references/phase-7-distribution.md` |

### Gateway Skills

Always invoke these before their dependent work:

| Skill | Gate For |
|-------|----------|
| brainstorming | Any creative/feature work |
| test-driven-development | Implementation code (phases 3-4) |
| verification-before-completion | Any completion claim |

### Utility Skills (always available)

`find-skills` (discover/install), `skill-creator` (create custom), `dispatching-parallel-agents` (orchestrate teams)

---

## Language Selection (Phase 1)

The pipeline does NOT ask the user which language to use. It analyzes the case and recommends automatically. See `references/language-decision-matrix.md` for the full decision tree.

**Choose TypeScript when:**
- API target has official JS/TS SDK
- `outputSchema` / `structuredContent` needed
- User ecosystem is JS/TS (detected in CLAUDE.md)
- No strong reason for Python

**Choose Python when:**
- API target only has official Python SDK
- User ecosystem is Python (detected in CLAUDE.md)
- API target uses Python-native protocols

**Default:** TypeScript (recommended by official MCP documentation)

---

## Agent Teams

Four parallelism opportunities via `dispatching-parallel-agents`. Always offer sequential as alternative.

Before dispatching agents, apply the [Skill Injection Rule](#skill-injection-rule).

### 1. Phase 1: Research in parallel
```
Agent A: API docs + endpoint mapping
Agent B: MCP protocol + SDK docs
Agent C: Target service SDK exploration
Sync: merge findings into discovery docs
```

### 2. Phase 4: Read-only vs write tools
```
Agent A: Implement read-only tools (list, get, search)
Agent B: Implement write tools (create, update, delete)
Sync: integration testing
```

### 3. Phase 6: Documentation + Tool inspection
```
Agent A: Documentation inspection (API docs, target service)
Agent B: Tool inspection (list tools, understand schemas)
Sync: generate QA pairs from combined understanding
```

### 4. Phase 7: README + Docker
```
Agent A: README + package configuration
Agent B: Docker setup (only if streamable HTTP transport)
Sync: final verification
```

---

## Auto-Install Missing Skills

On every invocation, verify skills for current and next phase are installed.

```
FOR each skill in current_phase:
  CHECK ~/.claude/skills/{skill-name}/ exists
  IF missing -> read references/install-commands.md, inform user, offer install command, block phase if declined
```

---

## Non-Linear Navigation

- **Skip ahead**: allowed if target phase's required inputs exist (phase 3 needs discovery + architecture docs, phase 4 needs scaffolded project, phase 6 needs all tools implemented)
- **Go back**: always allowed, never deletes previous outputs
- **Partial phases**: track individual skill completion; resume from last incomplete skill

```
FOR each skill in phase.skills:
  IF skill status == "completed" -> skip
  ELSE -> next skill to execute
```

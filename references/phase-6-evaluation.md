# Phase 6: Evaluation

## Contents
- [Objective](#objective)
- [Skills](#skills)
- [Prerequisites](#prerequisites)
- [Workflow](#workflow)
- [Team Opportunity](#team-opportunity)
- [Exit Criteria](#exit-criteria)

## Objective

Create 10 QA evaluation pairs and run the evaluation harness to measure MCP server quality. Target accuracy >= 70%.

## Skills

| Order | Skill | Purpose |
|-------|-------|---------|
| 1 | mcp-builder (evaluation) | Complete evaluation creation and execution guide |

## Prerequisites

Phase 5 completed: all tools pass Inspector testing, transport verified, auth verified.

## Workflow

### Step 1: Study Target Service Data

Use the MCP server tools (READ-ONLY operations only) to explore available data:
- List resources, users, projects, etc.
- Understand data patterns and relationships
- Identify stable/historical content that won't change
- Use `limit` parameter to avoid context overflow
- Make incremental, targeted tool calls

### Step 2: Find Stable Content

Look for "closed" concepts:
- Completed projects, resolved issues
- Historical data from specific time periods
- Archived items, past events
- Content unlikely to change over time

### Step 3: Create 10 QA Pairs

Follow the guidelines in `mcp-builder/reference/evaluation.md`:

Each question must be:
- **Independent**: not dependent on other questions
- **Read-only**: only non-destructive operations required
- **Complex**: requiring multiple tool calls (potentially dozens)
- **Realistic**: based on real human use cases
- **Verifiable**: single clear answer via string comparison
- **Stable**: answer won't change over time

Answer diversity: mix of names, IDs, counts, dates, booleans, usernames.

### Step 4: Write evaluation.xml

```xml
<evaluation>
  <qa_pair>
    <question>Complex question requiring multiple tool calls...</question>
    <answer>Single verifiable answer</answer>
  </qa_pair>
  <!-- 9 more qa_pairs -->
</evaluation>
```

Save to project root as `evaluation.xml`.

### Step 5: Verify Answers

Before running the harness, verify each answer yourself:
- Use the MCP server tools to solve each question
- Parallelize solving tasks across subagents
- Confirm each answer is correct and stable
- Fix any incorrect answers

### Step 6: Run Evaluation Harness

**TypeScript server:**
```bash
python ~/.claude/skills/mcp-builder/scripts/evaluation.py \
  -t stdio \
  -c node \
  -a dist/index.js \
  -e API_KEY=your_key \
  -o evaluation_report.md \
  evaluation.xml
```

**Python server:**
```bash
python ~/.claude/skills/mcp-builder/scripts/evaluation.py \
  -t stdio \
  -c python \
  -a server.py \
  -e API_KEY=your_key \
  -o evaluation_report.md \
  evaluation.xml
```

### Step 7: Analyze Results

Review `evaluation_report.md`:
- Check accuracy (target >= 70%)
- Read agent feedback for each failed question
- Identify tool improvements (descriptions, schemas, error messages)
- Note if questions need adjustment (ambiguous, answer changed)

### Step 8: Iterate

If accuracy < 70%:
1. Improve tools based on agent feedback
2. Fix question/answer issues
3. Re-run evaluation
4. Repeat until >= 70%

## Team Opportunity

Documentation + Tool inspection in parallel:
```
Agent A: Documentation inspection (fetch API docs, understand service)
Agent B: Tool inspection (list tools, understand schemas, explore data)
Sync: generate QA pairs from combined understanding
```

## Exit Criteria

- [ ] 10 QA pairs created in `evaluation.xml`
- [ ] All answers verified manually
- [ ] Evaluation harness runs successfully
- [ ] Accuracy >= 70%
- [ ] Agent feedback reviewed and addressed
- [ ] Evaluation report saved

## Notes

- Do NOT read the MCP server source code during evaluation -- treat it as a black box
- Questions should stress-test the tools, not just verify basic functionality
- If evaluation reveals tool issues, go back to Phase 4 to fix them

---
name: bootstrap
description: "MANDATORY SESSION INITIALIZER - Must be invoked FIRST at every new conversation, Plan mode entry, or status query. Loads persistent context, unfinished tasks, and project state. Triggers mandatory skill chain: load-context -> manage-project -> plan-tasks. Use when: new conversation starts, Plan mode, user asks about status/progress/tasks."
---

# Bootstrap - Session Initializer

=====================================
>>> MANDATORY FIRST ACTION <<<
=====================================

This skill MUST be invoked at the START of every conversation to ensure continuity.

## Trigger Conditions (MANDATORY)

**ALWAYS invoke this skill when:**
- New conversation starts (MANDATORY)
- Entering Plan mode / ultrathink (MANDATORY)
- User asks about "current status", "progress", "what tasks", "project state" (MANDATORY)

**YOU MUST NOT skip this skill in these scenarios.**

## Mandatory Skill Chain

=====================================
>>> REQUIRED SKILL CHAIN <<<
=====================================

After bootstrap completes, the following skills MUST be invoked in order:

```
1. [THIS]     bootstrap         - Session initialization (you are here)
2. [INVOKE]   load-context      - Load persistent context (MANDATORY - invoke immediately)
3. [INVOKE]   manage-project    - Get project plans and progress
4. [INVOKE]   plan-tasks        - Get task list and status
```

**Chain Invocation Instructions:**

- After this skill, you MUST call `llm-memory-mcp:load-context`
- load-context will then trigger manage-project and plan-tasks
- DO NOT proceed with user requests until the entire chain completes

## Execution Flow

### Step 1: Session Start Acknowledgment

```
=====================================
>>> SESSION BOOTSTRAP <<<
=====================================
[Bootstrap] Initializing session...
```

### Step 2: Invoke load-context (MANDATORY)

>>> MUST immediately invoke `llm-memory-mcp:load-context` <<<

This is NOT optional. Failure to invoke will result in context loss.

### Step 3: Wait for Chain Completion

The load-context skill will:
1. Call `plan_list` to get all plans
2. Call `todo_list` to get all tasks
3. Call `memory_search` for relevant memories
4. Trigger manage-project and plan-tasks

### Step 4: Report Status

After the entire chain completes, report:
- Active plans and their progress
- Pending todos with assignments
- Relevant memories

## Output Template

```
=====================================
>>> SESSION BOOTSTRAP <<<
=====================================

[Bootstrap] Session initialized
[Bootstrap] Invoking load-context...

--- load-context output ---
{Loaded plans, todos, and memories}
--- end load-context ---

[Bootstrap] Invoking manage-project...

--- manage-project output ---
{Project status and progress}
--- end manage-project ---

[Bootstrap] Invoking plan-tasks...

--- plan-tasks output ---
{Task list and assignments}
--- end plan-tasks ---

=====================================
>>> BOOTSTRAP COMPLETE - READY <<<
=====================================
```

## MCP Tools Used

This skill orchestrates the following tools through the skill chain:

- `plan_list({ scope: "all" })` - Get all plans
- `plan_get({ code: "..." })` - Get plan details
- `todo_list({ scope: "all" })` - Get all tasks
- `memory_search({ keyword: "..." })` - Search memories
- `memory_get({ code: "..." })` - Get memory details

## Related Skills

**MUST invoke after bootstrap (in order):**
1. `load-context` - Load project context (MANDATORY - triggers next skills)
2. `manage-project` - Get project plans
3. `plan-tasks` - Get task list

## FAIL-SAFE Reminder

=====================================
>>> ATTENTION <<<
=====================================

If you are reading this skill, you MUST:

1. Complete the bootstrap initialization
2. Immediately invoke `llm-memory-mcp:load-context`
3. Wait for the skill chain to complete
4. Report loaded context to user

**>>> DO NOT proceed with user requests until bootstrap chain completes <<<**

## Not Trigger Conditions

- Already in an ongoing conversation with loaded context
- Executing specific tasks (task-start, task-complete, etc.)
- Recording decisions or searching history

## Version

- v1.0.0 (2025-12-10): Initial release

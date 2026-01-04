---
description: Phase 3 - Execute implementation plan with coordinated agents
subtask: false
---

Load the `orchestrator-workflow` skill and execute **Phase 3: Implementation**.

## Context

$ARGUMENTS

---

## Instructions

1. **Read the PLAN file** to understand the implementation plan
2. Identify tasks that can run in parallel vs sequentially
3. Launch agents appropriately:
   - **Parallel**: Independent tasks in a single message with multiple Task calls
   - **Sequential**: Dependent tasks one after another
4. Inform parallel agents:
   - They're part of a team working concurrently
   - Type errors might be from other agents
   - Focus only on their assigned files
   - Don't run type-check themselves
5. **Update PLAN file progress** after each major completion
6. Run `bun run type-check` after ALL agents complete
7. Fix any type errors
8. Mark PLAN file as complete

Type-check must succeed. Linter errors can be deferred.

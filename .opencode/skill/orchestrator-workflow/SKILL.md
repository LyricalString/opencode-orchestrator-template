---
name: orchestrator-workflow
description: Multi-phase workflow for complex tasks with async agent orchestration and persistent PLAN file tracking
license: MIT
metadata:
  author: Alex Martinez
  version: '1.0'
---

# Orchestrator Workflow Protocol

Use this workflow for complex tasks like client feedback, multi-issue bug fixes, or large feature implementations.

## Core Principle: The PLAN File

**Always maintain a PLAN markdown file** (e.g., `FEATURE_PLAN.md`, `BUGFIX_PLAN.md`) as the single source of truth:

- Create it at the start of Phase 1
- Update it after each phase and major milestone
- Include: findings, decisions, progress, blockers, next steps
- If the user leaves, they can return to this file and say "continue from the PLAN"

### PLAN File Structure

```markdown
# [Task Name]

**Created:** [Date]
**Status:** [Investigation | Planning | Implementation | Complete]
**Last Updated:** [Date/Time]

---

## Overview

[Brief description of the task/feedback]

---

## Issues Identified

| #   | Issue | Status                                 | Complexity   | Notes |
| --- | ----- | -------------------------------------- | ------------ | ----- |
| 1   | ...   | Investigating/Planned/In Progress/Done | Low/Med/High | ...   |

---

## Investigation Findings

### Issue #1: [Name]

- **Root Cause:** ...
- **Files Involved:** ...
- **Proposed Fix:** ...

---

## Implementation Plan

### Phase 1: [Name]

- [ ] Task 1
- [ ] Task 2

### Phase 2: [Name]

...

---

## Progress Log

- [Date Time] - Started investigation
- [Date Time] - Completed Phase 1
  ...

---

## How to Continue

If resuming this task, start by:

1. Reading this PLAN file
2. Checking the Progress Log for last action
3. Reviewing any in-progress items
```

---

## The Three Phases

### Phase 1: Investigation (Read-Only)

**Goal:** Understand the problem deeply before proposing solutions.

**Steps:**

1. Create the PLAN file with initial structure
2. **Detect affected apps** from the feedback (see App Detection below)
3. Create a TodoWrite list to track each issue
4. Launch **parallel explore agents** for independent investigations
5. Act as orchestrator - synthesize findings
6. **DO NOT make code changes** - investigation only
7. Update PLAN file with findings, including which apps are affected

**App Detection Keywords:**

| Keywords                                          | App           |
| ------------------------------------------------- | ------------- |
| mobile, iOS, Android, phone, app store            | `mobile-app`  |
| admin, dashboard, passes, approve, manage members | `admin-panel` |
| website, portal, web, referral page, subscription | `web-app`     |
| database, migration, RLS, table, schema           | `database`    |

**Launching Parallel Explore Agents:**

```
Use the Task tool multiple times in a SINGLE message to launch parallel agents.
Each agent should investigate ONE specific issue or ONE specific app.
```

**Agent Instructions Template:**

```
You are a senior engineer investigating [ISSUE].

Look for:
1. [Specific files/patterns to check]
2. [Related functionality]
3. [Root cause indicators]

Search in:
- [directories]

Return:
1. Root cause analysis
2. Files that need modification
3. Which app(s) are affected
4. Proposed solution approach
```

---

### Phase 2: Plan & Review

**Goal:** Create a detailed, architecture-aligned implementation plan.

**Steps:**

1. Review investigation findings
2. Check codebase patterns (AGENTS.md, existing code)
3. Ensure plan follows:
   - Data-fetching patterns (React Query conventions)
   - Mutation patterns (cache invalidation)
   - Service layer patterns (ApiResponse, authenticated clients)
   - Component patterns (forms, state management)
4. Update PLAN file with detailed implementation steps
5. **Get user confirmation before proceeding**

**Pattern Review Checklist:**

- [ ] Query keys follow conventions
- [ ] Mutations invalidate related queries
- [ ] Services accept optional `authenticatedClient` parameter
- [ ] Error handling uses toast with title + description
- [ ] Forms use react-hook-form + zod validation

---

### Phase 3: Implementation

**Goal:** Execute the plan with coordinated app-specific agents.

**Steps:**

1. Identify which apps are affected (from Phase 1 findings)
2. Identify independent vs dependent tasks
3. Launch **app-specific agents** appropriately:
   - **Independent apps** → Parallel (single message, multiple Task calls)
   - **Dependencies** → Sequential (e.g., database migration before app code)
4. Update PLAN file progress after each completion
5. Run type-check after ALL agents complete
6. Fix any type errors
7. Final verification

**App-Specific Agents:**

| Agent         | Scope                             |
| ------------- | --------------------------------- |
| `web-app`     | `apps/web-app/` + `packages/`     |
| `mobile-app`  | `apps/mobile-app/` + `packages/`  |
| `admin-panel` | `apps/admin-panel/` + `packages/` |
| `database`    | `supabase/`                       |

**Parallel App Agent Template:**

```
You are a [APP-NAME] specialist implementing changes for this task.
You're part of a team implementing fixes IN PARALLEL across multiple apps.

IMPORTANT:
- Type errors you see might be from OTHER agents' incomplete work
- Focus ONLY on YOUR scope: [app directory] and packages/ if needed
- Do NOT run type-check - orchestrator does that after all complete
- Do NOT modify files in other apps - ask if cross-app changes are needed

Your Task: [specific task]

Files to Modify:
1. [file path] - [what to change]

Follow the patterns documented in your agent definition.
```

**Sequential Task Pattern:**

```
Wait for previous task to complete before launching next.
Use when:
- database migration must exist before app code references new types
- packages/ changes must complete before apps consuming them
- one app's API changes affect another app's consumption
```

**Example: Cross-App Feature Implementation**

```markdown
## Implementation Order

1. **Sequential**: database agent - Create migration for new table
2. **Parallel**: Launch all app agents simultaneously:
   - web-app agent - Add API routes and UI
   - mobile-app agent - Add screens and navigation
   - admin-panel agent - Add admin management UI
3. **Sequential**: Orchestrator runs type-check
```

---

## Coordination Rules

### When to Use Parallel Agents

- Different apps need changes (web-app + mobile-app + admin-panel)
- Tasks touch different files within same app
- No data dependencies between tasks
- Investigation of separate issues

### When to Use Sequential Agents

- **database** migration → then app code using new schema
- **packages/** changes → then apps consuming those packages
- API changes in one app → then another app calling that API
- Type changes → then code using those types

### Type-Check Protocol

1. Only run `bun run type-check` AFTER all agents complete
2. If errors occur, determine which agent's scope they belong to
3. Fix errors in the appropriate scope
4. Re-run type-check

---

## Recovery: Resuming from PLAN File

If returning to an interrupted task:

1. **Read the PLAN file** - understand current state
2. **Check Progress Log** - see last completed action
3. **Review Issues table** - see what's done/pending
4. **Check TodoRead** - see if there's an active todo list
5. **Continue from last incomplete item**

Tell the user: "I've reviewed the PLAN file. Last action was [X]. Next steps are [Y]. Shall I continue?"

---

## Quality Gates

Before moving to next phase, verify:

**After Phase 1:**

- [ ] All issues investigated
- [ ] Root causes identified
- [ ] PLAN file updated with findings

**After Phase 2:**

- [ ] Plan follows codebase patterns
- [ ] PLAN file has detailed implementation steps
- [ ] User has approved the plan

**After Phase 3:**

- [ ] All tasks completed
- [ ] Type-check passes
- [ ] PLAN file marked as complete

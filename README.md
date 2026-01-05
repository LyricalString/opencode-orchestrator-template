# OpenCode Orchestrator Template

A powerful multi-agent orchestration system for AI coding assistants. This template provides a structured workflow for handling complex, multi-phase tasks with parallel agent coordination and persistent progress tracking.

## What is This?

This is a template for setting up an **orchestrated AI agent workflow** in your monorepo. It's designed for projects where:

- You have multiple apps/domains (web, mobile, admin, database)
- Complex tasks require investigation before implementation
- You want persistent progress tracking (PLAN files)
- You need parallel agent execution for speed
- Client feedback needs systematic processing

## The Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    ORCHESTRATOR WORKFLOW                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  /client-feedback "user reports X, Y, Z issues"                 │
│                          │                                      │
│                          ▼                                      │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ PHASE 1: INVESTIGATION (Read-Only)                      │   │
│  │                                                         │   │
│  │  • Create PLAN.md file                                  │   │
│  │  • Launch parallel explore agents                       │   │
│  │  • Identify root causes                                 │   │
│  │  • NO code changes                                      │   │
│  └─────────────────────────────────────────────────────────┘   │
│                          │                                      │
│                          ▼                                      │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ PHASE 2: PLANNING                                       │   │
│  │                                                         │   │
│  │  • Review findings against codebase patterns            │   │
│  │  • Create detailed implementation plan                  │   │
│  │  • Get user approval                                    │   │
│  │  • Update PLAN.md                                       │   │
│  └─────────────────────────────────────────────────────────┘   │
│                          │                                      │
│                          ▼                                      │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │ PHASE 3: IMPLEMENTATION                                 │   │
│  │                                                         │   │
│  │  • Launch app-specific agents (parallel when possible)  │   │
│  │  • Update PLAN.md progress                              │   │
│  │  • Run type-check after all complete                    │   │
│  │  • Mark PLAN.md as complete                             │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Quick Start

### 1. Copy the Template

Copy the `.opencode/` folder and `AGENTS.md` to your project root:

```bash
cp -r opencode-orchestrator-template/.opencode your-project/
cp opencode-orchestrator-template/AGENTS.md your-project/
```

### 2. Customize for Your Project

Edit the files to match your project structure:

- **AGENTS.md**: Update with your tech stack, commands, and patterns
- **`.opencode/agent/*.md`**: Create agents for your apps/domains
- **`.opencode/skill/orchestrator-workflow/SKILL.md`**: Adjust the workflow if needed

### 3. Use the Commands

```bash
# Full workflow: investigate → plan → implement
/client-feedback "paste feedback here"

# Individual phases
/investigate "task description"    # Phase 1 only
/plan-fix                          # Phase 2 only
/implement                         # Phase 3 only

# Resume interrupted work
/continue-plan PLAN_FILE.md

# Agent management
/generate-agent apps/new-app       # Create new agent from directory
/update-agent web-app              # Update existing agent definition
```

## File Structure

```
your-project/
├── AGENTS.md                          # Main guidelines for AI agents
└── .opencode/
    ├── agent/
    │   ├── orchestrator.md            # Main orchestrator agent
    │   ├── web-app.md                 # Example: Web app specialist
    │   ├── mobile-app.md              # Example: Mobile app specialist
    │   ├── admin-panel.md             # Example: Admin panel specialist
    │   └── database.md                # Example: Database specialist
    ├── command/
    │   ├── client-feedback.md         # Full workflow command
    │   ├── investigate.md             # Phase 1 command
    │   ├── plan-fix.md                # Phase 2 command
    │   ├── implement.md               # Phase 3 command
    │   ├── continue-plan.md           # Resume command
    │   ├── generate-agent.md          # Create new agent from directory
    │   └── update-agent.md            # Update existing agent definition
    └── skill/
        └── orchestrator-workflow/
            └── SKILL.md               # Detailed workflow protocol
```

## The PLAN File

The PLAN file is the **single source of truth** for complex tasks. It enables:

- **Persistence**: Leave and return anytime
- **Visibility**: User always knows current status
- **Coordination**: Multiple agents reference the same plan
- **History**: Progress log tracks all actions

Example PLAN file structure:

```markdown
# Feature: User Authentication Refactor

**Created:** 2024-01-15
**Status:** Implementation
**Last Updated:** 2024-01-15 14:30

---

## Overview

Refactor authentication to use new provider...

## Issues Identified

| #   | Issue                | Status      | Complexity |
| --- | -------------------- | ----------- | ---------- |
| 1   | Token refresh broken | Done        | Medium     |
| 2   | Session persistence  | In Progress | High       |

## Implementation Plan

### Phase 1: Token Refresh

- [x] Update token service
- [x] Add refresh interceptor

### Phase 2: Session Persistence

- [ ] Implement secure storage
- [ ] Add session recovery

## Progress Log

- 14:00 - Started Phase 1
- 14:30 - Completed token refresh fix
```

## Parallel vs Sequential Execution

### Use Parallel Agents When:

- Different apps need changes (web + mobile + admin)
- Tasks touch different files
- No data dependencies between tasks

### Use Sequential Agents When:

- Database migration must exist before app code
- Shared package changes before apps consuming them
- API changes in one app before another app calling it

## Customization Guide

### Adding a New App Agent

Create `.opencode/agent/your-app.md`:

```markdown
---
description: Your App specialist - brief description
permission:
  edit: allow
  bash:
    'bun run type-check': allow
    'cd apps/your-app && bun *': allow
    '*': ask
---

# Your App Agent

You are a specialist for **Your App** (`apps/your-app/`).

## Scope

- **Primary**: `apps/your-app/`
- **Shared**: `packages/` (when needed)
- **Cross-app**: ASK before touching other apps

## Tech Stack

| Technology | Version | Purpose |
| ---------- | ------- | ------- |
| ...        | ...     | ...     |

## Key Patterns

...

## Commands

...
```

### Modifying the Workflow

Edit `.opencode/skill/orchestrator-workflow/SKILL.md` to:

- Add/remove phases
- Change quality gates
- Modify agent coordination rules

## Agent Maintenance

Agent definitions must stay in sync with your codebase. The system includes built-in maintenance features:

### Auto-Update Reminders

Each agent file includes a checklist of triggers that should prompt an update:

- Package version changes
- Directory structure modifications
- New patterns introduced
- Key files added/removed

### Generate New Agents

When you add a new app or package:

```bash
/generate-agent apps/my-new-app
```

This launches parallel investigation agents to:

1. Analyze `package.json` for tech stack
2. Map directory structure
3. Identify patterns (auth, data fetching, forms)
4. Detect integrations and env vars

Then generates a complete agent definition.

### Update Existing Agents

After significant changes to an app:

```bash
/update-agent web-app
```

This compares the current codebase against the documented agent and updates any outdated information.

## Best Practices

1. **Always create a PLAN file** for complex tasks
2. **Update the PLAN file** after each major action
3. **Get user approval** before Phase 3 implementation
4. **Run type-check** only after ALL agents complete
5. **Use parallel agents** when tasks are independent
6. **Use sequential agents** when there are dependencies
7. **Update agent definitions** after significant architectural changes
8. **Run `/update-agent`** periodically to keep docs in sync

## Integration with Other Tools

This template works with:

- **OpenCode**: Native support via `.opencode/` folder
- **Claude Code**: Can be adapted for Claude's agent system
- **Cursor**: Can be adapted for Cursor's rules system

## License

MIT - Feel free to use, modify, and share!

## Credits

Created by [Alex Martinez](https://github.com/LyricalString) based on real-world experience orchestrating AI agents in production monorepos.

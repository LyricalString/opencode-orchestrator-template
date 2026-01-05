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

---

## Getting Started

### For New Projects

1. **Copy the template files** to your project root:

```bash
# Clone or download this repo
git clone https://github.com/LyricalString/opencode-orchestrator-template.git

# Copy to your project
cp -r opencode-orchestrator-template/.opencode your-project/
cp opencode-orchestrator-template/AGENTS.md your-project/
```

2. **Update AGENTS.md** with your project specifics:
   - Package manager commands (bun, npm, pnpm)
   - Your tech stack and conventions
   - Import ordering rules
   - Any project-specific patterns

3. **Generate agents for your apps**:

```bash
# In your AI assistant, run:
/generate-agent apps/your-web-app
/generate-agent apps/your-mobile-app
/generate-agent packages/your-shared-package
```

4. **Update the orchestrator** (`.opencode/agent/orchestrator.md`):
   - Add your agents to the routing table
   - Add detection keywords for each app

5. **Start using the workflow**:

```bash
/client-feedback "user reports login is broken on mobile"
```

### For Existing Projects

If you already have a codebase and want to add orchestration:

1. **Copy the template** (same as above)

2. **Audit your project structure**:
   - List all apps: `ls apps/` or `ls packages/`
   - Identify which need dedicated agents

3. **Generate agents for each app/package**:

```bash
# The generate command will analyze and create agent definitions
/generate-agent apps/frontend
/generate-agent apps/backend
/generate-agent apps/admin
/generate-agent packages/shared-utils
```

4. **Review and refine generated agents**:
   - Check that patterns are correctly identified
   - Add any missing conventions
   - Remove any incorrect assumptions

5. **Customize AGENTS.md**:
   - Copy patterns from your existing documentation
   - Add your linting/testing commands
   - Document your authentication patterns

6. **Test with a small task**:

```bash
/investigate "understand how authentication works in this project"
```

### Minimal Setup (Quick Start)

If you want the fastest possible setup:

1. Copy `.opencode/` and `AGENTS.md`
2. Delete the example agents (`web-app.md`, `mobile-app.md`, etc.)
3. Keep only `orchestrator.md` and `database.md`
4. Run `/generate-agent` for each of your apps
5. Start using `/client-feedback`

---

## Commands Reference

### Workflow Commands

| Command                  | Description              | When to Use                     |
| ------------------------ | ------------------------ | ------------------------------- |
| `/client-feedback "..."` | Full 3-phase workflow    | Processing user/client feedback |
| `/investigate "..."`     | Phase 1 only (read-only) | Understanding a problem         |
| `/plan-fix`              | Phase 2 only             | Creating implementation plan    |
| `/implement`             | Phase 3 only             | Executing approved plan         |
| `/continue-plan FILE.md` | Resume from PLAN file    | Returning to interrupted work   |

### Agent Management Commands

| Command                       | Description                 | When to Use               |
| ----------------------------- | --------------------------- | ------------------------- |
| `/generate-agent path/to/app` | Create new agent definition | Adding a new app/package  |
| `/update-agent agent-name`    | Sync agent with codebase    | After significant changes |

---

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

---

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

---

## Parallel vs Sequential Execution

### Use Parallel Agents When:

- Different apps need changes (web + mobile + admin)
- Tasks touch different files
- No data dependencies between tasks

### Use Sequential Agents When:

- Database migration must exist before app code
- Shared package changes before apps consuming them
- API changes in one app before another app calling it

---

## Agent Design Philosophy

### What Goes IN an Agent Definition

- **Scope**: What directories/files this agent owns
- **Patterns**: How to do things (auth, data fetching, forms)
- **Commands**: How to run tests, build, lint
- **Key files**: Important entry points and configs
- **Conventions**: Naming, structure, imports

### What Stays OUT of Agent Definitions

- **Specific data**: Table schemas, API endpoints, env values
- **Volatile information**: Version numbers that change often
- **Large lists**: Every file in a directory

Instead, teach agents **where to find** information:

```markdown
## Where to Find Schema Information

| Information Needed | Where to Look               |
| ------------------ | --------------------------- |
| Table definitions  | `supabase/migrations/*.sql` |
| API endpoints      | `apps/api/src/routes/`      |
| Environment vars   | `.env.example`              |
```

This approach scales to large codebases without constant agent updates.

---

## Customization Guide

### Adding a New App Agent

Option 1: **Generate automatically**

```bash
/generate-agent apps/your-app
```

Option 2: **Create manually** at `.opencode/agent/your-app.md`:

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

---

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

---

## Best Practices

1. **Always create a PLAN file** for complex tasks
2. **Update the PLAN file** after each major action
3. **Get user approval** before Phase 3 implementation
4. **Run type-check** only after ALL agents complete
5. **Use parallel agents** when tasks are independent
6. **Use sequential agents** when there are dependencies
7. **Update agent definitions** after significant architectural changes
8. **Run `/update-agent`** periodically to keep docs in sync
9. **Keep agents focused on patterns**, not specific data
10. **Teach agents where to look**, not what they'll find

---

## Integration with Other Tools

This template works with:

- **OpenCode**: Native support via `.opencode/` folder
- **Claude Code**: Can be adapted for Claude's agent system (use AGENTS.md + custom instructions)
- **Cursor**: Can be adapted for Cursor's rules system (.cursorrules)

---

## Troubleshooting

### "Agent doesn't know about my new feature"

Run `/update-agent agent-name` to sync the agent with current codebase.

### "Parallel agents are conflicting"

Check if tasks have dependencies. Use sequential execution for:

- Database changes before app code
- Shared package changes before consuming apps

### "PLAN file is out of sync"

The orchestrator should update it automatically. If not, manually update the Progress Log section.

### "Agent is making changes outside its scope"

Check the agent's Scope section. Add explicit "ASK before touching" rules.

---

## License

MIT - Feel free to use, modify, and share!

## Credits

Created by [Alex Martinez](https://github.com/LyricalString) based on real-world experience orchestrating AI agents in production monorepos.

## Contributing

Found a bug or have an improvement? Open an issue or PR on [GitHub](https://github.com/LyricalString/opencode-orchestrator-template).

# OpenCode Orchestrator Template

A powerful multi-agent orchestration system for AI coding assistants. Handle complex tasks with parallel agent coordination, persistent PLAN file tracking, and app-specific specialists.

---

## Quick Install

> **Requires**: [OpenCode](https://opencode.ai) installed in your terminal

**From your project's root directory**, copy and run:

```bash
opencode run "Fetch https://raw.githubusercontent.com/LyricalString/opencode-orchestrator-template/main/SETUP_PROMPT.md and follow those instructions to set up the orchestrator in my project"
```

That's it. The AI will:

1. Check your git status (offers to stash uncommitted changes)
2. Download and copy the template files
3. Analyze your project structure
4. Show you what it found and ask for confirmation
5. Generate customized agents for your apps
6. Remind you to restart your terminal

---

## What is This?

An orchestrated workflow for AI coding assistants, designed for monorepos with multiple apps:

```
/client-feedback "user reports login is broken on mobile"
                    │
                    ▼
    ┌───────────────────────────────┐
    │  PHASE 1: INVESTIGATION       │
    │  • Parallel explore agents    │
    │  • Create PLAN.md             │
    │  • NO code changes            │
    └───────────────────────────────┘
                    │
                    ▼
    ┌───────────────────────────────┐
    │  PHASE 2: PLANNING            │
    │  • Review findings            │
    │  • Create implementation plan │
    │  • Get user approval          │
    └───────────────────────────────┘
                    │
                    ▼
    ┌───────────────────────────────┐
    │  PHASE 3: IMPLEMENTATION      │
    │  • Launch app-specific agents │
    │  • Parallel when possible     │
    │  • Type-check at the end      │
    └───────────────────────────────┘
```

---

## Commands

| Command                  | Description              |
| ------------------------ | ------------------------ |
| `/client-feedback "..."` | Full 3-phase workflow    |
| `/investigate "..."`     | Phase 1 only (read-only) |
| `/plan-fix`              | Phase 2 only             |
| `/implement`             | Phase 3 only             |
| `/continue-plan FILE.md` | Resume from PLAN file    |
| `/generate-agent path/`  | Create agent for new app |
| `/update-agent name`     | Update existing agent    |

---

## How It Works

### Orchestrator

Routes tasks to the right app-specific agents, coordinates parallel execution, maintains the PLAN file.

### App Agents

Each app gets a specialist agent that knows its tech stack, patterns, and conventions.

### PLAN File

Tracks complex tasks across sessions. Leave and return anytime with `/continue-plan`.

---

## Manual Installation

If you prefer to set things up yourself:

```bash
# Clone and copy
git clone https://github.com/LyricalString/opencode-orchestrator-template.git /tmp/orc-template
cp -r /tmp/orc-template/.opencode ./
cp /tmp/orc-template/AGENTS.md ./
rm -rf /tmp/orc-template

# Customize AGENTS.md with your conventions

# Generate agents for your apps
opencode "/generate-agent apps/your-app"

# Update orchestrator routing table
# Edit .opencode/agent/orchestrator.md

# Delete example agents you don't need
rm .opencode/agent/web-app.md
rm .opencode/agent/mobile-app.md
rm .opencode/agent/admin-panel.md
```

---

## After Installation

```bash
# Restart terminal (required)
source ~/.zshrc

# Test it
opencode "/investigate 'overview of this project'"
```

---

## License

MIT

## Credits

Created by [Alex Martinez](https://github.com/LyricalString)

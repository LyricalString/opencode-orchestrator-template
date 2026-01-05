# OpenCode Orchestrator Template

A powerful multi-agent orchestration system for AI coding assistants. Handle complex tasks with parallel agent coordination, persistent PLAN file tracking, and app-specific specialists.

---

## Quick Install

> **Requires**: [OpenCode](https://opencode.ai) installed in your terminal

### Step 1: Open OpenCode in your project

```bash
cd your-project
opencode
```

### Step 2: Send this message

Copy and paste this into OpenCode:

```
Fetch https://raw.githubusercontent.com/LyricalString/opencode-orchestrator-template/main/SETUP_PROMPT.md and follow those instructions to set up the orchestrator in my project
```

### Step 3: Follow the prompts

The AI will guide you through a multi-phase setup:

#### Phase 1: Pre-flight

- Check git status (offers to stash uncommitted changes)
- Download and copy template files

#### Phase 2: Documentation Discovery

- Scan for README, CONTRIBUTING, ARCHITECTURE, existing AGENTS.md
- Present findings and ask for any additional context

#### Phase 3: Project Analysis (4 parallel agents)

- **Stack Agent**: Detect monorepo tools, languages, frameworks, package manager
- **Apps Agent**: Find all apps/packages with their purpose
- **Services Agent**: Detect database, auth, external integrations
- **Quality Agent**: Find test frameworks, linters, CI/CD

#### Phase 4: AGENTS.md

- If you have one: Propose merge with orchestrator additions
- If not: Generate one based on your conventions

#### Phase 5: Agent Generation

- Update orchestrator routing table
- Generate specialized agent for each app (in parallel)
- Clean up unused template agents

#### Phase 6: Summary

- Show what was created
- Test command to verify setup

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

After installation, these commands are available:

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

> **Design principle**: Agents capture **patterns over specifics** - they document directory structures, coding conventions, and integration types, but NOT version numbers, function names, or config values. This prevents hallucination and keeps agents maintainable.

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

# Generate agents for your apps (inside OpenCode)
/generate-agent apps/your-app

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

# Open OpenCode and test it
opencode
```

Then send:

```
/investigate "overview of this project"
```

---

## License

MIT

## Credits

Created by [Alex Martinez](https://github.com/LyricalString)

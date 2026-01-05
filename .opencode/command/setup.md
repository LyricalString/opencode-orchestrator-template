---
description: Automatically set up the orchestrator system in the current project
subtask: false
---

# Orchestrator Setup Wizard

You are setting up the OpenCode Orchestrator system in this project. Follow this process carefully.

## Pre-Flight Checks

### Step 1: Verify Git Status

First, check if the working directory is clean:

```bash
git status --porcelain
```

**If there are uncommitted changes:**

1. List the changes to the user
2. Ask: "You have uncommitted changes. Would you like me to stash them before proceeding? (I'll remind you to unstash after setup)"
3. If yes: `git stash push -m "Pre-orchestrator-setup stash"`
4. If no: Stop and ask user to commit or stash manually

**If clean:** Proceed to Step 2.

### Step 2: Analyze Project Structure

Launch parallel agents to understand the project:

**Agent 1 - Root Analysis:**

- Check for monorepo indicators (turbo.json, nx.json, lerna.json, pnpm-workspace.yaml)
- Read root package.json for workspaces config
- Identify package manager (bun.lockb, pnpm-lock.yaml, yarn.lock, package-lock.json)
- Check for existing AGENTS.md or .opencode/ folder

**Agent 2 - Apps Discovery:**

- Look for apps/, packages/, src/ directories
- For each app found, identify:
  - Framework (Next.js, Expo, Express, etc.)
  - Key dependencies
  - Purpose (from package.json description or README)

**Agent 3 - Database Discovery:**

- Check for supabase/, prisma/, drizzle/ folders
- Identify database type and ORM
- Note migration patterns

**Agent 4 - Patterns Discovery:**

- Look for authentication setup (Clerk, Auth0, NextAuth)
- Identify state management (Zustand, Redux, Jotai)
- Find testing setup (Jest, Vitest, Playwright)
- Check CI/CD config (.github/workflows, vercel.json)

### Step 3: Generate Configuration

Based on findings, create:

1. **AGENTS.md** - Customized for this project:
   - Correct package manager commands
   - Actual workspace package names
   - Real authentication patterns found
   - Actual test/lint/build commands from package.json

2. **Orchestrator Agent** - Updated with:
   - Real app names and paths
   - Correct detection keywords
   - Actual routing table

3. **App-Specific Agents** - One for each app/package found:
   - Use `/generate-agent` logic for each
   - Include real tech stack versions
   - Document actual patterns found

4. **Database Agent** (if applicable):
   - Configured for actual database setup
   - Correct migration commands
   - Real schema discovery paths

### Step 4: Create Files

Create the `.opencode/` directory structure:

```
.opencode/
├── agent/
│   ├── orchestrator.md
│   ├── [app-1].md
│   ├── [app-2].md
│   └── database.md (if applicable)
├── command/
│   ├── client-feedback.md
│   ├── investigate.md
│   ├── plan-fix.md
│   ├── implement.md
│   ├── continue-plan.md
│   ├── generate-agent.md
│   └── update-agent.md
└── skill/
    └── orchestrator-workflow/
        └── SKILL.md
```

### Step 5: Summary & Next Steps

Present to the user:

```markdown
## Setup Complete!

### What was created:

- `AGENTS.md` - Main guidelines for AI agents
- `.opencode/agent/` - X agent definitions:
  - orchestrator.md
  - [list each agent created]
- `.opencode/command/` - 7 workflow commands
- `.opencode/skill/` - Orchestrator workflow protocol

### Detected Configuration:

| Setting         | Value                  |
| --------------- | ---------------------- |
| Package Manager | [bun/pnpm/npm/yarn]    |
| Monorepo        | [Yes/No]               |
| Apps Found      | [count]                |
| Database        | [Supabase/Prisma/None] |
| Auth            | [Clerk/Auth0/None]     |

### Next Steps:

1. **Restart your terminal** (or run `source ~/.zshrc`) to reload OpenCode
2. Review the generated agents in `.opencode/agent/`
3. Try your first command:
```

/investigate "understand how authentication works in this project"

```

### Quick Commands:

- `/client-feedback "..."` - Process feedback with full workflow
- `/investigate "..."` - Investigate an issue (read-only)
- `/generate-agent path/` - Create agent for new app
- `/update-agent name` - Update existing agent

[If stash was created]:
### Don't forget!
You have stashed changes. Run `git stash pop` to restore them.
```

---

## Important Notes

- If `.opencode/` already exists, ask user before overwriting
- If AGENTS.md exists, ask to merge or replace
- Always preserve any custom commands/agents the user may have added
- Be conservative with assumptions - ask if unclear

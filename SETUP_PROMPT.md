# OpenCode Orchestrator - Setup Instructions

You are setting up the OpenCode Orchestrator system in the user's current project. Follow these steps carefully and in order.

---

## Phase 1: Pre-flight Checks

### Step 1.1: Verify Git Status

Run:

```bash
git status --porcelain
```

**If there are uncommitted changes (output is not empty):**

Tell the user:

```
⚠️ You have uncommitted changes:

[list the changed files]

I recommend stashing these before proceeding. Options:
1. Stash changes (recommended) - I'll remind you to unstash after
2. Continue anyway
3. Stop - you handle it manually

Which option? (1/2/3)
```

- If 1: Run `git stash push -m "Pre-orchestrator-setup"` and remember `STASH_CREATED=true`
- If 2: Continue with `STASH_CREATED=false`
- If 3: Stop and wait for user

**If clean:** Continue with `STASH_CREATED=false`

---

## Phase 2: Download Template

### Step 2.1: Clone and Copy

Run these commands:

```bash
git clone https://github.com/LyricalString/opencode-orchestrator-template.git /tmp/orc-template
cp -r /tmp/orc-template/.opencode ./
cp /tmp/orc-template/AGENTS.md ./
rm -rf /tmp/orc-template
```

### Step 2.2: Verify Copy

```bash
ls .opencode/agent/ .opencode/command/ AGENTS.md
```

Confirm files exist before proceeding.

---

## Phase 3: Analyze Project

Tell the user:

```
🔍 Analyzing your project structure...
```

### Step 3.1: Launch Parallel Analysis

Launch 4 parallel investigation agents:

**Agent 1 - Root & Config:**

- Check for monorepo: turbo.json, nx.json, lerna.json, pnpm-workspace.yaml, package.json "workspaces"
- Identify package manager: bun.lockb → bun, pnpm-lock.yaml → pnpm, yarn.lock → yarn, package-lock.json → npm
- Read root package.json: scripts (test, lint, build, type-check), workspaces patterns
- Return: { package_manager, is_monorepo, monorepo_tool, scripts, workspace_scope }

**Agent 2 - Apps Discovery:**

- Scan: apps/_, packages/_, services/\*, src/
- For each directory with package.json, extract:
  - path, name, framework (from deps: next→Next.js, expo→Expo, express→Express, etc.)
  - purpose (from description or infer from name)
- Return: array of { path, name, framework, purpose }

**Agent 3 - Database:**

- Check folders: supabase/, prisma/, drizzle/
- Check deps: @supabase/supabase-js, prisma, drizzle-orm
- Return: { type: "supabase"|"prisma"|"drizzle"|null, migrations_path, config_file }

**Agent 4 - Patterns:**

- Auth: @clerk/_, next-auth, @auth0/_, @supabase/auth-helpers
- State: zustand, @reduxjs/toolkit, jotai, @tanstack/react-query
- Styling: tailwindcss, styled-components, @emotion/\*
- Testing: jest, vitest, @playwright/test
- Return: { auth, state_management, styling, testing }

### Step 3.2: Present Findings

Show the user:

```
## 📊 Project Analysis Complete

| Category | Detected |
|----------|----------|
| Package Manager | [bun/pnpm/npm/yarn] |
| Monorepo | [Yes (Turborepo) / No] |
| Apps Found | [count] |
| Database | [Supabase/Prisma/Drizzle/None] |
| Auth | [Clerk/NextAuth/Auth0/None] |
| State | [React Query + Zustand / etc.] |
| Testing | [Jest/Vitest/None] |

### Apps/Packages Detected:

| Name | Path | Framework |
|------|------|-----------|
[list each app]

Does this look correct? (yes/no/let me correct something)
```

**Wait for user confirmation before proceeding.**

If user says something is wrong, ask for corrections and update your understanding.

---

## Phase 4: Customize Template

### Step 4.1: Update AGENTS.md

Read the current AGENTS.md and update:

1. **Package manager**: Replace all `bun` with detected package manager if different
2. **Workspace scope**: Replace `@myapp/*` with actual scope (e.g., `@acme/*`)
3. **Commands**: Update test/lint/build/type-check with actual commands from package.json
4. **Auth patterns**: Replace example auth code with patterns matching detected auth provider

### Step 4.2: Update Orchestrator

Edit `.opencode/agent/orchestrator.md`:

1. **Replace the routing table** with actual apps:

```markdown
| Agent        | Domain    | Use For                      |
| ------------ | --------- | ---------------------------- |
| `[app-name]` | `[path]/` | [purpose based on framework] |
```

2. **Update detection keywords** based on actual app names and purposes

### Step 4.3: Generate App Agents

For EACH app discovered:

1. Create `.opencode/agent/[app-name].md` with:
   - Correct description based on framework
   - Actual path in scope
   - Tech stack from package.json dependencies (with versions)
   - Directory structure (actually scan it)
   - Patterns found in that specific app
   - Commands from that app's package.json

2. Use this structure:

````markdown
---
description: [App name] [framework] specialist - [brief purpose]
permission:
  edit: allow
  bash:
    '[pkg-manager] run type-check': allow
    '[pkg-manager] test*': allow
    'cd [path] && [pkg-manager] *': allow
    '*': ask
---

# [App Name] Agent

You are a specialist for **[App Name]** (`[path]/`). [Purpose description].

## Scope

- **Primary**: `[path]/`
- **Shared**: `packages/` (when changes needed for this app)
- **Cross-app**: ASK before touching other apps

## Tech Stack

| Technology | Version | Purpose |
| ---------- | ------- | ------- |

[from package.json]

## Directory Structure

[actual structure from scanning]

## Key Patterns

[patterns found in this app]

## Commands

```bash
cd [path]
[pkg-manager] dev
[pkg-manager] build
[pkg-manager] test
[pkg-manager] lint
```
````

## What This App Owns

[infer from purpose]

---

## Keeping This Agent Updated

Update this file when:

- [ ] Dependencies change
- [ ] Directory structure changes
- [ ] New patterns introduced

````

### Step 4.4: Handle Database Agent

**If database detected:**
- Update `.opencode/agent/database.md` with correct type (Supabase/Prisma/Drizzle)
- Update migration paths and commands

**If NO database:**
- Delete `.opencode/agent/database.md`

### Step 4.5: Clean Up Example Agents

Delete example agents that don't match real apps:
```bash
# Only delete if no matching app exists
rm .opencode/agent/web-app.md      # if no Next.js web app
rm .opencode/agent/mobile-app.md   # if no Expo app
rm .opencode/agent/admin-panel.md  # if no admin app
````

---

## Phase 5: Summary

Present to the user:

````
## ✅ Setup Complete!

### Files Created/Modified:

**AGENTS.md** - Updated with:
- Package manager: [detected]
- Workspace scope: [detected]
- Your commands and conventions

**Agents Generated:**
- `.opencode/agent/orchestrator.md` - Updated routing table
[list each app agent created]
[database.md if applicable]

**Commands Available:**
- `/client-feedback "..."` - Full 3-phase workflow
- `/investigate "..."` - Read-only investigation
- `/plan-fix` - Create implementation plan
- `/implement` - Execute the plan
- `/continue-plan FILE.md` - Resume interrupted work
- `/generate-agent path/` - Add new app agent
- `/update-agent name` - Update existing agent

---

### ⚡ Next Steps:

1. **Restart your terminal** (required):
   ```bash
   source ~/.zshrc  # or source ~/.bashrc
````

2. **Test the setup:**

   ```bash
   opencode "/investigate 'give me an overview of this project'"
   ```

3. **Try the full workflow:**
   ```bash
   opencode "/client-feedback 'describe an issue or feature'"
   ```

## [If STASH_CREATED is true:]

### ⚠️ Don't Forget!

You have stashed changes. Restore them with:

```bash
git stash pop
```

```

---

## Error Handling

- If git clone fails: Check internet connection, try again
- If analysis fails: Report which part failed, offer to retry or continue with partial info
- If user says analysis is wrong: Ask for corrections, update before generating
- If file write fails: Report error, don't leave partial state

---

## Important Notes

- Always ask for confirmation before making changes
- Be thorough in analysis - don't assume, discover
- Generate agents based on ACTUAL project structure, not examples
- Keep the orchestrator workflow and skill files unchanged - they're generic
- Only customize: AGENTS.md, orchestrator.md routing table, and app-specific agents
```

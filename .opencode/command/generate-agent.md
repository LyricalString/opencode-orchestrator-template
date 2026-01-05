---
description: Generate a new agent definition by analyzing a directory (app or package)
subtask: false
---

# Generate Agent Definition

Analyze the specified directory and generate a comprehensive agent definition file.

## Target Directory

$ARGUMENTS

---

## Instructions

You are creating a new agent definition for the specified directory. Follow this process:

### Phase 1: Deep Analysis (Parallel Investigation)

Launch parallel explore agents to gather information:

1. **Package Analysis Agent**:
   - Read `package.json` to extract:
     - Name, description
     - All dependencies with versions
     - Scripts available
     - Workspace dependencies (`@myapp/*`)
   - Identify the tech stack (Next.js, Expo, React, etc.)

2. **Structure Analysis Agent**:
   - Map the complete directory structure
   - Identify key directories and their purposes
   - Find important files (config files, entry points, etc.)
   - Note any README files that exist

3. **Pattern Analysis Agent**:
   - Look for authentication patterns (Clerk, Supabase, etc.)
   - Identify data fetching patterns (React Query, SWR, fetch)
   - Find state management (Zustand, Context, Redux)
   - Detect form handling (react-hook-form, Formik)
   - Note API route patterns
   - Find component patterns

4. **Integration Analysis Agent**:
   - Identify external service integrations (Stripe, etc.)
   - Find environment variables used
   - Note webhook handlers
   - Identify shared package usage

### Phase 2: Synthesize Findings

Combine all findings into a structured agent definition following this template:

```markdown
---
description: [One-line description of what this agent specializes in]
permission:
  edit: allow
  bash:
    'bun run type-check': allow
    'bun test*': allow
    'cd [directory] && bun *': allow
    '*': ask
---

# [Agent Name] Agent

You are a specialist for **[App/Package Name]** (`[directory]/`). [Brief description of purpose].

## Scope

- **Primary**: `[directory]/`
- **Shared**: `packages/` (when changes are needed for this app's features)
- **Cross-app**: ASK before touching other apps or `supabase/`

## Tech Stack

| Technology | Version | Purpose |
| ---------- | ------- | ------- |
| ...        | ...     | ...     |

## Directory Structure

\`\`\`
[directory]/
├── ...
\`\`\`

## Key Patterns

### [Pattern Category 1]

[Code examples and explanations]

### [Pattern Category 2]

[Code examples and explanations]

## Shared Packages

| Package | Usage |
| ------- | ----- |
| ...     | ...   |

## Commands

\`\`\`bash
cd [directory]
[available commands]
\`\`\`

## Environment Variables

\`\`\`bash
[list of env vars used]
\`\`\`

## Important Files

| File | Purpose |
| ---- | ------- |
| ...  | ...     |

## What This [App/Package] Owns

- [Responsibility 1]
- [Responsibility 2]
- ...
```

### Phase 3: Write the Agent File

1. Generate the agent definition file at `.opencode/agent/[name].md`
2. Use kebab-case for the filename (e.g., `my-new-app.md`)
3. Ensure all sections are filled with accurate information
4. Include real code examples from the codebase

### Phase 4: Update Orchestrator

After creating the agent, remind the user to:

1. Add the new agent to the orchestrator's agent table in `.opencode/agent/orchestrator.md`
2. Add detection keywords for the new agent
3. Update AGENTS.md if new patterns were discovered

---

## Output

When complete, provide:

1. The path to the new agent file
2. A summary of what was discovered
3. Any recommendations for the user (missing docs, inconsistent patterns, etc.)
4. Instructions for registering the agent with the orchestrator

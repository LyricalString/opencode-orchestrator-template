---
description: Update an existing agent definition to reflect current codebase state
subtask: false
---

# Update Agent Definition

Re-analyze the codebase and update the specified agent definition to reflect current state.

## Agent to Update

$ARGUMENTS

---

## Instructions

You are updating an existing agent definition to ensure it accurately reflects the current state of the codebase.

### Phase 1: Read Current Agent Definition

1. Read the existing agent file at `.opencode/agent/[agent-name].md`
2. Note the current documented:
   - Tech stack versions
   - Directory structure
   - Patterns
   - Key files
   - Shared packages

### Phase 2: Analyze Current Codebase (Parallel)

Launch parallel agents to check for changes:

1. **Version Check Agent**:
   - Read `package.json` in the agent's directory
   - Compare versions with what's documented
   - Note any new or removed dependencies

2. **Structure Check Agent**:
   - Map current directory structure
   - Compare with documented structure
   - Identify new, moved, or deleted directories/files

3. **Pattern Check Agent**:
   - Look for new patterns not documented
   - Check if documented patterns still exist
   - Identify deprecated patterns

4. **Integration Check Agent**:
   - Check for new environment variables
   - Look for new shared package usage
   - Identify new external integrations

### Phase 3: Generate Diff Report

Create a summary of what changed:

```markdown
## Agent Update Report: [agent-name]

### Version Changes

| Package | Old Version | New Version |
| ------- | ----------- | ----------- |
| ...     | ...         | ...         |

### Structure Changes

- Added: [list]
- Removed: [list]
- Moved: [list]

### New Patterns Detected

- [pattern description]

### Deprecated Patterns

- [pattern description]

### New Integrations

- [integration description]
```

### Phase 4: Update the Agent File

1. Update all outdated sections in the agent definition
2. Add new patterns with code examples
3. Remove deprecated information
4. Update the tech stack table
5. Refresh the directory structure diagram
6. Update the "Important Files" table

### Phase 5: Verify Consistency

After updating, ensure:

- [ ] All version numbers match `package.json`
- [ ] Directory structure matches reality
- [ ] Code examples are from actual codebase
- [ ] No references to deleted files/patterns

---

## Output

When complete, provide:

1. Summary of changes made to the agent definition
2. Any patterns that need human review
3. Recommendations for codebase improvements (if any)

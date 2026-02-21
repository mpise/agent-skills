---
name: aor-memory-sync
description: >
  Automates real-time memory persistence for the current workspace.
  Automatically triggered when new types, patterns, architectural decisions, or task completions occur.
  Use PROACTIVELY during execution, verification, and task completion phases.
license: MIT
---

# Workspace Memory Sync

Enforces the mandatory memory context rules from `.agent/rules/rules.md`. This skill ensures that project memory is updated **IMMEDIATELY** — never queued for "later".

> **CRITICAL**: Do not queue memory updates for "later". If you discover a pattern, write it to disk now. This ensures context is verified and saved even if the session crashes.

## Dynamic App-Name Resolution (CRITICAL)

The `[app-name]` placeholder MUST be resolved dynamically:
1.  **Scan `.agent/`**: Look for subdirectories containing `plans/`, `dds/`, and `memory/`.
2.  **Identify**: Identify the active project directory (e.g., `[app-name]`).
3.  **Reference**: Use this name for all memory paths (e.g., `.agent/[app-name]/memory/entities.md`).

## When This Skill Triggers (Automatic)

| Event | Action | File to Update |
|-------|--------|----------------|
| **Execution Phase** — new types defined | Append to `entities.md` | `.agent/[app-name]/memory/entities.md` |
| **Verification Phase** — new patterns discovered | Append to `patterns.md` | `.agent/[app-name]/memory/patterns.md` |
| **Architectural Choice** made | Append to `decisions.md` | `.agent/[app-name]/memory/decisions.md` |
| **Task Completion** | Create/append session summary | `.agent/[app-name]/memory/session_summaries/` |

## Memory File Inventory

All memory files live under `.agent/[app-name]/memory/`:

| File | Purpose | Update Frequency |
|------|---------|------------------|
| `project_context.md` | Tech stack, architecture, key files | On major changes |
| `decisions.md` | Why approaches were chosen | On every architectural choice |
| `patterns.md` | Reusable patterns discovered | On every new pattern |
| `entities.md` | Types, schemas, relationships | On every new type/entity |
| `user_preferences.md` | Style choices, tool preferences | On preference discovery |
| `session_summaries/*.md` | What was accomplished per session | On every task completion |

## 1. Updating `entities.md`

**Trigger**: New TypeScript interfaces, types, Supabase table schemas, or entity relationships are created or modified.

**Format**: Append the new entity at the end of the file:
```markdown
### [EntityName]
- **File**: `path/to/file.ts`
- **Purpose**: Brief description
- **Fields**: Key field names and types
- **Relationships**: Related entities
- **Added**: YYYY-MM-DD
```

## 2. Updating `patterns.md`

**Trigger**: A reusable code pattern is identified during verification or execution.

**Format**: Append the new pattern:
```markdown
### [Pattern Name]
- **Context**: When to use this pattern
- **Implementation**: Brief code snippet or description
- **File Reference**: `path/to/example.ts`
- **Added**: YYYY-MM-DD
```

## 3. Updating `decisions.md`

**Trigger**: An architectural or design decision is made (library choice, approach selection, trade-off evaluation).

**Format**: Append the new decision:
```markdown
### [Decision Title]
- **Date**: YYYY-MM-DD
- **Context**: Why this decision was needed
- **Decision**: What was chosen
- **Rationale**: Why this approach was selected
- **Alternatives Considered**: Other options and why they were rejected
```

## 4. Creating Session Summaries

**Trigger**: Every task completion.

**Location**: `.agent/[app-name]/memory/session_summaries/`

**Naming Convention**:
```
YYYY-MM-DD_summary_[incremental_integer].md
```
- `[incremental_integer]` is globally unique among all session summaries and increments by 1.

**Content Format**:
```markdown
# Session Summary — YYYY-MM-DD

## What Was Done
- Brief list of accomplishments

## Files Changed
- List of key files modified

## Key Decisions
- Any decisions made during the session

## Next Steps
- What remains to be done
```

## 5. Before Starting Any Task

**ALWAYS** read the memory files first:
1. `project_context.md` — understand the tech stack
2. `decisions.md` — avoid re-discussing settled decisions
3. `patterns.md` — reuse established patterns
4. `entities.md` — understand existing types and schemas

---
name: aor-artifact-manager
description: >
  Manages the lifecycle of Implementation Plans and Design Documents (DDS) for the current workspace.
  Automatically triggered when creating plans, transitioning tasks, or completing work.
  Use PROACTIVELY whenever entering PLANNING mode, creating a new feature, fixing multi-file bugs,
  refactoring, integrating services, or making database changes.
license: MIT
---

# Workspace Artifact Manager

Enforces the mandatory artifact management rules from the `.agent/rules/rules.md` file. This skill ensures that implementation plans and design documents are created, named, stored, and updated correctly across different project containers.

## Dynamic App-Name Resolution (CRITICAL)

The `[app-name]` placeholder MUST be resolved dynamically at the start of every task:
1.  **Scan `.agent/`**: Look for subdirectories that contain `plans/`, `dds/`, and `memory/`.
2.  **Identify**: Identify the primary project directory (e.g., `[app-name]` in this workspace).
3.  **Validate**: Ensure the identified directory exists and contains project history.
4.  **Reference**: Use this resolved name for all paths (e.g., `.agent/[app-name]/plans/`).

## When This Skill Triggers (Automatic)

This skill MUST be invoked when **ANY** of the following conditions are met:

| Trigger | Action |
|---------|--------|
| **New feature request** | Create a plan file immediately |
| **Bug fix with 2+ file changes** | Create a plan file immediately |
| **Refactoring task** | Create a plan file immediately |
| **Integration work** (new library, service, API) | Create a plan file immediately |
| **Database changes** (schema, migrations) | Create a plan file immediately |
| **Entering PLANNING mode** | Create a plan file immediately |

## 1. Implementation Plans

### Location
```
.agent/[app-name]/plans/
```
Cluster similar functionality plans in topicwise subfolders (e.g., `ux/`, `billing/`, `account/`).

### Naming Convention
```
YYYY-MM-DD_[feature_name]_plan_[incremental_integer].md
```
- **Example**: `2026-01-17_razorpay_integration_plan_03.md`
- **NEVER** name a plan generically (e.g., "Implementation Plan").
- `[incremental_integer]` must be **globally unique** across all plans for the app and must increment by 1 from the highest existing integer.

### Child Plans
If a child plan is spawned from a parent plan:
```
YYYY-MM-DD_[feature_name]_plan_[phase_goal]_phase[N]_[incremental_integer].md
```
- **Example**: Parent `2026-01-17_global_design_roadmap_plan_19.md` → Child `2026-01-17_global_design_roadmap_plan_india_polish_phase1_20.md`

### Plan Content Requirements
Every plan MUST contain:
- [ ] Checklist of tasks with checkboxes to track completion
- [ ] Goal description
- [ ] Proposed changes with file paths
- [ ] Verification plan

### Before Creating a Plan
**ALWAYS** refer to `.agent/[app-name]/memory/` files first:
- Read `project_context.md` for tech stack context
- Read `decisions.md` to avoid re-discussing settled choices
- Read `patterns.md` for reusable code patterns
- Read `entities.md` for types and schemas

## 2. Design Documents (DDS)

### Location
```
.agent/[app-name]/dds/
```

### Naming Convention
```
[feature_name]_design_[incremental_integer].md
```
- `[incremental_integer]` must be globally unique across all DDS files for the app.
- **Trigger**: Create BEFORE implementation begins.

## 3. Task Transitions

### PLANNING → EXECUTION
When the user says "continue", "implement", "go ahead", "execute", "start", or similar:
1. Save the approved plan to file **IMMEDIATELY**.
2. **DO NOT** wait for session end.
3. **DO NOT** proceed to coding without the saved plan file.
4. Always wait for user approval before proceeding.

### TASK COMPLETION
- Update the plan file to mark completed items: `[ ]` → `[x]`.
- **NEVER DELETE** plans — they are project history.

## 4. Finding the Next Incremental Integer

To determine the next `[incremental_integer]`:
1. List all `.md` files in `.agent/[app-name]/plans/` (recursively).
2. Extract the highest integer suffix from all filenames.
3. Add 1 to get the next value.

Similarly for DDS files in `.agent/[app-name]/dds/`.

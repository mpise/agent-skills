---
name: aor-workspace-engine
description: >
  Consolidated workspace development standards engine. Orchestrates artifact management,
  memory sync, and security enforcement. Use as a quick reference for project conventions,
  directory structure, code patterns, and tech stack. Automatically triggered at the start
  of every task.
license: MIT
---

# Workspace Engine

Consolidated reference for all workspace conventions. This skill ties together the `aor-artifact-manager`, `aor-memory-sync`, `aor-security-guard`, and `aor-thought-process` skills and adds project-level standards.

## Dynamic App-Name Resolution (CRITICAL)

At the start of **EVERY** task, the agent MUST resolve the `[app-name]` placeholder:
1.  **Heuristic**: List directories in `.agent/`. Identify the one containing `plans/`, `dds/`, and `memory/`.
2.  **Resolution**: Identify the primary project container (e.g., `[app-name]`).
3.  **State**: Explicitly mention the resolved project path in the first `task_boundary` call or internal rationale.

## When This Skill Triggers (Automatic)

This skill triggers at the **start of every task** to provide baseline context. It also invokes the four sub-skills:

| Phase | Skills Invoked |
|-------|----------------|
| **Every Response** | `aor-thought-process` (transparent logic) |
| **PLANNING mode entered** | `aor-artifact-manager` (create plan) |
| **EXECUTION — new types** | `aor-memory-sync` (update `entities.md`) |
| **EXECUTION — code generation** | `aor-security-guard` (security scan) |
| **VERIFICATION — new patterns** | `aor-memory-sync` (update `patterns.md`) |
| **TASK COMPLETION** | `aor-artifact-manager` (mark plan done) + `aor-memory-sync` (session summary) |

## 1. Project Overview

Typical tech stack for projects in this workspace:

| Layer | Technology |
|-------|-----------|
| **Mobile** | Expo/React Native (v54.0.0) |
| **Web Export** | React Native Web |
| **Landing Page** | Vite (`web-landing/`) |
| **Backend** | Supabase (auth, database, storage) |
| **Styling** | NativeWind / TailwindCSS |
| **State** | Zustand |

## 2. Directory Structure

```
[workspace-root]/
├── app/                 # Expo Router pages (file-based routing)
│   ├── (tabs)/          # Tab navigator screens
│   ├── auth/            # Authentication screens
│   ├── billboard/       # Billboard detail screens
│   └── booking/         # Booking flow screens
├── components/          # Reusable UI components
├── design-system/       # Design tokens and typography
├── hooks/               # Custom React hooks
├── lib/                 # Library utilities and configs
├── services/            # API services and data fetching
├── store/               # Zustand state stores
├── supabase/            # Supabase migrations and functions
├── types/               # TypeScript type definitions
├── utils/               # Helper utilities
├── web-landing/         # Vite-based landing page (separate app)
└── .agent/
    ├── [app-name]/
    │   ├── plans/       # Implementation plans (by topic)
    │   ├── dds/         # Design documents
    │   └── memory/      # Project memory context
    ├── skills/          # Agent skills (this directory)
    └── rules/           # Workspace rules
```

## 3. Code Conventions

### Components
- File naming: **PascalCase.tsx**
- Export **named** components (not default) for reusable components
- Use **NativeWind** for all styling
- Follow design tokens in `design-system/` for colors, typography, spacing

### Types
- Define shared types in `types/` directory
- Use **interfaces** for object shapes, **types** for unions
- Prefix interface names descriptively (e.g., `BillboardType`, `UserProfile`)

### Imports
- Use path aliases (`@/` prefix) for absolute imports
- Group: React/libraries → Components → Utils → Types → Styles
- Avoid circular dependencies

### State Management
- Use **Zustand** for global state (`store/`)
- Keep stores small and domain-focused
- Use **React Hook Form** + **Yup** for forms
- Prefer local state for component-specific UI

### Error Handling
```typescript
try {
  const { data, error } = await supabase.from('tablename').select('*');
  if (error) throw error;
  return data;
} catch (error) {
  Toast.show({ type: 'error', text1: 'Failed to load data' });
  console.error(error);
}
```

## 4. Thought Process Visibility

**ALWAYS** display the AI thought process to the user. This helps the user understand the reasoning and catch wrong decisions early. Prefix every response with a `### Strategic Rationale Thought` section.

## 5. Testing Guidelines

- Use **Jest** with **jest-expo**
- Place tests in `__tests__/`
- Use **React Testing Library** for component tests
- Mock Supabase and external services

## 6. Performance

- Use `expo-image` for optimized image loading
- Implement list virtualization for long lists
- Lazy load screens with Expo Router
- Minimize re-renders with proper memoization

## 7. Deployment

| Target | Method |
|--------|--------|
| **Mobile** | EAS Build for iOS/Android |
| **Web Export** | `npm run build:web` → `dist/` |
| **Landing Page** | Vite build in `web-landing/` for VPS |

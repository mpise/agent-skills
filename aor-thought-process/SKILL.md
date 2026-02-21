---
name: aor-thought-process
description: >
  Enforces transparent and human-like thought process visibility for all AI actions.
  Ensures the user can follow, understand, and correct the agent's logic in real-time.
  Automatically triggered for every response.
license: MIT
---

# Thought Process Visibility

Enforces the mandatory "Thought process from AI" rule from the workspace configuration. This skill ensures that the agent's logic is never hidden and is presented in a way that facilitates collaboration and error correction.

## Core Mandates

1.  **Always Visible**: Do not use hidden reasoning or "thought" blocks that are stripped from the final output. Every response must contain the logic used to generate it.
2.  **Explicit Title**: Every thought process block MUST be titled: `### Strategic Rationale Thoughts`.
3.  **Human-Like Narrative**: Present thoughts in a way that feels like a human software engineer explaining their approach to a peer. Avoid mechanical or robotic summaries.
4.  **Strategic Focus**: Detail architectural trade-offs, security considerations, and the step-by-step logic behind proposed changes.
5.  **Correction Loops**: Ensure the thought process provides enough detail for a user to identify and correct any wrong decisions **before** they are executed.

## Structure of a Thought Block

A typical `### Strategic Rationale Thoughts` section should include:

- **Goal**: What are we trying to achieve?
- **Analysis**: What did we find in the codebase or requirements?
- **Rationale**: Why choose this specific implementation over alternatives?
- **Safety**: What security or stability risks were considered?
- **Steps**: The granular plan for the current turn.

## Example

```markdown
### Strategic Rationale Thoughts

I've analyzed the request to add a new authentication flow. While we could use a third-party library, I recommend extending our existing Supabase auth service to maintain consistency and reduce bundle size. 

I've considered the security implications of session persistence on mobile and will be using Expo SecureStore as per our `aor-security-guard` guidelines.

Plan:
1. Create `services/auth_extended.ts`
2. Update `store/authStore.ts` to support the new state
3. Add a verification screen in `app/auth/verify.tsx`
```

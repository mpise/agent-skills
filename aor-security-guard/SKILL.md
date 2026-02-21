---
name: aor-security-guard
description: >
  Enforces secure coding practices for the current workspace.
  Automatically triggered when generating or reviewing code that involves secrets, user input,
  database queries, authentication, or storage. Use PROACTIVELY for all code generation and review.
license: MIT
---

# Workspace Security Guard

Enforces the mandatory security guidelines from `.agent/rules/rules.md`. All generated code must be secure by default. This skill proactively identifies and highlights potential security vulnerabilities.

## Dynamic App-Name Resolution (CRITICAL)

While primarily logic-focused, if this skill needs to reference workspace files, resolve `[app-name]` by identifying the subdirectory in `.agent/` that contains project history.

## When This Skill Triggers (Automatic)

This skill MUST be invoked during **ALL** code generation and code review activities. It runs as a passive check on every code output.

## 1. Secure Code Generation Principles

**ALWAYS** follow these principles:

| Principle | Rule |
|-----------|------|
| **No hardcoded secrets** | Never put API keys, passwords, or tokens in source code |
| **Input validation** | Always validate user inputs on both client and server |
| **Sanitize output** | Sanitize all data before rendering to prevent XSS |
| **Parameterized queries** | Use Supabase client — never concatenate strings in queries |
| **Auth before action** | Implement proper authentication checks before sensitive operations |
| **Least privilege** | Follow principle of least privilege for API access and permissions |

## 2. Vulnerability Checklist

When generating or reviewing code, **ALWAYS** check for:

| Vulnerability | What to Look For | Solution |
|--------------|------------------|----------|
| **Hardcoded Secrets** | API keys, passwords, tokens in code | Use environment variables (`EXPO_PUBLIC_*`) |
| **SQL Injection** | String concatenation in queries | Use Supabase client with parameterized queries |
| **XSS** | Unsanitized user input in rendering | Sanitize inputs, use React's built-in escaping |
| **Insecure Storage** | Sensitive data in AsyncStorage/localStorage | Use Expo SecureStore for sensitive data |
| **Missing Auth Checks** | API calls without authentication | Verify session before sensitive operations |
| **Exposed Service Keys** | `service_role` key on client side | Only use `anon` key on client; `service_role` on server only |
| **Missing RLS** | Database tables without Row Level Security | Always enable and configure RLS policies |
| **Insecure Deep Links** | Unvalidated deep link parameters | Validate and sanitize all URL parameters |
| **Debug Code in Production** | console.log with sensitive data, debug flags | Remove before deploy or use `__DEV__` checks |

## 3. Warning Format

When insecure code is detected, **ALWAYS** use this format:

```
⚠️ SECURITY WARNING: [Brief description]

RISK: [Explain the risk in simple terms]

SOLUTION: [Provide the secure alternative]
// ❌ INSECURE
[insecure code example]

// ✅ SECURE
[secure code example]
```

## 4. Supabase-Specific Security

| Rule | Detail |
|------|--------|
| **Always use RLS** | Every table must have Row Level Security enabled |
| **Validate on server** | Use Edge Functions for sensitive operations |
| **Check auth state** | Verify `supabase.auth.getUser()` before protected actions |
| **Secure file uploads** | Validate file types and sizes, use signed URLs |

### Secure Query Pattern
```typescript
// ✅ Secure Supabase query with auth check
const fetchUserData = async () => {
  const { data: { user }, error: authError } = await supabase.auth.getUser();
  if (authError || !user) {
    throw new Error('Unauthorized');
  }
  
  const { data, error } = await supabase
    .from('user_profiles')
    .select('*')
    .eq('user_id', user.id); // RLS also enforces this
    
  if (error) throw error;
  return data;
};
```

## 5. Environment Variables

Required environment variables for this project — **NEVER** hardcode these:
```
EXPO_PUBLIC_SUPABASE_URL
EXPO_PUBLIC_SUPABASE_ANON_KEY
EXPO_PUBLIC_STRIPE_PUBLISHABLE_KEY
EXPO_PUBLIC_RAZORPAY_KEY_ID
```

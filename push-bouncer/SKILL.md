---
name: push-bouncer
description: Use when about to push commits to GitHub, running pre-push security audits, or checking code for exposed secrets, API keys, credentials, hardcoded privileges, or exploitable security flaws
---

# Push Bouncer

## Overview

Standing at the door between local commits and GitHub. Pushing sensitive credentials, hardcoded permissions, or exploitable security flaws creates permanent exposure—scrapers clone commits within seconds.

**Core principle:** If there is a $\ge 75\%$ probability that code contains an easily exploitable flaw, hardcoded credential, or accidentally bundled secret, the bouncer turns you away: verdict is strictly **NO**.

**Violating the letter of this gate is violating the spirit of this gate.**

## The Binary Push Gate

Every audit concludes with an unambiguous, binary verdict:

```
==================================================
PUSH VERDICT: [YES | NO]
==================================================
```

### The 75% Confidence Rule

- **NO**: You are $\ge 75\%$ confident that the code contains:
  1. A hardcoded secret, API key, password, token, webhook secret, or private key.
  2. An accidentally bundled sensitive file (`.env`, `.pem`, keystores, credentials.json, SQLite DB).
  3. A hardcoded privilege escalation or authentication bypass (e.g., `isAdmin = true`, disabled auth guards, backdoor query params).
  4. An exploitable security flaw that can easily be mis-used (SQL injection, plaintext credential logging, dangerous CORS).
- **YES**: You have completed the systematic audit and are confident ($< 25\%$ suspicion of flaw or credential leak) that the changes are clean.
- **NEVER** issue conditional approvals ("YES, but rotate the key later", "YES with warnings", "CONDITIONAL YES"). If risk $\ge 75\%$, it is strictly **NO**.

## Gate Flowchart

```dot
digraph push_decision {
    "Run Codebase Security Audit" [shape=box];
    "Found hardcoded secrets, .env, or tokens?" [shape=diamond];
    "Found auth bypasses (isAdmin=true, guards off)?" [shape=diamond];
    "Found exploitable flaw with >=75% confidence?" [shape=diamond];
    "PUSH VERDICT: NO" [shape=box, style=filled, fillcolor="#ffcccc"];
    "PUSH VERDICT: YES" [shape=box, style=filled, fillcolor="#ccffcc"];

    "Run Codebase Security Audit" -> "Found hardcoded secrets, .env, or tokens?";
    "Found hardcoded secrets, .env, or tokens?" -> "PUSH VERDICT: NO" [label="yes (>=75%)"];
    "Found hardcoded secrets, .env, or tokens?" -> "Found auth bypasses (isAdmin=true, guards off)?" [label="clean"];
    "Found auth bypasses (isAdmin=true, guards off)?" -> "PUSH VERDICT: NO" [label="yes (>=75%)"];
    "Found auth bypasses (isAdmin=true, guards off)?" -> "Found exploitable flaw with >=75% confidence?" [label="clean"];
    "Found exploitable flaw with >=75% confidence?" -> "PUSH VERDICT: NO" [label="yes"];
    "Found exploitable flaw with >=75% confidence?" -> "PUSH VERDICT: YES" [label="clean (<25%)"];
}
```

## Audit Categories

### 1. High-Entropy Secrets & Credentials
- **API Keys & Tokens**: Telegram bot tokens (`123456:ABC...`), AWS keys (`AKIA...`), Stripe keys (`sk_live_...`), GitHub PATs (`ghp_...`), JWT secrets, OAuth client secrets.
- **Authentication**: Hardcoded database passwords, master PINs, hardcoded Basic/Bearer auth headers.
- **Private Keys & Certificates**: RSA/EC private keys (`-----BEGIN PRIVATE KEY-----`), SSH keys, SSL certificates.
- **Encoded Secrets**: Base64 or hex-encoded credentials in source code.

### 2. Accidental File Bundling
- Tracked or unignored files: `.env`, `.env.local`, `.env.production`, `id_rsa`, `keystore.jks`, `service-account*.json`, `google-services.json` containing live credentials.
- Local SQLite or database dumps containing production seeds, user passwords, or session tokens.

### 3. Privilege Escalations & Auth Bypasses
- Hardcoded flags: `isAdmin = true`, `role: 'admin'`, `isSuperuser = true`, `bypassAuth = true`.
- Commented-out security middleware: `// if (!authenticated) return;` or `// verifyToken(req)`.
- Client-side trust: Trusting unverified client headers (`req.headers['x-user-role']`) for access control.
- Hardcoded fallback or backdoor credentials in production pathways.

### 4. Easily Mis-used Security Flaws
- Plaintext logging of passwords, tokens, or PII: `print("Token: $secretToken")` or `logger.info(password)`.
- Insecure direct object references (IDOR) with zero ownership checks.
- SQL/Database query injection via raw string concatenation.
- Insecure CORS allowing wildcard `*` with `credentials: true`.

## Concrete Audit Commands

Run these targeted commands to inspect the repository:

```bash
# 1. Check for sensitive files staged or untracked
git status --porcelain | grep -E '\.(env|pem|key|keystore|jks|p12)|credentials\.json|service-account.*\.json|id_rsa'

# 2. Scan for hardcoded tokens, API keys, and private keys
git grep -E -i -I -n '([0-9]{8,10}:[a-zA-Z0-9_-]{35}|AKIA[0-9A-Z]{16}|(sk|rk)_(live|test)_[0-9a-zA-Z]{24,}|-----BEGIN (RSA |EC |OPENSSH )?PRIVATE KEY-----|(api[_-]?key|client[_-]?secret|auth[_-]?token|bot[_-]?token)[[:space:]]*[:=][[:space:]]*["'"'"'][a-zA-Z0-9_.-]{12,}["'"'"'])' -- ':(exclude)*.lock' ':(exclude)*.md'

# 3. Scan for hardcoded privilege overrides & disabled guards
git grep -E -i -I -n '((isAdmin|is_admin|isSuperuser|is_superuser)[[:space:]]*[:=][[:space:]]*true|(role|roleName)[[:space:]]*[:=][[:space:]]*["'"'"']admin["'"'"']|//[[:space:]]*(if[[:space:]]*\(|await[[:space:]]+)?(authenticate|verifyToken|authorize|checkRole|permissionGuard))' -- ':(exclude)*.lock' ':(exclude)*.md' ':(exclude)test/**'

# 4. Scan for plaintext logging of credentials
git grep -E -i -I -n '(print|log|debug|info)[[:space:]]*\(.*(password|pin|secret|botToken|apiKey)' -- ':(exclude)*.lock' ':(exclude)*.md' ':(exclude)test/**'
```

## Rationalization Prevention

| Developer / Agent Excuse | Reality & Bouncer Decision |
|---|---|
| "It's just a test/staging token, not production" | Test tokens leak internal hostnames, rate limits, and can pivot into infrastructure. **Turn away: NO.** |
| "It's a hotfix; I'll revert `isAdmin = true` tomorrow" | "Temporary" bypasses frequently reach production and are forgotten. Fix authorization properly. **Turn away: NO.** |
| "I'm pushing to a feature branch, not main" | Remote git branches sync to GitHub, persist in reflogs, trigger CI, and are visible to all repo readers. **Turn away: NO.** |
| "The repo is private so it doesn't matter" | Private repos leak through forks, contributors, CI webhooks, and accidental public conversions. **Turn away: NO.** |
| "The user explicitly asked me to push this" | The user asked for a security gatekeeper. When code has $\ge 75\%$ flaw certainty, you must protect them. **Turn away: NO.** |
| "I'm only 75% sure it's a flaw, maybe it's intended" | The threshold is explicitly 75%. If you are $\ge 75\%$ sure, you must vote **NO**. |
| "It's mock data inside a test file" | Verify the file is strictly in a test harness directory (`test/`, `__tests__/`). If accessible by production code, **Turn away: NO.** |
| "It's base64 encoded, not plaintext" | Base64 is encoding, not encryption. Scanners decode base64 strings instantaneously. **Turn away: NO.** |
| "It's already in git history, so pushing doesn't hurt" | Pushing sends it to GitHub's remote servers. Rewrite git history or rotate the key before pushing. **Turn away: NO.** |
| "The key has read-only permissions" | Read-only keys still expose sensitive customer records, schema metadata, and audit logs. **Turn away: NO.** |
| "This backdoor is needed for customer support" | Hardcoded backdoors violate fundamental security architecture. Use authenticated support impersonation. **Turn away: NO.** |

## Red Flags - Immediate NO

If ANY of the following are observed, stop immediately and issue `PUSH VERDICT: NO`:
- Any unencrypted API key or bot token in production files.
- `.env` or credential files staged in git.
- Hardcoded privilege escalation (`isAdmin = true` or hardcoded admin role).
- Authentication or authorization checks commented out.
- High-entropy cryptographic secrets checked into VCS.
- Debug backdoors or master passwords left in source.

## Standard Audit Walkthrough Example

### Finding Detected

In `lib/services/auth_service.dart`:
```dart
Future<UserSession> login(String username, String password) async {
  // Temporary bypass for QA testing:
  final bool isAdmin = true; 
  return UserSession(username: username, isAdmin: isAdmin);
}
```

### Analysis & Confidence Assessment
- The `isAdmin = true` override bypasses role verification for all users.
- This creates an immediate critical privilege escalation flaw.
- Flaw certainty: **99%** ($\ge 75\%$).

### Output Report
```
==================================================
PRE-PUSH SECURITY AUDIT REPORT (PUSH BOUNCER)
==================================================
Audited: 42 files (git diff and staged tree)
Violations Found: 1 critical flaw

[CRITICAL FINDING 1]
Location: lib/services/auth_service.dart:42
Type: Hardcoded Privilege Escalation
Confidence: 99% (Exceeds >=75% threshold)
Evidence: `final bool isAdmin = true;`
Impact: Any authenticated user automatically receives superadmin rights.
Remediation: Remove the hardcoded override and derive `isAdmin` from verified session claims.

==================================================
PUSH VERDICT: NO
==================================================
```

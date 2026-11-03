---
name: security-audit
description: Performs a structured application security review of source code, covering injection, authentication, authorization, secrets, input validation, dependency risk, and data exposure, mapped to OWASP Top 10 categories. Use when the user asks for a security review or audit, before releasing a feature that handles user data or authentication, or when investigating a suspected vulnerability.
license: MIT
---

# Security Audit

Perform a defensive security review of the code in scope. The goal is to find and help
fix vulnerabilities in the user's own code. Report only issues you can support with
evidence from the code, and rate them honestly.

## Ground rules

- Stay within the scope you were given (a directory, a pull request, or a set of files).
- Do not run exploits against live systems. Describe how an issue could be triggered in
  general terms and focus on remediation.
- If you find a real secret (key, token, password), report its location and recommend
  rotation. Do not repeat the secret value in your report.
- Distinguish confirmed issues from items that need manual verification.

## Procedure

1. **Map the attack surface.** List entry points: HTTP routes, message consumers, CLI
   arguments, file uploads, webhooks, scheduled jobs. Note which require authentication.
2. **Trace untrusted data** from each entry point to sensitive sinks: database queries,
   shell commands, file system paths, HTML output, redirects, deserialization, outbound
   HTTP requests, logs.
3. **Check the categories below.**
4. **Rate each finding** using the severity guide.
5. **Write the report.**

## Categories

### Injection
- SQL built with string concatenation or formatting instead of parameterized queries.
- Shell commands built from input; use argument lists and avoid shell invocation.
- Template or HTML output without contextual escaping (cross-site scripting).
- Path traversal: user input joined into file paths without normalization and a check that
  the result stays inside an allowed base directory.

### Authentication and session management
- Passwords stored without a slow, salted hash.
- Missing rate limiting or lockout on login and password reset.
- Tokens without expiry, tokens in URLs, or session identifiers not rotated on login.

### Authorization
- Endpoints that check authentication but not ownership (insecure direct object
  references): can user A read or modify user B's resource by changing an id?
- Authorization decided by client-supplied fields (role, user id, price).
- Administrative actions without server-side role checks.

### Secrets and configuration
- Hard-coded credentials, keys committed to the repository, secrets in logs or error
  messages.
- Debug mode, verbose errors, or permissive CORS enabled in production configuration.

### Input validation and file handling
- Missing size limits on request bodies and uploads.
- Archive extraction without checks for traversal entries, symlinks, and decompression
  bombs.
- Unsafe deserialization of untrusted data (for example native object serialization
  formats or YAML loaders that construct arbitrary objects).

### Server-side request forgery
- Outbound requests to URLs taken from user input without an allowlist.

### Dependencies
- Outdated packages with known vulnerabilities. Recommend running the ecosystem's audit
  tool and note that results must be checked against current advisories.

### Data exposure
- API responses returning more fields than the client needs (password hashes, internal
  flags, other users' data).
- Personal data written to logs.

## Severity guide

- **Critical:** exploitable remotely without authentication, leads to data breach or code
  execution.
- **High:** exploitable by an authenticated user to access other users' data or escalate
  privileges.
- **Medium:** requires unusual conditions, or impact is limited.
- **Low:** defense-in-depth improvements and hardening.

## Report format

```
## Scope and method
<what was reviewed, what was not>

## Findings
### [Severity] <title> (<category>)
Location: <file>:<line>
Evidence: <the relevant code path, briefly>
Impact: <what an attacker could achieve>
Remediation: <specific fix>
Status: Confirmed | Needs verification

## Positive observations
## Recommended next steps
```

Order findings by severity. If no issues are found in a category you checked, say so
briefly; this tells the reader what was covered.

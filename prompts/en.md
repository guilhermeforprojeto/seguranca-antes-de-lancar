## Pre-launch security audit

Before I launch this app, run a full security audit of the codebase using the checklist below.

For EACH item:
1. Give a status: ✅ OK, ❌ Issue, or ➖ Not applicable (with the reason).
2. If there's an issue, point to the file and line, explain the risk in one sentence and show the fix.
3. Don't mark anything OK without checking the code. If you can't verify it (e.g. a database or hosting dashboard setting), tell me what I need to check manually.

At the end, list the issues by severity (critical, high, medium, low) and fix the critical ones first.

### Checklist

**Secrets and credentials**
1. **Hide API keys**: no secret keys in frontend code or in the bundle shipped to the browser; secrets only in server-side environment variables.
2. **Clean secrets from git**: `.env` and similar files in `.gitignore`; search the history for committed keys and, if any, revoke and rotate them (deleting the commit isn't enough).
3. **Only the public database key on the client**: the frontend uses only the public/anon key (e.g. Supabase `anon`); the service key (`service_role`, admin) never leaves the server.

**Database and access**
4. **Enable RLS (Row Level Security)**: every table exposed to the client has RLS on, with policies that limit each user to their own data.
5. **Encrypt data**: sensitive data encrypted at rest and in transit; nothing sensitive in plain text in the database or logs.
6. **Server-side auth**: every "who are you" and "what can you do" check happens on the backend; hiding a button in the frontend is not protection.
7. **Restrict access**: least privilege for users, roles, keys, buckets and admin panels; admin routes protected.
8. **Block mass assignment**: the server accepts only expected fields (allowlist); a user can't send `role: "admin"`, `price` or `user_id` and have it saved.
9. **Protect cookies**: session cookies with `HttpOnly`, `Secure` and `SameSite`; avoid session tokens in `localStorage` where possible.
10. **Hash passwords**: bcrypt, scrypt or Argon2 (never plain MD5/SHA, never plain text); ideally use an auth provider instead of rolling your own.

**Abuse and input**
11. **Rate limiting**: limits on login, sign-up, password reset, email/SMS sending and expensive endpoints (e.g. AI calls).
12. **Bot protection**: CAPTCHA or equivalent (Turnstile, hCaptcha) on sign-up, login and public forms.
13. **Parameterized queries**: no queries built by concatenating user input; use parameters or the ORM properly (against SQL injection).
14. **Input validation**: validate type, format and length of everything reaching the server (e.g. Zod, Joi, Pydantic); sanitize anything rendered as HTML (against XSS).

**Data exposure**
15. **Don't leak content**: production errors without stack traces, SQL or internals; no user data reachable by another user just by changing an ID in the URL (IDOR).
16. **Restrict uploads**: limit type (checking content, not just the extension), size and count; store outside the public folder or behind signed URLs.
17. **Trim API responses**: return only the fields the UI uses; never send password hashes, other users' emails, tokens or internal fields.

**Transport and browser**
18. **Add security headers**: `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options: nosniff`, `X-Frame-Options` (or `frame-ancestors`), `Referrer-Policy`; CORS only for the origins you need.
19. **Force HTTPS**: redirect all HTTP to HTTPS and enable HSTS.

**Maintenance**
20. **Dependencies and logs**: run `npm audit` (or equivalent) and update vulnerable packages; log errors and suspicious access without ever writing passwords, tokens or personal data to the logs.

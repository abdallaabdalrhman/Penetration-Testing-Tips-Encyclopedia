# 03 — Broken Access Control

**CWE-284** | **CVSS: 5.4–9.8** | **OWASP A01:2021 (#1)**

---

## What Is It?

BAC failures occur when users can act outside their intended permissions. Includes horizontal/vertical privilege escalation, IDOR, missing function-level access control.

> **Core Problem:** System checks authentication but not authorization. Any user can call any function.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **IDOR (Horizontal)** | Access other users' data at the same privilege level via object reference manipulation. |
| **Vertical Privilege Escalation** | Regular user accesses admin functions by changing role/param. |
| **Missing Function-Level AC** | Admin endpoints accessible to regular users — just know the URL. |
| **Path Traversal** | `../../etc/passwd` — traverse outside allowed directory. |
| **JWT Role Manipulation** | Decode JWT → change `role:user` to `role:admin` → re-sign or bypass verification. |

---

## Hunting Methodology

1. Create accounts at multiple privilege levels: guest, user, premium, admin.
2. Map every endpoint — use Burp sitemap. Note what each role can access.
3. Replay privileged requests as lower-privileged user. Check if enforced server-side.
4. Test forced browsing: `/admin`, `/dashboard`, `/manage`, `/api/admin/*` with low-priv token.
5. Test parameter tampering: `role=admin`, `isAdmin=true`, `account_type=admin` in body/header.
6. JWT: decode payload, change `sub`/`role`/`email`, try `alg:none` attack, try weak secret with `jwt_tool`.
7. Test method switching: if `GET /admin` is blocked, try `POST /admin`.
8. Check UUID vs sequential IDs — UUIDs don't fix BAC, find UUID leaks elsewhere.
9. Test banking: change `account_id` in balance/statement/transfer API calls.
10. Mobile: decompile APK — find hidden admin endpoints hardcoded in source.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Autorize (Burp)** | Auto-replay all requests with lower-privilege cookies. |
| **Burp Intruder** | Fuzz IDs, role params, and endpoint paths. |
| **jwt_tool** | JWT manipulation, alg:none, key confusion attacks. |
| **ffuf** | Fuzz hidden admin paths and endpoints. |
| **Param Miner (Burp)** | Discover hidden role/privilege parameters. |
| **403Bypass** | Automated 403 forbidden bypass techniques. |

---

## Tips & Tricks

- **#1** Always test with multiple accounts — the 2-account rule is minimum.
- **#2** JWT `alg:none`: set algorithm to `'none'`, remove signature, change role to admin.
- **#3** Check every 403 response — use 403 bypass headers: `X-Original-URL`, `X-Rewrite-URL`.
- **#4** Banking API: test `GET /api/accounts/{id}/balance` with another user's account ID.
- **#5** Look for role in cookie/localStorage on mobile apps — client-side role is always bypassable.
- **#6** After finding BAC, don't just PoC the access — demonstrate real business impact (data exfil).

---

## Deadly Chains

```
BAC + SQLi → Full DB access
  └─ Admin panel exposes SQLi endpoint

BAC + File Upload → RCE
  └─ Reach admin upload with lower-priv account

JWT role change → Admin ATO → mass user data exfiltration

Forced browse to /admin/users → enumerate all users → credential stuffing

BAC + Banking transfer endpoint → unauthorized fund transfers
```

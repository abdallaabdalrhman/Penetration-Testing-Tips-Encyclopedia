# 14 — LDAP Injection

**CWE-90** | **CVSS: 7.5–9.8** | **OWASP A03:2021**

---

## What Is It?

LDAP injection occurs when user input is incorporated into LDAP queries without sanitization, allowing attackers to bypass authentication, enumerate users, and extract directory data.

> **Core Problem:** LDAP filter built from user input — attacker escapes filter.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Auth Bypass** | `username: *)(& or *)(|(uid=*` — collapses filter logic, bypasses password check. |
| **Blind LDAP Injection** | Infer data via true/false responses. Enumerate attributes character by character. |
| **LDAP Search Injection** | Manipulate search filters to retrieve all directory objects. |

---

## Hunting Methodology

1. Find LDAP-backed auth: corporate login portals, Active Directory-integrated apps, SSO.
2. Test auth bypass: `username: *` or `*)(|(uid=*` — does it log in?
3. Test: `username: admin)(|(password=*` — disrupt AND logic.
4. Blind injection: measure response differences for character-by-character enumeration.
5. Banking: Active Directory-backed admin portals, employee SSO systems.
6. Check error messages for LDAP syntax errors — reveals injection points.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Suite** | Manual LDAP injection testing via Repeater. |
| **OWASP ZAP** | Automated LDAP injection scanner. |
| **ldap3 (Python)** | Direct LDAP interaction for exploitation. |

---

## Tips & Tricks

- **#1** Classic bypass: `username=*` `password=anything` → filter becomes `(&(uid=*)(password=anything))` → true.
- **#2** Look for SSO and enterprise login portals — these are most likely LDAP-backed.
- **#3** Blind injection: enumerate admin email/attributes via character-by-character comparison.

---

## Deadly Chains

```
LDAP injection → auth bypass → internal admin panel → sensitive data
LDAP → enumerate all user emails → phishing/credential stuffing
```

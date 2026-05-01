# 09 — Cross-Site Request Forgery (CSRF)

**CWE-352** | **CVSS: 4.3–8.8** | **OWASP A01:2021**

---

## What Is It?

CSRF tricks authenticated users into executing unwanted actions. In banking apps, this can trigger fund transfers, email changes, and account modifications.

> **Core Problem:** Server trusts requests from authenticated browsers without verifying intent.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Classic CSRF** | Hidden form auto-submits to target site using victim's cookie. |
| **JSON CSRF** | `Content-Type: text/plain` with JSON body — browser sends with cookies. |
| **CSRF via CORS Misconfiguration** | Relaxed CORS allows cross-origin read, exposing CSRF tokens. |
| **Login CSRF** | Force victim to log into attacker's account — then track victim activity. |
| **CSRF Token Bypass** | Token not validated, or validated only on some parameters. |

---

## Hunting Methodology

1. Identify state-changing endpoints: password change, email change, fund transfer, profile update.
2. Check if CSRF token present in request. If yes: test removing it — does request succeed?
3. Test CSRF token validation: change token value — does server still accept?
4. Test if token is tied to session — use token from your session in request for victim's session.
5. Test for CORS misconfig: does the endpoint include ACAO header allowing arbitrary origins?
6. JSON CSRF: change `Content-Type` to `text/plain` and submit JSON body via HTML form.
7. Check `SameSite` cookie attribute — `None` or unset = CSRF possible.
8. Build PoC HTML page that auto-submits to vulnerable endpoint.
9. Banking: test fund transfer, bill payment, beneficiary addition endpoints.
10. Mobile: CSRF may affect web APIs consumed by mobile hybrid apps.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp CSRF PoC Generator** | Right-click request → Engagement Tools → Generate CSRF PoC. |
| **XSRFProbe** | CSRF audit and exploitation toolkit. |
| **Burp Suite** | Intercept, modify, replay requests to test CSRF protection. |
| **Custom HTML** | Build PoC forms in HTML — key for demonstrating real impact. |

---

## Tips & Tricks

- **#1** Remove CSRF token first — if request succeeds, it's not validated at all.
- **#2** CSRF + Banking: 'add beneficiary' + 'transfer funds' = direct financial fraud PoC.
- **#3** Check Referer header validation — sometimes only checked for presence, not value.
- **#4** `SameSite=Strict` cookies: CSRF won't work cross-site. Look for APIs that don't use cookies.
- **#5** Login CSRF: force victim to login as your account, then victim actions benefit attacker.

---

## Deadly Chains

```
CSRF + XSS token steal → CSRF bypass
  └─ XSS extracts CSRF token → enables further CSRF attacks

CSRF + Banking transfer → force authenticated user to transfer funds to attacker

CSRF + Email change + Password reset → change email → reset password → full ATO

CORS misconfiguration + CSRF → read CSRF token cross-origin → forge any request
```

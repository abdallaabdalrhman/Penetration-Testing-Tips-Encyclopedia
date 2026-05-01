# 04 — Bypass 2FA / MFA

**CWE-287** | **CVSS: 7.5–9.0** | **OWASP A07:2021**

---

## What Is It?

2FA/MFA bypass vulnerabilities allow attackers to authenticate without the second factor. Common in banking apps, financial platforms, and enterprise SSO systems.

> **Core Problem:** Server fails to enforce 2FA completion before granting access, or 2FA logic is bypassable.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Direct Endpoint Access** | After entering username/password, directly navigate to `/dashboard` — 2FA step skipped. |
| **Response Manipulation** | Change 2FA verification response from `false` to `true` in proxy. |
| **Code Reuse** | Used OTP accepted again — no one-time enforcement. |
| **Brute Force** | No rate limiting on 2FA code input — enumerate 000000–999999. |
| **Token Leakage** | 2FA code leaked in response body, JS file, or API error message. |
| **Backup Code Abuse** | Backup codes never expire and have no rate limit. |

---

## Hunting Methodology

1. After valid credentials, intercept 2FA request — note endpoint and parameters.
2. Try skipping 2FA: directly request authenticated endpoint with session from step 1.
3. Intercept 2FA verification response — change `'success':false` to `'success':true`.
4. Test code brute force: 6-digit = 1M combos. Check if lockout exists. Use Turbo Intruder.
5. Test code reuse: submit same OTP twice. If second works — no invalidation.
6. Check 2FA code in response of the `/send-otp` call — sometimes returned in API response.
7. Test backup codes for rate limiting and expiry.
8. Check if `X-Forwarded-For` bypass unlocks extra attempts.
9. Test remember-device functionality — does the device token expire?
10. Banking apps: test OTP flows for fund transfers and login separately.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Suite Repeater** | Manual response manipulation and request replay. |
| **Turbo Intruder** | High-speed OTP brute force — 1M requests quickly. |
| **AuthMatrix (Burp)** | Multi-account auth state testing. |
| **Evilginx2** | Adversary-in-the-middle framework — captures session cookies past 2FA. |

---

## Tips & Tricks

- **#1** Always test: can you access `/dashboard` directly after login, skipping the 2FA page?
- **#2** Response manipulation: change `status:false→true`, `verified:0→1`, `mfa_required:true→false`.
- **#3** OTP in response body: check the `/send-otp` API response thoroughly.
- **#4** Banking: 2FA for transactions (fund transfer) often has separate and weaker implementation.
- **#5** Check if changing `Accept-Language` or `User-Agent` disables 2FA (geographic trust logic).
- **#6** Session fixation: fix session ID before login — does 2FA bind to session correctly?

---

## Deadly Chains

```
2FA bypass + Valid credentials (from breach) → Full ATO

2FA bypass + Banking transfer endpoint → Unauthorized fund transfer

2FA token leak in API → Automated account takeover at scale

Evilginx phishing → Real-time 2FA relay → Session cookie theft → ATO

Brute force 2FA + No account lockout → Silent account compromise
```

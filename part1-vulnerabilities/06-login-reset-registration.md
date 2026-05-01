# 06 — Login, Reset & Registration Page Attacks

**CWE-287/640/521** | **CVSS: 5.0–9.8** | **OWASP A07:2021**

---

## What Is It?

Authentication flows (login, password reset, registration) are the most attacked surface in any application. Banking apps are prime targets due to direct financial impact.

> **Core Problem:** Weak implementation of auth flows enables enumeration, credential attacks, and ATO.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Username/Email Enumeration** | Different error messages for valid vs invalid users reveal account existence. |
| **Password Reset Poisoning** | Host header injection in reset email leads to token theft. |
| **Weak Password Policy** | No minimum complexity — allows brute force with common passwords. |
| **Token Predictability** | Reset tokens are sequential, time-based, or short. |
| **No Rate Limiting** | Unlimited login attempts enable credential stuffing and brute force. |
| **SQLi in Login** | `' OR 1=1--` bypasses authentication entirely. |

---

## Hunting Methodology

1. Test login: valid user + wrong pass vs invalid user + wrong pass — compare responses.
2. Password reset: inject `X-Forwarded-Host: attacker.com` — check if reset link points there.
3. Test rate limiting: 100 rapid login attempts — is there lockout, CAPTCHA, or delay?
4. Register with existing email — note behavior. Does it confirm account existence?
5. Request reset token, note format — is it UUID? timestamp-based? short numeric?
6. Test token expiry: request token, wait 25 hours, try again.
7. Test token reuse: use reset token to reset password, try using same token again.
8. SQLi in login: `username: ' OR '1'='1'--`, `password: anything`.
9. NoSQL: `username: {'$gt':''}`, `password: {'$gt':''}`
10. Banking registration: test if you can register with existing verified mobile number.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Burp Suite Intruder** | Credential stuffing, password spraying, token enumeration. |
| **Hydra / Medusa** | Network brute forcing login endpoints. |
| **ffuf** | Fast HTTP fuzzing for login endpoints. |
| **jwt_tool** | Reset token analysis if JWT-based. |
| **Custom Python scripts** | Token predictability analysis. |

---

## Tips & Tricks

- **#1** Timing attack: valid username takes slightly longer (DB lookup) — measure with Burp timing.
- **#2** Host header injection: add `X-Forwarded-Host`, `X-Host`, `Forwarded` headers to reset request.
- **#3** Registration race condition: register same email twice simultaneously — may create duplicates.
- **#4** Check forgot password: does it confirm if email exists? That's enumeration.
- **#5** Banking: test if OTP for password reset can be brute-forced (often separate implementation).
- **#6** Token entropy: collect 10 reset tokens, analyze for patterns.

---

## Deadly Chains

```
Password reset poisoning → token theft → ATO → banking access

Username enum + credential stuffing + no rate limit → mass ATO

Weak reset token + brute force → ATO without phishing

SQLi in login → auth bypass → admin access → full system compromise
```

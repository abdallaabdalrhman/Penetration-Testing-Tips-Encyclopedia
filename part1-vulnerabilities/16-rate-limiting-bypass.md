# 16 — Rate Limiting Bypass

**CWE-307 / CWE-799** | **CVSS: 5.3–7.5** | **OWASP A04:2021**

---

## What Is It?

Missing or bypassable rate limiting allows attackers to brute force credentials, OTPs, reset tokens, enumerate users, and conduct fraud attacks.

> **Core Problem:** Server doesn't limit request frequency per user/IP, enabling automated abuse.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **Login Brute Force** | Unlimited login attempts — credential stuffing or password spraying. |
| **OTP/2FA Brute Force** | Enumerate 4–6 digit codes — 10K to 1M attempts allowed. |
| **API Abuse** | No rate limit on costly endpoints — DoS or resource exhaustion. |
| **Business Logic Abuse** | Unlimited coupon use, referral abuse, voting manipulation. |

---

## Hunting Methodology

1. Send 100+ requests rapidly to login endpoint — check for lockout or slowdown.
2. Test IP-based rate limiting bypass: `X-Forwarded-For: 1.2.3.X` rotation.
3. Test account-based: use different usernames — is limit per account or per IP?
4. Test OTP endpoints specifically with Turbo Intruder.
5. Banking: test transfer endpoint — can you initiate 1000 transfers in seconds?
6. Check `Retry-After` header — is it honored? Can it be bypassed?

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Turbo Intruder** | Concurrent high-speed requests — ideal for OTP/code brute force. |
| **Burp Suite Intruder** | Sequential request flooding. |
| **rate-limit-bypass scripts** | Python scripts rotating `X-Forwarded-For` values. |

---

## Tips & Tricks

- **#1** IP bypass headers: `X-Forwarded-For`, `X-Real-IP`, `CF-Connecting-IP`, `True-Client-IP`.
- **#2** Try `X-Forwarded-For: 127.0.0.1` — server may whitelist localhost IP.
- **#3** Banking: test fund transfer rate — multiple transfers per second should be blocked.

---

## Deadly Chains

```
No rate limit + username enum + password list → mass ATO via credential stuffing
No rate limit on OTP + banking transfer → unlimited fraud attempts
IP bypass + OTP brute force → bypass banking 2FA
```

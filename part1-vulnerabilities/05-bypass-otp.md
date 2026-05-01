# 05 — Bypass OTP

**CWE-287 / CWE-640** | **CVSS: 7.5–9.5** | **OWASP A07:2021**

---

## What Is It?

OTP bypass vulnerabilities are critical in banking apps where OTPs protect transfers, login, and password resets. A bypassed OTP can directly lead to financial fraud.

> **Core Problem:** OTP validation is done client-side, lacks rate limiting, or the OTP is predictable/leaked.

---

## Vulnerability Types

| Type | Description |
|------|-------------|
| **OTP Brute Force** | 4-digit: 10,000 combos. 6-digit: 1M. No rate limit = automated bypass. |
| **OTP in API Response** | Server includes the OTP in the JSON response for 'debugging'. |
| **OTP Reuse** | Same OTP valid multiple times — not invalidated after first use. |
| **OTP for Any Account** | OTP generated for User A works when applied to User B's session. |
| **OTP Expiry Bypass** | Expired OTP still accepted — no time validation. |
| **Skip OTP Step** | Submit final step request without completing OTP verification. |

---

## Hunting Methodology

1. Intercept the OTP send request — check if OTP appears in the response.
2. Test brute force: use Turbo Intruder to enumerate 4–6 digit codes with no delay.
3. Test OTP reuse: use valid OTP twice — should fail second time.
4. Test cross-account OTP: get OTP for your account, try it for victim's account in a second session.
5. Test expired OTP: wait 15 minutes, try the same OTP — should fail.
6. Skip OTP: after initiating OTP flow, directly POST to the post-OTP endpoint.
7. Race condition: submit OTP twice simultaneously — race may allow dual acceptance.
8. Banking: test OTP for login AND transaction authorization separately.
9. Check if 'resend OTP' resets rate limiting counter.

---

## Essential Tools

| Tool | Purpose |
|------|---------|
| **Turbo Intruder** | High-concurrency OTP brute force — handles 4 and 6 digit codes. |
| **Burp Suite Intruder** | Sequential OTP enumeration with payload lists. |
| **ffuf** | Fast OTP fuzzing via HTTP. |
| **Race condition script** | Python threading to test concurrent OTP submissions. |

---

## Tips & Tricks

- **#1** Always check OTP in the API response — especially on banking apps with 'debug' enabled.
- **#2** Test cross-account: two browsers, two accounts, swap OTPs between sessions.
- **#3** Race condition: submit OTP for login and transaction simultaneously — timing window may exist.
- **#4** OTP skip: after sending OTP, try accessing the protected resource directly.
- **#5** Try OTP = `000000` or `123456` — some systems have test bypass codes.
- **#6** Banking app OTP bypass = immediate Critical/P1 — document financial impact clearly.

---

## Deadly Chains

```
OTP bypass + Login → Full ATO on banking application

OTP bypass + Fund transfer API → Unauthorized money transfer

OTP in response + Automated scripting → Mass account takeover at scale

Brute force OTP + No lockout + Valid username → Account hijacking
```
